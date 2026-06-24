You are an expert in software engineering, REST API analysis, domain modeling, and reverse engineering.

You are given access to all files of a REST API service, including:
- OpenAPI/Swagger definitions
- implementation source code
- tests and seed data
- `full-behavior.md`, which contains function-level analysis

Your task is to produce a business/domain-level behavior analysis in English.

Important terminology:
- A function is a low-level, endpoint-focused API capability described in `full-behavior.md`.
- A behavior is a domain-facing capability or workflow.
- A behavior may be atomic and directly map to one function.
- A behavior may also be composite and combine multiple functions into a meaningful domain workflow.
- Include both atomic domain behaviors and composite domain behaviors when meaningful.

Behavior granularity and denominator rule:
- The output is a coverage-ready business behavior inventory. Preserve atomic behavior granularity unless there is a clear domain reason to compose functions.
- Each distinct domain-facing function from `full-behavior.md` should normally become its own supported behavior when it has its own endpoint/action, business result, state transition, read model, operational effect, or failure surface.
- A behavior may include setup or verification functions in its workflow, but those setup or verification functions must not cause several independent core capabilities to be merged into one behavior.
- Do not group independent capabilities only because they belong to the same aggregate, controller, UI area, read screen, or operational category.
- Composite behaviors are allowed only when multiple functions are required together to complete one indivisible business outcome and the later step consumes state produced by the earlier step. Even then, keep any independently meaningful function as its own behavior elsewhere when it has its own business result.
- The supported behavior count should usually be close to the number of domain-facing functions, and may be higher when one endpoint supports role-specific or state-specific business behaviors. It should not be far lower than the function count unless excluded functions are purely technical and the exclusion is explicitly justified.
- Keep separate behaviors for distinct lookup/read/export functions, distinct state transitions, distinct dry-run versus persistent operations, distinct admin repair operations, distinct bulk versus selected operations, and distinct publication/patching operations.
- For example, do not collapse agreement detail retrieval, agreement-number retrieval, version listing, PDF download, account lookup, and Salesforce visibility into one behavior. Do not collapse user context, organization lookup, code lists, feature toggles, variants, and health into one behavior. Do not collapse DVH patching, selected event publication, all-event publication, and dry-run publication into one behavior.

Function reference rule:
- When describing workflow steps, reference the exact function name from `full-behavior.md`.
- Do not write workflow steps only as raw endpoints.
- Each workflow step must include the exact function name and may include its core endpoint in parentheses.
- If the function requires path, query, body, form, header, or generated values, include those values in the workflow step.

Workflow completeness rule:
- “Required execution workflow” must include all functions required to establish the state needed by the behavior.
- If a behavior requires an existing resource, relationship, active state, rule, generated id, authenticated session, ownership link, or other prerequisite state, include the function steps that create or obtain that state unless the behavior explicitly starts from pre-existing database state.
- Prefer complete API-realizable workflows over assuming pre-existing state.
- Do not omit setup functions that are necessary for the behavior to succeed.
- If the same behavior can be established through alternative setup paths, mention the alternatives explicitly.
- For every setup function in the workflow, include the relevant path/query/body/form/header values.

Existing-state shortcut rule:
- Keep the complete API-realizable workflow as the main workflow.
- Additionally, explain which workflow functions can be skipped if equivalent state already exists.
- Do this in a separate section named `Existing-state shortcuts`.
- Mention direct database setup alternatives where relevant.
- Mention generated id, ownership, scoping, or value-reuse requirements that must still hold.

Execution versus verification rule:
- Separate required execution steps from optional read/verification steps.
- Use this structure inside every supported behavior:
  - Required execution workflow
  - Optional verification workflow
- Required execution workflow must contain only functions necessary to perform the behavior successfully.
- Optional verification workflow may contain read/list/retrieve functions used to inspect the result.
- Do not place optional verification functions inside the required execution workflow.

Parameter and state binding rule:
- For every behavior, explicitly state how values are reused across functions.
- Mention important bindings such as:
  - the same resource id/name reused across create, read, update, delete, and child-resource functions
  - the same child-resource id/name created by one function and consumed by later functions
  - the same parent/owner id/name scoping all child operations
  - generated ids, tokens, locations, ETags, cursor values, or response fields captured and reused
  - body/form/query/header values that create state and later path/body/form/query/header values that consume that state
- If values intentionally differ, explain the mismatch and its domain meaning.

Business failure branch scope rule:
- In `Failure and exceptional cases`, include only concrete, implementation-backed business failure branches.
- A countable business failure branch is a branch where the request has passed ordinary request parsing and generic endpoint access gates, and the implementation rejects or changes behavior because of a domain rule, lifecycle state, ownership/eligibility rule, business invariant, business resource state, or persisted data relationship.
- Include all distinct source-level business failure branches for the behavior's core function. Do not choose only one representative failure when the source has multiple guards, exception types, `Feilkode` values, state-machine rejections, duplicate/idempotency checks, ownership checks, or domain not-found checks.
- Treat each distinct business condition as a separate failure entry, even if several entries use the same endpoint, HTTP status, exception class, or failing function.
- Use the implementation's most precise discriminator when splitting failures: each distinct `Feilkode`, exception class, explicit guard branch, state-machine rejection, or named validation method outcome must be its own failure entry.
- Do not merge multiple source discriminators into a broad condition. For example, split separate date-limit `Feilkode` values such as start-after-end, too-early start, end-date upper bound, Sommerjobb too early, Sommerjobb too late, Sommerjobb too long, and temporary wage-subsidy 12/24-month duration limits. Split separate calculation failures such as invalid wage-subsidy percent and invalid OTP rate when the source exposes them separately.
- Do not write umbrella failure entries such as "any creation rule fails", "same validation failures as the persistent update", "business validation fails", "invalid state", or "domain validation fails". Replace them with the individual concrete source-level conditions, each as its own failure entry.
- Do not write semi-umbrella failure entries such as "required content fields are missing", "measure-specific date limits are violated", "calculation inputs are invalid", or "approval prerequisites are missing" when the source exposes individual fields, `Feilkode` values, exception classes, or guard branches. Split them to the source-level discriminator.
- When a behavior reuses a setup function from another behavior, do not copy the setup function's concrete failures into this behavior's counted failure list. Instead, list the setup function under `Setup failure dependencies (not counted here)` and refer to the behavior or function where those failures are counted.
- Include business-scoped access and eligibility failures when they are part of the domain workflow, such as advisor write access to a participant, employer Altinn eligibility for a selected company and measure type, notification ownership/readability, or agreement-party access tied to a specific aggregate.
- Include business negative results from upstream/domain services when the result has business meaning, such as unknown organization, no employer account, protected participant status, missing NAV unit, or missing Altinn right.
- Exclude framework-level or technical failures from `Failure and exceptional cases`. Do not count or list them as failure entries.
- Excluded failures include parameter binding/parsing errors, malformed JSON, invalid UUID/string-to-enum conversion, generic missing request headers/cookies, generic authentication, generic authorization, unsupported role-cookie/token-issuer combinations before the domain operation starts, endpoint access gates such as configured system-user, developer-admin, DVH group, delete-marker-admin, or operational group checks, and infrastructure or external dependency availability failures.
- Exclude these technical/access-gate failures even if `full-behavior.md` lists them, even if the implementation throws `FeilkodeException` or `TilgangskontrollException`, and even if they produce a 4xx/5xx response.
- Before finalizing a behavior, audit every failure entry and remove it from `Failure and exceptional cases` if its condition is mainly "unsupported role/token", "role cookie and token issuer mismatch", "caller lacks developer-admin access", "caller lacks DVH group access", "caller is not configured system user", "caller is not delete-marker admin", "generic auth", "missing cookie/header", "malformed request", "invalid UUID/enum", "database unavailable", or "external dependency unavailable".
- Business-scoped access is countable only when it is about a concrete domain object or domain eligibility relation after caller context exists, such as access to a specific agreement, participant, notification, employer company, Altinn right for a selected company and measure, or decision-maker NAV unit scope. Generic endpoint/group admission is not countable.
- If an excluded technical or access-gate failure is important for implementers, mention it briefly under `Implementation notes`, not under `Failure and exceptional cases`.
- If no concrete business failure branch is visible after excluding technical/access-gate failures, write exactly `None.` under `Failure and exceptional cases`. Do not create a `Failing function` row whose condition says "none identified", "not applicable", or similar.

Failure deduplication and denominator rule:
- The failure denominator is a unique source-level business failure inventory, not a repeated workflow dependency inventory.
- Count a business failure exactly once by the tuple `{exact failing function, source discriminator, concrete failure condition}`.
- The `source discriminator` must be the most precise implementation clue available, such as a `Feilkode` value, exception class, guard method plus branch condition, state-machine transition rejection, repository not-found lookup, or domain-service negative result.
- Atomic behaviors count only failures of their own core function under `Core business failure branches (counted)`.
- Setup functions that appear in `Required execution workflow` must be listed under `Setup failure dependencies (not counted here)` without repeating their individual failure entries. Include the exact setup function name and, when available, the behavior number/name where that setup function's core failures are counted.
- Composite behaviors are explanatory workflows and must not create additional failure denominator entries. Under composite behaviors, reference the participating atomic behaviors/functions in `Setup failure dependencies (not counted here)` rather than copying their failure entries.
- If a required setup function has no separate atomic behavior but has concrete business failures, create a separate atomic behavior for that function unless it is purely technical. Only as a last resort, list it under `Additional setup-only business failures (counted once)` and make clear that these entries are not repeated elsewhere.

Analysis goals:
1. Read `full-behavior.md` and identify all available functions and exact function names.
2. Read OpenAPI/Swagger and implementation source code to understand the domain and verify actual behavior.
3. Produce domain-level behaviors from available functions.
4. Include both atomic behaviors corresponding to one function and composite behaviors combining multiple functions.
5. For every supported behavior, provide a complete API-realizable workflow unless the behavior explicitly starts from pre-existing database state.
6. Identify domain scenarios that appear expected or natural for this service but are not supported by any combination of available functions.
7. Do not invent supported behaviors that are not backed by `full-behavior.md`, OpenAPI/Swagger, or source code.
8. Prioritize implementation logic when OpenAPI/Swagger and source code disagree, and explicitly mention discrepancies.

Do not execute the API.

Output requirements:
- The entire output must be in English.
- Do not include Chinese.
- Do not mention this prompt.
- Do not rewrite the function-level document.
- Use “Behavior” only for business/domain-level capabilities or workflows.
- Keep the analysis grounded in the existing service.
- Do not include a separate “Composed functions” section.
- Do not include a section named “Required state and prerequisites” in supported behaviors.
- Do not output prerequisite bullets outside the workflow and shortcut sections. Required state must be expressed through the complete workflow, existing-state shortcuts, parameter/value bindings, constraints, and failure cases.

Required output structure:

# Domain-Level Behavior Analysis

## Domain Summary
Briefly describe the service domain, main resources, and business concepts.

## Available Function Inventory
List all functions from `full-behavior.md`, grouped by domain area.

For each function, include:
- exact function name
- core endpoint(s)
- short domain meaning

## Supported Business Behaviors

### Behavior {N}: {business behavior name}

Business goal:
Describe the domain-facing goal.

Domain context:
Explain why this is a meaningful behavior in this service.

Starting point:
State the baseline used for the main workflow. Prefer `No prior service state` when a complete API-realizable workflow can be shown.

Required execution workflow:
1. Use function `{exact function name from full-behavior.md}` (`{HTTP_METHOD} {path}`) with `{parameterName}={value}` ... to ...
2. Use function `{exact function name from full-behavior.md}` (`{HTTP_METHOD} {path}`) with `{parameterName}={value}` ... to ...
3. Continue until the domain behavior is actually completed.

Optional verification workflow:
1. Use function `{exact function name from full-behavior.md}` (`{HTTP_METHOD} {path}`) with `{parameterName}={value}` ... to verify or inspect ...
2. If no verification function is needed, write `None.`

Existing-state shortcuts:
- If equivalent state already exists, identify which workflow step can be skipped.
- Mention direct database setup alternatives where relevant.
- Mention generated id, ownership, scoping, or value-reuse requirements that must still hold.
- The core behavior action itself generally cannot be skipped for that behavior.

Parameter and value bindings:
- Explicitly describe which path/query/body/form/header values must be reused across functions.
- Include generated id, token, cursor, response-field, or location binding when relevant.
- Include ownership/scoping binding when relevant.
- If a later function consumes state created by an earlier function, state the exact link.

Business result:
Describe the concrete final domain state.
State what exists, what no longer exists, what relationships exist or were removed, what values changed, and what validity/status flags are set.
If the endpoint returns an error but still mutates state, explicitly describe both the response outcome and the persisted state outcome.

Constraints and invariants:
- Mention business constraints enforced by the implementation.
- Mention constraints implied by the API but not enforced, if relevant.
- Mention ownership/scoping rules.
- Mention side effects such as cascading deletes, automatic derivation, invalid-state persistence, cleanup, or lack of reevaluation.

Failure and exceptional cases:
- Cover only concrete business failure branches as defined by the `Business failure branch scope rule`.
- Use this exact subsection structure:
  - `Core business failure branches (counted):`
  - `Setup failure dependencies (not counted here):`
  - Optional only when unavoidable: `Additional setup-only business failures (counted once):`
- Under `Core business failure branches (counted)`, cover all concrete business failures of the behavior's core function only.
- Under `Setup failure dependencies (not counted here)`, list setup functions required by the workflow whose failures are counted in their own atomic behaviors. Do not duplicate their failure rows here.
- Use `Additional setup-only business failures (counted once)` only when a required setup function has concrete business failures but cannot reasonably be represented as its own atomic behavior.
- Inspect implementation source, not only `full-behavior.md`, to find business failure branches. `full-behavior.md` may be incomplete or may contain only representative failures.
- Do not collapse multiple source-level business guards into a single broad condition such as "required fields are incomplete", "measure-specific date limits are violated", "calculation inputs are invalid", "approval prerequisites are missing", "invalid state", or "business validation fails" when the implementation exposes distinct business conditions, exception classes, guard branches, or error codes.
- Do not create failure entries for excluded technical/access-gate failures, including unsupported role-token combinations, role cookie/token issuer mismatch, generic missing `innlogget-part`, configured system-user checks, developer-admin checks, DVH group checks, delete-marker-admin checks, malformed request values, or infrastructure/database availability failures.
- Do not create placeholder failure entries for functions with no retained business failures. If no retained core business failures exist for this behavior, write `None.` under `Core business failure branches (counted)`. If there are no setup dependencies, write `None.` under `Setup failure dependencies (not counted here)`.
- For each counted core failure case, use this structure:
  - Failing function: `{exact function name}`
  - Source discriminator: `{Feilkode value, exception class, guard method plus branch condition, state-machine rejection, repository lookup, or domain-service negative result}`
  - Failure condition: ...
  - Why it fails: implementation-backed explanation
  - Violated prerequisite or constraint: ...
- For each setup dependency, use this structure:
  - Setup function: `{exact function name}`
  - Counted under: `{Behavior N: behavior name}` or `{function name}` if the behavior number is not known yet
  - Dependency reason: why this setup function is required by the workflow
- If a condition should fail by domain/API expectation but does not fail in the implementation, state that explicitly.
- If a possible failure is excluded because it is framework-level, generic auth/access-gate, or infrastructure-only, do not list it here; optionally summarize the exclusion in `Implementation notes`.

Implementation notes:
- Mention relevant implementation details, discrepancies, or side effects.

Behavior granularity rule:
- Include small atomic behaviors when they are meaningful business capabilities, such as creating, retrieving, updating, deleting, listing, activating, deactivating, or otherwise changing a domain resource.
- Also include larger composite behaviors when multiple functions together form a complete domain workflow.
- Avoid inventing behaviors that are not supported by the source or OpenAPI.

## Unsupported or Missing Business Behaviors

Before writing missing behaviors:
- Identify expected domain behaviors that are not possible through any combination of functions.
- Include missing behaviors for first-class resource lifecycle gaps when applicable, such as inability to list, retrieve, update, search, validate, rename, or safely delete important domain resources.
- Include missing behaviors for validation, ownership, transactionality, consistency, audit/search, and reevaluation gaps when domain-relevant.
- Order missing behaviors by priority: all `Critical domain gap` first, then `Important robustness gap`, then `API ergonomics gap`.

### Missing Behavior {N}: {missing behavior name}

Priority:
Classify as one of:
- Critical domain gap
- Important robustness gap
- API ergonomics gap

Expected business goal:
Describe the domain scenario a user would reasonably expect.

Why it is unsupported:
Explain why no combination of existing functions can implement this behavior.

Existing functions considered:
- `{exact function name}`: explain what it can do and why it is insufficient.
- Include all functions that appear close to satisfying the missing behavior.

Missing capability:
Identify the missing endpoint, validation rule, query, state transition, persistence behavior, transaction behavior, ownership check, generated-id lookup, or data model support.

Proof that function composition is insufficient:
Provide a strict explanation of why chaining existing functions cannot produce the expected domain behavior.
Explicitly mention state that cannot be created, prevented, preserved, queried, validated, rolled back, distinguished, recomputed, or ownership-checked.
If delete-and-recreate or manual repair is possible but not equivalent, explain why it is not equivalent.

Evidence from existing functions/source:
- Reference relevant available functions by exact function name.
- Reference implementation behavior or OpenAPI gaps.
- Mention implementation/OpenAPI disagreement when relevant.

Business impact:
Explain what domain workflow is blocked, unsafe, ambiguous, stale, or weakened.

## Cross-Behavior Observations
Summarize important system-wide observations, such as:
- weak validation
- missing uniqueness constraints
- incomplete ownership checks
- constraint or rule evaluation behavior
- cascading delete behavior
- event-driven versus continuous consistency
- implementation/OpenAPI discrepancies
- error responses that still persist state changes

## Coverage Summary
Provide a concise summary of:
- supported domain areas
- partially supported domain areas
- unsupported domain areas
