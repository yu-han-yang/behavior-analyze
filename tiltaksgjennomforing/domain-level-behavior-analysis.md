# Domain-Level Behavior Analysis

## Domain Summary

The service manages Norwegian labor-market measure agreements (`Avtale`) between NAV advisors, participants, employers, mentors, and decision-makers. The central aggregate is the agreement, identified by generated `avtaleId` and later `avtaleNr`. Important child and support concepts include current and historical agreement content (`AvtaleInnhold`), approvals, mentor confidentiality signing, after-registration eligibility, subsidy periods (`TilskuddPeriode`), notifications (`Varsel`), saved searches (`FilterSok`), journal export state, data warehouse patch messages, and agreement event messages.

Lifecycle state is derived from timestamps, flags, current content, annulment/deletion fields, and subsidy-period state rather than a single status column. Workflows therefore commonly bind the same `avtaleId`, current `sistEndret`, role cookie `innlogget-part`, generated `tilskuddsperiodeId`, generated `varselId`, saved `sokId`, or agreement-version id across multiple functions.


## Available Function Inventory


### Agreement Creation And Search

- `list accessible agreements` - `GET /avtaler` - Returns a paginated role-scoped agreement list filtered by query fields.

- `create advisor agreement` - `POST /avtaler` - Creates a new agreement draft as a NAV advisor.

- `create Arena cleanup agreement` - `POST /avtaler` - Creates a NAV advisor draft and stores an Arena cleanup marker when `ryddeavtale=true`.

- `create employer agreement` - `POST /avtaler/opprett-som-arbeidsgiver` - Creates a new employer-started agreement draft using employer Altinn rights.

- `create mentor agreement as advisor` - `POST /avtaler/opprett-mentor-avtale` - Creates a mentor agreement draft through the advisor path.

- `create mentor agreement as employer` - `POST /avtaler/opprett-mentor-avtale` - Creates a mentor agreement draft through the employer path.

- `check participant overlap` - `GET /avtaler/deltaker-allerede-paa-tiltak` - Finds existing non-deleted/non-annulled agreements that overlap a participant and measure.

- `search agreements and save search` - `POST /avtaler/sok` - Searches visible agreements and stores or reuses a generated saved-search id.

- `replay saved agreement search` - `GET /avtaler/sok` - Reruns a previously saved agreement search by `sokId`.


### Agreement Retrieval And Versioning

- `retrieve agreement by id` - `GET /avtaler/{avtaleId}` - Retrieves one accessible agreement by UUID.

- `retrieve agreement by agreement number` - `GET /avtaler/avtaleNr/{avtaleNr}` - Retrieves one accessible agreement by generated agreement number.

- `list agreement versions` - `GET /avtaler/{avtaleId}/versjoner` - Lists persisted agreement-content versions for one accessible agreement.


### Draft Agreement Change And Sharing

- `update agreement` - `PUT /avtaler/{avtaleId}` - Persists draft content changes and recalculates subsidy periods where relevant.

- `dry-run agreement update` - `PUT /avtaler/{avtaleId}/dry-run` - Applies draft update rules in memory without saving.

- `share agreement with a party` - `POST /avtaler/{avtaleId}/del-med-avtalepart` - Registers a share event for a selected agreement party.


### Agreement Approval Lifecycle

- `approve agreement as participant` - `POST /avtaler/{avtaleId}/godkjenn` - Records participant approval on a complete agreement.

- `approve agreement as employer` - `POST /avtaler/{avtaleId}/godkjenn` - Records employer approval on a complete agreement.

- `sign mentor confidentiality declaration` - `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring, POST /avtaler/{avtaleId}/godkjenn` - Records mentor signing of the confidentiality declaration.

- `approve agreement as advisor` - `POST /avtaler/{avtaleId}/godkjenn` - Records advisor approval and can enter non-decision-maker agreements.

- `approve on behalf of participant` - `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av, POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker` - Lets an advisor approve for the participant with a recorded reason.

- `approve on behalf of employer` - `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver` - Lets an advisor approve for the employer with a recorded reason.

- `approve on behalf of participant and employer` - `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` - Lets an advisor approve for both external parties with recorded reasons.

- `revoke approvals` - `POST /avtaler/{avtaleId}/opphev-godkjenninger` - Clears recorded approvals before an agreement has been entered.

- `mark agreement eligible for after-registration` - `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` - Toggles after-registration eligibility from false to true.

- `remove after-registration eligibility` - `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` - Toggles after-registration eligibility from true to false.


### Subsidy Period Decisioning And Agreement Changes

- `approve subsidy period` - `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode` - Approves the current subsidy period and can enter a subsidy-backed agreement.

- `reject subsidy period` - `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` - Rejects the current subsidy period with causes and explanation.

- `send rejected subsidy period back` - `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter` - Deactivates rejected active periods and creates replacement unhandled periods.

- `shorten agreement` - `POST /avtaler/{avtaleId}/forkort` - Creates an approved shorter version and adjusts periods.

- `dry-run agreement shortening` - `POST /avtaler/{avtaleId}/forkort-dry-run` - Previews agreement shortening without saving.

- `extend agreement` - `POST /avtaler/{avtaleId}/forleng` - Creates an approved longer version and adjusts periods.

- `dry-run agreement extension` - `POST /avtaler/{avtaleId}/forleng-dry-run` - Previews agreement extension without saving.

- `change subsidy calculation` - `POST /avtaler/{avtaleId}/endre-tilskuddsberegning` - Creates an approved version with changed wage-subsidy calculation fields.

- `dry-run subsidy calculation change` - `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run` - Previews wage-subsidy calculation changes without saving.

- `change contact information` - `POST /avtaler/{avtaleId}/endre-kontaktinfo` - Creates an approved version with changed contact information.

- `change job description` - `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse` - Creates an approved version with changed job data.

- `change follow-up and adaptation text` - `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging` - Creates an approved version with changed follow-up text.

- `change work-training goals` - `POST /avtaler/{avtaleId}/endre-maal` - Replaces goals on a work-training agreement.

- `change inclusion subsidy expenses` - `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd` - Replaces inclusion-subsidy expenses.

- `change mentor details` - `POST /avtaler/{avtaleId}/endre-om-mentor` - Changes mentor-specific approved content.

- `change cost center` - `POST /avtaler/{avtaleId}/endre-kostnadssted` - Changes cost center on editable subsidy periods.

- `adjust Arena migration date` - `POST /avtaler/{avtaleId}/juster-arena-migreringsdato` - Persists an Arena migration date and recalculates cleanup periods.

- `dry-run Arena migration date adjustment` - `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run` - Previews Arena migration-date recalculation.


### Agreement Retrieval And Versioning

- `get employer account number` - `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver` - Looks up the employer account number for the agreement company.

- `download agreement PDF` - `GET /avtaler/{avtaleId}/pdf` - Downloads a generated agreement PDF when advisor approval exists.

- `check whether Salesforce dialog should be shown` - `GET /avtaler/{avtaleId}/vis-salesforce-dialog` - Returns whether a Salesforce dialog should be shown for the agreement.


### Draft Agreement Change And Sharing

- `refresh follow-up unit` - `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet` - Refreshes participant and NAV unit data.

- `take over agreement as advisor` - `PUT /avtaler/{avtaleId}/overta` - Assigns the logged-in advisor to the agreement.


### Agreement Termination

- `annul agreement` - `POST /avtaler/{avtaleId}/annuller` - Annuls an agreement and eligible subsidy periods.

- `soft-delete agreement` - `POST /avtaler/{avtaleId}/slettemerk` - Hides an agreement by setting `slettemerket=true`.


### Role-Specific Lists, User Context, Reference Data, And Integrations

- `list employer agreements for Min Side Arbeidsgiver` - `GET /avtaler/min-side-arbeidsgiver` - Lists employer-visible agreements for a company.

- `list decision-maker agreements` - `GET /avtaler/beslutter-liste` - Lists agreements/periods in a decision-maker work queue.

- `get logged-in user` - `GET /innlogget-bruker` - Returns role-specific logged-in user information.

- `look up organization` - `GET /organisasjoner` - Retrieves employer organization information from Ereg.

- `get Altinn rights request URLs` - `GET /be-om-altinn-rettighet-urler` - Returns URLs for requesting Altinn rights.

- `get all code lists` - `GET /kodeverk` - Returns measure and status code lists.

- `get status code list` - `GET /kodeverk/statuser` - Returns agreement statuses.

- `get measure type code list` - `GET /kodeverk/tiltakstyper` - Returns measure types.

- `evaluate feature toggles` - `GET /feature` - Evaluates Unleash feature toggles.

- `get feature variants` - `GET /feature/variant` - Returns Unleash variants.

- `health check` - `GET /internal/healthcheck` - Checks database health.


### Notifications, Journal, Admin Repair, And Messaging

- `list overview notifications` - `GET /varsler/oversikt` - Lists unread bell notifications for the logged-in party.

- `list agreement modal notifications` - `GET /varsler/avtale-modal` - Lists unread bell notifications for one agreement.

- `list agreement notification log` - `GET /varsler/avtale-logg` - Lists all notifications for one agreement and receiver role.

- `mark notification as read` - `POST /varsler/{varselId}/sett-til-lest` - Marks one caller-owned notification as read.

- `mark multiple notifications as read` - `POST /varsler/sett-alle-til-lest` - Marks several caller-owned notifications as read.

- `list unjournaled agreements` - `GET /internal/avtaler` - Exports agreement versions requiring journalføring.

- `mark agreement versions as journaled` - `PUT /internal/avtaler` - Stores journal post ids on exported agreement versions.

- `recalculate wage subsidy for selected agreements` - `POST /utvikler-admin/reberegn` - Recalculates missing wage-subsidy totals on selected agreements.

- `fix missing reduced-percent date in batch` - `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}` - Repairs permanent wage-subsidy reduced-percent data.

- `dry-run missing reduced-percent date fix` - `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}` - Counts/logs the same repair without saving.

- `generate subsidy periods for one agreement` - `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}` - Generates periods for one agreement.

- `recalculate unhandled subsidy periods` - `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}` - Removes and recreates unhandled periods.

- `find subsidy period date-order problems` - `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer` - Logs period ordering diagnostics.

- `annul subsidy period` - `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}` - Annuls one subsidy period.

- `annul and resend subsidy period as approved` - `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}` - Annuls a period and creates an approved replacement.

- `annul and generate unhandled subsidy period` - `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}` - Annuls a period and creates an unhandled replacement.

- `annul and generate Arena-treated periods before date` - `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}` - Replaces earlier periods as Arena-treated.

- `patch selected agreements to data warehouse` - `POST /utvikler-admin/dvh-melding/patch` - Creates DVH patch messages for selected agreements.

- `patch all agreements to data warehouse` - `POST /utvikler-admin/dvh-melding/patchalleavtaler` - Creates DVH patch messages for all agreements.

- `send agreement event message for one agreement` - `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}` - Sends one agreement event message.

- `send agreement event messages for all agreements` - `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler` - Sends event messages for all agreements.

- `dry-run event messages for all agreements` - `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler` - Previews/logs all-agreement event publication.


## Supported Business Behaviors

### Behavior 1: List Accessible Agreements

Business goal:
Returns a paginated role-scoped agreement list filtered by query fields.

Domain context:
This is a distinct domain-facing capability in the `Agreement Creation And Search` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `list accessible agreements` (`GET /avtaler`) with endpoint-specific request values.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Returns a paginated role-scoped agreement list filtered by query fields.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 2: Create Advisor Agreement

Business goal:
Creates a new agreement draft as a NAV advisor.

Domain context:
This is a distinct domain-facing capability in the `Agreement Creation And Search` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with body `deltakerFnr={participantFnr}`, `bedriftNr={employerUnitNumber}`, `tiltakstype={measureType}`, optional query `ryddeavtale=false`, then capture `avtaleId` from `Location`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates a new agreement draft as a NAV advisor.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 3: Create Arena Cleanup Agreement

Business goal:
Creates a NAV advisor draft and stores an Arena cleanup marker when `ryddeavtale=true`.

Domain context:
This is a distinct domain-facing capability in the `Agreement Creation And Search` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create Arena cleanup agreement` (`POST /avtaler`) with body `deltakerFnr={participantFnr}`, `bedriftNr={employerUnitNumber}`, `tiltakstype={subsidyMeasureType}`, query `ryddeavtale=true`, then capture `avtaleId` from `Location`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates a NAV advisor draft and stores an Arena cleanup marker when `ryddeavtale=true`.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `create Arena cleanup agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create Arena cleanup agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create Arena cleanup agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create Arena cleanup agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 4: Create Employer Agreement

Business goal:
Creates a new employer-started agreement draft using employer Altinn rights.

Domain context:
This is a distinct domain-facing capability in the `Agreement Creation And Search` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create employer agreement` (`POST /avtaler/opprett-som-arbeidsgiver`) with body `deltakerFnr={participantFnr}`, `bedriftNr={employerUnitNumber}`, `tiltakstype={measureType}` from an employer with Altinn rights, then capture `avtaleId` from `Location`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates a new employer-started agreement draft using employer Altinn rights.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `create employer agreement`
  - Failure condition: employer lacks Altinn access for `bedriftNr` and `tiltakstype`.
  - Why it fails: `Arbeidsgiver.tilgangTilBedriftVedOpprettelseAvAvtale` rejects the selected company/measure.
  - Violated prerequisite or constraint: employer must have measure-specific Altinn rights.
- Failing function: `create employer agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create employer agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 5: Create Mentor Agreement As Advisor

Business goal:
Creates a mentor agreement draft through the advisor path.

Domain context:
This is a distinct domain-facing capability in the `Agreement Creation And Search` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create mentor agreement as advisor` (`POST /avtaler/opprett-mentor-avtale`) with body `deltakerFnr={participantFnr}`, `mentorFnr={mentorFnr}`, `bedriftNr={employerUnitNumber}`, `tiltakstype=MENTOR`, `avtalerolle=VEILEDER`, then capture `avtaleId` from `Location`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates a mentor agreement draft through the advisor path.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `create mentor agreement as advisor`
  - Failure condition: `deltakerFnr` equals `mentorFnr`.
  - Why it fails: the controller explicitly rejects the same person as participant and mentor.
  - Violated prerequisite or constraint: participant and mentor must be distinct.
- Failing function: `create mentor agreement as advisor`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create mentor agreement as advisor`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create mentor agreement as advisor`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create mentor agreement as advisor`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 6: Create Mentor Agreement As Employer

Business goal:
Creates a mentor agreement draft through the employer path.

Domain context:
This is a distinct domain-facing capability in the `Agreement Creation And Search` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create mentor agreement as employer` (`POST /avtaler/opprett-mentor-avtale`) with body `deltakerFnr={participantFnr}`, `mentorFnr={mentorFnr}`, `bedriftNr={employerUnitNumber}`, `tiltakstype=MENTOR`, `avtalerolle=ARBEIDSGIVER`, then capture `avtaleId` from `Location`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates a mentor agreement draft through the employer path.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `create mentor agreement as employer`
  - Failure condition: `deltakerFnr` equals `mentorFnr`.
  - Why it fails: the controller explicitly rejects the same person as participant and mentor.
  - Violated prerequisite or constraint: participant and mentor must be distinct.
- Failing function: `create mentor agreement as employer`
  - Failure condition: employer lacks Altinn access for `bedriftNr` and `tiltakstype`.
  - Why it fails: the employer mentor path checks company/measure rights before save.
  - Violated prerequisite or constraint: employer must have measure-specific Altinn rights.
- Failing function: `create mentor agreement as employer`
  - Failure condition: `avtalerolle` is neither `VEILEDER` nor `ARBEIDSGIVER`.
  - Why it fails: the controller misses both creation branches and throws after no agreement is built.
  - Violated prerequisite or constraint: mentor creation supports only advisor or employer creator role.
- Failing function: `create mentor agreement as employer`
  - Failure condition: participant is under 16.
  - Why it fails: the mentor agreement constructor applies the minimum-age rule.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 7: Check Participant Overlap

Business goal:
Finds existing non-deleted/non-annulled agreements that overlap a participant and measure.

Domain context:
This is a distinct domain-facing capability in the `Agreement Creation And Search` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `check participant overlap` (`GET /avtaler/deltaker-allerede-paa-tiltak`) with endpoint-specific request values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Finds existing non-deleted/non-annulled agreements that overlap a participant and measure.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 8: Search Agreements And Save Search

Business goal:
Searches visible agreements and stores or reuses a generated saved-search id.

Domain context:
This is a distinct domain-facing capability in the `Agreement Creation And Search` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `search agreements and save search` (`POST /avtaler/sok`) with endpoint-specific request values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Searches visible agreements and stores or reuses a generated saved-search id.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 9: Replay Saved Agreement Search

Business goal:
Reruns a previously saved agreement search by `sokId`.

Domain context:
This is a distinct domain-facing capability in the `Agreement Creation And Search` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `search agreements and save search` (`POST /avtaler/sok`) with body `AvtalePredicate={filters}` to obtain `sokId`.
2. Use function `replay saved agreement search` (`GET /avtaler/sok`) with query `sokId={capturedSokId}`, optional `page`, `size`, and `sorteringskolonne`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Reruns a previously saved agreement search by `sokId`.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 10: Retrieve Agreement By Id

Business goal:
Retrieves one accessible agreement by UUID.

Domain context:
This is a distinct domain-facing capability in the `Agreement Retrieval And Versioning` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid body values and capture `avtaleId`.
2. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `avtaleId={capturedAvtaleId}` and matching `innlogget-part`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Retrieves one accessible agreement by UUID.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `retrieve agreement by id`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `retrieve agreement by id`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `retrieve agreement by id`
  - Failure condition: mentor retrieves before signing confidentiality declaration.
  - Why it fails: `Mentor.hentAvtale` throws `IKKE_TILGANG_TIL_AVTALE` after mentor identity access succeeds.
  - Violated prerequisite or constraint: mentor must sign before full detail access.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 11: Retrieve Agreement By Agreement Number

Business goal:
Retrieves one accessible agreement by generated agreement number.

Domain context:
This is a distinct domain-facing capability in the `Agreement Retrieval And Versioning` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid body values and capture `avtaleId`.
2. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `avtaleId={capturedAvtaleId}` to obtain `avtaleNr`.
3. Use function `retrieve agreement by agreement number` (`GET /avtaler/avtaleNr/{avtaleNr}`) with `avtaleNr={capturedAvtaleNr}` and matching `innlogget-part`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Retrieves one accessible agreement by generated agreement number.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `retrieve agreement by agreement number`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `retrieve agreement by agreement number`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `retrieve agreement by id`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `retrieve agreement by id`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 12: List Agreement Versions

Business goal:
Lists persisted agreement-content versions for one accessible agreement.

Domain context:
This is a distinct domain-facing capability in the `Agreement Retrieval And Versioning` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid body values and capture `avtaleId`.
2. Use function `list agreement versions` (`GET /avtaler/{avtaleId}/versjoner`) with `avtaleId={capturedAvtaleId}` and matching `innlogget-part`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Lists persisted agreement-content versions for one accessible agreement.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `list agreement versions`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `list agreement versions`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 13: Update Agreement

Business goal:
Persists draft content changes and recalculates subsidy periods where relevant.

Domain context:
This is a distinct domain-facing capability in the `Draft Agreement Change And Sharing` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid body values and capture `avtaleId`.
2. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `avtaleId={capturedAvtaleId}` to capture `sistEndret`.
3. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with path `avtaleId={capturedAvtaleId}`, header `If-Unmodified-Since={capturedSistEndret}`, body `EndreAvtale={completeOrCandidateContent}`, and editable `innlogget-part`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Persists draft content changes and recalculates subsidy periods where relevant.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `retrieve agreement by id`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `retrieve agreement by id`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 14: Dry-run Agreement Update

Business goal:
Applies draft update rules in memory without saving.

Domain context:
This is a distinct domain-facing capability in the `Draft Agreement Change And Sharing` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid body values and capture `avtaleId`.
2. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `avtaleId={capturedAvtaleId}` to capture `sistEndret`.
3. Use function `dry-run agreement update` (`PUT /avtaler/{avtaleId}/dry-run`) with path `avtaleId={capturedAvtaleId}`, header `If-Unmodified-Since={capturedSistEndret}`, body `EndreAvtale={completeOrCandidateContent}`, and editable `innlogget-part`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Applies draft update rules in memory without saving.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `dry-run agreement update`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `dry-run agreement update`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `dry-run agreement update`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `dry-run agreement update`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `dry-run agreement update`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `dry-run agreement update`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `dry-run agreement update`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `dry-run agreement update`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `dry-run agreement update`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `dry-run agreement update`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `dry-run agreement update`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `retrieve agreement by id`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `retrieve agreement by id`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 15: Share Agreement With A Party

Business goal:
Registers a share event for a selected agreement party.

Domain context:
This is a distinct domain-facing capability in the `Draft Agreement Change And Sharing` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) and capture `avtaleId`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with valid phone numbers on current content.
3. Use function `share agreement with a party` (`POST /avtaler/{avtaleId}/del-med-avtalepart`) with `avtaleId={capturedAvtaleId}` and body `Avtalerolle={DELTAKER|ARBEIDSGIVER|MENTOR}`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Registers a share event for a selected agreement party.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `share agreement with a party`
  - Failure condition: agreement does not exist.
  - Why it fails: repository lookup throws before sharing.
  - Violated prerequisite or constraint: agreement must exist.
- Failing function: `share agreement with a party`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `Avtale.delMedAvtalepart` rejects inactive agreements.
  - Violated prerequisite or constraint: only active agreements can be shared.
- Failing function: `share agreement with a party`
  - Failure condition: selected party phone number is not a valid mobile number.
  - Why it fails: `TelefonnummerValidator` rejects the role-specific phone number with `UGYLDIG_TLF`.
  - Violated prerequisite or constraint: party contact data must contain valid mobile number.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases. Implementation does not call advisor `sjekkTilgang` before sharing; this access gap is listed as missing behavior.

### Behavior 16: Approve Agreement As Participant

Business goal:
Records participant approval on a complete agreement.

Domain context:
This is a distinct domain-facing capability in the `Agreement Approval Lifecycle` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) and capture `avtaleId`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content and current `sistEndret`.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with `avtaleId={capturedAvtaleId}`, `innlogget-part=DELTAKER`, and fresh `If-Unmodified-Since`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Records participant approval on a complete agreement.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing `sumLonnstilskudd`, `lonnstilskuddProsent`, or periods.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 17: Approve Agreement As Employer

Business goal:
Records employer approval on a complete agreement.

Domain context:
This is a distinct domain-facing capability in the `Agreement Approval Lifecycle` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) and capture `avtaleId`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content and current `sistEndret`.
3. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with `avtaleId={capturedAvtaleId}`, `innlogget-part=ARBEIDSGIVER`, and fresh `If-Unmodified-Since`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Records employer approval on a complete agreement.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing `sumLonnstilskudd`, `lonnstilskuddProsent`, or periods.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 18: Sign Mentor Confidentiality Declaration

Business goal:
Records mentor signing of the confidentiality declaration.

Domain context:
This is a distinct domain-facing capability in the `Agreement Approval Lifecycle` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create mentor agreement as advisor` (`POST /avtaler/opprett-mentor-avtale`) with `tiltakstype=MENTOR` and distinct participant/mentor ids.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete mentor content.
3. Use function `sign mentor confidentiality declaration` (`POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring, POST /avtaler/{avtaleId}/godkjenn`) with `avtaleId={capturedAvtaleId}`, `innlogget-part=MENTOR`, and fresh `If-Unmodified-Since`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Records mentor signing of the confidentiality declaration.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `sign mentor confidentiality declaration`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `sign mentor confidentiality declaration`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `sign mentor confidentiality declaration`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `sign mentor confidentiality declaration`
  - Failure condition: caller is not mentor role.
  - Why it fails: the dedicated endpoint checks that the resolved party role is `MENTOR`.
  - Violated prerequisite or constraint: only mentor can sign confidentiality.
- Failing function: `sign mentor confidentiality declaration`
  - Failure condition: mentor signing already exists.
  - Why it fails: `godkjennForMentor` rejects duplicate signing.
  - Violated prerequisite or constraint: mentor can sign once.
- Failing function: `create mentor agreement as advisor`
  - Failure condition: `deltakerFnr` equals `mentorFnr`.
  - Why it fails: the controller explicitly rejects the same person as participant and mentor.
  - Violated prerequisite or constraint: participant and mentor must be distinct.
- Failing function: `create mentor agreement as advisor`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create mentor agreement as advisor`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create mentor agreement as advisor`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create mentor agreement as advisor`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 19: Approve Agreement As Advisor

Business goal:
Records advisor approval and can enter non-decision-maker agreements.

Domain context:
This is a distinct domain-facing capability in the `Agreement Approval Lifecycle` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) and capture `avtaleId`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh timestamp.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh timestamp.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with `innlogget-part=VEILEDER` and fresh `If-Unmodified-Since`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Records advisor approval and can enter non-decision-maker agreements.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing `sumLonnstilskudd`, `lonnstilskuddProsent`, or periods.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: advisor approval already exists.
  - Why it fails: `godkjennForVeileder` rejects duplicate advisor approval.
  - Violated prerequisite or constraint: advisor can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is ufordelt.
  - Why it fails: `AvtaleErIkkeFordeltException` is thrown.
  - Violated prerequisite or constraint: agreement must have assigned advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: mentor agreement lacks mentor confidentiality signing.
  - Why it fails: `MENTOR_MÅ_SIGNERE_TAUSHETSERKLÆRING` is thrown.
  - Violated prerequisite or constraint: mentor must sign first.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant violates age-at-start/end rules.
  - Why it fails: advisor approval rechecks Sommerjobb start-age and non-Sommerjobb end-age limits.
  - Violated prerequisite or constraint: participant must remain age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 20: Approve On Behalf Of Participant

Business goal:
Lets an advisor approve for the participant with a recorded reason.

Domain context:
This is a distinct domain-facing capability in the `Agreement Approval Lifecycle` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) and complete it with `update agreement`.
2. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) so employer approval exists.
3. Use function `approve on behalf of participant` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av, POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker`) with body `GodkjentPaVegneGrunn` where at least one reason flag is true.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Lets an advisor approve for the participant with a recorded reason.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `approve on behalf of participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve on behalf of participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve on behalf of participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve on behalf of participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve on behalf of participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve on behalf of participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing `sumLonnstilskudd`, `lonnstilskuddProsent`, or periods.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve on behalf of participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve on behalf of participant`
  - Failure condition: participant has already approved.
  - Why it fails: `DeltakerHarGodkjentException` prevents replacing real participant approval.
  - Violated prerequisite or constraint: participant approval must be absent.
- Failing function: `approve on behalf of participant`
  - Failure condition: employer has not approved.
  - Why it fails: `ArbeidsgiverSkalGodkjenneFørVeilederException` enforces employer-first order.
  - Violated prerequisite or constraint: employer approval must exist.
- Failing function: `approve on behalf of participant`
  - Failure condition: advisor approval already exists.
  - Why it fails: the method rejects already advisor-approved agreements.
  - Violated prerequisite or constraint: advisor approval must be absent.
- Failing function: `approve on behalf of participant`
  - Failure condition: no participant on-behalf reason is selected.
  - Why it fails: `GodkjentPaVegneGrunn.valgtMinstEnGrunn` rejects empty reason body.
  - Violated prerequisite or constraint: at least one reason must be selected.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 21: Approve On Behalf Of Employer

Business goal:
Lets an advisor approve for the employer with a recorded reason.

Domain context:
This is a distinct domain-facing capability in the `Agreement Approval Lifecycle` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with a supported subsidy-backed `tiltakstype` and complete it with `update agreement`.
2. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) so participant approval exists.
3. Use function `approve on behalf of employer` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver`) with body `GodkjentPaVegneAvArbeidsgiverGrunn` where at least one reason flag is true.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Lets an advisor approve for the employer with a recorded reason.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `approve on behalf of employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve on behalf of employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve on behalf of employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve on behalf of employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve on behalf of employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve on behalf of employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing `sumLonnstilskudd`, `lonnstilskuddProsent`, or periods.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve on behalf of employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve on behalf of employer`
  - Failure condition: measure is not Sommerjobb, temporary wage subsidy, or permanent wage subsidy.
  - Why it fails: the method throws `GODKJENN_PAA_VEGNE_AV_FEIL_TILTAKSTYPE`.
  - Violated prerequisite or constraint: measure must support employer on-behalf approval.
- Failing function: `approve on behalf of employer`
  - Failure condition: employer has already approved.
  - Why it fails: the method rejects existing employer approval.
  - Violated prerequisite or constraint: employer approval must be absent.
- Failing function: `approve on behalf of employer`
  - Failure condition: participant has not approved.
  - Why it fails: `DELTAKER_SKAL_GODKJENNE_FOER_VEILEDER` is thrown.
  - Violated prerequisite or constraint: participant approval must exist.
- Failing function: `approve on behalf of employer`
  - Failure condition: no employer on-behalf reason is selected.
  - Why it fails: the employer reason object rejects empty reason body.
  - Violated prerequisite or constraint: at least one reason must be selected.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 22: Approve On Behalf Of Participant And Employer

Business goal:
Lets an advisor approve for both external parties with recorded reasons.

Domain context:
This is a distinct domain-facing capability in the `Agreement Approval Lifecycle` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with a supported subsidy-backed `tiltakstype` and complete it with `update agreement`.
2. Use function `approve on behalf of participant and employer` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`) with both participant and employer on-behalf reason objects, each having at least one true flag.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Lets an advisor approve for both external parties with recorded reasons.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing `sumLonnstilskudd`, `lonnstilskuddProsent`, or periods.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: measure is not Sommerjobb, temporary wage subsidy, or permanent wage subsidy.
  - Why it fails: the method throws `GODKJENN_PAA_VEGNE_AV_FEIL_TILTAKSTYPE`.
  - Violated prerequisite or constraint: measure must support combined on-behalf approval.
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: participant already approved.
  - Why it fails: the method rejects existing participant approval.
  - Violated prerequisite or constraint: participant approval must be absent.
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: employer already approved.
  - Why it fails: the method rejects existing employer approval.
  - Violated prerequisite or constraint: employer approval must be absent.
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: participant or employer reason set is empty.
  - Why it fails: the nested reason objects each require at least one selected reason.
  - Violated prerequisite or constraint: both reason sets must be non-empty.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 23: Revoke Approvals

Business goal:
Clears recorded approvals before an agreement has been entered.

Domain context:
This is a distinct domain-facing capability in the `Agreement Approval Lifecycle` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) and complete it with `update agreement`.
2. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) or `approve agreement as employer` to create at least one approval.
3. Use function `revoke approvals` (`POST /avtaler/{avtaleId}/opphev-godkjenninger`) with `avtaleId={capturedAvtaleId}` before agreement entry.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Clears recorded approvals before an agreement has been entered.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `revoke approvals`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `revoke approvals`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `revoke approvals`
  - Failure condition: caller/state cannot revoke approvals.
  - Why it fails: `kanOppheveGodkjenninger` rejects participants, mentors, decision-makers, and employers after advisor approval.
  - Violated prerequisite or constraint: caller role and state must allow revocation.
- Failing function: `revoke approvals`
  - Failure condition: no approvals exist.
  - Why it fails: `opphevGodkjenninger` rejects when there is nothing to revoke.
  - Violated prerequisite or constraint: at least one approval must exist.
- Failing function: `revoke approvals`
  - Failure condition: agreement is already entered.
  - Why it fails: `KAN_IKKE_OPPHEVE_GODKJENNINGER_VED_INNGAATT_AVTALE` is thrown.
  - Violated prerequisite or constraint: revocation is pre-entry only.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 24: Mark Agreement Eligible For After-registration

Business goal:
Toggles after-registration eligibility from false to true.

Domain context:
This is a distinct domain-facing capability in the `Agreement Approval Lifecycle` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) or reuse an accessible not-entered agreement.
2. Use function `mark agreement eligible for after-registration` (`POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`) with `avtaleId={capturedAvtaleId}` when current `godkjentForEtterregistrering=false`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Toggles after-registration eligibility from false to true.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `mark agreement eligible for after-registration`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `mark agreement eligible for after-registration`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `mark agreement eligible for after-registration`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `mark agreement eligible for after-registration`
  - Failure condition: agreement is already entered.
  - Why it fails: `togglegodkjennEtterregistrering` rejects entered agreements.
  - Violated prerequisite or constraint: after-registration toggling is pre-entry only.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 25: Remove After-registration Eligibility

Business goal:
Toggles after-registration eligibility from true to false.

Domain context:
This is a distinct domain-facing capability in the `Agreement Approval Lifecycle` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) or reuse an accessible not-entered agreement.
2. Use function `remove after-registration eligibility` (`POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`) with `avtaleId={capturedAvtaleId}` when current `godkjentForEtterregistrering=true`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Toggles after-registration eligibility from true to false.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `remove after-registration eligibility`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `remove after-registration eligibility`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `remove after-registration eligibility`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `remove after-registration eligibility`
  - Failure condition: agreement is already entered.
  - Why it fails: `togglegodkjennEtterregistrering` rejects entered agreements.
  - Violated prerequisite or constraint: after-registration toggling is pre-entry only.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 26: Approve Subsidy Period

Business goal:
Approves the current subsidy period and can enter a subsidy-backed agreement.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with `tiltakstype={SOMMERJOBB|MIDLERTIDIG_LONNSTILSKUDD|VARIG_LONNSTILSKUDD}`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete subsidy content so periods are generated.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `approve subsidy period` (`POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`) with body `enhet={fourDigitCostUnit}`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Approves the current subsidy period and can enter a subsidy-backed agreement.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `approve subsidy period`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve subsidy period`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve subsidy period`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve subsidy period`
  - Failure condition: advisor approval is missing.
  - Why it fails: period treatment rejects agreements not approved by advisor.
  - Violated prerequisite or constraint: advisor approval must exist.
- Failing function: `approve subsidy period`
  - Failure condition: `enhet` is null, not four digits, or unknown in Norg2.
  - Why it fails: the method rejects invalid format and `Beslutter` rejects missing unit.
  - Violated prerequisite or constraint: decision unit must be valid.
- Failing function: `approve subsidy period`
  - Failure condition: decision-maker is the same NAV ident that advisor-approved the agreement.
  - Why it fails: `TILSKUDDSPERIODE_IKKE_GODKJENNE_EGNE` is thrown.
  - Violated prerequisite or constraint: decision-maker must be distinct from advisor approver.
- Failing function: `approve subsidy period`
  - Failure condition: current period is not `UBEHANDLET`.
  - Why it fails: `TilskuddPeriode.sjekkOmKanBehandles` rejects already treated periods.
  - Violated prerequisite or constraint: current period must be unhandled.
- Failing function: `approve subsidy period`
  - Failure condition: current period is too early to decide.
  - Why it fails: period treatment is blocked until `kanBesluttesFom`.
  - Violated prerequisite or constraint: current date must be inside treatment window.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 27: Reject Subsidy Period

Business goal:
Rejects the current subsidy period with causes and explanation.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with `tiltakstype={SOMMERJOBB|MIDLERTIDIG_LONNSTILSKUDD|VARIG_LONNSTILSKUDD}`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete subsidy content so periods are generated.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `reject subsidy period` (`POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`) with body `avslagsårsaker={nonEmptySet}` and `avslagsforklaring={nonBlankText}`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Rejects the current subsidy period with causes and explanation.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `reject subsidy period`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `reject subsidy period`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `reject subsidy period`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `reject subsidy period`
  - Failure condition: advisor approval is missing.
  - Why it fails: period treatment rejects agreements not approved by advisor.
  - Violated prerequisite or constraint: advisor approval must exist.
- Failing function: `reject subsidy period`
  - Failure condition: current period is not `UBEHANDLET`.
  - Why it fails: period treatment rejects already treated periods.
  - Violated prerequisite or constraint: current period must be unhandled.
- Failing function: `reject subsidy period`
  - Failure condition: current period is too early to decide.
  - Why it fails: period treatment is blocked until `kanBesluttesFom`.
  - Violated prerequisite or constraint: current date must be inside treatment window.
- Failing function: `reject subsidy period`
  - Failure condition: `avslagsforklaring` is blank.
  - Why it fails: `TilskuddPeriode.avslå` requires explanation.
  - Violated prerequisite or constraint: rejection must include explanation.
- Failing function: `reject subsidy period`
  - Failure condition: `avslagsårsaker` is empty.
  - Why it fails: `TilskuddPeriode.avslå` requires at least one cause.
  - Violated prerequisite or constraint: rejection must include causes.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 28: Send Rejected Subsidy Period Back

Business goal:
Deactivates rejected active periods and creates replacement unhandled periods.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with `tiltakstype={SOMMERJOBB|MIDLERTIDIG_LONNSTILSKUDD|VARIG_LONNSTILSKUDD}`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete subsidy content so periods are generated.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `reject subsidy period` (`POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`) with valid rejection body.
7. Use function `send rejected subsidy period back` (`POST /avtaler/{avtaleId}/send-tilbake-til-beslutter`) with the same `avtaleId`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Deactivates rejected active periods and creates replacement unhandled periods.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `send rejected subsidy period back`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `send rejected subsidy period back`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `send rejected subsidy period back`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.
- Failing function: `reject subsidy period`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `reject subsidy period`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `reject subsidy period`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `reject subsidy period`
  - Failure condition: advisor approval is missing.
  - Why it fails: period treatment rejects agreements not approved by advisor.
  - Violated prerequisite or constraint: advisor approval must exist.
- Failing function: `reject subsidy period`
  - Failure condition: `avslagsforklaring` is blank or `avslagsårsaker` is empty.
  - Why it fails: `TilskuddPeriode.avslå` requires explanation and at least one cause.
  - Violated prerequisite or constraint: rejection must include cause and explanation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 29: Shorten Agreement

Business goal:
Creates an approved shorter version and adjusts periods.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with the required `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `shorten agreement` (`POST /avtaler/{avtaleId}/forkort`) with `avtaleId={capturedAvtaleId}` and the endpoint-specific body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates an approved shorter version and adjusts periods.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `shorten agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `shorten agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `shorten agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `shorten agreement`
  - Failure condition: advisor approval is missing.
  - Why it fails: shortening rejects not advisor-approved agreements.
  - Violated prerequisite or constraint: agreement must be advisor-approved.
- Failing function: `shorten agreement`
  - Failure condition: new end date is not before current end date.
  - Why it fails: shortening rejects a non-shortening date.
  - Violated prerequisite or constraint: new end must be earlier.
- Failing function: `shorten agreement`
  - Failure condition: new end date is before protected paid/sent-claim period.
  - Why it fails: shortening blocks cutting before protected refund periods.
  - Violated prerequisite or constraint: paid/claim-sent periods must be preserved.
- Failing function: `shorten agreement`
  - Failure condition: reason is blank or `Annet` lacks custom text.
  - Why it fails: persistent shortening requires a reason.
  - Violated prerequisite or constraint: shortening requires reason text.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 30: Dry-run Agreement Shortening

Business goal:
Previews agreement shortening without saving.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with the required `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `dry-run agreement shortening` (`POST /avtaler/{avtaleId}/forkort-dry-run`) with `avtaleId={capturedAvtaleId}` and the endpoint-specific body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Previews agreement shortening without saving.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `dry-run agreement shortening`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `dry-run agreement shortening`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `dry-run agreement shortening`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `dry-run agreement shortening`
  - Failure condition: advisor approval is missing.
  - Why it fails: shortening rejects not advisor-approved agreements.
  - Violated prerequisite or constraint: agreement must be advisor-approved.
- Failing function: `dry-run agreement shortening`
  - Failure condition: new end date is not before current end date.
  - Why it fails: shortening rejects a non-shortening date.
  - Violated prerequisite or constraint: new end must be earlier.
- Failing function: `dry-run agreement shortening`
  - Failure condition: new end date is before protected paid/sent-claim period.
  - Why it fails: shortening blocks cutting before protected refund periods.
  - Violated prerequisite or constraint: paid/claim-sent periods must be preserved.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 31: Extend Agreement

Business goal:
Creates an approved longer version and adjusts periods.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with the required `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `extend agreement` (`POST /avtaler/{avtaleId}/forleng`) with `avtaleId={capturedAvtaleId}` and the endpoint-specific body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates an approved longer version and adjusts periods.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `extend agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `extend agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `extend agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `extend agreement`
  - Failure condition: advisor approval is missing.
  - Why it fails: extension rejects not advisor-approved agreements.
  - Violated prerequisite or constraint: agreement must be advisor-approved.
- Failing function: `extend agreement`
  - Failure condition: new end date is not after current end date.
  - Why it fails: extension rejects a non-extension date.
  - Violated prerequisite or constraint: new end must be later.
- Failing function: `extend agreement`
  - Failure condition: new period violates measure date limits.
  - Why it fails: the measure date strategy is reapplied.
  - Violated prerequisite or constraint: extended period must remain valid.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 32: Dry-run Agreement Extension

Business goal:
Previews agreement extension without saving.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with the required `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `dry-run agreement extension` (`POST /avtaler/{avtaleId}/forleng-dry-run`) with `avtaleId={capturedAvtaleId}` and the endpoint-specific body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Previews agreement extension without saving.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `dry-run agreement extension`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `dry-run agreement extension`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `dry-run agreement extension`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `dry-run agreement extension`
  - Failure condition: advisor approval is missing.
  - Why it fails: extension rejects not advisor-approved agreements.
  - Violated prerequisite or constraint: agreement must be advisor-approved.
- Failing function: `dry-run agreement extension`
  - Failure condition: new end date is not after current end date.
  - Why it fails: extension rejects a non-extension date.
  - Violated prerequisite or constraint: new end must be later.
- Failing function: `dry-run agreement extension`
  - Failure condition: new period violates measure date limits.
  - Why it fails: the measure date strategy is reapplied.
  - Violated prerequisite or constraint: extended period must remain valid.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 33: Change Subsidy Calculation

Business goal:
Creates an approved version with changed wage-subsidy calculation fields.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with the required `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `change subsidy calculation` (`POST /avtaler/{avtaleId}/endre-tilskuddsberegning`) with `avtaleId={capturedAvtaleId}` and the endpoint-specific body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates an approved version with changed wage-subsidy calculation fields.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `change subsidy calculation`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `change subsidy calculation`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `change subsidy calculation`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `change subsidy calculation`
  - Failure condition: measure is not temporary wage subsidy, permanent wage subsidy, or Sommerjobb.
  - Why it fails: `krevEnAvTiltakstyper` rejects unsupported measures.
  - Violated prerequisite or constraint: agreement must be subsidy-backed wage measure.
- Failing function: `change subsidy calculation`
  - Failure condition: advisor approval is missing.
  - Why it fails: calculation change rejects not advisor-approved agreements.
  - Violated prerequisite or constraint: agreement must be advisor-approved.
- Failing function: `change subsidy calculation`
  - Failure condition: required calculation field is missing.
  - Why it fails: the method rejects missing employer tax, holiday pay, monthly wage, or OTP rate.
  - Violated prerequisite or constraint: all calculation fields must be present.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 34: Dry-run Subsidy Calculation Change

Business goal:
Previews wage-subsidy calculation changes without saving.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with the required `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `dry-run subsidy calculation change` (`POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run`) with `avtaleId={capturedAvtaleId}` and the endpoint-specific body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Previews wage-subsidy calculation changes without saving.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `dry-run subsidy calculation change`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `dry-run subsidy calculation change`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `dry-run subsidy calculation change`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `dry-run subsidy calculation change`
  - Failure condition: measure is not temporary wage subsidy, permanent wage subsidy, or Sommerjobb.
  - Why it fails: `krevEnAvTiltakstyper` rejects unsupported measures.
  - Violated prerequisite or constraint: agreement must be subsidy-backed wage measure.
- Failing function: `dry-run subsidy calculation change`
  - Failure condition: advisor approval is missing.
  - Why it fails: calculation change rejects not advisor-approved agreements.
  - Violated prerequisite or constraint: agreement must be advisor-approved.
- Failing function: `dry-run subsidy calculation change`
  - Failure condition: required calculation field is missing.
  - Why it fails: the method rejects missing employer tax, holiday pay, monthly wage, or OTP rate.
  - Violated prerequisite or constraint: all calculation fields must be present.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 35: Change Contact Information

Business goal:
Creates an approved version with changed contact information.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with the required `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `change contact information` (`POST /avtaler/{avtaleId}/endre-kontaktinfo`) with `avtaleId={capturedAvtaleId}` and the endpoint-specific body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates an approved version with changed contact information.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `change contact information`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `change contact information`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `change contact information`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `change contact information`
  - Failure condition: advisor approval is missing.
  - Why it fails: the domain method rejects changes before advisor approval.
  - Violated prerequisite or constraint: agreement must be advisor-approved.
- Failing function: `change contact information`
  - Failure condition: contact field is missing.
  - Why it fails: the domain method checks endpoint-specific required fields.
  - Violated prerequisite or constraint: required change fields must be present.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 36: Change Job Description

Business goal:
Creates an approved version with changed job data.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with the required `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `change job description` (`POST /avtaler/{avtaleId}/endre-stillingbeskrivelse`) with `avtaleId={capturedAvtaleId}` and the endpoint-specific body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates an approved version with changed job data.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `change job description`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `change job description`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `change job description`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `change job description`
  - Failure condition: advisor approval is missing.
  - Why it fails: the domain method rejects changes before advisor approval.
  - Violated prerequisite or constraint: agreement must be advisor-approved.
- Failing function: `change job description`
  - Failure condition: job-description field is missing.
  - Why it fails: the domain method checks endpoint-specific required fields.
  - Violated prerequisite or constraint: required change fields must be present.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 37: Change Follow-up And Adaptation Text

Business goal:
Creates an approved version with changed follow-up text.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with the required `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `change follow-up and adaptation text` (`POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging`) with `avtaleId={capturedAvtaleId}` and the endpoint-specific body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates an approved version with changed follow-up text.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `change follow-up and adaptation text`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `change follow-up and adaptation text`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `change follow-up and adaptation text`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `change follow-up and adaptation text`
  - Failure condition: advisor approval is missing.
  - Why it fails: the domain method rejects changes before advisor approval.
  - Violated prerequisite or constraint: agreement must be advisor-approved.
- Failing function: `change follow-up and adaptation text`
  - Failure condition: follow-up or adaptation text is missing.
  - Why it fails: the domain method checks endpoint-specific required fields.
  - Violated prerequisite or constraint: required change fields must be present.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 38: Change Work-training Goals

Business goal:
Replaces goals on a work-training agreement.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with the required `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `change work-training goals` (`POST /avtaler/{avtaleId}/endre-maal`) with `avtaleId={capturedAvtaleId}` and the endpoint-specific body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Replaces goals on a work-training agreement.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `change work-training goals`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `change work-training goals`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `change work-training goals`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `change work-training goals`
  - Failure condition: measure is not `ARBEIDSTRENING`.
  - Why it fails: `krevEnAvTiltakstyper` rejects unsupported measures.
  - Violated prerequisite or constraint: agreement must be work training.
- Failing function: `change work-training goals`
  - Failure condition: advisor approval is missing.
  - Why it fails: the method rejects not-entered work-training agreements.
  - Violated prerequisite or constraint: agreement must be advisor-approved.
- Failing function: `change work-training goals`
  - Failure condition: goal list is empty.
  - Why it fails: the method throws `KAN_IKKE_ENDRE_MAAL_TOM_LISTE`.
  - Violated prerequisite or constraint: at least one goal is required.
- Failing function: `change work-training goals`
  - Failure condition: a goal lacks description or category.
  - Why it fails: the method throws `KAN_IKKE_ENDRE_MAAL_IKKE_BESKRIVELSE_ELLER_KATEGORI`.
  - Violated prerequisite or constraint: each goal needs description and category.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 39: Change Inclusion Subsidy Expenses

Business goal:
Replaces inclusion-subsidy expenses.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with the required `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `change inclusion subsidy expenses` (`POST /avtaler/{avtaleId}/endre-inkluderingstilskudd`) with `avtaleId={capturedAvtaleId}` and the endpoint-specific body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Replaces inclusion-subsidy expenses.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `change inclusion subsidy expenses`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `change inclusion subsidy expenses`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `change inclusion subsidy expenses`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `change inclusion subsidy expenses`
  - Failure condition: measure is not `INKLUDERINGSTILSKUDD`.
  - Why it fails: `krevEnAvTiltakstyper` rejects unsupported measures.
  - Violated prerequisite or constraint: agreement must be inclusion subsidy.
- Failing function: `change inclusion subsidy expenses`
  - Failure condition: advisor approval is missing.
  - Why it fails: the method rejects not-entered inclusion-subsidy agreements.
  - Violated prerequisite or constraint: agreement must be advisor-approved.
- Failing function: `change inclusion subsidy expenses`
  - Failure condition: expense list is empty.
  - Why it fails: the method rejects an empty list.
  - Violated prerequisite or constraint: at least one expense is required.
- Failing function: `change inclusion subsidy expenses`
  - Failure condition: expense total exceeds cap.
  - Why it fails: the method throws `INKLUDERINGSTILSKUDD_SUM_FOR_HØY`.
  - Violated prerequisite or constraint: expense total must stay within cap.
- Failing function: `change inclusion subsidy expenses`
  - Failure condition: expense lacks amount or type.
  - Why it fails: the method rejects incomplete expense rows.
  - Violated prerequisite or constraint: each expense must include amount and type.
- Failing function: `change inclusion subsidy expenses`
  - Failure condition: request ids do not match previous-version expense count.
  - Why it fails: the implementation treats an out-of-sync client as invalid.
  - Violated prerequisite or constraint: client must be synchronized with existing expenses.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 40: Change Mentor Details

Business goal:
Changes mentor-specific approved content.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with the required `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `change mentor details` (`POST /avtaler/{avtaleId}/endre-om-mentor`) with `avtaleId={capturedAvtaleId}` and the endpoint-specific body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Changes mentor-specific approved content.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `change mentor details`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `change mentor details`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `change mentor details`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `change mentor details`
  - Failure condition: measure is not `MENTOR`.
  - Why it fails: `krevEnAvTiltakstyper` rejects unsupported measures.
  - Violated prerequisite or constraint: agreement must be mentor.
- Failing function: `change mentor details`
  - Failure condition: advisor approval is missing.
  - Why it fails: the method rejects not-entered mentor agreements.
  - Violated prerequisite or constraint: agreement must be advisor-approved.
- Failing function: `change mentor details`
  - Failure condition: mentor-specific field is missing.
  - Why it fails: the method rejects missing mentor name, phone, wage, hours, or tasks.
  - Violated prerequisite or constraint: all mentor details must be present.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 41: Change Cost Center

Business goal:
Changes cost center on editable subsidy periods.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with subsidy-backed `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete subsidy content to generate editable periods.
3. Use function `change cost center` (`POST /avtaler/{avtaleId}/endre-kostnadssted`) with body `enhet={fourDigitUnit}` before agreement entry.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Changes cost center on editable subsidy periods.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `change cost center`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `change cost center`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `change cost center`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `change cost center`
  - Failure condition: requested unit does not exist in Norg2.
  - Why it fails: `Veileder.oppdatereKostnadssted` throws `ENHET_FINNES_IKKE`.
  - Violated prerequisite or constraint: cost center unit must exist.
- Failing function: `change cost center`
  - Failure condition: no active unhandled or rejected periods exist.
  - Why it fails: the method throws `TILSKUDDSPERIODE_ER_IKKE_SATT`.
  - Violated prerequisite or constraint: editable periods must exist.
- Failing function: `change cost center`
  - Failure condition: agreement is already entered.
  - Why it fails: the method throws `KAN_IKKE_OPPDATERE_KOSTNADSSTED_INGAATT_AVTALE`.
  - Violated prerequisite or constraint: cost center can only change before entry.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 42: Adjust Arena Migration Date

Business goal:
Persists an Arena migration date and recalculates cleanup periods.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create Arena cleanup agreement` (`POST /avtaler`) with subsidy-backed `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete subsidy content.
3. Use function `adjust Arena migration date` (`POST /avtaler/{avtaleId}/juster-arena-migreringsdato`) with body `migreringsdato={yyyy-MM-dd}`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Persists an Arena migration date and recalculates cleanup periods.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `adjust Arena migration date`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `adjust Arena migration date`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `adjust Arena migration date`
  - Failure condition: agreement is already entered.
  - Why it fails: the persistent endpoint throws `KAN_IKKE_ENDRE_ARENA_MIGRERINGSDATO_INNGAATT_AVTALE`.
  - Violated prerequisite or constraint: migration date can only be adjusted before entry.
- Failing function: `create Arena cleanup agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create Arena cleanup agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create Arena cleanup agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create Arena cleanup agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 43: Dry-run Arena Migration Date Adjustment

Business goal:
Previews Arena migration-date recalculation.

Domain context:
This is a distinct domain-facing capability in the `Subsidy Period Decisioning And Agreement Changes` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create Arena cleanup agreement` (`POST /avtaler`) with subsidy-backed `tiltakstype`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete subsidy content.
3. Use function `dry-run Arena migration date adjustment` (`POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run`) with body `migreringsdato={yyyy-MM-dd}`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Previews Arena migration-date recalculation.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `dry-run Arena migration date adjustment`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `dry-run Arena migration date adjustment`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `create Arena cleanup agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create Arena cleanup agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create Arena cleanup agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create Arena cleanup agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases. Dry-run does not enforce the entered-agreement guard used by the persistent Arena migration-date endpoint.

### Behavior 44: Get Employer Account Number

Business goal:
Looks up the employer account number for the agreement company.

Domain context:
This is a distinct domain-facing capability in the `Agreement Retrieval And Versioning` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid body values and capture `avtaleId`.
2. Use function `get employer account number` (`GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver`) with `avtaleId={capturedAvtaleId}` and matching `innlogget-part`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Looks up the employer account number for the agreement company.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `get employer account number`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `get employer account number`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `get employer account number`
  - Failure condition: Kontoregister returns 404 for the company.
  - Why it fails: the client maps `NOT_FOUND` to `KONTOREGISTER_FEIL_BEDRIFT_IKKE_FUNNET`.
  - Violated prerequisite or constraint: company must have account record.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 45: Download Agreement Pdf

Business goal:
Downloads a generated agreement PDF when advisor approval exists.

Domain context:
This is a distinct domain-facing capability in the `Agreement Retrieval And Versioning` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid body values and capture `avtaleId`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content and current `sistEndret`.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
6. Use function `download agreement PDF` (`GET /avtaler/{avtaleId}/pdf`) with `avtaleId={capturedAvtaleId}` and matching `innlogget-part`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Downloads a generated agreement PDF when advisor approval exists.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `download agreement PDF`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `download agreement PDF`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `download agreement PDF`
  - Failure condition: advisor approval is missing.
  - Why it fails: the controller throws `KAN_IKKE_LASTE_NED_PDF`.
  - Violated prerequisite or constraint: agreement must be approved by advisor.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 46: Check Whether Salesforce Dialog Should Be Shown

Business goal:
Returns whether a Salesforce dialog should be shown for the agreement.

Domain context:
This is a distinct domain-facing capability in the `Agreement Retrieval And Versioning` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid body values and capture `avtaleId`.
2. Use function `check whether Salesforce dialog should be shown` (`GET /avtaler/{avtaleId}/vis-salesforce-dialog`) with `avtaleId={capturedAvtaleId}` and matching `innlogget-part`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Returns whether a Salesforce dialog should be shown for the agreement.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `check whether Salesforce dialog should be shown`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `check whether Salesforce dialog should be shown`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 47: Refresh Follow-up Unit

Business goal:
Refreshes participant and NAV unit data.

Domain context:
This is a distinct domain-facing capability in the `Draft Agreement Change And Sharing` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid body values and capture `avtaleId`.
2. Use function `refresh follow-up unit` (`POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet`) with `avtaleId={capturedAvtaleId}` and required body/header values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Refreshes participant and NAV unit data.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `refresh follow-up unit`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `refresh follow-up unit`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `refresh follow-up unit`
  - Failure condition: PDL marks participant as Code 6 protected.
  - Why it fails: refresh uses the same persondata check as advisor creation.
  - Violated prerequisite or constraint: protected participant cannot be refreshed through advisor path.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 48: Take Over Agreement As Advisor

Business goal:
Assigns the logged-in advisor to the agreement.

Domain context:
This is a distinct domain-facing capability in the `Draft Agreement Change And Sharing` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create employer agreement` (`POST /avtaler/opprett-som-arbeidsgiver`) with valid body values and capture `avtaleId`.
2. Use function `take over agreement as advisor` (`PUT /avtaler/{avtaleId}/overta`) with `avtaleId={capturedAvtaleId}` and required body/header values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Assigns the logged-in advisor to the agreement.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `take over agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `take over agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `take over agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `take over agreement as advisor`
  - Failure condition: logged-in advisor is already current advisor.
  - Why it fails: `ErAlleredeVeilederException` is thrown.
  - Violated prerequisite or constraint: new advisor must differ from current advisor.
- Failing function: `create employer agreement`
  - Failure condition: employer lacks Altinn access for `bedriftNr` and `tiltakstype`.
  - Why it fails: `Arbeidsgiver.tilgangTilBedriftVedOpprettelseAvAvtale` rejects the selected company/measure.
  - Violated prerequisite or constraint: employer must have measure-specific Altinn rights.
- Failing function: `create employer agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create employer agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 49: Annul Agreement

Business goal:
Annuls an agreement and eligible subsidy periods.

Domain context:
This is a distinct domain-facing capability in the `Agreement Termination` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid body values and capture `avtaleId`.
2. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) to capture current `sistEndret`.
3. Use function `annul agreement` (`POST /avtaler/{avtaleId}/annuller`) with `avtaleId={capturedAvtaleId}` and required body/header values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Annuls an agreement and eligible subsidy periods.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `annul agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `annul agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `annul agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `annul agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `annul agreement`
  - Failure condition: agreement has a paid subsidy period.
  - Why it fails: the agreement throws `AVTALE_INNEHOLDER_UTBETALT_TILSKUDDSPERIODE`.
  - Violated prerequisite or constraint: paid periods cannot be annulled with the agreement.
- Failing function: `annul agreement`
  - Failure condition: agreement has a sent/approved refund claim period.
  - Why it fails: the agreement throws `AVTALE_INNEHOLDER_TILSKUDDSPERIODE_MED_GODKJENT_REFUSJON`.
  - Violated prerequisite or constraint: refund-approved periods cannot be annulled with the agreement.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `retrieve agreement by id`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `retrieve agreement by id`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 50: Soft-delete Agreement

Business goal:
Hides an agreement by setting `slettemerket=true`.

Domain context:
This is a distinct domain-facing capability in the `Agreement Termination` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid body values and capture `avtaleId`.
2. Use function `soft-delete agreement` (`POST /avtaler/{avtaleId}/slettemerk`) with `avtaleId={capturedAvtaleId}` and required body/header values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Hides an agreement by setting `slettemerket=true`.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `soft-delete agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `soft-delete agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 51: List Employer Agreements For Min Side Arbeidsgiver

Business goal:
Lists employer-visible agreements for a company.

Domain context:
This is a distinct domain-facing capability in the `Role-Specific Lists, User Context, Reference Data, And Integrations` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `list employer agreements for Min Side Arbeidsgiver` (`GET /avtaler/min-side-arbeidsgiver`) with query `bedriftNr={employerUnitNumber}` as an employer.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Lists employer-visible agreements for a company.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 52: List Decision-maker Agreements

Business goal:
Lists agreements/periods in a decision-maker work queue.

Domain context:
This is a distinct domain-facing capability in the `Role-Specific Lists, User Context, Reference Data, And Integrations` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `list decision-maker agreements` (`GET /avtaler/beslutter-liste`) with optional query filters `tilskuddPeriodeStatus`, `tiltakstype`, `bedriftNr`, `avtaleNr`, `navEnhet`, `page`, `size`, `sorteringskolonne`, and `sorteringOrder`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Lists agreements/periods in a decision-maker work queue.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `list decision-maker agreements`
  - Failure condition: decision-maker has no NAV units from Axsys.
  - Why it fails: `Beslutter.finnGodkjente...` throws `NavEnhetIkkeFunnetException`.
  - Violated prerequisite or constraint: decision-maker must have at least one NAV unit.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 53: Get Logged-in User

Business goal:
Returns role-specific logged-in user information.

Domain context:
This is a distinct domain-facing capability in the `Role-Specific Lists, User Context, Reference Data, And Integrations` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `get logged-in user` (`GET /innlogget-bruker`) with cookie `innlogget-part={role}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Request query values, if any, are consumed directly to compute the read response; no persisted service id is reused.
- No additional persisted binding is required.
- No timestamp or generated-id reuse is required unless supplied by the caller.

Business result:
Returns role-specific logged-in user information.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 54: Look Up Organization

Business goal:
Retrieves employer organization information from Ereg.

Domain context:
This is a distinct domain-facing capability in the `Role-Specific Lists, User Context, Reference Data, And Integrations` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `look up organization` (`GET /organisasjoner`) with query `bedriftNr={employerUnitNumber}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Request query values, if any, are consumed directly to compute the read response; no persisted service id is reused.
- No additional persisted binding is required.
- No timestamp or generated-id reuse is required unless supplied by the caller.

Business result:
Retrieves employer organization information from Ereg.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `look up organization`
  - Failure condition: Ereg does not return the unit.
  - Why it fails: `EregService` maps lookup failure to `ENHET_FINNES_IKKE`.
  - Violated prerequisite or constraint: organization unit must exist.
- Failing function: `look up organization`
  - Failure condition: Ereg classifies the unit as `JuridiskEnhet`.
  - Why it fails: the service rejects legal entities with `ENHET_ER_JURIDISK`.
  - Violated prerequisite or constraint: selected unit must be a business subunit.
- Failing function: `look up organization`
  - Failure condition: Ereg classifies the unit as `Organisasjonsledd`.
  - Why it fails: the service rejects organization links with `ENHET_ER_ORGLEDD`.
  - Violated prerequisite or constraint: selected unit must be a business subunit.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 55: Get Altinn Rights Request Urls

Business goal:
Returns URLs for requesting Altinn rights.

Domain context:
This is a distinct domain-facing capability in the `Role-Specific Lists, User Context, Reference Data, And Integrations` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `get Altinn rights request URLs` (`GET /be-om-altinn-rettighet-urler`) with query `orgNr={organizationNumber}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Request query values, if any, are consumed directly to compute the read response; no persisted service id is reused.
- No additional persisted binding is required.
- No timestamp or generated-id reuse is required unless supplied by the caller.

Business result:
Returns URLs for requesting Altinn rights.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 56: Get All Code Lists

Business goal:
Returns measure and status code lists.

Domain context:
This is a distinct domain-facing capability in the `Role-Specific Lists, User Context, Reference Data, And Integrations` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `get all code lists` (`GET /kodeverk`) with no business body.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Request query values, if any, are consumed directly to compute the read response; no persisted service id is reused.
- No additional persisted binding is required.
- No timestamp or generated-id reuse is required unless supplied by the caller.

Business result:
Returns measure and status code lists.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 57: Get Status Code List

Business goal:
Returns agreement statuses.

Domain context:
This is a distinct domain-facing capability in the `Role-Specific Lists, User Context, Reference Data, And Integrations` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `get status code list` (`GET /kodeverk/statuser`) with no business body.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Request query values, if any, are consumed directly to compute the read response; no persisted service id is reused.
- No additional persisted binding is required.
- No timestamp or generated-id reuse is required unless supplied by the caller.

Business result:
Returns agreement statuses.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 58: Get Measure Type Code List

Business goal:
Returns measure types.

Domain context:
This is a distinct domain-facing capability in the `Role-Specific Lists, User Context, Reference Data, And Integrations` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `get measure type code list` (`GET /kodeverk/tiltakstyper`) with no business body.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Request query values, if any, are consumed directly to compute the read response; no persisted service id is reused.
- No additional persisted binding is required.
- No timestamp or generated-id reuse is required unless supplied by the caller.

Business result:
Returns measure types.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 59: Evaluate Feature Toggles

Business goal:
Evaluates Unleash feature toggles.

Domain context:
This is a distinct domain-facing capability in the `Role-Specific Lists, User Context, Reference Data, And Integrations` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `evaluate feature toggles` (`GET /feature`) with repeated query `feature={featureName}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Request query values, if any, are consumed directly to compute the read response; no persisted service id is reused.
- No additional persisted binding is required.
- No timestamp or generated-id reuse is required unless supplied by the caller.

Business result:
Evaluates Unleash feature toggles.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 60: Get Feature Variants

Business goal:
Returns Unleash variants.

Domain context:
This is a distinct domain-facing capability in the `Role-Specific Lists, User Context, Reference Data, And Integrations` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `get feature variants` (`GET /feature/variant`) with repeated query `feature={featureName}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Request query values, if any, are consumed directly to compute the read response; no persisted service id is reused.
- No additional persisted binding is required.
- No timestamp or generated-id reuse is required unless supplied by the caller.

Business result:
Returns Unleash variants.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 61: Health Check

Business goal:
Checks database health.

Domain context:
This is a distinct domain-facing capability in the `Role-Specific Lists, User Context, Reference Data, And Integrations` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `health check` (`GET /internal/healthcheck`) with no business body.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Request query values, if any, are consumed directly to compute the read response; no persisted service id is reused.
- No additional persisted binding is required.
- No timestamp or generated-id reuse is required unless supplied by the caller.

Business result:
Checks database health.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 62: List Overview Notifications

Business goal:
Lists unread bell notifications for the logged-in party.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `list overview notifications` (`GET /varsler/oversikt`) with `innlogget-part={role}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Lists unread bell notifications for the logged-in party.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 63: List Agreement Modal Notifications

Business goal:
Lists unread bell notifications for one agreement.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `list agreement modal notifications` (`GET /varsler/avtale-modal`) with query `avtaleId={agreementId}` and `innlogget-part={role}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Lists unread bell notifications for one agreement.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 64: List Agreement Notification Log

Business goal:
Lists all notifications for one agreement and receiver role.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state for the complete API-realizable workflow.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) or reuse an accessible agreement and capture `avtaleId`.
2. Use function `list agreement notification log` (`GET /varsler/avtale-logg`) with query `avtaleId={capturedAvtaleId}` and `innlogget-part={role}`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Lists all notifications for one agreement and receiver role.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `list agreement notification log`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `list agreement notification log`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 65: Mark Notification As Read

Business goal:
Marks one caller-owned notification as read.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `mark notification as read` (`POST /varsler/{varselId}/sett-til-lest`) with path `varselId={ownedVarselId}` and `innlogget-part={role}`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Marks one caller-owned notification as read.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `mark notification as read`
  - Failure condition: notification id is unknown or not owned/readable by caller.
  - Why it fails: repository lookup by id and caller identifiers returns null and the endpoint fails when marking read.
  - Violated prerequisite or constraint: caller must own the notification.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 66: Mark Multiple Notifications As Read

Business goal:
Marks several caller-owned notifications as read.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `mark multiple notifications as read` (`POST /varsler/sett-alle-til-lest`) with body `[{ownedVarselId1}, {ownedVarselId2}]` and `innlogget-part={role}`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Marks several caller-owned notifications as read.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `mark multiple notifications as read`
  - Failure condition: any supplied notification id is unknown or not owned/readable by caller.
  - Why it fails: the loop delegates to single-notification marking.
  - Violated prerequisite or constraint: all supplied notifications must belong to caller.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 67: List Unjournaled Agreements

Business goal:
Exports agreement versions requiring journalføring.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `list unjournaled agreements` (`GET /internal/avtaler`) as configured system user.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Exports agreement versions requiring journalføring.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 68: Mark Agreement Versions As Journaled

Business goal:
Stores journal post ids on exported agreement versions.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `list unjournaled agreements` (`GET /internal/avtaler`) to obtain `avtaleVersjonId` values.
2. Use function `mark agreement versions as journaled` (`PUT /internal/avtaler`) with body `{ "{avtaleVersjonId}": "{journalpostId}" }`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Stores journal post ids on exported agreement versions.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 69: Recalculate Wage Subsidy For Selected Agreements

Business goal:
Recalculates missing wage-subsidy totals on selected agreements.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `recalculate wage subsidy for selected agreements` (`POST /utvikler-admin/reberegn`) with the endpoint-specific selected ids, path date, or body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Recalculates missing wage-subsidy totals on selected agreements.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `recalculate wage subsidy for selected agreements`
  - Failure condition: selected agreement id does not exist.
  - Why it fails: `findById(...).orElseThrow()` aborts the batch.
  - Violated prerequisite or constraint: all selected ids must exist.
- Failing function: `recalculate wage subsidy for selected agreements`
  - Failure condition: agreement is inactive.
  - Why it fails: `reberegnLønnstilskudd` rejects annulled/interrupted agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `recalculate wage subsidy for selected agreements`
  - Failure condition: agreement measure is unsupported.
  - Why it fails: `krevEnAvTiltakstyper` rejects non wage-subsidy/Sommerjobb measures.
  - Violated prerequisite or constraint: agreement must be subsidy-backed wage measure.
- Failing function: `recalculate wage subsidy for selected agreements`
  - Failure condition: sum is already set or required calculation inputs are missing.
  - Why it fails: the method throws `KAN_IKKE_REBEREGNE` unless the exact missing-sum repair state exists.
  - Violated prerequisite or constraint: repair target must have missing sum and complete inputs.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 70: Fix Missing Reduced-percent Date In Batch

Business goal:
Repairs permanent wage-subsidy reduced-percent data.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `fix missing reduced-percent date in batch` (`POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}`) with the endpoint-specific selected ids, path date, or body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Repairs permanent wage-subsidy reduced-percent data.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 71: Dry-run Missing Reduced-percent Date Fix

Business goal:
Counts/logs the same repair without saving.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `dry-run missing reduced-percent date fix` (`POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}`) with the endpoint-specific selected ids, path date, or body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Counts/logs the same repair without saving.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 72: Generate Subsidy Periods For One Agreement

Business goal:
Generates periods for one agreement.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `generate subsidy periods for one agreement` (`POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}`) with the endpoint-specific selected ids, path date, or body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Generates periods for one agreement.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `generate subsidy periods for one agreement`
  - Failure condition: agreement id does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException`.
  - Violated prerequisite or constraint: agreement must exist.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 73: Recalculate Unhandled Subsidy Periods

Business goal:
Removes and recreates unhandled periods.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `recalculate unhandled subsidy periods` (`POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}`) with the endpoint-specific selected ids, path date, or body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Removes and recreates unhandled periods.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `recalculate unhandled subsidy periods`
  - Failure condition: agreement id does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException`.
  - Violated prerequisite or constraint: agreement must exist.
- Failing function: `recalculate unhandled subsidy periods`
  - Failure condition: agreement measure is unsupported.
  - Why it fails: `krevEnAvTiltakstyper` rejects unsupported measures.
  - Violated prerequisite or constraint: agreement must be wage-subsidy/Sommerjobb.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 74: Find Subsidy Period Date-order Problems

Business goal:
Logs period ordering diagnostics.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `find subsidy period date-order problems` (`POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer`) with endpoint-specific request values.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Logs period ordering diagnostics.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 75: Annul Subsidy Period

Business goal:
Annuls one subsidy period.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `annul subsidy period` (`POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}`) with the endpoint-specific selected ids, path date, or body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Annuls one subsidy period.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `annul subsidy period`
  - Failure condition: period id does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException`.
  - Violated prerequisite or constraint: period must exist.
- Failing function: `annul subsidy period`
  - Failure condition: period refund status is `UTGÅTT`.
  - Why it fails: the domain method logs and does not set status or event.
  - Violated prerequisite or constraint: expired refund status prevents normal annulment side effect.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 76: Annul And Resend Subsidy Period As Approved

Business goal:
Annuls a period and creates an approved replacement.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `annul and resend subsidy period as approved` (`POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}`) with the endpoint-specific selected ids, path date, or body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Annuls a period and creates an approved replacement.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `annul and resend subsidy period as approved`
  - Failure condition: period id does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException`.
  - Violated prerequisite or constraint: period must exist.
- Failing function: `annul and resend subsidy period as approved`
  - Failure condition: parent agreement measure is unsupported.
  - Why it fails: replacement creation calls `krevEnAvTiltakstyper`.
  - Violated prerequisite or constraint: parent agreement must be subsidy-backed wage measure.
- Failing function: `annul and resend subsidy period as approved`
  - Failure condition: period cannot become `ANNULLERT`.
  - Why it fails: replacement creation rejects status not equal to `ANNULLERT`, including expired-refund cases.
  - Violated prerequisite or constraint: original period must be annullable.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 77: Annul And Generate Unhandled Subsidy Period

Business goal:
Annuls a period and creates an unhandled replacement.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `annul and generate unhandled subsidy period` (`POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}`) with the endpoint-specific selected ids, path date, or body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Annuls a period and creates an unhandled replacement.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `annul and generate unhandled subsidy period`
  - Failure condition: period id does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException`.
  - Violated prerequisite or constraint: period must exist.
- Failing function: `annul and generate unhandled subsidy period`
  - Failure condition: parent agreement measure is unsupported.
  - Why it fails: replacement creation calls `krevEnAvTiltakstyper`.
  - Violated prerequisite or constraint: parent agreement must be subsidy-backed wage measure.
- Failing function: `annul and generate unhandled subsidy period`
  - Failure condition: period cannot become `ANNULLERT`.
  - Why it fails: replacement creation rejects status not equal to `ANNULLERT`.
  - Violated prerequisite or constraint: original period must be annullable.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 78: Annul And Generate Arena-treated Periods Before Date

Business goal:
Replaces earlier periods as Arena-treated.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `annul and generate Arena-treated periods before date` (`POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}`) with the endpoint-specific selected ids, path date, or body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Replaces earlier periods as Arena-treated.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `annul and generate Arena-treated periods before date`
  - Failure condition: agreement id does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException`.
  - Violated prerequisite or constraint: agreement must exist.
- Failing function: `annul and generate Arena-treated periods before date`
  - Failure condition: parent agreement measure is unsupported.
  - Why it fails: replacement creation calls `krevEnAvTiltakstyper`.
  - Violated prerequisite or constraint: parent agreement must be subsidy-backed wage measure.
- Failing function: `annul and generate Arena-treated periods before date`
  - Failure condition: selected period cannot become `ANNULLERT`.
  - Why it fails: replacement creation rejects status not equal to `ANNULLERT`.
  - Violated prerequisite or constraint: selected periods must be annullable.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 79: Patch Selected Agreements To Data Warehouse

Business goal:
Creates DVH patch messages for selected agreements.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `patch selected agreements to data warehouse` (`POST /utvikler-admin/dvh-melding/patch`) with the endpoint-specific selected ids, path date, or body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates DVH patch messages for selected agreements.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 80: Patch All Agreements To Data Warehouse

Business goal:
Creates DVH patch messages for all agreements.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `patch all agreements to data warehouse` (`POST /utvikler-admin/dvh-melding/patchalleavtaler`) with endpoint-specific request values.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Creates DVH patch messages for all agreements.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 81: Send Agreement Event Message For One Agreement

Business goal:
Sends one agreement event message.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
Existing service state; direct database setup is a normal setup path for this operational function.

Required execution workflow:
1. Use function `send agreement event message for one agreement` (`POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}`) with the endpoint-specific selected ids, path date, or body values.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` when the behavior mutates or depends on an agreement, or use the closest list/log function named in the workflow to inspect the result.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Sends one agreement event message.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
- Failing function: `send agreement event message for one agreement`
  - Failure condition: agreement id does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException`.
  - Violated prerequisite or constraint: agreement must exist.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 82: Send Agreement Event Messages For All Agreements

Business goal:
Sends event messages for all agreements.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `send agreement event messages for all agreements` (`POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler`) with endpoint-specific request values.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Sends event messages for all agreements.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 83: Dry-run Event Messages For All Agreements

Business goal:
Previews/logs all-agreement event publication.

Domain context:
This is a distinct domain-facing capability in the `Notifications, Journal, Admin Repair, And Messaging` area and is kept separate because it has its own endpoint/action, result, state transition, read model, operational effect, or failure surface.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `dry-run event messages for all agreements` (`POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler`) with endpoint-specific request values.

Optional verification workflow:
None.

Existing-state shortcuts:
- If equivalent state already exists, skip setup creation/update/approval steps but keep the same generated ids, ownership scope, current `sistEndret`, and role bindings required by the core function.
- Direct database setup can replace setup functions when it creates the same aggregate, child rows, flags, approvals, and ownership relationships explicitly.
- The core behavior action itself is not skipped for this behavior.

Parameter and value bindings:
- Generated ids from setup, especially `avtaleId`, `tilskuddsperiodeId`, `varselId`, `sokId`, and `avtaleVersjonId`, must be reused exactly by later path, query, or body values.
- `innlogget-part` selects the domain party implementation and must match the ownership/role relationship needed by the function.
- When `sistEndret` is used, it must be captured from the latest agreement read or mutation response and sent as `If-Unmodified-Since`.

Business result:
Previews/logs all-agreement event publication.

Constraints and invariants:
- Object-level access is enforced where the implementation calls `Avtalepart.sjekkTilgang`; generic authentication, issuer/role-cookie mismatch, configured system-user, developer-admin, DVH, and delete-marker admission checks are excluded from business failures.
- The implementation source is treated as authoritative when OpenAPI and code differ.

Failure and exceptional cases:
None.

Implementation notes:
No API calls were executed for this analysis. Framework parsing errors, malformed UUID/date/enum values, generic auth/access gates, and external dependency availability failures are excluded from business failure cases.

### Behavior 84: Composite: Create, Fill, Approve, And Enter A Non-Decision Agreement

Business goal:
A non-decision agreement is fully approved and entered.

Domain context:
This composite is included because the later step consumes state created by earlier functions and together they complete a business workflow. The atomic functions remain separate supported behaviors above.

Starting point:
No prior service state when using the full workflow, except for the rejection-correction composite which may start from an existing advisor-approved subsidy agreement with an unhandled period.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with a non-decision `tiltakstype` and capture `avtaleId`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete valid content and current `sistEndret`.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
4. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `sistEndret`.
5. When `tiltakstype=MENTOR`, use function `sign mentor confidentiality declaration` (`POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`) with fresh `sistEndret`.
6. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh timestamp to enter the agreement.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` to inspect final agreement and period state.

Existing-state shortcuts:
- Skip setup functions when equivalent agreement, approval, period, and ownership state already exists.
- Direct database setup must preserve generated ids, current content, approval timestamps, active period flags, and role scoping.
- The final composite action cannot be skipped.

Parameter and value bindings:
- The same `avtaleId` flows through all steps.
- Fresh `sistEndret` values must be reused for approval functions that require optimistic concurrency.
- Period decision functions consume the current period selected by domain logic rather than a supplied period id.

Business result:
A non-decision agreement is fully approved and entered.

Constraints and invariants:
- Each atomic function retains its own constraints and failure surface.
- The composite does not merge independent capabilities for coverage purposes.

Failure and exceptional cases:
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing `sumLonnstilskudd`, `lonnstilskuddProsent`, or periods.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve agreement as employer`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as employer`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as employer`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as employer`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as employer`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as employer`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as employer`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as employer`
  - Failure condition: employer approval already exists.
  - Why it fails: `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Violated prerequisite or constraint: employer can approve once per approval cycle.
- Failing function: `sign mentor confidentiality declaration`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `sign mentor confidentiality declaration`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `sign mentor confidentiality declaration`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `sign mentor confidentiality declaration`
  - Failure condition: mentor signing already exists.
  - Why it fails: `godkjennForMentor` rejects duplicate signing.
  - Violated prerequisite or constraint: mentor can sign once.
- Failing function: `approve agreement as advisor`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as advisor`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as advisor`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as advisor`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as advisor`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as advisor`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as advisor`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as advisor`
  - Failure condition: participant or employer approval is missing.
  - Why it fails: `VeilederSkalGodkjenneSistException` enforces advisor-last order.
  - Violated prerequisite or constraint: external parties must approve first.

Implementation notes:
Composite workflows are documented for business completeness, but the coverage denominator should still count the atomic behaviors separately.

### Behavior 85: Composite: Create And Enter A Subsidy-Backed Agreement Through Period Approval

Business goal:
A subsidy-backed agreement is entered and its current subsidy period is approved.

Domain context:
This composite is included because the later step consumes state created by earlier functions and together they complete a business workflow. The atomic functions remain separate supported behaviors above.

Starting point:
No prior service state when using the full workflow, except for the rejection-correction composite which may start from an existing advisor-approved subsidy agreement with an unhandled period.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with `tiltakstype={SOMMERJOBB|MIDLERTIDIG_LONNSTILSKUDD|VARIG_LONNSTILSKUDD}`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete subsidy content so periods are generated.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) then employer, then advisor using fresh timestamps.
4. Use function `approve subsidy period` (`POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`) with `enhet={fourDigitUnit}` to enter the agreement and approve the current period.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` to inspect final agreement and period state.

Existing-state shortcuts:
- Skip setup functions when equivalent agreement, approval, period, and ownership state already exists.
- Direct database setup must preserve generated ids, current content, approval timestamps, active period flags, and role scoping.
- The final composite action cannot be skipped.

Parameter and value bindings:
- The same `avtaleId` flows through all steps.
- Fresh `sistEndret` values must be reused for approval functions that require optimistic concurrency.
- Period decision functions consume the current period selected by domain logic rather than a supplied period id.

Business result:
A subsidy-backed agreement is entered and its current subsidy period is approved.

Constraints and invariants:
- Each atomic function retains its own constraints and failure surface.
- The composite does not merge independent capabilities for coverage purposes.

Failure and exceptional cases:
- Failing function: `approve subsidy period`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve subsidy period`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve subsidy period`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve subsidy period`
  - Failure condition: advisor approval is missing.
  - Why it fails: period treatment rejects agreements not approved by advisor.
  - Violated prerequisite or constraint: advisor approval must exist.
- Failing function: `approve subsidy period`
  - Failure condition: `enhet` is null, not four digits, or unknown in Norg2.
  - Why it fails: the method rejects invalid format and `Beslutter` rejects missing unit.
  - Violated prerequisite or constraint: decision unit must be valid.
- Failing function: `approve subsidy period`
  - Failure condition: decision-maker is the same NAV ident that advisor-approved the agreement.
  - Why it fails: `TILSKUDDSPERIODE_IKKE_GODKJENNE_EGNE` is thrown.
  - Violated prerequisite or constraint: decision-maker must be distinct from advisor approver.
- Failing function: `approve subsidy period`
  - Failure condition: current period is not `UBEHANDLET`.
  - Why it fails: `TilskuddPeriode.sjekkOmKanBehandles` rejects already treated periods.
  - Violated prerequisite or constraint: current period must be unhandled.
- Failing function: `approve subsidy period`
  - Failure condition: current period is too early to decide.
  - Why it fails: period treatment is blocked until `kanBesluttesFom`.
  - Violated prerequisite or constraint: current date must be inside treatment window.
- Failing function: `create advisor agreement`
  - Failure condition: advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` checks candidate write access before save.
  - Violated prerequisite or constraint: advisor must have write access to the participant.
- Failing function: `create advisor agreement`
  - Failure condition: participant is under 16.
  - Why it fails: the `Avtale` constructor rejects under-age participants.
  - Violated prerequisite or constraint: participant must satisfy the minimum-age rule.
- Failing function: `create advisor agreement`
  - Failure condition: `tiltakstype=SOMMERJOBB` and participant is over the Sommerjobb creation age limit.
  - Why it fails: the constructor rejects participants over the Sommerjobb age limit.
  - Violated prerequisite or constraint: Sommerjobb participant must be age-eligible.
- Failing function: `create advisor agreement`
  - Failure condition: PDL marks the participant as Code 6 protected.
  - Why it fails: the advisor path fetches person data and blocks protected participants.
  - Violated prerequisite or constraint: protected participants cannot be processed through advisor creation.
- Failing function: `update agreement`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `update agreement`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `update agreement`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `update agreement`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `update agreement`
  - Failure condition: any participant, employer, or advisor approval already exists.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be revoked before editing.
  - Violated prerequisite or constraint: draft content can only be changed before approvals or after revocation.
- Failing function: `update agreement`
  - Failure condition: `startDato` is after `sluttDato`.
  - Why it fails: the shared start/end-date strategy throws `START_ETTER_SLUTT`.
  - Violated prerequisite or constraint: start date must not be after end date.
- Failing function: `update agreement`
  - Failure condition: not-entered agreement starts more than seven days in the past without after-registration eligibility.
  - Why it fails: the shared date strategy throws `FORTIDLIG_STARTDATO`.
  - Violated prerequisite or constraint: past start dates require after-registration eligibility.
- Failing function: `update agreement`
  - Failure condition: `sluttDato` is after 2089-12-31.
  - Why it fails: the shared date strategy throws `SLUTTDATO_GRENSE_NÅDD`.
  - Violated prerequisite or constraint: end date must stay inside the configured upper bound.
- Failing function: `update agreement`
  - Failure condition: measure-specific date limits are violated.
  - Why it fails: measure strategies reject too-long work training, inclusion subsidy, mentor, temporary wage subsidy, or Sommerjobb periods and invalid Sommerjobb season dates.
  - Violated prerequisite or constraint: measure-specific duration/season rules must hold.
- Failing function: `update agreement`
  - Failure condition: wage-subsidy percentage or OTP rate is outside measure rules.
  - Why it fails: wage-subsidy strategies reject invalid temporary, permanent, Sommerjobb, or OTP values.
  - Violated prerequisite or constraint: calculation inputs must fit the measure.
- Failing function: `update agreement`
  - Failure condition: inclusion-subsidy expense total exceeds the cap.
  - Why it fails: `InkluderingstilskuddStrategy` rejects totals above the configured maximum.
  - Violated prerequisite or constraint: inclusion subsidy total must stay within the cap.
- Failing function: `approve agreement as participant`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve agreement as participant`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve agreement as participant`
  - Failure condition: `If-Unmodified-Since` is missing or older than current `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws `SamtidigeEndringerException`.
  - Violated prerequisite or constraint: mutating calls with optimistic locking must reuse the latest timestamp.
- Failing function: `approve agreement as participant`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve agreement as participant`
  - Failure condition: required content fields are missing.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete current content.
  - Violated prerequisite or constraint: all measure-specific required fields must be filled.
- Failing function: `approve agreement as participant`
  - Failure condition: subsidy calculation or generated periods are missing for wage-subsidy/Sommerjobb measures.
  - Why it fails: approval rejects missing subsidy calculation or generated period state.
  - Violated prerequisite or constraint: subsidy-backed agreements must be calculated.
- Failing function: `approve agreement as participant`
  - Failure condition: no advisor is assigned.
  - Why it fails: approval rejects `veilederNavIdent=null`.
  - Violated prerequisite or constraint: agreement must be distributed to an advisor.
- Failing function: `approve agreement as participant`
  - Failure condition: participant approval already exists.
  - Why it fails: `godkjennForDeltaker` rejects duplicate participant approval.
  - Violated prerequisite or constraint: participant can approve once per approval cycle.
- Failing function: `approve subsidy period`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve subsidy period`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve subsidy period`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve subsidy period`
  - Failure condition: advisor approval is missing.
  - Why it fails: period treatment rejects agreements not approved by advisor.
  - Violated prerequisite or constraint: advisor approval must exist.
- Failing function: `approve subsidy period`
  - Failure condition: `enhet` is invalid or unknown.
  - Why it fails: the method rejects invalid format and missing Norg2 unit.
  - Violated prerequisite or constraint: decision unit must be valid.

Implementation notes:
Composite workflows are documented for business completeness, but the coverage denominator should still count the atomic behaviors separately.

### Behavior 86: Composite: Reject, Correct, And Return A Subsidy Period For New Decision

Business goal:
Rejected active periods are deactivated and replacement unhandled periods can be decided again.

Domain context:
This composite is included because the later step consumes state created by earlier functions and together they complete a business workflow. The atomic functions remain separate supported behaviors above.

Starting point:
No prior service state when using the full workflow, except for the rejection-correction composite which may start from an existing advisor-approved subsidy agreement with an unhandled period.

Required execution workflow:
1. Use function `reject subsidy period` (`POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`) with nonempty `avslagsårsaker` and nonblank `avslagsforklaring`.
2. Use function `send rejected subsidy period back` (`POST /avtaler/{avtaleId}/send-tilbake-til-beslutter`) with the same `avtaleId` to create replacement unhandled periods.
3. Use function `approve subsidy period` (`POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`) later with `enhet={fourDigitUnit}` if the corrected replacement should be approved.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the same `avtaleId` to inspect final agreement and period state.

Existing-state shortcuts:
- Skip setup functions when equivalent agreement, approval, period, and ownership state already exists.
- Direct database setup must preserve generated ids, current content, approval timestamps, active period flags, and role scoping.
- The final composite action cannot be skipped.

Parameter and value bindings:
- The same `avtaleId` flows through all steps.
- Fresh `sistEndret` values must be reused for approval functions that require optimistic concurrency.
- Period decision functions consume the current period selected by domain logic rather than a supplied period id.

Business result:
Rejected active periods are deactivated and replacement unhandled periods can be decided again.

Constraints and invariants:
- Each atomic function retains its own constraints and failure surface.
- The composite does not merge independent capabilities for coverage purposes.

Failure and exceptional cases:
- Failing function: `reject subsidy period`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `reject subsidy period`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `reject subsidy period`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `reject subsidy period`
  - Failure condition: advisor approval is missing.
  - Why it fails: period treatment rejects agreements not approved by advisor.
  - Violated prerequisite or constraint: advisor approval must exist.
- Failing function: `reject subsidy period`
  - Failure condition: current period is not `UBEHANDLET`.
  - Why it fails: period treatment rejects already treated periods.
  - Violated prerequisite or constraint: current period must be unhandled.
- Failing function: `reject subsidy period`
  - Failure condition: current period is too early to decide.
  - Why it fails: period treatment is blocked until `kanBesluttesFom`.
  - Violated prerequisite or constraint: current date must be inside treatment window.
- Failing function: `reject subsidy period`
  - Failure condition: `avslagsforklaring` is blank.
  - Why it fails: `TilskuddPeriode.avslå` requires explanation.
  - Violated prerequisite or constraint: rejection must include explanation.
- Failing function: `reject subsidy period`
  - Failure condition: `avslagsårsaker` is empty.
  - Why it fails: `TilskuddPeriode.avslå` requires at least one cause.
  - Violated prerequisite or constraint: rejection must include causes.
- Failing function: `send rejected subsidy period back`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `send rejected subsidy period back`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks participant, employer company/measure, mentor identity, advisor candidate access, decision-maker access, and `slettemerket`.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `send rejected subsidy period back`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `reject subsidy period`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `reject subsidy period`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `reject subsidy period`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `reject subsidy period`
  - Failure condition: advisor approval is missing.
  - Why it fails: period treatment rejects agreements not approved by advisor.
  - Violated prerequisite or constraint: advisor approval must exist.
- Failing function: `reject subsidy period`
  - Failure condition: `avslagsforklaring` is blank or `avslagsårsaker` is empty.
  - Why it fails: `TilskuddPeriode.avslå` requires explanation and at least one cause.
  - Violated prerequisite or constraint: rejection must include cause and explanation.
- Failing function: `approve subsidy period`
  - Failure condition: referenced agreement does not exist.
  - Why it fails: repository lookup throws `RessursFinnesIkkeException` or equivalent.
  - Violated prerequisite or constraint: the domain resource id/number must exist.
- Failing function: `approve subsidy period`
  - Failure condition: caller does not have domain access to the specific agreement or it is soft-deleted.
  - Why it fails: `Avtalepart.sjekkTilgang` checks object-level party access.
  - Violated prerequisite or constraint: caller must have object-level business access.
- Failing function: `approve subsidy period`
  - Failure condition: agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` rejects inactive agreements.
  - Violated prerequisite or constraint: agreement must be active.
- Failing function: `approve subsidy period`
  - Failure condition: advisor approval is missing.
  - Why it fails: period treatment rejects agreements not approved by advisor.
  - Violated prerequisite or constraint: advisor approval must exist.
- Failing function: `approve subsidy period`
  - Failure condition: `enhet` is invalid or unknown.
  - Why it fails: the method rejects invalid format and missing Norg2 unit.
  - Violated prerequisite or constraint: decision unit must be valid.

Implementation notes:
Composite workflows are documented for business completeness, but the coverage denominator should still count the atomic behaviors separately.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Idempotently Set Or Clear After-Registration Eligibility

Priority:
Important robustness gap

Expected business goal:
Explicitly set after-registration eligibility to true or false without depending on current toggle state.

Why it is unsupported:
The only available functions, `mark agreement eligible for after-registration` and `remove after-registration eligibility`, are the same toggle endpoint. A caller cannot send the desired final boolean state.

Existing functions considered:
- `mark agreement eligible for after-registration`: toggles the current flag on only if it was previously false.
- `remove after-registration eligibility`: toggles the current flag off only if it was previously true.
- `retrieve agreement by id`: can inspect current state first but cannot make the mutation idempotent under concurrent calls.

Missing capability:
An explicit set endpoint or request body such as `godkjentForEtterregistrering=true|false`, with concurrency protection.

Proof that function composition is insufficient:
Read-then-toggle can race with another toggle and produce the opposite final state. Direct database setup bypasses the domain event distinction between approval and removal.

Evidence from existing functions/source:
- `Avtale.togglegodkjennEtterregistrering` assigns `!this.godkjentForEtterregistrering` and emits either `GodkjentForEtterregistrering` or `FjernetEtterregistrering`.

Business impact:
Clients cannot safely express intended final eligibility in retry or concurrent workflows.

### Missing Behavior 2: Restore Or Audit Soft-Deleted Agreements

Priority:
Important robustness gap

Expected business goal:
Find, inspect, restore, or audit agreements marked `slettemerket=true`.

Why it is unsupported:
`soft-delete agreement` only sets the marker. Normal access checks then hide the agreement, and no restore/list-deleted endpoint exists.

Existing functions considered:
- `soft-delete agreement`: hides the agreement.
- `list accessible agreements`: filters soft-deleted agreements through access checks.
- `retrieve agreement by id`: denies access after soft delete.

Missing capability:
A restore endpoint, deleted-agreement listing, or admin audit read model.

Proof that function composition is insufficient:
Once `slettemerket=true`, ordinary functions cannot retrieve the agreement to reverse or inspect it. Delete-and-recreate is not equivalent because it loses ids, agreement number, versions, periods, events, journal links, and external references.

Evidence from existing functions/source:
- `Avtalepart.harTilgang` returns false when `avtale.isSlettemerket()`.
- `Avtale.slettemerk` only sets the marker and registers an event.

Business impact:
Accidental soft deletion is difficult to repair through the API and weakens auditability.

### Missing Behavior 3: Direct Subsidy-Period Lookup By Period Id

Priority:
API ergonomics gap

Expected business goal:
Retrieve one subsidy period by `tilskuddsperiodeId` before operational repair or investigation.

Why it is unsupported:
Period ids appear in agreement detail and admin paths, but there is no ordinary read endpoint for a period id.

Existing functions considered:
- `retrieve agreement by id`: returns periods only when the caller knows and can access the parent agreement.
- `annul subsidy period`, `annul and resend subsidy period as approved`, `annul and generate unhandled subsidy period`: consume a period id but do not offer a read-only lookup.

Missing capability:
A `GET /tilskuddsperioder/{tilskuddsperiodeId}` or admin equivalent with parent agreement context.

Proof that function composition is insufficient:
A period id alone cannot be resolved to its parent agreement through existing read APIs. Brute-force listing agreements is incomplete and role-filtered.

Evidence from existing functions/source:
- Admin period repair endpoints call `tilskuddPeriodeRepository.findById(id)` internally, but no controller exposes this lookup.

Business impact:
Operators can mutate a known period id more easily than they can inspect it safely first.

### Missing Behavior 4: Consistent Optimistic Concurrency For Post-Approval Changes

Priority:
Important robustness gap

Expected business goal:
Protect shortening, extension, content-change, and period repair operations against stale clients.

Why it is unsupported:
Only ordinary draft update and approval paths consistently call `sjekkSistEndret`. Several post-approval endpoints accept no timestamp, or accept a header but ignore it.

Existing functions considered:
- `update agreement`: enforces `If-Unmodified-Since`.
- `shorten agreement`, `dry-run agreement shortening`, `extend agreement`, `dry-run agreement extension`: controller signatures include a timestamp, but the domain calls do not check it.
- `change contact information`, `change job description`, `change follow-up and adaptation text`, `change subsidy calculation`, and similar endpoints: no timestamp is used.

Missing capability:
A consistent concurrency precondition for all mutating agreement-version endpoints.

Proof that function composition is insufficient:
Reading `sistEndret` before calling an endpoint cannot force the endpoint to reject stale state unless the endpoint checks it.

Evidence from existing functions/source:
- `Avtale.endreAvtale` calls `sjekkSistEndret`; `forkortAvtale` and `forlengAvtale` do not.

Business impact:
Late writes can overwrite or reorder important agreement changes without a domain conflict response.

### Missing Behavior 5: Structured Per-Item Results For Bulk Operational Jobs

Priority:
API ergonomics gap

Expected business goal:
Know which selected agreements or periods were repaired, skipped, ignored, or failed.

Why it is unsupported:
Bulk jobs mostly return `void`, log details, ignore unknown ids in some places, and abort on the first exception in others.

Existing functions considered:
- `patch selected agreements to data warehouse`: unknown ids are ignored.
- `mark agreement versions as journaled`: unknown version ids are ignored.
- `recalculate wage subsidy for selected agreements`: aborts on missing ids or domain failures.
- `patch all agreements to data warehouse`, `send agreement event messages for all agreements`: run asynchronously/log counts.

Missing capability:
Structured response rows per requested id with outcome, reason, and generated message ids where relevant.

Proof that function composition is insufficient:
Follow-up reads cannot reliably distinguish ignored id, skipped by filter, async still running, serialization error, or successful message creation without querying internal tables that have no API.

Evidence from existing functions/source:
- `findAllById` is used for selected patching/marking without comparing requested ids to found ids.
- Bulk methods log rather than return operational outcomes.

Business impact:
Operational repair and reconciliation workflows are harder to audit and retry safely.

### Missing Behavior 6: Safe Advisor Access Enforcement For Sharing And Single Event Publication

Priority:
Critical domain gap

Expected business goal:
Ensure every operation against a concrete agreement enforces domain access or an explicit operational access model.

Why it is unsupported:
Most advisor operations call `sjekkTilgang`, but `share agreement with a party` loads by id and calls `veileder.delAvtaleMedAvtalepart` without checking advisor access. `send agreement event message for one agreement` has no developer group check while the all-agreements event endpoints do.

Existing functions considered:
- `share agreement with a party`: validates active agreement and phone number but not advisor access.
- `send agreement event message for one agreement`: loads any agreement by id and sends a message without the group check used by bulk send.
- `retrieve agreement by id`: has the expected agreement-specific access check.

Missing capability:
Consistent `sjekkTilgang` in share, and consistent operational group admission for single-event publication.

Proof that function composition is insufficient:
Calling a checked read before an unchecked mutation does not make the mutation enforce the same access; another caller can invoke the unchecked endpoint directly.

Evidence from existing functions/source:
- `Veileder.delAvtaleMedAvtalepart` delegates directly to `avtale.delMedAvtalepart`.
- `AvtaleHendelseController.sendMeldingForEnAvtale` omits `sjekkTilgang()` while bulk endpoints call it.

Business impact:
A caller with endpoint admission may trigger sharing or event publication for agreements outside the intended domain scope.

## Cross-Behavior Observations

- The service’s business state is persisted mostly as timestamps, flags, current content pointers, and period rows; callers must preserve id and timestamp bindings carefully.
- OpenAPI exposes 79 HTTP operations, while `full-behavior.md` expands them into 83 functions because role-specific and mode-specific meanings share endpoints.
- Draft update and approval enforce optimistic concurrency, but many post-approval changes and operational repairs do not.
- Dry-run endpoints usually reuse the persistent domain method, but Arena migration dry-run lacks the persistent endpoint’s entered-agreement guard.
- Soft delete is one-way through the API and affects all normal access checks.
- Bulk operational endpoints often log or ignore per-item conditions instead of returning structured outcomes.
- Generic issuer/role-cookie mismatch, configured system-user checks, developer-admin/DVH/delete-marker group checks, malformed UUID/date/enum values, and external dependency availability failures are intentionally excluded from business failure lists.

## Coverage Summary

Supported domain areas: agreement creation, role-scoped listing/search, agreement retrieval/versioning, draft update/dry-run, sharing, party/advisor/on-behalf approval, mentor signing, after-registration toggling, subsidy-period decisioning, approved-agreement changes, Arena migration adjustment, account/PDF/Salesforce reads, advisor takeover/refresh, annulment/soft delete, employer and decision-maker lists, organization/code/feature reads, notifications, journaling, operational subsidy repairs, DVH patching, and agreement-event message publication.

Partially supported domain areas: after-registration state management, post-approval concurrency, bulk repair observability, soft-delete lifecycle, period-level inspection, and consistent access enforcement on all concrete-agreement operations.

Unsupported domain areas: idempotent after-registration set/clear, restore/list/audit of soft-deleted agreements, direct period-id lookup, structured per-item bulk job results, and API-enforced safe access for the identified unchecked sharing/single-publication paths.
