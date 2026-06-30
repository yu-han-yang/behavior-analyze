You are an expert in software engineering, REST API analysis, domain modeling, workflow analysis, state-machine analysis, and reverse engineering.

You are given access to all files of a REST API service, including:
- OpenAPI/Swagger definitions
- implementation source code
- tests and seed data
- `full-behavior.md`, which contains function-level analysis

Your task is to produce a workflow-level business behavior analysis in English.

Do not execute the API.

## Core terminology

- A **function** is a low-level, endpoint-focused API capability named in `full-behavior.md`.
- A **step** is one invocation of a function within an ordered business workflow.
- A **behavior** is an end-to-end, domain-facing workflow that moves the domain from a meaningful starting state to a meaningful business outcome.
- A **business failure branch** is a concrete, implementation-backed rejection or exceptional branch caused by a domain rule, lifecycle state, business invariant, resource relationship, ownership rule, or domain eligibility rule after ordinary request parsing and generic endpoint admission have succeeded.

A function is therefore normally a step, not a behavior. Do not use the function inventory as the behavior inventory.

## Source precedence and evidence

Use the following evidence order:
1. Implementation source code and persisted-state behavior
2. Tests and seed data
3. OpenAPI/Swagger
4. `full-behavior.md`

Use `full-behavior.md` to discover functions and exact function names, but inspect implementation source for workflow ordering, state transitions, value propagation, side effects, and business failure branches. It may contain incomplete or representative failure information.

When sources disagree, follow implementation behavior and record the discrepancy.

## Required analysis method

Before writing behaviors:
1. Inventory every function from `full-behavior.md`.
2. Identify the domain aggregates, actors, lifecycle states, state transitions, persisted relationships, and externally visible business outcomes.
3. Build a mental state-transition graph:
   - which function creates or discovers the initial domain state
   - which function changes that state
   - which later function requires or consumes the changed state
   - which function completes the actor's business goal
   - which functions only verify, display, configure, or technically support the workflow
4. Trace the complete implementation call graph for every function selected as a workflow step, from controller through application/service methods, domain methods, strategies, validators, repositories, event handlers executed synchronously, and business-significant upstream-result handling.
5. Build one internal master source-branch catalog per function before writing any behavior. Create one catalog item for every explicit or implicit rejection, exception throw, negative domain result, state-machine guard, ownership/eligibility decision, duplicate/idempotency branch, domain not-found branch, and error response in the complete traced call graph. Give each item a stable internal identity based on source path, method, and exact guard condition rather than its prose description.
6. For every catalog item, record the full call path, exact guard predicate, actual runtime discriminator, classification as `business failure`, `excluded technical/access-gate failure`, `successful negative result`, or `successful no-op/partial result`, and its actor/subtype/lifecycle/request-variant reachability dimensions.
7. Derive each invocation-specific failure ledger by filtering the master catalog for that invocation's actor operation, resource subtype, lifecycle scope, request variant, and state provenance. Do not rediscover or paraphrase the same function independently in each behavior.
8. Form behaviors from causal, ordered paths through that state-transition graph.
9. Internally audit every function and classify it as:
   - required step in one or more business workflows
   - optional verification or supporting lookup
   - standalone one-step business workflow, only when justified by the rules below
   - excluded technical, framework, access-gate, configuration, or infrastructure capability
10. Build a function-to-workflow audit mapping for all non-excluded functions.
11. Build a behavior index containing every final behavior name, a one-sentence description of its terminal outcome, and a stable link to that behavior.
12. Reconcile repeated invocations across all behaviors. Equivalent invocation scopes must have identical applicable branch identities and counts; any difference must be explained by a concrete actor, subtype, lifecycle, request-variant, or preceding-state distinction visible in the final workflow.

The function classification and state-transition graph are analysis tools only. Do not output a lifecycle/state-machine section or a function-classification section.

Do not target a predetermined behavior count. Derive the number of behaviors from distinct business goals and terminal domain outcomes, not from the number of endpoints.

## Workflow-level behavior boundary rules

- One behavior must represent one coherent actor goal and one meaningful terminal business outcome.
- Combine multiple functions into one behavior when they have a real causal order: a later function requires, validates, consumes, changes, approves, publishes, exports, or otherwise depends on state or values produced by an earlier function.
- Include all API-realizable steps needed to reach the final outcome. Do not begin at an intermediate state merely to make a function look like an independent behavior.
- Do not create a separate behavior for every function and then repeat those functions inside a workflow-level behavior.
- Do not output duplicate versions of the same business journey at different granularities.
- A shared function may appear as a step in multiple behaviors when it is genuinely required by multiple distinct business outcomes. This reuse does not create a separate behavior by itself.
- Split workflows when they have materially different actor goals, terminal states, lifecycle branches, persisted effects, or business meaning. For example, approval and rejection are different terminal outcomes even when they share setup steps.
- Do not merge an entire aggregate lifecycle into one oversized behavior when it contains independent actor goals or stable business milestones. End a behavior at a state that is meaningful to the domain or to a responsible actor.
- Treat alternative API paths that reach the same business outcome as workflow variants inside one behavior only when their actor responsibility, eligible input population, validation rules, persistence effects, transaction guarantees, completion timing, failure policy, and terminal state are materially equivalent.
- Split paths into separate behaviors when one path is synchronous and another asynchronous, one is best-effort and another atomic, one filters eligible records and another accepts explicitly selected records, one silently ignores missing items and another rejects them, or endpoint completion means different things.
- Treat dry-run and persistent functions as steps or variants of the same intended business workflow when the dry-run is a preflight for the persistent action. Do not create a separate behavior for a dry-run merely because it has a separate endpoint.
- Treat selected-item and bulk functions as variants of one behavior only when they perform the same domain operation with equivalent eligibility, transactionality, per-item failure handling, completion timing, and persisted outcome. Split them when any of those guarantees differ.
- Read, list, lookup, search, history, PDF, and status functions should normally be setup, decision-support, or optional verification steps inside a workflow.
- A read-only function may be a standalone one-step behavior only when the returned artifact or decision-support result is itself a complete, independently meaningful actor goal, such as obtaining a required document or work queue.
- A mutating function may be a standalone one-step behavior only when it starts from legitimately pre-existing domain state, reaches a complete independent business outcome, and has no API-realizable predecessor required for that outcome.
- One behavior must have one unambiguous terminal outcome. If the same endpoint can successfully mutate state, successfully do nothing, partially process records, enqueue asynchronous work, or persist only a subset, either constrain the behavior's starting state to one outcome or split materially different outcomes into separate behaviors. Do not write a contradictory terminal outcome such as "state X, except that it may remain unchanged."
- Every subtype, product, measure, actor variant, or initiator named in the behavior name, business goal, primary actors, starting state, terminal outcome, constraints, or failures must have a complete concrete execution path in that behavior. If the workflow demonstrates only one variant, narrow the behavior text to that variant; otherwise add a complete alternative path or split the behavior. Do not use a broad terminal outcome to justify failures for variants that have no workflow path.
- Health probes, generic feature/configuration lookups, generic caller-context endpoints, framework helpers, and operational access gates are not business behaviors. Exclude them or classify them as technical support.
- For every one-step behavior, explicitly justify why no meaningful API-realizable predecessor or successor belongs to the same business goal.

## Workflow completeness rules

- Prefer a complete API-realizable main workflow beginning with `No prior service state` when the API exposes the functions needed to create the prerequisite state.
- Use `Existing domain state` only when the service does not expose a function that can create that state, when the state legitimately originates outside this service, or when the behavior is an operational/batch action over existing records.
- Required execution steps must be ordered. Each later step must state which earlier state or output it consumes.
- Every value consumed by a later step must have an explicit provenance. State whether it comes from a response body field, response header, `Location`, cookie, token claim, generated value, user input, earlier request value, or a required read function.
- Verify response contracts in implementation source. Do not claim that an endpoint returns an id, timestamp, version, ETag, status, token, cursor, or updated resource unless the response actually exposes it.
- A persisted value is not automatically an API-visible output. If a mutating endpoint returns `void`, `204`, an empty body, or omits the newly generated value needed by the next step, add the exact read/retrieve function as a required execution step before that value is consumed.
- When optimistic concurrency or another freshness token is enforced, include every read needed to obtain the initial token and every refresh read needed after a mutation that changes the token but does not return the new value.
- If a header or parameter is syntactically required by the endpoint but ignored by business logic, still include a concrete, parseable value in every required, alternative, and optional invocation and explicitly state that the implementation does not enforce its semantic meaning. Such a value may be supplied as caller input; it must not be described as a freshness value returned by an earlier step unless an earlier step actually returned it.
- Never write `latest read response`, `current version`, `known token`, `existing id`, or equivalent provenance shorthand unless the producing read is an earlier required step or the exact value is explicitly declared as known in `Starting state`. A value that exists only in persistence is not known to an API caller.
- Do not put optional inspection or verification functions in the required execution workflow.
- Put valid alternative creation, approval, or transition paths under `Alternative valid workflow paths`.
- If a workflow requires a user-selected value returned by a lookup, include the lookup as a required step only when the API workflow cannot proceed correctly without it. Otherwise classify it as supporting.
- Include relevant path, query, body, form, and business-scoped header values for each step.
- Name concrete request fields and representative bound values. Do not use placeholders such as `complete fields`, `all required data`, `valid request`, `same inputs`, or `with resource and cutoff` when implementation or OpenAPI exposes the actual field names.
- Capture and reuse generated ids, business reference numbers, child-resource ids, response fields, locations, versions, tokens, cursors, dates, actor identities, ownership scope, and other handoff values.
- State the domain state before and after every mutating step.
- State side effects such as notifications, events, child-row creation/deletion, recalculation, derived status changes, journal markers, and partial persistence.

## Request-contract and example-value validation

- Validate every example request against implementation request DTOs, constructors, enums, OpenAPI schemas, and source-level field validation before using it in a workflow.
- Every enum literal must exist exactly in the implementation enum or accepted wire-value mapping. Never invent a plausible enum value.
- Every request body must use the implementation's actual JSON/object shape. Do not represent an object with boolean fields as an enum, list, string, or prose label.
- For nested request bodies, name every required nested object and field. Provide representative values with the correct scalar, list, set, enum, date, identifier, and nullability types.
- If OpenAPI and implementation disagree about a request field, follow the implementation and record the discrepancy in `Implementation notes`.
- Verify that a successful setup request actually satisfies downstream completeness checks. A workflow is not API-realizable if its example body would later fail an omitted required-field, subtype, calculation, or relationship guard.
- Do not use invented identifiers whose format fails source validation. When seed/test data supplies a valid reusable value, prefer it; otherwise use a clearly representative value that satisfies the source format.
- When one behavior claims to cover multiple resource subtypes, measures, products, actor variants, or request shapes, provide a complete concrete request path for each materially different variant or split them into separate behaviors. Do not show one subtype's request while claiming another subtype's terminal outcome or failures.
- Include every syntactically required path, query, body, form, cookie, and header value even when business logic ignores it. Explain ignored values in `Implementation notes`; do not omit them from the invocation.

## Alternative workflow path rules

- An alternative path must be a complete ordered route from the behavior's declared starting state, or from an explicitly identified branch point in the required workflow, to the same terminal outcome.
- Do not present a flat menu of unrelated replacement operations as one alternative path.
- Give every path a unique letter and every operation within it a unique step id: `Alternative Path A`, `Alternative Step A1`, `Alternative Step A2`, and so on.
- State exactly which required steps are replaced, retained, or resumed after the alternative path.
- If two alternative operations are causally dependent, place them in the same alternative path in execution order.
- If an alternative reuses a function already present in the required workflow, it is still a distinct invocation. Give it its own failure subsection and document branches reachable in that alternative state.

## Function reference rules

- Every workflow step must begin with the exact phrase `Use function` followed by the exact function name from `full-behavior.md`.
- Do not identify a step only by endpoint.
- Include the endpoint after the exact function name when available.
- Do not invent function names or silently rename them.

Use this form:

`Use function {exact function name from full-behavior.md} ({HTTP_METHOD} {path}) ...`

## Failure-ledger construction and cross-workflow reconciliation

Construct failure coverage from source before writing prose:

1. Create a master branch catalog for each function from its complete synchronous call graph. Each internal catalog item must contain:
   - stable source identity: exact source path, method, and guard predicate or lookup outcome
   - full call path from endpoint to the guard
   - actual runtime discriminator after exception construction and response translation
   - one independently triggerable failure condition
   - classification and exclusion reason when excluded
   - applicability dimensions: endpoint/request variant, actor operation, resource subtype, lifecycle state, ownership relation, and relevant preceding state
2. Create an invocation signature for every workflow step using the exact function, endpoint/request variant, actor operation, resource subtype, lifecycle scope, and preceding-state provenance. Request example values do not create a new signature unless they select a different source branch or domain variant.
3. Derive the invocation ledger by filtering the master catalog. Do not manually create a fresh list from memory for each behavior.
4. Compare every pair of repeated invocation signatures across the document:
   - identical signatures must have the same retained branch identities and count
   - a different count is permitted only when a source-backed applicability dimension differs
   - when counts differ, verify the exact included/excluded branch identities, not merely the totals
5. Perform bidirectional reconciliation:
   - source-to-document: every applicable catalog item appears exactly once under the invocation
   - document-to-source: every documented failure maps to one real reachable guard and actual discriminator

Matching numeric totals alone never proves completeness. `N` source-ledger items and `N` documented blocks may still hide an omitted branch, an invented branch, a duplicate, or a branch attached to the wrong subtype or discriminator.

To determine the actual runtime discriminator, trace the complete emission chain: throw site or negative result, exception constructor, superclass constructor, wrapper/rethrow logic, and exception-to-response mapping. Do not infer a `Feilkode`, error key, status, or message from an exception class name or from a similarly named enum member. An enum member that exists in source but is not emitted by the traced branch is not that branch's discriminator. If no stable runtime code exists, use the exact exception class or guard predicate instead.

## Mandatory business-failure discovery procedure

Apply this procedure independently to every required, alternative, and optional function invocation:

1. Start at the concrete endpoint method and record every branch that can reject, throw, return an error, or deliberately change the result.
2. Follow every invoked application, service, domain, strategy, validator, repository, and business-upstream-result method. Continue recursively until the call graph reaches persistence, a successful return, an excluded infrastructure boundary, or a previously audited method.
3. Expand helper methods. Do not summarize a helper as `validation fails`; enumerate every distinct guard inside it.
4. Expand subtype dispatch. Record which branches apply to each concrete subtype, actor, lifecycle state, and request variant represented by the behavior.
5. Expand compound guards. If independently triggerable business conditions share one `if`, error code, exception, or message, record each independently triggerable condition as its own ledger item.
6. Inspect repository lookups, optional/nullable results, duplicate checks, ownership joins, parent-child relations, state transitions, and business-significant upstream negative responses.
7. Trace every exception or error result through constructors, inheritance, wrappers, and response mapping to identify what the implementation actually emits. Record both the guard identity and emitted discriminator; do not substitute a plausible but unused enum value.
8. Inspect exception translation only to understand the response; do not replace the original business discriminator with a generic HTTP status.
9. Classify every ledger item using the business-failure scope rules below.
10. For each invocation, derive the complete set of business failures reachable within the behavior's explicitly declared scope. Use the successful workflow state before that step as the baseline, then vary business input values, lifecycle state, freshness, object identity, persisted relationships, ownership, and eligibility as needed to trigger every business guard while retaining the same endpoint, resource subtype, actor intent, and domain operation. Do not limit failure discovery to the fixed happy-path example values.
11. Reconcile source-to-document and document-to-source before writing. The source ledger and documented blocks must contain the same branch identities, not only the same number. A branch proven unreachable for the exact behavior scope must be removed or cause the behavior to be narrowed/split; retain an internal source-backed exclusion reason.
12. Compare the resulting branch identities with every equivalent invocation of the same function elsewhere in the document. Resolve unexplained differences before declaring coverage complete.

Tests are supporting evidence, not the branch denominator. A source-backed business branch must be included even when no test covers it. Conversely, do not include a test scenario when the implementation cannot reach it.

## Business failure branch scope

For every required execution step, alternative execution step, and optional verification/supporting step listed in a behavior, enumerate the complete set of distinct implementation-backed business failure branches reachable at that exact invocation under the behavior's declared resource subtype, actor, starting state, preceding steps, and parameter values. `All` means every source-ledger business item in the complete traced call graph, not representative examples.

The successful invocation values establish the workflow, but they do not define the failure denominator. Include every business rejection that can be triggered by a business-level failure variant of that same invocation. For example, a successful update uses valid dates, yet all source-backed invalid-date, stale-version, lifecycle, ownership, subtype-applicable completeness, and invariant branches of that update must still be documented. Do not include failures belonging only to a different subtype, actor operation, endpoint, or unreachable helper branch.

Include:
- domain lifecycle or state-machine rejections
- business invariant violations
- distinct domain validation guards
- duplicate or idempotency conflicts with business meaning
- domain resource not-found branches when absence prevents the business operation
- object-scoped ownership or access failures for a specific aggregate, account, customer, owner, child resource, notification, or other domain object
- business eligibility failures for a specific person, organization, product or program type, organizational unit, or persisted relationship
- business-significant negative results returned by domain/upstream services, such as an unknown business entity, ineligible customer, missing settlement account, missing organizational unit, or missing delegated right
- persisted-state conflicts and invalid business relationships
- business failures thrown by nested helpers, subtype strategies, constructors, synchronous domain listeners, and synchronous event handling reached by the invocation
- business failures that share a discriminator with another condition but violate a different independently triggerable business rule
- business failures of setup and retrieval functions when the behavior starts from pre-existing or externally mutable state, or when the failure remains possible after earlier workflow steps

Ignore completely and do not list anywhere:
- framework-level parameter binding, parsing, deserialization, malformed JSON, invalid UUID, or string-to-enum conversion
- generic missing headers or cookies
- generic authentication or token validation
- generic authorization before a concrete domain object or eligibility relation is evaluated
- unsupported role-cookie/token-issuer combinations
- endpoint access gates such as configured system-user checks, generic administrator groups, data-export groups, deletion-operator groups, or operational group membership
- infrastructure failures, database availability, network failures, timeouts, and external dependency availability failures

If a controller/framework requires a header, cookie, body field, or parameter before entering the endpoint method, its absence is a parsing or binding failure and is excluded even when a deeper domain method contains a null check for the same value. Include the domain branch only when a successfully parsed request can reach it, such as a stale but present concurrency token. Never combine an excluded missing-input condition with a retained business condition in one failure entry.

Business-scoped access is included only when caller context already exists and the decision concerns a concrete domain object or domain eligibility relationship. Generic endpoint admission is excluded.

A role check performed before a concrete domain resource or domain eligibility relationship is loaded is normally a generic endpoint-admission gate and must be excluded. A role or identity check is countable only when it evaluates the caller against a specific domain object, party relationship, owner, subject, organization, or other persisted business scope.

Do not turn a generic role gate into a business failure merely because the operation has a business-sounding name. Use source execution order: if the role/group/token decision happens before a concrete domain object or eligibility relation is evaluated, exclude it.

An upstream result is included only when the result has concrete business meaning. Failure to reach the upstream system is excluded.

## Business failure branch granularity

- Attach every failure to the exact workflow step whose function encounters it.
- Include all distinct source-level branches; do not select only representative examples.
- Filter function-wide failures by path reachability. Do not copy failures for resource subtypes, measures, actors, lifecycle branches, or request variants that cannot occur in the behavior being described.
- Do not use one concrete happy-path example to suppress failures that remain reachable for other explicitly declared variants of the same behavior. Either document each variant and its full failure set or split the behavior.
- Trace conditional guards through helper methods. When a helper throws different errors for different subtypes, include only the branches enabled by the current behavior and explicitly name the enabling condition.
- Use the most precise source discriminator actually emitted by the traced branch: exact runtime `Feilkode` or error key, exact exception class, guard method plus branch condition, state-machine rejection, repository lookup outcome, named validation result, or domain-service negative result. Verify exception constructors and response mapping before naming it.
- Do not infer a discriminator from naming. If `SomeBusinessException` constructs a different error code, use the constructed code and optionally name the exception class as evidence. If several branches emit the same code, keep distinct entries keyed by their exact guard conditions.
- Split distinct discriminators into distinct entries even when they share an HTTP status, exception class, message, endpoint, or step.
- Split separate field rules, date limits, calculation rules, approval prerequisites, lifecycle states, ownership conditions, and eligibility outcomes when source code distinguishes them.
- Split different source conditions even when they produce the same `Feilkode` or exception. Each independently triggerable guard condition is one failure entry.
- Do not merge them into umbrella descriptions such as `business validation fails`, `invalid state`, `required fields are incomplete`, `date rules are violated`, `approval prerequisites are missing`, or `same failures as another step`.
- Do not place multiple discriminators or independently triggerable conditions in one failure entry using commas, slashes, `and`, or `or`. A single source guard may retain its own compound boolean predicate, but separately triggerable guards must remain separate. Do not write phrases such as `each exact discriminator`, `named rule`, `readiness guards`, `same method failures`, or references to another step in place of concrete branches.
- The failure condition must match its discriminator's exact source guard. Do not copy a combined condition onto several split discriminators, and do not attach ownership, assignment, approval, subtype, or age conditions to the wrong error code.
- Different discriminators must not reuse identical `Failure condition` text unless the source really contains the same predicate at distinct throw sites. Treat repeated generic wording across different guards as a failed audit and rewrite each condition from its exact predicate.
- `Why this is a business failure` must explain the concrete domain meaning of that branch. Do not reuse tautologies such as `the exact domain guard prevents the transition`.
- `Violated state, rule, relationship, ownership, or eligibility condition` must name the actual invariant. Never write `named rule`, `corresponding rule`, `same rule`, or another placeholder.
- `Implementation evidence` must contain only exact source paths plus class/method or guard names relevant to that branch. Remove inherited fragments such as `controller`, `same method`, `strategies`, bare class names, stale prose, or concatenated evidence from unrelated branches.
- Do not copy a failure from one step to another. If the same source branch is reachable through two different workflow steps/functions, document it under each actually failing step with the relevant context.
- Do not treat an empty list, false decision result, or other valid negative response as a failure unless implementation or domain semantics reject the workflow because of it.
- If no retained business failure branch exists for a step, write exactly `None.`
- Write `None.` only after the complete call-graph ledger proves that the invocation has zero reachable business failures. Absence from tests, OpenAPI, or `full-behavior.md` is not proof.
- Do not create placeholder failures such as `none identified`, `not applicable`, or speculative failures unsupported by source.

## Final consistency audit

Before writing the final document, verify all of the following and correct every failure silently. This is an internal audit; do not output this checklist or a `Final consistency audit` section.
- Every behavior has exactly one row in `Behavior Index`, and every index link targets an explicit existing anchor.
- Behavior numbers are contiguous; index labels, anchors, and headings agree exactly.
- Every non-excluded function appears in `Function-to-Workflow Map`; every excluded function has a concise reason.
- Every required input has an explicit source, and no step consumes a value that an earlier endpoint did not actually return.
- Every enforced freshness token is obtained or refreshed through an API-visible response or a required read step.
- Every alternative path is complete, uniquely labeled, and reaches the same outcome with materially equivalent guarantees.
- Every required, alternative, and optional function invocation has exactly one matching failure subsection.
- Every failure entry contains one discriminator and one independently triggerable condition.
- Every failure is reachable for the behavior's exact subtype, actor, state, and path; unreachable function-wide branches are removed.
- For every invocation, source branch identities and documented branch identities match bidirectionally. Equal counts without identity matching are insufficient.
- Equivalent invocation signatures across behaviors have identical branch identities and counts. Every difference is justified by a visible source-backed actor, subtype, lifecycle, request-variant, or preceding-state distinction.
- Failure discovery varied business inputs and states beyond the happy-path example while preserving the invocation's subtype, actor intent, and domain operation.
- Generic authentication, role/token mismatch, pre-resource role admission, operational group gates, parsing, and infrastructure failures are absent from business failure sections.
- Synchronous, asynchronous, atomic, partial, best-effort, filtered, selected, bulk, and successful no-op variants are not merged when their guarantees or terminal outcomes differ.
- Every implementation-evidence field names an exact source path and class/method or guard.
- Every named error code or discriminator is verified through the throw/negative-result site, exception constructor and inheritance chain, wrapper/rethrow logic, and response mapping. No unused but similarly named enum value is presented as emitted behavior.
- Every enum literal exists in source, every request body matches its DTO shape, every nested required field is present, and every representative identifier satisfies source validation.
- Every syntactically required header, cookie, query, form, body, and path value is present, including values ignored by business logic.
- Every value described as coming from a prior read, response, generated identifier, or freshness result has an actual producing step and response location, or is explicitly declared as caller-known starting state.
- Every subtype, actor variant, initiator, and terminal outcome claimed by a behavior has a complete concrete workflow path; otherwise the claim is narrowed or split.
- No failure uses tautological explanations, placeholder invariants, copied conditions, generic evidence fragments, or a condition belonging to another discriminator.
- `Function-to-Workflow Map` behavior numbers and step roles are recomputed from the final workflow text and are not stale.

## Output requirements

- The entire output must be in English.
- Do not include Chinese.
- Do not mention this prompt.
- Do not execute the API.
- Do not produce test-analysis sections or a function-level behavior inventory.
- Keep the analysis grounded in available functions and implementation source.
- Use `Behavior` only for workflow-level business outcomes.
- Use `Step` for function-level operations inside a behavior.
- Cite an exact source file path and class/method or guard location for every retained failure. Do not use evidence labels such as `controller`, `same method`, `strategy`, or `service` without identifying the concrete source.
- Do not output `Lifecycle and State-Machine Map` or `Function Classification` sections. Perform that reasoning internally.

Use the following output structure.

# Workflow-Level Business Behavior Analysis

## Behavior Index

Place this table immediately after the document title, before the domain summary. Include one row for every supported behavior and no rows for excluded technical functions.

| Behavior | Brief description |
|---|---|
| [Behavior 1: {exact behavior name}](#behavior-1) | {One sentence naming the actor goal and terminal outcome.} |
| [Behavior 2: {exact behavior name}](#behavior-2) | {One sentence naming the actor goal and terminal outcome.} |

Use explicit, stable, lowercase HTML anchors. Do not rely only on renderer-generated heading anchors. The table label must exactly match the corresponding behavior heading. Number behaviors contiguously and place the matching anchor immediately before each behavior:

```markdown
<a id="behavior-1"></a>
### Behavior 1: {exact behavior name}
```

## Domain Summary

Describe the service domain, primary actors, aggregates, and externally meaningful outcomes.

## Supported Business Workflows

<a id="behavior-{N}"></a>
### Behavior {N}: {workflow-level business outcome}

Business goal:
Describe the complete actor goal, not an endpoint action.

Primary actor(s):
Identify the actors responsible for the workflow and any handoff between them.

Workflow boundary rationale:
Explain why these functions form one causally connected workflow and why the terminal state is an appropriate behavior boundary. If this is a one-step behavior, justify it using the one-step rule.

Starting state:
Use `No prior service state` when possible. Otherwise name the exact existing domain state, identify which ids/versions/tokens are already known versus still need to be obtained, and explain why the state cannot or should not be created by an earlier API step in this behavior.

Terminal business outcome:
Name the final domain state, artifact, decision, or operational result that completes the actor goal.

Required execution workflow:

#### Step 1: {domain meaning of the step}
- Use function `{exact function name from full-behavior.md}` (`{HTTP_METHOD} {path}`) with `{parameter}={value}` to ...
- Actor: ...
- State before: ...
- State transition or decision: ...
- Output/state after: ...
- API-visible outputs: {exact response body fields, headers, status, or `None`}
- Handoff to later step: {exact value and provenance, or `None`}

#### Step 2: {domain meaning of the step}
- Use function `{exact function name from full-behavior.md}` (`{HTTP_METHOD} {path}`) with values produced by Step 1 to ...
- Actor: ...
- State before: ...
- State transition or decision: ...
- Output/state after: ...
- API-visible outputs: {exact response body fields, headers, status, or `None`}
- Handoff to later step: {exact value and provenance, or `None`}

Continue until the terminal business outcome is reached.

Alternative valid workflow paths:
- Describe only complete, source-backed alternatives that reach the same terminal business outcome with materially equivalent guarantees.
- Use unique path and step labels such as `Alternative Path A` and `Alternative Step A1`.
- Express every alternative operation using the exact `Use function` format and the same actor/state/output/handoff fields as required steps.
- State the branch point, which required steps are replaced, and where the path rejoins or completes.
- Write `None.` when no alternative path exists.

Optional verification/supporting steps:
- List non-required reads, history views, PDFs, searches, or status checks using the exact `Use function` format.
- Label each listed operation as `Optional Step V{N}` so its business failure branches can be attached unambiguously.
- Explain what each verifies or supports.
- Write `None.` when no optional verification is useful.

Parameter, identity, and state bindings:
- State exactly which outputs and values pass from one step to the next.
- Include generated ids, business reference numbers, child ids, dates, versions, actor identity, ownership scope, and request/response fields.
- For every later input, name the producing step and exact response location. If no API response supplies it, identify the required retrieval step that does.
- Distinguish persisted state from values visible to the API caller.

Business result and side effects:
- Describe final persisted state and observable output.
- Include derived status, created/removed relationships, events, notifications, recalculations, exports, journal markers, and partial persistence.
- For asynchronous operations, distinguish endpoint acceptance from eventual processing. Do not claim eventual rows, messages, or publications already exist when the endpoint returns unless the implementation waits for completion.
- For best-effort, partial, ignored-item, or successful no-op behavior, state the exact persisted result and ensure it is consistent with the declared terminal outcome.

Constraints and invariants:
- List source-backed business rules spanning the workflow.
- Distinguish rules enforced at different steps.

Business failure branches:

#### Step 1 - `{exact function name}`

For each retained business failure, create a separate numbered block. Never combine independent conditions:

##### Failure 1
- Source discriminator: `{exact Feilkode, exception, guard branch, state rejection, lookup outcome, or domain-service result}`
- Failure condition: ...
- Why this is a business failure: ...
- Violated state, rule, relationship, ownership, or eligibility condition: ...
- Implementation evidence: `{exact source file path and class/method or guard}`

##### Failure 2
- Source discriminator: `{one discriminator only, even when it matches Failure 1}`
- Failure condition: `{one independently triggerable condition only}`
- Why this is a business failure: ...
- Violated state, rule, relationship, ownership, or eligibility condition: ...
- Implementation evidence: `{exact source file path and class/method or guard}`

Write `None.` when Step 1 has no concrete retained business failure branch.

Business failure coverage:
- Source-ledger business branches for this invocation: `{N}`
- Documented failure blocks for this invocation: `{N}`
- Coverage result: `Complete`

Use `Complete` only after branch-identity reconciliation in both directions and cross-workflow comparison of equivalent invocation signatures. Equal numbers alone are not sufficient. When there are no business failures, write `None.` and use `0` for both counts only after the master branch catalog proves that no retained branch is applicable. These counts include only retained business failures; excluded technical/access-gate failures and successful negative/no-op results are not counted.

#### Step 2 - `{exact function name}`

Repeat the same structure for every required, alternative, and optional verification/supporting step listed in the behavior. Do not omit setup, intermediate, terminal, or optional steps.

Use the exact invocation label in each heading:
- `#### Step 2 - {function}` for required steps
- `#### Alternative Step A1 - {function}` for alternative steps
- `#### Optional Step V1 - {function}` for optional steps

Every invocation listed earlier in the behavior must have exactly one matching failure subsection, even when the same function appears more than once. Do not merge failure subsections for repeated function invocations.

After each invocation's failure subsection, include its `Business failure coverage` count block. Do not defer counts to a behavior-level total because repeated invocations may have different reachable branches.

Implementation notes:
- Record source/OpenAPI discrepancies, non-obvious state derivation, transaction boundaries, or error responses that still persist state.

## Function-to-Workflow Map

Map all non-excluded functions to their usage:

| Function | Behavior(s) | Step or supporting role | Reuse explanation |
|---|---|---|---|

This map must expose omitted functions and accidental duplicate behaviors without turning functions into behaviors. Generate it from the final workflow text after all behavior edits: include every behavior in which the function is invoked, use the exact final required/alternative/optional step label, and describe synchronous/asynchronous, selected/bulk, dry-run/persistent, and terminal roles accurately. Do not reuse an earlier draft of the map.

List excluded functions after the table in one concise sentence with their exclusion reasons. Do not create a separate function-classification section.

## Cross-Workflow Observations

Summarize important system-wide findings such as:
- shared state-machine rules
- actor handoffs
- business invariants reused by multiple workflows
- transaction and partial-persistence behavior
- event-driven consistency
- implementation/OpenAPI discrepancies
- domain capabilities that cannot be completed through available function composition
