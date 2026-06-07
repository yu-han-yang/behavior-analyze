# Domain-Level Behavior Analysis

## Domain Summary

The service manages NAV labor-market measure agreements (`Avtale`) between a participant, an employer, and NAV. The main domain resources are agreements, agreement content versions, subsidy periods, notifications, journal-ready agreement versions, developer-admin repair actions, event/data-warehouse messages, feature flags, code lists, organizations, and logged-in party context.

The core agreement lifecycle is: create an agreement, complete its required content, share it with the relevant parties, collect participant/employer/mentor/NAV approvals in the required order, optionally let a decision-maker approve or reject subsidy periods, then maintain the agreement through post-approval amendments, shortening, extension, annulment, soft deletion, and administrative repair workflows.

Implementation behavior is more precise than the OpenAPI contract. The OpenAPI file exposes 79 operations, while `full-behavior.md` documents 83 functions because several endpoints have distinct role-specific meanings. Implementation-only endpoints under `/utvikler-admin/tilskuddsperioder` exist in source but are not part of `full-behavior.md` and are therefore not treated as supported functions here.

## Available Function Inventory

### Agreement Creation and Discovery

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list accessible agreements` | `GET /avtaler` | Role-scoped, paginated agreement overview. |
| `create advisor agreement` | `POST /avtaler` | Advisor creates a normal agreement draft. |
| `create Arena cleanup agreement` | `POST /avtaler` | Advisor creates an agreement and marks it as an Arena cleanup agreement. |
| `create employer agreement` | `POST /avtaler/opprett-som-arbeidsgiver` | Employer creates an agreement draft for an Altinn-authorized company and measure. |
| `create mentor agreement as advisor` | `POST /avtaler/opprett-mentor-avtale` | Advisor creates a mentor agreement with a distinct mentor identity. |
| `create mentor agreement as employer` | `POST /avtaler/opprett-mentor-avtale` | Employer creates a mentor agreement with a distinct mentor identity. |
| `check participant overlap` | `GET /avtaler/deltaker-allerede-paa-tiltak` | Advisor checks whether a participant already has overlapping agreements. |
| `search agreements and save search` | `POST /avtaler/sok` | Runs a filtered agreement search and persists a reusable search id. |
| `replay saved agreement search` | `GET /avtaler/sok` | Replays a previously saved search by `sokId`. |
| `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | Retrieves an accessible agreement by generated UUID. |
| `retrieve agreement by agreement number` | `GET /avtaler/avtaleNr/{avtaleNr}` | Retrieves an accessible agreement by generated agreement number. |
| `list agreement versions` | `GET /avtaler/{avtaleId}/versjoner` | Lists persisted content versions for an agreement. |
| `list employer agreements` | `GET /avtaler/min-side-arbeidsgiver` | Employer-specific list for one company number. |
| `list decision-maker agreements` | `GET /avtaler/beslutter-liste` | Decision-maker overview of agreements with subsidy periods to handle. |

### Agreement Editing, Sharing, and Approval

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `update agreement` | `PUT /avtaler/{avtaleId}` | Edits an agreement draft and may regenerate subsidy periods. |
| `dry-run agreement update` | `PUT /avtaler/{avtaleId}/dry-run` | Computes the same update in memory without saving. |
| `share agreement with party` | `POST /avtaler/{avtaleId}/del-med-avtalepart` | Emits a share event for participant, employer, advisor, or mentor. |
| `approve agreement as participant` | `POST /avtaler/{avtaleId}/godkjenn` | Participant approval for a complete agreement. |
| `approve agreement as employer` | `POST /avtaler/{avtaleId}/godkjenn` | Employer approval for a complete agreement. |
| `sign mentor confidentiality declaration` | `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`; `POST /avtaler/{avtaleId}/godkjenn` | Mentor signs confidentiality before NAV can approve a mentor agreement. |
| `approve agreement as advisor` | `POST /avtaler/{avtaleId}/godkjenn` | Advisor performs the final agreement approval. |
| `approve on behalf of participant` | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av`; `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker` | Advisor approves for NAV and participant with a reason. |
| `approve on behalf of employer` | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver` | Advisor approves for NAV and employer with a reason. |
| `approve on behalf of participant and employer` | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` | Advisor approves for NAV, participant, and employer with reasons. |
| `revoke approvals` | `POST /avtaler/{avtaleId}/opphev-godkjenninger` | Clears existing participant, employer, and advisor approval flags before agreement entry. |
| `mark agreement eligible for after-registration` | `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` | Decision-maker toggles after-registration eligibility on. |
| `remove after-registration eligibility` | `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` | Decision-maker toggles after-registration eligibility off. |

### Subsidy Period Decision and Agreement Maintenance

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `approve subsidy period` | `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode` | Decision-maker approves the current subsidy period. |
| `reject subsidy period` | `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` | Decision-maker rejects the current subsidy period with reasons. |
| `send rejected subsidy period back` | `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter` | Advisor replaces active rejected subsidy periods with new unhandled periods. |
| `shorten agreement` | `POST /avtaler/{avtaleId}/forkort` | Shortens an approved agreement and adjusts subsidy periods. |
| `dry-run agreement shortening` | `POST /avtaler/{avtaleId}/forkort-dry-run` | Computes shortening without saving. |
| `extend agreement` | `POST /avtaler/{avtaleId}/forleng` | Extends an approved agreement and adjusts subsidy periods. |
| `dry-run agreement extension` | `POST /avtaler/{avtaleId}/forleng-dry-run` | Computes extension without saving. |
| `change subsidy calculation` | `POST /avtaler/{avtaleId}/endre-tilskuddsberegning` | Creates a new approved version with updated wage-subsidy economics. |
| `dry-run subsidy calculation change` | `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run` | Computes the subsidy calculation change without saving. |
| `change contact information` | `POST /avtaler/{avtaleId}/endre-kontaktinfo` | Creates a new approved version with changed party contact fields. |
| `change job description` | `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse` | Creates a new approved version with changed job details. |
| `change follow-up and adaptation text` | `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging` | Creates a new approved version with changed follow-up/adaptation text. |
| `change work-training goals` | `POST /avtaler/{avtaleId}/endre-maal` | Creates new work-training goals for an approved work-training agreement. |
| `change inclusion subsidy expenses` | `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd` | Replaces inclusion-subsidy expense lines on an approved inclusion-subsidy agreement. |
| `change mentor details` | `POST /avtaler/{avtaleId}/endre-om-mentor` | Creates a new approved version with changed mentor details. |
| `change cost center` | `POST /avtaler/{avtaleId}/endre-kostnadssted` | Updates cost-center unit on unhandled/rejected subsidy periods. |
| `adjust Arena migration date` | `POST /avtaler/{avtaleId}/juster-arena-migreringsdato` | Rebuilds Arena-migration subsidy periods and stores cleanup migration date. |
| `dry-run Arena migration date adjustment` | `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run` | Computes Arena-migration period changes without saving. |
| `refresh follow-up unit` | `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet` | Refreshes participant, geographic, and follow-up unit data from external services. |
| `take over agreement as advisor` | `PUT /avtaler/{avtaleId}/overta` | Assigns the logged-in advisor as agreement advisor. |
| `annul agreement` | `POST /avtaler/{avtaleId}/annuller` | Annuls the agreement and removes/annuls subsidy periods. |
| `soft-delete agreement` | `POST /avtaler/{avtaleId}/slettemerk` | Marks an agreement hidden/deleted for ordinary access. |

### Reference, Output, Notifications, and Internal Processing

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `get employer account number` | `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver` | Gets the employer account number for an accessible agreement. |
| `download agreement PDF` | `GET /avtaler/{avtaleId}/pdf` | Produces agreement PDF after NAV approval. |
| `check Salesforce dialog visibility` | `GET /avtaler/{avtaleId}/vis-salesforce-dialog` | Evaluates whether a Salesforce dialog should appear for the agreement. |
| `get logged-in user` | `GET /innlogget-bruker` | Returns the user context for the selected logged-in party role. |
| `look up organization` | `GET /organisasjoner` | Looks up employer organization information by company number. |
| `get Altinn rights request URLs` | `GET /be-om-altinn-rettighet-urler` | Builds Altinn rights request URLs per measure type. |
| `get all code lists` | `GET /kodeverk` | Returns status and measure-type code lists. |
| `get status code list` | `GET /kodeverk/statuser` | Returns agreement statuses. |
| `get measure type code list` | `GET /kodeverk/tiltakstyper` | Returns measure types. |
| `evaluate feature toggles` | `GET /feature` | Evaluates named feature flags. |
| `get feature variants` | `GET /feature/variant` | Gets named feature variants. |
| `run health check` | `GET /internal/healthcheck` | Checks database-backed service health. |
| `list overview notifications` | `GET /varsler/oversikt` | Lists unread bell notifications for the logged-in party. |
| `list agreement modal notifications` | `GET /varsler/avtale-modal` | Lists unread bell notifications for one agreement. |
| `list agreement notification log` | `GET /varsler/avtale-logg` | Lists the agreement notification log for one role. |
| `mark notification as read` | `POST /varsler/{varselId}/sett-til-lest` | Marks one owned notification as read. |
| `mark multiple notifications as read` | `POST /varsler/sett-alle-til-lest` | Marks several owned notifications as read. |
| `list unjournaled agreements` | `GET /internal/avtaler` | System user lists agreement versions ready for journaling. |
| `mark agreement versions as journaled` | `PUT /internal/avtaler` | System user records journalpost ids on agreement versions. |

### Developer/Admin Repair and Integration Replay

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `recalculate wage subsidy` | `POST /utvikler-admin/reberegn` | Recomputes missing wage-subsidy totals for selected agreements. |
| `fix missing reduced-percent date` | `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}` | Batch-fixes varig wage-subsidy agreements missing reduced-percent dates. |
| `dry-run missing reduced-percent date fix` | `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}` | Logs how many agreements would be fixed. |
| `generate subsidy periods for agreement` | `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}` | Rebuilds subsidy periods for one agreement using an Arena migration date. |
| `recalculate unhandled subsidy periods` | `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}` | Removes and recalculates unhandled periods after the last approved period. |
| `find subsidy period date-order problems` | `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer` | Logs date-order anomalies for approved temporary wage-subsidy agreements. |
| `annul subsidy period` | `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}` | Marks one subsidy period annulled and emits an annulment event when applicable. |
| `annul and resend approved subsidy period` | `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}` | Annuls one period and creates a new approved replacement. |
| `annul and generate unhandled subsidy period` | `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}` | Annuls one period and creates a new unhandled replacement. |
| `annul and generate Arena-treated periods` | `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}` | Replaces periods before a date with Arena-treated periods. |
| `patch selected data warehouse messages` | `POST /utvikler-admin/dvh-melding/patch` | Creates data-warehouse patch messages for selected agreements. |
| `patch all data warehouse messages` | `POST /utvikler-admin/dvh-melding/patchalleavtaler` | Creates data-warehouse patch messages for all agreements. |
| `send event message for one agreement` | `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}` | Replays one agreement event message. |
| `send event messages for all agreements` | `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler` | Replays agreement event messages for all agreements. |
| `dry-run event messages for all agreements` | `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler` | Dry-runs full agreement event replay. |

## Supported Business Behaviors

### Behavior 1: Create an Advisor-Owned Agreement Draft

Business goal:
Create a new agreement draft owned by a NAV advisor, optionally as an Arena cleanup agreement.

Domain context:
This is the normal entry point when NAV initiates an agreement for a participant and employer. The agreement receives a generated `avtaleId`, a database-generated `avtaleNr`, participant and employer identities, empty measure-specific content, participant name from PDL, employer name from EREG, and unit data from Arena/Norg where available.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with body `deltakerFnr={valid participant FNR accessible to the advisor}`, `bedriftNr={valid employer company number}`, `tiltakstype={ARBEIDSTRENING|INKLUDERINGSTILSKUDD|MENTOR|MIDLERTIDIG_LONNSTILSKUDD|VARIG_LONNSTILSKUDD|SOMMERJOBB}` and query `ryddeavtale` omitted or `false` to create a normal advisor-owned agreement.
2. Alternative cleanup path: use function `create Arena cleanup agreement` (`POST /avtaler`) with the same body values and query `ryddeavtale=true` to create the agreement and an `ArenaRyddeAvtale` marker.
3. Capture the generated `{avtaleId}` from the `Location: /avtaler/{avtaleId}` response header for later agreement-specific operations.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with path `avtaleId={captured avtaleId}` and cookie `innlogget-part=VEILEDER` to inspect the created draft.
2. Use function `retrieve agreement by agreement number` (`GET /avtaler/avtaleNr/{avtaleNr}`) with path `avtaleNr={generated avtaleNr from the retrieved agreement}` and cookie `innlogget-part=VEILEDER` to verify the generated agreement number lookup.

Existing-state shortcuts:
- If an equivalent advisor-created agreement already exists, the creation step can be skipped for downstream behaviors, but the later workflow must reuse that agreement's `avtaleId`, `avtaleNr`, `deltakerFnr`, `bedriftNr`, and advisor access scope.
- Direct database setup can create an `Avtale` and optional `ArenaRyddeAvtale`, but must preserve the same ownership and generated id relationships.

Parameter and value bindings:
- `deltakerFnr` binds the agreement to the participant and is later used for access control, overlap checks, and decision-maker filtering.
- `bedriftNr` binds the agreement to the employer and is later used for employer access, organization lookup, and account-number lookup.
- `tiltakstype` fixes the agreement's measure type; it is not updated later and controls required fields, approval rules, subsidy-period rules, and post-approval amendment eligibility.
- `avtaleId` from `Location` is reused in all `/avtaler/{avtaleId}` operations.

Business result:
A persisted agreement draft exists. For advisor creation, `veilederNavIdent` is set to the logged-in advisor. For cleanup creation, an Arena cleanup marker also exists. The agreement is not approved and normally has status `PÅBEGYNT` until required content is filled.

Constraints and invariants:
- Advisor must have write access to `deltakerFnr`.
- Participant must not be under 16; `SOMMERJOBB` also rejects participants over the configured age limit.
- Kode6 participant handling is blocked during advisor creation through PDL checks.
- `ryddeavtale=true` only adds the cleanup marker after the agreement has been created successfully.

Failure and exceptional cases:
- Failing function: `create advisor agreement`
  - Failure condition: The advisor lacks write access to `deltakerFnr`.
  - Why it fails: `Veileder.sjekkTilgangskontroll` rejects the participant before saving.
  - Violated prerequisite or constraint: Advisor-participant write access.
- Failing function: `create advisor agreement`
  - Failure condition: Required body values such as `deltakerFnr` or `bedriftNr` are missing, the participant is too young, or `SOMMERJOBB` age rules are violated.
  - Why it fails: `Avtale` constructor validation throws before persistence.
  - Violated prerequisite or constraint: Valid participant, employer, and measure creation data.
- Failing function: `create Arena cleanup agreement`
  - Failure condition: Agreement creation fails.
  - Why it fails: The cleanup marker is created only after the agreement save path has a valid `Avtale`.
  - Violated prerequisite or constraint: Same as advisor agreement creation.

Implementation notes:
- The OpenAPI operation is one `POST /avtaler`, but `full-behavior.md` correctly separates normal advisor creation and cleanup creation because `ryddeavtale=true` persists additional cleanup state.

### Behavior 2: Create an Employer-Owned Agreement Draft

Business goal:
Allow an employer to initiate an agreement for a participant in an organization and measure type for which the employer has Altinn rights.

Domain context:
This supports employer-led agreement creation before NAV has necessarily assigned an advisor.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create employer agreement` (`POST /avtaler/opprett-som-arbeidsgiver`) with body `deltakerFnr={valid participant FNR}`, `bedriftNr={company number in the employer's Altinn rights}`, and `tiltakstype={measure included in the employer's rights for bedriftNr}`.
2. Capture `{avtaleId}` from `Location: /avtaler/{avtaleId}`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `avtaleId={captured avtaleId}` and cookie `innlogget-part=ARBEIDSGIVER`.
2. Use function `list employer agreements` (`GET /avtaler/min-side-arbeidsgiver`) with query `bedriftNr={same bedriftNr}` to inspect employer-visible agreements for the company.

Existing-state shortcuts:
- If an employer-created agreement already exists, downstream workflows can skip creation and reuse its `avtaleId`, but the employer must still have Altinn access to the same `bedriftNr` and `tiltakstype`.
- Direct database setup must set `opprettetAvArbeidsgiver=true`; otherwise the domain meaning is not equivalent.

Parameter and value bindings:
- `bedriftNr` and `tiltakstype` are validated together against the employer's Altinn access map.
- The same `bedriftNr` can be reused in `list employer agreements`.
- The generated `avtaleId` is consumed by later update, approval, retrieval, takeover, and notification operations.

Business result:
A persisted employer-created agreement draft exists with `opprettetAvArbeidsgiver=true`. It may be unassigned to a NAV advisor until advisor takeover.

Constraints and invariants:
- Employer access is scoped by company number and measure type.
- Employer-visible lists hide agreements that are too old after annulment, interruption, or approved end date, and hide annulment/interruption reasons and qualification group on detail retrieval.

Failure and exceptional cases:
- Failing function: `create employer agreement`
  - Failure condition: The employer lacks Altinn rights for the submitted `bedriftNr` and `tiltakstype`.
  - Why it fails: `Arbeidsgiver.tilgangTilBedriftVedOpprettelseAvAvtale` throws `TilgangskontrollException`.
  - Violated prerequisite or constraint: Employer company/measure authorization.
- Failing function: `create employer agreement`
  - Failure condition: Required participant or company data is missing or age rules fail.
  - Why it fails: Agreement constructor validation rejects invalid creation data.
  - Violated prerequisite or constraint: Valid agreement creation data.

Implementation notes:
- The source does not call EREG to add employer name in the employer creation path, unlike advisor creation.

### Behavior 3: Create and Prepare a Mentor Agreement

Business goal:
Create a mentor agreement where the participant and mentor are distinct people, then obtain the mentor confidentiality signature required before NAV approval.

Domain context:
Mentor agreements have an extra person (`mentorFnr`) and an additional approval-like confidentiality declaration by the mentor.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create mentor agreement as advisor` (`POST /avtaler/opprett-mentor-avtale`) with body `avtalerolle=VEILEDER`, `deltakerFnr={valid accessible participant FNR}`, `mentorFnr={different FNR}`, `bedriftNr={company number}`, and `tiltakstype=MENTOR` to create the mentor agreement as NAV.
2. Alternative employer path: use function `create mentor agreement as employer` (`POST /avtaler/opprett-mentor-avtale`) with body `avtalerolle=ARBEIDSGIVER`, `deltakerFnr={valid participant FNR}`, `mentorFnr={different FNR}`, `bedriftNr={Altinn-authorized company}`, and `tiltakstype=MENTOR`.
3. Capture `{avtaleId}` from `Location`.
4. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with path `avtaleId={captured avtaleId}`, header `If-Unmodified-Since={current sistEndret from retrieval}`, cookie `innlogget-part={VEILEDER or ARBEIDSGIVER with edit access}`, and body containing all required mentor agreement fields, including mentor contact and task fields, start/end dates, participant/employer/advisor contact fields, and other required measure fields.
5. Use function `sign mentor confidentiality declaration` (`POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`) with path `avtaleId={captured avtaleId}`, header `If-Unmodified-Since={current sistEndret}`, and cookie `innlogget-part=MENTOR`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with cookie `innlogget-part=MENTOR` or another accessible party to verify `erGodkjentTaushetserklæringAvMentor=true`.

Existing-state shortcuts:
- If a mentor agreement already exists and already has complete content, steps 1-4 can be skipped; the confidentiality step still requires the mentor party and the same `avtaleId`.
- Direct database setup must ensure `tiltakstype=MENTOR`, `mentorFnr` differs from `deltakerFnr`, and mentor access resolves to the same agreement.

Parameter and value bindings:
- `mentorFnr` identifies the mentor and must be different from `deltakerFnr`.
- `avtalerolle` selects whether advisor or employer creation logic and authorization rules apply.
- The same `avtaleId` produced by creation is used for content update and confidentiality signing.
- `If-Unmodified-Since` must be refreshed after mutations because `sistEndret` changes.

Business result:
A mentor agreement exists with mentor-specific content and the mentor confidentiality timestamp set. This does not itself make the full agreement approved; it only removes a prerequisite for advisor approval.

Constraints and invariants:
- The implementation rejects identical participant and mentor FNR values.
- Only role `MENTOR` can call the confidentiality endpoint.
- Advisor approval of a mentor agreement fails until the confidentiality declaration is signed.

Failure and exceptional cases:
- Failing function: `create mentor agreement as advisor`
  - Failure condition: `deltakerFnr` equals `mentorFnr`.
  - Why it fails: The controller explicitly rejects equal identity values.
  - Violated prerequisite or constraint: Participant and mentor must be distinct people.
- Failing function: `create mentor agreement as employer`
  - Failure condition: `avtalerolle` is neither `VEILEDER` nor `ARBEIDSGIVER`.
  - Why it fails: No agreement is created and the controller throws a runtime exception.
  - Violated prerequisite or constraint: Supported creator role.
- Failing function: `sign mentor confidentiality declaration`
  - Failure condition: The cookie role is not `MENTOR`.
  - Why it fails: The controller checks the resolved party role before loading and approving the agreement.
  - Violated prerequisite or constraint: Mentor-only signing.

Implementation notes:
- The function-level document maps `sign mentor confidentiality declaration` to both the dedicated mentor endpoint and the generic approval endpoint because both ultimately call role-specific approval logic.

### Behavior 4: Complete or Preview an Agreement Draft

Business goal:
Fill out required agreement content and optionally preview the result without saving.

Domain context:
Most approval and subsidy behaviors require an agreement whose required fields are complete. For wage subsidy measures, content completion may generate subsidy periods.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with body `deltakerFnr={valid accessible FNR}`, `bedriftNr={company number}`, `tiltakstype={target measure}` and capture `{avtaleId}`.
2. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with cookie `innlogget-part=VEILEDER` to capture the current `sistEndret`.
3. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with path `avtaleId={captured avtaleId}`, header `If-Unmodified-Since={captured sistEndret}`, cookie `innlogget-part=VEILEDER`, and body `EndreAvtale` containing the required fields for the chosen `tiltakstype`, including start/end dates, contact information, job/work details, and subsidy calculation fields where required.

Optional verification workflow:
1. Use function `dry-run agreement update` (`PUT /avtaler/{avtaleId}/dry-run`) before the real update with the same `avtaleId`, `If-Unmodified-Since`, cookie, and body to inspect computed content and subsidy periods without saving.
2. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) after the real update to verify `felterSomIkkeErFyltUt` is empty and the status is `MANGLER_GODKJENNING` if no approvals exist.
3. Use function `list agreement versions` (`GET /avtaler/{avtaleId}/versjoner`) to inspect stored content versions.

Existing-state shortcuts:
- If a complete but unapproved draft already exists, the create and update steps can be skipped. The same `avtaleId`, role access, and fresh `sistEndret` must still be used for later mutation workflows.
- Direct database setup can create a complete `Avtale` with `AvtaleInnhold`, but generated subsidy periods must be consistent with start/end dates and subsidy values.

Parameter and value bindings:
- `If-Unmodified-Since` is bound to the current `sistEndret`; stale or missing values fail optimistic locking.
- The update body's `startDato`, `sluttDato`, and wage-subsidy fields feed generated `TilskuddPeriode` records for wage-subsidy measures.
- The same `tiltakstype` selected at creation controls which fields are required and which post-approval amendment functions are valid.

Business result:
The current agreement content is updated. For supported subsidy measures, unhandled subsidy periods are regenerated unless the agreement is already entered. The persisted `sistEndret` changes.

Constraints and invariants:
- Agreement content cannot be edited after any participant, employer, or advisor approval unless approvals are revoked first.
- Annulled or interrupted agreements cannot be changed.
- Advisor update refreshes participant, follow-up, and unit data from external services.
- Employer updates on unassigned agreements reject past start/end dates.

Failure and exceptional cases:
- Failing function: `update agreement`
  - Failure condition: Header `If-Unmodified-Since` is missing or older than the stored `sistEndret`.
  - Why it fails: `Avtale.sjekkSistEndret` throws a concurrent-change exception.
  - Violated prerequisite or constraint: Fresh optimistic-lock timestamp.
- Failing function: `update agreement`
  - Failure condition: One or more parties have already approved the agreement.
  - Why it fails: `sjekkOmAvtalenKanEndres` requires approvals to be cleared first.
  - Violated prerequisite or constraint: Draft must be unapproved for full content edit.
- Failing function: `dry-run agreement update`
  - Failure condition: Same validation failures as real update.
  - Why it fails: The dry-run endpoint calls the same domain update logic without saving.
  - Violated prerequisite or constraint: Same as persisted update.

Implementation notes:
- Dry-run returns a mutated in-memory agreement. It is not saved explicitly, but it still executes most domain validation and derived-state calculation.

### Behavior 5: Share an Agreement with a Party

Business goal:
Notify or expose an agreement to a selected agreement party.

Domain context:
Sharing is a domain event action used after an agreement has enough contact information for the selected party.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid creation body and capture `{avtaleId}`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with a complete body that includes a valid mobile number for the target `avtalerolle`.
3. Use function `share agreement with party` (`POST /avtaler/{avtaleId}/del-med-avtalepart`) with path `avtaleId={captured avtaleId}`, cookie `innlogget-part=VEILEDER`, and body `{avtalerolle}` where `avtalerolle={DELTAKER|ARBEIDSGIVER|VEILEDER|MENTOR}`.

Optional verification workflow:
1. Use function `list agreement notification log` (`GET /varsler/avtale-logg`) with query `avtaleId={captured avtaleId}` and cookie `innlogget-part={target or inspecting role}` if notification rows have been generated by event listeners.

Existing-state shortcuts:
- If a non-annulled, non-interrupted agreement already exists with valid phone data for the target party, only the share step is required.
- Direct database setup may prepopulate contact phone fields, but the target `avtalerolle` must map to a valid phone field.

Parameter and value bindings:
- The body `avtalerolle` selects which phone number field is validated: participant, employer, advisor, or mentor.
- `avtaleId` scopes the event to the agreement.

Business result:
The agreement itself is not materially edited, but an `AvtaleDeltMedAvtalepart` domain event is registered for the selected party.

Constraints and invariants:
- The agreement must not be annulled or interrupted.
- The selected party's phone number must pass mobile-number validation.

Failure and exceptional cases:
- Failing function: `share agreement with party`
  - Failure condition: The selected party has no valid mobile number in agreement content.
  - Why it fails: `telefonnummerTilAvtalepart` is validated and invalid values throw `UGYLDIG_TLF`.
  - Violated prerequisite or constraint: Valid target-party contact information.
- Failing function: `share agreement with party`
  - Failure condition: Agreement is annulled or interrupted.
  - Why it fails: `sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt` blocks sharing.
  - Violated prerequisite or constraint: Active agreement state.

Implementation notes:
- The controller does not itself perform a `sjekkTilgang` call before `delMedAvtalepart`; the role method only registers the event on the loaded agreement. This is weaker than most advisor operations.

### Behavior 6: Approve an Agreement Through the Normal Party Sequence

Business goal:
Move a complete agreement from draft to party-approved state, and for non-decision measures into entered agreement state.

Domain context:
Approvals are central to making an agreement legally/operationally effective. Participant and employer approve first; advisor approves last. Mentor agreements require mentor confidentiality before advisor approval.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with valid body and capture `{avtaleId}`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete body and header `If-Unmodified-Since={current sistEndret}`.
3. If `tiltakstype=MENTOR`, use function `sign mentor confidentiality declaration` (`POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`) with cookie `innlogget-part=MENTOR` and header `If-Unmodified-Since={current sistEndret}`.
4. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with cookie `innlogget-part=DELTAKER` and header `If-Unmodified-Since={current sistEndret}`.
5. Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with cookie `innlogget-part=ARBEIDSGIVER` and header `If-Unmodified-Since={current sistEndret}`.
6. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with cookie `innlogget-part=VEILEDER` and header `If-Unmodified-Since={current sistEndret}`.
7. For `SOMMERJOBB`, `VARIG_LONNSTILSKUDD`, or `MIDLERTIDIG_LONNSTILSKUDD`, use function `approve subsidy period` (`POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`) with cookie for a decision-maker context and body `enhet={four-digit NAV unit different from advisor self-approval restriction}` to enter the agreement through subsidy-period approval.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with an accessible cookie role to verify approval timestamps, `godkjentAvNavIdent`, `avtaleInngått`, and current status.
2. Use function `download agreement PDF` (`GET /avtaler/{avtaleId}/pdf`) with cookie `innlogget-part={accessible role}` to verify that NAV approval enables PDF generation.

Existing-state shortcuts:
- If the agreement is already complete, skip creation and update.
- If participant or employer approval already exists, skip only that approval step, but advisor approval must still happen last.
- For wage-subsidy and summer-job measures, party approvals alone are not enough for `avtaleInngått`; a decision-maker subsidy-period approval is still required unless equivalent database state already marks the first period and agreement as entered.

Parameter and value bindings:
- The same `avtaleId` links all party approvals and the later subsidy-period decision.
- Each approval must use a fresh `If-Unmodified-Since` value because previous approvals change `sistEndret`.
- The `innlogget-part` cookie determines which role-specific approval logic runs on the shared `POST /avtaler/{avtaleId}/godkjenn` endpoint.

Business result:
Participant, employer, and advisor approval timestamps are set. For measures that do not require decision-maker subsidy-period approval, `avtaleInngått` is set at advisor approval. For measures that do require decision-maker handling, the agreement enters only when a subsidy period is approved by a decision-maker.

Constraints and invariants:
- Advisor must approve last.
- All required fields must be filled before any approval.
- The agreement must have an assigned advisor.
- Mentor agreements require mentor confidentiality before advisor approval.
- Advisors cannot approve Kode6 participants and must pass follow-up status checks for non-summer-job measures.
- Advisor cannot approve if participant age exceeds measure-specific limits at relevant start/end dates.

Failure and exceptional cases:
- Failing function: `approve agreement as advisor`
  - Failure condition: Participant or employer has not approved.
  - Why it fails: `godkjennForVeileder` enforces advisor-last ordering.
  - Violated prerequisite or constraint: Approval order.
- Failing function: `approve agreement as advisor`
  - Failure condition: The agreement is a mentor agreement without mentor confidentiality approval.
  - Why it fails: The aggregate checks `erGodkjentTaushetserklæringAvMentor`.
  - Violated prerequisite or constraint: Mentor confidentiality prerequisite.
- Failing function: `approve agreement as participant`
  - Failure condition: Required fields are incomplete.
  - Why it fails: `sjekkOmAltErKlarTilGodkjenning` rejects incomplete content.
  - Violated prerequisite or constraint: Complete agreement content.
- Failing function: `approve subsidy period`
  - Failure condition: `enhet` is null, not four digits, not found in Norg, or the decision-maker is the same NAV ident that approved the agreement.
  - Why it fails: Decision-maker and aggregate validation reject invalid unit/self-approval.
  - Violated prerequisite or constraint: Valid cost unit and separation of duties.

Implementation notes:
- The same endpoint has role-specific functions in `full-behavior.md`, which is essential for business interpretation.

### Behavior 7: Approve on Behalf of Missing Parties

Business goal:
Allow an advisor to complete agreement approval when the participant, employer, or both cannot approve directly, while recording reason fields.

Domain context:
This supports exception handling in the approval process.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) and capture `{avtaleId}`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete body and fresh `If-Unmodified-Since`.
3. For participant-only proxy approval, first use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with cookie `innlogget-part=ARBEIDSGIVER`, then use function `approve on behalf of participant` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker`) with body `GodkjentPaVegneGrunn={at least one selected reason}`.
4. For employer-only proxy approval, first use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with cookie `innlogget-part=DELTAKER`, then use function `approve on behalf of employer` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver`) with body `GodkjentPaVegneAvArbeidsgiverGrunn={at least one selected reason}`.
5. For proxy approval for both external parties, use function `approve on behalf of participant and employer` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`) with body `GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn={participant reason and employer reason, each with at least one selected reason}`.
6. For wage-subsidy or summer-job measures, use function `approve subsidy period` (`POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`) after proxy approval with body `enhet={valid four-digit NAV unit}`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) to verify `godkjentPaVegneAv`, `godkjentPaVegneGrunn`, `godkjentPaVegneAvArbeidsgiver`, and approval timestamps.

Existing-state shortcuts:
- If the agreement is already complete and one external party has already approved, skip that external approval step and perform the relevant proxy approval.
- Existing direct database setup must preserve the fact that the other party's approval is missing; otherwise the proxy endpoint rejects duplicate approval.

Parameter and value bindings:
- The proxy approval body reason object is stored on the agreement content and is bound to the approval flags it creates.
- The same `avtaleId` and fresh `sistEndret` chain from setup to proxy approval.

Business result:
The agreement records advisor approval plus participant and/or employer approval timestamps, with proxy flags and reason data for the parties approved on behalf of.

Constraints and invariants:
- Proxy approval for employer or both external parties is limited to `SOMMERJOBB`, `MIDLERTIDIG_LONNSTILSKUDD`, and `VARIG_LONNSTILSKUDD`.
- Reason objects must select at least one reason.
- Duplicate participant/employer/advisor approval is rejected.

Failure and exceptional cases:
- Failing function: `approve on behalf of participant`
  - Failure condition: Employer has not approved.
  - Why it fails: The aggregate requires employer approval before advisor approves on behalf of participant.
  - Violated prerequisite or constraint: Approval ordering.
- Failing function: `approve on behalf of employer`
  - Failure condition: Participant has not approved.
  - Why it fails: The aggregate requires participant approval before employer proxy approval.
  - Violated prerequisite or constraint: Approval ordering.
- Failing function: `approve on behalf of participant and employer`
  - Failure condition: Measure type is not one of the supported wage-subsidy/summer-job types.
  - Why it fails: The aggregate enforces `GODKJENN_PAA_VEGNE_AV_FEIL_TILTAKSTYPE`.
  - Violated prerequisite or constraint: Measure-type eligibility.

Implementation notes:
- `approve on behalf of participant` has two paths in source; both `/godkjenn-paa-vegne-av` and `/godkjenn-paa-vegne-av-deltaker` call the same controller method.

### Behavior 8: Reopen a Pre-Entered Approval Process

Business goal:
Clear existing approval flags so a not-yet-entered agreement can be edited and approved again.

Domain context:
This is needed when parties approved an agreement and then content must be corrected before final entry.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) and capture `{avtaleId}`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete body.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) or `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) to establish at least one approval.
4. Use function `revoke approvals` (`POST /avtaler/{avtaleId}/opphev-godkjenninger`) with path `avtaleId={captured avtaleId}` and cookie `innlogget-part=VEILEDER` or, when advisor has not approved, `innlogget-part=ARBEIDSGIVER`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) to verify participant, employer, advisor, proxy approval flags, and `godkjentAvNavIdent` are cleared.

Existing-state shortcuts:
- If an unentered agreement already has at least one approval, skip creation, update, and initial approval.
- The core revoke action cannot be skipped for the behavior.

Parameter and value bindings:
- `avtaleId` identifies the exact agreement whose approval state is cleared.
- Role binding matters: employers cannot revoke after advisor approval; advisors can revoke broader approval state.

Business result:
Approval timestamps and proxy approval metadata are cleared, and `sistEndret` changes. The agreement can again be edited through `update agreement`.

Constraints and invariants:
- At least one approval must exist.
- Entered agreements cannot have approvals revoked.
- Participant and mentor roles cannot revoke approvals.

Failure and exceptional cases:
- Failing function: `revoke approvals`
  - Failure condition: No participant, employer, or advisor approvals exist.
  - Why it fails: `Avtalepart.opphevGodkjenninger` rejects no-op revocation.
  - Violated prerequisite or constraint: There must be approval state to clear.
- Failing function: `revoke approvals`
  - Failure condition: Agreement has already entered.
  - Why it fails: The method rejects `erAvtaleInngått`.
  - Violated prerequisite or constraint: Revocation only before entry.

Implementation notes:
- Revocation clears party approval fields, proxy flags, proxy reasons, and `godkjentAvNavIdent`, but it does not remove content or generated subsidy periods.

### Behavior 9: Decide, Reject, and Return Subsidy Periods

Business goal:
Let decision-makers approve or reject the current subsidy period, and let advisors correct a rejected period by sending it back for a new decision.

Domain context:
For wage-subsidy and summer-job agreements, decision-maker handling of subsidy periods completes entry and controls payment-period progression.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) with `tiltakstype={SOMMERJOBB|MIDLERTIDIG_LONNSTILSKUDD|VARIG_LONNSTILSKUDD}` and capture `{avtaleId}`.
2. Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with complete content including `startDato`, `sluttDato`, wage-subsidy calculation values, and cost-center data so subsidy periods are generated.
3. Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) and function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `If-Unmodified-Since` headers.
4. Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with fresh `If-Unmodified-Since`.
5. Use function `approve subsidy period` (`POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`) with body `enhet={valid four-digit NAV unit}` to approve the current subsidy period.
6. Alternative rejection path: instead of step 5, use function `reject subsidy period` (`POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`) with body `avslagsårsaker={non-empty set}` and `avslagsforklaring={text}`.
7. To correct a rejected period, use function `send rejected subsidy period back` (`POST /avtaler/{avtaleId}/send-tilbake-til-beslutter`) with path `avtaleId={same avtaleId}` and advisor access.

Optional verification workflow:
1. Use function `list decision-maker agreements` (`GET /avtaler/beslutter-liste`) with query `tilskuddPeriodeStatus={UBEHANDLET|AVSLÅTT|GODKJENT}`, optional `tiltakstype`, `bedriftNr`, `avtaleNr`, `navEnhet`, `sorteringskolonne`, `page`, `size`, and `sorteringOrder` to inspect decision-maker queues.
2. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) to inspect `gjeldendeTilskuddsperiode`, `tilskuddPeriode` statuses, and `avtaleInngått`.

Existing-state shortcuts:
- If a complete, party-approved wage-subsidy/summer-job agreement with generated unhandled subsidy periods already exists, skip setup and perform only the decision step.
- If a rejected active subsidy period already exists, the return step can be called directly with the same `avtaleId`.
- Direct database setup must maintain active period flags, status ordering, `løpenummer`, and agreement ownership.

Parameter and value bindings:
- The update body's dates and calculation values create `TilskuddPeriode` children consumed by approval/rejection.
- `enhet` in `approve subsidy period` becomes the period's decision/cost unit.
- `avslagsårsaker` and `avslagsforklaring` are stored on the rejected current period and used in notification text.

Business result:
Approval marks the current subsidy period `GODKJENT`, may set decision-maker approval and `avtaleInngått` on the agreement, and emits a subsidy-period approved event. Rejection marks the current period `AVSLÅTT`. Sending back deactivates active rejected periods and adds new unhandled replacements.

Constraints and invariants:
- Subsidy periods can only be handled after advisor approval.
- Decision-maker must have participant access.
- Decision-maker unit must exist in Norg and be four digits.
- Decision-maker cannot approve a period for an agreement they approved as advisor.

Failure and exceptional cases:
- Failing function: `approve subsidy period`
  - Failure condition: Advisor approval is missing.
  - Why it fails: The aggregate requires `erGodkjentAvVeileder`.
  - Violated prerequisite or constraint: Agreement must be NAV-approved before period treatment.
- Failing function: `reject subsidy period`
  - Failure condition: Advisor approval is missing.
  - Why it fails: Same `TILSKUDDSPERIODE_KAN_KUN_BEHANDLES_VED_INNGAATT_AVTALE` check.
  - Violated prerequisite or constraint: Agreement must be ready for subsidy-period treatment.
- Failing function: `send rejected subsidy period back`
  - Failure condition: No active rejected period exists.
  - Why it fails: The method does not fail; it simply has no rejected period to deactivate and replace.
  - Violated prerequisite or constraint: Domain expectation of a rejected current period is not enforced as an error.

Implementation notes:
- `gjeldendeTilskuddsperiode` prioritizes the first rejected active period, then treatable periods, then latest approved, then the first active period.

### Behavior 10: Maintain an Approved Agreement Through Amendments, Shortening, and Extension

Business goal:
Change selected agreement content after NAV approval while preserving version history and pushing affected subsidy periods back to decision-maker handling where needed.

Domain context:
After an agreement is approved, full draft editing is blocked. The service instead exposes targeted amendment functions for specific content areas.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`), `update agreement` (`PUT /avtaler/{avtaleId}`), `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`), `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`), and `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) to create an approved agreement.
2. Use one of the amendment functions with path `avtaleId={same avtaleId}` and a body matching the target content: `change contact information` (`POST /avtaler/{avtaleId}/endre-kontaktinfo`), `change job description` (`POST /avtaler/{avtaleId}/endre-stillingbeskrivelse`), `change follow-up and adaptation text` (`POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging`), `change subsidy calculation` (`POST /avtaler/{avtaleId}/endre-tilskuddsberegning`), `change work-training goals` (`POST /avtaler/{avtaleId}/endre-maal`), `change inclusion subsidy expenses` (`POST /avtaler/{avtaleId}/endre-inkluderingstilskudd`), or `change mentor details` (`POST /avtaler/{avtaleId}/endre-om-mentor`).
3. For date-range changes, use function `shorten agreement` (`POST /avtaler/{avtaleId}/forkort`) with body `sluttDato={date before current sluttDato}`, `grunn={non-blank reason}`, and `annetGrunn={text when grunn=Annet}`, or use function `extend agreement` (`POST /avtaler/{avtaleId}/forleng`) with body `sluttDato={date after current sluttDato}`.

Optional verification workflow:
1. Use the matching dry-run function before saving where available: `dry-run subsidy calculation change`, `dry-run agreement shortening`, or `dry-run agreement extension` with the same path/body values.
2. Use function `list agreement versions` (`GET /avtaler/{avtaleId}/versjoner`) to verify a new content version.
3. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) to inspect changed fields and subsidy-period statuses.

Existing-state shortcuts:
- If an approved, non-annulled, non-interrupted agreement already exists with the correct `tiltakstype`, skip creation and approvals.
- Direct database setup must include the relevant approval state and current content version; measure-specific amendments require matching measure type.

Parameter and value bindings:
- `avtaleId` binds the amendment to the current agreement aggregate.
- Amendment body values become the new `gjeldendeInnhold` version.
- For inclusion-subsidy amendments, existing expense `id` values from the current version must be echoed for existing lines; new lines omit `id` and receive generated UUIDs.
- For work-training goals, submitted goals receive new generated UUIDs.

Business result:
A new approved content version becomes current. Many amendments call `sendTilbakeTilBeslutter`, which deactivates active rejected subsidy periods and creates unhandled replacements. Shortening and extension adjust or create subsidy periods according to existing period status and refund status.

Constraints and invariants:
- All post-approval amendments require an agreement that is already NAV-approved.
- Measure-specific functions enforce measure type: work-training goals require `ARBEIDSTRENING`, inclusion expenses require `INKLUDERINGSTILSKUDD`, mentor details require `MENTOR`, and subsidy calculation requires wage-subsidy/summer-job measures.
- Annulled or interrupted agreements cannot be amended.
- Shortening cannot move the end date after or equal to the current end date and cannot shorten before already paid/sent refund periods.
- Extension cannot move the end date before or equal to the current end date.

Failure and exceptional cases:
- Failing function: `change work-training goals`
  - Failure condition: Agreement `tiltakstype` is not `ARBEIDSTRENING`, the list is empty, or a goal lacks description/category.
  - Why it fails: The aggregate enforces measure type and required goal fields.
  - Violated prerequisite or constraint: Work-training-specific goal amendment.
- Failing function: `change inclusion subsidy expenses`
  - Failure condition: Total amount exceeds 136000, list is empty, fields are missing, or existing ids do not match current version count.
  - Why it fails: The aggregate validates total, required fields, and frontend synchronization.
  - Violated prerequisite or constraint: Inclusion-subsidy expense consistency.
- Failing function: `shorten agreement`
  - Failure condition: New end date is not earlier, reason is missing, or it cuts before a paid/sent refund period.
  - Why it fails: `forkortAvtale` enforces date, reason, and refund-period constraints.
  - Violated prerequisite or constraint: Valid shortening boundary.
- Failing function: `extend agreement`
  - Failure condition: New end date is not later.
  - Why it fails: `forlengAvtale` enforces extension direction.
  - Violated prerequisite or constraint: Valid extension boundary.

Implementation notes:
- The dry-run shorten endpoint passes reason `"dry run"` and empty `annetGrunn`, so it does not validate the real reason text the same way as persisted shortening.

### Behavior 11: Reassign, Refresh, and Correct Administrative Agreement Data

Business goal:
Keep agreement ownership and NAV unit/cost-center data current.

Domain context:
Agreements can be employer-created and unassigned, transferred between advisors, refreshed from external person/unit sources, or corrected for cost center before entry.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create employer agreement` (`POST /avtaler/opprett-som-arbeidsgiver`) with valid body and capture `{avtaleId}`.
2. Use function `take over agreement as advisor` (`PUT /avtaler/{avtaleId}/overta`) with path `avtaleId={captured avtaleId}` and advisor access to assign the logged-in advisor.
3. Use function `refresh follow-up unit` (`POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet`) with the same `avtaleId` and advisor access to refresh participant, follow-up, and unit names.
4. For wage-subsidy/summer-job agreements with unhandled or rejected periods, use function `change cost center` (`POST /avtaler/{avtaleId}/endre-kostnadssted`) with body `enhet={valid NAV unit}` before the agreement is entered.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) to inspect `veilederNavIdent`, `enhetOppfolging`, `enhetsnavnOppfolging`, `enhetGeografisk`, and cost-center fields.
2. Use function `list accessible agreements` (`GET /avtaler`) with cookie `innlogget-part=VEILEDER` and query `veilederNavIdent={new advisor}` to verify advisor listing.

Existing-state shortcuts:
- If an agreement is already assigned to the target advisor, takeover is not applicable and will fail; skip directly to refresh/cost-center correction if those are the desired behaviors.
- Direct database setup can assign `veilederNavIdent`, but generated subsidy-period recalculation and takeover events will not be equivalent.

Parameter and value bindings:
- `avtaleId` is reused across takeover, refresh, cost-center correction, and verification.
- `enhet` in `change cost center` is validated through Norg and then copied to unhandled/rejected subsidy periods through regenerated periods.

Business result:
The logged-in advisor becomes agreement advisor, external unit/person data is refreshed, and cost-center fields are corrected on the current agreement and generated subsidy periods where applicable.

Constraints and invariants:
- Takeover fails if the logged-in advisor is already the agreement advisor.
- Takeover of an unassigned employer-created agreement generates subsidy periods and an "employer-created agreement distributed" event.
- Cost-center correction is blocked after agreement entry and requires at least one active unhandled or rejected subsidy period.

Failure and exceptional cases:
- Failing function: `take over agreement as advisor`
  - Failure condition: The current advisor already equals the logged-in advisor.
  - Why it fails: `Veileder.overtaAvtale` throws `ErAlleredeVeilederException`.
  - Violated prerequisite or constraint: Takeover must change advisor ownership.
- Failing function: `change cost center`
  - Failure condition: `enhet` does not resolve in Norg.
  - Why it fails: `oppdatereKostnadssted` throws `ENHET_FINNES_IKKE`.
  - Violated prerequisite or constraint: Valid NAV unit.
- Failing function: `change cost center`
  - Failure condition: Agreement is already entered or has no editable subsidy periods.
  - Why it fails: The aggregate blocks entered agreements and missing editable periods.
  - Violated prerequisite or constraint: Pre-entry editable subsidy-period state.

Implementation notes:
- `refresh follow-up unit` persists external data changes but has no dry-run counterpart.

### Behavior 12: Mark, Unmark, and Use After-Registration Eligibility

Business goal:
Let a decision-maker toggle whether an agreement may be after-registered before it is entered.

Domain context:
After-registration affects start/end-date validation for some agreement flows.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) and capture `{avtaleId}`.
2. Use function `mark agreement eligible for after-registration` (`POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`) with path `avtaleId={captured avtaleId}` and decision-maker access.
3. To remove eligibility, use function `remove after-registration eligibility` (`POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`) a second time with the same `avtaleId`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) to inspect `godkjentForEtterregistrering`.

Existing-state shortcuts:
- If a pre-entered agreement already exists, skip creation and call the toggle endpoint.
- If the agreement already has the desired flag value, calling the endpoint again will invert it; there is no idempotent "set true" or "set false" function.

Parameter and value bindings:
- `avtaleId` scopes the toggle.
- The function name differs from implementation semantics: the endpoint toggles current state rather than setting a submitted value.

Business result:
`godkjentForEtterregistrering` changes from false to true or true to false, `sistEndret` changes, and a corresponding domain event is registered.

Constraints and invariants:
- Decision-maker must have access to the participant.
- Entered agreements cannot be marked or unmarked for after-registration.

Failure and exceptional cases:
- Failing function: `mark agreement eligible for after-registration`
  - Failure condition: Agreement is already entered.
  - Why it fails: `togglegodkjennEtterregistrering` rejects `erAvtaleInngått`.
  - Violated prerequisite or constraint: Only pre-entry agreements can be toggled.
- Failing function: `remove after-registration eligibility`
  - Failure condition: Decision-maker lacks participant access.
  - Why it fails: `Beslutter.sjekkTilgang` fails before toggling.
  - Violated prerequisite or constraint: Decision-maker participant access.

Implementation notes:
- The two functions are the same endpoint and no request body identifies the intended target value, making the operation non-idempotent.

### Behavior 13: Annul or Soft-Delete an Agreement

Business goal:
Remove an agreement from active business use, either by formal annulment or by administrative soft deletion.

Domain context:
Annulment is a business status transition with subsidy-period side effects. Soft deletion hides an agreement from ordinary access and listing.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create advisor agreement` (`POST /avtaler`) and capture `{avtaleId}`.
2. For formal annulment, use function `annul agreement` (`POST /avtaler/{avtaleId}/annuller`) with path `avtaleId={captured avtaleId}`, header `If-Unmodified-Since={current sistEndret}`, and body `annullertGrunn={reason text}`.
3. For administrative hiding instead of formal annulment, use function `soft-delete agreement` (`POST /avtaler/{avtaleId}/slettemerk`) with path `avtaleId={captured avtaleId}` and an advisor identity included in `SlettemerkeProperties.ident`.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) as an authorized internal party after annulment to verify `annullertTidspunkt`, `annullertGrunn`, `feilregistrert` if reason is `Feilregistrering`, and subsidy-period statuses.
2. Use function `list accessible agreements` (`GET /avtaler`) to verify soft-deleted or feilregistrert agreements are omitted from ordinary list results.

Existing-state shortcuts:
- If an active agreement already exists, creation can be skipped.
- Direct database setup can mark `annullertTidspunkt` or `slettemerket`, but will not emit the same domain events or adjust subsidy periods.

Parameter and value bindings:
- `annullertGrunn` is stored as the agreement annulment reason.
- The literal reason `Feilregistrering` additionally sets `feilregistrert=true`.
- The same `avtaleId` scopes the state transition and later verification.

Business result:
Annulment sets `annullertTidspunkt`, stores the reason, removes unhandled subsidy periods, annuls approved periods unless refund status blocks, assigns advisor if the agreement was unassigned, and marks feilregistrering when the reason matches. Soft deletion sets `slettemerket=true`.

Constraints and invariants:
- Agreements with paid subsidy periods or approved refunds cannot be annulled through the agreement endpoint.
- Annulled, interrupted, and soft-deleted agreements are blocked from ordinary mutation/access flows.
- Soft deletion requires a configured privileged advisor identity.

Failure and exceptional cases:
- Failing function: `annul agreement`
  - Failure condition: Any subsidy period is paid or has approved refund.
  - Why it fails: `sjekkAtIkkeAvtalenInneholderUtbetaltTilskuddsperiode` rejects the annulment.
  - Violated prerequisite or constraint: Agreement must not contain paid/approved-refund subsidy periods.
- Failing function: `soft-delete agreement`
  - Failure condition: Logged-in advisor is not configured for soft-delete access.
  - Why it fails: `Veileder.slettemerk` checks `SlettemerkeProperties.ident`.
  - Violated prerequisite or constraint: Administrative soft-delete authorization.

Implementation notes:
- Soft deletion is not a hard delete. The row remains and access checks return false because `harTilgang` rejects `slettemerket`.

### Behavior 14: Read, Search, and Reuse Agreement Views

Business goal:
Find agreements through role-scoped lists, saved searches, direct id lookup, agreement-number lookup, employer-side list, and decision-maker queue.

Domain context:
Agreement users work from overviews, saved filters, direct links, and role-specific task queues.

Starting point:
Existing readable agreement state, or an empty database for empty-list behavior.

Required execution workflow:
1. Use function `list accessible agreements` (`GET /avtaler`) with cookie `innlogget-part={VEILEDER|ARBEIDSGIVER|DELTAKER|MENTOR|BESLUTTER as supported by login context}`, query fields from `AvtalePredicate`, optional `sorteringskolonne`, `page`, and `size`.
2. Use function `search agreements and save search` (`POST /avtaler/sok`) with cookie `innlogget-part={role}`, body `AvtalePredicate={desired filters}`, optional `sorteringskolonne`, `page`, and `size`; capture returned `sokId`.
3. Use function `replay saved agreement search` (`GET /avtaler/sok`) with query `sokId={captured sokId}`, same or compatible `innlogget-part`, optional `sorteringskolonne`, `page`, and `size`.
4. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `avtaleId={known generated UUID}` and cookie `innlogget-part={authorized role}`.
5. Use function `retrieve agreement by agreement number` (`GET /avtaler/avtaleNr/{avtaleNr}`) with `avtaleNr={generated agreement number}` and cookie `innlogget-part={authorized role}`.
6. Use function `list employer agreements` (`GET /avtaler/min-side-arbeidsgiver`) with query `bedriftNr={company number}` for employer-side view.
7. Use function `list decision-maker agreements` (`GET /avtaler/beslutter-liste`) with decision-maker context and query filters for subsidy-period decision queue.
8. Use function `list agreement versions` (`GET /avtaler/{avtaleId}/versjoner`) with `avtaleId={same avtaleId}` to inspect version history.

Optional verification workflow:
None.

Existing-state shortcuts:
- Direct database fixtures can be used to create agreements for non-empty list/search results.
- If only empty-list semantics are being inspected, no agreement setup is required.

Parameter and value bindings:
- `sokId` is generated from the search predicate hash in `search agreements and save search` and consumed by `replay saved agreement search`.
- `avtaleNr` is generated by the database and can be obtained from agreement retrieval.
- `innlogget-part` scopes access and controls masking/filtering behavior.

Business result:
No agreement state changes are expected except saved-search metadata: a new or existing `FilterSok` is saved/updated, count and last-search timestamp are updated, and the search response includes `sokId`.

Constraints and invariants:
- Lists filter out `feilregistrert` and `slettemerket` agreements.
- Page and size are normalized with `Math.abs`.
- Employer search silently returns empty results when filtering for a company without access.
- Unknown `sokId` returns an empty synthetic result rather than failing.

Failure and exceptional cases:
- Failing function: `retrieve agreement by id`
  - Failure condition: The id does not exist or the role lacks access.
  - Why it fails: Repository lookup or `sjekkTilgang` rejects the request.
  - Violated prerequisite or constraint: Existing accessible agreement.
- Failing function: `list decision-maker agreements`
  - Failure condition: Decision-maker has no NAV units.
  - Why it fails: `hentNavEnheter` returns empty and `NavEnhetIkkeFunnetException` is thrown.
  - Violated prerequisite or constraint: Decision-maker unit assignment.

Implementation notes:
- Saved-search hash collisions are only logged as errors when a stored search id maps to a different predicate; the request still returns a `sokId`.

### Behavior 15: Inspect Agreement Outputs and External Reference Data

Business goal:
Retrieve supporting information needed by users and clients: PDFs, employer account numbers, Salesforce-dialog eligibility, organization data, logged-in user context, Altinn rights URLs, code lists, feature flags, and health.

Domain context:
These read behaviors support user interfaces and downstream business tasks around agreements.

Starting point:
No prior service state for public reference/feature/health functions; an accessible agreement for agreement-specific outputs.

Required execution workflow:
1. For PDF output, use function `create advisor agreement`, `update agreement`, party approval functions, and `approve agreement as advisor`; then use function `download agreement PDF` (`GET /avtaler/{avtaleId}/pdf`) with path `avtaleId={same avtaleId}` and cookie `innlogget-part={authorized role}`.
2. For account lookup, use function `get employer account number` (`GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver`) with `avtaleId={accessible agreement id}` and cookie `innlogget-part={authorized role}`.
3. Use function `check Salesforce dialog visibility` (`GET /avtaler/{avtaleId}/vis-salesforce-dialog`) with the same `avtaleId` and role cookie.
4. Use function `get logged-in user` (`GET /innlogget-bruker`) with cookie `innlogget-part={selected role}`.
5. Use function `look up organization` (`GET /organisasjoner`) with query `bedriftNr={company number}`.
6. Use function `get Altinn rights request URLs` (`GET /be-om-altinn-rettighet-urler`) with query `orgNr={organization number}`.
7. Use function `get all code lists` (`GET /kodeverk`), `get status code list` (`GET /kodeverk/statuser`), or `get measure type code list` (`GET /kodeverk/tiltakstyper`) without prior business state.
8. Use function `evaluate feature toggles` (`GET /feature`) with query `feature={list of names}` or function `get feature variants` (`GET /feature/variant`) with query `feature={list of names}`.
9. Use function `run health check` (`GET /internal/healthcheck`) to verify database-backed health.

Optional verification workflow:
None.

Existing-state shortcuts:
- For PDF, account number, and Salesforce dialog, skip setup if an accessible agreement already exists and satisfies the output-specific state.
- Reference-data functions need no agreement state and can be called directly.

Parameter and value bindings:
- `avtaleId` scopes PDF/account/Salesforce reads to one accessible agreement.
- PDF requires advisor approval state created by `approve agreement as advisor`.
- `bedriftNr` in organization lookup should match agreement `bedriftNr` when used as agreement support data.
- `orgNr` is injected into every returned Altinn rights URL.

Business result:
The service returns supporting output or reference data without changing agreement state.

Constraints and invariants:
- PDF download fails unless the agreement is advisor-approved.
- Salesforce dialog returns true only for selected configured offices, `MIDLERTIDIG_LONNSTILSKUDD`, and status `GJENNOMFØRES` or `AVSLUTTET`.
- Health check depends on a successful database query.

Failure and exceptional cases:
- Failing function: `download agreement PDF`
  - Failure condition: Agreement lacks advisor approval.
  - Why it fails: Controller checks `erGodkjentAvVeileder`.
  - Violated prerequisite or constraint: PDF only after NAV approval.
- Failing function: `get logged-in user`
  - Failure condition: `innlogget-part` cookie is missing.
  - Why it fails: Controller throws `IkkeValgtPartException`.
  - Violated prerequisite or constraint: Selected logged-in party role.

Implementation notes:
- `get all code lists` composes the same status and measure-type lists returned by the two narrower code-list endpoints.

### Behavior 16: Read and Acknowledge Notifications

Business goal:
Let a party inspect unread notifications and mark them read.

Domain context:
Agreement lifecycle events generate notifications for parties. Users need both a bell view and an agreement-specific log.

Starting point:
Existing notification state produced by event listeners or direct database setup.

Required execution workflow:
1. Use function `list overview notifications` (`GET /varsler/oversikt`) with cookie `innlogget-part={role}` to list unread bell notifications for identifiers owned by the logged-in party.
2. Use function `list agreement modal notifications` (`GET /varsler/avtale-modal`) with query `avtaleId={agreement UUID}` and cookie `innlogget-part={role}` to list unread bell notifications for one agreement.
3. Use function `list agreement notification log` (`GET /varsler/avtale-logg`) with query `avtaleId={agreement UUID}` and cookie `innlogget-part={role}` to list all notifications for that agreement and role after checking agreement access.
4. Use function `mark notification as read` (`POST /varsler/{varselId}/sett-til-lest`) with path `varselId={notification UUID returned by a list}` and cookie `innlogget-part={same owning role}`.
5. To mark several, use function `mark multiple notifications as read` (`POST /varsler/sett-alle-til-lest`) with body `[varselId1, varselId2, ...]` and cookie `innlogget-part={same owning role}`.

Optional verification workflow:
1. Use function `list overview notifications` (`GET /varsler/oversikt`) again to confirm the read notification no longer appears in unread bell results.

Existing-state shortcuts:
- Direct database setup can create `Varsel` rows with `lest=false`, `bjelle=true`, `identifikator={party identifier}`, `avtaleId={agreement id}`, and `mottaker={role}`.
- The read action still requires the notification id to belong to one of the logged-in party's identifiers.

Parameter and value bindings:
- `varselId` comes from notification list results and is consumed by mark-read functions.
- `innlogget-part` resolves the party identifiers used to find owned notifications.
- `avtaleId` links agreement-specific modal/log notification reads.

Business result:
Selected notifications have `lest=true`. Overview/modal unread bell queries no longer return them.

Constraints and invariants:
- Agreement log checks access to the agreement before returning notifications.
- Mark-read lookup is ownership-scoped by party identifiers.
- Bulk mark-read is implemented by calling the single mark-read method for each id.

Failure and exceptional cases:
- Failing function: `mark notification as read`
  - Failure condition: `varselId` does not exist or is not owned by the logged-in party.
  - Why it fails: Repository returns null and the controller dereferences it, causing an implementation error rather than a controlled not-found response.
  - Violated prerequisite or constraint: Existing owned notification id.
- Failing function: `list agreement notification log`
  - Failure condition: The party lacks access to the agreement.
  - Why it fails: `avtalepart.sjekkTilgang(avtale)` rejects the request.
  - Violated prerequisite or constraint: Agreement access.

Implementation notes:
- There is no API function to create notifications directly; they are produced by event listeners or must be set up directly in the database for isolated testing.

### Behavior 17: Journal Agreement Versions

Business goal:
Expose agreement versions ready for journaling and mark them with external journalpost ids after journaling is completed.

Domain context:
Journaling is a system-to-system process for archiving agreement versions outside the core agreement API.

Starting point:
Existing agreement versions that the repository query considers ready for journaling.

Required execution workflow:
1. Use function `list unjournaled agreements` (`GET /internal/avtaler`) with a valid system token to get agreement-version payloads and capture each agreement content version id.
2. Use function `mark agreement versions as journaled` (`PUT /internal/avtaler`) with body `{avtaleInnholdId: journalpostId, ...}` where each key is a version id returned by step 1 and each value is the external journalpost id.

Optional verification workflow:
1. Use function `list unjournaled agreements` (`GET /internal/avtaler`) again to verify marked versions no longer appear if repository criteria exclude non-null `journalpostId`.

Existing-state shortcuts:
- Direct database setup can create agreement content versions that meet journal query criteria.
- The mark step still requires exact `AvtaleInnhold` ids and journalpost ids.

Parameter and value bindings:
- The version ids returned by `list unjournaled agreements` are the map keys consumed by `mark agreement versions as journaled`.
- The external `journalpostId` values are persisted on those `AvtaleInnhold` rows.

Business result:
Selected agreement versions are updated with journalpost ids.

Constraints and invariants:
- Both endpoints require a system-authenticated caller validated by `InnloggingService`.
- The update silently affects only ids found by `findAllById`; nonexistent keys are not explicitly reported in the controller.

Failure and exceptional cases:
- Failing function: `list unjournaled agreements`
  - Failure condition: Caller is not a valid system user.
  - Why it fails: `validerSystembruker` rejects the token.
  - Violated prerequisite or constraint: System-to-system authentication.
- Failing function: `mark agreement versions as journaled`
  - Failure condition: Body uses ids that are not existing content version ids.
  - Why it fails: No matching entities are loaded; the endpoint may still return OK without marking those ids.
  - Violated prerequisite or constraint: Existing agreement version ids.

Implementation notes:
- The list mapper includes subsidy periods from the agreement, not only the content version itself.

### Behavior 18: Repair or Rebuild Subsidy Periods as Developer Admin

Business goal:
Perform privileged repair actions for wage-subsidy calculations, missing reduced-percent dates, incorrect subsidy-period dates, and annulled subsidy periods.

Domain context:
These are operational maintenance workflows rather than ordinary user workflows.

Starting point:
Existing agreements or subsidy periods requiring repair.

Required execution workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with an accessible role to obtain `tilskuddPeriode[*].id` where a period-specific repair needs `{tilskuddsperiodeId}`.
2. Use function `recalculate wage subsidy` (`POST /utvikler-admin/reberegn`) with body `[avtaleId1, avtaleId2, ...]` to recalculate selected agreements with missing wage-subsidy totals.
3. Use function `dry-run missing reduced-percent date fix` (`POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}`) with path `migreringsDato={yyyy-MM-dd}` to count affected varig wage-subsidy agreements.
4. Use function `fix missing reduced-percent date` (`POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}`) with the same `migreringsDato` to persist recalculation and migrated subsidy periods.
5. Use function `generate subsidy periods for agreement` (`POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}`) with path `avtaleId={agreement UUID}` and `migreringsDato={yyyy-MM-dd}` to rebuild one agreement's periods.
6. Use function `recalculate unhandled subsidy periods` (`POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}`) with `avtaleId={agreement UUID}`.
7. Use function `find subsidy period date-order problems` (`POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer`) to log date-order anomalies.
8. For period-specific repair, use one of `annul subsidy period` (`POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}`), `annul and resend approved subsidy period` (`POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}`), `annul and generate unhandled subsidy period` (`POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}`), or `annul and generate Arena-treated periods` (`POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}`) with the relevant captured ids and dates.

Optional verification workflow:
1. Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) to inspect recalculated content and subsidy-period statuses.
2. Use function `list decision-maker agreements` (`GET /avtaler/beslutter-liste`) to inspect queue effects.

Existing-state shortcuts:
- These functions mostly start from pre-existing database state; there is no complete public API workflow to create every malformed repair scenario.
- Direct database setup is relevant for missing reduced-percent dates, paid/refund statuses, bad period date ordering, and annulled-period replacements.

Parameter and value bindings:
- `avtaleId` identifies the agreement to repair.
- `tilskuddsperiodeId` must be obtained from an agreement's `tilskuddPeriode` collection or direct database lookup.
- `migreringsDato` and `dato` determine which rebuilt periods are marked `BEHANDLET_I_ARENA`.

Business result:
Depending on function, wage-subsidy totals are recomputed, reduced-percent dates/sums are filled, unhandled periods are regenerated, selected periods are annulled, replacement periods are created as approved/unhandled/Arena-treated, or anomalies are logged.

Constraints and invariants:
- All developer-admin repair functions require the configured developer AD group.
- Several aggregate repair methods only support `MIDLERTIDIG_LONNSTILSKUDD`, `VARIG_LONNSTILSKUDD`, and `SOMMERJOBB`.
- Replacement from an annulled period requires the source period to have status `ANNULLERT`.

Failure and exceptional cases:
- Failing function: `annul and resend approved subsidy period`
  - Failure condition: The period id does not exist.
  - Why it fails: Repository lookup throws `RessursFinnesIkkeException`.
  - Violated prerequisite or constraint: Existing subsidy period id.
- Failing function: `annul and generate unhandled subsidy period`
  - Failure condition: The source period cannot be made/treated as annulled.
  - Why it fails: Replacement method requires `TilskuddPeriodeStatus.ANNULLERT`.
  - Violated prerequisite or constraint: Annulled source period state.
- Failing function: `recalculate wage subsidy`
  - Failure condition: Agreement is not a supported subsidy measure or lacks required calculation fields.
  - Why it fails: `Avtale.reberegnLønnstilskudd` enforces measure type and required fields.
  - Violated prerequisite or constraint: Repairable wage-subsidy state.

Implementation notes:
- `find subsidy period date-order problems` only logs warnings and does not mutate or return problem details.

### Behavior 19: Replay Agreement Events and Patch Data Warehouse Messages

Business goal:
Recreate downstream integration messages for selected or all agreements.

Domain context:
This supports repair and backfill for data sharing and data warehouse consumers.

Starting point:
Existing agreement state.

Required execution workflow:
1. Use function `patch selected data warehouse messages` (`POST /utvikler-admin/dvh-melding/patch`) with body `avtaleIder=[avtaleId1, avtaleId2, ...]` to create patch messages for selected agreements.
2. Use function `patch all data warehouse messages` (`POST /utvikler-admin/dvh-melding/patchalleavtaler`) to create patch messages for all agreements.
3. Use function `send event message for one agreement` (`POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}`) with path `avtaleId={agreement UUID}` to replay one agreement event message.
4. Use function `dry-run event messages for all agreements` (`POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler`) to simulate full replay.
5. Use function `send event messages for all agreements` (`POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler`) to replay all agreement messages.

Optional verification workflow:
None through this REST API. Verification requires inspecting message repositories, Kafka topics, or logs outside the documented functions.

Existing-state shortcuts:
- Direct database setup can provide agreements for replay/patching.
- For selected operations, the exact `avtaleId` list must match existing agreements; `findAllById` for data warehouse patch silently ignores missing ids.

Parameter and value bindings:
- `avtaleIder` in the patch request selects agreements to materialize as data warehouse patch messages.
- Each data warehouse patch generates a new random `meldingId` and stores the agreement id and current status.
- `avtaleId` in event replay selects one agreement for event message construction.

Business result:
Data warehouse patch rows or agreement event messages are produced for downstream processing.

Constraints and invariants:
- Data warehouse patch operations require the configured DVH AD group.
- All-agreement event replay requires developer access.
- The one-agreement event replay endpoint in source does not call `sjekkTilgang`, unlike all-agreement replay endpoints.

Failure and exceptional cases:
- Failing function: `patch selected data warehouse messages`
  - Failure condition: Caller lacks the configured DVH group.
  - Why it fails: Controller throws `FORBIDDEN`.
  - Violated prerequisite or constraint: DVH admin authorization.
- Failing function: `send event message for one agreement`
  - Failure condition: `avtaleId` does not exist.
  - Why it fails: Repository lookup throws `RessursFinnesIkkeException`.
  - Violated prerequisite or constraint: Existing agreement id.

Implementation notes:
- OpenAPI and `full-behavior.md` include these developer-admin endpoints, but runtime authorization is implementation-defined through AD-group checks.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Hard Delete or Restore an Agreement

Priority:
Critical domain gap

Expected business goal:
Permanently delete an incorrect agreement or restore an accidentally soft-deleted/annulled agreement through the API.

Why it is unsupported:
The API exposes annulment and soft deletion, but no hard delete, undo soft-delete, or undo annulment function.

Existing functions considered:
- `annul agreement`: sets annulment state and adjusts subsidy periods but does not delete.
- `soft-delete agreement`: sets `slettemerket=true` but has no inverse.
- `retrieve agreement by id`: cannot retrieve soft-deleted agreements through normal access because access checks reject them.

Missing capability:
A hard-delete endpoint, restore endpoint, or controlled state transition for reversing soft deletion/annulment.

Proof that function composition is insufficient:
No existing function can set `slettemerket=false`, clear `annullertTidspunkt`, rebuild original active subsidy periods, or remove agreement rows. Delete-and-recreate is not equivalent because it changes `avtaleId`, `avtaleNr`, content version ids, events, notifications, journal links, and downstream integration identity.

Evidence from existing functions/source:
- `soft-delete agreement` only calls `avtale.slettemerk`.
- `Avtalepart.harTilgang` returns false for soft-deleted agreements.
- No function in `full-behavior.md` clears the deletion or annulment fields.

Business impact:
Accidental deletion or annulment cannot be corrected through supported API workflows and may require direct database repair.

### Missing Behavior 2: Direct Lifecycle Management of Subsidy Periods by Agreement Users

Priority:
Critical domain gap

Expected business goal:
List, retrieve, edit, or correct an individual subsidy period by id through a supported business API.

Why it is unsupported:
Subsidy periods are exposed only as embedded agreement data and through decision/admin mutation endpoints. There is no first-class `GET /tilskuddsperioder/{id}`, list-by-agreement endpoint, or ordinary update endpoint.

Existing functions considered:
- `retrieve agreement by id`: exposes periods only inside the agreement payload.
- `approve subsidy period` and `reject subsidy period`: operate only on the current period selected by aggregate logic.
- Developer-admin period functions: mutate by `tilskuddsperiodeId` but are privileged repair actions, not normal lifecycle management.

Missing capability:
First-class subsidy-period retrieval/search/update endpoints and explicit current-period selection.

Proof that function composition is insufficient:
The current period is chosen by `gjeldendeTilskuddsperiode`; callers cannot choose another active period for decision, cannot query a period by id directly, and cannot safely update period metadata except through broad aggregate recalculation or developer repair actions.

Evidence from existing functions/source:
- `gjeldendeTilskuddsperiode` chooses rejected, treatable, approved, or first active period by internal priority.
- `full-behavior.md` has no ordinary subsidy-period read/update functions.

Business impact:
Operational users cannot inspect or correct a specific period without retrieving the whole agreement or using admin tools, making support workflows brittle.

### Missing Behavior 3: Idempotent After-Registration Setting

Priority:
Important robustness gap

Expected business goal:
Set after-registration eligibility explicitly to true or false.

Why it is unsupported:
Both documented functions use the same endpoint and the implementation toggles the existing boolean.

Existing functions considered:
- `mark agreement eligible for after-registration`: toggles state.
- `remove after-registration eligibility`: toggles state.
- `retrieve agreement by id`: can inspect current state but cannot make the toggle idempotent under concurrent changes.

Missing capability:
An endpoint accepting an explicit desired boolean value with optimistic locking.

Proof that function composition is insufficient:
Read-then-toggle can race with another caller and produce the opposite final state. Repeating the same request reverses the intended state.

Evidence from existing functions/source:
- `Beslutter.setOmAvtalenKanEtterregistreres` calls `avtale.togglegodkjennEtterregistrering`.

Business impact:
Clients cannot safely implement "ensure eligible" or "ensure not eligible" semantics.

### Missing Behavior 4: Controlled Notification Creation, Search, and Ownership Errors

Priority:
Important robustness gap

Expected business goal:
Create, search, and safely acknowledge notifications with clear not-found/forbidden outcomes.

Why it is unsupported:
Notifications can only be read and marked read. Creation happens indirectly through event listeners, and mark-read lacks explicit null handling.

Existing functions considered:
- `list overview notifications`, `list agreement modal notifications`, `list agreement notification log`: read existing notification rows.
- `mark notification as read`: marks an owned notification but dereferences null if not found.
- `mark multiple notifications as read`: repeats the same behavior for each id.

Missing capability:
Notification creation API, search/filter API, and controlled 404/403 responses for non-owned notification ids.

Proof that function composition is insufficient:
No existing function can create a notification row or query historical/read notifications across arbitrary criteria. Passing an unowned id cannot be turned into a safe domain response through chaining.

Evidence from existing functions/source:
- `VarselController.settTilLest` calls `varsel.settTilLest()` without checking for null.
- `Varsel.nyttVarsel` is a domain factory, not exposed as a REST function.

Business impact:
Support and UI workflows are dependent on indirect event processing and can encounter server errors for stale or unauthorized notification ids.

### Missing Behavior 5: Audit, Event, and Message Verification Reads

Priority:
Important robustness gap

Expected business goal:
Verify which events, data-warehouse patches, or replay messages were produced for an agreement.

Why it is unsupported:
The API can produce patch/replay messages but cannot read them back.

Existing functions considered:
- `patch selected data warehouse messages` and `patch all data warehouse messages`: write DVH message entities.
- `send event message for one agreement`, `send event messages for all agreements`, and `dry-run event messages for all agreements`: publish/replay messages or dry-run them.
- `retrieve agreement by id`: returns agreement state, not integration message state.

Missing capability:
Read endpoints for event log, DVH message rows, replay status, or published-message audit.

Proof that function composition is insufficient:
After a patch or replay, no documented function returns generated `meldingId`, publish status, topic offset, or message contents. External database/Kafka/log access is required.

Evidence from existing functions/source:
- DVH patch generates `meldingId` and saves `DvhMeldingEntitet`, but no controller reads it.
- Event replay controllers return void.

Business impact:
Operators cannot verify integration repair outcomes through the API that triggered them.

### Missing Behavior 6: Safe Bulk Transaction Semantics for Administrative Repairs

Priority:
Important robustness gap

Expected business goal:
Run bulk repair operations with clear per-item results, rollback semantics, and retry-safe reporting.

Why it is unsupported:
Several bulk functions return void and either log counts or iterate without reporting per-agreement outcomes.

Existing functions considered:
- `recalculate wage subsidy`: loops through ids and saves each agreement.
- `fix missing reduced-percent date`: filters and saves matching agreements, logging counts.
- `patch selected data warehouse messages`: ignores ids not returned by `findAllById`.
- `send event messages for all agreements`: returns no per-item result.

Missing capability:
Structured result reports, idempotency keys, per-item error lists, and transactional policy controls.

Proof that function composition is insufficient:
No follow-up function exposes what was attempted, skipped, failed, or partially committed. Retrying may duplicate messages or repeat repairs without visibility.

Evidence from existing functions/source:
- Admin controllers mostly return `void`.
- Missing ids in selected DVH patch are not reported.

Business impact:
Operational repair can be ambiguous and difficult to audit, especially during incidents.

### Missing Behavior 7: Direct Participant-Initiated Agreement Creation or Editing

Priority:
API ergonomics gap

Expected business goal:
Allow a participant to start or edit their own agreement details.

Why it is unsupported:
Participants can approve and read accessible agreements, but no participant creation or edit function exists.

Existing functions considered:
- `approve agreement as participant`: only sets participant approval.
- `retrieve agreement by id`: reads accessible agreement.
- `update agreement`: role resolution allows only roles whose `kanEndreAvtale` is true; participant implementation does not support editing.

Missing capability:
Participant create/update endpoints and participant-specific validation rules.

Proof that function composition is insufficient:
Participant approval cannot create an agreement or fill required content. Advisor or employer setup is always needed.

Evidence from existing functions/source:
- Creation functions are advisor, employer, and mentor-specific creation paths; none use participant as creator.

Business impact:
Participant self-service is limited to approval, not agreement initiation or correction.

### Missing Behavior 8: Search and Retrieve Soft-Deleted or Feilregistrert Agreements Through Controlled Admin API

Priority:
API ergonomics gap

Expected business goal:
Allow authorized administrators to inspect hidden, soft-deleted, or feilregistrert agreements without direct database access.

Why it is unsupported:
Ordinary list filters out `feilregistrert`, and access checks reject `slettemerket`.

Existing functions considered:
- `list accessible agreements`: filters out `feilregistrert` and inaccessible/soft-deleted agreements.
- `retrieve agreement by id`: requires role access and rejects soft-deleted agreements.
- `soft-delete agreement`: hides but does not provide an admin read path.

Missing capability:
Privileged include-hidden search/detail endpoints.

Proof that function composition is insufficient:
Once `slettemerket=true`, normal `harTilgang` returns false. No alternate documented function bypasses this for administrative inspection.

Evidence from existing functions/source:
- `Avtalepart.harTilgang` immediately returns false for `slettemerket`.
- `hentAlleAvtalerMedLesetilgang` filters `!avtale.isFeilregistrert()`.

Business impact:
Support teams need database access to inspect hidden records, weakening operational traceability.

## Cross-Behavior Observations

- Role is central: the same endpoint can mean participant approval, employer approval, advisor approval, or mentor signing depending on `innlogget-part`.
- Optimistic locking is enforced on full update and approval endpoints using `If-Unmodified-Since`, but many targeted post-approval mutations do not require that header.
- Full agreement editing is blocked after any party approval; targeted post-approval amendment functions create new content versions instead.
- Subsidy-period generation is event-driven by content changes, takeover, migration adjustment, and admin repair. It is not continuously recomputed.
- Several operations mutate state while returning no body, making clients dependent on later retrieval.
- Soft deletion and feilregistrering remove agreements from ordinary visibility but are not reversible through API functions.
- Validation is strong in the `Avtale` aggregate for approval order, measure type, dates, age limits, and subsidy-period constraints, but weaker around some controller-level access checks and null handling.
- OpenAPI and source disagree in coverage: source contains `/utvikler-admin/tilskuddsperioder/...` Kafka-message endpoints not documented in `full-behavior.md`; these were excluded from supported behavior analysis.
- Some dry-run endpoints call the same mutation logic on an in-memory aggregate and return the result without explicit save; they are useful previews but do not always validate every persisted-request field exactly, such as shortening reason text.
- Developer-admin one-agreement event replay lacks the AD-group access check present on all-agreement replay functions.

## Coverage Summary

Supported domain areas:
Agreement creation by advisor/employer, mentor agreement creation, agreement content completion, party sharing, approval workflows, proxy approvals, approval revocation, decision-maker subsidy-period handling, post-approval amendments, shortening/extension, takeover, unit refresh, cost-center correction, annulment, soft deletion, agreement search/retrieval, version listing, PDF/account/reference lookups, notifications, journaling, and developer-admin repair/replay workflows.

Partially supported domain areas:
Subsidy-period management is available through aggregate current-period decisions and developer-admin repairs, but not as a first-class business resource. Notifications can be read and acknowledged but not created or safely searched. Integration replay can be triggered but not verified through read APIs.

Unsupported domain areas:
Hard delete/restore, idempotent after-registration setting, participant-initiated creation/editing, controlled admin inspection of hidden agreements, first-class subsidy-period retrieval/update, event/DVH audit reads, and robust structured outcomes for bulk repair operations.
