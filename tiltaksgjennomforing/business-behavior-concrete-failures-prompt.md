# Business Behavior Concrete Failure Revision Prompt

Use this prompt to revise the existing business behavior document without changing its behavior model or structure.

```text
You are an expert in software engineering, REST API analysis, domain modeling, source-code tracing, and reverse engineering.

You are given access to:
- the existing business behavior document, normally `business-behavior.md`
- `full-behavior.md`, which contains the exact function names and function-level analysis
- OpenAPI/Swagger definitions
- implementation source code
- tests and seed data

Your task is to produce a revised copy of the existing business behavior document in English.

This is a constrained failure-section revision. It is not a new behavior analysis and it is not a workflow redesign.

Do not execute the API.

## Primary objective

Keep the existing business behavior document unchanged except for the contents of every `Failure and exceptional cases:` section.

Replace each existing failure section with the complete set of concrete, implementation-backed business failure branches owned by that existing behavior.

First parse the supplied document's behavior inventory. Let `N` be the number of `### Behavior <number>:` headings in the input document. The revised document must still contain exactly the same `N` behaviors.

Do not hardcode a behavior count. The expected behavior count is always the count parsed from the supplied project document.

## Mandatory structure-preservation rule

Treat all text outside `Failure and exceptional cases:` sections as frozen.

Preserve exactly:
- the document title
- the behavior index
- all input behavior numbers, anchors, names, and ordering
- `Domain Summary`
- `Business goal`
- `API group boundary`
- `Domain context`
- `Starting point`
- `State transition summary`
- `Required execution workflow`
- `Optional verification workflow`
- `Existing-state shortcuts`
- `Parameter and value bindings`
- `Business result`
- `Constraints and invariants`
- `Implementation notes`
- all unsupported/missing behavior sections
- all cross-behavior observations
- the existing summary sections

Do not:
- add, remove, merge, split, rename, renumber, or reorder behaviors
- convert the document to a workflow-level behavior model
- create new composite behaviors
- expand atomic behaviors into end-to-end workflows
- collapse multiple existing behaviors into one
- alter required or optional workflow steps
- alter business goals, boundaries, state descriptions, results, constraints, or notes
- add a function classification, function-to-workflow map, failure summary, coverage section, denominator, or test analysis
- repair unrelated inaccuracies outside the failure sections

The existing behavior model is authoritative for this task, even when another behavior grouping might also be reasonable.

## Function ownership rule

Determine which exact function or functions own the failure inventory of each existing behavior.

- For an atomic behavior, analyze only the exact core function named or described by that behavior's existing `API group boundary`, business goal, and required execution step.
- For an existing composite behavior whose stated business goal inherently includes multiple core functions, analyze each of those existing core functions. Do not split the composite behavior or create separate behaviors.
- Do not add failures from optional verification functions.
- Do not add failures from a function mentioned only as a shortcut, lookup aid, implementation note, or unrelated prerequisite.
- Do not copy failures from another behavior merely because its function could be used to prepare state.
- If a required step exists only to establish state for another core action, include its failures only when that setup action is explicitly part of the existing behavior's stated business outcome.
- Preserve exact function names from `full-behavior.md`.

## Definition of a concrete business failure

A retained business failure must satisfy all of the following:

1. The request has passed ordinary request parsing, deserialization, generic authentication, and generic endpoint admission.
2. The owned function or its reachable domain call chain is actually entered.
3. A concrete source branch rejects, blocks, or exceptionally changes the operation because of domain data, domain state, lifecycle state, a business invariant, a persisted relationship, object-scoped ownership, or domain eligibility.
4. The branch is reachable for the behavior and function being analyzed.
5. The branch is supported by implementation evidence, not only by OpenAPI wording, test naming, or assumption.

Include concrete branches such as:
- domain lifecycle or state-machine rejections
- invalid transitions from the current persisted state
- business invariant violations
- measure-specific field, date, duration, amount, percentage, or calculation rules
- approval ordering and duplicate-approval rules
- duplicate or idempotency conflicts with business meaning
- missing domain aggregates or child resources when their absence prevents the requested business operation
- persisted ownership or object-scoped access failures for a concrete agreement, participant, employer, mentor, notification, subsidy period, or other domain object
- domain eligibility failures tied to a concrete participant, employer, measure, NAV unit, Altinn right, or persisted relationship
- business-significant negative results from domain/upstream services, such as unknown organization, protected participant, missing employer account, missing NAV unit, or missing domain right
- domain collection consistency failures, child-state conflicts, and finality rules
- error branches that still mutate or partially persist business state

## Failures to ignore completely

Do not include or mention any of the following in `Failure and exceptional cases:`:

- framework-level path, query, header, cookie, body, or form binding failures
- malformed JSON or deserialization failures
- invalid UUID, date, number, boolean, string-to-enum, `Fnr`, or `BedriftNr` conversion occurring before the owned function's business logic starts
- missing generic request headers or cookies
- generic authentication, token validation, or missing identity
- unsupported role-cookie/token-issuer combinations used only to establish caller context
- generic authorization before a concrete domain object or domain eligibility relation is evaluated
- endpoint access gates such as configured system-user, developer-admin, DVH group, delete-marker-admin, or operational-group checks
- infrastructure, database availability, network, timeout, serialization infrastructure, or transaction-system failures
- external dependency availability failures
- unexpected runtime exceptions with no specific business meaning

Ignore excluded failures even when:
- they produce a 4xx or 5xx response
- `full-behavior.md` lists them
- they are represented by `FeilkodeException`, `TilgangskontrollException`, or another application exception
- generated tests happen to exercise them

Business-scoped access is retained only when caller context already exists and the decision concerns a concrete domain object or concrete eligibility relationship.

An upstream result is retained only when the returned result has business meaning. Failure to contact the upstream service is excluded.

## Valid negative outcomes are not failures

Do not classify a valid result as a failure merely because it is empty, false, ignored, idempotent, or a no-op.

Examples:
- an empty list from a valid search
- a boolean decision result of `false`
- an unknown lookup value that the implementation intentionally converts to an empty successful result
- a missing selected bulk id that the implementation intentionally skips
- a dry-run that successfully performs no persistence

Include such a branch only when the implementation rejects the business operation or produces an explicitly exceptional business outcome.

## Required source-analysis method

For each behavior:

1. Identify its owned exact function or functions using the existing document and `full-behavior.md`.
2. Find the concrete controller/handler method for each owned function.
3. Trace all reachable service, aggregate, entity, validator, strategy, repository, event, and domain-client calls.
4. Enumerate every explicit guard, conditional rejection, thrown domain exception, exact `Feilkode`, repository not-found outcome, state-machine rejection, and business-significant negative service result.
5. Inspect subtype and strategy implementations selected by measure type, actor type, state, or child-resource type.
6. Inspect exception constructors and handlers to determine the discriminator actually emitted.
7. Determine whether each branch is reachable for this exact function and behavior.
8. Classify each branch as retained business failure or excluded technical/access/infrastructure failure.
9. Deduplicate retained failures within the behavior.
10. Replace only that behavior's failure section.

Do not stop at `full-behavior.md`. It may contain only representative failures or may include technical failures that must be excluded.

## Failure granularity rule

Include every distinct source-level business branch.

Use the most precise discriminator available:
- exact `Feilkode` value
- exact exception class when no more precise business code exists
- guard method plus branch condition
- named validation method outcome
- state-machine transition rejection
- repository lookup outcome
- domain-service negative result

Treat each distinct discriminator and concrete condition as a separate failure entry, even when multiple failures:
- occur in the same function
- return the same HTTP status
- use the same exception class
- share similar text
- violate the same broad category of rule

Do not use umbrella entries such as:
- `business validation fails`
- `invalid state`
- `required fields are incomplete`
- `date limits are violated`
- `calculation inputs are invalid`
- `approval prerequisites are missing`
- `same failures as another behavior`
- `any domain rule fails`

Split source-distinct rules such as:
- each individual required field or named validation outcome
- start-after-end, too-early start, upper end-date bound, and each measure-specific duration limit
- each approval-order, duplicate-approval, and missing-signature rule
- each amount, percentage, rate, or calculation restriction
- each child status or refund/finality restriction
- each ownership, eligibility, or domain-resource relationship

## Deduplication rule

Within one behavior, count a retained failure once by:

`{exact failing function, exact source discriminator, concrete failure condition}`

- Do not repeat the same branch because multiple source methods propagate it without changing its meaning.
- Do keep separate branches when the same discriminator represents materially different source conditions.
- Do keep separate entries when different owned functions in an existing composite behavior encounter the condition independently.
- Do not deduplicate by HTTP status or exception class alone.

## Required failure-section format

Keep the existing heading exactly:

`Failure and exceptional cases:`

For each retained business failure, output:

- Failing function: `{exact function name from full-behavior.md}`
  - Source discriminator: `{exact Feilkode, exception class, guard branch, named validation outcome, repository outcome, or domain-service result}`
  - Failure condition: {one concrete source-backed condition}
  - Why it fails: {precise implementation-backed explanation}
  - Violated prerequisite or constraint: {specific lifecycle state, invariant, relationship, ownership, or eligibility rule}
  - Implementation evidence: `{source file and class/method}`

If the branch returns an error while mutating or partially persisting state, add:

  - Persisted outcome despite failure: {exact state change or side effect}

If no retained concrete business failure exists after applying all exclusions, write exactly:

`None.`

Do not create placeholder entries such as `none identified`, `not applicable`, or `no known failure`.

## Output validation

Before returning the revised document, verify all of the following:

- exactly `N` `### Behavior <number>:` headings remain, where `N` is parsed from the input document
- behavior numbers, names, anchors, and order match the input
- exactly `N` `Failure and exceptional cases:` headings remain
- no text outside failure sections changed
- no behavior was added, removed, merged, split, or converted into a workflow-level behavior
- every retained failure has an exact failing function, source discriminator, concrete condition, explanation, violated rule, and implementation evidence
- every source-distinct business branch is represented
- no framework binding, generic auth, endpoint gate, infrastructure, or external-availability failure remains
- no valid empty/false/no-op result is mislabeled as a failure
- behaviors with no retained failures contain exactly `None.`

Return only the complete revised business behavior document. Do not include a preface, changelog, analysis notes, failure counts, coverage metrics, or explanation outside the document.
```
