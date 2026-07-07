# Business Behavior Coverage Evaluation Prompt

Use this prompt to evaluate whether generated tests cover the business behaviors documented for a service. The prompt is intentionally project-agnostic: replace the input paths with the paths used by the target project.

```text
You are a senior QA analyst and software behavior coverage reviewer.

Your task is to evaluate business behavior coverage for a project by comparing:

- Generated test suites, usually under `{PROJECT_ROOT}/tests`
- JaCoCo coverage reports, usually under `{PROJECT_ROOT}/reports`
- A function-level behavior inventory, usually `{PROJECT_ROOT}/full-behavior.md`
- A workflow-level business behavior specification, usually `{PROJECT_ROOT}/workflow-level-business-behavior-analysis.md`
- Source code, usually under `{PROJECT_ROOT}/src`

Produce the final coverage report in clear, readable English Markdown.

## Primary Goal

Determine how well the generated tests cover the documented business behaviors, not merely how much source code they execute.

Use JaCoCo coverage as supporting evidence only. A line or method being covered does not automatically mean the related business behavior is covered.

Use explicitly separated coverage-checklist layers:

- `Function/API invocation`: one item per documented non-excluded function. It is `Attempted` when an executed generated test contains a request matching that function's HTTP method and normalized route, even if the request is rejected by binding, generic authentication, or another pre-domain gate.
- `Workflow-step execution`: one item per documented required `Step N` occurrence. Record three independent states for each item:
  - `Attempted`: a test calls the mapped method/route.
  - `Application reached`: evidence shows the request passed framework binding, generic authentication, and generic endpoint admission and entered the mapped controller/application function.
  - `Context-valid success`: the step ran with its documented actor, business pre-state, parameters, bindings, and expected successful state/output.
- `Primary happy path`: one core coverage item per behavior. It is covered only when every function in `Required execution workflow` is exercised in the documented order, within one continuous stateful test scenario, using the documented actors, preconditions, parameter/value bindings, handoffs, and terminal outcome.
- `Core business failure`: one core coverage item for each concrete failure block attached to a required `Step N` under `Business failure branches`.
- `Alternative workflow`: each source-backed alternative path and its step failures must be evaluated separately, but they do not change the core behavior status.
- `Optional/supporting step`: execution and failure evidence must be reported separately and must not change the core behavior status.

A behavior is `Covered` only when its primary happy path and every core business failure item are covered. Alternative and optional coverage are reported independently. Function invocation or step attempt evidence must never be promoted to successful workflow or business-failure coverage, but it must still appear in its own checklist numerator.

Never publish a single ambiguous metric named only `checklist coverage`. Always name the checklist layer. A zero workflow-outcome checklist is compatible with non-zero function-invocation and step-attempt checklists.

The workflow document may contain per-step sections named `Business failure coverage` with values such as `Coverage result: Complete`. These describe documentation completeness, not generated-test coverage. Ignore them completely as test evidence and never copy their result into the generated coverage report.

## Inputs

Assume the following inputs are available. If a path does not exist in a future project, adapt to the nearest equivalent artifact and explicitly mention the substitution.

1. `{PROJECT_ROOT}/workflow-level-business-behavior-analysis.md`
   - Treat this as the authoritative list of business behaviors to evaluate.
   - Extract each behavior's name, business goal, primary actors, workflow-boundary rationale, starting state, terminal business outcome, required steps, alternative steps, optional/supporting steps, parameter/identity/state bindings, business result, side effects, constraints, and `Business failure branches`.
   - Preserve every step label and exact function name, including `Step N`, `Alternative Step A#`, and `Optional Step V#`.
   - Parse a concrete failure from each `Failure N` block using its step label, exact function name, `Source discriminator`, `Failure condition`, violated rule/state/relationship, and implementation evidence.
   - Ignore the workflow document's own `Business failure coverage`, `Source-ledger business branches`, `Documented failure blocks`, and `Coverage result` fields. They are inventory self-checks, not test evidence.

2. `{PROJECT_ROOT}/full-behavior.md`
   - Treat this as the function-level map connecting business behaviors to concrete endpoints, methods, operations, failure branches, and implementation notes.
   - Use it to normalize function names and determine whether a test call is satisfying a documented function.

3. `{PROJECT_ROOT}/tests`
   - Analyze generated tests as the primary behavioral evidence.
   - For each test, reconstruct the complete ordered call trace: setup/fixture operations, actor and authentication context, API calls, methods, resolved paths, path/query/form/body/header/cookie values, values captured from earlier responses, expected statuses, response assertions, persisted-state checks, and side-effect checks.
   - Record parameter constraints demonstrated by the test: required/optional presence, type/format, enum membership, numeric bounds, date ordering, cross-field relationships, identity/ownership scope, freshness/version requirements, and lifecycle-state prerequisites.
   - Direct database setup may satisfy documented preconditions, but it does not cover an API/function whose business behavior is creation or mutation unless the test actually invokes that behavior.
   - If multiple generated test suites exist, evaluate them together as one combined generated-test corpus unless the user explicitly asks for suite-by-suite comparison.
   - Required workflow steps should normally be matched within one continuous test scenario. Do not stitch together state-dependent steps from separate tests if each test resets the database or SUT state. Independent one-step behaviors may be covered by any suite.

4. `{PROJECT_ROOT}/reports`
   - Prefer machine-readable JaCoCo XML reports when available, then HTML reports if XML is absent.
   - Use coverage to corroborate whether relevant source methods, branches, and lines were executed.
   - Report code coverage signals that explain behavioral gaps, but do not replace behavior-based judgment with raw JaCoCo percentages.
   - If multiple JaCoCo reports exist, evaluate their combined coverage as the union of all generated-test executions. Use source-file line and branch entries from XML when possible so the same covered line or branch is counted once even if it appears in more than one report.
   - Do not simply add aggregate counters from multiple reports when they may cover overlapping source code. Prefer an element-level union. If exact union is not possible, report each report separately and clearly state the approximation used for the combined signal.

5. `{PROJECT_ROOT}/src`
   - Use source code to resolve ambiguities in behavior, endpoints, validation, persistence, exception handling, and side effects.
   - If documentation and source code disagree, state the discrepancy and base coverage scoring on the documented business behavior while noting the implementation behavior.

## Evaluation Method

Follow these steps:

1. Build a workflow behavior inventory from `workflow-level-business-behavior-analysis.md`.
   - Assign stable IDs such as `B1`, `B2`, etc.
   - Preserve required, alternative, and optional step labels and exact function names.
   - Record the documented actor, state before, transition/decision, state after, API-visible output, and handoff for every step.
   - Separate primary-path success, alternative-path success, optional/supporting execution, and their respective business failure blocks.
   - Build a per-behavior coverage-item checklist:
     - `Required step attempt`: one item for every required `Step N` occurrence.
     - `Required step application reach`: one item for every required `Step N` occurrence.
     - `Required step context-valid success`: one item for every required `Step N` occurrence.
     - `Primary happy path`: one core item, covered only if all required steps execute in order and reach the terminal business outcome.
     - `Core failure`: one core item for every `Failure N` under a required `Step N`.
     - `Alternative path success`: one separate item per complete alternative path.
     - `Alternative failure`: one separate item per `Failure N` under an `Alternative Step A#`.
     - `Optional/supporting execution`: one separate item per `Optional Step V#`.
     - `Optional/supporting failure`: one separate item per `Failure N` under an `Optional Step V#`.

2. Build a function and endpoint map from `full-behavior.md`.
   - Map function names to endpoints or callable operations.
   - Record preconditions, expected successful results, and documented failure branches.
   - Build one invocation-checklist item per exact non-excluded function name.
   - Normalize concrete test URLs to documented route templates before matching.
   - When multiple functions share one method/route, use actor, role, query/body discriminator, documented variant, and source method to distinguish them. If the test call cannot distinguish the functions, mark the route as attempted but the exact function item as `Ambiguous`; do not silently credit every function sharing the route.

3. Build a test evidence map from the generated tests.
   - For each test case, list the operations it performs in order.
   - Include setup data, direct database operations, actor/auth context, all request values, dynamic response-to-request bindings, response assertions, persistence assertions, side-effect assertions, and final checks.
   - Identify whether the test verifies business outcomes or only checks status codes.
   - Detect database/SUT resets and transaction isolation. Never stitch steps from different tests when state is reset between them.

4. Correlate test evidence to each business behavior.
   - For the required workflow, report each function step separately using its exact step label and function name.
   - Mark `Attempted=Yes` whenever an executed generated test calls the step's mapped method/route. A malformed request or generic 4xx can count as an attempt.
   - Mark `Application reached=Yes` only with evidence that framework binding, generic authentication, and generic endpoint admission succeeded and the mapped controller/application function started. Use direct response evidence, test instrumentation, logs, or precise JaCoCo lines/methods. Aggregate class coverage without a traceable method/line is insufficient.
   - Mark `Context-valid success=Yes` only when the documented actor, pre-state, parameters, value bindings, successful response, state transition, and output all match.
   - Mark the happy-path item as covered only when all required workflow function steps are covered in order, with the relevant preconditions and parameter/value bindings.
   - Mark a failure item as covered only when one test establishes all earlier prerequisites, reaches the exact documented step/function, intentionally violates the exact documented business condition, and asserts an outcome traceable to the documented source discriminator.
   - A random 4xx/5xx, generic authentication rejection, parameter-binding failure, missing generic header/cookie, endpoint access-gate failure, infrastructure failure, or external-dependency availability failure does not cover a business failure item.
   - If the primary happy path is absent but one or more core required-step failure items are directly exercised, classify the behavior as `Partially Covered`, not `Not Covered`.
   - Do not count vague or merely similar failures as covered unless you label them separately as inferred evidence with low confidence, outside the coverage-item numerator.
   - Evaluate each alternative path separately. Do not combine half of one alternative with half of another.
   - Optional/supporting steps are not required for the core behavior status. Report them as `Executed`, `Not Executed`, or `Not Applicable`, with evidence.
   - Response content checks, persisted-state checks, derived-state checks, and subsequent read operations strengthen confidence and should be described, but absence of optional verification alone must not downgrade a behavior from `Covered` to `Partially Covered`.
   - A single endpoint call may support multiple behaviors only if the surrounding setup and assertions match each behavior's business meaning.
   - Generated exploratory calls that merely trigger HTTP 4xx/5xx responses should count only toward the corresponding failure case, not toward the successful business behavior.
   - Generated exploratory calls must still count toward function/API invocation and step-attempt metrics when their method/route matches.

5. Use JaCoCo coverage as a secondary signal.
   - Align code coverage evidence to the same required workflow steps and step-scoped business failure occurrences used in the behavior checklist.
   - Identify relevant classes, methods, lines, and branches for each required function and each source-discriminated business failure.
   - Avoid vague behavior-level code coverage statements such as "cleanup methods are not proven" unless they are tied to a specific documented function row.
   - Note when code is covered but behavioral evidence is weak or absent.

6. Assign coverage status and confidence.
   - Use one of these statuses for each behavior:
     - `Covered`: the primary happy path and all core required-step failure items are covered.
     - `Partially Covered`: at least one required workflow step has `Context-valid success=Yes`, or at least one core failure item is covered, but the primary path or another core failure remains uncovered.
     - `Not Covered`: no required workflow step has context-valid success and no core failure item is covered. Raw invocation, step attempt, or application reach alone does not change this status.
     - `Unclear`: available evidence is ambiguous or insufficient to judge safely.
   - Behavior status is intentionally based on context-valid business outcomes, not raw invocation. Report non-zero invocation/attempt evidence alongside a `Not Covered` behavior when appropriate.
   - Use confidence levels:
     - `High`: direct test evidence clearly covers the relevant checklist items and coverage evidence agrees.
     - `Medium`: direct test evidence covers at least one checklist item, but optional verification, assertions, or coverage corroboration are incomplete.
     - `Low`: conclusion depends on inference, incomplete artifacts, or ambiguous generated tests.

7. Compute business behavior coverage.
   - Count each documented business behavior once for the headline metric.
   - Suggested formula:
     - `Covered = 1.0`
     - `Partially Covered = 0.5`
     - `Not Covered = 0`
     - `Unclear = 0`
   - Business Behavior Coverage Percentage:
     - `(sum of behavior scores / total number of documented behaviors) * 100`
   - Also compute all of the following without mixing their denominators:
     - `Function/API invocation coverage`: exact non-excluded functions attempted / all exact non-excluded functions. Also report ambiguous shared-route matches separately.
     - `Required-step attempt coverage`: required step occurrences with a matching executed request / all required step occurrences.
     - `Required-step application-reach coverage`: required step occurrences proven to enter the mapped controller/application function / all required step occurrences.
     - `Required-step context-valid success coverage`: required step occurrences successfully executed with the documented business context / all required step occurrences.
     - `Primary workflow success coverage`: behaviors whose complete required workflow reaches the terminal outcome / all behaviors.
     - `Core required-step failure coverage`: covered failure occurrences under required steps / all failure occurrences under required steps.
     - `Alternative path success coverage`: fully covered alternative paths / all documented alternative paths.
     - `Alternative-step failure coverage`: covered failure occurrences under alternative steps / all failure occurrences under alternative steps.
     - `Optional/supporting execution coverage`: executed optional steps / all documented optional steps.
     - `Optional-step failure coverage`: covered failure occurrences under optional steps / all failure occurrences under optional steps.
     - `All workflow-context business-failure coverage`: covered required + alternative + optional failure occurrences / all such occurrences.
     - `Unique source business-branch coverage`: covered unique business branches / all unique business branches.

   - These metrics form a funnel and must be internally consistent:
     - context-valid success <= application reached <= attempted
     - primary workflow success cannot exceed context-valid required-step success evidence
     - business-failure coverage is independent of successful-step coverage but requires application reach
   - If any inequality is violated, flag the report as internally inconsistent and investigate before finalizing.

8. Build both failure denominators.
   - A workflow-context failure occurrence is keyed by `{behavior ID, step label, exact function name, source discriminator, concrete failure condition}`.
   - A unique source business branch is keyed by `{exact function name, normalized source discriminator, normalized concrete failure condition}`.
   - Deduplicate only for the unique-source metric. Do not silently deduplicate the workflow-context metric.
   - One test may cover one unique source branch while covering only one of several workflow-context occurrences. Credit another occurrence only when actor, prerequisite state, measure type, and surrounding workflow meaning also match.
   - Report numerator and denominator counts for both metrics and explain the difference.

## Important Judgment Rules

- Do not give credit for a behavior only because its endpoint was called. The call must match the behavior's business workflow.
- Do give invocation/attempt checklist credit when the endpoint is called, even when the call fails at binding, generic authentication, or endpoint admission. Keep that credit exclusively in the invocation/attempt layers.
- Do not require optional read-after-write or state-verification steps to mark the core behavior as `Covered` when the required workflow itself is executed correctly.
- Do not give full behavior credit for tests that cover only the primary happy path when core required-step business failures remain uncovered.
- Do not give full behavior credit for tests that only assert an HTTP status code if the required workflow needs additional setup, multiple ordered calls, specific request values, or a particular state transition that is absent.
- Do not give creation-behavior credit when the test only inserts data directly into the database as setup.
- Do not give successful-behavior credit for tests that only exercise missing-resource, validation, or server-error branches.
- Do not classify a behavior as `Not Covered` when a generated test directly covers a core required-step business failure; classify it as `Partially Covered` and explain that the successful workflow is still absent.
- Do give partial behavior credit when any required workflow step has context-valid success or any core required-step failure item is covered, even if the full happy path is not covered.
- Do give failure-case credit when a test intentionally creates or omits preconditions and asserts the documented failure outcome.
- Do not count "close" failure evidence in any business-failure numerator unless it directly maps to a documented step, function, source discriminator, and failure condition. If you mention close or inferred evidence, keep it separate from the counted metrics.
- Treat alternative and optional workflows as separate evidence, not mandatory evidence for the core behavior status.
- Use exact step labels and function names from `workflow-level-business-behavior-analysis.md` in all workflow and failure coverage sections.
- Ignore every source-document statement that its own business failure coverage is complete. Recompute generated-test coverage from test evidence.
- Do not report zero for function/API invocation or required-step attempt coverage when matching calls are present in the executed generated suite. Show the matched call count and representative tests.
- Do not use `Core behavior checklist coverage` as a synonym for all checklist layers. Call it `Core workflow-outcome checklist coverage`.
- Prefer explicit evidence from tests and source over assumptions from naming.
- If multiple generated suites exist, evaluate all of them together unless instructed to compare them separately.
- If JaCoCo reports are split across runs, calculate combined coverage as a union across reports whenever possible. Avoid double-counting overlapping lines, branches, methods, or classes.

## Required Output Format

Write the report in English Markdown using this structure:

# Business Behavior Coverage Report

## Executive Summary

- Project under analysis: `{PROJECT_NAME_OR_ROOT}`
- Test suites analyzed: list the generated test files or directories
- Coverage reports analyzed: list the JaCoCo reports used
- Source analyzed: list the source roots used
- Total documented business behaviors: `N`
- Covered: `N`
- Partially covered: `N`
- Not covered: `N`
- Unclear: `N`
- Business behavior coverage: `XX.X%`
- Combined JaCoCo coverage signal: `XX.X% lines`, `XX.X% branches`, `XX.X% methods`, or an explanation if exact combined coverage cannot be computed
- Function/API invocation coverage: `X/Y (XX.X%)`, plus `N ambiguous shared-route matches`
- Required-step attempt coverage: `X/Y (XX.X%)`
- Required-step application-reach coverage: `X/Y (XX.X%)`
- Required-step context-valid success coverage: `X/Y (XX.X%)`
- Primary workflow success coverage: `X/Y (XX.X%)`
- Core required-step failure coverage: `X/Y (XX.X%)`
- Alternative path success coverage: `X/Y (XX.X%)`
- Alternative-step failure coverage: `X/Y (XX.X%)`
- Optional/supporting execution coverage: `X/Y (XX.X%)`
- Optional-step failure coverage: `X/Y (XX.X%)`
- All workflow-context business-failure coverage: `X/Y (XX.X%)`
- Unique source business-branch coverage: `X/Y (XX.X%)`
- Core workflow-outcome checklist coverage: `X/Y`, where each behavior contributes one primary happy-path item plus its required-step failure occurrences

Briefly summarize the coverage funnel from invocation through application reach, context-valid step success, complete workflow success, and business failures. Explain explicitly when a non-zero invocation checklist coexists with zero business-outcome coverage.

## Coverage Matrix

Create a table with these columns:

| ID | Business Behavior | Required Steps Attempted | Application Reached | Context-Valid Steps | Primary Workflow | Core Failure Coverage | Status | Confidence |
|---|---|---|---|---|---|---|---|---|

For evidence cells, cite specific test names, operations, relevant source methods/classes, and combined JaCoCo report signals where useful. Keep entries concise but traceable.

## Behavior Details

For each business behavior, provide:

### `B#`: `{Behavior Name}`

- Business goal: one sentence.
- Required execution workflow coverage: a table with one row per required workflow step:

| Step | Exact Function Name | Actor/Pre-State | Operation And Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|---|---|---|

  - `Exact Function Name` must exactly match the workflow document.
  - `Attempted` records method/route exercise only.
  - `Application Reached` records entry into the mapped application function after generic gates.
  - `Context-Valid Success` records successful execution with the documented business context.
  - The happy-path item is `Covered` only if every row has `Context-Valid Success=Yes` in one continuous ordered scenario with the required bindings.

- Happy-path item: `Covered` or `Not Covered`, with one concise reason.
- Alternative workflow coverage: a separate table with one row per alternative path and its ordered steps:

| Alternative Path | Ordered Steps And Functions | Covered? | Test Evidence | Gap |
|---|---|---|---|---|

- Optional/supporting workflow coverage: a table with one row per optional step:

| Optional Step | Exact Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|

  - Mark each optional verification step as `Executed`, `Not Executed`, or `Not Applicable`.

- Additional verification evidence: response assertions, state checks, follow-up reads, or lack thereof.
- Core required-step business failure coverage:

| Step | Exact Function Name | Source Discriminator | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|---|

  - Mark each failure occurrence as `Covered`, `Not Covered`, or `Unclear`.

- Alternative-step business failure coverage: use the same columns in a separate table.
- Optional-step business failure coverage: use the same columns in a separate table.
- Required-step checklist summary:
  - Attempted: `X/Y`
  - Application reached: `X/Y`
  - Context-valid success: `X/Y`
- Core workflow-outcome checklist summary: `X/Y`, counting the primary happy path and required-step failures only.
- Workflow-context failure summary: `X/Y` across required, alternative, and optional step occurrences.
- Unique source-branch contribution: list the unique source keys exercised by this behavior and whether another behavior already exercises the same key.
- Status: `Covered`, `Partially Covered`, `Not Covered`, or `Unclear`.
- Confidence: `High`, `Medium`, or `Low`.
- Gap: what is missing for full behavior coverage.
- Recommended tests: concrete tests that would close the gap.

## Cross-Behavior Gaps

List systemic gaps, such as:

- Workflows that stop at status-code assertions.
- Missing read-after-write verification.
- Direct database setup replacing business operations.
- Missing negative paths.
- Missing edge cases around duplicate values, absent dependencies, invalid relationships, authorization, validation, concurrency, or idempotency, if relevant to the project.
- Code covered without business assertions.
- Business behaviors with no corresponding generated test.

## Suggested Additional Tests

Provide a prioritized list of implementation-ready test specifications. Do not provide only a one-sentence recommendation. Each proposed test must contain all of the following:

### Test `T#`: `{descriptive test name}`

- Priority: `P0`, `P1`, or `P2`.
- Target coverage item:
  - behavior ID and name
  - primary, alternative, optional, core failure, alternative failure, or optional failure
  - exact step label and exact function name
  - for failures, exact source discriminator and failure condition
- Test category: success, business failure, boundary, state transition, concurrency, ownership/eligibility, idempotency, or regression.
- Why this test is needed: identify the exact uncovered item and current evidence gap.

Initial state and fixture plan:
- State whether the test starts after a database/SUT reset.
- List every required aggregate, child row, relationship, actor identity, role, ownership link, lifecycle state, date/time assumption, feature/config value, and external-domain stub result.
- Prefer API-realizable setup when the behavior requires earlier functions. If direct database setup is necessary, provide the exact entity-level state and explain why it is a prerequisite rather than the behavior being tested.
- For a business-failure test, configure all unrelated parameters and prior states as valid so execution reaches the intended domain branch. Violate only the target business rule unless multiple invalid values are essential to that exact branch.

Complete API call sequence:

Provide a numbered table containing every call from setup through final verification. Do not omit authentication/setup calls, freshness reads, id capture, or verification calls that are necessary for an executable scenario.

| Order | Step/Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | State After Call |
|---:|---|---|---|---|---|---|---|---|---|

For every API call:
- Use the exact workflow step label and function name when it exists in the behavior document.
- Give a concrete method and path. Resolve path templates with example values while also naming the originating variable.
- Specify role/token identity, business-scoped caller identity, required cookies, content type, conditional headers such as `If-Unmodified-Since`, and correlation/idempotency headers when relevant.
- Give all path, query, form, and body parameters, including valid non-target fields needed to pass earlier validation.
- Show complete JSON/form bodies. Do not use placeholders such as `valid body`, `same as above`, `etc.`, or `appropriate values`.
- State where every dynamic value comes from, for example `Location -> avtaleId`, response-body `sistEndret -> If-Unmodified-Since`, returned child id, fixture id, or generated timestamp.
- State the expected HTTP status, important response headers, error discriminator/body, response fields, and persisted state after each call.

Parameter and state constraints:

Provide a table for every parameter that affects reachability or the asserted result:

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Range/Values | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|

Include, where relevant:
- UUID, national identifier, organization number, enum, date/time, decimal precision, string length, collection size, nullability, and required/optional constraints
- inclusive/exclusive numeric and date bounds
- start/end ordering and measure-specific duration limits
- actor role, issuer, persisted ownership, NAV unit, employer/participant/mentor relationship, and Altinn/domain eligibility
- agreement status, approval order, active/current child status, version/freshness value, and entered/annulled/deleted flags
- exact target-invalid value for a failure test and concrete valid values for every non-target constraint

Assertions:
- List exact HTTP status, response-header, response-body, and error-discriminator assertions.
- List database/entity assertions for created, updated, unchanged, deleted, active/inactive, versioned, or child-row state.
- List derived-status and business-invariant assertions.
- List event, notification, journal, message, recalculation, and other side-effect assertions, including explicit assertions that forbidden side effects did not occur on failure.
- Include a final read/verification call when needed to prove the terminal business outcome.
- Name the relevant source class/method/branch and expected JaCoCo corroboration, while keeping behavioral assertions primary.

Isolation and variants:
- Explain reset/cleanup requirements, fixed clock needs, deterministic ids, external stubs, and transaction/async waiting strategy.
- Identify nearby boundary variants that deserve separate parameterized tests, but do not combine semantically distinct failure branches into one test recommendation.
- State exactly which coverage numerator(s) and denominator item(s) this test would change if it passes.

## Notes And Assumptions

Document:

- Missing or unreadable artifacts.
- Any substitutions made for expected paths.
- Any ambiguity in generated tests, source code, or behavior documents.
- Any discrepancy between business documentation and implementation.

## Style Requirements

- Use precise, readable English.
- Be specific and evidence-based.
- Avoid vague statements such as "seems covered" unless accompanied by evidence and confidence.
- Keep the report useful for engineers who need to add missing tests.
- Do not paste large source snippets or full generated test bodies. Summarize and cite evidence instead.
- Do not hallucinate test behavior. If evidence is absent, say it is absent.
```
