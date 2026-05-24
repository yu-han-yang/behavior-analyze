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
- Cover both:
  1. failures of the core behavior function, and
  2. meaningful failures of required setup functions in the workflow.
- For each meaningful failure case, use this structure:
  - Failing function: `{exact function name}`
  - Failure condition: ...
  - Why it fails: implementation-backed explanation
  - Violated prerequisite or constraint: ...
- If a condition should fail by domain/API expectation but does not fail in the implementation, state that explicitly.

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