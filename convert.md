下面是改好的完整提示词，已改为只看当前目录下的 `full-behavior.md`：

```text
You are an expert in software engineering, REST API analysis, and reverse engineering.

You are given a REST API project directory containing:
1. The project’s OpenAPI/Swagger definition.
2. The project’s implementation source code.
3. A `full-behavior.md` file containing the existing behavior-level analysis.

Your task is to rewrite the current target `full-behavior.md` into a function-level analysis.

Target project and file-scope rules:
- Use the current working directory as the target project directory.
- The target file is `full-behavior.md` directly inside the current working directory.
- Only inspect files inside the current working directory and its descendants.
- Only rewrite `./full-behavior.md`.
- Ignore IDE active-file metadata such as `## Active file: ...`.
- Ignore sibling project directories, even if they contain files with the same name.
- If `./full-behavior.md` does not exist, stop and report that the required target file is missing.

Terminology:
- Replace every “behavior” concept with “function”.
- A function is a lower-level, endpoint-focused API capability.
- A function should focus only on the core endpoint or core endpoints that directly perform the action.
- Setup endpoints used only to create required state must not remain in the successful invocation sequence. They must be converted into explicit preconditions.

Core endpoint rule:
For each function, identify the primary endpoint(s) that directly perform the function.

Example:
For “delete product constraint”, the core endpoint is:
- `DELETE /products/{productName}/constraints/{constraintId}`

The setup endpoints:
- `POST /products/{productName}`
- `POST /products/{productName}/constraints/requires`

must not appear in the final invocation sequence. They must appear as separate precondition bullets.

Setup-step preservation rule:
When converting an existing behavior’s successful endpoint sequence into function format, preserve every non-core setup step as its own explicit precondition item.

Do not collapse multiple setup steps into one broad precondition.

For each non-core setup step from the original endpoint sequence, create one separate precondition bullet that includes:
- the database/resource state established by that setup endpoint,
- the setup endpoint that can establish that state,
- the path/query/body/form/header values involved,
- any generated id, token, location, ETag, cursor, or response value produced by that setup endpoint,
- how the same state can also be satisfied by direct database setup.

Important wording rule:
Do not use labels such as:
- “From original setup Step 1”
- “From original setup Step X”
- “Original setup step”
- “Original Step”
- “Setup Step X”

Instead, write natural precondition bullets.

Correct style:
Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
- A requires constraint exists for product `{productName}` with `sourceFeatureName = {sourceFeature}` and `requiredFeatureName = {requiredFeature}`. This can be satisfied by directly inserting the constraint row linked to `{productName}` or by calling `POST /products/{productName}/constraints/requires` with form fields `sourceFeature={sourceFeature}` and `requiredFeature={requiredFeature}`.
- The `{constraintId}` used by `DELETE /products/{productName}/constraints/{constraintId}` must identify the constraint described above. If the API is used to establish the constraint, `{constraintId}` must be obtained from the constraint creation response or created resource location.

Incorrect style:
Preconditions:
- From original setup Step 1, `POST /products/{productName}`:
  A product named `{productName}` exists...
- From original setup Step 2, `POST /products/{productName}/constraints/requires`:
  A requires constraint exists...

Failure branch conversion rule:
When converting an existing failure branch’s endpoint group or equivalent sequence:
- Keep the failure endpoint under “Failure endpoint”.
- Convert every endpoint before the failure endpoint into separate natural precondition bullets.
- Preserve the granularity of the original endpoint group.
- If a setup endpoint was intentionally omitted, express the omitted state as a natural precondition bullet and mention the omitted endpoint naturally.
- If a setup endpoint creates invalid, duplicate, mismatched, expired, unauthorized, unauthenticated, or cross-scoped state, represent that exact state as a precondition and mention the endpoint that can produce it.

Failure branch preconditions must include:
- valid prerequisite state created before the failing endpoint,
- invalid prerequisite state that causes the failure,
- omitted prerequisite state if the failure is caused by omission,
- duplicated state if the failure is caused by duplicate creation,
- mismatched path/query/body/form/header relationships,
- wrong, missing, expired, or reused generated ids/tokens/response values,
- authentication, authorization, ownership, tenant, parent-child, or resource scoping mismatches.

Correct duplicate failure style:
- Preconditions:
  - A product named `{productName}` exists. This can be satisfied by direct database insertion or by calling `POST /products/{productName}`.
  - A feature named `{featureName}` already exists for product `{productName}`. This can be satisfied by directly inserting the feature row linked to the product or by calling `POST /products/{productName}/features/{featureName}` once before the failing request.
- Failure endpoint:
  `POST /products/{productName}/features/{featureName}`
- Why this fails:
  ...
- Intentionally violated constraints:
  The feature-name uniqueness requirement was violated by invoking the creation endpoint for an already existing feature.

Correct missing-resource failure style:
- Preconditions:
  - No product named `{productName}` exists in the database. This can be produced by starting from an empty database, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /products/{productName}`.
- Failure endpoint:
  `GET /products/{productName}`
- Why this fails:
  ...
- Intentionally violated constraints:
  The required product state was omitted.

Source-of-truth rule:
- Use `./full-behavior.md` as the starting point for behavior-to-function conversion.
- Use OpenAPI/Swagger inside the current working directory to verify available endpoints, parameters, methods, and documented request/response shapes.
- Use implementation source code inside the current working directory as the final authority for runtime behavior, validation, persistence, side effects, and failure causes.
- If OpenAPI/Swagger and source code disagree, prioritize the source code and explicitly mention the discrepancy.
- Do not execute the API.

Output format:
For each function, use this exact structure:

### Function {N}: {function name}

Function name:
{concise verb phrase}

Core endpoint(s):
- `{HTTP_METHOD} {path}`

Preconditions:
- {one explicit prerequisite state corresponding to one non-core setup endpoint, including the endpoint that can establish it and direct database alternative}
- {another explicit prerequisite state corresponding to another non-core setup endpoint, including the endpoint that can establish it and direct database alternative}
- {generated id, token, response value, ownership, scoping, or value-reuse requirement, if applicable}

Successful execution:
- Result:
  {natural language description of what the core endpoint completes}
- Invocation:
  Step 1: `{HTTP_METHOD} {path}` with required path/query/body/form/header values
  Step 2: `{HTTP_METHOD} {path}` only if this is also a core endpoint, not merely setup
- Constraints:
  {runtime constraints for invoking the core endpoint correctly}

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - {natural precondition bullet describing valid, invalid, duplicate, missing, mismatched, omitted, unauthorized, unauthenticated, expired, or cross-scoped state}
    - {another natural precondition bullet if another setup state exists}
  - Failure endpoint:
    `{HTTP_METHOD} {path}`
  - Why this fails:
    {implementation-backed explanation}
  - Intentionally violated constraints:
    {specific violated state/value/id/token/auth/ownership/relationship}
- Branch 2:
  ...

Endpoint coverage:
- Covers:
  `{HTTP_METHOD} {path}`
- Distinct meaning:
  {why this endpoint belongs to this function}

Additional rules:
- Do not leave any old “Behavior” labels in the rewritten output.
- Do not output functions as a Markdown numbered list.
- Each function must be a separate top-level section using `### Function {N}: ...`.
- Use English only in the produced analysis.
- Do not execute the API.
- Do not invent functions unsupported by OpenAPI/Swagger or source code.
- If the existing document uses different field names, interpret them semantically. For example:
  - “Endpoint sequence”, “Success flow”, “Successful path”, or “Happy path” all mean the successful endpoint sequence.
  - “Endpoint group”, “Failure sequence”, or “Failure setup” all mean the failure branch endpoint group.
  - “Behavior” means the existing higher-level unit that must be rewritten as “Function”.
- Preserve the original setup sequence granularity. If the existing analysis had three setup endpoints before the core endpoint, the rewritten function must have three corresponding precondition bullets unless source code proves one setup step was unrelated or optional.
- Do not mention the existence of the original document or original steps in the rewritten output. The rewritten output should read as a clean standalone function-level analysis.
- If an endpoint is useful only as setup for another function, it can still be its own function if OpenAPI/source code support it as a direct endpoint capability.
- If multiple existing behaviors collapse to the same core endpoint but represent distinct implementation-backed effects, keep separate functions only when the distinct effect is meaningful at endpoint level. Otherwise consolidate them and preserve relevant branches/constraints.
- When in doubt, keep the analysis endpoint-focused and implementation-backed.
```