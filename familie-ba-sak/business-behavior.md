# Domain-Level Workflow and State Behavior Analysis

| No. | Behavior name | Business goal |
|---:|---|---|
| 1 | [Behavior 1: Establish a case and reuse the case identity](#behavior-1) | Create the domain parent case for later treatments, letters, complaints, repayment checks, and statistics. |
| 2 | [Behavior 2: Inspect case views and locate cases by person](#behavior-2) | Read case state by known `fagsakId` and by person/type lookup. |
| 3 | [Behavior 3: Resolve case participation and ongoing benefit relationships](#behavior-3) | Determine where a person participates in child-benefit cases and whether the person has ongoing benefit. |
| 4 | [Behavior 4: Create and check manual repayment treatment on a case](#behavior-4) | Create a repayment treatment for a case and inspect whether open repayment state exists. |
| 5 | [Behavior 5: Create a treatment and restart an early active treatment](#behavior-5) | Start a treatment on a case, then reuse and reset it when another creation request arrives before decision stage. |
| 6 | [Behavior 6: Queue automated birth-event treatment processing](#behavior-6) | Accept a birth event and queue asynchronous treatment processing. |
| 7 | [Behavior 7: Change treatment theme](#behavior-7) | Change treatment category and subcategory while treatment is editable. |
| 8 | [Behavior 8: Execute the caseworker treatment step flow through decision](#behavior-8) | Move a treatment from application registration through result, repayment assessment, decision-maker handoff, and decision implementation. |
| 9 | [Behavior 9: Dismiss an active treatment](#behavior-9) | Close a treatment by dismissal rather than full decision implementation. |
| 10 | [Behavior 10: Register institution and guardian information on treatment](#behavior-10) | Store institution and/or guardian information required by the treatment flow. |
| 11 | [Behavior 11: Add a child to treatment basis and reset later treatment steps](#behavior-11) | Add a child into the treatment person basis after treatment creation. |
| 12 | [Behavior 12: Put treatment on wait, update wait, and resume](#behavior-12) | Pause a treatment with a deadline/reason, change the wait metadata, then resume the treatment. |
| 13 | [Behavior 13: Read person information for case handling](#behavior-13) | Retrieve detailed, simple, and address-focused person information for casework. |
| 14 | [Behavior 14: Refresh treatment register basis and manually record death](#behavior-14) | Update treatment person basis from register information and record manual death details for a person in the treatment. |
| 15 | [Behavior 15: Maintain condition assessment records](#behavior-15) | Add, update, delete, and supplement condition assessment data on an editable treatment. |
| 16 | [Behavior 16: Maintain EEA competence intervals](#behavior-16) | Create/replace competence periods and delete an existing competence interval. |
| 17 | [Behavior 17: Maintain foreign period amounts](#behavior-17) | Update and delete an existing foreign benefit amount period. |
| 18 | [Behavior 18: Update existing currency rate from ECB](#behavior-18) | Update an existing currency-rate period by fetching a rate from ECB when currency/date changes. |
| 19 | [Behavior 19: Set historical ISK currency rate manually and delete currency rate](#behavior-19) | Store a manually supplied historical ISK rate, then remove the currency-rate row. |
| 20 | [Behavior 20: Maintain changed payment shares and reset treatment result](#behavior-20) | Create, fill, remove, and explicitly reset changed-payment share state in an editable treatment. |
| 21 | [Behavior 21: Inspect EEA timelines](#behavior-21) | Read calculated EEA timelines for a treatment. |
| 22 | [Behavior 22: Maintain EEA refund periods](#behavior-22) | Add, list, update, and delete refund periods for EEA handling. |
| 23 | [Behavior 23: Maintain overpaid currency periods](#behavior-23) | Add, list, update, and delete periods with overpaid currency amount. |
| 24 | [Behavior 24: Activate and deactivate corrected decision metadata](#behavior-24) | Mark a treatment as having corrected decision metadata, then deactivate that metadata. |
| 25 | [Behavior 25: Activate, list, and deactivate corrected after-payment metadata](#behavior-25) | Mark corrected after-payment metadata active, inspect all records, and deactivate the active correction. |
| 26 | [Behavior 26: Add and remove small child supplement correction](#behavior-26) | Add a small-child supplement correction for a month and remove it later. |
| 27 | [Behavior 27: Preview repayment warning letter](#behavior-27) | Generate a repayment warning letter preview without sending it. |
| 28 | [Behavior 28: Generate and retrieve decision letter](#behavior-28) | Generate the persisted decision letter for an active decision and retrieve it. |
| 29 | [Behavior 29: Preview and send manual treatment letter](#behavior-29) | Preview and send a manual letter tied to a treatment. |
| 30 | [Behavior 30: Preview and send manual case letter](#behavior-30) | Preview and send a manual letter tied directly to a case. |
| 31 | [Behavior 31: Maintain manual letter recipients](#behavior-31) | Add, list, and remove manual letter recipients for a treatment. |
| 32 | [Behavior 32: Edit decision periods and regenerate letter explanations](#behavior-32) | Modify decision-period explanations, override change date, and generate final letter explanation texts. |
| 33 | [Behavior 33: Retrieve treatment log](#behavior-33) | Read audit/log entries for a treatment. |
| 34 | [Behavior 34: Retrieve external benefit data for BISYS](#behavior-34) | Provide BISYS with extended child benefit and small child supplement periods for a person. |
| 35 | [Behavior 35: Retrieve pension child benefit](#behavior-35) | Provide Pension with child-benefit case and period data for one person. |
| 36 | [Behavior 36: Order pension yearly export](#behavior-36) | Queue export of persons with child benefit for a Pension tax/reporting year. |
| 37 | [Behavior 37: Production tax data export](#behavior-37) | Return Skatteetaten production person and period data for one tax year. |
| 38 | [Behavior 38: Tax test endpoint data retrieval](#behavior-38) | Return Skatteetaten test-path person and period data for one tax year. |
| 39 | [Behavior 39: Retrieve Infotrygd case and benefit context](#behavior-39) | Read legacy Infotrygd case, benefit, and ongoing-state information for an applicant. |
| 40 | [Behavior 40: Discover collaborators by search and organization number](#behavior-40) | Find collaborator/institution information and retrieve details by organization number. |
| 41 | [Behavior 41: Create and list complaint treatments for a case](#behavior-41) | Start a complaint treatment for a child-benefit case and list complaint treatments. |
| 42 | [Behavior 42: Let complaint system create a revision after precheck](#behavior-42) | Let the external complaint system check and create a complaint-triggered revision on a case. |
| 43 | [Behavior 43: Retrieve decisions for complaint system](#behavior-43) | Provide fagsystem decisions for a case to the complaint system. |
| 44 | [Behavior 44: Search external tasks](#behavior-44) | Find external task ids that can be acted on by later task workflows. |
| 45 | [Behavior 45: Assign external task](#behavior-45) | Assign a known task to a caseworker. |
| 46 | [Behavior 46: Reset external task assignment](#behavior-46) | Clear assignment on a known external task. |
| 47 | [Behavior 47: Retrieve journaling task data](#behavior-47) | Gather task, person, minimal case, and optional journalpost context for manual journaling. |
| 48 | [Behavior 48: Complete external task](#behavior-48) | Close a known external task without linking a journalpost. |
| 49 | [Behavior 49: Complete task while linking a journalpost](#behavior-49) | Link a journalpost to a case/treatment context and complete the related task. |
| 50 | [Behavior 50: Retrieve open extended-benefit deadlines](#behavior-50) | Report deadlines for open extended child-benefit treatments. |
| 51 | [Behavior 51: Clear application task ownership](#behavior-51) | Remove `behandlesAvApplikasjon` ownership markers from selected tasks. |
| 52 | [Behavior 52: Inspect journalpost and retrieve documents](#behavior-52) | Read journalpost metadata and fetch documents in resource/PDF form. |
| 53 | [Behavior 53: Journal an incoming journalpost](#behavior-53) | Journal an incoming journalpost to the correct unit/task context. |
| 54 | [Behavior 54: Retrieve feature toggles](#behavior-54) | Return enabled/disabled state for requested feature toggles. |
| 55 | [Behavior 55: Check person access](#behavior-55) | Determine whether the current caller may access a person and see the person’s discretion code. |
| 56 | [Behavior 56: Queue identity event handling](#behavior-56) | Create asynchronous work for a new identity/PDL identity event. |
| 57 | [Behavior 57: Queue transitional-benefit event handling](#behavior-57) | Create asynchronous work for a transitional-benefit decision event. |
| 58 | [Behavior 58: Check rate-change eligibility for one case](#behavior-58) | Determine whether a case can undergo manual rate change. |
| 59 | [Behavior 59: Queue rate change for one case](#behavior-59) | Queue rate-change processing for a single case. |
| 60 | [Behavior 60: Queue rate change for multiple cases](#behavior-60) | Queue rate-change processing for a supplied set of cases. |
| 61 | [Behavior 61: Run synchronous rate change for one case](#behavior-61) | Execute rate change immediately for one eligible case. |
| 62 | [Behavior 62: Queue rate change from identities](#behavior-62) | Discover relevant cases from supplied identities and queue rate-change tasks. |
| 63 | [Behavior 63: Queue technical dismissal for long-deadline treatments](#behavior-63) | Create dismissal tasks for treatments with deadlines beyond a validation date. |
| 64 | [Behavior 64: Identify ongoing cases without latest rate](#behavior-64) | Start background discovery of ongoing cases missing the latest rate. |
| 65 | [Behavior 65: Run consistency reconciliation dry run](#behavior-65) | Queue economy consistency reconciliation without sending to the economy system. |
| 66 | [Behavior 66: Run real consistency reconciliation](#behavior-66) | Queue economy consistency reconciliation that sends to the economy system. |
| 67 | [Behavior 67: Retrieve internal and application statistics](#behavior-67) | Read aggregate service statistics and application counts. |
| 68 | [Behavior 68: Retrieve treatment statistics payload](#behavior-68) | Map one treatment to a DVH treatment statistics payload. |
| 69 | [Behavior 69: Retrieve case statistics payload](#behavior-69) | Map one case to a DVH case statistics payload. |
| 70 | [Behavior 70: Register statistics message as sent](#behavior-70) | Persist that an externally sent statistics message has been sent and should not be resent. |
| 71 | [Behavior 71: Retrieve benefit statistics decisions](#behavior-71) | Map treatment ids to DVH V2 benefit-statistics decision payloads. |
| 72 | [Behavior 72: Queue unsent benefit statistics](#behavior-72) | Queue publication tasks for supplied treatments that have not already been sent. |
| 73 | [Behavior 73: Manually queue benefit statistics](#behavior-73) | Queue benefit-statistics publication for supplied treatments without the normal sent-state filter. |
| 74 | [Behavior 74: Resend manual migration statistics](#behavior-74) | Backfill benefit statistics for eligible manual migration treatments. |
| 75 | [Behavior 75: Complete an administrative task list with partial success](#behavior-75) | Attempt to complete a list of tasks administratively and report failures. |
| 76 | [Behavior 76: Restart small child supplement job](#behavior-76) | Trigger manual restart logic for small child supplement processing. |
| 77 | [Behavior 77: Send payment orders administratively](#behavior-77) | Generate and send payment orders to the economy system for supplied treatments. |
| 78 | [Behavior 78: Bulk corrected payment-order resend](#behavior-78) | Generate and implement corrected payment orders for a list of treatments. |
| 79 | [Behavior 79: Single-version corrected payment-order resend](#behavior-79) | Generate and implement a corrected payment order for one treatment and version. |
| 80 | [Behavior 80: Run unvalidated rate change administratively](#behavior-80) | Run simplified rate change for supplied cases without normal validation. |
| 81 | [Behavior 81: Identify payments over 100 percent](#behavior-81) | Start background analysis for payments exceeding 100 percent. |
| 82 | [Behavior 82: Find payment-order issue candidates](#behavior-82) | Identify treatments with potentially incorrect payment orders. |
| 83 | [Behavior 83: Check incorrect cessation dates for selected treatments](#behavior-83) | Validate payment-order cessation dates for supplied treatment ids. |
| 84 | [Behavior 84: Populate support dates for one treatment](#behavior-84) | Populate support-from/support-to dates for one treatment. |
| 85 | [Behavior 85: Populate support dates in bulk](#behavior-85) | Populate support end dates for multiple active treatments up to a limit. |
| 86 | [Behavior 86: Find cases to close](#behavior-86) | Discover cases that should be closed because they have no ongoing entitlement. |
| 87 | [Behavior 87: Update case ongoing status](#behavior-87) | Bulk update ongoing/closed status on cases according to service rules. |
| 88 | [Behavior 88: Find migration duplicates with ongoing Infotrygd case](#behavior-88) | Identify open cases with multiple migration treatments and an ongoing Infotrygd case. |
| 89 | [Behavior 89: Find migration duplicates](#behavior-89) | Identify open cases with multiple migration treatments. |
| 90 | [Behavior 90: Fill empty condition start dates in preprod](#behavior-90) | Mutate preprod/local test data by filling missing condition start dates from birth dates. |

## Domain Summary
The service manages Norwegian child-benefit case handling. The main aggregate roots are `fagsakId` for child-benefit cases, `behandlingId` for treatment workflows, `vedtakId` and `vedtaksperiodeId` for decisions and decision periods, `oppgaveId` for external tasks, and `journalpostId`/`dokumentInfoId` for journal documents.

The strongest state machines are the `fagsak` lifecycle, the active `behandling` step flow, wait-state handling, decision/implementation flow, EEA and differential-calculation side records, manual payment adjustments, corrected-decision metadata, letter recipients, external complaint revision, task assignment/completion, journalføring, rate-change tasks, statistics publishing, and administrative repair jobs. Many endpoints are not independent CRUD: they mutate a treatment and deliberately reset later treatment steps, create asynchronous tasks, deactivate previous active metadata, or require ids returned from earlier reads/mutations.

## Supported Business Behaviors

<a id="behavior-1"></a>
### Behavior 1: Establish a case and reuse the case identity
Business goal: Create the domain parent case for later treatments, letters, complaints, repayment checks, and statistics.

API group boundary: The functions share the `fagsak` aggregate and the uniqueness key `personIdent` + `fagsakType` + optional institution organization number.

Domain context: Case creation is idempotent by business key; the returned `fagsakId` is the parent id for most later workflows.

Starting point: `No prior service state`

State transition summary:
- State before: no matching case exists for the uniqueness key.
- Transition trigger: case creation request.
- Intermediate states: actor/person identity is resolved; case and shadow case/statistics side effects may be created.
- State after: one case exists and repeated creation returns that case.
- Invalid or blocked transitions: institution case without organization number fails; missing access to person blocks creation.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with body `personIdent=P1`, `fagsakType=NORMAL` to create a case and capture `RestMinimalFagsak.id=fagsakId`.
2. Use function `return existing case` (`POST /api/fagsaker`) with body `personIdent=P1`, `fagsakType=NORMAL` to return the existing case for the same key.

Optional verification workflow:
1. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) with `fagsakId` from step 1 to inspect treatments and repayment treatments.
2. Use function `retrieve minimal case` (`GET /api/fagsaker/minimal/{fagsakId}`) with the same `fagsakId` to inspect compact case data.

Existing-state shortcuts:
- Step 1 can be skipped when an equivalent case already exists for the same person, case type, and institution organization number.
- Direct database setup can replace creation only when actor, person-ident, case type, institution relation, and case status are consistent.
- The core idempotent return action cannot be skipped when proving duplicate prevention.

Parameter and value bindings:
- `personIdent`, `fagsakType`, and `institusjon.orgNummer` define the uniqueness key.
- `RestMinimalFagsak.id` becomes `fagsakId` for all later case-scoped and treatment-scoped functions.
- Caller must have person access and caseworker permission for creation.

Business result: Exactly one case exists for the business key. Repeating the creation call returns the same domain case rather than creating a duplicate.

Constraints and invariants:
- Institution cases require `institusjon.orgNummer`.
- The service validates person access before creation.
- The implementation returns HTTP 201 even when it returns an existing case.

Failure and exceptional cases:
- Failing function: `create case`
  - Source discriminator: `FagsakService.hentEllerOpprettFagsak institution guard`
  - Failure condition: `fagsakType=INSTITUSJON` is requested without `institusjon.orgNummer`.
  - Why it fails: The owned service cannot form the institution-scoped uniqueness key and throws a functional exception.
  - Violated prerequisite or constraint: An institution case must identify its organization.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/fagsak/FagsakService.kt — FagsakService.hentEllerOpprettFagsak`

Implementation notes: Source and OpenAPI agree on endpoint shape. Implementation creates/updates surrounding identity/statistics/shadow-case state through services, not merely a case row.

<a id="behavior-2"></a>
### Behavior 2: Inspect case views and locate cases by person
Business goal: Read case state by known `fagsakId` and by person/type lookup.

API group boundary: These functions are read-only views over the same `fagsak` aggregate and reuse the case id or person/type key created by case establishment.

Domain context: Caseworkers and integrations need both full and minimal views before routing later treatment or complaint work.

Starting point: `Existing service state`

State transition summary:
- State before: a case exists.
- Transition trigger: read or lookup request.
- Intermediate states: no persistent state change.
- State after: case state is returned.
- Invalid or blocked transitions: missing case/person/type returns failure or access error.

Required execution workflow:
1. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) with `fagsakId=F1` to retrieve the full case.
2. Use function `retrieve minimal case` (`GET /api/fagsaker/minimal/{fagsakId}`) with `fagsakId=F1` to retrieve compact case data.
3. Use function `find minimal case for person` (`POST /api/fagsaker/hent-fagsak-paa-person`) with body `personIdent=P1`, `fagsakType=NORMAL` to locate the case by person and type.
4. Use function `find all minimal cases for person` (`POST /api/fagsaker/hent-fagsaker-paa-person`) with body `personIdent=P1`, `fagsakTyper=[NORMAL]` to locate matching case set.

Optional verification workflow:
None.

Existing-state shortcuts:
- If `fagsakId` and the person/type key are already known from trusted upstream state, no setup API call is needed.
- The `fagsakId` and `personIdent` must still refer to the same case scope when the lookup result is compared with id-based reads.

Parameter and value bindings:
- `fagsakId=F1` is reused for full and minimal reads.
- `personIdent=P1` and `fagsakType=NORMAL` must match the case key.
- The returned minimal case id can be reused as `fagsakId` for later workflows.

Business result: The service returns full and compact views for the same case, and person-based lookup confirms the case can be found through the applicant/type key.

Constraints and invariants:
- Access checks apply to id-based and person-based reads.
- Person lookup resolves actor identity before querying case state.

Failure and exceptional cases:
- Failing function: `find minimal case for person`
  - Source discriminator: `FagsakService branch for no case exists for personIdent=P1 and fagsakType=NORMAL`
  - Failure condition: no case exists for `personIdent=P1` and `fagsakType=NORMAL`.
  - Why it fails: service returns a failure resource when no matching case is found.
  - Violated prerequisite or constraint: required case state does not exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/fagsak/FagsakService.kt — FagsakService`
- Failing function: `retrieve full case`
  - Source discriminator: `FagsakService.hentPåFagsakId not-found outcome`
  - Failure condition: No case exists for `fagsakId`.
  - Why it fails: The aggregate view cannot be built.
  - Violated prerequisite or constraint: The case must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/fagsak/FagsakService.kt — FagsakService`
- Failing function: `retrieve minimal case`
  - Source discriminator: `FagsakService.hentPåFagsakId not-found outcome`
  - Failure condition: No case exists for `fagsakId`.
  - Why it fails: The compact view cannot be built.
  - Violated prerequisite or constraint: The case must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/fagsak/FagsakService.kt — FagsakService`

Implementation notes: These are pure reads. They are often verification steps for broader workflows, but they are also a standalone case-discovery capability.

<a id="behavior-3"></a>
### Behavior 3: Resolve case participation and ongoing benefit relationships
Business goal: Determine where a person participates in child-benefit cases and whether the person has ongoing benefit.

API group boundary: The functions share the person participation/search domain and bind `personIdent` or child identities to case/benefit rows.

Domain context: This workflow supports manual routing, duplicate checks, and assessment of applicant/child relationships.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: service has participant and benefit state for the requested identities.
- Transition trigger: search/resolve request.
- Intermediate states: no persistent state change.
- State after: matching participants, case ids, and benefit-bearing cases are returned.
- Invalid or blocked transitions: unresolvable identifiers or missing access produce failure or empty result.

Required execution workflow:
1. Use function `search case participants` (`POST /api/fagsaker/sok`) with body `personIdent=P1` to search participant records.
2. Use function `resolve case participants` (`POST /api/fagsaker/sok/fagsakdeltagere`) with body `personIdent=P1`, `barnasIdenter=[C1]` to resolve applicant and child participants.
3. Use function `search cases where person participates` (`POST /api/fagsaker/sok/fagsaker-hvor-person-er-deltaker`) with body `personIdent=P1` to return cases where the person participates.
4. Use function `search cases with ongoing benefit for person` (`POST /api/fagsaker/sok/fagsaker-hvor-person-mottar-lopende-ytelse`) with body `personIdent=P1` to return cases with ongoing ordinary or extended benefit.

Optional verification workflow:
1. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) with a `fagsakId` returned by steps 3 or 4 to inspect the case.

Existing-state shortcuts:
- No setup endpoint can guarantee ongoing benefit state by itself; benefit rows usually come from completed treatment/payment state or direct database setup.
- Existing `personIdent` and `barnasIdenter` can be supplied directly when the identity mapping exists upstream.

Parameter and value bindings:
- `personIdent=P1` is reused across all searches.
- `barnasIdenter=[C1]` are resolved to actor ids and bound to participant results.
- Returned `fagsakId` values can be reused for case/treatment workflows.

Business result: The caller receives participant rows and case ids showing applicant/child participation and current benefit relationships.

Constraints and invariants:
- Ongoing-benefit search depends on persisted benefit shares, not just case existence.
- Access checks apply to person-based searches.

Failure and exceptional cases:
- Failing function: `resolve case participants`
  - Source discriminator: `search methods branch for applicant or child identifier cannot be resolved`
  - Failure condition: applicant or child identifier cannot be resolved.
  - Why it fails: controller catches actor/participant lookup failures and returns a failure resource.
  - Violated prerequisite or constraint: identifiers must resolve to actors.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/fagsak/FagsakController.kt — participant/search methods`
- Failing function: `search cases with ongoing benefit for person`
  - Source discriminator: `actor-resolution negative result`
  - Failure condition: The identity cannot be resolved to an actor.
  - Why it fails: The query has no domain subject.
  - Violated prerequisite or constraint: Identity must resolve to an actor.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/fagsak/FagsakController.kt — participant/search methods`

Implementation notes: The participant search endpoint `POST /api/fagsaker/sok` does not explicitly perform the same visible access validation as the two ongoing/participant case-search endpoints in the controller.

<a id="behavior-4"></a>
### Behavior 4: Create and check manual repayment treatment on a case
Business goal: Create a repayment treatment for a case and inspect whether open repayment state exists.

API group boundary: Both functions are scoped by `fagsakId` and interact with repayment treatment state for the case.

Domain context: Repayment processing is a side lifecycle under a child-benefit case.

Starting point: `Existing service state`

State transition summary:
- State before: a case exists and may have no open repayment treatment.
- Transition trigger: manual repayment-treatment creation.
- Intermediate states: repayment integration/service creates a repayment treatment reference.
- State after: the case can report open repayment state.
- Invalid or blocked transitions: missing case or insufficient role blocks creation.

Required execution workflow:
1. Use function `create repayment treatment` (`GET /api/fagsaker/{fagsakId}/opprett-tilbakekreving`) with `fagsakId=F1` to create a repayment treatment.

Optional verification workflow:
1. Use function `check open repayment case` (`GET /api/fagsaker/{fagsakId}/har-apen-tilbakekreving`) with `fagsakId=F1` to inspect open repayment state.
2. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) with `fagsakId=F1` to inspect repayment treatments on the full case view.

Existing-state shortcuts:
- Case creation can be skipped when `fagsakId=F1` already exists.
- Direct database or external repayment setup can replace creation for verification, but the core manual creation action cannot be skipped when testing this behavior.

Parameter and value bindings:
- The same `fagsakId` is reused for creation and verification.
- Caller must have caseworker permission for repayment creation.

Business result: A repayment treatment is created or triggered for the case, and open repayment state can be observed through the case-scoped check.

Constraints and invariants:
- The function uses `GET` for a mutating creation action.
- The check endpoint has no identified business failure beyond missing case/access conditions.

Failure and exceptional cases:
- Failing function: `create repayment treatment`
  - Source discriminator: `TilbakekrevingController branch for fagsakId=F1 does not identify an existing case`
  - Failure condition: `fagsakId=F1` does not identify an existing case.
  - Why it fails: repayment service cannot create repayment treatment without the parent case.
  - Violated prerequisite or constraint: parent `fagsakId` must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/tilbakekreving/TilbakekrevingController.kt — repayment methods`
- Failing function: `create repayment treatment`
  - Source discriminator: `KanBehandlingOpprettesManueltRespons.kanBehandlingOpprettes=false`
  - Failure condition: The domain precheck rejects manual creation.
  - Why it fails: The explicit business rejection is returned.
  - Violated prerequisite or constraint: The case must be eligible.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/tilbakekreving/TilbakekrevingController.kt — repayment methods`
- Failing function: `create repayment treatment`
  - Source discriminator: `kravgrunnlagsreferanse lifecycle outcome`
  - Failure condition: The reference is unknown or belongs to a non-closed repayment treatment.
  - Why it fails: It cannot be reused.
  - Violated prerequisite or constraint: The reference must exist in the required state.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/tilbakekreving/TilbakekrevingController.kt — repayment methods`

Implementation notes: The mutating endpoint is exposed as `GET`, which is an OpenAPI/API semantics mismatch even though source implements the mutation.

<a id="behavior-5"></a>
### Behavior 5: Create a treatment and restart an early active treatment
Business goal: Start a treatment on a case, then reuse and reset it when another creation request arrives before decision stage.

API group boundary: Both functions share the same `POST /api/behandlinger` endpoint and active-treatment state under one `fagsakId`.

Domain context: A case may not have multiple active unfinished treatments. Early active treatments are reset rather than duplicated.

Starting point: `Existing service state`

State transition summary:
- State before: a case exists without an active blocking treatment.
- Transition trigger: treatment creation request.
- Intermediate states: person basis and active decision are initialized; task/statistics side effects may be created.
- State after: one active treatment exists; a repeated request before decision stage resets/reuses it.
- Invalid or blocked transitions: active treatment at or after decision stage blocks new creation.

Required execution workflow:
1. Use function `create treatment` (`POST /api/behandlinger`) with body `fagsakId=F1`, `søkersIdent=P1`, `behandlingType=FØRSTEGANGSBEHANDLING`, `behandlingÅrsak=SØKNAD`, `søknadMottattDato=D1` to create a treatment and capture `behandlingId=B1`.
2. Use function `restart active early treatment` (`POST /api/behandlinger`) with the same body values and `fagsakId=F1` while treatment `B1` is before `BESLUTTE_VEDTAK` to reset and reuse the active treatment.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect current step, person basis, and active decision.

Existing-state shortcuts:
- The case setup can be skipped when a valid `fagsakId` exists for `søkersIdent=P1`.
- Direct database setup can seed an early active treatment, but the restart action must be performed through the core function to prove reset behavior.

Parameter and value bindings:
- `fagsakId=F1` from the case is reused in both requests.
- `søkersIdent=P1` must be the applicant for the case.
- `RestUtvidetBehandling.behandlingId` is reused as `behandlingId` for verification and later workflows.

Business result: One active treatment exists under the case. The second creation request does not create a second active treatment; it resets the existing early treatment to initial step state.

Constraints and invariants:
- First-time and application-driven revision treatments require `søknadMottattDato`.
- Manual migration requires `nyMigreringsdato`.
- Active treatment at or after `BESLUTTE_VEDTAK` blocks creation/restart.

Failure and exceptional cases:
- Failing function: `create treatment`
  - Source discriminator: `BehandlingService.opprettBehandling branch for fagsakId=F404 does not exist`
  - Failure condition: `fagsakId=F404` does not exist.
  - Why it fails: `BehandlingService.opprettBehandling` throws when the case cannot be found.
  - Violated prerequisite or constraint: treatment must belong to an existing case.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/BehandlingService.kt — BehandlingService.opprettBehandling`
- Failing function: `create treatment`
  - Source discriminator: `BehandlingService.opprettBehandling branch for required søkersIdent, søknadMottattDato, or nyMigreringsdato is missing for the chosen type/reason`
  - Failure condition: required `søkersIdent`, `søknadMottattDato`, or `nyMigreringsdato` is missing for the chosen type/reason.
  - Why it fails: `NyBehandling` validates these fields.
  - Violated prerequisite or constraint: request body must satisfy treatment-type requirements.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/BehandlingService.kt — BehandlingService.opprettBehandling`
- Failing function: `restart active early treatment`
  - Source discriminator: `BehandlingService.opprettBehandling branch for active treatment is at or after BESLUTTE_VEDTAK`
  - Failure condition: active treatment is at or after `BESLUTTE_VEDTAK`.
  - Why it fails: implementation throws functional error for active unfinished decision-stage treatment.
  - Violated prerequisite or constraint: only pre-decision active treatments can be reset through creation.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/BehandlingService.kt — BehandlingService.opprettBehandling`
- Failing function: `create treatment`
  - Source discriminator: `BehandlingService revision prerequisite guard`
  - Failure condition: A revision has no prior implemented decision.
  - Why it fails: There is no decision to revise.
  - Violated prerequisite or constraint: A prior decision is required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/BehandlingService.kt — BehandlingService.opprettBehandling`
- Failing function: `create treatment`
  - Source discriminator: `BehandlingService active-Infotrygd guard`
  - Failure condition: An active Infotrygd case blocks local creation.
  - Why it fails: Parallel processing is ineligible.
  - Violated prerequisite or constraint: The case must be locally eligible.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/BehandlingService.kt — BehandlingService.opprettBehandling`
- Failing function: `create treatment`
  - Source discriminator: `BehandlingService active manual-migration guard`
  - Failure condition: An ongoing manual migration already exists.
  - Why it fails: A conflicting lifecycle is rejected.
  - Violated prerequisite or constraint: Only one active migration may exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/BehandlingService.kt — BehandlingService.opprettBehandling`
- Failing function: `create treatment`
  - Source discriminator: `BehandlingService migration-rate prerequisite`
  - Failure condition: No latest applicable migration rate exists.
  - Why it fails: Calculation basis is incomplete.
  - Violated prerequisite or constraint: The applicable rate must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/BehandlingService.kt — BehandlingService.opprettBehandling`
- Failing function: `restart active early treatment`
  - Source discriminator: `BehandlingService restart-step guard`
  - Failure condition: The treatment reached or passed decision.
  - Why it fails: It is too late to reset.
  - Violated prerequisite or constraint: Only an early treatment may restart.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/BehandlingService.kt — BehandlingService.opprettBehandling`

Implementation notes: Treatment creation initializes an active decision and may create a task. For first-time treatments, the service sends start-treatment information to Infotrygd feed.

<a id="behavior-6"></a>
### Behavior 6: Queue automated birth-event treatment processing
Business goal: Accept a birth event and queue asynchronous treatment processing.

API group boundary: The function is an event-ingestion lifecycle resource keyed by mother and child identities, creating a task rather than an immediate treatment.

Domain context: Birth events can automatically create or update child-benefit treatments without a synchronous caseworker flow.

Starting point: `No prior service state`

State transition summary:
- State before: event has not been queued.
- Transition trigger: birth event request.
- Intermediate states: a processing task is created.
- State after: asynchronous task exists; treatment creation may occur later.
- Invalid or blocked transitions: invalid payload or task repository failure returns failure resource.

Required execution workflow:
1. Use function `queue treatment from birth event` (`PUT /api/behandlinger`) with body `morsIdent=P1`, `barnasIdenter=[C1]` to create the birth-event processing task.

Optional verification workflow:
None.

Existing-state shortcuts:
- No case setup is required because later task execution may create or locate the case.
- Direct task-table setup can represent an already queued event, but the core queue action cannot be skipped when validating API behavior.

Parameter and value bindings:
- `morsIdent` and `barnasIdenter` are serialized into the task payload.
- Caller must have system-level permission.

Business result: A task for birth-event handling exists; no immediate treatment id is returned.

Constraints and invariants:
- The endpoint creates a task only.
- The task executor owns later case/treatment creation and retry behavior.

Failure and exceptional cases:
None.

Implementation notes: Lack of returned task id makes later API-level correlation weak.

<a id="behavior-7"></a>
### Behavior 7: Change treatment theme
Business goal: Change treatment category and subcategory while treatment is editable.

API group boundary: The function is a treatment-scoped mutation using `behandlingId`.

Domain context: Theme controls treatment classification and downstream handling.

Starting point: `Existing service state`

State transition summary:
- State before: treatment exists and is editable.
- Transition trigger: theme update request.
- Intermediate states: category/subcategory are persisted as manually updated values.
- State after: treatment has new theme.
- Invalid or blocked transitions: closed, waiting, or non-editable treatment blocks update through editability/access checks.

Required execution workflow:
1. Use function `change treatment theme` (`PUT /api/behandlinger/{behandlingId}/behandlingstema`) with `behandlingId=B1` and body `behandlingKategori=NASJONAL`, `behandlingUnderkategori=ORDINÆR` to update theme.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect the updated theme.

Existing-state shortcuts:
- Treatment creation can be skipped when an editable treatment already exists.
- Direct database state must still represent the same active treatment and caller-access scope.

Parameter and value bindings:
- `behandlingId=B1` scopes the update and verification.
- Body values become persisted treatment category/subcategory.

Business result: Treatment theme values are changed on the existing treatment.

Constraints and invariants:
- Caller must have update access and caseworker role.
- Treatment must pass `validerKanRedigereBehandling`.

Failure and exceptional cases:
- Failing function: `change treatment theme`
  - Source discriminator: `update method branch for treatment is not editable`
  - Failure condition: treatment is not editable.
  - Why it fails: controller validates editability before service update.
  - Violated prerequisite or constraint: only editable active treatments can be changed.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/behandlingstema/BehandlingstemaService.kt — update method`

Implementation notes: The update is direct and does not itself recalculate treatment result.

<a id="behavior-8"></a>
### Behavior 8: Execute the caseworker treatment step flow through decision
Business goal: Move a treatment from application registration through result, repayment assessment, decision-maker handoff, and decision implementation.

API group boundary: All steps share the same `behandlingId` and the treatment step state machine.

Domain context: This is the core manual caseworker lifecycle for a child-benefit treatment.

Starting point: `Existing service state`

State transition summary:
- State before: treatment exists at an early editable step.
- Transition trigger: sequential step execution.
- Intermediate states: application basis, condition validation, derived result, decision periods, repayment assessment, and decision-stage state are persisted.
- State after: decision is recorded and implementation steps are advanced or queued.
- Invalid or blocked transitions: step order violations, waiting status, closed treatment, decision-stage lock, and missing role block execution.

Required execution workflow:
1. Use function `register application` (`POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`) with `behandlingId=B1` and body `RestRegistrerSøknad` to store application data.
2. Use function `validate conditions` (`POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering`) with `behandlingId=B1` to validate condition assessment.
3. Use function `derive treatment result` (`POST /api/behandlinger/{behandlingId}/steg/behandlingsresultat`) with `behandlingId=B1` to derive result and decision periods.
4. Use function `assess repayment` (`POST /api/behandlinger/{behandlingId}/steg/tilbakekreving`) with `behandlingId=B1` and body `RestTilbakekreving` to store repayment assessment.
5. Use function `send to decision maker` (`POST /api/behandlinger/{behandlingId}/steg/send-til-beslutter`) with `behandlingId=B1`, query `behandlendeEnhet=E1` to send the treatment to decision maker.
6. Use function `decide treatment` (`POST /api/behandlinger/{behandlingId}/steg/iverksett-vedtak`) with `behandlingId=B1` and body `beslutning=GODKJENT`, `begrunnelse=null`, `kontrollerteSider=[]` to record the decision.

Optional verification workflow:
1. Use function `validate treatment result` (`GET /api/behandlinger/{behandlingId}/steg/behandlingsresultat/valider`) with `behandlingId=B1` before step 3 to inspect readiness without advancing.
2. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` after step 6 to inspect step/status.
3. Use function `list decision periods` (`GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder`) with `behandlingId=B1` to inspect generated periods.

Existing-state shortcuts:
- Prior setup can be skipped if an editable treatment already exists with valid application and condition basis at the correct step.
- Direct database setup must preserve active treatment, active decision, step-state rows, caller access, and no wait state.
- The core step execution cannot be skipped when validating state transitions.

Parameter and value bindings:
- `behandlingId=B1` is reused across all steps.
- `behandlendeEnhet=E1` is bound to send-to-decision-maker handling.
- `RestBeslutningPåVedtak.beslutning` drives approval or underapproval.
- Caller role changes matter: caseworker role for earlier steps, decision-maker role for decision except special manual-migration logic.

Business result: Treatment has completed the caseworker/decision-maker API-visible flow, with result and decision state persisted and implementation continuation triggered by internal step logic.

Constraints and invariants:
- `StegService` rejects execution while status is `SATT_PÅ_VENT` or `SATT_PÅ_MASKINELL_VENT`.
- A treatment at `BESLUTTE_VEDTAK` is locked for other changes except allowed decision/technical maintenance dismissal.
- Step order is enforced; later caseworker steps cannot be executed before the current step reaches them.
- Automatic decision can occur when `send to decision maker` determines the treatment should be automatically decided.

Failure and exceptional cases:
- Failing function: `register application`
  - Source discriminator: `StegService.utførSteg branch for treatment has advanced beyond application registration and request differs from existing application basis`
  - Failure condition: treatment has advanced beyond application registration and request differs from existing application basis.
  - Why it fails: `StegService` rejects executing an earlier caseworker step than the current step.
  - Violated prerequisite or constraint: required step ordering.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/steg/StegService.kt — StegService.utførSteg`
- Failing function: `validate conditions`
  - Source discriminator: `StegService.utførSteg branch for treatment status is SATT_PÅ_VENT`
  - Failure condition: treatment status is `SATT_PÅ_VENT`.
  - Why it fails: `StegService.validerBehandlingIkkeSattPåVent` rejects step execution.
  - Violated prerequisite or constraint: waiting treatment cannot advance steps.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/steg/StegService.kt — StegService.utførSteg`
- Failing function: `validate conditions`
  - Source discriminator: `VilkårsvurderingValidering required age-18 outcome`
  - Failure condition: The required age-18 condition is absent.
  - Why it fails: Eligibility validation is incomplete.
  - Violated prerequisite or constraint: All required conditions must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/steg/StegService.kt — StegService.utførSteg`
- Failing function: `derive treatment result`
  - Source discriminator: `BehandlingsresultatValidering named outcome`
  - Failure condition: Results or periods are incomplete or inconsistent.
  - Why it fails: The result cannot be finalized.
  - Violated prerequisite or constraint: Results and periods must be consistent.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/steg/StegService.kt — StegService.utførSteg`
- Failing function: `assess repayment`
  - Source discriminator: `TilbakekrevingService strategy-mismatch guard`
  - Failure condition: Assessment conflicts with calculated overpayment strategy.
  - Why it fails: The choice is incompatible.
  - Violated prerequisite or constraint: Assessment must match calculation.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/steg/StegService.kt — StegService.utførSteg`
- Failing function: `send to decision maker`
  - Source discriminator: `AnnenVurderingService unanswered-assessment guard`
  - Failure condition: A required other assessment is unanswered.
  - Why it fails: Decision control cannot start.
  - Violated prerequisite or constraint: All assessments must be answered.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/steg/StegService.kt — StegService.utførSteg`
- Failing function: `decide treatment`
  - Source discriminator: `TotrinnskontrollService control guards`
  - Failure condition: Control is missing or self-approved.
  - Why it fails: Two-step control is invalid.
  - Violated prerequisite or constraint: A distinct controller and complete result are required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/steg/StegService.kt — StegService.utførSteg`
- Failing function: `decide treatment`
  - Source discriminator: `TilkjentYtelseValidering percentage guard`
  - Failure condition: Payment shares exceed 100 percent.
  - Why it fails: Implementation rejects over-allocation.
  - Violated prerequisite or constraint: Total share may not exceed 100 percent.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/steg/StegService.kt — StegService.utførSteg`

Implementation notes: Decision implementation may continue through internal tasks and later non-public steps; this API-visible flow is not a guaranteed synchronous final closure of the treatment.

<a id="behavior-9"></a>
### Behavior 9: Dismiss an active treatment
Business goal: Close a treatment by dismissal rather than full decision implementation.

API group boundary: The function is a treatment-scoped terminal transition using `behandlingId`.

Domain context: Incorrectly created or withdrawn treatments must be closed with an explicit reason.

Starting point: `Existing service state`

State transition summary:
- State before: treatment exists and has not been sent to external services.
- Transition trigger: dismissal request.
- Intermediate states: dismissal reason/result is stored and finish-treatment step is executed.
- State after: treatment is dismissed/finished.
- Invalid or blocked transitions: external sending, disallowed dismissal type, missing feature toggle for technical reasons, or non-editable state blocks dismissal.

Required execution workflow:
1. Use function `dismiss treatment` (`PUT /api/behandlinger/{behandlingId}/steg/henlegg`) with `behandlingId=B1` and body `årsak=FEILAKTIG_OPPRETTET`, `begrunnelse=R1` to dismiss the treatment.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect status/result.

Existing-state shortcuts:
- Treatment setup can be skipped when a dismissible active treatment already exists.
- Direct database setup must keep the treatment unsent to external services and in a dismissal-allowed state.

Parameter and value bindings:
- `behandlingId=B1` scopes the dismissal.
- Body `årsak` maps to dismissal result; `begrunnelse` is stored/logged through step handling.

Business result: The treatment is no longer an ordinary active treatment and carries a dismissal result/reason.

Constraints and invariants:
- Dismissal after external sending is rejected.
- Technical-maintenance dismissal requires the corresponding feature toggle.
- Technical treatment dismissal requires technical-change toggle.

Failure and exceptional cases:
- Failing function: `dismiss treatment`
  - Source discriminator: `dismissal guards branch for treatment has been sent to external services`
  - Failure condition: treatment has been sent to external services.
  - Why it fails: controller calls `validerBehandlingIkkeSendtTilEksterneTjenester`.
  - Violated prerequisite or constraint: externally sent treatments cannot be dismissed through this endpoint.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/common/BehandlingValidering.kt — dismissal guards`
- Failing function: `dismiss treatment`
  - Source discriminator: `dismissal guards branch for årsak=TEKNISK_VEDLIKEHOLD while feature toggle is disabled`
  - Failure condition: `årsak=TEKNISK_VEDLIKEHOLD` while feature toggle is disabled.
  - Why it fails: dismissal-type validation rejects the reason.
  - Violated prerequisite or constraint: feature-gated dismissal reason is not enabled.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/common/BehandlingValidering.kt — dismissal guards`
- Failing function: `dismiss treatment`
  - Source discriminator: `BehandlingValidering external-finality guards`
  - Failure condition: A payment order, repayment instruction, external send, or distributed decision finalized state.
  - Why it fails: Dismissal conflicts with external finality.
  - Violated prerequisite or constraint: No external finality may exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/common/BehandlingValidering.kt — dismissal guards`

Implementation notes: Source finishes the treatment after handling dismissal; any dismissal letter side effect is handled by step services.

<a id="behavior-10"></a>
### Behavior 10: Register institution and guardian information on treatment
Business goal: Store institution and/or guardian information required by the treatment flow.

API group boundary: The function mutates treatment-scoped institution/guardian step state.

Domain context: Institution and guardian information affects representation, routing, and letter recipients.

Starting point: `Existing service state`

State transition summary:
- State before: treatment exists and institution/guardian step data is missing or outdated.
- Transition trigger: registration request.
- Intermediate states: request is converted to institution and guardian domain objects.
- State after: valid institution/guardian information is stored and step advances.
- Invalid or blocked transitions: request converting to neither institution nor guardian returns failure resource.

Required execution workflow:
1. Use function `register institution and guardian` (`POST /api/behandlinger/{behandlingId}/steg/registrer-institusjon-og-verge`) with `behandlingId=B1` and body containing valid institution or guardian fields to persist the step data.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect institution/guardian data.

Existing-state shortcuts:
- Treatment setup can be skipped when `behandlingId=B1` already exists at a compatible step.
- Direct database setup must preserve treatment ownership and editable/step state.

Parameter and value bindings:
- `behandlingId=B1` scopes the institution/guardian records.
- Body values are converted against treatment context, so guardian relationship and institution fields must be valid for the treatment.

Business result: The treatment has persisted institution and/or guardian step data.

Constraints and invariants:
- The controller accepts the request only when conversion yields at least one non-null institution or guardian object.
- The function returns a failure resource rather than throwing for invalid converted data.

Failure and exceptional cases:
- Failing function: `register institution and guardian`
  - Source discriminator: `registration method branch for request contains neither valid institution data nor valid guardian data`
  - Failure condition: request contains neither valid institution data nor valid guardian data.
  - Why it fails: controller returns `Ressurs.failure("Ugydig verge info")`.
  - Violated prerequisite or constraint: registration must provide meaningful institution or guardian information.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/UtvidetBehandlingController.kt — registration method`

Implementation notes: The failure response can be HTTP 200 with failure resource; clients must inspect the resource payload.

<a id="behavior-11"></a>
### Behavior 11: Add a child to treatment basis and reset later treatment steps
Business goal: Add a child into the treatment person basis after treatment creation.

API group boundary: The function mutates person basis for one `behandlingId` and resets downstream treatment state.

Domain context: Missing child basis invalidates later condition/result/payment calculations.

Starting point: `Existing service state`

State transition summary:
- State before: treatment exists without the child in active person basis.
- Transition trigger: add-child request.
- Intermediate states: child identity is resolved and person basis is updated.
- State after: child is part of treatment basis and later steps are reset to condition assessment.
- Invalid or blocked transitions: non-editable treatment or unresolvable child blocks update.

Required execution workflow:
1. Use function `add child to basis` (`POST /api/behandlinger/{behandlingId}/legg-til-barn`) with `behandlingId=B1` and body `barnIdent=C1` to add the child.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect person basis and current step.

Existing-state shortcuts:
- Treatment setup can be skipped when an editable treatment exists and child `C1` is not already represented in the same basis.
- Direct database setup must preserve active persongrunnlag and treatment step consistency.

Parameter and value bindings:
- `behandlingId=B1` scopes the basis.
- `barnIdent=C1` is resolved to actor/person state and added under the same treatment.

Business result: The child exists in the treatment basis, and treatment state is reset so later assessments can be redone with the new child.

Constraints and invariants:
- Treatment must be editable.
- Child identity must be valid and processable.
- Downstream treatment state is reset; previous result/decision-derived state may become stale or removed.

Failure and exceptional cases:
- Failing function: `add child to basis`
  - Source discriminator: `child-basis methods branch for treatment is closed or locked`
  - Failure condition: treatment is closed or locked.
  - Why it fails: implementation validates editability.
  - Violated prerequisite or constraint: person basis can be changed only on editable treatments.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/grunnlag/personopplysninger/PersongrunnlagService.kt — child-basis methods`
- Failing function: `add child to basis`
  - Source discriminator: `child-basis methods branch for barnIdent=C1 cannot be resolved or processed`
  - Failure condition: `barnIdent=C1` cannot be resolved or processed.
  - Why it fails: person lookup/validation fails while building basis.
  - Violated prerequisite or constraint: child must be a valid treatment person.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/grunnlag/personopplysninger/PersongrunnlagService.kt — child-basis methods`
- Failing function: `add child to basis`
  - Source discriminator: `PersongrunnlagService missing-basis guard`
  - Failure condition: No active person basis exists.
  - Why it fails: There is no basis to extend.
  - Violated prerequisite or constraint: An active basis is required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/grunnlag/personopplysninger/PersongrunnlagService.kt — child-basis methods`
- Failing function: `add child to basis`
  - Source discriminator: `PersongrunnlagService duplicate-child guard`
  - Failure condition: The child already belongs to the basis.
  - Why it fails: A duplicate relation is rejected.
  - Violated prerequisite or constraint: A child may occur only once.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/grunnlag/personopplysninger/PersongrunnlagService.kt — child-basis methods`
- Failing function: `add child to basis`
  - Source discriminator: `PersongrunnlagService post-refresh guard`
  - Failure condition: The child remains absent after refresh.
  - Why it fails: The relation was not established.
  - Violated prerequisite or constraint: The refreshed basis must contain the child.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/grunnlag/personopplysninger/PersongrunnlagService.kt — child-basis methods`

Implementation notes: This mutation has broader side effects than a child-row insert because later steps are reset.

<a id="behavior-12"></a>
### Behavior 12: Put treatment on wait, update wait, and resume
Business goal: Pause a treatment with a deadline/reason, change the wait metadata, then resume the treatment.

API group boundary: All functions share the active wait record for one `behandlingId`.

Domain context: Waiting controls treatment status, task deadlines, and whether caseworker steps may execute.

Starting point: `Existing service state`

State transition summary:
- State before: active treatment has status `UTREDES` and no active wait record.
- Transition trigger: wait creation.
- Intermediate states: active wait record is stored, treatment status becomes `SATT_PÅ_VENT`, task deadlines are extended, metadata can be updated.
- State after: wait record is inactive, treatment status returns to `UTREDES`, open task deadlines are adjusted.
- Invalid or blocked transitions: duplicate wait, past deadline, unchanged update, or resume without active wait fails.

Required execution workflow:
1. Use function `set treatment on wait` (`POST /api/sett-på-vent/{behandlingId}`) with `behandlingId=B1` and body `frist=D_future`, `årsak=A1` to place the treatment on wait.
2. Use function `update wait` (`PUT /api/sett-på-vent/{behandlingId}`) with `behandlingId=B1` and body `frist=D_later`, `årsak=A2` to update active wait metadata.
3. Use function `resume treatment` (`PUT /api/sett-på-vent/{behandlingId}/fortsettbehandling`) with `behandlingId=B1` to resume the treatment.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` after step 1 or 3 to inspect status and active wait.

Existing-state shortcuts:
- Step 1 can be skipped only when an equivalent active wait record already exists for the same treatment; then update/resume can use it.
- Direct database setup must set both active wait row and treatment status consistently.
- The core resume action cannot be skipped when validating wait closure.

Parameter and value bindings:
- The same `behandlingId=B1` binds wait creation, update, and resume.
- `frist` drives task-deadline extension; `årsak` is stored/logged.
- `D_later` must differ from the existing deadline or `A2` must differ from existing reason.

Business result: The treatment is paused, metadata is changed, then the active wait is deactivated and treatment resumes investigation status.

Constraints and invariants:
- `frist` cannot be before today.
- Treatment must be active, `UTREDES`, and not already on wait for creation.
- While waiting, ordinary treatment step execution is blocked by `StegService`.

Failure and exceptional cases:
- Failing function: `set treatment on wait`
  - Source discriminator: `wait-state guards branch for treatment already has active wait`
  - Failure condition: treatment already has active wait.
  - Why it fails: `validerBehandlingKanSettesPåVent` rejects duplicate active wait.
  - Violated prerequisite or constraint: only one active wait record per treatment.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/settpåvent/SettPåVentUtils.kt — wait-state guards`
- Failing function: `set treatment on wait`
  - Source discriminator: `wait-state guards branch for frist is before today`
  - Failure condition: `frist` is before today.
  - Why it fails: deadline validation rejects past deadlines.
  - Violated prerequisite or constraint: wait deadline must be current/future.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/settpåvent/SettPåVentUtils.kt — wait-state guards`
- Failing function: `update wait`
  - Source discriminator: `wait-state guards branch for new frist and årsak equal existing values`
  - Failure condition: new `frist` and `årsak` equal existing values.
  - Why it fails: service rejects no-op updates.
  - Violated prerequisite or constraint: update must change wait state.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/settpåvent/SettPåVentUtils.kt — wait-state guards`
- Failing function: `resume treatment`
  - Source discriminator: `wait-state guards branch for no active wait exists`
  - Failure condition: no active wait exists.
  - Why it fails: `gjenopptaBehandling` requires active wait and waiting status.
  - Violated prerequisite or constraint: treatment must currently be on wait.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/settpåvent/SettPåVentUtils.kt — wait-state guards`
- Failing function: `set treatment on wait`
  - Source discriminator: `SettPåVentUtils wait-state guards`
  - Failure condition: An active wait exists, deadline is past, or lifecycle disallows waiting.
  - Why it fails: The transition is rejected.
  - Violated prerequisite or constraint: Eligible state, no wait, and a non-past deadline are required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/settpåvent/SettPåVentUtils.kt — wait-state guards`
- Failing function: `update wait`
  - Source discriminator: `SettPåVentService active-wait/deadline guards`
  - Failure condition: No active wait exists or the new deadline is past.
  - Why it fails: No mutable valid wait exists.
  - Violated prerequisite or constraint: An active wait and valid deadline are required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/settpåvent/SettPåVentUtils.kt — wait-state guards`
- Failing function: `resume treatment`
  - Source discriminator: `SettPåVentUtils resume-state guard`
  - Failure condition: No active wait exists or it is not manually resumable, including machine wait.
  - Why it fails: Resume is invalid.
  - Violated prerequisite or constraint: A manual resumable wait is required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/behandling/settpåvent/SettPåVentUtils.kt — wait-state guards`

Implementation notes: Wait creation publishes treatment statistics and extends open task deadlines; resume sets open task deadlines to tomorrow and publishes statistics through wait save.

<a id="behavior-13"></a>
### Behavior 13: Read person information for case handling
Business goal: Retrieve detailed, simple, and address-focused person information for casework.

API group boundary: These are person lookup functions bound by the same person identity and caller access context.

Domain context: Case creation, treatment basis, and letter addressing depend on person/register data.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: person exists in upstream person/register systems.
- Transition trigger: person lookup requests.
- Intermediate states: no service state mutation.
- State after: person details or masked data are returned.
- Invalid or blocked transitions: upstream lookup failures or missing access produce failures/masked data.

Required execution workflow:
1. Use function `retrieve full person information` (`GET /api/person`) with header `personIdent=P1` to read detailed person information.
2. Use function `retrieve simple person information` (`GET /api/person/enkel`) with header `personIdent=P1` to read simple person information.
3. Use function `retrieve person address` (`GET /api/person/adresse`) with header `personIdent=P1` to read name and address.

Optional verification workflow:
None.

Existing-state shortcuts:
- No local case state is required.
- Upstream person data and caller identity must still exist and be authorized.

Parameter and value bindings:
- Header `personIdent=P1` is reused across all three reads.
- Caller context determines whether full or masked information is returned.

Business result: The caller receives person information suitable for casework and address/recipient decisions.

Constraints and invariants:
- Implementation uses the `personIdent` header.
- Access-control result may mask data rather than fail for some person reads.

Failure and exceptional cases:
- Failing function: `retrieve full person information`
  - Source discriminator: `PersonopplysningerService missing-birth-data outcome`
  - Failure condition: Required birth information is absent.
  - Why it fails: The requested domain person view cannot be constructed.
  - Violated prerequisite or constraint: Required birth information must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/pdl/PersonopplysningerService.kt — PersonopplysningerService`
- Failing function: `retrieve full person information`
  - Source discriminator: `PersonopplysningerService discontinued-identity outcome`
  - Failure condition: The identity has ceased without a usable current identity.
  - Why it fails: The person cannot be resolved as an active domain subject.
  - Violated prerequisite or constraint: A usable current identity must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/pdl/PersonopplysningerService.kt — PersonopplysningerService`
- Failing function: `retrieve full person information`
  - Source discriminator: `PersonopplysningerService actor-not-found outcome`
  - Failure condition: No domain actor resolves for the supplied identity.
  - Why it fails: The person-scoped operation has no persisted subject.
  - Violated prerequisite or constraint: The identity must resolve to a domain actor.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/pdl/PersonopplysningerService.kt — PersonopplysningerService`
- Failing function: `retrieve simple person information`
  - Source discriminator: `PersonopplysningerService missing-birth-data outcome`
  - Failure condition: Required birth information is absent.
  - Why it fails: The requested domain person view cannot be constructed.
  - Violated prerequisite or constraint: Required birth information must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/pdl/PersonopplysningerService.kt — PersonopplysningerService`
- Failing function: `retrieve simple person information`
  - Source discriminator: `PersonopplysningerService discontinued-identity outcome`
  - Failure condition: The identity has ceased without a usable current identity.
  - Why it fails: The person cannot be resolved as an active domain subject.
  - Violated prerequisite or constraint: A usable current identity must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/pdl/PersonopplysningerService.kt — PersonopplysningerService`
- Failing function: `retrieve simple person information`
  - Source discriminator: `PersonopplysningerService actor-not-found outcome`
  - Failure condition: No domain actor resolves for the supplied identity.
  - Why it fails: The person-scoped operation has no persisted subject.
  - Violated prerequisite or constraint: The identity must resolve to a domain actor.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/pdl/PersonopplysningerService.kt — PersonopplysningerService`
- Failing function: `retrieve person address`
  - Source discriminator: `PersonopplysningerService missing-birth-data outcome`
  - Failure condition: Required birth information is absent.
  - Why it fails: The requested domain person view cannot be constructed.
  - Violated prerequisite or constraint: Required birth information must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/pdl/PersonopplysningerService.kt — PersonopplysningerService`
- Failing function: `retrieve person address`
  - Source discriminator: `PersonopplysningerService discontinued-identity outcome`
  - Failure condition: The identity has ceased without a usable current identity.
  - Why it fails: The person cannot be resolved as an active domain subject.
  - Violated prerequisite or constraint: A usable current identity must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/pdl/PersonopplysningerService.kt — PersonopplysningerService`
- Failing function: `retrieve person address`
  - Source discriminator: `PersonopplysningerService actor-not-found outcome`
  - Failure condition: No domain actor resolves for the supplied identity.
  - Why it fails: The person-scoped operation has no persisted subject.
  - Violated prerequisite or constraint: The identity must resolve to a domain actor.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/pdl/PersonopplysningerService.kt — PersonopplysningerService`

Implementation notes: OpenAPI exposes `personIdentBody` for `GET /api/person`, but source uses the `personIdent` header and does not use that body-style parameter.

<a id="behavior-14"></a>
### Behavior 14: Refresh treatment register basis and manually record death
Business goal: Update treatment person basis from register information and record manual death details for a person in the treatment.

API group boundary: Both functions mutate the treatment person basis under the same `behandlingId`.

Domain context: Register changes and death data can change benefit entitlement and require reassessment.

Starting point: `Existing service state`

State transition summary:
- State before: treatment has an active person basis.
- Transition trigger: register refresh and manual death registration.
- Intermediate states: external register data is reloaded; manual death date/reason is stored.
- State after: treatment basis reflects refreshed register information and manual death metadata.
- Invalid or blocked transitions: missing treatment, missing person in basis, or invalid death payload blocks mutation.

Required execution workflow:
1. Use function `refresh register information` (`GET /api/person/oppdater-registeropplysninger/{behandlingId}`) with `behandlingId=B1` to refresh register basis.
2. Use function `register manual death` (`POST /api/person/registrer-manuell-dodsfall/{behandlingId}`) with `behandlingId=B1` and body `personIdent=P1`, `dødsfallDato=D_death`, `begrunnelse=R1` to store manual death data.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect treatment person basis.

Existing-state shortcuts:
- Refresh can be skipped when current basis is already known to be fresh, but manual death still requires the target person to belong to the treatment.
- Direct database setup must preserve active persongrunnlag and person ownership under `behandlingId=B1`.

Parameter and value bindings:
- `behandlingId=B1` scopes both basis mutations.
- `personIdent=P1` in manual death must identify a person in that treatment context.

Business result: Register information is refreshed and manual death information exists on the relevant treatment person.

Constraints and invariants:
- Treatment access is validated.
- Manual death body must include identity, death date, and reason.

Failure and exceptional cases:
- Failing function: `register manual death`
  - Source discriminator: `register/death methods branch for personIdent=P2 is not part of treatment B1`
  - Failure condition: `personIdent=P2` is not part of treatment `B1`.
  - Why it fails: service validates treatment/person relationship.
  - Violated prerequisite or constraint: manual death can only be recorded for a treatment participant.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/grunnlag/personopplysninger/PersongrunnlagService.kt — register/death methods`
- Failing function: `refresh register information`
  - Source discriminator: `register/death methods branch for behandlingId=B404 does not exist`
  - Failure condition: `behandlingId=B404` does not exist.
  - Why it fails: treatment lookup fails.
  - Violated prerequisite or constraint: treatment must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/grunnlag/personopplysninger/PersongrunnlagService.kt — register/death methods`
- Failing function: `register manual death`
  - Source discriminator: `PersongrunnlagService duplicate-death guard`
  - Failure condition: A death date is already registered.
  - Why it fails: A conflicting second record is rejected.
  - Violated prerequisite or constraint: Only one effective death date may exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/grunnlag/personopplysninger/PersongrunnlagService.kt — register/death methods`
- Failing function: `register manual death`
  - Source discriminator: `PersongrunnlagService death-before-birth guard`
  - Failure condition: Death date precedes birth date.
  - Why it fails: The chronology is impossible.
  - Violated prerequisite or constraint: Death cannot precede birth.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/grunnlag/personopplysninger/PersongrunnlagService.kt — register/death methods`
- Failing function: `register manual death`
  - Source discriminator: `missing-condition-assessment outcome`
  - Failure condition: Dependent condition assessment is absent.
  - Why it fails: Eligibility state cannot be updated.
  - Violated prerequisite or constraint: Required assessments must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/grunnlag/personopplysninger/PersongrunnlagService.kt — register/death methods`

Implementation notes: `refresh register information` is mutating despite being exposed as `GET`.

<a id="behavior-15"></a>
### Behavior 15: Maintain condition assessment records
Business goal: Add, update, delete, and supplement condition assessment data on an editable treatment.

API group boundary: The functions share one active vilkårsvurdering under `behandlingId` and reuse condition/assessment ids.

Domain context: Condition assessment is core entitlement evidence; changes reset later result and decision state.

Starting point: `Existing service state`

State transition summary:
- State before: treatment has an active condition assessment.
- Transition trigger: condition mutation requests.
- Intermediate states: condition rows or periods are added/changed/deleted; other-assessment row is updated; later steps are reset.
- State after: assessment reflects the requested changes and later treatment steps must be redone.
- Invalid or blocked transitions: missing ids, non-editable treatment, or invalid payload fails.

Required execution workflow:
1. Use function `add condition` (`POST /api/vilkaarsvurdering/{behandlingId}`) with `behandlingId=B1` and body `RestNyttVilkår` to add a condition and capture `vilkaarId=V1` from the returned treatment.
2. Use function `update condition` (`PUT /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}`) with `behandlingId=B1`, `vilkaarId=V1`, and body `RestPersonResultat` to update the condition.
3. Use function `delete condition period` (`DELETE /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}`) with `behandlingId=B1`, `vilkaarId=V1`, and body `personIdent=P1` to delete one condition period.
4. Use function `add condition` (`POST /api/vilkaarsvurdering/{behandlingId}`) with `behandlingId=B1` and body `RestNyttVilkår` to create another condition for deletion and capture `vilkaarId=V2`.
5. Use function `delete condition` (`DELETE /api/vilkaarsvurdering/{behandlingId}/vilkaar`) with `behandlingId=B1` and body `RestSlettVilkår` referencing `vilkaarId=V2` to delete the condition.
6. Use function `update other assessment` (`PUT /api/vilkaarsvurdering/{behandlingId}/annenvurdering/{annenVurderingId}`) with `behandlingId=B1`, `annenVurderingId=A1`, and body `RestAnnenVurdering` to update other assessment state.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect condition and assessment data.
2. Use function `list condition explanation texts` (`GET /api/vilkaarsvurdering/vilkaarsbegrunnelser`) to inspect available explanation metadata.

Existing-state shortcuts:
- Steps creating condition ids can be skipped when valid `vilkaarId` values already exist under the same treatment.
- `annenVurderingId=A1` may come from initial treatment setup or a later treatment read.
- Direct database setup must preserve id ownership under the same active vilkårsvurdering.

Parameter and value bindings:
- `behandlingId=B1` scopes all condition mutations.
- `vilkaarId` returned in a treatment response is reused for update/delete.
- `personIdent` in delete-period body must map to the actor owning the condition period.
- `annenVurderingId=A1` must belong to treatment `B1`.

Business result: Condition assessment data is changed, deleted, and supplemented; later result/decision state is reset or invalidated by these changes.

Constraints and invariants:
- Treatment must be editable.
- Existing condition/assessment ids must belong to the active assessment for the treatment.
- Mutations reset later workflow state.

Failure and exceptional cases:
- Failing function: `update condition`
  - Source discriminator: `VilkårsvurderingValidering branch for vilkaarId=V404 does not identify a condition in the treatment`
  - Failure condition: `vilkaarId=V404` does not identify a condition in the treatment.
  - Why it fails: service cannot load/update the condition result.
  - Violated prerequisite or constraint: condition id must come from treatment state.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vilkårsvurdering/VilkårsvurderingValidering.kt — validation methods`
- Failing function: `delete condition period`
  - Source discriminator: `VilkårsvurderingValidering branch for body person identity does not own the requested period`
  - Failure condition: body person identity does not own the requested period.
  - Why it fails: deletion resolves person/condition ownership.
  - Violated prerequisite or constraint: period deletion must target the correct treatment person.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vilkårsvurdering/VilkårsvurderingValidering.kt — validation methods`
- Failing function: `update other assessment`
  - Source discriminator: `VilkårsvurderingValidering branch for annenVurderingId=A404 is missing`
  - Failure condition: `annenVurderingId=A404` is missing.
  - Why it fails: service cannot find the other-assessment record.
  - Violated prerequisite or constraint: assessment id must exist under treatment.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vilkårsvurdering/VilkårsvurderingValidering.kt — validation methods`
- Failing function: `update condition`
  - Source discriminator: `VilkårsvurderingValidering explanation-compatibility outcomes`
  - Failure condition: Result, type, and explanation are incompatible.
  - Why it fails: The named validator rejects the combination.
  - Violated prerequisite or constraint: Explanation must match condition and result.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vilkårsvurdering/VilkårsvurderingValidering.kt — validation methods`
- Failing function: `add condition`
  - Source discriminator: `duplicate-unassessed-condition guard`
  - Failure condition: An equivalent unassessed condition already exists.
  - Why it fails: Duplicate pending assessment is rejected.
  - Violated prerequisite or constraint: Only one matching unassessed condition may exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vilkårsvurdering/VilkårsvurderingValidering.kt — validation methods`

Implementation notes: These mutations are not isolated edits; they are treatment-state reset points.

<a id="behavior-16"></a>
### Behavior 16: Maintain EEA competence intervals
Business goal: Create/replace competence periods and delete an existing competence interval.

API group boundary: Both functions share `behandlingId` and competence interval ids.

Domain context: EEA competence affects differential calculation and entitlement.

Starting point: `Existing service state`

State transition summary:
- State before: editable treatment has EEA competence basis.
- Transition trigger: competence upsert and delete.
- Intermediate states: periods can be split/merged/replaced by body period and children.
- State after: competence timeline reflects upserted/deleted intervals.
- Invalid or blocked transitions: missing `fom`, empty children, reversed period, incompatible activities, or id/treatment mismatch fails.

Required execution workflow:
1. Use function `upsert competence interval` (`PUT /api/kompetanse/{behandlingId}`) with `behandlingId=B1` and body `fom=YM1`, `tom=YM2`, `barnIdenter=[C1]`, required activity/result fields to upsert competence and capture `kompetanseId=K1`.
2. Use function `delete competence interval` (`DELETE /api/kompetanse/{behandlingId}/{kompetanseId}`) with `behandlingId=B1`, `kompetanseId=K1` to delete the interval.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect competence periods.
2. Use function `retrieve EØS timelines` (`GET /api/tidslinjer/{behandlingId}`) with `behandlingId=B1` to inspect timelines.

Existing-state shortcuts:
- The upsert can be skipped for delete only when `kompetanseId=K1` already exists under `behandlingId=B1`.
- Direct database setup must preserve the interval-to-treatment ownership and child actors.

Parameter and value bindings:
- `behandlingId=B1` scopes both functions.
- `barnIdenter` are resolved to child actors.
- The `kompetanseId` returned in treatment state is consumed by delete.

Business result: Competence intervals are upserted, recalculated by the schema service, and then removed when requested.

Constraints and invariants:
- `fom` is required and cannot be after `tom`.
- At least one child is required.
- Activity combinations are validated.

Failure and exceptional cases:
- Failing function: `upsert competence interval`
  - Source discriminator: `PeriodeOgBarnSkjemaService branch for missing fom, empty barnIdenter, or fom > tom`
  - Failure condition: missing `fom`, empty `barnIdenter`, or `fom > tom`.
  - Why it fails: controller validation rejects invalid period/child list.
  - Violated prerequisite or constraint: competence interval must be well-formed.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/eøs/felles/PeriodeOgBarnSkjemaService.kt — validation methods`
- Failing function: `delete competence interval`
  - Source discriminator: `PeriodeOgBarnSkjemaService branch for kompetanseId=K1 belongs to another treatment`
  - Failure condition: `kompetanseId=K1` belongs to another treatment.
  - Why it fails: shared schema deletion rejects treatment/id mismatch.
  - Violated prerequisite or constraint: child resource id must belong to `behandlingId=B1`.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/eøs/felles/PeriodeOgBarnSkjemaService.kt — validation methods`
- Failing function: `upsert competence interval`
  - Source discriminator: `PeriodeOgBarnSkjemaService validation outcomes`
  - Failure condition: `fom` is missing, dates are reversed, no child is selected, or activities are invalid.
  - Why it fails: The shared EEA validator rejects the interval.
  - Violated prerequisite or constraint: Period, children, and activities must be valid.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/eøs/felles/PeriodeOgBarnSkjemaService.kt — validation methods`

Implementation notes: Competence upsert is period-and-child based, not simple row overwrite.

<a id="behavior-17"></a>
### Behavior 17: Maintain foreign period amounts
Business goal: Update and delete an existing foreign benefit amount period.

API group boundary: Both functions share the differential-calculation amount resource under `behandlingId`.

Domain context: Foreign amounts feed EEA differential calculation.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: a foreign period amount row already exists for treatment.
- Transition trigger: amount update and delete request.
- Intermediate states: amount/monthly calculation is changed.
- State after: the amount period is updated or removed.
- Invalid or blocked transitions: missing amount id or negative amount fails.

Required execution workflow:
1. Use function `update foreign period amount` (`PUT /api/differanseberegning/utenlandskperidebeløp/{behandlingId}`) with `behandlingId=B1` and body `id=U1`, `beløp=100`, period fields to update the existing amount.
2. Use function `delete foreign period amount` (`DELETE /api/differanseberegning/utenlandskperidebeløp/{behandlingId}/{utenlandskPeriodebeløpId}`) with `behandlingId=B1`, `utenlandskPeriodebeløpId=U1` to delete the amount.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect differential-calculation state.

Existing-state shortcuts:
- No API in this function set creates the initial foreign amount row; direct database setup or earlier internal EEA basis generation must provide `id=U1`.
- The existing row must belong to `behandlingId=B1`.

Parameter and value bindings:
- Body `id=U1` identifies the existing amount row for update.
- Path `utenlandskPeriodebeløpId=U1` consumes the same row id for deletion.
- `behandlingId=B1` scopes access and returned treatment state.

Business result: The existing foreign amount period has updated amount data, then is deleted from the treatment.

Constraints and invariants:
- `beløp` cannot be negative.
- Implementation preserves existing `utbetalingsland` when updating.

Failure and exceptional cases:
- Failing function: `update foreign period amount`
  - Source discriminator: `UtenlandskPeriodebeløpService branch for body id=U404 does not exist`
  - Failure condition: body `id=U404` does not exist.
  - Why it fails: implementation loads the existing row to preserve country data.
  - Violated prerequisite or constraint: update requires pre-existing foreign amount row.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/eøs/utenlandskperiodebeløp/UtenlandskPeriodebeløpService.kt — mutation methods`

Implementation notes: This area has update/delete but no API-realizable create workflow for the initial row.

<a id="behavior-18"></a>
### Behavior 18: Update existing currency rate from ECB
Business goal: Update an existing currency-rate period by fetching a rate from ECB when currency/date changes.

API group boundary: The function is the ECB branch of the currency-rate lifecycle under `behandlingId`.

Domain context: Currency rates are required for EEA differential calculation when foreign currency amounts are present.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: currency-rate row exists for a treatment.
- Transition trigger: update with currency code/date not matching the historical ISK manual branch.
- Intermediate states: existing row is compared; ECB rate is fetched when date/code changed.
- State after: row contains updated code/date/rate data.
- Invalid or blocked transitions: missing existing id, ECB failure, non-editable treatment, or child identity failure blocks update.

Required execution workflow:
1. Use function `update currency rate from ECB` (`PUT /api/differanseberegning/valutakurs/{behandlingId}`) with `behandlingId=B1` and body `id=VK1`, `valutakode=EUR`, `valutakursdato=D1`, `barnIdenter=[C1]` to update the existing currency rate.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect currency-rate state.

Existing-state shortcuts:
- Existing row `VK1` must be provided by prior internal generation or direct database setup.
- Direct setup must keep `VK1` in the same treatment scope.

Parameter and value bindings:
- Body `id=VK1` is used to load existing row and detect change.
- `valutakode` and `valutakursdato` drive ECB lookup.
- `barnIdenter` bind the rate to child actors.

Business result: The existing currency-rate period contains updated ECB-sourced rate data.

Constraints and invariants:
- This branch applies unless `valutakode=ISK` and `valutakursdato` is before 2018-02-01.
- Treatment must be editable.

Failure and exceptional cases:
- Failing function: `update currency rate from ECB`
  - Source discriminator: `update method branch for id=VK404 does not exist`
  - Failure condition: `id=VK404` does not exist.
  - Why it fails: controller/service loads existing rate for comparison.
  - Violated prerequisite or constraint: update requires pre-existing currency-rate row.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/eøs/valutakurs/ValutakursService.kt — update method`
- Failing function: `update currency rate from ECB`
  - Source discriminator: `update method branch for ECB cannot provide a rate for supplied code/date`
  - Failure condition: ECB cannot provide a rate for supplied code/date.
  - Why it fails: ECB service throws.
  - Violated prerequisite or constraint: external rate data must be available.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/eøs/valutakurs/ValutakursService.kt — update method`
- Failing function: `update currency rate from ECB`
  - Source discriminator: `ECBService no-business-rate result`
  - Failure condition: No applicable ECB rate exists for currency and date.
  - Why it fails: No domain rate can be calculated.
  - Violated prerequisite or constraint: An applicable business rate must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/eøs/valutakurs/ValutakursService.kt — update method`

Implementation notes: Source compares incoming code/date against existing row and fetches from ECB only when changed.

<a id="behavior-19"></a>
### Behavior 19: Set historical ISK currency rate manually and delete currency rate
Business goal: Store a manually supplied historical ISK rate, then remove the currency-rate row.

API group boundary: The functions share the same currency-rate row under `behandlingId`; manual ISK is a distinct branch from ECB update.

Domain context: ECB data is not used for Icelandic krona before 2018-02-01, so manual rate entry is required.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: historical ISK currency-rate row exists for treatment.
- Transition trigger: manual ISK update and delete.
- Intermediate states: supplied `kurs` is stored without ECB lookup.
- State after: row is updated, then deleted.
- Invalid or blocked transitions: missing existing rate id or mismatched treatment/rate id fails.

Required execution workflow:
1. Use function `set historical ISK rate manually` (`PUT /api/differanseberegning/valutakurs/{behandlingId}`) with `behandlingId=B1` and body `id=VK1`, `valutakode=ISK`, `valutakursdato=2018-01-31`, `kurs=K_manual`, `barnIdenter=[C1]` to store manual rate.
2. Use function `delete currency rate` (`DELETE /api/differanseberegning/valutakurs/{behandlingId}/{valutakursId}`) with `behandlingId=B1`, `valutakursId=VK1` to delete the rate.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect currency-rate state.

Existing-state shortcuts:
- Existing rate `VK1` must already exist under treatment through internal generation or direct database setup.
- The delete step can start from any valid existing rate id in the same treatment.

Parameter and value bindings:
- `valutakode=ISK` and `valutakursdato=2018-01-31` select the manual branch.
- Manual `kurs` is stored.
- `valutakursId=VK1` reuses the id updated in step 1.

Business result: Historical ISK rate is manually stored, then the rate row is removed.

Constraints and invariants:
- Manual branch is limited to ISK before 2018-02-01.
- Treatment must be editable.

Failure and exceptional cases:
- Failing function: `delete currency rate`
  - Source discriminator: `ValutakursService branch for valutakursId=VK1 does not belong to behandlingId=B1`
  - Failure condition: `valutakursId=VK1` does not belong to `behandlingId=B1`.
  - Why it fails: shared schema deletion rejects id/treatment mismatch.
  - Violated prerequisite or constraint: currency-rate id must belong to the treatment.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/eøs/valutakurs/ValutakursService.kt — mutation methods`

Implementation notes: The branch distinction is implemented in controller code with `LocalDate.of(2018, 2, 1)`.

<a id="behavior-20"></a>
### Behavior 20: Maintain changed payment shares and reset treatment result
Business goal: Create, fill, remove, and explicitly reset changed-payment share state in an editable treatment.

API group boundary: All functions share `behandlingId`, changed-payment share id, benefit recalculation, and treatment-result reset.

Domain context: Changed payment shares modify payment allocation and require recalculation of granted benefit.

Starting point: `Existing service state`

State transition summary:
- State before: treatment is editable and has benefit/person basis.
- Transition trigger: changed-payment share creation/update/delete/reset.
- Intermediate states: empty share is created, filled, benefits recalculated, treatment reset to result step.
- State after: share state is updated or removed and treatment result must be redone.
- Invalid or blocked transitions: missing share id, non-treatment person, invalid/overlapping period, or non-editable treatment fails.

Required execution workflow:
1. Use function `create changed payment share` (`POST /api/endretutbetalingandel/{behandlingId}`) with `behandlingId=B1` to create an empty share and capture `endretUtbetalingAndelId=EUA1`.
2. Use function `update changed payment share` (`PUT /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`) with `behandlingId=B1`, `endretUtbetalingAndelId=EUA1`, and body `personIdent=P1`, period and payment-share fields to update the share.
3. Use function `delete changed payment share` (`DELETE /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`) with `behandlingId=B1`, `endretUtbetalingAndelId=EUA1` to remove it.
4. Use function `reset treatment to treatment result` (`POST /api/endretutbetalingandel/{behandlingId}/tilbakestill`) with `behandlingId=B1` to explicitly reset treatment to result step.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect share list and current step.
2. Use function `find invalid after-payment periods` (`GET /api/behandlinger/{behandlingId}/personer-med-ugyldig-etterbetalingsperiode`) with `behandlingId=B1` to inspect after-payment validity.

Existing-state shortcuts:
- Step 1 can be skipped when a valid `endretUtbetalingAndelId` already exists under treatment.
- Direct database setup must preserve share ownership and treatment person ownership for `personIdent`.

Parameter and value bindings:
- `endretUtbetalingAndelId=EUA1` from creation is reused in update and delete.
- Body `personIdent=P1` must identify a treatment person.
- `behandlingId=B1` scopes recalculation and treatment reset.

Business result: Changed-payment share state is created, updated, removed, and the treatment is reset to treatment-result state after recalculation-sensitive changes.

Constraints and invariants:
- Treatment must be editable.
- `personIdent` must belong to treatment.
- Period must be inside granted benefit and cannot violate overlap rules.

Failure and exceptional cases:
- Failing function: `update changed payment share`
  - Source discriminator: `EndretUtbetalingAndelValidering branch for personIdent=P2 is not in the treatment`
  - Failure condition: `personIdent=P2` is not in the treatment.
  - Why it fails: service resolves body person identity against treatment persons.
  - Violated prerequisite or constraint: changed-payment share must target a treatment person.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/endretutbetaling/EndretUtbetalingAndelValidering.kt — validation methods`
- Failing function: `delete changed payment share`
  - Source discriminator: `EndretUtbetalingAndelValidering branch for endretUtbetalingAndelId=EUA404 does not exist`
  - Failure condition: `endretUtbetalingAndelId=EUA404` does not exist.
  - Why it fails: service cannot find share for deletion.
  - Violated prerequisite or constraint: share id must come from creation or treatment state.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/endretutbetaling/EndretUtbetalingAndelValidering.kt — validation methods`
- Failing function: `create changed payment share`
  - Source discriminator: `EndretUtbetalingAndelValidering named outcomes`
  - Failure condition: Reason, period, percentage, person, entitlement overlap, or share relation is invalid.
  - Why it fails: A source-distinct validator rejects the adjustment.
  - Violated prerequisite or constraint: All adjustment inputs and relationships must be valid.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/endretutbetaling/EndretUtbetalingAndelValidering.kt — validation methods`

Implementation notes: Create/update/delete all call the reset-to-treatment-result service after recalculation changes.

<a id="behavior-21"></a>
### Behavior 21: Inspect EEA timelines
Business goal: Read calculated EEA timelines for a treatment.

API group boundary: The function is a treatment-scoped EEA read model.

Domain context: Timelines show how EEA basis affects entitlement and differential calculation.

Starting point: `Existing service state`

State transition summary:
- State before: treatment has EEA-relevant basis.
- Transition trigger: timeline read request.
- Intermediate states: no persistent mutation.
- State after: calculated timelines are returned.
- Invalid or blocked transitions: missing treatment or access failure blocks read.

Required execution workflow:
1. Use function `retrieve EØS timelines` (`GET /api/tidslinjer/{behandlingId}`) with `behandlingId=B1` to retrieve timelines.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup API call is needed when `behandlingId=B1` already exists with EEA basis.
- Direct database setup must preserve treatment and EEA basis consistency.

Parameter and value bindings:
- `behandlingId=B1` scopes timeline calculation.

Business result: Caller receives EEA timeline data for the treatment.

Constraints and invariants:
- Timeline result is derived from treatment state and does not mutate it.

Failure and exceptional cases:
- Failing function: `retrieve EØS timelines`
  - Source discriminator: `timeline methods branch for behandlingId=B404 does not exist`
  - Failure condition: `behandlingId=B404` does not exist.
  - Why it fails: treatment lookup/access fails.
  - Violated prerequisite or constraint: treatment must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/eøs/vilkårsvurdering/VilkårsvurderingTidslinjeService.kt — timeline methods`
- Failing function: `retrieve EØS timelines`
  - Source discriminator: `timeline prerequisite outcomes`
  - Failure condition: Treatment or condition-assessment basis is absent.
  - Why it fails: Timelines cannot be derived.
  - Violated prerequisite or constraint: Treatment and assessment basis must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/eøs/vilkårsvurdering/VilkårsvurderingTidslinjeService.kt — timeline methods`

Implementation notes: This is a read-only behavior used frequently to verify EEA side-resource mutations.

<a id="behavior-22"></a>
### Behavior 22: Maintain EEA refund periods
Business goal: Add, list, update, and delete refund periods for EEA handling.

API group boundary: All functions share `behandlingId` and refund-period `id`.

Domain context: EEA refund periods affect decision/payment handling.

Starting point: `Existing service state`

State transition summary:
- State before: editable treatment has no target refund period.
- Transition trigger: refund-period creation.
- Intermediate states: period id is generated; list can return it; update changes it.
- State after: period is deleted or no longer present.
- Invalid or blocked transitions: missing period id or non-editable treatment fails.

Required execution workflow:
1. Use function `add EØS refund period` (`POST /api/refusjon-eøs/behandlinger/{behandlingId}`) with `behandlingId=B1` and body `RestRefusjonEøs` to create refund period and capture `id=R1`.
2. Use function `list EØS refund periods` (`GET /api/refusjon-eøs/behandlinger/{behandlingId}`) with `behandlingId=B1` to retrieve and confirm `id=R1`.
3. Use function `update EØS refund period` (`PUT /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`) with `behandlingId=B1`, `id=R1`, and body `RestRefusjonEøs` to update the period.
4. Use function `delete EØS refund period` (`DELETE /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`) with `behandlingId=B1`, `id=R1` to delete the period.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect returned treatment state.

Existing-state shortcuts:
- Step 1 can be skipped when `id=R1` already exists under `behandlingId=B1`.
- Direct database setup must ensure period ownership and treatment editability.

Parameter and value bindings:
- `id=R1` generated by creation or returned by list is reused for update/delete.
- `behandlingId=B1` scopes access and response but update service loads by `id`.

Business result: A refund period is created, made visible, updated, and removed.

Constraints and invariants:
- Treatment must be editable for create/update/delete.
- Period id must exist for update/delete.

Failure and exceptional cases:
- Failing function: `update EØS refund period`
  - Source discriminator: `RefusjonEøsService branch for id=R404 does not exist`
  - Failure condition: `id=R404` does not exist.
  - Why it fails: service lookup throws when refund period id is missing.
  - Violated prerequisite or constraint: period id must come from creation/list.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vedtak/refusjonEøs/RefusjonEøsService.kt — mutation methods`
- Failing function: `delete EØS refund period`
  - Source discriminator: `RefusjonEøsService branch for id=R404 does not exist`
  - Failure condition: `id=R404` does not exist.
  - Why it fails: service logs the period before deleting and lookup throws.
  - Violated prerequisite or constraint: period id must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vedtak/refusjonEøs/RefusjonEøsService.kt — mutation methods`

Implementation notes: Update does not pass `behandlingId` to the service lookup, so id/path ownership is weaker than the endpoint shape implies.

<a id="behavior-23"></a>
### Behavior 23: Maintain overpaid currency periods
Business goal: Add, list, update, and delete periods with overpaid currency amount.

API group boundary: All functions share `behandlingId` and overpaid-currency period `id`.

Domain context: Overpaid currency periods support repayment and correction calculations.

Starting point: `Existing service state`

State transition summary:
- State before: editable treatment has no target overpaid-currency period.
- Transition trigger: period creation.
- Intermediate states: period id is generated; update changes amount/period/monthly flag.
- State after: period is deleted.
- Invalid or blocked transitions: missing id or invalid treatment/access fails.

Required execution workflow:
1. Use function `add overpaid currency period` (`POST /api/feilutbetalt-valuta/behandling/{behandlingId}`) with `behandlingId=B1` and body `RestFeilutbetaltValuta` to create period and capture `id=FV1`.
2. Use function `list overpaid currency periods` (`GET /api/feilutbetalt-valuta/behandling/{behandlingId}`) with `behandlingId=B1` to retrieve and confirm `id=FV1`.
3. Use function `update overpaid currency period` (`PUT /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`) with `behandlingId=B1`, `id=FV1`, and body `RestFeilutbetaltValuta` to update period data.
4. Use function `delete overpaid currency period` (`DELETE /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`) with `behandlingId=B1`, `id=FV1` to delete it.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect treatment state.

Existing-state shortcuts:
- Step 1 can be skipped when a valid `id=FV1` already exists for the treatment.
- Direct database setup must keep the period under the same treatment and valid caller-access scope.

Parameter and value bindings:
- `id=FV1` created/listed is reused for update/delete.
- If `erPerMåned` is absent, feature toggle value determines stored flag.

Business result: Overpaid-currency period exists, is updated, and is eventually removed.

Constraints and invariants:
- Treatment access is validated for create/update/delete/list.
- Feature toggle can influence default monthly interpretation.

Failure and exceptional cases:
- Failing function: `update overpaid currency period`
  - Source discriminator: `FeilutbetaltValutaService branch for id=FV404 does not exist`
  - Failure condition: `id=FV404` does not exist.
  - Why it fails: service lookup throws when id is not found.
  - Violated prerequisite or constraint: period id must come from creation/list.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vedtak/feilutbetaltValuta/FeilutbetaltValutaService.kt — mutation methods`
- Failing function: `delete overpaid currency period`
  - Source discriminator: `FeilutbetaltValutaService branch for id=FV404 does not exist`
  - Failure condition: `id=FV404` does not exist.
  - Why it fails: service logs the period before deleting and lookup throws.
  - Violated prerequisite or constraint: period id must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vedtak/feilutbetaltValuta/FeilutbetaltValutaService.kt — mutation methods`

Implementation notes: Update service loads by `id` and does not enforce `behandlingId` ownership in the service call itself.

<a id="behavior-24"></a>
### Behavior 24: Activate and deactivate corrected decision metadata
Business goal: Mark a treatment as having corrected decision metadata, then deactivate that metadata.

API group boundary: Both functions share active corrected-decision state under `behandlingId`.

Domain context: Corrected decisions need explicit metadata that can be active for letter/decision handling.

Starting point: `Existing service state`

State transition summary:
- State before: editable treatment has no active corrected-decision metadata or has an old active record.
- Transition trigger: create corrected-decision metadata.
- Intermediate states: previous active record is deactivated.
- State after: new active metadata exists, then no active metadata after deactivation.
- Invalid or blocked transitions: non-editable treatment or missing treatment blocks mutation.

Required execution workflow:
1. Use function `create corrected decision metadata` (`POST /api/korrigertvedtak/behandling/{behandlingId}`) with `behandlingId=B1` and body `KorrigerVedtakRequest` to create active metadata.
2. Use function `deactivate corrected decision metadata` (`PATCH /api/korrigertvedtak/behandling/{behandlingId}`) with `behandlingId=B1` to deactivate active metadata.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect returned treatment state.

Existing-state shortcuts:
- Creation can start with an existing active correction; the service deactivates it before saving the new one.
- Direct database setup must keep active flags scoped to the same treatment.

Parameter and value bindings:
- `behandlingId=B1` binds metadata to the treatment.
- Body values become corrected-decision metadata.

Business result: Corrected-decision metadata is activated and then inactivated; previous active metadata is deactivated when a new record is created.

Constraints and invariants:
- At most one active corrected-decision metadata record is intended per treatment.
- Treatment must be editable.

Failure and exceptional cases:
- Failing function: `create corrected decision metadata`
  - Source discriminator: `KorrigertVedtakService branch for treatment is not editable`
  - Failure condition: treatment is not editable.
  - Why it fails: controller calls `validerBehandlingKanRedigeres`.
  - Violated prerequisite or constraint: correction metadata can be changed only on editable treatment.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/korrigertvedtak/KorrigertVedtakService.kt — mutation methods`

Implementation notes: There is no dedicated list/retrieve endpoint for corrected-decision metadata.

<a id="behavior-25"></a>
### Behavior 25: Activate, list, and deactivate corrected after-payment metadata
Business goal: Mark corrected after-payment metadata active, inspect all records, and deactivate the active correction.

API group boundary: Functions share corrected after-payment state under `behandlingId`.

Domain context: Corrected after-payment metadata documents manual correction decisions around after-payment periods.

Starting point: `Existing service state`

State transition summary:
- State before: editable treatment has no active corrected after-payment metadata or has an old active record.
- Transition trigger: create correction metadata.
- Intermediate states: previous active record is deactivated; list returns all records.
- State after: new record is active, then active flag is cleared.
- Invalid or blocked transitions: non-editable treatment blocks mutation.

Required execution workflow:
1. Use function `create corrected after-payment metadata` (`POST /api/korrigertetterbetaling/behandling/{behandlingId}`) with `behandlingId=B1` and body `KorrigertEtterbetalingRequest` to create active metadata.
2. Use function `list corrected after-payment metadata` (`GET /api/korrigertetterbetaling/behandling/{behandlingId}`) with `behandlingId=B1` to list correction records.
3. Use function `deactivate corrected after-payment metadata` (`PATCH /api/korrigertetterbetaling/behandling/{behandlingId}`) with `behandlingId=B1` to deactivate active metadata.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect treatment state.

Existing-state shortcuts:
- Creation can start when a previous active correction exists; the service deactivates it.
- Direct setup must maintain active flags and treatment ownership.

Parameter and value bindings:
- `behandlingId=B1` scopes creation/list/deactivation.
- Body values are converted to corrected after-payment metadata for the treatment.

Business result: Corrected after-payment metadata is created, visible in list output, and then inactivated.

Constraints and invariants:
- At most one active corrected after-payment record is intended per treatment.
- Treatment must be editable for create/deactivate.

Failure and exceptional cases:
- Failing function: `create corrected after-payment metadata`
  - Source discriminator: `KorrigertEtterbetalingService branch for treatment is not editable`
  - Failure condition: treatment is not editable.
  - Why it fails: controller validates editability.
  - Violated prerequisite or constraint: correction metadata requires editable treatment.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/korrigertetterbetaling/KorrigertEtterbetalingService.kt — mutation methods`

Implementation notes: Deactivation without an active correction is effectively a no-op in service logic.

<a id="behavior-26"></a>
### Behavior 26: Add and remove small child supplement correction
Business goal: Add a small-child supplement correction for a month and remove it later.

API group boundary: Both functions share `behandlingId` and `årMåned`.

Domain context: Manual supplement corrections adjust entitlement for specific months.

Starting point: `Existing service state`

State transition summary:
- State before: editable treatment has no correction for target month.
- Transition trigger: add correction request.
- Intermediate states: monthly correction is persisted.
- State after: correction exists, then is removed.
- Invalid or blocked transitions: non-editable treatment or invalid month fails.

Required execution workflow:
1. Use function `add small child supplement correction` (`POST /api/småbarnstilleggkorrigering/behandling/{behandlingId}`) with `behandlingId=B1` and body `årMåned=YM1` to add correction.
2. Use function `remove small child supplement correction` (`DELETE /api/småbarnstilleggkorrigering/behandling/{behandlingId}`) with `behandlingId=B1` and body `årMåned=YM1` to remove correction.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect treatment state.

Existing-state shortcuts:
- Add setup can be skipped for remove only when a correction for the same `årMåned` exists on the treatment.
- Direct database setup must preserve the month/treatment ownership.

Parameter and value bindings:
- `årMåned=YM1` must be reused exactly for removal.
- `behandlingId=B1` scopes both actions.

Business result: The monthly supplement correction is added and then removed from the treatment.

Constraints and invariants:
- Treatment must be editable.
- There is no dedicated list endpoint for supplement corrections.

Failure and exceptional cases:
- Failing function: `remove small child supplement correction`
  - Source discriminator: `SmåbarnstilleggKorrigeringService branch for no correction exists for årMåned=YM1`
  - Failure condition: no correction exists for `årMåned=YM1`.
  - Why it fails: service cannot remove a non-existing correction for the treatment/month.
  - Violated prerequisite or constraint: removal requires existing month correction.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/småbarnstilleggkorrigering/SmåbarnstilleggKorrigeringService.kt — mutation methods`
- Failing function: `add small child supplement correction`
  - Source discriminator: `duplicate-month guard`
  - Failure condition: A correction already exists for the month.
  - Why it fails: Duplicate correction is rejected.
  - Violated prerequisite or constraint: Only one correction per month may exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/småbarnstilleggkorrigering/SmåbarnstilleggKorrigeringService.kt — mutation methods`

Implementation notes: The API depends on treatment reads to observe correction state.

<a id="behavior-27"></a>
### Behavior 27: Preview repayment warning letter
Business goal: Generate a repayment warning letter preview without sending it.

API group boundary: The function is scoped to a treatment’s repayment context.

Domain context: Caseworkers preview repayment warning text before sending formal communication.

Starting point: `Existing service state`

State transition summary:
- State before: treatment exists.
- Transition trigger: preview request.
- Intermediate states: PDF preview is generated.
- State after: no persisted letter/send state is required.
- Invalid or blocked transitions: missing treatment or invalid free text/upstream document generation failure blocks preview.

Required execution workflow:
1. Use function `preview repayment warning letter` (`POST /api/tilbakekreving/{behandlingId}/forhandsvis-varselbrev`) with `behandlingId=B1` and body `fritekst=T1` to generate preview.

Optional verification workflow:
None.

Existing-state shortcuts:
- Treatment setup can be skipped when a valid treatment exists.
- Direct database setup must preserve repayment-relevant treatment data.

Parameter and value bindings:
- `behandlingId=B1` scopes the preview.
- Body `fritekst=T1` is rendered into the preview.

Business result: Caller receives preview PDF/resource; no send state is persisted by this function.

Constraints and invariants:
- Body must include free text.
- Document generation depends on letter services.

Failure and exceptional cases:
- Failing function: `preview repayment warning letter`
  - Source discriminator: `warning preview branch for behandlingId=B404 does not exist`
  - Failure condition: `behandlingId=B404` does not exist.
  - Why it fails: treatment lookup/document-generation basis fails.
  - Violated prerequisite or constraint: treatment must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/BrevService.kt — warning preview`
- Failing function: `preview repayment warning letter`
  - Source discriminator: `decision/person-basis/unit prerequisites`
  - Failure condition: Active decision, person basis, or handling unit is missing.
  - Why it fails: The warning-letter basis is incomplete.
  - Violated prerequisite or constraint: All prerequisites must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/BrevService.kt — warning preview`

Implementation notes: Preview and send are intentionally separated; this function is read-like but computational.

<a id="behavior-28"></a>
### Behavior 28: Generate and retrieve decision letter
Business goal: Generate the persisted decision letter for an active decision and retrieve it.

API group boundary: Both functions share `vedtakId`.

Domain context: A decision letter is the formal document representation of a treatment decision.

Starting point: `Existing service state`

State transition summary:
- State before: active decision exists without generated stored letter.
- Transition trigger: decision-letter generation.
- Intermediate states: PDF is generated and stored.
- State after: stored decision letter can be retrieved.
- Invalid or blocked transitions: missing decision id, generation failure, or retrieval before generation fails.

Required execution workflow:
1. Use function `generate decision letter` (`POST /api/dokument/vedtaksbrev/{vedtakId}`) with `vedtakId=Vd1` to generate and store the decision letter.
2. Use function `retrieve decision letter` (`GET /api/dokument/vedtaksbrev/{vedtakId}`) with `vedtakId=Vd1` to retrieve the stored PDF.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to find the active `vedtak.id` when it is not already known.

Existing-state shortcuts:
- Treatment step setup can be skipped when `vedtakId=Vd1` already identifies an active decision.
- Direct database setup must include decision/treatment relationship and document prerequisites.

Parameter and value bindings:
- `vedtakId=Vd1` must come from treatment active decision state.
- The same `vedtakId` is reused for retrieval.

Business result: A decision letter PDF exists for the decision and is retrievable.

Constraints and invariants:
- Retrieval requires prior generation.
- Decision id must identify a valid decision.

Failure and exceptional cases:
- Failing function: `retrieve decision letter`
  - Source discriminator: `BrevService branch for letter has not been generated for vedtakId=Vd1`
  - Failure condition: letter has not been generated for `vedtakId=Vd1`.
  - Why it fails: retrieval expects stored/generated document state.
  - Violated prerequisite or constraint: generation must precede retrieval.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/BrevService.kt — decision-letter methods`
- Failing function: `generate decision letter`
  - Source discriminator: `BrevService branch for vedtakId=V404 does not exist`
  - Failure condition: `vedtakId=V404` does not exist.
  - Why it fails: document service cannot load decision basis.
  - Violated prerequisite or constraint: decision id must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/BrevService.kt — decision-letter methods`
- Failing function: `generate decision letter`
  - Source discriminator: `template/result/explanation guards`
  - Failure condition: Lifecycle, template, explanations, or cessation period structure is unsupported.
  - Why it fails: The letter cannot be composed.
  - Violated prerequisite or constraint: Document prerequisites must be supported.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/BrevService.kt — decision-letter methods`

Implementation notes: Decision letter generation is a mutating `POST`; retrieval is a binary/document response.

<a id="behavior-29"></a>
### Behavior 29: Preview and send manual treatment letter
Business goal: Preview and send a manual letter tied to a treatment.

API group boundary: Both functions share `behandlingId` and `ManueltBrevRequest`.

Domain context: Treatment-scoped letters use treatment basis, recipients, and work distribution.

Starting point: `Existing service state`

State transition summary:
- State before: treatment exists and letter has not been sent.
- Transition trigger: preview then send request.
- Intermediate states: preview document is generated; send generates and dispatches/journals document.
- State after: manual letter is sent for treatment.
- Invalid or blocked transitions: missing treatment, invalid request, or document/distribution failure blocks send.

Required execution workflow:
1. Use function `preview treatment letter` (`POST /api/dokument/forhaandsvis-brev/{behandlingId}`) with `behandlingId=B1` and body `ManueltBrevRequest` to preview the letter.
2. Use function `send treatment letter` (`POST /api/dokument/send-brev/{behandlingId}`) with `behandlingId=B1` and body `ManueltBrevRequest` to send the letter.

Optional verification workflow:
1. Use function `retrieve treatment log` (`GET /api/logg/{behandlingId}`) with `behandlingId=B1` to inspect logged communication events.

Existing-state shortcuts:
- Treatment setup can be skipped when `behandlingId=B1` exists and has required recipient/person basis.
- Preview is not a persisted prerequisite for send, but it is part of this concrete quality-control workflow.

Parameter and value bindings:
- `behandlingId=B1` scopes preview and send.
- The manual letter request body should be reused between preview and send to ensure the sent letter matches the preview.

Business result: A treatment-scoped manual letter is sent; preview was generated with the same request values.

Constraints and invariants:
- Recipient data is enriched from treatment person basis and work distribution.
- Send side effects occur in document/journal/distribution integrations.

Failure and exceptional cases:
- Failing function: `send treatment letter`
  - Source discriminator: `manual treatment letter methods branch for ManueltBrevRequest lacks required template/recipient content`
  - Failure condition: `ManueltBrevRequest` lacks required template/recipient content.
  - Why it fails: document service cannot generate/send a valid manual letter.
  - Violated prerequisite or constraint: request body must define a valid manual letter.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/DokumentService.kt — manual treatment letter methods`
- Failing function: `preview treatment letter`
  - Source discriminator: `manual-template validation outcomes`
  - Failure condition: SED, cohabitation date, response weeks, or template choice is invalid.
  - Why it fails: The template strategy rejects the content.
  - Violated prerequisite or constraint: Template-specific rules must hold.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/DokumentService.kt — manual treatment letter methods`
- Failing function: `send treatment letter`
  - Source discriminator: `participant/confidentiality guards`
  - Failure condition: Recipient is not a participant or confidentiality disallows it.
  - Why it fails: Distribution is rejected.
  - Violated prerequisite or constraint: Recipient and confidentiality rules must hold.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/DokumentService.kt — manual treatment letter methods`

Implementation notes: Preview and send are separate; clients must control request equality if they require preview/send consistency.

<a id="behavior-30"></a>
### Behavior 30: Preview and send manual case letter
Business goal: Preview and send a manual letter tied directly to a case.

API group boundary: Both functions share `fagsakId` and `ManueltBrevRequest`.

Domain context: Some manual communication belongs to the case rather than a specific treatment.

Starting point: `Existing service state`

State transition summary:
- State before: case exists and letter has not been sent.
- Transition trigger: case-letter preview and send request.
- Intermediate states: preview is generated; send dispatches/journals document.
- State after: manual case letter is sent.
- Invalid or blocked transitions: missing case, invalid request, or document integration failure blocks send.

Required execution workflow:
1. Use function `preview case letter` (`POST /api/dokument/fagsak/{fagsakId}/forhaandsvis-brev`) with `fagsakId=F1` and body `ManueltBrevRequest` to preview the letter.
2. Use function `send case letter` (`POST /api/dokument/fagsak/{fagsakId}/send-brev`) with `fagsakId=F1` and body `ManueltBrevRequest` to send the letter.

Optional verification workflow:
1. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) with `fagsakId=F1` to inspect case context.

Existing-state shortcuts:
- Case creation can be skipped when `fagsakId=F1` exists.
- Preview is not a persisted prerequisite for send, but it is part of this concrete workflow.

Parameter and value bindings:
- `fagsakId=F1` scopes both functions.
- Manual letter request body should be reused between preview and send.

Business result: A case-scoped manual letter is sent.

Constraints and invariants:
- Case must exist and caller must have access.
- Letter service owns distribution/journal side effects.

Failure and exceptional cases:
- Failing function: `send case letter`
  - Source discriminator: `manual case letter methods branch for fagsakId=F404 does not exist`
  - Failure condition: `fagsakId=F404` does not exist.
  - Why it fails: case/document basis cannot be loaded.
  - Violated prerequisite or constraint: parent case must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/DokumentService.kt — manual case letter methods`
- Failing function: `send case letter`
  - Source discriminator: `case/institution lookup outcomes`
  - Failure condition: Case or selected institution relation is absent.
  - Why it fails: Recipient context is invalid.
  - Violated prerequisite or constraint: Case and relation must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/DokumentService.kt — manual case letter methods`

Implementation notes: This workflow is distinct from treatment-scoped letters because no `behandlingId` is required.

<a id="behavior-31"></a>
### Behavior 31: Maintain manual letter recipients
Business goal: Add, list, and remove manual letter recipients for a treatment.

API group boundary: All functions share `behandlingId` and recipient id.

Domain context: Manual recipients affect who receives treatment letters and how addresses are constructed.

Starting point: `Existing service state`

State transition summary:
- State before: treatment has default or no manual recipients.
- Transition trigger: recipient creation.
- Intermediate states: recipient row is saved/logged and can be listed.
- State after: recipient is deleted.
- Invalid or blocked transitions: confidential-person rule, invalid recipient id, or non-editable treatment blocks mutation.

Required execution workflow:
1. Use function `add letter recipient` (`POST /api/brevmottaker/{behandlingId}`) with `behandlingId=B1` and body `RestBrevmottaker` to add recipient and capture `mottakerId=M1`.
2. Use function `list letter recipients` (`GET /api/brevmottaker/{behandlingId}`) with `behandlingId=B1` to list recipients and confirm `mottakerId=M1`.
3. Use function `delete letter recipient` (`DELETE /api/brevmottaker/{behandlingId}/{mottakerId}`) with `behandlingId=B1`, `mottakerId=M1` to delete the recipient.

Optional verification workflow:
1. Use function `retrieve treatment log` (`GET /api/logg/{behandlingId}`) with `behandlingId=B1` to inspect recipient add/remove logs.

Existing-state shortcuts:
- Add can be skipped for deletion only when a valid recipient id already exists.
- Direct database setup must preserve recipient-to-treatment ownership.

Parameter and value bindings:
- `mottakerId=M1` from add/list is reused in delete.
- `behandlingId=B1` scopes access and list, but deletion service deletes by recipient id.

Business result: Manual recipient is added, visible in list, and removed.

Constraints and invariants:
- Treatment must be editable.
- Strictly confidential person rules can reject manual recipients.
- Recipient combinations are validated later when constructing actual letter recipients.

Failure and exceptional cases:
- Failing function: `add letter recipient`
  - Source discriminator: `BrevmottakerService branch for treatment contains strictly confidential person and manual recipient is disallowed`
  - Failure condition: treatment contains strictly confidential person and manual recipient is disallowed.
  - Why it fails: validation service rejects the combination.
  - Violated prerequisite or constraint: confidentiality rules restrict manual recipients.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/mottaker/BrevmottakerService.kt — recipient methods`
- Failing function: `delete letter recipient`
  - Source discriminator: `BrevmottakerService branch for mottakerId=M404 does not exist`
  - Failure condition: `mottakerId=M404` does not exist.
  - Why it fails: service throws when recipient id is missing.
  - Violated prerequisite or constraint: deletion requires existing recipient id.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/mottaker/BrevmottakerService.kt — recipient methods`
- Failing function: `add letter recipient`
  - Source discriminator: `editability/confidentiality guards`
  - Failure condition: Treatment is non-editable or confidentiality disallows recipient.
  - Why it fails: Recipient cannot be attached.
  - Violated prerequisite or constraint: Editable state and permitted recipient are required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/brev/mottaker/BrevmottakerService.kt — recipient methods`

Implementation notes: Delete validates access to `behandlingId` but service deletes by `mottakerId`; ownership enforcement is weaker than the path shape suggests.

<a id="behavior-32"></a>
### Behavior 32: Edit decision periods and regenerate letter explanations
Business goal: Modify decision-period explanations, override change date, and generate final letter explanation texts.

API group boundary: Functions share generated decision-period state under `behandlingId` and `vedtaksperiodeId`.

Domain context: Decision periods and explanations determine the content of decision letters.

Starting point: `Existing service state`

State transition summary:
- State before: treatment result has been derived and decision periods exist.
- Transition trigger: list/read period ids, update explanations, override change date, generate text.
- Intermediate states: standard explanations, free texts, and overridden change date are persisted; decision periods may be regenerated.
- State after: periods contain updated explanation data and generated letter texts are available.
- Invalid or blocked transitions: missing period id, invalid explanation enum, unsupported explanation data, or missing treatment state fails.

Required execution workflow:
1. Use function `list decision periods` (`GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder`) with `behandlingId=B1` to obtain `vedtaksperiodeId=VP1`.
2. Use function `update standard explanations` (`PUT /api/vedtaksperioder/standardbegrunnelser/{vedtaksperiodeId}`) with `vedtaksperiodeId=VP1` and body `standardbegrunnelser=[S1]` to update standard explanations.
3. Use function `update decision free texts` (`PUT /api/vedtaksperioder/fritekster/{vedtaksperiodeId}`) with `vedtaksperiodeId=VP1` and body `RestPutVedtaksperiodeMedFritekster` to update free texts.
4. Use function `regenerate decision periods` (`PUT /api/vedtaksperioder/endringstidspunkt`) with body `behandlingId=B1`, overridden date fields to regenerate periods.
5. Use function `list decision periods` (`GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder`) with `behandlingId=B1` to obtain current `vedtaksperiodeId=VP2`.
6. Use function `generate letter explanation texts` (`GET /api/vedtaksperioder/brevbegrunnelser/{vedtaksperiodeId}`) with `vedtaksperiodeId=VP2` to generate final explanation text.

Optional verification workflow:
1. Use function `get change date` (`GET /api/behandlinger/{behandlingId}/endringstidspunkt`) with `behandlingId=B1` to inspect the change date.
2. Use function `generate decision letter` (`POST /api/dokument/vedtaksbrev/{vedtakId}`) with `vedtakId=Vd1` to generate the full letter after explanations are set.

Existing-state shortcuts:
- The initial list can be skipped when a current `vedtaksperiodeId` is already known from treatment result generation.
- After overriding change date, previous period ids may be stale; the period list should be refreshed before generating text.

Parameter and value bindings:
- `behandlingId=B1` is used to list/regenerate periods.
- `vedtaksperiodeId=VP1` from list is reused for explanation updates.
- `vedtaksperiodeId=VP2` after regeneration is used for explanation-text generation.
- Explanation names must map to `IVedtakBegrunnelse` enum values.

Business result: Decision-period explanation metadata and free texts are updated; change date override can regenerate the period set; final letter explanation text can be generated.

Constraints and invariants:
- Updating explanations derives treatment id from period id before access check.
- Change-date override regenerates periods through the first change date.
- Unsupported explanation data fails during generated text retrieval.

Failure and exceptional cases:
- Failing function: `update standard explanations`
  - Source discriminator: `explanation methods branch for body contains an explanation string not convertible to a known enum`
  - Failure condition: body contains an explanation string not convertible to a known enum.
  - Why it fails: controller maps names through `IVedtakBegrunnelse.konverterTilEnumVerdi`.
  - Violated prerequisite or constraint: explanation names must be recognized.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vedtak/vedtaksperiode/VedtaksperiodeService.kt — explanation methods`
- Failing function: `generate letter explanation texts`
  - Source discriminator: `explanation methods branch for decision period contains unsupported explanation data`
  - Failure condition: decision period contains unsupported explanation data.
  - Why it fails: controller throws `Feil("Ukjent begrunnelsestype")`.
  - Violated prerequisite or constraint: explanation data must be a supported type.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vedtak/vedtaksperiode/VedtaksperiodeService.kt — explanation methods`
- Failing function: `update standard explanations`
  - Source discriminator: `period/explanation/rejection guards`
  - Failure condition: Period is missing, explanation unknown, or removal contradicts rejection result.
  - Why it fails: The explanation set would be invalid.
  - Violated prerequisite or constraint: Period and compatible explanations must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vedtak/vedtaksperiode/VedtaksperiodeService.kt — explanation methods`
- Failing function: `generate letter explanation texts`
  - Source discriminator: `generated-type outcome`
  - Failure condition: A generated explanation type is unsupported.
  - Why it fails: Final text cannot be assembled.
  - Violated prerequisite or constraint: Every generated type must be supported.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/vedtak/vedtaksperiode/VedtaksperiodeService.kt — explanation methods`

Implementation notes: Period regeneration can invalidate ids acquired earlier in the workflow.

<a id="behavior-33"></a>
### Behavior 33: Retrieve treatment log
Business goal: Read audit/log entries for a treatment.

API group boundary: The function is scoped by `behandlingId`.

Domain context: Logs are used to inspect state-changing actions and communication events.

Starting point: `Existing service state`

State transition summary:
- State before: treatment exists with zero or more log entries.
- Transition trigger: log read request.
- Intermediate states: no persistent mutation.
- State after: log entries are returned.
- Invalid or blocked transitions: invalid id or repository failure returns bad-request resource.

Required execution workflow:
1. Use function `retrieve treatment log` (`GET /api/logg/{behandlingId}`) with `behandlingId=B1` to retrieve logs.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup API call is required when `behandlingId=B1` already exists.
- Direct database setup can seed log rows for verification.

Parameter and value bindings:
- `behandlingId=B1` scopes the log query.

Business result: Caller receives log entries for the treatment.

Constraints and invariants:
- The function is read-only.

Failure and exceptional cases:
None.

Implementation notes: This behavior is often verification for other mutations.

<a id="behavior-34"></a>
### Behavior 34: Retrieve external benefit data for BISYS
Business goal: Provide BISYS with extended child benefit and small child supplement periods for a person.

API group boundary: The function is a single external integration lookup.

Domain context: BISYS consumes child-benefit data but does not mutate local state through this endpoint.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: benefit/person data exists locally/upstream.
- Transition trigger: BISYS lookup.
- Intermediate states: no local mutation.
- State after: benefit periods are returned.
- Invalid or blocked transitions: too old `fraDato` or unknown person fails.

Required execution workflow:
1. Use function `retrieve BISYS extended benefit` (`POST /api/bisys/hent-utvidet-barnetrygd`) with body `personIdent=P1`, `fraDato=D_within_5_years` to retrieve benefit periods.

Optional verification workflow:
None.

Existing-state shortcuts:
- No local API setup can guarantee returned benefit periods; completed benefit/payment state or test data must already exist.

Parameter and value bindings:
- `personIdent=P1` identifies the external subject.
- `fraDato` controls period cutoff and must be no older than five years.

Business result: BISYS receives benefit period data or a controlled external-service error.

Constraints and invariants:
- `fraDato` older than five years is rejected.
- Unknown PDL person is converted to bad-request external-service error.

Failure and exceptional cases:
- Failing function: `retrieve BISYS extended benefit`
  - Source discriminator: `lookup method branch for fraDato < today - 5 years`
  - Failure condition: `fraDato < today - 5 years`.
  - Why it fails: controller throws BAD_REQUEST external-service error.
  - Violated prerequisite or constraint: BISYS lookup window is limited.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/ekstern/bisys/BisysController.kt — lookup method`
- Failing function: `retrieve BISYS extended benefit`
  - Source discriminator: `lookup method branch for unknown personIdent`
  - Failure condition: unknown `personIdent`.
  - Why it fails: PDL not-found `Feil` is converted to BAD_REQUEST external-service error.
  - Violated prerequisite or constraint: person must exist upstream.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/ekstern/bisys/BisysController.kt — lookup method`

Implementation notes: This is an integration read, not a caseworker workflow.

<a id="behavior-35"></a>
### Behavior 35: Retrieve pension child benefit
Business goal: Provide Pension with child-benefit case and period data for one person.

API group boundary: Atomic external lookup scoped by `ident` and `fraDato`.

Domain context: Pension consumers need child-benefit state without entering the caseworker flow.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: benefit state may exist for the person.
- Transition trigger: Pension lookup request.
- Intermediate states: no local mutation.
- State after: child-benefit data is returned or an error is raised.
- Invalid or blocked transitions: `fraDato` older than two years or upstream failure blocks the lookup.

Required execution workflow:
1. Use function `retrieve pension child benefit` (`POST /api/ekstern/pensjon/hent-barnetrygd`) with body `ident=P1`, `fraDato=D_within_two_years` to retrieve child-benefit data for Pension.

Optional verification workflow:
None.

Existing-state shortcuts:
- No API setup is required when upstream person and benefit state already exists.
- Direct database setup must still produce benefit periods that the Pension service can map for `P1`.

Parameter and value bindings:
- Body `ident=P1` is the person key; `fraDato` limits returned periods.
- Caller identity is an external/system integration caller.

Business result: Pension receives mapped child-benefit data for the requested person and date range.

Constraints and invariants:
- `fraDato` cannot be more than two years before the request date.
- The function is read-only and does not create local tasks.

Failure and exceptional cases:
- Failing function: `retrieve pension child benefit`
  - Source discriminator: `lookup method branch for fraDato is older than the allowed two-year window`
  - Failure condition: `fraDato` is older than the allowed two-year window.
  - Why it fails: the controller throws a BAD_REQUEST external service error.
  - Violated prerequisite or constraint: Pension lookup date window.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/ekstern/pensjon/PensjonController.kt — lookup method`
- Failing function: `retrieve pension child benefit`
  - Source discriminator: `actor-not-found outcome`
  - Failure condition: Identity cannot resolve to actor.
  - Why it fails: No pension case can be scoped.
  - Violated prerequisite or constraint: Identity must resolve to actor.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/ekstern/pensjon/PensjonController.kt — lookup method`
- Failing function: `retrieve pension child benefit`
  - Source discriminator: `implemented-treatment prerequisite`
  - Failure condition: Implemented treatment lacks awarded-benefit or payment state.
  - Why it fails: Finalized response cannot be mapped.
  - Violated prerequisite or constraint: Implemented state must be complete.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/ekstern/pensjon/PensjonController.kt — lookup method`

Implementation notes: This is separate from yearly Pension export; no id returned here is consumed by the export endpoint.

<a id="behavior-36"></a>
### Behavior 36: Order pension yearly export
Business goal: Queue export of persons with child benefit for a Pension tax/reporting year.

API group boundary: Atomic asynchronous export order scoped by path year.

Domain context: Pension can order a yearly population export through task/Kafka processing.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: yearly benefit population may exist.
- Transition trigger: yearly export order.
- Intermediate states: a task/Kafka flow is created.
- State after: export work is queued; the person list is not returned synchronously.
- Invalid or blocked transitions: year outside `1970..2300` fails.

Required execution workflow:
1. Use function `order pension yearly export` (`GET /api/ekstern/pensjon/bestill-personer-med-barnetrygd/{år}`) with `år=2026` to queue the yearly export.

Optional verification workflow:
None.

Existing-state shortcuts:
- No case setup is required when the export job is intended to scan existing state.
- Direct task setup can represent an already queued export, but the order action itself cannot be skipped when validating this API behavior.

Parameter and value bindings:
- Path `år=2026` is serialized into the export task/event.

Business result: A yearly export job for Pension is queued.

Constraints and invariants:
- `år` must be an integer in the accepted range.
- The endpoint uses `GET` while creating asynchronous work.

Failure and exceptional cases:
- Failing function: `order pension yearly export`
  - Source discriminator: `export method branch for år=1969 or år=2301`
  - Failure condition: `år=1969` or `år=2301`.
  - Why it fails: the controller throws `IllegalArgumentException` for year outside range.
  - Violated prerequisite or constraint: allowed export year.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/ekstern/pensjon/PensjonController.kt — export method`

Implementation notes: This behavior does not consume data returned by `retrieve pension child benefit`.

<a id="behavior-37"></a>
### Behavior 37: Production tax data export
Business goal: Return Skatteetaten production person and period data for one tax year.

API group boundary: The functions share production tax year `aar` and response-to-request binding from listed persons to requested period `identer`.

Domain context: Skatteetaten receives extended child-benefit information for tax handling.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: tax-relevant benefit data exists for the year.
- Transition trigger: production list and period requests.
- Intermediate states: no local mutation.
- State after: production person list and period data are returned.
- Invalid or blocked transitions: invalid year/body or upstream failure blocks response.

Required execution workflow:
1. Use function `list tax persons` (`GET /api/skatt/personer`) with query `aar=2026` to list production tax persons and capture `ident=P1` from the response.
2. Use function `retrieve tax periods` (`POST /api/skatt/perioder`) with body `identer=[P1]`, `aar=2026` to retrieve production tax periods for the listed person.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped when `P1` is already a trusted tax-relevant identity for `aar=2026`.
- The identity must still belong to the same tax year and benefit population.

Parameter and value bindings:
- Query/body `aar=2026` is reused.
- `P1` returned by `list tax persons` is consumed in body `identer` for `retrieve tax periods`.

Business result: The production tax consumer receives period data for listed tax persons.

Constraints and invariants:
- `aar` must parse as a year string.
- Production list may use real or fallback data according to feature-toggle state, but the endpoint is still the production path.

Failure and exceptional cases:
None.

Implementation notes: Production and test endpoints are separate exposed capabilities and are modeled separately.

<a id="behavior-38"></a>
### Behavior 38: Tax test endpoint data retrieval
Business goal: Return Skatteetaten test-path person and period data for one tax year.

API group boundary: The functions share test endpoint paths, tax year `aar`, and response-to-request binding from listed persons to requested period `identer`.

Domain context: The test path exercises tax mapping without relying on the production feature-toggle route.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: test-path tax data exists.
- Transition trigger: test list and period requests.
- Intermediate states: no local mutation.
- State after: test person list and periods are returned.
- Invalid or blocked transitions: invalid year/body or upstream failure blocks response.

Required execution workflow:
1. Use function `list tax persons test` (`GET /api/skatt/personer/test`) with query `aar=2026` to list test-path tax persons and capture `ident=P1` from the response.
2. Use function `retrieve tax periods test` (`POST /api/skatt/perioder/test`) with body `identer=[P1]`, `aar=2026` to retrieve test-path tax periods.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped when `P1` is already known from test fixture data for `aar=2026`.
- Direct database/test fixture setup must still match the same year and identity scope.

Parameter and value bindings:
- Query/body `aar=2026` is reused.
- `P1` returned by `list tax persons test` is consumed in `retrieve tax periods test`.

Business result: Test callers receive mapped tax periods through the explicit test endpoints.

Constraints and invariants:
- Test endpoints always call the test-path service behavior.
- They are not interchangeable required steps for production export.

Failure and exceptional cases:
None.

Implementation notes: This behavior is intentionally split from production tax export.

<a id="behavior-39"></a>
### Behavior 39: Retrieve Infotrygd case and benefit context
Business goal: Read legacy Infotrygd case, benefit, and ongoing-state information for an applicant.

API group boundary: Functions share applicant `ident` and Infotrygd integration.

Domain context: Legacy Infotrygd state affects migration, duplicate checks, and caseworker context.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: applicant may have legacy Infotrygd state.
- Transition trigger: Infotrygd lookup requests.
- Intermediate states: no local mutation.
- State after: legacy cases, benefits, and ongoing boolean are returned.
- Invalid or blocked transitions: missing access can return masked data; upstream failure blocks response.

Required execution workflow:
1. Use function `retrieve Infotrygd cases` (`POST /api/infotrygd/hent-infotrygdsaker-for-soker`) with body `ident=P1` to retrieve legacy cases.
2. Use function `retrieve Infotrygd benefits` (`POST /api/infotrygd/hent-infotrygdstonader-for-soker`) with body `ident=P1` to retrieve legacy benefits.
3. Use function `check ongoing Infotrygd case` (`POST /api/infotrygd/har-lopende-sak`) with body `ident=P1` to check ongoing state.

Optional verification workflow:
None.

Existing-state shortcuts:
- No local case setup is needed.
- Upstream Infotrygd state must exist to return positive data.

Parameter and value bindings:
- `ident=P1` is reused across all three integration calls.

Business result: Caller receives legacy context for the applicant.

Constraints and invariants:
- Access can affect masking.
- These endpoints do not create local case/treatment state.

Failure and exceptional cases:
- Failing function: `retrieve Infotrygd cases`
  - Source discriminator: `actor-resolution not-found outcome`
  - Failure condition: The applicant identity cannot be resolved to a domain actor.
  - Why it fails: The legacy query cannot be scoped to a persisted applicant.
  - Violated prerequisite or constraint: The identity must resolve to a domain actor.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/infotrygd/InfotrygdController.kt — InfotrygdController`
- Failing function: `retrieve Infotrygd benefits`
  - Source discriminator: `actor-resolution not-found outcome`
  - Failure condition: The applicant identity cannot be resolved to a domain actor.
  - Why it fails: The legacy query cannot be scoped to a persisted applicant.
  - Violated prerequisite or constraint: The identity must resolve to a domain actor.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/infotrygd/InfotrygdController.kt — InfotrygdController`

Implementation notes: These are lookup behaviors only; they do not establish migration state by themselves.

<a id="behavior-40"></a>
### Behavior 40: Discover collaborators by search and organization number
Business goal: Find collaborator/institution information and retrieve details by organization number.

API group boundary: Both functions access the collaborator/institution service and can bind search result organization number into detail lookup.

Domain context: Institution cases and letters need collaborator data.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: collaborator exists upstream.
- Transition trigger: collaborator search and lookup.
- Intermediate states: no local mutation.
- State after: collaborator details are returned.
- Invalid or blocked transitions: no search variable or unknown organization number fails.

Required execution workflow:
1. Use function `search collaborator` (`POST /api/samhandler/navn`) with body `navn=N1` to search collaborators and capture `orgnr=ORG1` from a result.
2. Use function `retrieve collaborator by organization` (`GET /api/samhandler/orgnr/{orgnr}`) with `orgnr=ORG1` to retrieve details.

Optional verification workflow:
None.

Existing-state shortcuts:
- Search can be skipped when `orgnr=ORG1` is known from upstream or existing case state.

Parameter and value bindings:
- `orgnr=ORG1` returned by search is consumed by organization lookup.
- Search body must include at least `navn`, `postnummer`, or `område`.

Business result: Collaborator details are available for institution/case workflows.

Constraints and invariants:
- Empty search request is invalid.
- Unknown organization number maps to 404-style functional error.

Failure and exceptional cases:
- Failing function: `search collaborator`
  - Source discriminator: `SamhandlerController branch for body has no navn, postnummer, or område`
  - Failure condition: body has no `navn`, `postnummer`, or `område`.
  - Why it fails: controller throws BAD_REQUEST.
  - Violated prerequisite or constraint: at least one search variable is required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/institusjon/SamhandlerController.kt — collaborator methods`
- Failing function: `retrieve collaborator by organization`
  - Source discriminator: `SamhandlerController branch for orgnr=UNKNOWN`
  - Failure condition: `orgnr=UNKNOWN`.
  - Why it fails: not-found exceptions are converted to functional 404.
  - Violated prerequisite or constraint: collaborator must exist upstream.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/institusjon/SamhandlerController.kt — collaborator methods`

Implementation notes: This workflow can provide institution details but does not itself create an institution case.

<a id="behavior-41"></a>
### Behavior 41: Create and list complaint treatments for a case
Business goal: Start a complaint treatment for a child-benefit case and list complaint treatments.

API group boundary: Both functions share `fagsakId` and complaint integration state.

Domain context: Complaint handling is a separate treatment lifecycle linked to a case.

Starting point: `Existing service state`

State transition summary:
- State before: case exists without the requested complaint treatment.
- Transition trigger: complaint treatment creation request.
- Intermediate states: complaint integration creates treatment.
- State after: complaint treatment is listed for the case.
- Invalid or blocked transitions: missing case or invalid complaint body fails.

Required execution workflow:
1. Use function `create complaint treatment` (`POST /api/fagsaker/{fagsakId}/opprett-klagebehandling`) with `fagsakId=F1` and body `OpprettKlageDto` to create complaint treatment.
2. Use function `list complaint treatments` (`GET /api/fagsaker/{fagsakId}/hent-klagebehandlinger`) with `fagsakId=F1` to list complaint treatments.

Optional verification workflow:
1. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) with `fagsakId=F1` to inspect case context.

Existing-state shortcuts:
- Case setup can be skipped when `fagsakId=F1` exists.
- Direct complaint-system setup can seed complaint treatments for listing, but the core creation action cannot be skipped when testing creation.

Parameter and value bindings:
- `fagsakId=F1` scopes both creation and listing.
- Body `OpprettKlageDto` carries complaint creation data.

Business result: A complaint treatment exists for the case and is visible through the list endpoint.

Constraints and invariants:
- Parent case must exist.
- Complaint integration owns detailed complaint treatment semantics.

Failure and exceptional cases:
- Failing function: `create complaint treatment`
  - Source discriminator: `KlageService branch for fagsakId=F404 does not exist`
  - Failure condition: `fagsakId=F404` does not exist.
  - Why it fails: complaint creation cannot bind to parent case.
  - Violated prerequisite or constraint: complaint treatment requires existing case.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/klage/KlageService.kt — complaint methods`
- Failing function: `create complaint treatment`
  - Source discriminator: `KlageService future-date guard`
  - Failure condition: Complaint date is in the future.
  - Why it fails: Chronology is invalid.
  - Violated prerequisite or constraint: Received date may not be future.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/klage/KlageService.kt — complaint methods`
- Failing function: `list complaint treatments`
  - Source discriminator: `response-key consistency outcome`
  - Failure condition: Response omits requested case key.
  - Why it fails: Case-scoped list cannot be returned.
  - Violated prerequisite or constraint: Response must contain requested case.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/klage/KlageService.kt — complaint methods`

Implementation notes: This is internal complaint integration under `/api/fagsaker`, distinct from external Klage M2M revision endpoints.

<a id="behavior-42"></a>
### Behavior 42: Let complaint system create a revision after precheck
Business goal: Let the external complaint system check and create a complaint-triggered revision on a case.

API group boundary: Both functions share `fagsakId` and require Klage caller context.

Domain context: Complaint outcomes can require a revision treatment in the child-benefit system.

Starting point: `Existing service state`

State transition summary:
- State before: case exists and complaint system is authorized.
- Transition trigger: precheck then revision creation.
- Intermediate states: eligibility is evaluated.
- State after: revision treatment is created for complaint workflow.
- Invalid or blocked transitions: non-Klage caller or ineligible case blocks creation.

Required execution workflow:
1. Use function `check complaint revision creation` (`GET /api/klage/fagsaker/{fagsakId}/kan-opprette-revurdering-klage`) with `fagsakId=F1` and Klage machine-to-machine caller context to check eligibility.
2. Use function `create complaint revision` (`POST /api/klage/fagsaker/{fagsakId}/opprett-revurdering-klage/`) with `fagsakId=F1` and Klage machine-to-machine caller context to create the revision.

Optional verification workflow:
1. Use function `retrieve complaint decisions` (`GET /api/klage/fagsaker/{fagsakId}/vedtak`) with `fagsakId=F1` to inspect decisions available to complaint system.

Existing-state shortcuts:
- Precheck can be skipped only when equivalent eligibility was already established by trusted complaint workflow state, but creation still requires authorized Klage context.
- Direct database setup must preserve case state and lack of active blocking treatment where relevant.

Parameter and value bindings:
- `fagsakId=F1` is reused between precheck and creation.
- Caller context must satisfy `SikkerhetContext.kallKommerFraKlage()`.

Business result: A complaint-triggered revision treatment is created for the case.

Constraints and invariants:
- Klage caller is mandatory for precheck and creation.
- Ordinary user access rules differ for `retrieve complaint decisions`; M2M callers bypass ordinary fagsak access check.

Failure and exceptional cases:
- Failing function: `create complaint revision`
  - Source discriminator: `explicit not-created outcome`
  - Failure condition: An open treatment exists or no decision can be revised.
  - Why it fails: The precheck returns not-created.
  - Violated prerequisite or constraint: A revisable decision and no conflict are required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/ekstern/EksternKlageController.kt — revision methods`

Implementation notes: Precheck and creation are separate API calls; no token or precheck result id is bound between them.

<a id="behavior-43"></a>
### Behavior 43: Retrieve decisions for complaint system
Business goal: Provide fagsystem decisions for a case to the complaint system.

API group boundary: The function is case-scoped and externally exposed under Klage API.

Domain context: Complaint handling needs the child-benefit decisions being complained about.

Starting point: `Existing service state`

State transition summary:
- State before: case exists with decision history.
- Transition trigger: decision retrieval request.
- Intermediate states: no mutation.
- State after: decisions are returned.
- Invalid or blocked transitions: missing case or access failure blocks retrieval.

Required execution workflow:
1. Use function `retrieve complaint decisions` (`GET /api/klage/fagsaker/{fagsakId}/vedtak`) with `fagsakId=F1` to retrieve decisions.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup call is needed when `fagsakId=F1` is known and decisions exist.
- Direct database setup must include implemented/decision state.

Parameter and value bindings:
- `fagsakId=F1` scopes decision retrieval.
- Caller context determines ordinary access check behavior.

Business result: Complaint system receives decision data for the case.

Constraints and invariants:
- Non-M2M callers are subject to ordinary fagsak access validation.

Failure and exceptional cases:
- Failing function: `retrieve complaint decisions`
  - Source discriminator: `finalized-decision prerequisites`
  - Failure condition: Case is missing or completed treatment lacks active timestamped decision.
  - Why it fails: The complaint payload is incomplete.
  - Violated prerequisite or constraint: Case and timestamped decision must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/ekstern/EksternKlageController.kt — decision lookup`

Implementation notes: This is a lookup behavior and does not create complaint/revision state.

<a id="behavior-44"></a>
### Behavior 44: Search external tasks
Business goal: Find external task ids that can be acted on by later task workflows.

API group boundary: Atomic task discovery returning upstream `oppgaveId` values.

Domain context: Caseworker and admin task actions require ids from the external task service.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: external tasks may exist.
- Transition trigger: task search request.
- Intermediate states: no local mutation.
- State after: task search result is returned.
- Invalid or blocked transitions: invalid search body or upstream task failure returns illegal-state response.

Required execution workflow:
1. Use function `search tasks` (`POST /api/oppgave/hent-oppgaver`) with body `RestFinnOppgaveRequest` to search tasks and capture `oppgaveId=O1`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Discovery can be skipped when `oppgaveId=O1` is already trusted from upstream state.
- The task id must still refer to the expected task domain and tenant/context.

Parameter and value bindings:
- Search body fields are converted to the upstream task query.
- Returned `oppgaveId` can be reused by assignment, reset, retrieval, completion, and journal-link workflows.

Business result: The caller receives task ids and task metadata from the external task system.

Constraints and invariants:
- The endpoint does not own task state; it proxies the integration.

Failure and exceptional cases:
None.

Implementation notes: Discovery is intentionally separate from repair or completion actions.

<a id="behavior-45"></a>
### Behavior 45: Assign external task
Business goal: Assign a known task to a caseworker.

API group boundary: Task state transition scoped by `oppgaveId` returned by task discovery.

Domain context: Work distribution moves an external task to a named caseworker.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: task exists and is assignable.
- Transition trigger: assignment request.
- Intermediate states: upstream task assignment is updated.
- State after: task is assigned to the requested caseworker.
- Invalid or blocked transitions: missing/unassignable task or insufficient role fails.

Required execution workflow:
1. Use function `search tasks` (`POST /api/oppgave/hent-oppgaver`) with body `RestFinnOppgaveRequest` to capture `oppgaveId=O1`.
2. Use function `assign task` (`POST /api/oppgave/{oppgaveId}/fordel`) with `oppgaveId=O1`, query `saksbehandler=S1` to assign the task.

Optional verification workflow:
1. Use function `retrieve journaling task data` (`GET /api/oppgave/{oppgaveId}`) with `oppgaveId=O1` to inspect task context.

Existing-state shortcuts:
- Step 1 can be skipped when `O1` is already known and still assignable.
- The caller must still have caseworker role.

Parameter and value bindings:
- `oppgaveId=O1` from search is reused in the assignment path.
- Query `saksbehandler=S1` becomes the assignee.

Business result: The upstream task is assigned and the response returns the assigned task id/string.

Constraints and invariants:
- The controller sets `overstyrFordeling=false`.

Failure and exceptional cases:
- Failing function: `assign task`
  - Source discriminator: `already-assigned/task-not-found outcomes`
  - Failure condition: Task is missing or assigned to another caseworker.
  - Why it fails: Conflicting ownership is rejected.
  - Violated prerequisite or constraint: Task must exist and be assignable.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/oppgave/OppgaveService.kt — assignment method`

Implementation notes: Assignment is a distinct transition from reset and completion.

<a id="behavior-46"></a>
### Behavior 46: Reset external task assignment
Business goal: Clear assignment on a known external task.

API group boundary: Task state transition scoped by `oppgaveId`.

Domain context: A wrongly or temporarily assigned task can be returned to unassigned state.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: task exists with assignment state.
- Transition trigger: reset request.
- Intermediate states: upstream assignment is cleared.
- State after: task assignment is reset.
- Invalid or blocked transitions: invalid or unresettable task returns illegal-state response.

Required execution workflow:
1. Use function `search tasks` (`POST /api/oppgave/hent-oppgaver`) with body `RestFinnOppgaveRequest` to capture `oppgaveId=O1`.
2. Use function `reset task assignment` (`POST /api/oppgave/{oppgaveId}/tilbakestill`) with `oppgaveId=O1` to clear assignment.

Optional verification workflow:
1. Use function `retrieve journaling task data` (`GET /api/oppgave/{oppgaveId}`) with `oppgaveId=O1` to inspect task state.

Existing-state shortcuts:
- Step 1 can be skipped when `O1` is already known and belongs to the external task scope.

Parameter and value bindings:
- `oppgaveId=O1` from discovery is reused in the reset path.

Business result: The external task is no longer assigned to a caseworker.

Constraints and invariants:
- The task must exist and be mutable upstream.

Failure and exceptional cases:
- Failing function: `reset task assignment`
  - Source discriminator: `task-not-found/nonmutable outcome`
  - Failure condition: Task is missing or nonmutable.
  - Why it fails: Assignment cannot be cleared.
  - Violated prerequisite or constraint: Task must be resettable.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/oppgave/OppgaveService.kt — reset method`

Implementation notes: Reset is not a prerequisite for plain completion.

<a id="behavior-47"></a>
### Behavior 47: Retrieve journaling task data
Business goal: Gather task, person, minimal case, and optional journalpost context for manual journaling.

API group boundary: Atomic read scoped by `oppgaveId`.

Domain context: Journalføring needs enriched task context before linking or journaling documents.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: task exists and may reference actor/journalpost.
- Transition trigger: task-data read.
- Intermediate states: no local mutation.
- State after: enriched manual-journaling context is returned.
- Invalid or blocked transitions: missing task or upstream person/journal lookup failure blocks response.

Required execution workflow:
1. Use function `retrieve journaling task data` (`GET /api/oppgave/{oppgaveId}`) with `oppgaveId=O1` to retrieve task context.

Optional verification workflow:
None.

Existing-state shortcuts:
- Task search can be skipped when `O1` is already known.

Parameter and value bindings:
- Path `oppgaveId=O1` selects the external task.
- Task actor id and journalpost id, when present, are used to enrich response data.

Business result: The caller receives task details plus person, minimal case, and journalpost data when available.

Constraints and invariants:
- Missing optional actor or journalpost data yields partial enrichment, not necessarily endpoint failure.

Failure and exceptional cases:
- Failing function: `retrieve journaling task data`
  - Source discriminator: `journaling-task method branch for oppgaveId=O1 cannot be loaded`
  - Failure condition: `oppgaveId=O1` cannot be loaded.
  - Why it fails: `oppgaveService.hentOppgave` must return an upstream task.
  - Violated prerequisite or constraint: task id must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/oppgave/OppgaveController.kt — journaling-task method`
- Failing function: `retrieve journaling task data`
  - Source discriminator: `task/person/journalpost outcomes`
  - Failure condition: Task is missing, person unresolved, or journalpost missing.
  - Why it fails: Context cannot be assembled.
  - Violated prerequisite or constraint: All referenced objects must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/oppgave/OppgaveController.kt — journaling-task method`

Implementation notes: This is a read workflow; it does not complete or link the task.

<a id="behavior-48"></a>
### Behavior 48: Complete external task
Business goal: Close a known external task without linking a journalpost.

API group boundary: Task terminal transition scoped by `oppgaveId`.

Domain context: Casework tasks can be closed after required work is complete.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: task exists and is open.
- Transition trigger: completion request.
- Intermediate states: upstream task is loaded and completed.
- State after: task is closed.
- Invalid or blocked transitions: missing or already uncompletable task fails upstream.

Required execution workflow:
1. Use function `search tasks` (`POST /api/oppgave/hent-oppgaver`) with body `RestFinnOppgaveRequest` to capture `oppgaveId=O1`.
2. Use function `complete task` (`GET /api/oppgave/{oppgaveId}/ferdigstill`) with `oppgaveId=O1` to complete the task.

Optional verification workflow:
1. Use function `search tasks` (`POST /api/oppgave/hent-oppgaver`) with the same search body to verify the task no longer appears as open.

Existing-state shortcuts:
- Step 1 can be skipped when `O1` is already known and open.

Parameter and value bindings:
- `oppgaveId=O1` from discovery is reused in the completion path.

Business result: The upstream task is closed and the response says the task was closed.

Constraints and invariants:
- `complete task` is a mutating `GET`.

Failure and exceptional cases:
- Failing function: `complete task`
  - Source discriminator: `completion method branch for oppgaveId=O1 does not identify an open task`
  - Failure condition: `oppgaveId=O1` does not identify an open task.
  - Why it fails: the service cannot load or complete the task.
  - Violated prerequisite or constraint: task must exist and be completable.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/oppgave/OppgaveService.kt — completion method`
- Failing function: `complete task`
  - Source discriminator: `task-object id outcome`
  - Failure condition: Task is missing or has no id.
  - Why it fails: No concrete task can complete.
  - Violated prerequisite or constraint: Task with id must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/oppgave/OppgaveService.kt — completion method`

Implementation notes: Journalpost-link completion is modeled separately.

<a id="behavior-49"></a>
### Behavior 49: Complete task while linking a journalpost
Business goal: Link a journalpost to a case/treatment context and complete the related task.

API group boundary: The function binds `oppgaveId` to journalpost/fagsak data in the body.

Domain context: Manual journaling work can be completed only after the journalpost is associated with the correct case context.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: task and journalpost exist and are not completed/linked in this way.
- Transition trigger: complete-and-link request.
- Intermediate states: journalpost association is created/updated.
- State after: task is completed and journalpost link exists.
- Invalid or blocked transitions: missing task, missing journalpost, or invalid body fails.

Required execution workflow:
1. Use function `complete task and link journalpost` (`POST /api/oppgave/{oppgaveId}/ferdigstillOgKnyttjournalpost`) with `oppgaveId=O1` and body `RestFerdigstillOppgaveKnyttJournalpost` containing `journalpostId=J1`, `fagsakId=F1` to link and complete.

Optional verification workflow:
1. Use function `retrieve journaling task data` (`GET /api/oppgave/{oppgaveId}`) with `oppgaveId=O1` before completion to inspect task context.
2. Use function `retrieve journalpost` (`GET /api/journalpost/{journalpostId}/hent`) with `journalpostId=J1` to inspect journalpost metadata.

Existing-state shortcuts:
- Task search can be skipped when `oppgaveId=O1` is known.
- Journalpost lookup can be skipped when `journalpostId=J1` is trusted upstream.

Parameter and value bindings:
- `oppgaveId=O1` scopes task completion.
- Body `journalpostId=J1` is linked to case/treatment context in the body.
- `fagsakId=F1` must identify the case for the link.

Business result: Journalpost is connected to the case/treatment context and the task is completed.

Constraints and invariants:
- The body must provide enough link data for the integration service.
- Upstream task and journal systems must accept the state transition.

Failure and exceptional cases:
- Failing function: `complete task and link journalpost`
  - Source discriminator: `link/completion method branch for oppgaveId=O404 or journalpostId=J404 is invalid`
  - Failure condition: `oppgaveId=O404` or `journalpostId=J404` is invalid.
  - Why it fails: task/journal integration cannot complete/link missing resources.
  - Violated prerequisite or constraint: both upstream resources must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/journalføring/InnkommendeJournalføringService.kt — link/completion method`
- Failing function: `complete task and link journalpost`
  - Source discriminator: `task/journalpost/treatment outcomes`
  - Failure condition: Task, journalpost, or linked treatment is missing.
  - Why it fails: Link context is incomplete.
  - Violated prerequisite or constraint: All references must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/journalføring/InnkommendeJournalføringService.kt — link/completion method`

Implementation notes: This is an alternative to plain task completion and should be modeled separately.

<a id="behavior-50"></a>
### Behavior 50: Retrieve open extended-benefit deadlines
Business goal: Report deadlines for open extended child-benefit treatments.

API group boundary: Atomic operational report over open treatment task state.

Domain context: Operations can inspect deadline state for open extended-benefit treatments.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: open extended-benefit treatment/task state may exist.
- Transition trigger: deadline report request.
- Intermediate states: no local mutation.
- State after: deadline report string is returned.
- Invalid or blocked transitions: upstream task lookup failure blocks response.

Required execution workflow:
1. Use function `retrieve open treatment deadlines` (`POST /api/oppgave/hent-frister-for-apne-utvidet-barnetrygd-behandlinger`) with no body to retrieve deadline information.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup function is required when open task state already exists.

Parameter and value bindings:
- No request ids are reused; the service scans open extended-benefit treatment task state.

Business result: The caller receives current deadline information for open extended-benefit treatments.

Constraints and invariants:
- This is read-only and does not clear ownership markers.

Failure and exceptional cases:
None.

Implementation notes: This report is separate from the ownership-clear mutation.

<a id="behavior-51"></a>
### Behavior 51: Clear application task ownership
Business goal: Remove `behandlesAvApplikasjon` ownership markers from selected tasks.

API group boundary: Task mutation scoped by request body task ids.

Domain context: Tasks locked to application processing can be released for manual or other handling.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: tasks exist with possible application ownership marker.
- Transition trigger: clear ownership request.
- Intermediate states: each requested task is updated upstream.
- State after: ownership marker is removed for tasks the service can update.
- Invalid or blocked transitions: invalid task ids or upstream update failures block or reduce success.

Required execution workflow:
1. Use function `clear application task ownership` (`POST /api/oppgave/fjern-behandles-av-applikasjon`) with body `[O1,O2]` to clear ownership markers.

Optional verification workflow:
1. Use function `retrieve journaling task data` (`GET /api/oppgave/{oppgaveId}`) with `oppgaveId=O1` to inspect a selected task.

Existing-state shortcuts:
- Task discovery can be skipped when task ids are already known.
- The task ids must still be valid upstream task ids.

Parameter and value bindings:
- Body task ids are the exact tasks whose ownership marker is updated.

Business result: The service reports which tasks had `behandlesAvApplikasjon` removed.

Constraints and invariants:
- The endpoint does not itself discover task ids.

Failure and exceptional cases:
None.

Implementation notes: This is an independent repair action, not part of deadline reporting.

<a id="behavior-52"></a>
### Behavior 52: Inspect journalpost and retrieve documents
Business goal: Read journalpost metadata and fetch documents in resource/PDF form.

API group boundary: Functions share `journalpostId` and document ids returned by journalpost metadata.

Domain context: Manual journaling and casework require reading incoming documents.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: journalpost and document exist in journal system.
- Transition trigger: metadata and document read requests.
- Intermediate states: no local mutation.
- State after: metadata and document bytes are returned.
- Invalid or blocked transitions: missing journalpost/document id or upstream journal failure blocks read.

Required execution workflow:
1. Use function `retrieve journalpost` (`GET /api/journalpost/{journalpostId}/hent`) with `journalpostId=J1` to retrieve metadata and capture `dokumentInfoId=DOK1`.
2. Use function `retrieve journal document resource` (`GET /api/journalpost/{journalpostId}/hent/{dokumentInfoId}`) with `journalpostId=J1`, `dokumentInfoId=DOK1` to retrieve the document resource.
3. Use function `retrieve journal document PDF` (`GET /api/journalpost/{journalpostId}/dokument/{dokumentInfoId}`) with `journalpostId=J1`, `dokumentInfoId=DOK1` to retrieve the document as PDF.

Optional verification workflow:
1. Use function `list user journalposts` (`POST /api/journalpost/for-bruker`) with body `PersonIdent=P1` to list journalposts for a person and find `journalpostId=J1`.

Existing-state shortcuts:
- Metadata retrieval can be skipped when both `journalpostId` and `dokumentInfoId` are trusted from upstream.
- The document id must still belong to the journalpost.

Parameter and value bindings:
- `dokumentInfoId=DOK1` comes from retrieved journalpost metadata and is reused for document fetches.
- `journalpostId=J1` scopes both document calls.

Business result: The caller gets journalpost metadata and document bytes in both wrapped resource and PDF media forms.

Constraints and invariants:
- Document id must belong to the journalpost.
- Upstream journal system is authoritative.

Failure and exceptional cases:
- Failing function: `retrieve journal document resource`
  - Source discriminator: `JournalføringController branch for dokumentInfoId=DOK404 does not belong to journalpostId=J1`
  - Failure condition: `dokumentInfoId=DOK404` does not belong to `journalpostId=J1`.
  - Why it fails: journal integration cannot retrieve the document.
  - Violated prerequisite or constraint: document id must come from journalpost metadata.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/journalføring/JournalføringController.kt — retrieval methods`
- Failing function: `retrieve journalpost`
  - Source discriminator: `journalpost-not-found outcome`
  - Failure condition: Journalpost is missing.
  - Why it fails: No metadata can return.
  - Violated prerequisite or constraint: Journalpost must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/journalføring/JournalføringController.kt — retrieval methods`
- Failing function: `retrieve journal document resource`
  - Source discriminator: `document-membership outcome`
  - Failure condition: Document is missing or not in journalpost.
  - Why it fails: Relationship cannot resolve.
  - Violated prerequisite or constraint: Document must belong to journalpost.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/journalføring/JournalføringController.kt — retrieval methods`
- Failing function: `retrieve journal document PDF`
  - Source discriminator: `document-membership outcome`
  - Failure condition: Document is missing or not in journalpost.
  - Why it fails: Relationship cannot resolve.
  - Violated prerequisite or constraint: Document must belong to journalpost.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/journalføring/JournalføringController.kt — retrieval methods`

Implementation notes: Resource and PDF document retrieval are separate exposed representations of document bytes.

<a id="behavior-53"></a>
### Behavior 53: Journal an incoming journalpost
Business goal: Journal an incoming journalpost to the correct unit/task context.

API group boundary: The function binds `journalpostId`, `oppgaveId`, `journalfoerendeEnhet`, and document metadata.

Domain context: Incoming documents must be journaled before task/case handling can be finalized.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: journalpost and task exist and journalpost is not journaled by this request.
- Transition trigger: journalføring request.
- Intermediate states: document metadata is validated; journalføring service updates journalpost state.
- State after: journalpost is journaled.
- Invalid or blocked transitions: blank document title, missing task/journalpost, or journal service failure blocks transition.

Required execution workflow:
1. Use function `journal journalpost` (`POST /api/journalpost/{journalpostId}/journalfør/{oppgaveId}`) with `journalpostId=J1`, `oppgaveId=O1`, query `journalfoerendeEnhet=E1`, and body `RestJournalføring` where every `dokumenter[].dokumentTittel` is non-empty to journal the post.

Optional verification workflow:
1. Use function `retrieve journalpost` (`GET /api/journalpost/{journalpostId}/hent`) with `journalpostId=J1` to inspect metadata after journaling.
2. Use function `retrieve journaling task data` (`GET /api/oppgave/{oppgaveId}`) with `oppgaveId=O1` to inspect task context.

Existing-state shortcuts:
- Journalpost/task lookup can be skipped when ids are trusted from upstream.
- The ids must still refer to resources that can be journaled together.

Parameter and value bindings:
- `journalpostId=J1` and `oppgaveId=O1` bind journalpost to task.
- `journalfoerendeEnhet=E1` supplies journaling unit.
- Body document titles are mandatory validation inputs.

Business result: The journalpost is journaled with supplied document metadata and unit context.

Constraints and invariants:
- Every document title must be non-empty.
- Upstream journal/task state must allow journaling.

Failure and exceptional cases:
- Failing function: `journal journalpost`
  - Source discriminator: `journaling method branch for any dokumentTittel is blank or missing`
  - Failure condition: any `dokumentTittel` is blank or missing.
  - Why it fails: controller throws functional error before journalføring.
  - Violated prerequisite or constraint: all documents must have titles.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/journalføring/InnkommendeJournalføringService.kt — journaling method`
- Failing function: `journal journalpost`
  - Source discriminator: `journalpost/treatment outcomes`
  - Failure condition: Journalpost or linked treatment is missing.
  - Why it fails: Journaling context cannot establish.
  - Violated prerequisite or constraint: Both objects must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/journalføring/InnkommendeJournalføringService.kt — journaling method`
- Failing function: `journal journalpost`
  - Source discriminator: `post-update completeness guard`
  - Failure condition: After external update, documents or received date remain absent.
  - Why it fails: The incomplete journalpost is rejected.
  - Violated prerequisite or constraint: Documents and received date must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/integrasjoner/journalføring/InnkommendeJournalføringService.kt — journaling method`
  - Persisted outcome despite failure: The external journalpost update has already occurred.

Implementation notes: The title validation is implemented directly in the controller.

<a id="behavior-54"></a>
### Behavior 54: Retrieve feature toggles
Business goal: Return enabled/disabled state for requested feature toggles.

API group boundary: Atomic read over feature-toggle ids.

Domain context: UI and services can gate behavior by feature state.

Starting point: `No prior service state`

State transition summary:
- State before: feature flags have configured values.
- Transition trigger: toggle lookup request.
- Intermediate states: no local mutation.
- State after: toggle values are returned.
- Invalid or blocked transitions: no implementation-backed domain failure identified.

Required execution workflow:
1. Use function `retrieve feature toggles` (`POST /api/feature`) with body `[T1,T2]` to retrieve toggle values.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup endpoint is required; toggle configuration is external/environment state.

Parameter and value bindings:
- Request body toggle ids are mapped directly to returned boolean states.

Business result: The caller receives feature-toggle values.

Constraints and invariants:
- This is read-only.

Failure and exceptional cases:
None.

Implementation notes: This is separate from person access checking.

<a id="behavior-55"></a>
### Behavior 55: Check person access
Business goal: Determine whether the current caller may access a person and see the person’s discretion code.

API group boundary: Atomic access-control lookup scoped by `brukerIdent` and caller context.

Domain context: Caseworker UI and integrations need explicit access feedback before showing person/case data.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: person and caller access state exist upstream.
- Transition trigger: access check request.
- Intermediate states: no local mutation.
- State after: access result and address-protection/discretion information are returned.
- Invalid or blocked transitions: unknown person or upstream access-service failure blocks response.

Required execution workflow:
1. Use function `check person access` (`POST /api/tilgang`) with body `brukerIdent=P1` and current caller context `C1` to check access.

Optional verification workflow:
None.

Existing-state shortcuts:
- No local case setup is required when person and access state exist upstream.

Parameter and value bindings:
- Body `brukerIdent=P1` is evaluated against the authenticated caller context.

Business result: Caller receives an access decision and discretion/address-protection information.

Constraints and invariants:
- Result depends on caller identity and upstream access rules, not only request body.

Failure and exceptional cases:
None.

Implementation notes: This function is not a feature-toggle lookup and has no response binding to `retrieve feature toggles`.

<a id="behavior-56"></a>
### Behavior 56: Queue identity event handling
Business goal: Create asynchronous work for a new identity/PDL identity event.

API group boundary: Atomic event-ingestion workflow scoped by `PersonIdent`.

Domain context: Identity changes must be processed asynchronously to update affected case state.

Starting point: `No prior service state`

State transition summary:
- State before: identity event has not been queued.
- Transition trigger: identity event request.
- Intermediate states: task payload is persisted.
- State after: identity handling task exists.
- Invalid or blocked transitions: invalid payload or task persistence failure blocks queuing.

Required execution workflow:
1. Use function `handle identity event` (`POST /api/ident`) with body `ident=P1` to queue identity handling.

Optional verification workflow:
None.

Existing-state shortcuts:
- Direct task-table setup can represent already queued work, but the event-ingestion action cannot be skipped for API validation.

Parameter and value bindings:
- Body identity is serialized into the task payload.

Business result: Asynchronous identity processing is queued.

Constraints and invariants:
- The endpoint returns task-queue result, not completed case mutation.

Failure and exceptional cases:
None.

Implementation notes: This is independent from transitional-benefit event handling.

<a id="behavior-57"></a>
### Behavior 57: Queue transitional-benefit event handling
Business goal: Create asynchronous work for a transitional-benefit decision event.

API group boundary: Atomic event-ingestion workflow scoped by event `ident`.

Domain context: Transitional-benefit decisions can affect small child supplement handling.

Starting point: `No prior service state`

State transition summary:
- State before: transitional-benefit event has not been queued.
- Transition trigger: event request.
- Intermediate states: task payload is persisted.
- State after: processing task exists.
- Invalid or blocked transitions: invalid payload or task persistence failure blocks queuing.

Required execution workflow:
1. Use function `handle transitional benefit event` (`POST /api/overgangsstonad`) with body `ident=P1` to queue event handling.

Optional verification workflow:
None.

Existing-state shortcuts:
- Direct task setup can represent an already queued event, but the API ingestion action remains the core behavior.

Parameter and value bindings:
- Body `ident=P1` is serialized into the task payload.

Business result: Asynchronous transitional-benefit processing is queued.

Constraints and invariants:
- No immediate treatment mutation is guaranteed in the response.

Failure and exceptional cases:
None.

Implementation notes: This is independent from identity-event processing.

<a id="behavior-58"></a>
### Behavior 58: Check rate-change eligibility for one case
Business goal: Determine whether a case can undergo manual rate change.

API group boundary: Atomic read scoped by `fagsakId`.

Domain context: Caseworkers/admins can preflight manual rate-change execution.

Starting point: `Existing service state`

State transition summary:
- State before: case exists with rate/benefit state.
- Transition trigger: eligibility check.
- Intermediate states: no local mutation.
- State after: boolean eligibility result is returned.
- Invalid or blocked transitions: missing case or service validation failure blocks response.

Required execution workflow:
1. Use function `check rate change eligibility` (`GET /api/satsendring/{fagsakId}/kan-kjore-satsendring`) with `fagsakId=F1` to check eligibility.

Optional verification workflow:
None.

Existing-state shortcuts:
- Case creation can be skipped when `F1` already exists and belongs to the target case.

Parameter and value bindings:
- Path `fagsakId=F1` scopes the eligibility check.

Business result: The caller receives whether rate change can run for `F1`.

Constraints and invariants:
- This read does not queue or run a rate change.

Failure and exceptional cases:
None.

Implementation notes: Synchronous execution is modeled separately.

<a id="behavior-59"></a>
### Behavior 59: Queue rate change for one case
Business goal: Queue rate-change processing for a single case.

API group boundary: Atomic asynchronous transition scoped by `fagsakId`.

Domain context: A single ongoing case can be scheduled for rate update.

Starting point: `Existing service state`

State transition summary:
- State before: case exists and may have outdated rates.
- Transition trigger: single-case queue request.
- Intermediate states: a rate-change task is created.
- State after: asynchronous rate-change work exists for the case.
- Invalid or blocked transitions: missing case or task creation failure blocks queuing.

Required execution workflow:
1. Use function `trigger rate change for case` (`GET /api/satsendring/kjorsatsendring/{fagsakId}`) with `fagsakId=F1` to queue rate change for that case.

Optional verification workflow:
1. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) with `fagsakId=F1` after task execution to inspect resulting state.

Existing-state shortcuts:
- Case setup can be skipped when `F1` is already known.

Parameter and value bindings:
- Path `fagsakId=F1` is serialized into the rate-change task.

Business result: A rate-change task is queued for one case.

Constraints and invariants:
- This is a mutating `GET`.
- The response does not mean the rate change is completed.

Failure and exceptional cases:
None.

Implementation notes: Multi-case and identity-based queueing are separate entry points.

<a id="behavior-60"></a>
### Behavior 60: Queue rate change for multiple cases
Business goal: Queue rate-change processing for a supplied set of cases.

API group boundary: Atomic asynchronous bulk transition scoped by body `fagsakId` set.

Domain context: Operations can schedule rate updates for a known case set.

Starting point: `Existing service state`

State transition summary:
- State before: requested cases exist.
- Transition trigger: multi-case queue request.
- Intermediate states: tasks are created for supplied cases.
- State after: asynchronous rate-change work exists for the case set.
- Invalid or blocked transitions: invalid ids or per-case task failures may reduce success.

Required execution workflow:
1. Use function `trigger rate change for cases` (`POST /api/satsendring/kjorsatsendring`) with body `[F1,F2]` to queue rate changes for the supplied cases.

Optional verification workflow:
None.

Existing-state shortcuts:
- Case lookup can be skipped when `F1` and `F2` are already trusted case ids.

Parameter and value bindings:
- Body case ids are the exact targets for task creation.

Business result: Rate-change tasks are queued for the supplied case ids.

Constraints and invariants:
- This endpoint does not discover cases by identity.

Failure and exceptional cases:
None.

Implementation notes: This is split from `trigger rate change from identities` because body values have different domain meaning.

<a id="behavior-61"></a>
### Behavior 61: Run synchronous rate change for one case
Business goal: Execute rate change immediately for one eligible case.

API group boundary: Concrete validation-to-execution chain scoped by the same `fagsakId`.

Domain context: Manual synchronous rate change applies changes now instead of queuing a task.

Starting point: `Existing service state`

State transition summary:
- State before: case exists and is eligible.
- Transition trigger: eligibility check followed by synchronous run.
- Intermediate states: rate-change service recalculates/updates treatment/case state.
- State after: case has been synchronously rate-changed or the execution fails.
- Invalid or blocked transitions: ineligible case blocks execution.

Required execution workflow:
1. Use function `check rate change eligibility` (`GET /api/satsendring/{fagsakId}/kan-kjore-satsendring`) with `fagsakId=F1` to obtain `kanKjøre=true`.
2. Use function `run synchronous rate change` (`PUT /api/satsendring/{fagsakId}/kjor-satsendring-synkront`) with `fagsakId=F1` to execute the rate change.

Optional verification workflow:
1. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) with `fagsakId=F1` to inspect resulting treatment/case state.

Existing-state shortcuts:
- Eligibility preknowledge can skip step 1 for operational use, but the same case must still be eligible or step 2 may fail.

Parameter and value bindings:
- `fagsakId=F1` is reused across validation and execution.
- The boolean from step 1 is the decision input for step 2.

Business result: The case is rate-changed synchronously.

Constraints and invariants:
- Synchronous execution is not the same state transition as queueing.

Failure and exceptional cases:
- Failing function: `run synchronous rate change`
  - Source discriminator: `synchronous rate change branch for F1 is not eligible for rate change`
  - Failure condition: `F1` is not eligible for rate change.
  - Why it fails: rate-change service validates business eligibility.
  - Violated prerequisite or constraint: eligible ongoing case/rate state.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/autovedtak/satsendring/SatsendringService.kt — synchronous rate change`
- Failing function: `run synchronous rate change`
  - Source discriminator: `latest-rate guard`
  - Failure condition: Case already has latest rate.
  - Why it fails: Duplicate transition is invalid.
  - Violated prerequisite or constraint: Case must lack latest rate.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/autovedtak/satsendring/SatsendringService.kt — synchronous rate change`
- Failing function: `run synchronous rate change`
  - Source discriminator: `prior-treatment guard`
  - Failure condition: No previous decided ongoing treatment supplies basis.
  - Why it fails: Revision cannot derive.
  - Violated prerequisite or constraint: A decided ongoing predecessor must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/autovedtak/satsendring/SatsendringService.kt — synchronous rate change`
- Failing function: `run synchronous rate change`
  - Source discriminator: `SatsendringSvar lifecycle outcomes`
  - Failure condition: Rate change is done or treatment cannot be locked, paused, or bypassed.
  - Why it fails: The workflow reports rejection.
  - Violated prerequisite or constraint: Case and treatment must be eligible.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/autovedtak/satsendring/SatsendringService.kt — synchronous rate change`

Implementation notes: The required workflow binds the same `fagsakId` across check and execution.

<a id="behavior-62"></a>
### Behavior 62: Queue rate change from identities
Business goal: Discover relevant cases from supplied identities and queue rate-change tasks.

API group boundary: Atomic identity-based queueing scoped by person identifiers rather than explicit case ids.

Domain context: Operations can schedule rate changes when they have identities instead of case ids.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: supplied identities may map to old-rate cases.
- Transition trigger: identity-list queue request.
- Intermediate states: service resolves identities and creates tasks for matching cases.
- State after: rate-change tasks exist for resolved candidates.
- Invalid or blocked transitions: unresolvable identities do not produce target tasks.

Required execution workflow:
1. Use function `trigger rate change from identities` (`POST /api/satsendring/kjorsatsendringForListeMedIdenter`) with body `[P1,P2]` to resolve identities and queue matching rate changes.

Optional verification workflow:
None.

Existing-state shortcuts:
- Identity resolution can be prepared upstream, but the endpoint itself accepts identities and performs the discovery.

Parameter and value bindings:
- Body identities are not case ids; they are resolved to cases inside service logic.

Business result: Rate-change work is queued for old-rate cases found from supplied identities.

Constraints and invariants:
- A person without a matching case produces no meaningful task for that person.

Failure and exceptional cases:
- Failing function: `trigger rate change from identities`
  - Source discriminator: `actor-not-found outcome`
  - Failure condition: Identity cannot resolve to actor.
  - Why it fails: Candidate discovery cannot scope.
  - Violated prerequisite or constraint: Identity must resolve to actor.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/autovedtak/satsendring/SatsendringService.kt — identity resolution`

Implementation notes: This is not equivalent to passing case ids to `trigger rate change for cases`.

<a id="behavior-63"></a>
### Behavior 63: Queue technical dismissal for long-deadline treatments
Business goal: Create dismissal tasks for treatments with deadlines beyond a validation date.

API group boundary: The function is a rate-change/maintenance task workflow scoped by treatment ids and validation date.

Domain context: During rate-change maintenance, treatments with long deadlines can be queued for technical dismissal.

Starting point: `Existing service state`

State transition summary:
- State before: treatments exist with task deadlines.
- Transition trigger: dismissal queue request.
- Intermediate states: validation date is checked and tasks are created for supplied treatments.
- State after: dismissal tasks exist.
- Invalid or blocked transitions: invalid or too-early validation date fails.

Required execution workflow:
1. Use function `queue long-deadline dismissals` (`POST /api/satsendring/henleggBehandlingerMedLangFristSenereEnn/{valideringsdato}`) with `valideringsdato=D_after_one_month` and body `["B1","B2"]` to queue technical dismissal tasks.

Optional verification workflow:
1. Use function `search tasks` (`POST /api/oppgave/hent-oppgaver`) with task query for the treatment ids to inspect created tasks.

Existing-state shortcuts:
- Treatment discovery can be skipped when treatment ids are already known.
- Direct database setup must preserve task deadline state for those treatments.

Parameter and value bindings:
- Path `valideringsdato` controls validation.
- Body treatment id strings are task payload targets.

Business result: Technical dismissal tasks are queued for selected treatments.

Constraints and invariants:
- Validation date must parse and be after one month from current date.

Failure and exceptional cases:
- Failing function: `queue long-deadline dismissals`
  - Source discriminator: `dismissal selection branch for valideringsdato is invalid or too early`
  - Failure condition: `valideringsdato` is invalid or too early.
  - Why it fails: controller returns bad request.
  - Violated prerequisite or constraint: date must be a valid future maintenance threshold.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterController.kt — dismissal selection`

Implementation notes: This queues work; it does not immediately dismiss the treatments.

<a id="behavior-64"></a>
### Behavior 64: Identify ongoing cases without latest rate
Business goal: Start background discovery of ongoing cases missing the latest rate.

API group boundary: The function is a service-wide rate-change discovery job.

Domain context: Operations need a call id to track missing-rate analysis.

Starting point: `Existing service state`

State transition summary:
- State before: ongoing cases may be missing latest rate.
- Transition trigger: async search request.
- Intermediate states: background analysis is started.
- State after: call id is returned.
- Invalid or blocked transitions: task/background infrastructure failure blocks job start.

Required execution workflow:
1. Use function `find cases without latest rate` (`POST /api/satsendring/saker-uten-sats`) with no case-specific body to start async search and capture `callId=CID1`.

Optional verification workflow:
None.

Existing-state shortcuts:
- No specific case setup is required for the service-wide scan.
- Direct database setup can seed cases without latest rate for the job to find.

Parameter and value bindings:
- Returned `callId` is the only API-visible correlation value.

Business result: A background analysis starts and returns a call id.

Constraints and invariants:
- Job result retrieval is not available through a function in `full-behavior.md`.

Failure and exceptional cases:
None.

Implementation notes: Swagger includes shared `/api/task/callId/{callId}`, but no project controller implementation is present and it was not converted to a function.

<a id="behavior-65"></a>
### Behavior 65: Run consistency reconciliation dry run
Business goal: Queue economy consistency reconciliation without sending to the economy system.

API group boundary: Atomic reconciliation task trigger for dry-run mode.

Domain context: Operations can validate reconciliation effects before real sending.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: reconciliation has not been queued.
- Transition trigger: dry-run request.
- Intermediate states: dry-run task is created.
- State after: dry-run reconciliation work exists.
- Invalid or blocked transitions: task/batch persistence failure blocks queuing.

Required execution workflow:
1. Use function `run consistency dry run` (`POST /api/konsistensavstemming/dryrun`) with no body to queue dry-run reconciliation.

Optional verification workflow:
None.

Existing-state shortcuts:
- Direct task setup can represent existing dry-run work, but the API trigger cannot be skipped for this behavior.

Parameter and value bindings:
- No request body values are reused.

Business result: A dry-run reconciliation task is queued.

Constraints and invariants:
- It does not send reconciliation to the economy system.

Failure and exceptional cases:
None.

Implementation notes: Real reconciliation is separate.

<a id="behavior-66"></a>
### Behavior 66: Run real consistency reconciliation
Business goal: Queue economy consistency reconciliation that sends to the economy system.

API group boundary: Atomic reconciliation task trigger using `triggerTid`.

Domain context: Operations can start the actual economy reconciliation run.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: real reconciliation has not been queued for the trigger time.
- Transition trigger: real-run request.
- Intermediate states: real reconciliation task is created with reconciliation date.
- State after: economy-sending reconciliation work exists.
- Invalid or blocked transitions: missing/invalid `triggerTid` or persistence failure blocks queuing.

Required execution workflow:
1. Use function `run consistency reconciliation` (`POST /api/konsistensavstemming/run`) with body `triggerTid=T1` to queue real reconciliation.

Optional verification workflow:
None.

Existing-state shortcuts:
- Direct task setup can represent an already queued real run, but it is not equivalent to calling the endpoint.

Parameter and value bindings:
- Body `triggerTid=T1` becomes task trigger time and reconciliation date.

Business result: Real consistency reconciliation is queued for economy sending.

Constraints and invariants:
- This behavior sends to economy when the task executes.

Failure and exceptional cases:
None.

Implementation notes: Dry run and real run are separate capabilities.

<a id="behavior-67"></a>
### Behavior 67: Retrieve internal and application statistics
Business goal: Read aggregate service statistics and application counts.

API group boundary: Both functions are internal statistics read models.

Domain context: Operational reporting needs counts for cases, unfinished treatments, and applications over time.

Starting point: `Existing service state`

State transition summary:
- State before: service has case/treatment/application data.
- Transition trigger: statistics read.
- Intermediate states: no mutation.
- State after: aggregate statistics are returned.
- Invalid or blocked transitions: invalid date parameters or repository failure blocks response.

Required execution workflow:
1. Use function `retrieve internal statistics` (`GET /api/internstatistikk`) with no parameters to retrieve aggregate counts.
2. Use function `retrieve application statistics` (`GET /api/internstatistikk/antallSoknader`) with query `fom=D1`, `tom=D2` to retrieve application statistics.

Optional verification workflow:
None.

Existing-state shortcuts:
- No case-specific setup is required.
- Omitting date parameters uses implementation defaults for the current four-month period.

Parameter and value bindings:
- `fom` and `tom` define application statistics range.

Business result: Internal statistics are returned for operational reporting.

Constraints and invariants:
- Date query parameters must parse as dates.

Failure and exceptional cases:
None.

Implementation notes: These reads do not update statistics sent-state.

<a id="behavior-68"></a>
### Behavior 68: Retrieve treatment statistics payload
Business goal: Map one treatment to a DVH treatment statistics payload.

API group boundary: Atomic read scoped by `behandlingId`.

Domain context: Statistics publishing and debugging need the mapped treatment message.

Starting point: `Existing service state`

State transition summary:
- State before: treatment exists with enough state for mapping.
- Transition trigger: treatment statistics read.
- Intermediate states: no local mutation.
- State after: mapped treatment statistics payload is returned.
- Invalid or blocked transitions: incomplete treatment state or mapping failure blocks response.

Required execution workflow:
1. Use function `retrieve treatment statistics` (`GET /api/saksstatistikk/behandling/{behandlingId}`) with `behandlingId=B1` to map treatment statistics.

Optional verification workflow:
None.

Existing-state shortcuts:
- Treatment setup can be skipped when `B1` already exists and is mappable.

Parameter and value bindings:
- Path `behandlingId=B1` selects the treatment message.

Business result: The caller receives a treatment-level statistics payload.

Constraints and invariants:
- Mapping can fail if required treatment fields are missing.

Failure and exceptional cases:
- Failing function: `retrieve treatment statistics`
  - Source discriminator: `treatment mapping branch for missing/incomplete treatment state`
  - Failure condition: missing/incomplete treatment state.
  - Why it fails: controller logs and rethrows mapping errors.
  - Violated prerequisite or constraint: treatment must be mappable.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/statistikk/saksstatistikk/SaksstatistikkService.kt — treatment mapping`
- Failing function: `retrieve treatment statistics`
  - Source discriminator: `treatment/unit outcomes`
  - Failure condition: Treatment or handling unit is missing.
  - Why it fails: Statistics context cannot map.
  - Violated prerequisite or constraint: Treatment and unit must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/statistikk/saksstatistikk/SaksstatistikkService.kt — treatment mapping`

Implementation notes: Case statistics and sent-registration are separate behaviors.

<a id="behavior-69"></a>
### Behavior 69: Retrieve case statistics payload
Business goal: Map one case to a DVH case statistics payload.

API group boundary: Atomic read scoped by `fagsakId`.

Domain context: Statistics publishing and debugging need the mapped case message.

Starting point: `Existing service state`

State transition summary:
- State before: case exists with enough state for mapping.
- Transition trigger: case statistics read.
- Intermediate states: no local mutation.
- State after: mapped case statistics payload is returned.
- Invalid or blocked transitions: missing case or mapping failure blocks response.

Required execution workflow:
1. Use function `retrieve case statistics` (`GET /api/saksstatistikk/sak/{fagsakId}`) with `fagsakId=F1` to map case statistics.

Optional verification workflow:
None.

Existing-state shortcuts:
- Case setup can be skipped when `F1` already exists.

Parameter and value bindings:
- Path `fagsakId=F1` selects the case message.

Business result: The caller receives a case-level statistics payload.

Constraints and invariants:
- Mapping is read-only and does not mark messages sent.

Failure and exceptional cases:
- Failing function: `retrieve case statistics`
  - Source discriminator: `case mapping branch for F1 cannot be mapped`
  - Failure condition: `F1` cannot be mapped.
  - Why it fails: statistics service requires valid case state.
  - Violated prerequisite or constraint: case must exist and be mappable.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/statistikk/saksstatistikk/SaksstatistikkService.kt — case mapping`
- Failing function: `retrieve case statistics`
  - Source discriminator: `case/owner outcomes`
  - Failure condition: Case or owner person is missing.
  - Why it fails: Statistics context is absent.
  - Violated prerequisite or constraint: Case and owner must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/statistikk/saksstatistikk/SaksstatistikkService.kt — case mapping`

Implementation notes: Treatment and case mappings use different aggregate ids.

<a id="behavior-70"></a>
### Behavior 70: Register statistics message as sent
Business goal: Persist that an externally sent statistics message has been sent and should not be resent.

API group boundary: Atomic statistics sent-state mutation scoped by message metadata and JSON payload.

Domain context: Statistics outbox/mellomlagring needs sent markers to avoid duplicate sending.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: sent marker for the message does not exist.
- Transition trigger: sent-registration request.
- Intermediate states: JSON fields are parsed.
- State after: intermediate statistics record is stored with sent timestamp.
- Invalid or blocked transitions: required JSON fields missing blocks registration.

Required execution workflow:
1. Use function `register statistics sent` (`POST /api/saksstatistikk/registrer-sendt-fra-statistikk`) with body `offset=O1`, `type=SAK`, `json=J1`, `sendtTidspunkt=T1` to register sent state.

Optional verification workflow:
None.

Existing-state shortcuts:
- Message production can be external; this API only needs the sent metadata and JSON payload.

Parameter and value bindings:
- Body `json=J1` must contain `funksjonellId`, `versjon`, and the id field matching `type`.
- `offset` and `sendtTidspunkt` are persisted with the sent marker.

Business result: The statistics message is marked as sent.

Constraints and invariants:
- Required JSON nodes must exist.

Failure and exceptional cases:
None.

Implementation notes: This does not generate the statistics payload itself.

<a id="behavior-71"></a>
### Behavior 71: Retrieve benefit statistics decisions
Business goal: Map treatment ids to DVH V2 benefit-statistics decision payloads.

API group boundary: Atomic read scoped by body treatment ids.

Domain context: Operators and publishers can inspect benefit-statistics payloads for implemented treatments.

Starting point: `Existing service state`

State transition summary:
- State before: treatment ids exist and are mappable.
- Transition trigger: payload retrieval request.
- Intermediate states: no local mutation.
- State after: decision payloads are returned.
- Invalid or blocked transitions: missing or unmappable treatment ids fail mapping.

Required execution workflow:
1. Use function `retrieve benefit statistics decisions` (`POST /api/stonadsstatistikk/vedtakV2`) with body `[B1,B2]` to retrieve benefit-statistics decisions.

Optional verification workflow:
None.

Existing-state shortcuts:
- Treatment setup can be skipped when ids are known and mappable.

Parameter and value bindings:
- Body treatment ids select the decision payloads.

Business result: The caller receives DVH V2 benefit-statistics payloads.

Constraints and invariants:
- This read does not queue publishing.

Failure and exceptional cases:
- Failing function: `retrieve benefit statistics decisions`
  - Source discriminator: `decision mapping branch for a treatment id cannot be mapped`
  - Failure condition: a treatment id cannot be mapped.
  - Why it fails: statistics mapping requires complete treatment/decision state.
  - Violated prerequisite or constraint: mappable treatment ids.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/statistikk/stønadsstatistikk/StønadsstatistikkService.kt — decision mapping`
- Failing function: `retrieve benefit statistics decisions`
  - Source discriminator: `mapping prerequisites`
  - Failure condition: Treatment, active person basis, decision date, or benefit-person reference is absent.
  - Why it fails: Decision payload cannot build.
  - Violated prerequisite or constraint: Complete references are required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/statistikk/stønadsstatistikk/StønadsstatistikkService.kt — decision mapping`

Implementation notes: Publication queueing is modeled separately.

<a id="behavior-72"></a>
### Behavior 72: Queue unsent benefit statistics
Business goal: Queue publication tasks for supplied treatments that have not already been sent.

API group boundary: Atomic publish-task creation scoped by treatment ids and sent-state filter.

Domain context: Normal benefit-statistics publishing avoids duplicate sends.

Starting point: `Existing service state`

State transition summary:
- State before: treatment ids exist and may or may not have sent-state records.
- Transition trigger: unsent publication request.
- Intermediate states: sent-state filter is applied and tasks are created for unsent treatments.
- State after: publish tasks exist for unsent eligible treatments.
- Invalid or blocked transitions: mapping/task failures block affected treatments.

Required execution workflow:
1. Use function `queue unsent benefit statistics` (`POST /api/stonadsstatistikk/send-til-dvh`) with body `[B1,B2]` to queue unsent benefit-statistics publication.

Optional verification workflow:
1. Use function `retrieve benefit statistics decisions` (`POST /api/stonadsstatistikk/vedtakV2`) with body `[B1,B2]` to inspect payloads.

Existing-state shortcuts:
- Treatment discovery can be skipped when ids are known.
- Existing sent-state records may intentionally prevent task creation.

Parameter and value bindings:
- Body treatment ids are publication candidates.
- Sent-state lookup controls which ids become tasks.

Business result: Publish tasks are created only for unsent eligible treatments.

Constraints and invariants:
- Already sent treatments are skipped.

Failure and exceptional cases:
- Failing function: `queue unsent benefit statistics`
  - Source discriminator: `mapping prerequisites`
  - Failure condition: Treatment, active basis, decision date, or benefit-person reference is absent.
  - Why it fails: Publication payload cannot build.
  - Violated prerequisite or constraint: Complete references are required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/statistikk/stønadsstatistikk/StønadsstatistikkService.kt — queue method`

Implementation notes: Manual publication ignores the sent-state filter and is modeled separately.

<a id="behavior-73"></a>
### Behavior 73: Manually queue benefit statistics
Business goal: Queue benefit-statistics publication for supplied treatments without the normal sent-state filter.

API group boundary: Atomic manual publish-task creation scoped by treatment ids.

Domain context: Operators can force statistics republishing when needed.

Starting point: `Existing service state`

State transition summary:
- State before: treatment ids exist and may already have sent-state records.
- Transition trigger: manual publication request.
- Intermediate states: tasks are created without skipping already sent ids.
- State after: publish tasks exist for supplied treatments where mapping succeeds.
- Invalid or blocked transitions: mapping/task failures block affected treatments.

Required execution workflow:
1. Use function `queue benefit statistics manually` (`POST /api/stonadsstatistikk/send-til-dvh-manuell`) with body `[B1,B2]` to manually queue benefit statistics.

Optional verification workflow:
1. Use function `retrieve benefit statistics decisions` (`POST /api/stonadsstatistikk/vedtakV2`) with body `[B1,B2]` to inspect payloads.

Existing-state shortcuts:
- Treatment setup can be skipped when ids are trusted.

Parameter and value bindings:
- Body treatment ids are direct publish targets.

Business result: Benefit-statistics publish tasks are manually queued.

Constraints and invariants:
- Sent-state is not used as a skip filter.

Failure and exceptional cases:
- Failing function: `queue benefit statistics manually`
  - Source discriminator: `mapping prerequisites`
  - Failure condition: Treatment, active basis, decision date, or benefit-person reference is absent.
  - Why it fails: Publication payload cannot build.
  - Violated prerequisite or constraint: Complete references are required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/statistikk/stønadsstatistikk/StønadsstatistikkService.kt — queue method`

Implementation notes: This differs from `queue unsent benefit statistics` by duplicate-send protection.

<a id="behavior-74"></a>
### Behavior 74: Resend manual migration statistics
Business goal: Backfill benefit statistics for eligible manual migration treatments.

API group boundary: Atomic service-wide migration-statistics backfill controlled by `dryRun`.

Domain context: Historical manual migration treatments may need statistics backfill.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: eligible manual migration treatments may exist.
- Transition trigger: backfill request.
- Intermediate states: treatments are scanned; publish tasks are created when `dryRun=false`.
- State after: dry-run report or queued publish tasks exist.
- Invalid or blocked transitions: service scan/task failures block completion.

Required execution workflow:
1. Use function `resend migration statistics` (`POST /api/stonadsstatistikk/ettersend-manuell-migrering/{dryRun}`) with `dryRun=false` to create backfill tasks.

Optional verification workflow:
None.

Existing-state shortcuts:
- No treatment list is supplied; the service scans existing migration treatment state.

Parameter and value bindings:
- Path `dryRun=false` controls whether the behavior mutates task state.

Business result: Publish tasks are created for eligible manual migration treatments.

Constraints and invariants:
- With `dryRun=true`, the endpoint reports without creating publish tasks.

Failure and exceptional cases:
- Failing function: `resend migration statistics`
  - Source discriminator: `mapping prerequisites`
  - Failure condition: Treatment, active basis, decision date, or benefit-person reference is absent.
  - Why it fails: Backfill payload cannot build.
  - Violated prerequisite or constraint: Complete references are required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/statistikk/stønadsstatistikk/StønadsstatistikkService.kt — migration resend`

Implementation notes: This is not the same as manually supplying treatment ids.

<a id="behavior-75"></a>
### Behavior 75: Complete an administrative task list with partial success
Business goal: Attempt to complete a list of tasks administratively and report failures.

API group boundary: The function is a bulk admin operation over task ids.

Domain context: Operations may need to clean up task queues.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: task ids exist or some ids are invalid.
- Transition trigger: admin bulk completion request.
- Intermediate states: each task is attempted independently.
- State after: valid tasks may be completed; failures are counted/reported.
- Invalid or blocked transitions: invalid ids fail per item without necessarily failing whole endpoint.

Required execution workflow:
1. Use function `finish admin task list` (`POST /api/forvalter/ferdigstill-oppgaver`) with body `[O1,O2]` to complete tasks administratively.

Optional verification workflow:
1. Use function `search tasks` (`POST /api/oppgave/hent-oppgaver`) with a task query to inspect remaining open tasks.

Existing-state shortcuts:
- Task search can be skipped when task ids are known.
- Upstream task state must permit completion.

Parameter and value bindings:
- Body task ids are processed independently.

Business result: Some or all supplied tasks are completed; response reports failure count.

Constraints and invariants:
- Partial success is allowed.
- This admin endpoint does not provide all-or-nothing transaction semantics.

Failure and exceptional cases:
- Failing function: `finish admin task list`
  - Source discriminator: `task completion branch for one task id is invalid`
  - Failure condition: one task id is invalid.
  - Why it fails: per-task completion catches/logs failure and reports failed count.
  - Violated prerequisite or constraint: each task id must be completable for full success.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterController.kt — task completion`
- Failing function: `finish admin task list`
  - Source discriminator: `per-task completion outcome`
  - Failure condition: One task cannot be completed.
  - Why it fails: It is reported failed while others continue.
  - Violated prerequisite or constraint: Every task must be completable for all-success.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterController.kt — task completion`
  - Persisted outcome despite failure: Other valid tasks are completed.

Implementation notes: This is explicitly partial-success admin behavior.

<a id="behavior-76"></a>
### Behavior 76: Restart small child supplement job
Business goal: Trigger manual restart logic for small child supplement processing.

API group boundary: The function is a single administrative job trigger.

Domain context: Operations can replay supplement logic and optionally create tasks.

Starting point: `Existing service state`

State transition summary:
- State before: supplement job is not currently triggered by this request.
- Transition trigger: admin restart request.
- Intermediate states: restart logic scans/acts on cases; tasks may be created.
- State after: job logic has been started.
- Invalid or blocked transitions: service failure blocks trigger.

Required execution workflow:
1. Use function `restart small child supplement job` (`POST /api/forvalter/start-manuell-restart-av-smaabarnstillegg-jobb/skalOppretteOppgaver/{skalOppretteOppgaver}`) with `skalOppretteOppgaver=true` to trigger restart and task creation.

Optional verification workflow:
None.

Existing-state shortcuts:
- No case-specific setup is required.
- Direct database setup can seed cases affected by the job.

Parameter and value bindings:
- `skalOppretteOppgaver=true` controls task creation side effects.

Business result: Manual restart logic runs and may create follow-up tasks.

Constraints and invariants:
- This is an administrative operation, not a caseworker treatment step.

Failure and exceptional cases:
- Failing function: `restart small child supplement job`
  - Source discriminator: `active-decision prerequisite`
  - Failure condition: A selected decided treatment has no active decision.
  - Why it fails: No finalized restart basis exists.
  - Violated prerequisite or constraint: Active decision is required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/autovedtak/småbarnstillegg/RestartAvSmåbarnstilleggService.kt — restart method`

Implementation notes: The endpoint starts logic synchronously at controller/service level but effects may include tasks.

<a id="behavior-77"></a>
### Behavior 77: Send payment orders administratively
Business goal: Generate and send payment orders to the economy system for supplied treatments.

API group boundary: Atomic partial-success admin mutation scoped by body treatment ids.

Domain context: Operations can repair or force payment-order sending outside the ordinary treatment flow.

Starting point: `Existing service state`

State transition summary:
- State before: treatments have payment basis that may need sending.
- Transition trigger: admin send request.
- Intermediate states: each treatment is attempted independently.
- State after: successful treatments have payment orders sent; failures are logged.
- Invalid or blocked transitions: invalid/payment-incomplete treatments fail per item without failing the whole endpoint.

Required execution workflow:
1. Use function `send payment orders administratively` (`POST /api/forvalter/lag-og-send-utbetalingsoppdrag-til-økonomi`) with body `[B1,B2]` to send payment orders.

Optional verification workflow:
None.

Existing-state shortcuts:
- Treatment ids can come from direct database/admin discovery, but must identify treatments with valid payment basis.

Parameter and value bindings:
- Body treatment ids are the exact payment-order targets.

Business result: Payment orders are generated/sent where possible; per-treatment failures are logged while response can still be `OK`.

Constraints and invariants:
- Partial success is possible and not represented as endpoint failure.

Failure and exceptional cases:
- Failing function: `send payment orders administratively`
  - Source discriminator: `awarded-benefit lookup`
  - Failure condition: Awarded-benefit state is absent.
  - Why it fails: No order can generate.
  - Violated prerequisite or constraint: Awarded benefit must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — payment-order method`
- Failing function: `send payment orders administratively`
  - Source discriminator: `existing-order guard`
  - Failure condition: A payment order already exists.
  - Why it fails: Duplicate generation is rejected.
  - Violated prerequisite or constraint: No order may already exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — payment-order method`
- Failing function: `send payment orders administratively`
  - Source discriminator: `later-sent-treatment guard`
  - Failure condition: A later treatment was sent to economy.
  - Why it fails: Ordering would be violated.
  - Violated prerequisite or constraint: Orders must follow treatment order.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — payment-order method`
- Failing function: `send payment orders administratively`
  - Source discriminator: `active-decision prerequisite`
  - Failure condition: No active decision exists.
  - Why it fails: No finalized basis exists.
  - Violated prerequisite or constraint: Active decision must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — payment-order method`

Implementation notes: This behavior is independent from corrected resend workflows.

<a id="behavior-78"></a>
### Behavior 78: Bulk corrected payment-order resend
Business goal: Generate and implement corrected payment orders for a list of treatments.

API group boundary: Atomic partial-success admin repair scoped by body treatment ids.

Domain context: Payment-order defects can be corrected in bulk after diagnosis.

Starting point: `Existing service state`

State transition summary:
- State before: treatments have payment-order state requiring correction.
- Transition trigger: bulk corrected resend request.
- Intermediate states: each treatment is corrected and implemented independently.
- State after: response lists `iverksattOk` successes and `harFeil` failures.
- Invalid or blocked transitions: invalid or inconsistent treatments are returned in failure set.

Required execution workflow:
1. Use function `resend corrected payment orders` (`POST /api/forvalter/sendKorrigertUtbetalingsoppdragForBehandlinger`) with body `[B1,B2]` to resend corrected payment orders in bulk.

Optional verification workflow:
1. Use function `find payment-order issues` (`POST /api/forvalter/finnBehandlingerMedPotensieltFeilUtbetalingsoppdrag`) with no body to discover candidates before repair.

Existing-state shortcuts:
- Candidate discovery can be skipped when `B1` and `B2` are already known issue treatments.

Parameter and value bindings:
- Body treatment ids are corrected resend targets.
- Response separates successful and failed treatment ids.

Business result: Corrected payment orders are implemented for successful treatments; failures are reported without rolling back successes.

Constraints and invariants:
- Partial success is explicit in the response.

Failure and exceptional cases:
- Failing function: `resend corrected payment orders`
  - Source discriminator: `correction guards`
  - Failure condition: Treatment is inactive, lacks awarded benefit, has empty/inconsistent periods, or yields no order.
  - Why it fails: It is reported in `harFeil`.
  - Violated prerequisite or constraint: Active complete correctable state is required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — bulk correction method`

Implementation notes: Single-version resend is a separate targeted repair.

<a id="behavior-79"></a>
### Behavior 79: Single-version corrected payment-order resend
Business goal: Generate and implement a corrected payment order for one treatment and version.

API group boundary: Atomic targeted admin repair scoped by `behandlingId` and `versjon`.

Domain context: Operators can resend a specific payment-order version when bulk repair is too broad.

Starting point: `Existing service state`

State transition summary:
- State before: one treatment has a payment-order version requiring correction.
- Transition trigger: single-version corrected resend request.
- Intermediate states: selected version is corrected and implemented.
- State after: response lists success or failure for the treatment.
- Invalid or blocked transitions: inactive treatment, missing version, or mismatched corrected periods produce failure response.

Required execution workflow:
1. Use function `resend corrected payment order version` (`POST /api/forvalter/sendKorrigertUtbetalingsoppdragForBehandling/{behandlingId}/{versjon}`) with `behandlingId=B1`, `versjon=V1` to resend that version.

Optional verification workflow:
1. Use function `check incorrect cessation dates` (`POST /api/forvalter/sjekkOmTilkjentYtelseForBehandlingHarUkorrektOpphørsdato`) with body `[B1]` to inspect cessation-date issue state before repair.

Existing-state shortcuts:
- Discovery can be skipped when `B1` and `V1` are already known.

Parameter and value bindings:
- Path `behandlingId=B1` and `versjon=V1` select the exact payment-order version.

Business result: The selected corrected payment order is implemented or reported in `harFeil`.

Constraints and invariants:
- Failure is returned in the response rather than necessarily failing the HTTP request.

Failure and exceptional cases:
- Failing function: `resend corrected payment order version`
  - Source discriminator: `version correction method branch for treatment is not active or corrected periods do not match erroneous periods`
  - Failure condition: treatment is not active or corrected periods do not match erroneous periods.
  - Why it fails: `ForvalterService` validates active treatment and corrected-period consistency.
  - Violated prerequisite or constraint: version-specific correction consistency.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — version correction method`
- Failing function: `resend corrected payment order version`
  - Source discriminator: `version-correction guards`
  - Failure condition: Treatment is inactive, lacks awarded benefit, has empty/inconsistent periods, or yields no order.
  - Why it fails: Version cannot be corrected.
  - Violated prerequisite or constraint: Complete consistent state is required.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — version correction method`

Implementation notes: This is intentionally split from bulk corrected resend.

<a id="behavior-80"></a>
### Behavior 80: Run unvalidated rate change administratively
Business goal: Run simplified rate change for supplied cases without normal validation.

API group boundary: The function is an administrative rate-change bypass over fagsak ids.

Domain context: Operations may need to repair or force rate changes outside the normal validation path.

Starting point: `Existing service state`

State transition summary:
- State before: cases exist and may need rate update.
- Transition trigger: unvalidated admin request.
- Intermediate states: each case is processed independently.
- State after: successful cases are rate-changed; failures are logged.
- Invalid or blocked transitions: missing/incompatible case causes per-case failure.

Required execution workflow:
1. Use function `run rate change without validation` (`POST /api/forvalter/kjor-satsendring-uten-validering`) with body `[F1,F2]` to run unvalidated rate change.

Optional verification workflow:
1. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) with `fagsakId=F1` to inspect case state.

Existing-state shortcuts:
- No eligibility check is required by this admin bypass, but case ids must exist.

Parameter and value bindings:
- Body fagsak ids are processed independently.

Business result: Rate change is attempted for supplied cases without the normal validation gate.

Constraints and invariants:
- Normal validation is intentionally bypassed.
- Per-case failures are logged.

Failure and exceptional cases:
- Failing function: `run rate change without validation`
  - Source discriminator: `rate-change method branch for fagsakId=F404 does not exist`
  - Failure condition: `fagsakId=F404` does not exist.
  - Why it fails: service cannot load/process case; failure is logged.
  - Violated prerequisite or constraint: case id must exist for successful processing.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — rate-change method`
- Failing function: `run rate change without validation`
  - Source discriminator: `case/revision/active-treatment/basis outcomes`
  - Failure condition: Case, prior revision basis, eligible active state, or prior person basis is absent.
  - Why it fails: Administrative rate change cannot complete.
  - Violated prerequisite or constraint: All case and revision prerequisites must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — rate-change method`

Implementation notes: This is not equivalent to the normal checked rate-change workflow.

<a id="behavior-81"></a>
### Behavior 81: Identify payments over 100 percent
Business goal: Start background analysis for payments exceeding 100 percent.

API group boundary: Atomic async admin discovery job returning a call id.

Domain context: Operations need to identify overlapping or excessive payment state.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: payment state exists across cases.
- Transition trigger: anomaly discovery request.
- Intermediate states: a background thread starts and a `callId` is returned.
- State after: analysis is running asynchronously.
- Invalid or blocked transitions: service/thread failure blocks analysis.

Required execution workflow:
1. Use function `identify payments over 100 percent` (`POST /api/forvalter/identifiser-utbetalinger-over-100-prosent`) with no body to start analysis and capture `callId=C1`.

Optional verification workflow:
None.

Existing-state shortcuts:
- No target ids are required; the service scans payment state.

Parameter and value bindings:
- Returned `callId=C1` is for logs/correlation; no result retrieval endpoint is exposed here.

Business result: Background payment-overlap analysis starts.

Constraints and invariants:
- The endpoint returns before analysis completes.

Failure and exceptional cases:
None.

Implementation notes: Missing result retrieval by `callId` is captured as a missing behavior.

<a id="behavior-82"></a>
### Behavior 82: Find payment-order issue candidates
Business goal: Identify treatments with potentially incorrect payment orders.

API group boundary: Atomic admin discovery over payment-order state.

Domain context: Operators need candidate treatment ids before payment-order repair.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: treatments and payment orders exist.
- Transition trigger: issue-discovery request.
- Intermediate states: service validates affected payment orders.
- State after: candidate treatments and validation details are returned.
- Invalid or blocked transitions: service validation failure blocks response.

Required execution workflow:
1. Use function `find payment-order issues` (`POST /api/forvalter/finnBehandlingerMedPotensieltFeilUtbetalingsoppdrag`) with no body to return payment-order issue candidates.

Optional verification workflow:
None.

Existing-state shortcuts:
- No supplied ids are used; the service scans existing payment-order state.

Parameter and value bindings:
- Returned treatment ids can be used by corrected resend behaviors, but discovery is not required when ids are already known.

Business result: Candidate treatments with payment-order issues are returned.

Constraints and invariants:
- This behavior is read/discovery; it does not repair payment orders.

Failure and exceptional cases:
None.

Implementation notes: Repair workflows are modeled separately.

<a id="behavior-83"></a>
### Behavior 83: Check incorrect cessation dates for selected treatments
Business goal: Validate payment-order cessation dates for supplied treatment ids.

API group boundary: Atomic targeted admin validation scoped by body treatment ids.

Domain context: Operators can inspect whether specific treatments have incorrect cessation dates before repair.

Starting point: `Existing service state`

State transition summary:
- State before: selected treatments have payment-order state.
- Transition trigger: targeted validation request.
- Intermediate states: each treatment is validated.
- State after: treatments with errors are returned.
- Invalid or blocked transitions: invalid treatment ids or validation failure blocks response.

Required execution workflow:
1. Use function `check incorrect cessation dates` (`POST /api/forvalter/sjekkOmTilkjentYtelseForBehandlingHarUkorrektOpphørsdato`) with body `[B1,B2]` to validate cessation dates.

Optional verification workflow:
None.

Existing-state shortcuts:
- Candidate discovery can be skipped when treatment ids are already known.

Parameter and value bindings:
- Body treatment ids are the exact validation targets.

Business result: The response contains only treatments whose payment orders have incorrect cessation dates.

Constraints and invariants:
- Validation does not repair state.

Failure and exceptional cases:
- Failing function: `check incorrect cessation dates`
  - Source discriminator: `awarded-benefit not-found outcome`
  - Failure condition: Treatment has no awarded-benefit row.
  - Why it fails: No periods can be checked.
  - Violated prerequisite or constraint: Awarded benefit must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — cessation-date check`

Implementation notes: This can feed corrected resend, but corrected resend can also be called with known ids.

<a id="behavior-84"></a>
### Behavior 84: Populate support dates for one treatment
Business goal: Populate support-from/support-to dates for one treatment.

API group boundary: Atomic admin mutation scoped by `behandlingId`.

Domain context: Operations can repair missing support date fields on a specific treatment.

Starting point: `Existing service state`

State transition summary:
- State before: treatment may lack support date fields.
- Transition trigger: single-treatment population request.
- Intermediate states: support dates are derived and persisted.
- State after: treatment support dates are populated or the response indicates no update.
- Invalid or blocked transitions: missing treatment or derivation failure blocks update.

Required execution workflow:
1. Use function `populate support dates for treatment` (`POST /api/forvalter/populer-stonad-fom-tom/{behandlingId}`) with `behandlingId=B1` to populate dates.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect updated treatment state.

Existing-state shortcuts:
- Treatment setup can be skipped when `B1` already exists.

Parameter and value bindings:
- Path `behandlingId=B1` selects the treatment to repair.

Business result: The treatment has support-from/support-to values populated when derivation succeeds.

Constraints and invariants:
- The response is a boolean update result.

Failure and exceptional cases:
- Failing function: `populate support dates for treatment`
  - Source discriminator: `awarded-benefit not-found outcome`
  - Failure condition: Treatment has no awarded-benefit row.
  - Why it fails: No dates can populate.
  - Violated prerequisite or constraint: Awarded benefit must exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — support-date update`

Implementation notes: Bulk population is separate.

<a id="behavior-85"></a>
### Behavior 85: Populate support dates in bulk
Business goal: Populate support end dates for multiple active treatments up to a limit.

API group boundary: Atomic bulk admin job scoped by processing `limit`.

Domain context: Operations can repair missing support dates across many active treatments.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: active treatments may lack support end dates.
- Transition trigger: bulk population request.
- Intermediate states: service finds candidates up to limit and attempts updates individually.
- State after: successful candidates have support dates populated; failures are logged.
- Invalid or blocked transitions: per-treatment failures do not fail the whole endpoint.

Required execution workflow:
1. Use function `populate support dates in bulk` (`POST /api/forvalter/populer-stonad-fom-tom-alle/{limit}`) with `limit=100` to populate dates in bulk.

Optional verification workflow:
None.

Existing-state shortcuts:
- No ids are supplied; the repository query finds candidate treatments.

Parameter and value bindings:
- Path `limit=100` caps candidate count.

Business result: Candidate treatments are updated where possible and endpoint returns `ok`.

Constraints and invariants:
- Per-treatment failures are logged and do not necessarily fail the endpoint.

Failure and exceptional cases:
- Failing function: `populate support dates in bulk`
  - Source discriminator: `per-candidate race outcome`
  - Failure condition: Candidate disappears or becomes ineligible before update.
  - Why it fails: That candidate fails while others continue.
  - Violated prerequisite or constraint: Candidate must remain eligible.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — bulk support-date update`
- Failing function: `populate support dates in bulk`
  - Source discriminator: `partial-persistence outcome`
  - Failure condition: A selected candidate fails validation during the loop.
  - Why it fails: The loop records the failure and continues.
  - Violated prerequisite or constraint: Each candidate must remain eligible.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/internal/ForvalterService.kt — bulk support-date update`
  - Persisted outcome despite failure: Other eligible candidates are persisted and the request returns normally.

Implementation notes: Bulk mutation is not the same as targeted single-treatment repair.

<a id="behavior-86"></a>
### Behavior 86: Find cases to close
Business goal: Discover cases that should be closed because they have no ongoing entitlement.

API group boundary: Atomic admin discovery over case status and benefit state.

Domain context: Operations can inspect which cases are candidates for status closure.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: cases have current status and benefit state.
- Transition trigger: discovery request.
- Intermediate states: no local mutation.
- State after: candidate `fagsakId` values are returned.
- Invalid or blocked transitions: repository/query failure blocks response.

Required execution workflow:
1. Use function `find cases to close` (`GET /api/forvalter/finnFagsakerSomSkalAvsluttes`) with no body to retrieve closable case ids.

Optional verification workflow:
1. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) with a returned `fagsakId=F1` to inspect a candidate case.

Existing-state shortcuts:
- No setup endpoint is required; the query scans existing case/payment state.

Parameter and value bindings:
- Returned `fagsakId` values may inform operational decisions, but the status-update endpoint performs its own scan.

Business result: Operators receive a list of closable cases.

Constraints and invariants:
- This behavior does not update case status.

Failure and exceptional cases:
None.

Implementation notes: Discovery and repair are split because update does not consume a request body of discovered ids.

<a id="behavior-87"></a>
### Behavior 87: Update case ongoing status
Business goal: Bulk update ongoing/closed status on cases according to service rules.

API group boundary: Atomic admin mutation over service-selected case state.

Domain context: Operations can close cases that no longer have ongoing entitlement.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: cases may have stale ongoing status.
- Transition trigger: bulk status update request.
- Intermediate states: service scans cases and updates statuses.
- State after: ongoing status is recalculated/persisted for selected cases.
- Invalid or blocked transitions: repository/service failure blocks update.

Required execution workflow:
1. Use function `update case ongoing status` (`POST /api/forvalter/oppdaterLøpendeStatusPåFagsaker`) with no body to update case statuses.

Optional verification workflow:
1. Use function `find cases to close` (`GET /api/forvalter/finnFagsakerSomSkalAvsluttes`) with no body before the update to inspect likely candidates.

Existing-state shortcuts:
- Candidate discovery can be skipped because the update service performs its own selection.

Parameter and value bindings:
- No path/body ids are supplied; the service owns candidate selection.

Business result: Case ongoing/closed statuses are updated according to current entitlement state.

Constraints and invariants:
- The caller cannot restrict the update to a supplied case list.

Failure and exceptional cases:
- Failing function: `update case ongoing status`
  - Source discriminator: `per-case reload not-found outcome`
  - Failure condition: Selected case disappears before reload.
  - Why it fails: Its status cannot update.
  - Violated prerequisite or constraint: Selected case must still exist.
  - Implementation evidence: `src/main/kotlin/no/nav/familie/ba/sak/kjerne/fagsak/FagsakService.kt — ongoing-status update`

Implementation notes: This is not response-bound to `find cases to close`.

<a id="behavior-88"></a>
### Behavior 88: Find migration duplicates with ongoing Infotrygd case
Business goal: Identify open cases with multiple migration treatments and an ongoing Infotrygd case.

API group boundary: Atomic admin discovery over case, migration-treatment, and Infotrygd overlap state.

Domain context: Migration duplicates with legacy ongoing cases are higher-risk anomalies.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: migration treatments and Infotrygd state exist.
- Transition trigger: anomaly query request.
- Intermediate states: no local mutation.
- State after: `(fagsakId, ident)` pairs are returned.
- Invalid or blocked transitions: Infotrygd/service query failure blocks response.

Required execution workflow:
1. Use function `find migration duplicates with Infotrygd` (`GET /api/forvalter/finnÅpneFagsakerMedFlereMigreringsbehandlingerOgLøpendeSakIInfotrygd`) with no body to list anomalies.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup endpoint is required; the query scans existing migration and Infotrygd state.

Parameter and value bindings:
- Returned `fagsakId` and identity values are report outputs for manual follow-up.

Business result: Operators receive migration duplicate cases that also overlap an ongoing Infotrygd case.

Constraints and invariants:
- This behavior is read-only and does not repair duplicates.

Failure and exceptional cases:
None.

Implementation notes: Migration duplicate discovery without Infotrygd constraint is separate.

<a id="behavior-89"></a>
### Behavior 89: Find migration duplicates
Business goal: Identify open cases with multiple migration treatments.

API group boundary: Atomic admin discovery over local case and migration-treatment state.

Domain context: Duplicate migration treatments can indicate unsafe or ambiguous case state.

Starting point: `Pre-existing service/upstream state required`

State transition summary:
- State before: open cases and migration treatments exist.
- Transition trigger: local duplicate query request.
- Intermediate states: no local mutation.
- State after: `(fagsakId, ident)` pairs are returned.
- Invalid or blocked transitions: local query failure blocks response.

Required execution workflow:
1. Use function `find migration duplicates` (`GET /api/forvalter/finnÅpneFagsakerMedFlereMigreringsbehandlinger`) with no body to list local migration duplicates.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup endpoint is required; the query scans existing local migration state.

Parameter and value bindings:
- Returned ids are report outputs and are not consumed by an exposed repair endpoint.

Business result: Operators receive open cases with multiple migration treatments.

Constraints and invariants:
- This behavior is read-only and does not close or merge duplicates.

Failure and exceptional cases:
None.

Implementation notes: This behavior intentionally differs from the Infotrygd-overlap query.

<a id="behavior-90"></a>
### Behavior 90: Fill empty condition start dates in preprod
Business goal: Mutate preprod/local test data by filling missing condition start dates from birth dates.

API group boundary: The function is an environment-gated treatment mutation.

Domain context: Test/preprod environments need data repair shortcuts that must not run in production.

Starting point: `Existing service state`

State transition summary:
- State before: treatment has condition results with empty start dates.
- Transition trigger: preprod fill request.
- Intermediate states: missing dates are calculated from birth dates.
- State after: condition start dates are populated.
- Invalid or blocked transitions: prod or unsupported runtime profile fails.

Required execution workflow:
1. Use function `fill condition dates in preprod` (`PUT /api/preprod/{behandlingId}/fyll-ut-vilkarsvurdering`) with `behandlingId=B1` in preprod/dev-postgres-preprod runtime to fill dates.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) with `behandlingId=B1` to inspect condition dates.

Existing-state shortcuts:
- Treatment setup can be skipped when a preprod treatment with missing condition dates exists.
- Direct database setup must run only in allowed environment.

Parameter and value bindings:
- `behandlingId=B1` scopes mutation.
- Runtime profile controls whether function can execute.

Business result: Empty condition start dates are filled for the treatment in preprod-like environments.

Constraints and invariants:
- The endpoint must not run in prod and explicitly rejects unsupported profiles.

Failure and exceptional cases:
None.

Implementation notes: This is modeled as supported admin/test behavior, not ordinary production casework.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Fully synchronous treatment closure after decision implementation
Priority: Critical domain gap

Expected business goal: A caller would expect to drive a treatment from decision approval to fully closed/finished state and verify payment, journal, distribution, and final status in one API-realizable lifecycle.

Why it is unsupported: The exposed decision function records the decision and advances implementation, but later steps are internal/task-driven and not exposed as safe public functions in `full-behavior.md`.

Existing functions considered:
- `decide treatment`: records decision and invokes step handling, but does not expose every later implementation step as request-addressable workflow.
- `generate decision letter`: generates a letter, but does not close treatment.
- `send payment orders administratively`: admin repair/send path, not the ordinary treatment lifecycle.

Missing capability: Public, transactional, state-safe endpoints or observable workflow handles for post-decision implementation, economy status, journal/distribution, and final treatment closure.

Proof that function composition is insufficient: Chaining decision, letter generation, and admin payment-send functions does not reproduce the ordinary state machine because internal task steps, economy callbacks, and finalization rules are not exposed or bound by returned ids.

Evidence from existing functions/source:
- `decide treatment` delegates to `StegService` and later steps exist in code as internal step handlers.
- No function in `full-behavior.md` exposes ordinary `IVERKSETT_MOT_OPPDRAG`, `VENTE_PÅ_STATUS_FRA_ØKONOMI`, `JOURNALFØR_VEDTAKSBREV`, `DISTRIBUER_VEDTAKSBREV`, or finalization as direct public API.

Business impact: API clients cannot fully drive or deterministically test the full treatment lifecycle from public endpoints alone.

### Missing Behavior 2: API-create foreign amount and currency-rate rows
Priority: Critical domain gap

Expected business goal: A caseworker should be able to create, update, and delete foreign period amount and currency-rate rows through API workflows.

Why it is unsupported: The available functions update/delete existing rows, but initial row creation for foreign period amount and currency rate is not exposed as a standalone create function.

Existing functions considered:
- `update foreign period amount`: requires an existing `id` in body.
- `delete foreign period amount`: requires existing `utenlandskPeriodebeløpId`.
- `update currency rate from ECB`: requires existing `id`.
- `set historical ISK rate manually`: requires existing `id`.
- `delete currency rate`: requires existing `valutakursId`.

Missing capability: Create endpoints or upsert semantics for foreign period amount and currency-rate rows when none exists.

Proof that function composition is insufficient: Treatment creation and EEA competence upsert do not return a guaranteed foreign amount or currency-rate id. Update endpoints cannot create because they load existing rows by id.

Evidence from existing functions/source:
- `update foreign period amount` explicitly loads existing state to preserve `utbetalingsland`.
- `update currency rate from ECB` compares request to `valutakursService.hentValutakurs(restValutakurs.id)`.

Business impact: Complete EEA differential-calculation setup may require direct database/internal generation and cannot always be completed through REST alone.

### Missing Behavior 3: Strong child-resource ownership enforcement for several treatment subresources
Priority: Important robustness gap

Expected business goal: Updating or deleting a child row through `/behandling/{behandlingId}/.../{id}` should fail when the child id belongs to another treatment.

Why it is unsupported: Several services validate access/editability for the path treatment, then load or delete the child row by child id without enforcing path-treatment ownership in the service call.

Existing functions considered:
- `update EØS refund period`: service updates by refund id only.
- `delete EØS refund period`: service logs/deletes by id and accepts `behandlingId` mainly for response/log context.
- `update overpaid currency period`: service updates by id only.
- `delete overpaid currency period`: service logs/deletes by id.
- `delete letter recipient`: service deletes by `mottakerId` only.

Missing capability: Repository/service ownership checks that assert child row belongs to path `behandlingId` before mutation.

Proof that function composition is insufficient: Calling list first can discover the correct id, but it cannot prevent a later request from sending a different valid child id from another treatment.

Evidence from existing functions/source:
- `RefusjonEøsService.oppdaterRefusjonEøsPeriode` loads by `id` and does not compare `behandlingId`.
- `FeilutbetaltValutaService.oppdatertFeilutbetaltValutaPeriode` loads by `id`.
- `BrevmottakerService.fjernBrevmottaker` deletes by id.

Business impact: A caller with access to one editable treatment could potentially mutate a child resource from another treatment if they know the child id.

### Missing Behavior 4: Query corrected decision metadata and small-child supplement corrections directly
Priority: API ergonomics gap

Expected business goal: A client should be able to list or retrieve current and historical corrected-decision metadata and small-child supplement corrections.

Why it is unsupported: Create/deactivate or add/remove functions exist, but direct list/retrieve endpoints are missing for these resources.

Existing functions considered:
- `create corrected decision metadata`: creates active metadata.
- `deactivate corrected decision metadata`: deactivates active metadata.
- `add small child supplement correction`: adds monthly correction.
- `remove small child supplement correction`: removes monthly correction.
- `retrieve treatment`: may expose some state indirectly, but does not provide a narrow resource history/list contract.

Missing capability: Dedicated list/retrieve endpoints for corrected-decision metadata and supplement corrections.

Proof that function composition is insufficient: Treatment retrieval is broad and cannot reliably provide historical correction metadata or a resource-specific audit/list contract.

Evidence from existing functions/source:
- `list corrected after-payment metadata` exists for after-payment corrections, but no equivalent function exists for corrected decision metadata.
- No list function exists for small-child supplement correction in `full-behavior.md`.

Business impact: Clients cannot confidently verify correction history without broad treatment reads or database access.

### Missing Behavior 5: Async task correlation and result retrieval for queued domain jobs
Priority: Important robustness gap

Expected business goal: When an endpoint queues a task, the API should return a task id/call id and provide a supported way to retrieve processing result.

Why it is unsupported: Many queue functions return generic success strings or no domain id; shared `/api/task/*` endpoints appear only as Swagger auxiliary endpoints without project controller implementation and were not converted to functions.

Existing functions considered:
- `queue treatment from birth event`: queues a task but returns no treatment id.
- `handle identity event`: queues identity-event handling.
- `handle transitional benefit event`: queues transitional-benefit handling.
- `trigger rate change for case`: queues rate change.
- `run consistency dry run`: queues reconciliation dry run.
- `run consistency reconciliation`: queues real reconciliation.

Missing capability: Stable returned `taskId` or `callId` plus project-owned task status/result retrieval functions.

Proof that function composition is insufficient: Later case or treatment reads do not identify which task ran, failed, retried, or produced the observed state. Auxiliary task endpoints lack implementation evidence in this project.

Evidence from existing functions/source:
- `full-behavior.md` lists shared `/api/task/{id}`, `/api/task/callId/{callId}`, and related endpoints as auxiliary, not converted to functions due to missing project controller source.

Business impact: Clients cannot reliably correlate queued domain work with completed state, failures, or retries.

### Missing Behavior 6: Transactional all-or-nothing administrative bulk operations
Priority: Important robustness gap

Expected business goal: Administrative bulk repair endpoints should support atomic mode or explicit per-item transaction/reporting semantics.

Why it is unsupported: Several admin endpoints intentionally continue after per-item failures and return/log partial results.

Existing functions considered:
- `finish admin task list`: reports failed count.
- `send payment orders administratively`: logs per-treatment errors.
- `run rate change without validation`: logs per-case failures.
- `resend corrected payment orders`: returns successes and failures.
- `populate support dates in bulk`: logs per-treatment failures.

Missing capability: Atomic mode, rollback behavior, or a durable per-item audit/result resource.

Proof that function composition is insufficient: Retrying after partial success can duplicate side effects for successful items while still not repairing failed items; no composition can retroactively make the original bulk action atomic.

Evidence from existing functions/source:
- Forvalter controller/service code catches exceptions per item and accumulates success/failure sets.

Business impact: Administrative repair can leave mixed state that requires manual reconciliation.

### Missing Behavior 7: Public per-case close/reopen lifecycle
Priority: Critical domain gap

Expected business goal: A caseworker or authorized system should be able to close or reopen a specific case with explicit reason and verification.

Why it is unsupported: Public case endpoints create/read/search cases but do not expose close/reopen. Admin endpoints discover and bulk-update ongoing status without taking a specific `fagsakId` request.

Existing functions considered:
- `create case`: creates/returns case.
- `retrieve full case`: reads case.
- `find cases to close`: discovers candidates.
- `update case ongoing status`: bulk recomputes status and accepts no explicit case id.

Missing capability: Case-scoped close/reopen endpoint with reason, caller authorization, and audit.

Proof that function composition is insufficient: Finding closable cases and running a service-wide update cannot close a specific case selected by a business user, nor can it reopen an incorrectly closed case.

Evidence from existing functions/source:
- No `PUT/PATCH /api/fagsaker/{fagsakId}/status`-style function exists in `full-behavior.md`.

Business impact: Case status lifecycle is only indirectly and administratively controllable.

### Missing Behavior 8: Safe delete/update ownership for letter and document recipient effects
Priority: Important robustness gap

Expected business goal: Manual letter recipient changes should be scoped to the treatment and auditable through a dedicated recipient resource history.

Why it is unsupported: Recipient delete uses `mottakerId` without service-level treatment ownership validation, and historical recipient state is available only via logs/broad treatment context.

Existing functions considered:
- `add letter recipient`: adds recipient and logs.
- `list letter recipients`: lists current recipients by treatment.
- `delete letter recipient`: deletes by recipient id.
- `retrieve treatment log`: broad log read.

Missing capability: Recipient-specific history/retrieve endpoint and service-enforced treatment ownership on delete.

Proof that function composition is insufficient: Listing before deletion cannot enforce that the delete id belongs to the listed treatment; logs cannot reconstruct a typed recipient history contract.

Evidence from existing functions/source:
- `BrevmottakerService.fjernBrevmottaker` loads and deletes by id.

Business impact: Recipient management is harder to audit and may be vulnerable to cross-treatment id mistakes.

## Cross-Behavior Observations
- The treatment step machine is strict: waiting treatments, machine-waiting treatments, closed treatments, and decision-stage treatments block many mutations.
- Generated ids are central: `fagsakId`, `behandlingId`, `vedtakId`, `vedtaksperiodeId`, condition ids, EEA ids, recipient ids, task ids, journalpost ids, and document ids are reused across workflows.
- Several functions mutate state through `GET`, notably repayment creation, register refresh, task completion, and queued rate change for one case.
- Many treatment-basis edits reset later workflow state: condition changes, child addition, changed-payment shares, and payment recalculation can invalidate derived result/decision data.
- Corrected-decision and corrected after-payment metadata use active-flag semantics: creating a new active record deactivates the previous active record.
- Validation strength varies. Competence and wait handling enforce clear constraints, while some child-resource endpoints rely on path treatment access but mutate by child id.
- Access-control differs by context: ordinary caseworker access, system role, Klage machine-to-machine caller, and admin/internal endpoints have different gates.
- Async/event-driven behavior is common: birth events, identity events, transitional benefit events, rate changes, reconciliation, statistics publishing, and several admin jobs create tasks rather than final business state synchronously.
- Administrative bulk endpoints often allow partial success and may mutate state for successful items while reporting failures for others.
- OpenAPI/source discrepancies include `GET /api/person` exposing `personIdentBody` while source uses the `personIdent` header, and Swagger auxiliary `/api/task/*` endpoints lacking project-owned controller implementation.
- Some endpoints can return failure resources with HTTP 200-style response behavior, so clients must inspect `Ressurs` payloads rather than status code alone.

## Coverage Summary
- Fully supported workflow/state areas: case creation/idempotent return, treatment creation/restart, core treatment step execution through decision, wait lifecycle, condition mutations, competence intervals, changed-payment shares, refund/overpaid currency period lifecycles, correction active flags, manual recipient lifecycle, decision-period explanation editing, task assignment/completion, journalpost/document inspection, journalføring, external benefit lookups, complaint creation/revision, rate-change queueing, statistics mapping/queueing, and selected admin repair jobs.
- Partially supported workflow/state areas: post-decision treatment completion, EEA differential setup, async task result tracking, corrected-decision and supplement correction inspection, payment-order repair, and case closure.
- Unsupported or unsafe workflow/state areas: API creation of some EEA calculation rows, strict child-resource ownership enforcement for several ids, all-or-nothing admin bulk operations, public per-case close/reopen, and production business modeling of internal/test-tool endpoints.
