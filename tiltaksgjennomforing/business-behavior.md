# Domain-Level Workflow and State Behavior Analysis

| No. | Behavior name | Business goal |
|---:|---|---|
| 1 | [Behavior 1: Role-scoped agreement listing](#behavior-1) | Returns a paginated list of agreements visible to the logged-in party, filtered by the query fields. |
| 2 | [Behavior 2: Advisor-created agreement](#behavior-2) | Creates a new agreement for a participant and employer as a NAV advisor and returns a `Location` pointing to `/avtaler/{avtaleId}`. |
| 3 | [Behavior 3: Arena cleanup agreement creation](#behavior-3) | Creates a new advisor agreement and also marks it as an Arena cleanup agreement. |
| 4 | [Behavior 4: Employer-created agreement](#behavior-4) | Creates a new agreement as an employer and returns a `Location` for `/avtaler/{avtaleId}`. |
| 5 | [Behavior 5: Advisor-created mentor agreement](#behavior-5) | Creates a mentor agreement where the advisor is the creator. |
| 6 | [Behavior 6: Employer-created mentor agreement](#behavior-6) | Creates a mentor agreement where the employer is the creator. |
| 7 | [Behavior 7: Check participant overlap](#behavior-7) | Returns existing agreements that overlap the participant, measure type, and optional period. |
| 8 | [Behavior 8: Saved agreement search registration and replay](#behavior-8) | Persist a search filter and replay it later through its generated search id. |
| 9 | [Behavior 9: Agreement detail retrieval by id](#behavior-9) | Retrieves the agreement identified by `{avtaleId}` for the selected logged-in party. |
| 10 | [Behavior 10: Agreement detail retrieval by agreement number](#behavior-10) | Retrieves an agreement by its generated agreement number. |
| 11 | [Behavior 11: Agreement version history retrieval](#behavior-11) | Returns all stored content versions for an agreement. |
| 12 | [Behavior 12: Draft agreement update](#behavior-12) | Updates mutable agreement content and, for subsidy-backed measures, recalculates subsidy periods. |
| 13 | [Behavior 13: Draft agreement update dry-run](#behavior-13) | Validates and returns the would-be updated agreement without saving it. |
| 14 | [Behavior 14: Agreement sharing with a party](#behavior-14) | Registers a share event for the selected agreement party and creates corresponding notifications. |
| 15 | [Behavior 15: Participant agreement approval](#behavior-15) | Records participant approval on a fully filled agreement. |
| 16 | [Behavior 16: Employer agreement approval](#behavior-16) | Records employer approval on a fully filled agreement. |
| 17 | [Behavior 17: Mentor confidentiality signing](#behavior-17) | Records that the mentor has signed the confidentiality declaration. |
| 18 | [Behavior 18: Advisor final approval](#behavior-18) | Records advisor approval after participant and employer have approved. For measures not requiring decision-maker approval, this also makes the agreement entered. |
| 19 | [Behavior 19: Advisor approval on behalf of participant](#behavior-19) | Advisor records both advisor approval and participant approval on behalf of the participant. |
| 20 | [Behavior 20: Advisor approval on behalf of employer](#behavior-20) | Advisor records both advisor approval and employer approval on behalf of the employer. |
| 21 | [Behavior 21: Advisor approval on behalf of participant and employer](#behavior-21) | Advisor records advisor, participant, and employer approvals in one operation. |
| 22 | [Behavior 22: Approval revocation](#behavior-22) | Clears existing participant, employer, and advisor approvals on an agreement that has not yet been entered. |
| 23 | [Behavior 23: After-registration eligibility marking](#behavior-23) | Toggles an unentered agreement so it is approved for after-registration. |
| 24 | [Behavior 24: After-registration eligibility removal](#behavior-24) | Toggles an unentered agreement so it is no longer approved for after-registration. |
| 25 | [Behavior 25: Subsidy period approval](#behavior-25) | Decision-maker approves the current subsidy period; for the first decision-maker-controlled period, this also enters the agreement. |
| 26 | [Behavior 26: Subsidy period rejection](#behavior-26) | Decision-maker rejects the current subsidy period with rejection causes and explanation. |
| 27 | [Behavior 27: Rejected subsidy period return](#behavior-27) | Deactivates active rejected subsidy periods and creates new unhandled periods for correction. |
| 28 | [Behavior 28: Agreement shortening](#behavior-28) | Creates a new approved version with an earlier end date and adjusts subsidy periods. |
| 29 | [Behavior 29: Dry-run agreement shortening](#behavior-29) | Returns the would-be shortened agreement without saving it. |
| 30 | [Behavior 30: Agreement extension](#behavior-30) | Creates a new approved version with a later end date and adds or recalculates subsidy periods. |
| 31 | [Behavior 31: Dry-run agreement extension](#behavior-31) | Returns the would-be extended agreement without saving it. |
| 32 | [Behavior 32: Subsidy calculation change](#behavior-32) | Creates a new approved version with changed monthly salary, holiday pay rate, employer tax, and pension rate, then recalculates unhandled subsidy amounts. |
| 33 | [Behavior 33: Dry-run subsidy calculation change](#behavior-33) | Returns the would-be updated agreement after subsidy calculation changes without saving. |
| 34 | [Behavior 34: Contact information change](#behavior-34) | Creates a new approved version with changed participant, advisor, employer, and refund contact information. |
| 35 | [Behavior 35: Job description change](#behavior-35) | Creates a new approved version with changed title, work tasks, occupation code, position percent, and days per week. |
| 36 | [Behavior 36: Follow-up and adaptation text change](#behavior-36) | Creates a new approved version with changed follow-up and adaptation descriptions. |
| 37 | [Behavior 37: Work-training goal replacement](#behavior-37) | Replaces goals on an approved work-training agreement. |
| 38 | [Behavior 38: Inclusion subsidy expense replacement](#behavior-38) | Replaces inclusion subsidy expense lines on an approved inclusion subsidy agreement. |
| 39 | [Behavior 39: Mentor details change](#behavior-39) | Creates a new approved version with changed mentor name, phone, tasks, hours, and hourly wage. |
| 40 | [Behavior 40: Cost center change](#behavior-40) | Sets the cost-center unit and unit name on unhandled or rejected subsidy periods. |
| 41 | [Behavior 41: Arena migration date adjustment](#behavior-41) | Recomputes subsidy periods around a migration date and saves or updates the agreement’s Arena cleanup marker. |
| 42 | [Behavior 42: Dry-run Arena migration date adjustment](#behavior-42) | Returns the would-be agreement after recalculating periods around the migration date without saving it. |
| 43 | [Behavior 43: Employer account number lookup](#behavior-43) | Returns the employer’s bank account number for the agreement’s company. |
| 44 | [Behavior 44: Agreement PDF download](#behavior-44) | Returns a PDF representation of an advisor-approved agreement. |
| 45 | [Behavior 45: Salesforce dialog visibility check](#behavior-45) | Returns `true` when the agreement belongs to a configured Salesforce office, has `MIDLERTIDIG_LONNSTILSKUDD`, and is `GJENNOMFØRES` or `AVSLUTTET`; otherwise returns `false`. |
| 46 | [Behavior 46: Follow-up unit refresh](#behavior-46) | Refreshes participant data, follow-up status, geographic unit, and follow-up unit name, then saves the agreement. |
| 47 | [Behavior 47: Advisor takeover of agreement](#behavior-47) | Assigns the logged-in advisor as the agreement’s advisor and refreshes follow-up data. |
| 48 | [Behavior 48: Agreement annulment](#behavior-48) | Annuls an agreement and annuls/removes eligible subsidy periods; if reason is `Feilregistrering`, the agreement is also marked as incorrectly registered. |
| 49 | [Behavior 49: Agreement soft deletion](#behavior-49) | Marks the agreement as deleted/hidden. |
| 50 | [Behavior 50: Employer Min Side agreement listing](#behavior-50) | Returns all agreements for a company that the logged-in employer can view. |
| 51 | [Behavior 51: Decision-maker work queue listing](#behavior-51) | Returns paginated agreements whose subsidy periods match decision-maker filters and the decision-maker’s NAV units. |
| 52 | [Behavior 52: Logged-in user context lookup](#behavior-52) | Returns role-specific information for the logged-in user. |
| 53 | [Behavior 53: Employer organization lookup](#behavior-53) | Returns organization data for an employer unit. |
| 54 | [Behavior 54: Altinn rights request URL lookup](#behavior-54) | Returns URLs that let an employer request Altinn rights for each supported measure type. |
| 55 | [Behavior 55: Combined code-list lookup](#behavior-55) | Returns both measure types and agreement statuses. |
| 56 | [Behavior 56: Agreement status code-list lookup](#behavior-56) | Returns all `Status` enum names and descriptions. |
| 57 | [Behavior 57: Measure type code-list lookup](#behavior-57) | Returns all `Tiltakstype` enum names, descriptions, treatment themes, and optional Arena codes. |
| 58 | [Behavior 58: Feature toggle evaluation](#behavior-58) | Returns enabled/disabled values for requested feature names. |
| 59 | [Behavior 59: Feature variant lookup](#behavior-59) | Returns Unleash variant objects for requested feature names. |
| 60 | [Behavior 60: Internal health probe](#behavior-60) | Returns `ok` if the database query succeeds. |
| 61 | [Behavior 61: Overview notification listing](#behavior-61) | Returns unread bell notifications for the logged-in party’s identifiers. |
| 62 | [Behavior 62: Agreement modal notification listing](#behavior-62) | Returns unread bell notifications for a specific agreement and logged-in party. |
| 63 | [Behavior 63: Agreement notification log listing](#behavior-63) | Returns all notifications for a specific agreement and receiver role. |
| 64 | [Behavior 64: Single notification read marking](#behavior-64) | Mark one notification as read for the logged-in party. |
| 65 | [Behavior 65: Bulk notification read marking](#behavior-65) | Mark several caller-owned notifications as read in one request. |
| 66 | [Behavior 66: Journal export and completion marking](#behavior-66) | Export unjournaled agreement versions and mark the consumed versions as journaled. |
| 67 | [Behavior 67: Selected agreement wage-subsidy recalculation](#behavior-67) | Recalculates missing wage subsidy totals for each selected agreement. |
| 68 | [Behavior 68: Missing reduced-percent date repair](#behavior-68) | Finds entered permanent wage-subsidy agreements missing reduced-percent data and recalculates reduced values and migrated periods. |
| 69 | [Behavior 69: Dry-run missing reduced-percent date fix](#behavior-69) | Counts/logs permanent wage-subsidy agreements that would be repaired, without saving changes. |
| 70 | [Behavior 70: Admin subsidy-period generation for one agreement](#behavior-70) | Generates subsidy periods for one agreement after an Arena migration date. |
| 71 | [Behavior 71: Unhandled subsidy-period recalculation](#behavior-71) | Removes unhandled periods and recreates them from the first unhandled point through agreement end. |
| 72 | [Behavior 72: Subsidy-period date-order diagnostic](#behavior-72) | Scans temporary wage-subsidy agreements and logs periods whose start date is earlier than the previous sequence number’s start date. |
| 73 | [Behavior 73: Subsidy-period annulment](#behavior-73) | Marks a subsidy period as annulled and emits an annulment event unless its refund status is expired. |
| 74 | [Behavior 74: Annul and resend approved subsidy period](#behavior-74) | Annuls an existing subsidy period and creates a replacement period with approved status, reusing approval metadata. |
| 75 | [Behavior 75: Annul and generate unhandled subsidy period](#behavior-75) | Annuls an existing subsidy period and creates a replacement with unhandled status. |
| 76 | [Behavior 76: Annul and generate Arena-treated periods](#behavior-76) | Annuls all subsidy periods on an agreement ending before `{dato}` and creates replacements with `BEHANDLET_I_ARENA` status. |
| 77 | [Behavior 77: Selected data warehouse patching](#behavior-77) | Creates DVH patch message entities for selected agreement ids found in the repository. |
| 78 | [Behavior 78: All-agreement data warehouse patching](#behavior-78) | Creates DVH patch messages for all agreements. |
| 79 | [Behavior 79: Single agreement event publication](#behavior-79) | Sends an agreement event message for one existing agreement. |
| 80 | [Behavior 80: All-agreement event publication](#behavior-80) | Sends agreement event messages for all agreements. |
| 81 | [Behavior 81: All-agreement event publication dry-run](#behavior-81) | Performs the all-agreement event-message operation in dry-run mode. |

## Domain Summary

The service manages Norwegian labor-market measure agreements (`Avtale`) between NAV advisors, participants, employers, mentors, and decision-makers. The main aggregate root is `avtaleId`; generated `avtaleNr` is a secondary lookup key. The primary lifecycle resources are current and historical agreement content (`AvtaleInnhold`), approval timestamps, after-registration flag state, subsidy periods (`TilskuddPeriode`), notification rows (`Varsel`), saved searches (`FilterSok`), journal state (`journalpostId` on agreement versions), data warehouse patch messages, and agreement event messages.

The major state machines are agreement creation and filling, party approval, advisor final approval, decision-maker subsidy-period approval or rejection, post-approval versioning, duration changes, annulment and soft deletion, notification read state, journal export/completion, and operational repair/publication jobs. The implementation derives visible agreement status from several fields rather than storing a single authoritative status column, so transitions often mutate timestamps, flags, child-period rows, and domain events together.

## Supported Business Behaviors

<a id="behavior-1"></a>
### Behavior 1: Role-scoped agreement listing

Business goal:
Returns a paginated list of agreements visible to the logged-in party, filtered by the query fields.

API group boundary:
Atomic read/export/lookup behavior. The single function `list accessible agreements` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: the role cookie and token issuer are not a supported combination

Required execution workflow:
1. Use function `list accessible agreements` (`GET /avtaler`) with query/path values documented by the endpoint; cookie `innlogget-part` when required to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns a paginated list of agreements visible to the logged-in party, filtered by the query fields.

Constraints and invariants:
- `innlogget-part` selects the role. Query fields from `AvtalePredicate` constrain the repository search. `page` and `size` are normalized with `Math.abs`.

Failure and exceptional cases:
- Failing function: `list accessible agreements`
  - Failure condition: the role cookie and token issuer are not a supported combination
  - Why it fails: `InnloggingService.hentAvtalepart` rejects invalid issuer-role combinations.
  - Violated prerequisite or constraint: Use an unsupported `innlogget-part` for the authenticated token.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-2"></a>
### Behavior 2: Advisor-created agreement

Business goal:
Creates a new agreement for a participant and employer as a NAV advisor and returns a `Location` pointing to `/avtaler/{avtaleId}`.

API group boundary:
Atomic creation behavior. The single function `create advisor agreement` creates the aggregate or lifecycle resource.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
No prior service state

State transition summary:
- State before: No service aggregate exists for the requested business object.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation validates caller context and required body values, then saves a new aggregate and emits creation side effects.
- State after: A new aggregate or marker state is persisted.
- Invalid or blocked transitions: advisor lacks write access to the participant

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with body `OpprettAvtale` with `deltakerFnr`, `bedriftNr`, `tiltakstype`; query `ryddeavtale=false`; authenticated advisor caller to create the domain object and capture generated ids from the response.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup step is needed; tests may create equivalent rows directly, but generated ids, ownership, and current content must be internally consistent.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Creates a new agreement for a participant and employer as a NAV advisor and returns a `Location` pointing to `/avtaler/{avtaleId}`.

Constraints and invariants:
- Body must contain `deltakerFnr`, `bedriftNr`, and `tiltakstype`. The logged-in advisor must have write access to the participant. The participant cannot be under 16; `SOMMERJOBB` also rejects participants over the age limit.

Failure and exceptional cases:
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to the participant
  - Why it fails: `Veileder.sjekkTilgangskontroll` throws access failure before saving.
  - Violated prerequisite or constraint: Use a `deltakerFnr` the advisor cannot access.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-3"></a>
### Behavior 3: Arena cleanup agreement creation

Business goal:
Creates a new advisor agreement and also marks it as an Arena cleanup agreement.

API group boundary:
Atomic creation behavior. The single function `create Arena cleanup agreement` creates the aggregate or lifecycle resource.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
No prior service state

State transition summary:
- State before: No service aggregate exists for the requested business object.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation validates caller context and required body values, then saves a new aggregate and emits creation side effects.
- State after: A new aggregate or marker state is persisted.
- Invalid or blocked transitions: agreement creation itself is invalid

Required execution workflow:
1. Use function `create Arena cleanup agreement` (`POST /avtaler`) with body `OpprettAvtale` with `deltakerFnr`, `bedriftNr`, `tiltakstype`; query `ryddeavtale=true`; authenticated advisor caller to create the domain object and capture generated ids from the response.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup step is needed; tests may create equivalent rows directly, but generated ids, ownership, and current content must be internally consistent.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Creates a new advisor agreement and also marks it as an Arena cleanup agreement.

Constraints and invariants:
- Same creation constraints as Function 2. Query parameter `ryddeavtale=true` must be supplied; the created `{avtaleId}` is reused internally to save an `ArenaRyddeAvtale`.

Failure and exceptional cases:
- Failing function: `create Arena cleanup agreement`
  - Failure condition: agreement creation itself is invalid
  - Why it fails: The cleanup marker is only saved after a valid agreement is created.
  - Violated prerequisite or constraint: Omit required body fields or use a participant the advisor cannot access.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-4"></a>
### Behavior 4: Employer-created agreement

Business goal:
Creates a new agreement as an employer and returns a `Location` for `/avtaler/{avtaleId}`.

API group boundary:
Atomic creation behavior. The single function `create employer agreement` creates the aggregate or lifecycle resource.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
No prior service state

State transition summary:
- State before: No service aggregate exists for the requested business object.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation validates caller context and required body values, then saves a new aggregate and emits creation side effects.
- State after: A new aggregate or marker state is persisted.
- Invalid or blocked transitions: employer lacks Altinn access for `bedriftNr` and `tiltakstype`

Required execution workflow:
1. Use function `create employer agreement` (`POST /avtaler/opprett-som-arbeidsgiver`) with body `OpprettAvtale` with `deltakerFnr`, `bedriftNr`, `tiltakstype`; authenticated employer caller with Altinn rights to create the domain object and capture generated ids from the response.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup step is needed; tests may create equivalent rows directly, but generated ids, ownership, and current content must be internally consistent.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Creates a new agreement as an employer and returns a `Location` for `/avtaler/{avtaleId}`.

Constraints and invariants:
- Body must contain `deltakerFnr`, `bedriftNr`, and `tiltakstype`. The employer must have Altinn access for the selected company and measure type.

Failure and exceptional cases:
- Failing function: `create employer agreement`
  - Failure condition: employer lacks Altinn access for `bedriftNr` and `tiltakstype`
  - Why it fails: `Arbeidsgiver.tilgangTilBedriftVedOpprettelseAvAvtale` rejects the creation.
  - Violated prerequisite or constraint: Use a company or measure not present in the employer’s Altinn rights.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-5"></a>
### Behavior 5: Advisor-created mentor agreement

Business goal:
Creates a mentor agreement where the advisor is the creator.

API group boundary:
Atomic creation behavior. The single function `create mentor agreement as advisor` creates the aggregate or lifecycle resource.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
No prior service state

State transition summary:
- State before: No service aggregate exists for the requested business object.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation validates caller context and required body values, then saves a new aggregate and emits creation side effects.
- State after: A new aggregate or marker state is persisted.
- Invalid or blocked transitions: participant and mentor have the same national identity number

Required execution workflow:
1. Use function `create mentor agreement as advisor` (`POST /avtaler/opprett-mentor-avtale`) with body `OpprettMentorAvtale` with `deltakerFnr`, `mentorFnr`, `bedriftNr`, `tiltakstype=MENTOR`, `avtalerolle=VEILEDER`; authenticated advisor caller to create the domain object and capture generated ids from the response.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup step is needed; tests may create equivalent rows directly, but generated ids, ownership, and current content must be internally consistent.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Creates a mentor agreement where the advisor is the creator.

Constraints and invariants:
- Body must include `deltakerFnr`, `mentorFnr`, `bedriftNr`, `tiltakstype`, and `avtalerolle=VEILEDER`. For the intended mentor workflow, `tiltakstype` should be `MENTOR`. `deltakerFnr` and `mentorFnr` must differ.

Failure and exceptional cases:
- Failing function: `create mentor agreement as advisor`
  - Failure condition: participant and mentor have the same national identity number
  - Why it fails: The controller explicitly rejects equal `deltakerFnr` and `mentorFnr`.
  - Violated prerequisite or constraint: Set `deltakerFnr` equal to `mentorFnr`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-6"></a>
### Behavior 6: Employer-created mentor agreement

Business goal:
Creates a mentor agreement where the employer is the creator.

API group boundary:
Atomic creation behavior. The single function `create mentor agreement as employer` creates the aggregate or lifecycle resource.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
No prior service state

State transition summary:
- State before: No service aggregate exists for the requested business object.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation validates caller context and required body values, then saves a new aggregate and emits creation side effects.
- State after: A new aggregate or marker state is persisted.
- Invalid or blocked transitions: `avtalerolle` is neither `VEILEDER` nor `ARBEIDSGIVER`

Required execution workflow:
1. Use function `create mentor agreement as employer` (`POST /avtaler/opprett-mentor-avtale`) with body `OpprettMentorAvtale` with `deltakerFnr`, `mentorFnr`, `bedriftNr`, `tiltakstype=MENTOR`, `avtalerolle=ARBEIDSGIVER`; authenticated employer caller with Altinn rights to create the domain object and capture generated ids from the response.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup step is needed; tests may create equivalent rows directly, but generated ids, ownership, and current content must be internally consistent.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Creates a mentor agreement where the employer is the creator.

Constraints and invariants:
- Body must include `avtalerolle=ARBEIDSGIVER`. The employer must have Altinn access for `bedriftNr` and `tiltakstype`. `deltakerFnr` and `mentorFnr` must differ.

Failure and exceptional cases:
- Failing function: `create mentor agreement as employer`
  - Failure condition: `avtalerolle` is neither `VEILEDER` nor `ARBEIDSGIVER`
  - Why it fails: The implementation never creates an agreement and throws `Opprett Mentor fant ingen avtale å behandle`.
  - Violated prerequisite or constraint: Use `avtalerolle=DELTAKER`, `MENTOR`, or `BESLUTTER`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-7"></a>
### Behavior 7: Check participant overlap

Business goal:
Returns existing agreements that overlap the participant, measure type, and optional period.

API group boundary:
Atomic read/export/lookup behavior. The single function `check participant overlap` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: `avtaleId` is supplied but is not a UUID

Required execution workflow:
1. Use function `check participant overlap` (`GET /avtaler/deltaker-allerede-paa-tiltak`) with query `deltakerFnr`, `tiltakstype`, optional `startDato`, `sluttDato`, `avtaleId`; advisor caller to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns existing agreements that overlap the participant, measure type, and optional period.

Constraints and invariants:
- Requires `deltakerFnr` and `tiltakstype`. If `avtaleId`, `startDato`, and `sluttDato` are all provided, the check excludes that agreement and uses the full date interval; otherwise it checks from `startDato` or today.

Failure and exceptional cases:
- Failing function: `check participant overlap`
  - Failure condition: `avtaleId` is supplied but is not a UUID
  - Why it fails: The controller calls `UUID.fromString(avtaleId)`.
  - Violated prerequisite or constraint: Use a non-UUID `avtaleId`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-8"></a>
### Behavior 8: Saved agreement search registration and replay

Business goal:
Persist a search filter and replay it later through its generated search id.

API group boundary:
Composite workflow with explicit response-to-request value binding across the same lifecycle resource.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: `sokId` is unknown

Required execution workflow:
1. Use function `search agreements and save search` (`POST /avtaler/sok`) with body `AvtalePredicate`; cookie `innlogget-part`; optional `sorteringskolonne`, `page`, `size` to save the search filter and capture `sokId`.
2. Use function `replay saved agreement search` (`GET /avtaler/sok`) with query `sokId` returned by `search agreements and save search`; same cookie `innlogget-part`; optional paging and sorting to replay the saved filter through the captured `sokId`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If the response value consumed by the later step is already known and still belongs to the same scope, the discovery step can be skipped; direct database setup must preserve the same ownership and lifecycle state.

Parameter and value bindings:
- `sokId` returned by the save-search step is consumed by the replay step.

Business result:
Looks up a saved search by `sokId`, reruns it, increments usage, and returns the same response shape as search.

Constraints and invariants:
- `POST /avtaler/sok` produces `sokId`; `GET /avtaler/sok` consumes that exact `sokId`. The same role constraints apply to the result filtering.

Failure and exceptional cases:
- Failing function: `search agreements and save search`
  - Failure condition: unsupported role/token combination
  - Why it fails: The search is role-scoped through `InnloggingService.hentAvtalepart`.
  - Violated prerequisite or constraint: Use an `innlogget-part` role not valid for the authenticated issuer.
- Failing function: `replay saved agreement search`
  - Failure condition: `sokId` is unknown
  - Why it fails: It does not throw; it returns an empty result with `sokId=""`, which is an exceptional successful branch.
  - Violated prerequisite or constraint: Omit the prior `POST /avtaler/sok` or use an unrelated `sokId`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-9"></a>
### Behavior 9: Agreement detail retrieval by id

Business goal:
Retrieves the agreement identified by `{avtaleId}` for the selected logged-in party.

API group boundary:
Atomic read/export/lookup behavior. The single function `retrieve agreement by id` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: agreement does not exist

Required execution workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Retrieves the agreement identified by `{avtaleId}` for the selected logged-in party.

Constraints and invariants:
- `POST /avtaler` returns `Location: /avtaler/{avtaleId}`; `GET /avtaler/{avtaleId}` consumes that `{avtaleId}`. The role in `innlogget-part` must have access to the agreement.

Failure and exceptional cases:
- Failing function: `retrieve agreement by id`
  - Failure condition: agreement does not exist
  - Why it fails: `Avtalepart.hentAvtale` throws when the repository cannot find the id.
  - Violated prerequisite or constraint: Omit any agreement creation endpoint and use an unknown `{avtaleId}`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-10"></a>
### Behavior 10: Agreement detail retrieval by agreement number

Business goal:
Retrieves an agreement by its generated agreement number.

API group boundary:
Atomic read/export/lookup behavior. The single function `retrieve agreement by agreement number` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: `avtaleNr` is unknown

Required execution workflow:
1. Use function `retrieve agreement by agreement number` (`GET /avtaler/avtaleNr/{avtaleNr}`) with query/path values documented by the endpoint; cookie `innlogget-part` when required to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Retrieves an agreement by its generated agreement number.

Constraints and invariants:
- `POST /avtaler` produces `{avtaleId}` in `Location`. `GET /avtaler/{avtaleId}` returns the agreement’s `avtaleNr`. `GET /avtaler/avtaleNr/{avtaleNr}` consumes that exact `{avtaleNr}`.

Failure and exceptional cases:
- Failing function: `retrieve agreement by agreement number`
  - Failure condition: `avtaleNr` is unknown
  - Why it fails: Repository lookup by `avtaleNr` throws resource-not-found.
  - Violated prerequisite or constraint: Use an `avtaleNr` not obtained from a created agreement.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-11"></a>
### Behavior 11: Agreement version history retrieval

Business goal:
Returns all stored content versions for an agreement.

API group boundary:
Atomic read/export/lookup behavior. The single function `list agreement versions` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: agreement is missing or inaccessible

Required execution workflow:
1. Use function `list agreement versions` (`GET /avtaler/{avtaleId}/versjoner`) with path `avtaleId`; authenticated caller with required role and access to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Returns all stored content versions for an agreement.

Constraints and invariants:
- `POST /avtaler` produces `{avtaleId}`; `GET /avtaler/{avtaleId}/versjoner` consumes it. The logged-in party must have access.

Failure and exceptional cases:
- Failing function: `list agreement versions`
  - Failure condition: agreement is missing or inaccessible
  - Why it fails: The implementation first loads the agreement and checks access.
  - Violated prerequisite or constraint: Use an unknown `{avtaleId}` or a role without access.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-12"></a>
### Behavior 12: Draft agreement update

Business goal:
Updates mutable agreement content and, for subsidy-backed measures, recalculates subsidy periods.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `update agreement` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: stale or missing concurrency timestamp

Required execution workflow:
1. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with path `avtaleId`; header `If-Unmodified-Since` from current `sistEndret`; body `EndreAvtale`; cookie `innlogget-part` with edit rights to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Updates mutable agreement content and, for subsidy-backed measures, recalculates subsidy periods.

Constraints and invariants:
- `POST /avtaler` produces `{avtaleId}`. `GET /avtaler/{avtaleId}` returns current `sistEndret`; `PUT /avtaler/{avtaleId}` sends `If-Unmodified-Since` not earlier than that value. Body is `EndreAvtale`. Existing approvals must be absent or revoked before editing.

Failure and exceptional cases:
- Failing function: `update agreement`
  - Failure condition: stale or missing concurrency timestamp
  - Why it fails: `Avtale.sjekkSistEndret` rejects a timestamp before the current `sistEndret`.
  - Violated prerequisite or constraint: Send an old `If-Unmodified-Since` value.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-13"></a>
### Behavior 13: Draft agreement update dry-run

Business goal:
Validates and returns the would-be updated agreement without saving it.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `dry-run agreement update` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target resource exists in the state required by the simulated transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation applies validation or calculation in memory or logs, then avoids repository save.
- State after: No persisted state changes; only a preview or diagnostic is produced.
- Invalid or blocked transitions: agreement has already been approved

Required execution workflow:
1. Use function `dry-run agreement update` (`PUT /avtaler/{avtaleId}/dry-run`) with path `avtaleId`; header `If-Unmodified-Since`; body `EndreAvtale`; cookie `innlogget-part` with edit rights to validate and preview the transition without saving it.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Validates and returns the would-be updated agreement without saving it.

Constraints and invariants:
- Same body and `If-Unmodified-Since` constraints as `PUT /avtaler/{avtaleId}`. The returned object is not persisted.

Failure and exceptional cases:
- Failing function: `dry-run agreement update`
  - Failure condition: agreement has already been approved
  - Why it fails: Agreements with approvals cannot be edited until approvals are revoked.
  - Violated prerequisite or constraint: Attempt dry-run editing after an approval exists.

Implementation notes:
This is a non-persistent simulation; it may validate and recalculate but should not save state.

<a id="behavior-14"></a>
### Behavior 14: Agreement sharing with a party

Business goal:
Registers a share event for the selected agreement party and creates corresponding notifications.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `share agreement with party` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: phone number for the selected party is missing or invalid

Required execution workflow:
1. Use function `share agreement with party` (`POST /avtaler/{avtaleId}/del-med-avtalepart`) with path `avtaleId`; body `Avtalerolle` for the receiver; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Registers a share event for the selected agreement party and creates corresponding notifications.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/del-med-avtalepart` body is an `Avtalerolle` such as `DELTAKER`, `ARBEIDSGIVER`, or `MENTOR`. The relevant phone number on the current agreement content must be a valid mobile number.

Failure and exceptional cases:
- Failing function: `share agreement with party`
  - Failure condition: phone number for the selected party is missing or invalid
  - Why it fails: `Avtale.delMedAvtalepart` validates the party’s phone number before registering the event.
  - Violated prerequisite or constraint: Omit the content update that supplies a valid phone number.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-15"></a>
### Behavior 15: Participant agreement approval

Business goal:
Records participant approval on a fully filled agreement.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `approve agreement as participant` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: required agreement fields are incomplete

Required execution workflow:
1. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with path `avtaleId`; header `If-Unmodified-Since`; cookie `innlogget-part=DELTAKER` to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Records participant approval on a fully filled agreement.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/godkjenn` uses `innlogget-part=DELTAKER` and `If-Unmodified-Since` from `GET /avtaler/{avtaleId}`. The participant must match the agreement’s `deltakerFnr`. All fields required for the measure must be filled.

Failure and exceptional cases:
- Failing function: `approve agreement as participant`
  - Failure condition: required agreement fields are incomplete
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete agreement content.
  - Violated prerequisite or constraint: Omit `PUT /avtaler/{avtaleId}` with the required fields.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-16"></a>
### Behavior 16: Employer agreement approval

Business goal:
Records employer approval on a fully filled agreement.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `approve agreement as employer` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: employer has already approved

Required execution workflow:
1. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with path `avtaleId`; header `If-Unmodified-Since`; cookie `innlogget-part=ARBEIDSGIVER` to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Records employer approval on a fully filled agreement.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/godkjenn` uses `innlogget-part=ARBEIDSGIVER`, and the employer must have access to the agreement’s `bedriftNr` and `tiltakstype`.

Failure and exceptional cases:
- Failing function: `approve agreement as employer`
  - Failure condition: employer has already approved
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: Repeat employer approval without revoking approvals.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-17"></a>
### Behavior 17: Mentor confidentiality signing

Business goal:
Records that the mentor has signed the confidentiality declaration.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `sign mentor confidentiality declaration` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: caller is not logged in as mentor

Required execution workflow:
1. Use function `sign mentor confidentiality declaration` (`POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`) with path `avtaleId`; header `If-Unmodified-Since`; cookie `innlogget-part=MENTOR` to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Records that the mentor has signed the confidentiality declaration.

Constraints and invariants:
- `POST /avtaler/opprett-mentor-avtale` produces `{avtaleId}`. `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring` must be called with `innlogget-part=MENTOR`, and `If-Unmodified-Since` must not be older than the agreement’s current `sistEndret`.

Failure and exceptional cases:
- Failing function: `sign mentor confidentiality declaration`
  - Failure condition: caller is not logged in as mentor
  - Why it fails: The controller explicitly checks `avtalepart.rolle() == MENTOR`.
  - Violated prerequisite or constraint: Use `innlogget-part` other than `MENTOR`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-18"></a>
### Behavior 18: Advisor final approval

Business goal:
Records advisor approval after participant and employer have approved. For measures not requiring decision-maker approval, this also makes the agreement entered.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `approve agreement as advisor` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: participant or employer approval is missing

Required execution workflow:
1. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with path `avtaleId`; header `If-Unmodified-Since`; cookie `innlogget-part=VEILEDER` to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Records advisor approval after participant and employer have approved. For measures not requiring decision-maker approval, this also makes the agreement entered.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/godkjenn` is participant approval, `POST /avtaler/{avtaleId}/godkjenn` is employer approval, and `POST /avtaler/{avtaleId}/godkjenn` is advisor approval with `innlogget-part=VEILEDER`. Each approval uses a fresh `If-Unmodified-Since` value.

Failure and exceptional cases:
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing
  - Why it fails: Advisor approval must happen last.
  - Violated prerequisite or constraint: Call advisor approval before participant and employer approvals.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-19"></a>
### Behavior 19: Advisor approval on behalf of participant

Business goal:
Advisor records both advisor approval and participant approval on behalf of the participant.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `approve on behalf of participant` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: no on-behalf reason is selected

Required execution workflow:
1. Use function `approve on behalf of participant` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av`) with path `avtaleId`; body `GodkjentPaVegneGrunn` with at least one reason; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Advisor records both advisor approval and participant approval on behalf of the participant.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/godkjenn` must be employer approval. `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker` body must set at least one participant reason field in `GodkjentPaVegneGrunn`.

Failure and exceptional cases:
- Failing function: `approve on behalf of participant`
  - Failure condition: no on-behalf reason is selected
  - Why it fails: `GodkjentPaVegneGrunn.valgtMinstEnGrunn` rejects an all-false body.
  - Violated prerequisite or constraint: Send all participant reason booleans as false.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-20"></a>
### Behavior 20: Advisor approval on behalf of employer

Business goal:
Advisor records both advisor approval and employer approval on behalf of the employer.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `approve on behalf of employer` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: measure type is not supported for employer on-behalf approval

Required execution workflow:
1. Use function `approve on behalf of employer` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver`) with path `avtaleId`; body `GodkjentPaVegneAvArbeidsgiverGrunn` with at least one reason; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Advisor records both advisor approval and employer approval on behalf of the employer.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/godkjenn` must be participant approval. The measure must be `SOMMERJOBB`, `MIDLERTIDIG_LONNSTILSKUDD`, or `VARIG_LONNSTILSKUDD`. `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver` body must set at least one employer reason.

Failure and exceptional cases:
- Failing function: `approve on behalf of employer`
  - Failure condition: measure type is not supported for employer on-behalf approval
  - Why it fails: Implementation rejects measure types outside the three subsidized decision-maker measures.
  - Violated prerequisite or constraint: Use `ARBEIDSTRENING`, `INKLUDERINGSTILSKUDD`, or `MENTOR`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-21"></a>
### Behavior 21: Advisor approval on behalf of participant and employer

Business goal:
Advisor records advisor, participant, and employer approvals in one operation.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `approve on behalf of participant and employer` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: participant or employer has already approved

Required execution workflow:
1. Use function `approve on behalf of participant and employer` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`) with path `avtaleId`; body with participant and employer on-behalf reason objects; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Advisor records advisor, participant, and employer approvals in one operation.

Constraints and invariants:
- Measure must be `SOMMERJOBB`, `MIDLERTIDIG_LONNSTILSKUDD`, or `VARIG_LONNSTILSKUDD`. Body contains both reason objects, and each must select at least one reason.

Failure and exceptional cases:
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: participant or employer has already approved
  - Why it fails: The combined endpoint only applies when neither participant nor employer has approved yet.
  - Violated prerequisite or constraint: Create a prior participant or employer approval before the combined call.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-22"></a>
### Behavior 22: Approval revocation

Business goal:
Clears existing participant, employer, and advisor approvals on an agreement that has not yet been entered.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `revoke approvals` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: no approvals exist

Required execution workflow:
1. Use function `revoke approvals` (`POST /avtaler/{avtaleId}/opphev-godkjenninger`) with path `avtaleId`; authenticated caller with required role and access to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Clears existing participant, employer, and advisor approvals on an agreement that has not yet been entered.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/godkjenn` creates at least one approval. `POST /avtaler/{avtaleId}/opphev-godkjenninger` uses an `innlogget-part` allowed to revoke; employer cannot revoke after advisor approval, and no one can revoke after the agreement is entered.

Failure and exceptional cases:
- Failing function: `revoke approvals`
  - Failure condition: no approvals exist
  - Why it fails: The implementation requires at least one approval to revoke.
  - Violated prerequisite or constraint: Omit all approval endpoints.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-23"></a>
### Behavior 23: After-registration eligibility marking

Business goal:
Toggles an unentered agreement so it is approved for after-registration.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `mark agreement eligible for after-registration` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: agreement is already entered

Required execution workflow:
1. Use function `mark agreement eligible for after-registration` (`POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`) with path `avtaleId`; authenticated caller with required role and access to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Toggles an unentered agreement so it is approved for after-registration.

Constraints and invariants:
- Agreement must exist, be accessible to a decision-maker, and not already be entered. Initial `godkjentForEtterregistrering` must be false.

Failure and exceptional cases:
- Failing function: `mark agreement eligible for after-registration`
  - Failure condition: agreement is already entered
  - Why it fails: Entered agreements cannot be marked for after-registration.
  - Violated prerequisite or constraint: Enter the agreement before toggling the flag.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-24"></a>
### Behavior 24: After-registration eligibility removal

Business goal:
Toggles an unentered agreement so it is no longer approved for after-registration.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `remove after-registration eligibility` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: decision-maker access is missing

Required execution workflow:
1. Use function `remove after-registration eligibility` (`POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`) with path `avtaleId`; authenticated caller with required role and access to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Toggles an unentered agreement so it is no longer approved for after-registration.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` enables the flag. `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` consumes the same `{avtaleId}` and disables the flag. Agreement must still not be entered.

Failure and exceptional cases:
- Failing function: `remove after-registration eligibility`
  - Failure condition: decision-maker access is missing
  - Why it fails: The controller calls `innloggingService.hentBeslutter()`.
  - Violated prerequisite or constraint: Call without a valid decision-maker role/group.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-25"></a>
### Behavior 25: Subsidy period approval

Business goal:
Decision-maker approves the current subsidy period; for the first decision-maker-controlled period, this also enters the agreement.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `approve subsidy period` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: `enhet` is not four digits

Required execution workflow:
1. Use function `approve subsidy period` (`POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`) with path `avtaleId`; body `GodkjennTilskuddsperiodeRequest` with four-digit `enhet`; decision-maker caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Decision-maker approves the current subsidy period; for the first decision-maker-controlled period, this also enters the agreement.

Constraints and invariants:
- Use a subsidy-backed measure with generated `tilskuddPeriode`. `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode` body `enhet` must be four digits and must exist in Norg2. Decision-maker cannot be the same NAV ident that approved the agreement.

Failure and exceptional cases:
- Failing function: `approve subsidy period`
  - Failure condition: `enhet` is not four digits
  - Why it fails: `Avtale.godkjennTilskuddsperiode` requires `enhet` to match `^\d{4}$`.
  - Violated prerequisite or constraint: Send a non-four-digit `enhet`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-26"></a>
### Behavior 26: Subsidy period rejection

Business goal:
Decision-maker rejects the current subsidy period with rejection causes and explanation.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `reject subsidy period` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: rejection explanation is blank

Required execution workflow:
1. Use function `reject subsidy period` (`POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`) with path `avtaleId`; body `AvslagRequest` with non-empty `avslagsårsaker` and non-blank `avslagsforklaring`; decision-maker caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Decision-maker rejects the current subsidy period with rejection causes and explanation.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` body must include a non-empty `avslagsårsaker` set and a non-blank `avslagsforklaring`. Current subsidy period must be `UBEHANDLET` and not too early to decide.

Failure and exceptional cases:
- Failing function: `reject subsidy period`
  - Failure condition: rejection explanation is blank
  - Why it fails: `TilskuddPeriode.avslå` requires a non-blank explanation.
  - Violated prerequisite or constraint: Send empty `avslagsforklaring`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-27"></a>
### Behavior 27: Rejected subsidy period return

Business goal:
Deactivates active rejected subsidy periods and creates new unhandled periods for correction.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `send rejected subsidy period back` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: agreement is annulled or interrupted

Required execution workflow:
1. Use function `send rejected subsidy period back` (`POST /avtaler/{avtaleId}/send-tilbake-til-beslutter`) with path `avtaleId`; authenticated caller with required role and access to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Deactivates active rejected subsidy periods and creates new unhandled periods for correction.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` creates the rejected active period that `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter` processes. Advisor must have access.

Failure and exceptional cases:
- Failing function: `send rejected subsidy period back`
  - Failure condition: agreement is annulled or interrupted
  - Why it fails: `sendTilbakeTilBeslutter` rejects annulled/interrupted agreements.
  - Violated prerequisite or constraint: Annul the agreement before sending it back.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-28"></a>
### Behavior 28: Agreement shortening

Business goal:
Creates a new approved version with an earlier end date and adjusts subsidy periods.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `shorten agreement` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: new end date is not before current end date

Required execution workflow:
1. Use function `shorten agreement` (`POST /avtaler/{avtaleId}/forkort`) with path `avtaleId`; body `ForkortAvtale` with earlier `sluttDato`, `grunn`, optional `annetGrunn`; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Creates a new approved version with an earlier end date and adjusts subsidy periods.

Constraints and invariants:
- Agreement must be advisor-approved. `POST /avtaler/{avtaleId}/forkort` body `sluttDato` must be before current end date, after any paid/sent-claim subsidy period, and `grunn` must be present; if `grunn=Annet`, `annetGrunn` must be present.

Failure and exceptional cases:
- Failing function: `shorten agreement`
  - Failure condition: new end date is not before current end date
  - Why it fails: The implementation rejects shortening to the same or a later end date.
  - Violated prerequisite or constraint: Send `sluttDato` equal to or after the current `sluttDato`.

Implementation notes:
The controller accepts `If-Unmodified-Since`, but the source path does not consume it in the domain call.

<a id="behavior-29"></a>
### Behavior 29: Dry-run agreement shortening

Business goal:
Returns the would-be shortened agreement without saving it.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `dry-run agreement shortening` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target resource exists in the state required by the simulated transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation applies validation or calculation in memory or logs, then avoids repository save.
- State after: No persisted state changes; only a preview or diagnostic is produced.
- Invalid or blocked transitions: agreement is not advisor-approved

Required execution workflow:
1. Use function `dry-run agreement shortening` (`POST /avtaler/{avtaleId}/forkort-dry-run`) with path `avtaleId`; body `ForkortAvtale` with earlier `sluttDato`; advisor caller to validate and preview the transition without saving it.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Returns the would-be shortened agreement without saving it.

Constraints and invariants:
- Same date constraints as shorten. The controller substitutes `"dry run"` as reason and does not persist.

Failure and exceptional cases:
- Failing function: `dry-run agreement shortening`
  - Failure condition: agreement is not advisor-approved
  - Why it fails: `forkortAvtale` requires advisor approval.
  - Violated prerequisite or constraint: Omit the approval sequence.

Implementation notes:
This is a non-persistent simulation; it may validate and recalculate but should not save state.

<a id="behavior-30"></a>
### Behavior 30: Agreement extension

Business goal:
Creates a new approved version with a later end date and adds or recalculates subsidy periods.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `extend agreement` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: new end date is not after current end date

Required execution workflow:
1. Use function `extend agreement` (`POST /avtaler/{avtaleId}/forleng`) with path `avtaleId`; body `ForlengAvtale` with later `sluttDato`; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Creates a new approved version with a later end date and adds or recalculates subsidy periods.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/forleng` body `sluttDato` must be after current `sluttDato` and satisfy measure-specific duration rules.

Failure and exceptional cases:
- Failing function: `extend agreement`
  - Failure condition: new end date is not after current end date
  - Why it fails: The implementation rejects extension unless `sluttDato` increases.
  - Violated prerequisite or constraint: Send a same or earlier `sluttDato`.

Implementation notes:
The controller accepts `If-Unmodified-Since`, but the source path does not consume it in the domain call.

<a id="behavior-31"></a>
### Behavior 31: Dry-run agreement extension

Business goal:
Returns the would-be extended agreement without saving it.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `dry-run agreement extension` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target resource exists in the state required by the simulated transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation applies validation or calculation in memory or logs, then avoids repository save.
- State after: No persisted state changes; only a preview or diagnostic is produced.
- Invalid or blocked transitions: agreement is not advisor-approved

Required execution workflow:
1. Use function `dry-run agreement extension` (`POST /avtaler/{avtaleId}/forleng-dry-run`) with path `avtaleId`; body `ForlengAvtale` with later `sluttDato`; advisor caller to validate and preview the transition without saving it.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Returns the would-be extended agreement without saving it.

Constraints and invariants:
- Same constraints as extension. No persistence.

Failure and exceptional cases:
- Failing function: `dry-run agreement extension`
  - Failure condition: agreement is not advisor-approved
  - Why it fails: Extension is only allowed on advisor-approved agreements.
  - Violated prerequisite or constraint: Omit the approval sequence.

Implementation notes:
This is a non-persistent simulation; it may validate and recalculate but should not save state.

<a id="behavior-32"></a>
### Behavior 32: Subsidy calculation change

Business goal:
Creates a new approved version with changed monthly salary, holiday pay rate, employer tax, and pension rate, then recalculates unhandled subsidy amounts.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `change subsidy calculation` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: agreement is not approved by advisor

Required execution workflow:
1. Use function `change subsidy calculation` (`POST /avtaler/{avtaleId}/endre-tilskuddsberegning`) with path `avtaleId`; body `EndreTilskuddsberegning` with calculation fields; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Creates a new approved version with changed monthly salary, holiday pay rate, employer tax, and pension rate, then recalculates unhandled subsidy amounts.

Constraints and invariants:
- Agreement must be advisor-approved and measure must be `MIDLERTIDIG_LONNSTILSKUDD`, `VARIG_LONNSTILSKUDD`, or `SOMMERJOBB`. Body fields `manedslonn`, `feriepengesats`, `arbeidsgiveravgift`, and `otpSats` must be present.

Failure and exceptional cases:
- Failing function: `change subsidy calculation`
  - Failure condition: agreement is not approved by advisor
  - Why it fails: Economy changes require an advisor-approved agreement.
  - Violated prerequisite or constraint: Omit the approval sequence.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-33"></a>
### Behavior 33: Dry-run subsidy calculation change

Business goal:
Returns the would-be updated agreement after subsidy calculation changes without saving.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `dry-run subsidy calculation change` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target resource exists in the state required by the simulated transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation applies validation or calculation in memory or logs, then avoids repository save.
- State after: No persisted state changes; only a preview or diagnostic is produced.
- Invalid or blocked transitions: required calculation input is missing

Required execution workflow:
1. Use function `dry-run subsidy calculation change` (`POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run`) with path `avtaleId`; body `EndreTilskuddsberegning`; advisor caller to validate and preview the transition without saving it.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Returns the would-be updated agreement after subsidy calculation changes without saving.

Constraints and invariants:
- Same measure, approval, and body constraints as the persistent calculation change. No save.

Failure and exceptional cases:
- Failing function: `dry-run subsidy calculation change`
  - Failure condition: required calculation input is missing
  - Why it fails: The implementation requires all calculation inputs.
  - Violated prerequisite or constraint: Omit one of `manedslonn`, `feriepengesats`, `arbeidsgiveravgift`, or `otpSats`.

Implementation notes:
This is a non-persistent simulation; it may validate and recalculate but should not save state.

<a id="behavior-34"></a>
### Behavior 34: Contact information change

Business goal:
Creates a new approved version with changed participant, advisor, employer, and refund contact information.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `change contact information` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: required contact field is missing

Required execution workflow:
1. Use function `change contact information` (`POST /avtaler/{avtaleId}/endre-kontaktinfo`) with path `avtaleId`; body `EndreKontaktInformasjon` with participant, advisor, and employer contact fields; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Creates a new approved version with changed participant, advisor, employer, and refund contact information.

Constraints and invariants:
- Agreement must be advisor-approved. Required contact fields must be non-empty.

Failure and exceptional cases:
- Failing function: `change contact information`
  - Failure condition: required contact field is missing
  - Why it fails: `endreKontaktInformasjon` rejects missing contact fields.
  - Violated prerequisite or constraint: Omit one required name or phone field.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-35"></a>
### Behavior 35: Job description change

Business goal:
Creates a new approved version with changed title, work tasks, occupation code, position percent, and days per week.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `change job description` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: agreement is not advisor-approved

Required execution workflow:
1. Use function `change job description` (`POST /avtaler/{avtaleId}/endre-stillingbeskrivelse`) with path `avtaleId`; body `EndreStillingsbeskrivelse` with job-title, task, occupation, percentage, and workday fields; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Creates a new approved version with changed title, work tasks, occupation code, position percent, and days per week.

Constraints and invariants:
- Agreement must be advisor-approved. Body fields required by `EndreStillingsbeskrivelse` must be present.

Failure and exceptional cases:
- Failing function: `change job description`
  - Failure condition: agreement is not advisor-approved
  - Why it fails: Job description changes are only allowed on approved agreements.
  - Violated prerequisite or constraint: Omit the approval sequence.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-36"></a>
### Behavior 36: Follow-up and adaptation text change

Business goal:
Creates a new approved version with changed follow-up and adaptation descriptions.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `change follow-up and adaptation text` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: follow-up or adaptation text is missing

Required execution workflow:
1. Use function `change follow-up and adaptation text` (`POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging`) with path `avtaleId`; body `EndreOppfølgingOgTilrettelegging` with `oppfolging` and `tilrettelegging`; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Creates a new approved version with changed follow-up and adaptation descriptions.

Constraints and invariants:
- Agreement must be advisor-approved. `oppfolging` and `tilrettelegging` must both be present.

Failure and exceptional cases:
- Failing function: `change follow-up and adaptation text`
  - Failure condition: follow-up or adaptation text is missing
  - Why it fails: The implementation rejects missing text fields.
  - Violated prerequisite or constraint: Omit `oppfolging` or `tilrettelegging`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-37"></a>
### Behavior 37: Work-training goal replacement

Business goal:
Replaces goals on an approved work-training agreement.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `change work-training goals` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: agreement is not work training

Required execution workflow:
1. Use function `change work-training goals` (`POST /avtaler/{avtaleId}/endre-maal`) with path `avtaleId`; body `EndreMål` with non-empty `maal` list; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Replaces goals on an approved work-training agreement.

Constraints and invariants:
- Agreement must have `tiltakstype=ARBEIDSTRENING` and be advisor-approved. Body `maal` must be non-empty, and each goal must have `beskrivelse` and `kategori`.

Failure and exceptional cases:
- Failing function: `change work-training goals`
  - Failure condition: agreement is not work training
  - Why it fails: `endreMål` requires `Tiltakstype.ARBEIDSTRENING`.
  - Violated prerequisite or constraint: Create any other `tiltakstype`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-38"></a>
### Behavior 38: Inclusion subsidy expense replacement

Business goal:
Replaces inclusion subsidy expense lines on an approved inclusion subsidy agreement.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `change inclusion subsidy expenses` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: total inclusion subsidy amount is too high

Required execution workflow:
1. Use function `change inclusion subsidy expenses` (`POST /avtaler/{avtaleId}/endre-inkluderingstilskudd`) with path `avtaleId`; body `EndreInkluderingstilskudd` with expense rows; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Replaces inclusion subsidy expense lines on an approved inclusion subsidy agreement.

Constraints and invariants:
- Agreement must have `tiltakstype=INKLUDERINGSTILSKUDD` and be advisor-approved. Expense list must be non-empty; each item must include `beløp` and `type`; total must not exceed the implementation limit.

Failure and exceptional cases:
- Failing function: `change inclusion subsidy expenses`
  - Failure condition: total inclusion subsidy amount is too high
  - Why it fails: `endreInkluderingstilskudd` rejects totals above the configured code limit.
  - Violated prerequisite or constraint: Send expense lines whose total exceeds the allowed maximum.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-39"></a>
### Behavior 39: Mentor details change

Business goal:
Creates a new approved version with changed mentor name, phone, tasks, hours, and hourly wage.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `change mentor details` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: agreement is not a mentor agreement

Required execution workflow:
1. Use function `change mentor details` (`POST /avtaler/{avtaleId}/endre-om-mentor`) with path `avtaleId`; body `EndreOmMentor` with mentor contact, wage, hours, and task fields; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Creates a new approved version with changed mentor name, phone, tasks, hours, and hourly wage.

Constraints and invariants:
- Agreement must have `tiltakstype=MENTOR` and be advisor-approved. Mentor detail fields must be present.

Failure and exceptional cases:
- Failing function: `change mentor details`
  - Failure condition: agreement is not a mentor agreement
  - Why it fails: `endreOmMentor` requires `Tiltakstype.MENTOR`.
  - Violated prerequisite or constraint: Use a non-mentor agreement.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-40"></a>
### Behavior 40: Cost center change

Business goal:
Sets the cost-center unit and unit name on unhandled or rejected subsidy periods.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `change cost center` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: norg2 does not return a unit for `enhet`

Required execution workflow:
1. Use function `change cost center` (`POST /avtaler/{avtaleId}/endre-kostnadssted`) with path `avtaleId`; body `EndreKostnadsstedRequest` with `enhet`; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Sets the cost-center unit and unit name on unhandled or rejected subsidy periods.

Constraints and invariants:
- The agreement must have generated subsidy periods that are not closed for editing. Body `enhet` must exist in Norg2. Agreement must not be entered.

Failure and exceptional cases:
- Failing function: `change cost center`
  - Failure condition: norg2 does not return a unit for `enhet`
  - Why it fails: `Veileder.oppdatereKostnadssted` throws `ENHET_FINNES_IKKE`.
  - Violated prerequisite or constraint: Use an unknown unit number.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-41"></a>
### Behavior 41: Arena migration date adjustment

Business goal:
Recomputes subsidy periods around a migration date and saves or updates the agreement’s Arena cleanup marker.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `adjust Arena migration date` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: agreement is already entered

Required execution workflow:
1. Use function `adjust Arena migration date` (`POST /avtaler/{avtaleId}/juster-arena-migreringsdato`) with path `avtaleId`; body `JusterArenaMigreringsdato` with `migreringsdato`; advisor caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Recomputes subsidy periods around a migration date and saves or updates the agreement’s Arena cleanup marker.

Constraints and invariants:
- Body contains `migreringsdato`. Agreement must not be entered. Generated periods ending before the migration date are marked `BEHANDLET_I_ARENA`.

Failure and exceptional cases:
- Failing function: `adjust Arena migration date`
  - Failure condition: agreement is already entered
  - Why it fails: The controller rejects migration-date changes on entered agreements.
  - Violated prerequisite or constraint: Enter the agreement before adjusting migration date.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-42"></a>
### Behavior 42: Dry-run Arena migration date adjustment

Business goal:
Returns the would-be agreement after recalculating periods around the migration date without saving it.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `dry-run Arena migration date adjustment` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target resource exists in the state required by the simulated transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation applies validation or calculation in memory or logs, then avoids repository save.
- State after: No persisted state changes; only a preview or diagnostic is produced.
- Invalid or blocked transitions: agreement is missing

Required execution workflow:
1. Use function `dry-run Arena migration date adjustment` (`POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run`) with path `avtaleId`; body `JusterArenaMigreringsdato` with `migreringsdato`; advisor caller to validate and preview the transition without saving it.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Returns the would-be agreement after recalculating periods around the migration date without saving it.

Constraints and invariants:
- Body contains `migreringsdato`. Advisor must have access. Unlike the persistent endpoint, the implementation does not explicitly reject entered agreements before recalculation.

Failure and exceptional cases:
- Failing function: `dry-run Arena migration date adjustment`
  - Failure condition: agreement is missing
  - Why it fails: Repository lookup by `{avtaleId}` throws resource-not-found.
  - Violated prerequisite or constraint: Omit the agreement creation endpoint.

Implementation notes:
This is a non-persistent simulation; it may validate and recalculate but should not save state.

<a id="behavior-43"></a>
### Behavior 43: Employer account number lookup

Business goal:
Returns the employer’s bank account number for the agreement’s company.

API group boundary:
Atomic read/export/lookup behavior. The single function `get employer account number` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: account register has no company account

Required execution workflow:
1. Use function `get employer account number` (`GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver`) with path `avtaleId`; authenticated caller with required role and access to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Returns the employer’s bank account number for the agreement’s company.

Constraints and invariants:
- `POST /avtaler` produces `{avtaleId}`. `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver` uses the agreement’s `bedriftNr` when calling the account register.

Failure and exceptional cases:
- Failing function: `get employer account number`
  - Failure condition: account register has no company account
  - Why it fails: The real account-register client maps 404 to `KONTOREGISTER_FEIL_BEDRIFT_IKKE_FUNNET`.
  - Violated prerequisite or constraint: Use an agreement whose company is not found in the account register.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-44"></a>
### Behavior 44: Agreement PDF download

Business goal:
Returns a PDF representation of an advisor-approved agreement.

API group boundary:
Atomic read/export/lookup behavior. The single function `download agreement PDF` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: agreement is not approved by advisor

Required execution workflow:
1. Use function `download agreement PDF` (`GET /avtaler/{avtaleId}/pdf`) with path `avtaleId`; authenticated caller with required role and access to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Returns a PDF representation of an advisor-approved agreement.

Constraints and invariants:
- Agreement must be approved by advisor before `GET /avtaler/{avtaleId}/pdf`. Response is `application/pdf`.

Failure and exceptional cases:
- Failing function: `download agreement PDF`
  - Failure condition: agreement is not approved by advisor
  - Why it fails: The controller throws `KAN_IKKE_LASTE_NED_PDF`.
  - Violated prerequisite or constraint: Omit advisor approval.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-45"></a>
### Behavior 45: Salesforce dialog visibility check

Business goal:
Returns `true` when the agreement belongs to a configured Salesforce office, has `MIDLERTIDIG_LONNSTILSKUDD`, and is `GJENNOMFØRES` or `AVSLUTTET`; otherwise returns `false`.

API group boundary:
Atomic read/export/lookup behavior. The single function `check Salesforce dialog visibility` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: agreement is missing or inaccessible

Required execution workflow:
1. Use function `check Salesforce dialog visibility` (`GET /avtaler/{avtaleId}/vis-salesforce-dialog`) with path `avtaleId`; authenticated caller with required role and access to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Returns `true` when the agreement belongs to a configured Salesforce office, has `MIDLERTIDIG_LONNSTILSKUDD`, and is `GJENNOMFØRES` or `AVSLUTTET`; otherwise returns `false`.

Constraints and invariants:
- `GET /avtaler/{avtaleId}/vis-salesforce-dialog` consumes `{avtaleId}` from `POST /avtaler`. The current agreement state and configured office list determine the boolean.

Failure and exceptional cases:
- Failing function: `check Salesforce dialog visibility`
  - Failure condition: agreement is missing or inaccessible
  - Why it fails: The controller loads the agreement through access-checked `hentAvtale`.
  - Violated prerequisite or constraint: Use an unknown `{avtaleId}` or unauthorized role.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-46"></a>
### Behavior 46: Follow-up unit refresh

Business goal:
Refreshes participant data, follow-up status, geographic unit, and follow-up unit name, then saves the agreement.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `refresh follow-up unit` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: participant has protected address code 6 when person data is refreshed

Required execution workflow:
1. Use function `refresh follow-up unit` (`POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet`) with path `avtaleId`; authenticated caller with required role and access to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Refreshes participant data, follow-up status, geographic unit, and follow-up unit name, then saves the agreement.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet` consumes `{avtaleId}` and must be called by an advisor with access.

Failure and exceptional cases:
- Failing function: `refresh follow-up unit`
  - Failure condition: participant has protected address code 6 when person data is refreshed
  - Why it fails: Person-data refresh uses the same code-6 guard as agreement creation/update.
  - Violated prerequisite or constraint: Use an agreement participant with code 6 protection.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-47"></a>
### Behavior 47: Advisor takeover of agreement

Business goal:
Assigns the logged-in advisor as the agreement’s advisor and refreshes follow-up data.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `take over agreement as advisor` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: logged-in advisor is already the agreement advisor

Required execution workflow:
1. Use function `take over agreement as advisor` (`PUT /avtaler/{avtaleId}/overta`) with path `avtaleId`; authenticated caller with required role and access to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Assigns the logged-in advisor as the agreement’s advisor and refreshes follow-up data.

Constraints and invariants:
- `PUT /avtaler/{avtaleId}/overta` consumes `{avtaleId}`. The logged-in advisor must have access and must not already be the agreement’s advisor.

Failure and exceptional cases:
- Failing function: `take over agreement as advisor`
  - Failure condition: logged-in advisor is already the agreement advisor
  - Why it fails: `Veileder.overtaAvtale` throws `ER_ALLEREDE_VEILEDER`.
  - Violated prerequisite or constraint: Use the same advisor who created or already owns the agreement.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-48"></a>
### Behavior 48: Agreement annulment

Business goal:
Annuls an agreement and annuls/removes eligible subsidy periods; if reason is `Feilregistrering`, the agreement is also marked as incorrectly registered.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `annul agreement` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: agreement contains a paid subsidy period

Required execution workflow:
1. Use function `annul agreement` (`POST /avtaler/{avtaleId}/annuller`) with path `avtaleId`; authenticated caller with required role and access to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Annuls an agreement and annuls/removes eligible subsidy periods; if reason is `Feilregistrering`, the agreement is also marked as incorrectly registered.

Constraints and invariants:
- `POST /avtaler/{avtaleId}/annuller` body contains `annullertGrunn`. `If-Unmodified-Since` must not be stale. Agreement must not contain paid or refund-approved subsidy periods.

Failure and exceptional cases:
- Failing function: `annul agreement`
  - Failure condition: agreement contains a paid subsidy period
  - Why it fails: `sjekkAtIkkeAvtalenInneholderUtbetaltTilskuddsperiode` blocks annulment when a period is paid or refund-approved.
  - Violated prerequisite or constraint: Annul after period/refund state blocks annulment.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-49"></a>
### Behavior 49: Agreement soft deletion

Business goal:
Marks the agreement as deleted/hidden.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `soft-delete agreement` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: advisor is not configured as a delete-marker admin

Required execution workflow:
1. Use function `soft-delete agreement` (`POST /avtaler/{avtaleId}/slettemerk`) with path `avtaleId`; authenticated caller with required role and access to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Marks the agreement as deleted/hidden.

Constraints and invariants:
- Logged-in advisor must have access and must be configured in `SlettemerkeProperties.ident`.

Failure and exceptional cases:
- Failing function: `soft-delete agreement`
  - Failure condition: advisor is not configured as a delete-marker admin
  - Why it fails: `Veileder.slettemerk` checks configured allowed NAV idents.
  - Violated prerequisite or constraint: Call as a valid advisor not in the configured admin ident list.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-50"></a>
### Behavior 50: Employer Min Side agreement listing

Business goal:
Returns all agreements for a company that the logged-in employer can view.

API group boundary:
Atomic read/export/lookup behavior. The single function `list employer agreements` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: caller is not an employer token/role

Required execution workflow:
1. Use function `list employer agreements` (`GET /avtaler/min-side-arbeidsgiver`) with query/path values documented by the endpoint; cookie `innlogget-part` when required to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns all agreements for a company that the logged-in employer can view.

Constraints and invariants:
- Query `bedriftNr` is required. Employer must have Altinn access to the company/measure; old annulled, interrupted, or ended agreements may be filtered out.

Failure and exceptional cases:
- Failing function: `list employer agreements`
  - Failure condition: caller is not an employer token/role
  - Why it fails: The controller calls `innloggingService.hentArbeidsgiver()`.
  - Violated prerequisite or constraint: Call with non-employer token/role.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-51"></a>
### Behavior 51: Decision-maker work queue listing

Business goal:
Returns paginated agreements whose subsidy periods match decision-maker filters and the decision-maker’s NAV units.

API group boundary:
Atomic read/export/lookup behavior. The single function `list decision-maker agreements` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: decision-maker has no NAV units

Required execution workflow:
1. Use function `list decision-maker agreements` (`GET /avtaler/beslutter-liste`) with query/path values documented by the endpoint; cookie `innlogget-part` when required to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns paginated agreements whose subsidy periods match decision-maker filters and the decision-maker’s NAV units.

Constraints and invariants:
- Caller must be a decision-maker. If `tilskuddPeriodeStatus` is absent, implementation defaults to `UBEHANDLET`. If `tiltakstype` is absent, it searches `SOMMERJOBB`, `VARIG_LONNSTILSKUDD`, and `MIDLERTIDIG_LONNSTILSKUDD`.

Failure and exceptional cases:
- Failing function: `list decision-maker agreements`
  - Failure condition: decision-maker has no NAV units
  - Why it fails: `Beslutter.finnGodkjente...` throws `NAV_ENHET_IKKE_FUNNET`.
  - Violated prerequisite or constraint: Use a decision-maker identity with no AXsys units.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-52"></a>
### Behavior 52: Logged-in user context lookup

Business goal:
Returns role-specific information for the logged-in user.

API group boundary:
Atomic read/export/lookup behavior. The single function `get logged-in user` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: `innlogget-part` cookie is absent

Required execution workflow:
1. Use function `get logged-in user` (`GET /innlogget-bruker`) with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns role-specific information for the logged-in user.

Constraints and invariants:
- Although OpenAPI marks `innlogget-part` as optional, implementation requires it and throws if absent. The role must match the token issuer.

Failure and exceptional cases:
- Failing function: `get logged-in user`
  - Failure condition: `innlogget-part` cookie is absent
  - Why it fails: The implementation calls `orElseThrow(IkkeValgtPartException::new)`.
  - Violated prerequisite or constraint: Omit `innlogget-part`.

Implementation notes:
OpenAPI marks `innlogget-part` as optional, but implementation throws when it is absent.

<a id="behavior-53"></a>
### Behavior 53: Employer organization lookup

Business goal:
Returns organization data for an employer unit.

API group boundary:
Atomic read/export/lookup behavior. The single function `look up organization` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: ereg does not find the unit

Required execution workflow:
1. Use function `look up organization` (`GET /organisasjoner`) with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns organization data for an employer unit.

Constraints and invariants:
- Query `bedriftNr` is required. Ereg result must not be a juridical unit or organization-led unit.

Failure and exceptional cases:
- Failing function: `look up organization`
  - Failure condition: ereg does not find the unit
  - Why it fails: `EregService` maps Ereg client errors to `ENHET_FINNES_IKKE`.
  - Violated prerequisite or constraint: Use an unknown `bedriftNr`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-54"></a>
### Behavior 54: Altinn rights request URL lookup

Business goal:
Returns URLs that let an employer request Altinn rights for each supported measure type.

API group boundary:
Atomic read/export/lookup behavior. The single function `get Altinn rights request URLs` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: Authentication, authorization, parameter binding, missing resource, or endpoint-specific domain validation can block the operation.

Required execution workflow:
1. Use function `get Altinn rights request URLs` (`GET /be-om-altinn-rettighet-urler`) with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns URLs that let an employer request Altinn rights for each supported measure type.

Constraints and invariants:
- Query `orgNr` is appended as `bedrift` to the configured base URL. The response maps supported `Tiltakstype` values to URLs.

Failure and exceptional cases:
- Failing function: `get Altinn rights request URLs`
  - Failure condition: Authentication, authorization, parameter binding, or external dependency failure.
  - Why it fails: The framework or called service rejects the request before the domain result is produced.
  - Violated prerequisite or constraint: Valid caller context and required request values are prerequisites.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-55"></a>
### Behavior 55: Combined code-list lookup

Business goal:
Returns both measure types and agreement statuses.

API group boundary:
Atomic read/export/lookup behavior. The single function `get all code lists` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: Authentication, authorization, parameter binding, missing resource, or endpoint-specific domain validation can block the operation.

Required execution workflow:
1. Use function `get all code lists` (`GET /kodeverk`) with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns both measure types and agreement statuses.

Constraints and invariants:
- No resource state required.

Failure and exceptional cases:
- Failing function: `get all code lists`
  - Failure condition: Authentication, authorization, parameter binding, or external dependency failure.
  - Why it fails: The framework or called service rejects the request before the domain result is produced.
  - Violated prerequisite or constraint: Valid caller context and required request values are prerequisites.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-56"></a>
### Behavior 56: Agreement status code-list lookup

Business goal:
Returns all `Status` enum names and descriptions.

API group boundary:
Atomic read/export/lookup behavior. The single function `get status code list` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: Authentication, authorization, parameter binding, missing resource, or endpoint-specific domain validation can block the operation.

Required execution workflow:
1. Use function `get status code list` (`GET /kodeverk/statuser`) with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns all `Status` enum names and descriptions.

Constraints and invariants:
- No resource state required.

Failure and exceptional cases:
- Failing function: `get status code list`
  - Failure condition: Authentication, authorization, parameter binding, or external dependency failure.
  - Why it fails: The framework or called service rejects the request before the domain result is produced.
  - Violated prerequisite or constraint: Valid caller context and required request values are prerequisites.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-57"></a>
### Behavior 57: Measure type code-list lookup

Business goal:
Returns all `Tiltakstype` enum names, descriptions, treatment themes, and optional Arena codes.

API group boundary:
Atomic read/export/lookup behavior. The single function `get measure type code list` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: Authentication, authorization, parameter binding, missing resource, or endpoint-specific domain validation can block the operation.

Required execution workflow:
1. Use function `get measure type code list` (`GET /kodeverk/tiltakstyper`) with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns all `Tiltakstype` enum names, descriptions, treatment themes, and optional Arena codes.

Constraints and invariants:
- No resource state required.

Failure and exceptional cases:
- Failing function: `get measure type code list`
  - Failure condition: Authentication, authorization, parameter binding, or external dependency failure.
  - Why it fails: The framework or called service rejects the request before the domain result is produced.
  - Violated prerequisite or constraint: Valid caller context and required request values are prerequisites.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-58"></a>
### Behavior 58: Feature toggle evaluation

Business goal:
Returns enabled/disabled values for requested feature names.

API group boundary:
Atomic read/export/lookup behavior. The single function `evaluate feature toggles` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: Authentication, authorization, parameter binding, missing resource, or endpoint-specific domain validation can block the operation.

Required execution workflow:
1. Use function `evaluate feature toggles` (`GET /feature`) with query list `feature` to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns enabled/disabled values for requested feature names.

Constraints and invariants:
- Query `feature` is a list. Each feature name becomes a key in the returned map.

Failure and exceptional cases:
- Failing function: `evaluate feature toggles`
  - Failure condition: Authentication, authorization, parameter binding, or external dependency failure.
  - Why it fails: The framework or called service rejects the request before the domain result is produced.
  - Violated prerequisite or constraint: Valid caller context and required request values are prerequisites.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-59"></a>
### Behavior 59: Feature variant lookup

Business goal:
Returns Unleash variant objects for requested feature names.

API group boundary:
Atomic read/export/lookup behavior. The single function `get feature variants` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: Authentication, authorization, parameter binding, missing resource, or endpoint-specific domain validation can block the operation.

Required execution workflow:
1. Use function `get feature variants` (`GET /feature/variant`) with query list `feature` to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns Unleash variant objects for requested feature names.

Constraints and invariants:
- Query `feature` is a list. Logged-in user id is included in the Unleash context when available.

Failure and exceptional cases:
- Failing function: `get feature variants`
  - Failure condition: Authentication, authorization, parameter binding, or external dependency failure.
  - Why it fails: The framework or called service rejects the request before the domain result is produced.
  - Violated prerequisite or constraint: Valid caller context and required request values are prerequisites.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-60"></a>
### Behavior 60: Internal health probe

Business goal:
Returns `ok` if the database query succeeds.

API group boundary:
Atomic read/export/lookup behavior. The single function `run health check` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: database query fails

Required execution workflow:
1. Use function `run health check` (`GET /internal/healthcheck`) with authenticated caller and endpoint-specific required request values to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns `ok` if the database query succeeds.

Constraints and invariants:
- No specific resource state required; database must answer `select 'ok'`.

Failure and exceptional cases:
- Failing function: `run health check`
  - Failure condition: database query fails
  - Why it fails: The controller directly returns the JDBC query result.
  - Violated prerequisite or constraint: Database unavailable or query cannot execute.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-61"></a>
### Behavior 61: Overview notification listing

Business goal:
Returns unread bell notifications for the logged-in party’s identifiers.

API group boundary:
Atomic read/export/lookup behavior. The single function `list overview notifications` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: unsupported role/token combination

Required execution workflow:
1. Use function `list overview notifications` (`GET /varsler/oversikt`) with cookie `innlogget-part` to obtain notification ids visible to the caller.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns unread bell notifications for the logged-in party’s identifiers.

Constraints and invariants:
- Agreement creation and other agreement events create notifications through event listeners. `GET /varsler/oversikt` returns only `lest=false`, `bjelle=true`, and matching identifiers.

Failure and exceptional cases:
- Failing function: `list overview notifications`
  - Failure condition: unsupported role/token combination
  - Why it fails: Notification lookup is scoped through `hentAvtalepart`.
  - Violated prerequisite or constraint: Use invalid `innlogget-part` for the token.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-62"></a>
### Behavior 62: Agreement modal notification listing

Business goal:
Returns unread bell notifications for a specific agreement and logged-in party.

API group boundary:
Atomic read/export/lookup behavior. The single function `list agreement modal notifications` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: Authentication, authorization, parameter binding, missing resource, or endpoint-specific domain validation can block the operation.

Required execution workflow:
1. Use function `list agreement modal notifications` (`GET /varsler/avtale-modal`) with query `avtaleId`; cookie `innlogget-part` to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns unread bell notifications for a specific agreement and logged-in party.

Constraints and invariants:
- `POST /avtaler` produces `{avtaleId}`; `GET /varsler/avtale-modal` uses query `avtaleId={avtaleId}` and filters by the logged-in party identifiers.

Failure and exceptional cases:
- Failing function: `list agreement modal notifications`
  - Failure condition: Authentication, authorization, parameter binding, or external dependency failure.
  - Why it fails: The framework or called service rejects the request before the domain result is produced.
  - Violated prerequisite or constraint: Valid caller context and required request values are prerequisites.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-63"></a>
### Behavior 63: Agreement notification log listing

Business goal:
Returns all notifications for a specific agreement and receiver role.

API group boundary:
Atomic read/export/lookup behavior. The single function `list agreement notification log` is itself the domain-facing lookup.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Relevant service or upstream data may already exist; empty datasets are allowed for collection reads.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation resolves caller context and resource scope, then returns a read model without saving changes.
- State after: No persisted service state changes.
- Invalid or blocked transitions: agreement id is unknown

Required execution workflow:
1. Use function `list agreement notification log` (`GET /varsler/avtale-logg`) with query `avtaleId`; cookie `innlogget-part` to return the requested view without changing persisted state.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Returns all notifications for a specific agreement and receiver role.

Constraints and invariants:
- `GET /varsler/avtale-logg` uses `avtaleId` from `POST /avtaler` and checks the logged-in party’s access to the agreement before returning log entries.

Failure and exceptional cases:
- Failing function: `list agreement notification log`
  - Failure condition: agreement id is unknown
  - Why it fails: The controller calls `avtaleRepository.findById(avtaleId).orElseThrow()`.
  - Violated prerequisite or constraint: Omit agreement creation and use an unknown `avtaleId`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-64"></a>
### Behavior 64: Single notification read marking

Business goal:
Mark one notification as read for the logged-in party.

API group boundary:
Composite workflow with explicit response-to-request value binding across the same lifecycle resource.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: notification does not belong to logged-in party

Required execution workflow:
1. Use function `list overview notifications` (`GET /varsler/oversikt`) with cookie `innlogget-part` to obtain notification ids visible to the caller.
2. Use function `mark notification as read` (`POST /varsler/{varselId}/sett-til-lest`) with path `varselId` returned from `list overview notifications`; same cookie `innlogget-part` to set that notification to read.

Optional verification workflow:
1. Use function `list overview notifications` (`GET /varsler/oversikt`) with cookie `innlogget-part` to verify the submitted ids are absent from unread notifications.

Existing-state shortcuts:
- If the response value consumed by the later step is already known and still belongs to the same scope, the discovery step can be skipped; direct database setup must preserve the same ownership and lifecycle state.

Parameter and value bindings:
- `varselId` values must be returned for, or otherwise owned by, the same caller identifiers.

Business result:
Marks one notification as read for the logged-in party.

Constraints and invariants:
- `GET /varsler/oversikt` returns a notification id. `POST /varsler/{varselId}/sett-til-lest` consumes that exact `{varselId}`. Notification must belong to one of the logged-in party’s identifiers.

Failure and exceptional cases:
- Failing function: `list overview notifications`
  - Failure condition: unsupported role/token combination
  - Why it fails: Notification lookup is scoped through `hentAvtalepart`.
  - Violated prerequisite or constraint: Use invalid `innlogget-part` for the token.
- Failing function: `mark notification as read`
  - Failure condition: notification does not belong to logged-in party
  - Why it fails: Repository lookup by id and identifiers returns null, then `varsel.settTilLest()` fails.
  - Violated prerequisite or constraint: Reuse a `{varselId}` from another party’s notification list.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-65"></a>
### Behavior 65: Bulk notification read marking

Business goal:
Mark several caller-owned notifications as read in one request.

API group boundary:
Composite workflow with explicit response-to-request value binding across the same lifecycle resource.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: any body id is not readable by the logged-in party

Required execution workflow:
1. Use function `list overview notifications` (`GET /varsler/oversikt`) with cookie `innlogget-part` to obtain notification ids visible to the caller.
2. Use function `mark multiple notifications as read` (`POST /varsler/sett-alle-til-lest`) with body array of `varselId` values returned from `list overview notifications`; same cookie `innlogget-part` to set all submitted notifications to read.

Optional verification workflow:
1. Use function `list overview notifications` (`GET /varsler/oversikt`) with cookie `innlogget-part` to verify the submitted ids are absent from unread notifications.

Existing-state shortcuts:
- If the response value consumed by the later step is already known and still belongs to the same scope, the discovery step can be skipped; direct database setup must preserve the same ownership and lifecycle state.

Parameter and value bindings:
- `varselId` values must be returned for, or otherwise owned by, the same caller identifiers.

Business result:
Marks each notification id in the request body as read.

Constraints and invariants:
- `GET /varsler/oversikt` produces notification ids. `POST /varsler/sett-alle-til-lest` body is an array of those ids and internally calls the single-notification read function for each.

Failure and exceptional cases:
- Failing function: `list overview notifications`
  - Failure condition: unsupported role/token combination
  - Why it fails: Notification lookup is scoped through `hentAvtalepart`.
  - Violated prerequisite or constraint: Use invalid `innlogget-part` for the token.
- Failing function: `mark multiple notifications as read`
  - Failure condition: any body id is not readable by the logged-in party
  - Why it fails: The loop delegates to `settTilLest`; one invalid id causes the same failure as the single-read endpoint.
  - Violated prerequisite or constraint: Include at least one foreign or unknown notification id.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-66"></a>
### Behavior 66: Journal export and completion marking

Business goal:
Export unjournaled agreement versions and mark the consumed versions as journaled.

API group boundary:
Composite workflow with explicit response-to-request value binding across the same lifecycle resource.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target aggregate exists with the lifecycle state required by the transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation loads state, applies domain validation, and mutates the relevant aggregate or child resource.
- State after: The aggregate or child lifecycle resource is updated according to the transition.
- Invalid or blocked transitions: caller is not the configured system user

Required execution workflow:
1. Use function `list unjournaled agreements` (`GET /internal/avtaler`) with system issuer caller accepted by `validerSystembruker` to obtain agreement version ids requiring journalføring.
2. Use function `mark agreement versions as journaled` (`PUT /internal/avtaler`) with body map from `avtaleVersjonId` UUIDs returned by `list unjournaled agreements` to upstream `journalpostId` strings; same system caller to persist journal post ids on those versions.

Optional verification workflow:
1. Use function `list unjournaled agreements` (`GET /internal/avtaler`) with system issuer caller accepted by `validerSystembruker` to verify processed versions are no longer returned.

Existing-state shortcuts:
- If the response value consumed by the later step is already known and still belongs to the same scope, the discovery step can be skipped; direct database setup must preserve the same ownership and lifecycle state.

Parameter and value bindings:
- Agreement version UUIDs from export are reused as keys in the journal completion body, and upstream `journalpostId` values are stored on those versions.

Business result:
Stores journal post ids on agreement content versions.

Constraints and invariants:
- `GET /internal/avtaler` returns agreement version ids. `PUT /internal/avtaler` body is a map from those UUIDs to journal post id strings. OpenAPI only says request body is `object`; implementation specifically expects `Map<UUID, String>`.

Failure and exceptional cases:
- Failing function: `list unjournaled agreements`
  - Failure condition: caller is not the configured system user
  - Why it fails: `innloggingService.validerSystembruker` enforces system issuer and configured id.
  - Violated prerequisite or constraint: Use a non-system token.
- Failing function: `mark agreement versions as journaled`
  - Failure condition: caller is not the configured system user
  - Why it fails: The implementation validates the system user before updating.
  - Violated prerequisite or constraint: Use a non-system token.

Implementation notes:
OpenAPI describes a generic object body, while implementation expects `Map<UUID, String>`.

<a id="behavior-67"></a>
### Behavior 67: Selected agreement wage-subsidy recalculation

Business goal:
Recalculates missing wage subsidy totals for each selected agreement.

API group boundary:
Atomic admin or operational behavior. The single function `recalculate wage subsidy` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: caller lacks developer-admin access

Required execution workflow:
1. Use function `recalculate wage subsidy` (`POST /utvikler-admin/reberegn`) with body array of `avtaleId` UUIDs; developer-admin caller to complete the domain transition.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Recalculates missing wage subsidy totals for each selected agreement.

Constraints and invariants:
- `POST /avtaler` produces `{avtaleId}`. `POST /utvikler-admin/reberegn` body is an array containing `{avtaleId}`. Caller must have developer-admin group access. Agreement must be a subsidy-backed measure and satisfy recalculation preconditions.

Failure and exceptional cases:
- Failing function: `recalculate wage subsidy`
  - Failure condition: caller lacks developer-admin access
  - Why it fails: `AdminController.sjekkTilgang` throws forbidden.
  - Violated prerequisite or constraint: Call without the configured developer group.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-68"></a>
### Behavior 68: Missing reduced-percent date repair

Business goal:
Finds entered permanent wage-subsidy agreements missing reduced-percent data and recalculates reduced values and migrated periods.

API group boundary:
Atomic admin or operational behavior. The single function `fix missing reduced-percent date` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: caller lacks developer-admin access

Required execution workflow:
1. Use function `fix missing reduced-percent date` (`POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}`) with path `migreringsDato` in `yyyy-MM-dd`; developer-admin caller to complete the domain transition.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Finds entered permanent wage-subsidy agreements missing reduced-percent data and recalculates reduced values and migrated periods.

Constraints and invariants:
- `{migreringsDato}` must parse as `yyyy-MM-dd`. Caller must have developer-admin access. The endpoint scans repository state; no specific resource id is required.

Failure and exceptional cases:
- Failing function: `fix missing reduced-percent date`
  - Failure condition: caller lacks developer-admin access
  - Why it fails: Admin group check is enforced before the batch job.
  - Violated prerequisite or constraint: Call without developer-admin group membership.

Implementation notes:
Operational evidence is primarily persisted messages or logs; many endpoints return no structured job result.

<a id="behavior-69"></a>
### Behavior 69: Dry-run missing reduced-percent date fix

Business goal:
Counts/logs permanent wage-subsidy agreements that would be repaired, without saving changes.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `dry-run missing reduced-percent date fix` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target resource exists in the state required by the simulated transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation applies validation or calculation in memory or logs, then avoids repository save.
- State after: No persisted state changes; only a preview or diagnostic is produced.
- Invalid or blocked transitions: caller lacks developer-admin access

Required execution workflow:
1. Use function `dry-run missing reduced-percent date fix` (`POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}`) with path `migreringsDato` in `yyyy-MM-dd`; developer-admin caller to validate and preview the transition without saving it.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Counts/logs permanent wage-subsidy agreements that would be repaired, without saving changes.

Constraints and invariants:
- `{migreringsDato}` must parse as `yyyy-MM-dd`. Caller must have developer-admin access.

Failure and exceptional cases:
- Failing function: `dry-run missing reduced-percent date fix`
  - Failure condition: caller lacks developer-admin access
  - Why it fails: Admin group check is enforced.
  - Violated prerequisite or constraint: Call without developer-admin group membership.

Implementation notes:
This is a non-persistent simulation; it may validate and recalculate but should not save state.

<a id="behavior-70"></a>
### Behavior 70: Admin subsidy-period generation for one agreement

Business goal:
Generates subsidy periods for one agreement after an Arena migration date.

API group boundary:
Atomic admin or operational behavior. The single function `generate subsidy periods for agreement` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: agreement id is unknown

Required execution workflow:
1. Use function `generate subsidy periods for agreement` (`POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}`) with path `avtaleId`; developer-admin caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Generates subsidy periods for one agreement after an Arena migration date.

Constraints and invariants:
- `POST /avtaler` produces `{avtaleId}`. `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}` consumes it and a `yyyy-MM-dd` migration date. Agreement must have sufficient salary/date fields for period generation.

Failure and exceptional cases:
- Failing function: `generate subsidy periods for agreement`
  - Failure condition: agreement id is unknown
  - Why it fails: Repository lookup throws resource-not-found.
  - Violated prerequisite or constraint: Omit agreement creation and use an unknown `{avtaleId}`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-71"></a>
### Behavior 71: Unhandled subsidy-period recalculation

Business goal:
Removes unhandled periods and recreates them from the first unhandled point through agreement end.

API group boundary:
Atomic admin or operational behavior. The single function `recalculate unhandled subsidy periods` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: agreement type is not subsidy-backed

Required execution workflow:
1. Use function `recalculate unhandled subsidy periods` (`POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}`) with path `avtaleId`; developer-admin caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Removes unhandled periods and recreates them from the first unhandled point through agreement end.

Constraints and invariants:
- Agreement must be subsidy-backed and have period state. Caller must have developer-admin access.

Failure and exceptional cases:
- Failing function: `recalculate unhandled subsidy periods`
  - Failure condition: agreement type is not subsidy-backed
  - Why it fails: `krevEnAvTiltakstyper` rejects unsupported measures.
  - Violated prerequisite or constraint: Use `ARBEIDSTRENING`, `INKLUDERINGSTILSKUDD`, or `MENTOR`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-72"></a>
### Behavior 72: Subsidy-period date-order diagnostic

Business goal:
Scans temporary wage-subsidy agreements and logs periods whose start date is earlier than the previous sequence number’s start date.

API group boundary:
Atomic admin or operational behavior. The single function `find subsidy period date-order problems` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: caller lacks developer-admin access

Required execution workflow:
1. Use function `find subsidy period date-order problems` (`POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer`) with authenticated caller and endpoint-specific required request values to complete the domain transition.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Scans temporary wage-subsidy agreements and logs periods whose start date is earlier than the previous sequence number’s start date.

Constraints and invariants:
- Caller must have developer-admin access. The endpoint is diagnostic and returns no domain object.

Failure and exceptional cases:
- Failing function: `find subsidy period date-order problems`
  - Failure condition: caller lacks developer-admin access
  - Why it fails: Admin group check is enforced.
  - Violated prerequisite or constraint: Call without developer-admin group membership.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-73"></a>
### Behavior 73: Subsidy-period annulment

Business goal:
Marks a subsidy period as annulled and emits an annulment event unless its refund status is expired.

API group boundary:
Atomic admin or operational behavior. The single function `annul subsidy period` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: subsidy period id is unknown

Required execution workflow:
1. Use function `annul subsidy period` (`POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}`) with path `tilskuddsperiodeId` from agreement detail or operational record; developer-admin caller to complete the domain transition.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `tilskuddsperiodeId` must identify a period belonging to the agreement or operational repair scope being changed.

Business result:
Marks a subsidy period as annulled and emits an annulment event unless its refund status is expired.

Constraints and invariants:
- `GET /avtaler/{avtaleId}` returns `tilskuddPeriode[].id`; `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}` consumes that `{tilskuddsperiodeId}`. Caller must have developer-admin access.

Failure and exceptional cases:
- Failing function: `annul subsidy period`
  - Failure condition: subsidy period id is unknown
  - Why it fails: `tilskuddPeriodeRepository.findById` throws resource-not-found.
  - Violated prerequisite or constraint: Use a `{tilskuddsperiodeId}` not obtained from an agreement response.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-74"></a>
### Behavior 74: Annul and resend approved subsidy period

Business goal:
Annuls an existing subsidy period and creates a replacement period with approved status, reusing approval metadata.

API group boundary:
Atomic admin or operational behavior. The single function `annul and resend approved subsidy period` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: subsidy period id is unknown

Required execution workflow:
1. Use function `annul and resend approved subsidy period` (`POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}`) with path `tilskuddsperiodeId` from agreement detail or operational record; developer-admin caller to complete the domain transition.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `tilskuddsperiodeId` must identify a period belonging to the agreement or operational repair scope being changed.

Business result:
Annuls an existing subsidy period and creates a replacement period with approved status, reusing approval metadata.

Constraints and invariants:
- `GET /avtaler/{avtaleId}` returns `{tilskuddsperiodeId}`. `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}` consumes it. Caller must have developer-admin access.

Failure and exceptional cases:
- Failing function: `annul and resend approved subsidy period`
  - Failure condition: subsidy period id is unknown
  - Why it fails: Repository lookup fails before annulment/replacement.
  - Violated prerequisite or constraint: Use an unknown `{tilskuddsperiodeId}`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-75"></a>
### Behavior 75: Annul and generate unhandled subsidy period

Business goal:
Annuls an existing subsidy period and creates a replacement with unhandled status.

API group boundary:
Atomic admin or operational behavior. The single function `annul and generate unhandled subsidy period` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: agreement measure is not subsidy-backed

Required execution workflow:
1. Use function `annul and generate unhandled subsidy period` (`POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}`) with path `tilskuddsperiodeId` from agreement detail or operational record; developer-admin caller to complete the domain transition.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `tilskuddsperiodeId` must identify a period belonging to the agreement or operational repair scope being changed.

Business result:
Annuls an existing subsidy period and creates a replacement with unhandled status.

Constraints and invariants:
- `GET /avtaler/{avtaleId}` returns `{tilskuddsperiodeId}`. `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}` consumes it. Caller must have developer-admin access.

Failure and exceptional cases:
- Failing function: `annul and generate unhandled subsidy period`
  - Failure condition: agreement measure is not subsidy-backed
  - Why it fails: Replacement generation requires one of the subsidy-backed measure types.
  - Violated prerequisite or constraint: Use a subsidy period associated with an unsupported measure type, if such invalid state exists.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-76"></a>
### Behavior 76: Annul and generate Arena-treated periods

Business goal:
Annuls all subsidy periods on an agreement ending before `{dato}` and creates replacements with `BEHANDLET_I_ARENA` status.

API group boundary:
Atomic admin or operational behavior. The single function `annul and generate Arena-treated periods` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: agreement id is unknown

Required execution workflow:
1. Use function `annul and generate Arena-treated periods` (`POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}`) with path `avtaleId`; path `dato` in `yyyy-MM-dd`; developer-admin caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Annuls all subsidy periods on an agreement ending before `{dato}` and creates replacements with `BEHANDLET_I_ARENA` status.

Constraints and invariants:
- `POST /avtaler` produces `{avtaleId}`. `{dato}` must parse as `yyyy-MM-dd`. Caller must have developer-admin access.

Failure and exceptional cases:
- Failing function: `annul and generate Arena-treated periods`
  - Failure condition: agreement id is unknown
  - Why it fails: Repository lookup by `{avtaleId}` throws resource-not-found.
  - Violated prerequisite or constraint: Use an unknown `{avtaleId}`.

Implementation notes:
Implementation logic, not OpenAPI alone, determines the practical state transition and validation behavior.

<a id="behavior-77"></a>
### Behavior 77: Selected data warehouse patching

Business goal:
Creates DVH patch message entities for selected agreement ids found in the repository.

API group boundary:
Atomic admin or operational behavior. The single function `patch selected data warehouse messages` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: caller lacks DVH patch group access

Required execution workflow:
1. Use function `patch selected data warehouse messages` (`POST /utvikler-admin/dvh-melding/patch`) with body object with `avtaleIder` array; DVH patch group caller to complete the domain transition.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Creates DVH patch message entities for selected agreement ids found in the repository.

Constraints and invariants:
- `POST /avtaler` produces `{avtaleId}`. `POST /utvikler-admin/dvh-melding/patch` body is `{"avtaleIder":[ "{avtaleId}" ]}`. Caller must have the configured DVH patch group. Unknown ids are ignored by `findAllById`.

Failure and exceptional cases:
- Failing function: `patch selected data warehouse messages`
  - Failure condition: caller lacks DVH patch group access
  - Why it fails: `InternalDvhMeldingProdusentController.sjekkTilgang` throws forbidden.
  - Violated prerequisite or constraint: Call without the configured group.

Implementation notes:
Operational evidence is primarily persisted messages or logs; many endpoints return no structured job result.

<a id="behavior-78"></a>
### Behavior 78: All-agreement data warehouse patching

Business goal:
Creates DVH patch messages for all agreements.

API group boundary:
Atomic admin or operational behavior. The single function `patch all data warehouse messages` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: caller lacks DVH patch group access

Required execution workflow:
1. Use function `patch all data warehouse messages` (`POST /utvikler-admin/dvh-melding/patchalleavtaler`) with authenticated caller and endpoint-specific required request values to complete the domain transition.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Creates DVH patch messages for all agreements.

Constraints and invariants:
- Caller must have the configured DVH patch group. No specific agreement setup endpoint is required for the endpoint to run.

Failure and exceptional cases:
- Failing function: `patch all data warehouse messages`
  - Failure condition: caller lacks DVH patch group access
  - Why it fails: The controller enforces group access.
  - Violated prerequisite or constraint: Call without the configured group.

Implementation notes:
Operational evidence is primarily persisted messages or logs; many endpoints return no structured job result.

<a id="behavior-79"></a>
### Behavior 79: Single agreement event publication

Business goal:
Sends an agreement event message for one existing agreement.

API group boundary:
Atomic admin or operational behavior. The single function `send event message for one agreement` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: agreement id is unknown

Required execution workflow:
1. Use function `send event message for one agreement` (`POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}`) with path `avtaleId`; developer-admin caller to complete the domain transition.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId`; authenticated caller with required role and access to inspect the resulting agreement state.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- `avtaleId` scopes all agreement and child-resource operations to the same aggregate.

Business result:
Sends an agreement event message for one existing agreement.

Constraints and invariants:
- `POST /avtaler` produces `{avtaleId}`. `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}` consumes it. Implementation loads the agreement and sends through `AvtaleHendelseService`.

Failure and exceptional cases:
- Failing function: `send event message for one agreement`
  - Failure condition: agreement id is unknown
  - Why it fails: Repository lookup throws resource-not-found.
  - Violated prerequisite or constraint: Omit agreement creation and use an unknown `{avtaleId}`.

Implementation notes:
Implementation does not call the same developer group check as the all-agreement event endpoints.

<a id="behavior-80"></a>
### Behavior 80: All-agreement event publication

Business goal:
Sends agreement event messages for all agreements.

API group boundary:
Atomic admin or operational behavior. The single function `send event messages for all agreements` performs the exposed maintenance operation.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: Operational candidate state exists in agreements, periods, message rows, or external job context.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The service loads matching records and mutates, logs, or emits messages according to the operation.
- State after: Matching records are repaired, messages are created, or diagnostics are logged according to the operation.
- Invalid or blocked transitions: caller lacks developer-admin access

Required execution workflow:
1. Use function `send event messages for all agreements` (`POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler`) with authenticated caller and endpoint-specific required request values to complete the domain transition.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Sends agreement event messages for all agreements.

Constraints and invariants:
- Caller must have developer-admin group access.

Failure and exceptional cases:
- Failing function: `send event messages for all agreements`
  - Failure condition: caller lacks developer-admin access
  - Why it fails: `AvtaleHendelseController.sjekkTilgang` throws forbidden.
  - Violated prerequisite or constraint: Call without developer-admin group membership.

Implementation notes:
Operational evidence is primarily persisted messages or logs; many endpoints return no structured job result.

<a id="behavior-81"></a>
### Behavior 81: All-agreement event publication dry-run

Business goal:
Performs the all-agreement event-message operation in dry-run mode.

API group boundary:
Atomic state transition on the agreement aggregate or a child lifecycle resource. The single function `dry-run event messages for all agreements` is the transition.

Domain context:
This behavior is meaningful because it changes, retrieves, or operationally repairs a concrete lifecycle state in the agreement, notification, journal, code-list, feature, or integration domain. It is not grouped merely by controller name.

Starting point:
Existing service state

State transition summary:
- State before: The target resource exists in the state required by the simulated transition.
- Transition trigger: The caller invokes the endpoint with the required role, ids, body values, and upstream state.
- Intermediate states: The implementation applies validation or calculation in memory or logs, then avoids repository save.
- State after: No persisted state changes; only a preview or diagnostic is produced.
- Invalid or blocked transitions: caller lacks developer-admin access

Required execution workflow:
1. Use function `dry-run event messages for all agreements` (`POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler`) with authenticated caller and endpoint-specific required request values to validate and preview the transition without saving it.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent resource state already exists, setup can be skipped. Direct database setup may establish the same aggregate, child rows, approval flags, journal state, notification state, or operational candidates, but ids and caller scope must still match the endpoint.

Parameter and value bindings:
- Path, query, body, header, and caller-context values bind the operation to the visible resource scope.

Business result:
Performs the all-agreement event-message operation in dry-run mode.

Constraints and invariants:
- Caller must have developer-admin group access. Implementation delegates to the dry-run service method.

Failure and exceptional cases:
- Failing function: `dry-run event messages for all agreements`
  - Failure condition: caller lacks developer-admin access
  - Why it fails: Developer group access is checked before the service call.
  - Violated prerequisite or constraint: Call without developer-admin group membership.

Implementation notes:
This is a non-persistent simulation; it may validate and recalculate but should not save state. Operational evidence is primarily persisted messages or logs; many endpoints return no structured job result.


## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Idempotent after-registration set and clear

Priority:
Critical domain gap

Expected business goal:
A decision-maker should be able to explicitly set after-registration eligibility to true or false without first reading and trusting the current toggle state.

Why it is unsupported:
Only a toggle operation exists, so the same request can create or remove eligibility. No available function accepts a desired boolean state.

Existing functions considered:
- `mark agreement eligible for after-registration`: can turn the flag on only when the caller knows it is currently false; the endpoint itself does not enforce the desired target state.
- `remove after-registration eligibility`: can turn the flag off only when the caller knows it is currently true; it is the same toggle endpoint.
- `retrieve agreement by id`: can inspect the current flag but cannot make the following toggle idempotent under concurrent changes.

Missing capability:
A state-setting endpoint or request body with the desired boolean value, with optimistic concurrency or compare-and-set semantics.

Proof that function composition is insufficient:
Reading and then toggling is not equivalent to setting a desired state because another caller can change the flag between the read and toggle. Repeating the same command reverses the intended state.

Evidence from existing functions/source:
- `mark agreement eligible for after-registration` and `remove after-registration eligibility` both map to `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`; implementation toggles the current boolean value.

Business impact:
Clients cannot safely retry or enforce a target after-registration state.

### Missing Behavior 2: Restore or audit soft-deleted agreements

Priority:
Important robustness gap

Expected business goal:
Administrators should be able to list, inspect, and restore agreements hidden by delete marking.

Why it is unsupported:
The API can mark an agreement as deleted but exposes no restore endpoint and ordinary access filtering hides delete-marked agreements.

Existing functions considered:
- `soft-delete agreement`: sets `slettemerket=true` and registers an event.
- `retrieve agreement by id`: uses access checks that reject delete-marked agreements.
- `list accessible agreements`: filters delete-marked agreements out through access logic.

Missing capability:
A privileged list/retrieve/restore endpoint for `slettemerket` agreements, plus audit metadata for who hid and restored the agreement.

Proof that function composition is insufficient:
Once `slettemerket=true`, normal retrieval and listing are designed not to expose the agreement. No available function clears the flag. Direct database repair is not equivalent to an audited restore workflow.

Evidence from existing functions/source:
- `soft-delete agreement` delegates to `Avtale.slettemerk`; `Avtalepart.harTilgang` returns false for delete-marked agreements.

Business impact:
Accidental delete marking cannot be recovered through the API, and support staff lack an API-realizable audit/restore workflow.

### Missing Behavior 3: Direct subsidy-period lookup by period id

Priority:
Important robustness gap

Expected business goal:
An operator with a `tilskuddsperiodeId` from a payment, refund, or admin log should be able to retrieve the period, its parent agreement, and current status before repair.

Why it is unsupported:
Admin repair endpoints consume `tilskuddsperiodeId`, but no public or internal read endpoint accepts a subsidy-period id.

Existing functions considered:
- `retrieve agreement by id`: returns periods only when the caller already knows the parent `avtaleId`.
- `annul subsidy period`: mutates a period by id but does not return the period first.
- `annul and resend approved subsidy period`: repairs by id but does not provide discovery.
- `annul and generate unhandled subsidy period`: repairs by id but does not provide discovery.

Missing capability:
A read endpoint such as `GET /tilskuddsperioder/{tilskuddsperiodeId}` or an admin lookup returning parent agreement id, active flag, status, refund status, and decision metadata.

Proof that function composition is insufficient:
Without the parent agreement id, agreement retrieval cannot discover the period. Calling a repair endpoint as a probe is unsafe because it mutates state.

Evidence from existing functions/source:
- `annul subsidy period`, `annul and resend approved subsidy period`, and `annul and generate unhandled subsidy period` call `tilskuddPeriodeRepository.findById`, but there is no corresponding read function in `full-behavior.md`.

Business impact:
Operational repairs are harder to verify and can become unsafe when operators only have downstream period ids.

### Missing Behavior 4: Optimistic concurrency for post-approval changes

Priority:
Important robustness gap

Expected business goal:
Post-approval changes such as shortening, extending, contact updates, and calculation changes should reject stale client state.

Why it is unsupported:
Some endpoints accept an `If-Unmodified-Since` header in the controller signature, but the domain transition does not consume it; other post-approval change endpoints do not expose a concurrency guard at all.

Existing functions considered:
- `shorten agreement`: accepts the header but does not pass it to the domain method.
- `extend agreement`: accepts the header but does not pass it to the domain method.
- `change subsidy calculation`: mutates approved state without an `If-Unmodified-Since` guard.
- `change contact information`: mutates approved state without an `If-Unmodified-Since` guard.

Missing capability:
Consistent optimistic concurrency enforcement for every agreement mutation that creates a new approved version or changes period state.

Proof that function composition is insufficient:
A client can retrieve current `sistEndret`, but the later mutation will not necessarily compare it. Re-reading before mutation does not protect against concurrent changes between calls.

Evidence from existing functions/source:
- `update agreement` and approval functions call `sjekkSistEndret`; `shorten agreement` and `extend agreement` controller methods receive the header but invoke domain methods without using it.

Business impact:
Concurrent post-approval edits can overwrite or compound each other without a stale-write rejection.

### Missing Behavior 5: Per-item result reporting for bulk operational jobs

Priority:
API ergonomics gap

Expected business goal:
Admin and integration operators should receive which records were changed, skipped, failed, or queued.

Why it is unsupported:
Several bulk endpoints return void and rely on logs. Selected DVH patching silently ignores unknown ids, and async event publication returns before completion.

Existing functions considered:
- `patch selected data warehouse messages`: creates messages for ids found by `findAllById`; unknown ids are not reported.
- `send event messages for all agreements`: runs asynchronously and returns no job id or summary.
- `dry-run event messages for all agreements`: logs counts but returns no response body.
- `fix missing reduced-percent date`: logs counts but returns no structured summary.

Missing capability:
Structured job responses or job-status resources with counts and per-id outcomes.

Proof that function composition is insufficient:
No available read endpoint exposes a specific admin job result or correlates input ids to output message rows. Logs are not an API contract and cannot be reliably consumed by clients.

Evidence from existing functions/source:
- `patch selected data warehouse messages` iterates over found ids from the request; `send event messages for all agreements` delegates to an async service method.

Business impact:
Operators cannot distinguish complete success, skipped ids, delayed async execution, or partial operational failure from the HTTP response alone.


## Cross-Behavior Observations

- Agreement state is mostly derived from timestamps and flags on `AvtaleInnhold` and `Avtale`: party approval timestamps, advisor approval, decision-maker approval, `avtaleInngått`, annulment, interruption, soft deletion, and generated period status.
- The same endpoint can represent different state transitions based on `innlogget-part`; the generic approval endpoint is split into participant, employer, advisor, and mentor behaviors.
- Generated id reuse is central: `Location` yields `avtaleId`, saved search returns `sokId`, notification lists return `varselId`, agreement detail exposes `tilskuddPeriode[].id`, and journal export returns agreement-version ids.
- Post-approval edit functions usually create a new approved `AvtaleInnhold` version and call `sendTilbakeTilBeslutter`, which deactivates active rejected periods and creates new unhandled periods.
- Dry-run endpoints run substantial domain logic but avoid repository save; they are previews, not independent state transitions.
- Several admin functions are operationally meaningful but return no structured result. Logs are often the only evidence of skipped or counted records.
- Access control is uneven: most agreement user functions use role-specific `Avtalepart` checks, while `send event message for one agreement` loads by id without the developer group check used by the all-agreement event endpoints.
- Implementation/OpenAPI discrepancies include `innlogget-part` being effectively required for `get logged-in user`, the journal completion body being a `Map<UUID,String>` despite a generic object schema, and source-level `TilskuddsperiodeAdminController` endpoints existing outside the functions extracted from `full-behavior.md`.
- Soft deletion is not a true delete; it hides the agreement from normal access checks and has no API restore path.
- Some endpoints accept concurrency headers without enforcing them in the domain method, especially shortening and extension.

## Coverage Summary

Fully supported workflow/state areas include agreement creation variants, role-scoped reads, saved search replay, core approval transitions, subsidy-period decision transitions, post-approval edits, notification read marking, journal export/completion, and several admin repair/publication operations.

Partially supported areas include after-registration toggling, subsidy-period admin repair, data warehouse/event bulk publication, and post-approval mutation concurrency because the API exposes useful operations but lacks idempotent state setting, direct lookup, structured results, or consistent stale-write protection.

Unsupported or unsafe areas include restoring soft-deleted agreements, directly looking up subsidy periods by id, safely retrying after-registration state changes, and obtaining per-record outcomes for operational batch jobs.
