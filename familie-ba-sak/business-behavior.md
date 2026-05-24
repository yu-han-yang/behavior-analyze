# Domain-Level Behavior Analysis

## Domain Summary
This service manages Norwegian child benefit case handling. The central business resources are `fagsak` cases, `behandling` treatments, person and child bases, condition assessments, EEA competence and differential-payment records, payment-result corrections, decision periods, decision and manual letters, complaint integrations, external tasks, journalposts, statistics exports, rate-change jobs, reconciliation jobs, and administrative repair tools.

The domain model is process-heavy: most important state changes happen inside a case-scoped treatment workflow, where generated ids from earlier functions are reused by later case, treatment, decision-period, document, or child-resource functions.

## Available Function Inventory

### Case and repayment case functions
- `create case` (`POST /api/fagsaker`): creates or finds a child-benefit case for a person/type/institution scope.
- `return existing case` (`POST /api/fagsaker`): idempotently returns an existing matching case.
- `retrieve full case` (`GET /api/fagsaker/{fagsakId}`): reads a full case with treatments and repayment treatments.
- `retrieve minimal case` (`GET /api/fagsaker/minimal/{fagsakId}`): reads compact case information.
- `find minimal case for person` (`POST /api/fagsaker/hent-fagsak-paa-person`): finds one case by person and type.
- `find all minimal cases for person` (`POST /api/fagsaker/hent-fagsaker-paa-person`): finds cases by person and type set.
- `search case participants` (`POST /api/fagsaker/sok`): searches participant records.
- `search cases where person participates` (`POST /api/fagsaker/sok/fagsaker-hvor-person-er-deltaker`): finds cases where a person is applicant or ordinary-benefit recipient.
- `search cases with ongoing benefit for person` (`POST /api/fagsaker/sok/fagsaker-hvor-person-mottar-lopende-ytelse`): finds cases with ongoing ordinary or extended benefit.
- `resolve case participants` (`POST /api/fagsaker/sok/fagsakdeltagere`): resolves applicant and child participants.
- `check open repayment case` (`GET /api/fagsaker/{fagsakId}/har-apen-tilbakekreving`): checks open repayment treatment state.
- `create repayment treatment` (`GET /api/fagsaker/{fagsakId}/opprett-tilbakekreving`): starts a manual repayment treatment.

### Treatment lifecycle functions
- `create treatment` (`POST /api/behandlinger`): creates a treatment and initializes person basis and active decision.
- `restart active early treatment` (`POST /api/behandlinger`): resets/reuses an early active treatment.
- `queue treatment from birth event` (`PUT /api/behandlinger`): queues automated birth-event processing.
- `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`): reads extended treatment state.
- `change treatment theme` (`PUT /api/behandlinger/{behandlingId}/behandlingstema`): changes treatment category/subcategory.
- `find invalid after-payment periods` (`GET /api/behandlinger/{behandlingId}/personer-med-ugyldig-etterbetalingsperiode`): detects invalid after-payment periods.
- `get change date` (`GET /api/behandlinger/{behandlingId}/endringstidspunkt`): reads calculated or overridden change date.
- `register application` (`POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`): stores application data and advances step flow.
- `validate conditions` (`POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering`): validates condition assessment.
- `validate treatment result` (`GET /api/behandlinger/{behandlingId}/steg/behandlingsresultat/valider`): pre-validates result derivation.
- `derive treatment result` (`POST /api/behandlinger/{behandlingId}/steg/behandlingsresultat`): derives and stores treatment result and decision periods.
- `assess repayment` (`POST /api/behandlinger/{behandlingId}/steg/tilbakekreving`): stores repayment assessment.
- `send to decision maker` (`POST /api/behandlinger/{behandlingId}/steg/send-til-beslutter`): sends treatment to decision maker.
- `decide treatment` (`POST /api/behandlinger/{behandlingId}/steg/iverksett-vedtak`): records decision and implements approved/underapproved result.
- `dismiss treatment` (`PUT /api/behandlinger/{behandlingId}/steg/henlegg`): dismisses treatment.
- `register institution and guardian` (`POST /api/behandlinger/{behandlingId}/steg/registrer-institusjon-og-verge`): stores institution/guardian data.
- `add child to basis` (`POST /api/behandlinger/{behandlingId}/legg-til-barn`): adds a child to treatment basis and resets later steps.

### Waiting, person, and condition functions
- `set treatment on wait` (`POST /api/sett-på-vent/{behandlingId}`): puts treatment on wait.
- `update wait` (`PUT /api/sett-på-vent/{behandlingId}`): changes active wait metadata.
- `resume treatment` (`PUT /api/sett-på-vent/{behandlingId}/fortsettbehandling`): resumes a waiting treatment.
- `retrieve full person information` (`GET /api/person`): gets detailed person information.
- `retrieve simple person information` (`GET /api/person/enkel`): gets simple person information.
- `retrieve person address` (`GET /api/person/adresse`): gets person address.
- `refresh register information` (`GET /api/person/oppdater-registeropplysninger/{behandlingId}`): refreshes treatment register basis.
- `register manual death` (`POST /api/person/registrer-manuell-dodsfall/{behandlingId}`): manually records death information.
- `add condition` (`POST /api/vilkaarsvurdering/{behandlingId}`): adds a condition result.
- `update condition` (`PUT /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}`): updates condition result.
- `delete condition period` (`DELETE /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}`): deletes one condition period.
- `delete condition` (`DELETE /api/vilkaarsvurdering/{behandlingId}/vilkaar`): deletes a condition by payload.
- `update other assessment` (`PUT /api/vilkaarsvurdering/{behandlingId}/annenvurdering/{annenVurderingId}`): updates another-assessment record.
- `list condition explanation texts` (`GET /api/vilkaarsvurdering/vilkaarsbegrunnelser`): lists condition explanation metadata.

### EEA, differential calculation, and payment adjustment functions
- `upsert competence interval` (`PUT /api/kompetanse/{behandlingId}`): creates/replaces/splits/merges competence periods.
- `delete competence interval` (`DELETE /api/kompetanse/{behandlingId}/{kompetanseId}`): deletes a competence interval.
- `update foreign period amount` (`PUT /api/differanseberegning/utenlandskperidebeløp/{behandlingId}`): updates existing foreign amount period.
- `delete foreign period amount` (`DELETE /api/differanseberegning/utenlandskperidebeløp/{behandlingId}/{utenlandskPeriodebeløpId}`): deletes foreign amount period.
- `update currency rate from ECB` (`PUT /api/differanseberegning/valutakurs/{behandlingId}`): updates currency rate using ECB.
- `set historical ISK rate manually` (`PUT /api/differanseberegning/valutakurs/{behandlingId}`): sets historical ISK rate manually.
- `delete currency rate` (`DELETE /api/differanseberegning/valutakurs/{behandlingId}/{valutakursId}`): deletes currency rate.
- `create changed payment share` (`POST /api/endretutbetalingandel/{behandlingId}`): creates empty changed-payment share and recalculates.
- `update changed payment share` (`PUT /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`): updates changed-payment share and recalculates.
- `delete changed payment share` (`DELETE /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`): removes changed-payment share and recalculates.
- `reset treatment to treatment result` (`POST /api/endretutbetalingandel/{behandlingId}/tilbakestill`): resets treatment flow to result step.
- `retrieve EØS timelines` (`GET /api/tidslinjer/{behandlingId}`): reads EEA timelines.
- `add EØS refund period` (`POST /api/refusjon-eøs/behandlinger/{behandlingId}`): adds EEA refund period.
- `list EØS refund periods` (`GET /api/refusjon-eøs/behandlinger/{behandlingId}`): lists EEA refund periods.
- `update EØS refund period` (`PUT /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`): updates EEA refund period.
- `delete EØS refund period` (`DELETE /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`): deletes EEA refund period.
- `add overpaid currency period` (`POST /api/feilutbetalt-valuta/behandling/{behandlingId}`): adds overpaid-currency period.
- `list overpaid currency periods` (`GET /api/feilutbetalt-valuta/behandling/{behandlingId}`): lists overpaid-currency periods.
- `update overpaid currency period` (`PUT /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`): updates overpaid-currency period.
- `delete overpaid currency period` (`DELETE /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`): deletes overpaid-currency period.

### Correction, letters, decision periods, and log functions
- `create corrected decision metadata` (`POST /api/korrigertvedtak/behandling/{behandlingId}`): creates active corrected-decision metadata.
- `deactivate corrected decision metadata` (`PATCH /api/korrigertvedtak/behandling/{behandlingId}`): deactivates active corrected-decision metadata.
- `create corrected after-payment metadata` (`POST /api/korrigertetterbetaling/behandling/{behandlingId}`): creates active corrected after-payment metadata.
- `list corrected after-payment metadata` (`GET /api/korrigertetterbetaling/behandling/{behandlingId}`): lists corrected after-payment records.
- `deactivate corrected after-payment metadata` (`PATCH /api/korrigertetterbetaling/behandling/{behandlingId}`): deactivates corrected after-payment metadata.
- `add small child supplement correction` (`POST /api/småbarnstilleggkorrigering/behandling/{behandlingId}`): adds monthly supplement correction.
- `remove small child supplement correction` (`DELETE /api/småbarnstilleggkorrigering/behandling/{behandlingId}`): removes monthly supplement correction.
- `preview repayment warning letter` (`POST /api/tilbakekreving/{behandlingId}/forhandsvis-varselbrev`): previews repayment warning letter PDF.
- `generate decision letter` (`POST /api/dokument/vedtaksbrev/{vedtakId}`): generates and stores decision letter.
- `retrieve decision letter` (`GET /api/dokument/vedtaksbrev/{vedtakId}`): retrieves stored decision letter.
- `preview treatment letter` (`POST /api/dokument/forhaandsvis-brev/{behandlingId}`): previews treatment-scoped manual letter.
- `send treatment letter` (`POST /api/dokument/send-brev/{behandlingId}`): sends treatment-scoped manual letter.
- `preview case letter` (`POST /api/dokument/fagsak/{fagsakId}/forhaandsvis-brev`): previews case-scoped manual letter.
- `send case letter` (`POST /api/dokument/fagsak/{fagsakId}/send-brev`): sends case-scoped manual letter.
- `add letter recipient` (`POST /api/brevmottaker/{behandlingId}`): adds manual letter recipient.
- `list letter recipients` (`GET /api/brevmottaker/{behandlingId}`): lists manual letter recipients.
- `delete letter recipient` (`DELETE /api/brevmottaker/{behandlingId}/{mottakerId}`): deletes manual letter recipient.
- `update standard explanations` (`PUT /api/vedtaksperioder/standardbegrunnelser/{vedtaksperiodeId}`): updates standard period explanations.
- `update decision free texts` (`PUT /api/vedtaksperioder/fritekster/{vedtaksperiodeId}`): updates period free-text explanations.
- `regenerate decision periods` (`PUT /api/vedtaksperioder/endringstidspunkt`): overrides change date and regenerates periods.
- `generate letter explanation texts` (`GET /api/vedtaksperioder/brevbegrunnelser/{vedtaksperiodeId}`): generates possible letter explanations.
- `list decision periods` (`GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder`): lists decision periods.
- `retrieve treatment log` (`GET /api/logg/{behandlingId}`): reads treatment log.

### External, complaint, task, journal, event, job, statistics, and admin functions
- `retrieve BISYS extended benefit` (`POST /api/bisys/hent-utvidet-barnetrygd`)
- `retrieve pension child benefit` (`POST /api/ekstern/pensjon/hent-barnetrygd`)
- `order pension yearly export` (`GET /api/ekstern/pensjon/bestill-personer-med-barnetrygd/{år}`)
- `list tax persons` (`GET /api/skatt/personer`)
- `list tax persons test` (`GET /api/skatt/personer/test`)
- `retrieve tax periods` (`POST /api/skatt/perioder`)
- `retrieve tax periods test` (`POST /api/skatt/perioder/test`)
- `retrieve Infotrygd cases` (`POST /api/infotrygd/hent-infotrygdsaker-for-soker`)
- `retrieve Infotrygd benefits` (`POST /api/infotrygd/hent-infotrygdstonader-for-soker`)
- `check ongoing Infotrygd case` (`POST /api/infotrygd/har-lopende-sak`)
- `retrieve collaborator by organization` (`GET /api/samhandler/orgnr/{orgnr}`)
- `search collaborator` (`POST /api/samhandler/navn`)
- `create complaint treatment` (`POST /api/fagsaker/{fagsakId}/opprett-klagebehandling`)
- `list complaint treatments` (`GET /api/fagsaker/{fagsakId}/hent-klagebehandlinger`)
- `check complaint revision creation` (`GET /api/klage/fagsaker/{fagsakId}/kan-opprette-revurdering-klage`)
- `create complaint revision` (`POST /api/klage/fagsaker/{fagsakId}/opprett-revurdering-klage/`)
- `retrieve complaint decisions` (`GET /api/klage/fagsaker/{fagsakId}/vedtak`)
- `search tasks`, `assign task`, `reset task assignment`, `retrieve journaling task data`, `complete task`, `complete task and link journalpost`, `retrieve open treatment deadlines`, `clear application task ownership`
- `retrieve journalpost`, `list user journalposts`, `retrieve journal document resource`, `retrieve journal document PDF`, `journal journalpost`
- `retrieve feature toggles`, `check person access`, `handle identity event`, `handle transitional benefit event`
- `trigger rate change for case`, `trigger rate change for cases`, `run synchronous rate change`, `check rate change eligibility`, `trigger rate change from identities`, `queue long-deadline dismissals`, `find cases without latest rate`
- `run consistency dry run`, `run consistency reconciliation`
- `retrieve internal statistics`, `retrieve application statistics`, `retrieve treatment statistics`, `retrieve case statistics`, `register statistics sent`, `retrieve benefit statistics decisions`, `queue unsent benefit statistics`, `queue benefit statistics manually`, `resend migration statistics`
- `finish admin task list`, `restart small child supplement job`, `send payment orders administratively`, `run rate change without validation`, `identify payments over 100 percent`, `find payment-order issues`, `check incorrect cessation dates`, `resend corrected payment orders`, `resend corrected payment order version`, `populate support dates for treatment`, `populate support dates in bulk`, `find cases to close`, `update case ongoing status`, `find migration duplicates with Infotrygd`, `find migration duplicates`, `fill condition dates in preprod`
- `trigger test autobrev scheduler`, `trigger test rate change`, `trigger test transitional event`, `trigger test birth event`, `trigger internal reconciliation tasks`, `trigger wait-deadline resume task`, `retrieve test simulation`, `retrieve explanation test text`, `retrieve decision-period test text`, `redirect to treatment UI`

## Supported Business Behaviors

### Behavior 1: Establish and inspect a child-benefit case

Business goal:
Create or locate a case for a person and inspect it at full, minimal, or person-search level.

Domain context:
A `fagsak` is the parent resource for treatments, complaints, letters, repayment checks, and statistics.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with body `personIdent=P1`, `fagsakType=NORMAL` to create the case and capture `RestMinimalFagsak.id=fagsakId`.
2. To prove idempotent creation, use function `return existing case` (`POST /api/fagsaker`) again with the same `personIdent=P1`, `fagsakType=NORMAL`; it returns the same domain case instead of a duplicate.
3. For institution cases, use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`, `fagsakType=INSTITUSJON`, `institusjon.orgNummer=ORG1`, and optional `institusjon.tssEksternId=TSS1`.

Optional verification workflow:
1. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) with `fagsakId` from step 1.
2. Use function `retrieve minimal case` (`GET /api/fagsaker/minimal/{fagsakId}`) with the same `fagsakId`.
3. Use function `find minimal case for person` (`POST /api/fagsaker/hent-fagsak-paa-person`) with body `personIdent=P1`, `fagsakType=NORMAL`.
4. Use function `find all minimal cases for person` (`POST /api/fagsaker/hent-fagsaker-paa-person`) with body `personIdent=P1`, `fagsakTyper=[NORMAL]`.

Existing-state shortcuts:
- Step 1 can be skipped if a matching case already exists for the same person identity, case type, and institution organization number.
- Direct database setup can replace step 1 only if actor, person-ident, case type, status, and institution relationship match.
- The `fagsakId` reused by read and child workflows must belong to that same case scope.

Parameter and value bindings:
- `personIdent=P1`, `fagsakType`, and `institusjon.orgNummer` define uniqueness.
- The generated `fagsakId` is reused in all case-scoped functions and later treatment creation.
- For institution cases, `orgNummer` must be reused to retrieve the same case.

Business result:
A case exists for the person/type/scope. Repeated creation returns the existing case; it does not create duplicates for the same uniqueness key.

Constraints and invariants:
- Institution cases require `institusjon.orgNummer`.
- Case creation calls person identity lookup and may create person/actor state.
- Case creation publishes case statistics and creates a shadow case in implementation.

Failure and exceptional cases:
- Failing function: `create case`
  - Failure condition: `fagsakType=INSTITUSJON` without `institusjon.orgNummer`.
  - Why it fails: `FagsakService.hentEllerOpprettFagsak` throws a functional error.
  - Violated prerequisite or constraint: institution-scoped case key is incomplete.
- Failing function: `find minimal case for person`
  - Failure condition: no matching case exists for `personIdent` and `fagsakType`.
  - Why it fails: service returns a failure resource.
  - Violated prerequisite or constraint: the case was not created or directly seeded.

Implementation notes:
Implementation behavior matches the OpenAPI shape for case creation and reads. Creation returns HTTP 201 even when it returns an existing case.

### Behavior 2: Search case participation and ongoing benefit relationships

Business goal:
Find where a person participates in cases or receives ongoing child benefit.

Domain context:
Caseworkers need to understand whether a person is applicant, child, participant, or ongoing benefit recipient before starting or routing work.

Starting point:
No prior service state for generic search; complete case-backed workflow when verifying a known case.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`, `fagsakType=NORMAL` to establish an applicant case and capture `fagsakId`.
2. Use function `search case participants` (`POST /api/fagsaker/sok`) with body `personIdent=P1` to search participant records.
3. Use function `resolve case participants` (`POST /api/fagsaker/sok/fagsakdeltagere`) with body `personIdent=P1`, `barnasIdenter=[C1]` to resolve applicant and child participants.
4. If treatment/payment state exists, use function `search cases where person participates` (`POST /api/fagsaker/sok/fagsaker-hvor-person-er-deltaker`) with `personIdent=P1`.
5. If ongoing benefit exists, use function `search cases with ongoing benefit for person` (`POST /api/fagsaker/sok/fagsaker-hvor-person-mottar-lopende-ytelse`) with `personIdent=P1`.

Optional verification workflow:
1. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) with the case id returned by `create case`.

Existing-state shortcuts:
- Step 1 can be skipped when relevant case, participant, and ongoing benefit state already exists.
- Direct database setup may create case, person, child, treatment, and benefit-share rows, but ongoing-benefit searches still require current benefit state of the expected type.

Parameter and value bindings:
- `personIdent=P1` is reused as applicant/search identity.
- `barnasIdenter=[C1]` is consumed by participant resolution and must resolve to child actors.
- Ongoing-benefit searches bind the returned `fagsakId` to benefit shares for ordinary or extended child benefit.

Business result:
The service returns participant rows and/or case ids showing where the person is involved or has ongoing benefit.

Constraints and invariants:
- Access is checked for person-scoped searches except `search case participants`, which has lighter explicit validation in the controller.
- Search results depend on persisted treatments and benefit shares; case existence alone may not produce ongoing-benefit matches.

Failure and exceptional cases:
- Failing function: `resolve case participants`
  - Failure condition: child identity cannot be resolved or participant lookup fails.
  - Why it fails: controller catches lookup errors and returns a failure resource, using the exception status for functional errors.
  - Violated prerequisite or constraint: applicant/child identities must be resolvable.
- Failing function: `create case`
  - Failure condition: invalid institution case input when used as setup.
  - Why it fails: missing institution organization number.
  - Violated prerequisite or constraint: case scope must be complete.

Implementation notes:
Participant search and ongoing-benefit search are not simple case lookup; they are relationship queries over person, case, and benefit state.

### Behavior 3: Create and manage an ordinary treatment workflow

Business goal:
Create a treatment under a case, register application data, validate conditions, derive result, assess repayment, send to decision maker, and decide/implement.

Domain context:
A treatment is the core case-processing workflow that moves from application registration to decision.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`, `fagsakType=NORMAL`; capture `fagsakId`.
2. Use function `create treatment` (`POST /api/behandlinger`) with body `fagsakId={fagsakId}`, `søkersIdent=P1`, `behandlingType=FØRSTEGANGSBEHANDLING`, `behandlingÅrsak=SØKNAD`, `søknadMottattDato=2026-05-24`; capture `behandlingId` and active `vedtak.id=vedtakId`.
3. Use function `register application` (`POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`) with `{behandlingId}` and a valid `RestRegistrerSøknad` body.
4. Use function `validate conditions` (`POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering`) with the same `behandlingId`.
5. Use function `validate treatment result` (`GET /api/behandlinger/{behandlingId}/steg/behandlingsresultat/valider`) with the same `behandlingId`.
6. Use function `derive treatment result` (`POST /api/behandlinger/{behandlingId}/steg/behandlingsresultat`) with the same `behandlingId`.
7. Use function `assess repayment` (`POST /api/behandlinger/{behandlingId}/steg/tilbakekreving`) with body `RestTilbakekreving` or `null`, depending on repayment need.
8. Use function `send to decision maker` (`POST /api/behandlinger/{behandlingId}/steg/send-til-beslutter`) with query `behandlendeEnhet=ENHET1`.
9. Use function `decide treatment` (`POST /api/behandlinger/{behandlingId}/steg/iverksett-vedtak`) with body `beslutning=GODKJENT`, optional `begrunnelse`, and `kontrollerteSider=[...]`.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) to inspect current step/status.
2. Use function `retrieve full case` (`GET /api/fagsaker/{fagsakId}`) to verify the treatment is attached to the case.
3. Use function `retrieve treatment log` (`GET /api/logg/{behandlingId}`) to inspect workflow log entries.

Existing-state shortcuts:
- Step 1 can be skipped if the case exists for `P1`.
- Step 2 can be skipped only if an equivalent active treatment already exists under the same `fagsakId`; otherwise later step functions lack the `behandlingId`.
- Individual step functions can be skipped only if equivalent persisted step state already exists, but step ordering and current status must still match.
- Direct database setup must include treatment, person basis, condition assessment, treatment result, decision, and step-state rows consistently.

Parameter and value bindings:
- `fagsakId` from `create case` is consumed by `create treatment`.
- `søkersIdent=P1` must match the case actor.
- `behandlingId` from `create treatment` scopes all step functions.
- `vedtakId` from treatment response later scopes decision-letter behavior.
- `behandlendeEnhet` is set when sending to decision maker and influences decision workflow routing.

Business result:
A treatment is created, evaluated, sent to a decision maker, and decided. On approval, implementation advances toward external/payment effects according to service step logic.

Constraints and invariants:
- `søknadMottattDato` is required for first-time and application-based revision treatments.
- Manual migration requires `nyMigreringsdato`.
- Step execution is ordered; earlier steps cannot generally be rerun after later steps.
- Treatment must not be on wait or closed when executing active workflow steps.
- Decision requires decision-maker role except special manual migration logic.

Failure and exceptional cases:
- Failing function: `create treatment`
  - Failure condition: `fagsakId` does not exist.
  - Why it fails: treatment service throws when case cannot be found.
  - Violated prerequisite or constraint: treatment must be scoped to an existing case.
- Failing function: `create treatment`
  - Failure condition: missing `søkersIdent`, missing `søknadMottattDato` for application treatment, or missing `nyMigreringsdato` for manual migration.
  - Why it fails: `NyBehandling` validates required fields in its initializer.
  - Violated prerequisite or constraint: treatment creation fields are incomplete.
- Failing function: `register application`
  - Failure condition: treatment has advanced beyond application registration.
  - Why it fails: `StegService` rejects executing a caseworker step earlier than the current step.
  - Violated prerequisite or constraint: step ordering.
- Failing function: `validate conditions`
  - Failure condition: treatment is on wait.
  - Why it fails: `StegService.validerBehandlingIkkeSattPåVent` rejects step execution.
  - Violated prerequisite or constraint: active treatment must not have status `SATT_PÅ_VENT`.

Implementation notes:
The implementation prioritizes step-state validation over raw endpoint availability. OpenAPI exposes endpoints, but successful use depends on treatment step state, editability, roles, and wait status.

### Behavior 4: Restart or dismiss a treatment instead of completing it

Business goal:
Reuse an early active treatment or dismiss an active treatment that should not continue.

Domain context:
Case handling sometimes starts incorrectly, duplicates early work, or must be stopped before external sending.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`, `fagsakType=NORMAL`; capture `fagsakId`.
2. Use function `create treatment` (`POST /api/behandlinger`) with `fagsakId={fagsakId}`, `søkersIdent=P1`, valid treatment type/reason/date; capture `behandlingId`.
3. To restart early work, use function `restart active early treatment` (`POST /api/behandlinger`) again with the same `fagsakId` and compatible body while the active treatment is before `BESLUTTE_VEDTAK`.
4. To stop work, use function `dismiss treatment` (`PUT /api/behandlinger/{behandlingId}/steg/henlegg`) with body `årsak=FEILAKTIG_OPPRETTET` or another allowed value and `begrunnelse=...`.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) to inspect status/result.
2. Use function `retrieve treatment log` (`GET /api/logg/{behandlingId}`) to inspect dismissal or reset logging.

Existing-state shortcuts:
- Existing case and early active treatment can replace steps 1 and 2.
- Direct database setup must preserve active status and step before decision for restart.
- Dismissal can use an existing treatment, but the treatment must not already have been sent to external services.

Parameter and value bindings:
- The same `fagsakId` is reused between the first and second `POST /api/behandlinger`.
- `behandlingId` from created treatment is reused for dismissal.
- Dismissal `årsak` maps to a treatment result.

Business result:
The treatment is either reset to early processing or dismissed with a dismissal result and possible dismissal letter side effect.

Constraints and invariants:
- Restart only applies to active treatments before decision step.
- Dismissal is blocked after external sending.
- Technical-maintenance dismissal and technical-treatment dismissal are feature-toggle constrained.

Failure and exceptional cases:
- Failing function: `restart active early treatment`
  - Failure condition: active treatment is at or after `BESLUTTE_VEDTAK`.
  - Why it fails: implementation throws a functional error for active unfinished decision-stage treatment.
  - Violated prerequisite or constraint: restart is limited to early treatment.
- Failing function: `dismiss treatment`
  - Failure condition: treatment already sent to external services or disallowed dismissal reason.
  - Why it fails: controller validates external-send state and dismissal type before invoking step.
  - Violated prerequisite or constraint: dismissal must happen before external sending and with allowed reason.
- Failing function: `create treatment`
  - Failure condition: missing required creation fields.
  - Why it fails: request object validation.
  - Violated prerequisite or constraint: treatment cannot be initialized.

Implementation notes:
The same endpoint as treatment creation implements both new treatment creation and early active treatment restart; business effect depends on existing active treatment state.

### Behavior 5: Handle birth-event-driven automated treatment creation

Business goal:
Queue automated processing of a birth event.

Domain context:
Birth events can trigger child-benefit processing asynchronously rather than through immediate manual treatment creation.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `queue treatment from birth event` (`PUT /api/behandlinger`) with body `morsIdent=M1`, `barnasIdenter=[C1,C2]`.

Optional verification workflow:
1. Use function `search tasks` (`POST /api/oppgave/hent-oppgaver`) with a task-search body matching birth-event task criteria, if the external task system exposes it.

Existing-state shortcuts:
- None for the core behavior; queuing the task is the behavior.
- Direct database setup could insert an equivalent task, but that bypasses the API behavior and task-creation validation.

Parameter and value bindings:
- `morsIdent` and `barnasIdenter` become the payload of the background task.
- There is no immediate `behandlingId`; later treatment creation is asynchronous.

Business result:
A task is persisted to process the birth event.

Constraints and invariants:
- Caller must have system-level access.
- The endpoint creates a task, not an immediate treatment.

Failure and exceptional cases:
- Failing function: `queue treatment from birth event`
  - Failure condition: invalid event payload or task repository failure.
  - Why it fails: controller catches task creation errors and returns illegal-state resource.
  - Violated prerequisite or constraint: task payload/storage must be valid.
- Failing function: `search tasks`
  - Failure condition: upstream task search fails during optional verification.
  - Why it fails: task controller returns illegal-state response on retrieval failure.
  - Violated prerequisite or constraint: external task system must be reachable.

Implementation notes:
This is event-driven. The API does not provide a synchronous “birth event to completed treatment” workflow.

### Behavior 6: Put treatment on wait and resume it

Business goal:
Pause an active treatment until a deadline, update the wait reason/deadline, then resume processing.

Domain context:
Caseworkers need to suspend treatments while waiting for information or deadlines.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`, `fagsakType=NORMAL`; capture `fagsakId`.
2. Use function `create treatment` (`POST /api/behandlinger`) with valid body and `fagsakId={fagsakId}`; capture `behandlingId`.
3. Use function `set treatment on wait` (`POST /api/sett-på-vent/{behandlingId}`) with body `frist=2026-06-24`, `årsak=AVVENTER_DOKUMENTASJON` or another valid reason.
4. To change waiting metadata, use function `update wait` (`PUT /api/sett-på-vent/{behandlingId}`) with changed `frist` or changed `årsak`.
5. Use function `resume treatment` (`PUT /api/sett-på-vent/{behandlingId}/fortsettbehandling`) with the same `behandlingId`.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) to confirm status changes.
2. Use function `retrieve treatment log` (`GET /api/logg/{behandlingId}`) to inspect wait/resume events.

Existing-state shortcuts:
- Steps 1 and 2 can be skipped when an editable active treatment already exists.
- Step 3 can be skipped only for the update/resume variants if an active wait record already exists.
- Direct database setup must set both active wait record and treatment status `SATT_PÅ_VENT`.

Parameter and value bindings:
- `behandlingId` from treatment creation scopes all wait functions.
- `frist` must be current/future; updated `frist`/`årsak` must differ from the active wait values.
- Resume consumes the same treatment-scoped active wait state.

Business result:
The treatment is paused with active wait metadata, can have that metadata changed, and can be resumed to investigation state.

Constraints and invariants:
- Treatment must be active and status `UTREDES` to be put on wait.
- A treatment cannot have duplicate active wait records.
- Past deadlines are rejected.
- Step execution is blocked while treatment is on wait.

Failure and exceptional cases:
- Failing function: `set treatment on wait`
  - Failure condition: treatment is already on wait.
  - Why it fails: validation rejects existing active wait record.
  - Violated prerequisite or constraint: only one active wait state.
- Failing function: `set treatment on wait`
  - Failure condition: `frist` is before current date.
  - Why it fails: deadline validation rejects past dates.
  - Violated prerequisite or constraint: wait deadline must be current/future.
- Failing function: `update wait`
  - Failure condition: treatment is not currently on wait.
  - Why it fails: service requires active wait state.
  - Violated prerequisite or constraint: update requires prior `set treatment on wait`.
- Failing function: `resume treatment`
  - Failure condition: no active wait state.
  - Why it fails: service requires active wait record and `SATT_PÅ_VENT`.
  - Violated prerequisite or constraint: resume requires waiting treatment.

Implementation notes:
Waiting also extends open task deadlines. Wait state is not just metadata; it blocks later step execution.

### Behavior 7: Maintain person basis, child basis, death, and condition assessments

Business goal:
Refresh person data, add a child, register manual death, and manage condition assessments that determine entitlement.

Domain context:
The entitlement calculation depends on person basis, child relationships, death data, and condition periods.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`, `fagsakType=NORMAL`; capture `fagsakId`.
2. Use function `create treatment` (`POST /api/behandlinger`) with `fagsakId={fagsakId}`, `søkersIdent=P1`, and valid type/reason/date; capture `behandlingId` and condition ids from the treatment view.
3. Use function `refresh register information` (`GET /api/person/oppdater-registeropplysninger/{behandlingId}`) with the same `behandlingId` to refresh person basis.
4. Use function `add child to basis` (`POST /api/behandlinger/{behandlingId}/legg-til-barn`) with body `barnIdent=C1`.
5. Use function `register manual death` (`POST /api/person/registrer-manuell-dodsfall/{behandlingId}`) with body `personIdent=C1`, `dødsfallDato=2026-05-01`, `begrunnelse=...`.
6. Use function `add condition` (`POST /api/vilkaarsvurdering/{behandlingId}`) with body `RestNyttVilkår`.
7. Use function `update condition` (`PUT /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}`) with `vilkaarId` from treatment condition results and body `RestPersonResultat`.
8. Use function `update other assessment` (`PUT /api/vilkaarsvurdering/{behandlingId}/annenvurdering/{annenVurderingId}`) with an existing `annenVurderingId` from the treatment response and body `RestAnnenVurdering`.

Optional verification workflow:
1. Use function `retrieve full person information` (`GET /api/person`) with relevant person query/header values required by the endpoint.
2. Use function `retrieve simple person information` (`GET /api/person/enkel`) for a compact person view.
3. Use function `retrieve person address` (`GET /api/person/adresse`) for address.
4. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) to inspect person basis and condition results.
5. Use function `list condition explanation texts` (`GET /api/vilkaarsvurdering/vilkaarsbegrunnelser`) to inspect available explanation metadata.

Existing-state shortcuts:
- Existing editable treatment can replace case/treatment setup.
- Child/person/condition records can be directly seeded, but `vilkaarId` and `annenVurderingId` must belong to the same `behandlingId`.
- Person lookup functions can be used without service-created case state if the caller supplies valid person input and has access.

Parameter and value bindings:
- `fagsakId` binds treatment to case.
- `behandlingId` scopes person-basis refresh, child addition, manual death, and condition changes.
- `barnIdent=C1` becomes child actor/person state consumed by condition and benefit logic.
- `vilkaarId` and `annenVurderingId` must be generated by or attached to the same treatment.

Business result:
The treatment basis is enriched or corrected, condition state changes are persisted, and later treatment steps are reset where condition changes affect downstream results.

Constraints and invariants:
- Treatment must be editable.
- Manual death requires person to belong to treatment context.
- Condition mutation resets later steps.
- Deleting condition periods or whole conditions requires existing condition state.

Failure and exceptional cases:
- Failing function: `add child to basis`
  - Failure condition: treatment is non-editable or child lookup fails.
  - Why it fails: access/editability and person lookup validation.
  - Violated prerequisite or constraint: child must be resolvable and treatment editable.
- Failing function: `update condition`
  - Failure condition: `vilkaarId` does not belong to treatment or treatment cannot be edited.
  - Why it fails: condition/editability validation.
  - Violated prerequisite or constraint: condition id must be scoped to `behandlingId`.
- Failing function: `register manual death`
  - Failure condition: `personIdent` is not in treatment context.
  - Why it fails: person/treatment validation fails.
  - Violated prerequisite or constraint: death registration is treatment-basis scoped.
- Failing function: `create treatment`
  - Failure condition: required treatment setup fields missing.
  - Why it fails: `NyBehandling` validation.
  - Violated prerequisite or constraint: treatment must exist before treatment-basis mutations.

Implementation notes:
Condition and person-basis changes are not isolated edits; they reset downstream treatment steps and can force recalculation.

### Behavior 8: Configure EEA competence and differential calculation inputs

Business goal:
Maintain EEA competence intervals, foreign period amounts, and currency rates used for differential child-benefit calculation.

Domain context:
EEA cases require time-bounded competence and foreign amount/currency data for children.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`, `fagsakType=NORMAL`; capture `fagsakId`.
2. Use function `create treatment` (`POST /api/behandlinger`) with valid treatment body and `barnasIdenter=[C1]`; capture `behandlingId`.
3. Use function `upsert competence interval` (`PUT /api/kompetanse/{behandlingId}`) with body `fom=2026-01`, `tom=2026-12`, `barnIdenter=[C1]`, and required activity/result fields.
4. To remove competence, use function `delete competence interval` (`DELETE /api/kompetanse/{behandlingId}/{kompetanseId}`) with `kompetanseId` from treatment response or later treatment read.
5. To update a foreign amount, use function `update foreign period amount` (`PUT /api/differanseberegning/utenlandskperidebeløp/{behandlingId}`) with body containing an existing `id`, `barnIdenter=[C1]`, period, non-negative `beløp`, and amount fields.
6. Use function `update currency rate from ECB` (`PUT /api/differanseberegning/valutakurs/{behandlingId}`) with existing `id`, `barnIdenter=[C1]`, `valutakode=EUR`, `valutakursdato=2026-05-01`.
7. For historical Icelandic currency, use function `set historical ISK rate manually` (`PUT /api/differanseberegning/valutakurs/{behandlingId}`) with `valutakode=ISK`, `valutakursdato` before `2018-02-01`, and manual `kurs`.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) to inspect competence, foreign amounts, and currency rates.
2. Use function `retrieve EØS timelines` (`GET /api/tidslinjer/{behandlingId}`) to inspect derived EEA timelines.

Existing-state shortcuts:
- Existing editable EEA treatment can replace steps 1 and 2.
- `delete competence interval`, `delete foreign period amount`, and `delete currency rate` can start from directly seeded child records, but ids must belong to the target treatment for shared EEA schema services.
- `update foreign period amount` specifically needs an existing record because the controller loads it by `id` to preserve `utbetalingsland`.

Parameter and value bindings:
- `behandlingId` scopes all EEA data.
- `barnIdenter=[C1]` bind competence, foreign amount, and currency-rate rows to child actors.
- `kompetanseId`, `utenlandskPeriodebeløpId`, and `valutakursId` are generated child-resource ids reused for delete/update.
- ECB update consumes `valutakode` and `valutakursdato`; historical ISK before `2018-02-01` consumes caller-supplied rate.

Business result:
EEA calculation inputs exist, are updated, or are removed for the treatment and selected children.

Constraints and invariants:
- Competence `fom` is required, `fom` cannot be after `tom`, and `barnIdenter` cannot be empty.
- Competence activity combinations are validated against whether the other parent is covered by Norwegian legislation.
- Foreign amount `beløp` cannot be negative.
- Treatment must be editable.
- Shared EEA schema delete operations validate treatment ownership.

Failure and exceptional cases:
- Failing function: `upsert competence interval`
  - Failure condition: missing `fom`, empty `barnIdenter`, or `fom > tom`.
  - Why it fails: `KompetanseController.validerOppdatering` rejects invalid period/child list.
  - Violated prerequisite or constraint: competence period and child scope are invalid.
- Failing function: `upsert competence interval`
  - Failure condition: incompatible activity values.
  - Why it fails: controller validates activity values against parent-law coverage.
  - Violated prerequisite or constraint: EEA activity semantics are inconsistent.
- Failing function: `update foreign period amount`
  - Failure condition: body `id` does not identify an existing foreign amount.
  - Why it fails: controller calls repository `getById` before update.
  - Violated prerequisite or constraint: function updates existing amount only.
- Failing function: `delete competence interval`
  - Failure condition: `kompetanseId` belongs to another treatment.
  - Why it fails: shared schema service rejects mismatched treatment id.
  - Violated prerequisite or constraint: child record must be owned by `behandlingId`.

Implementation notes:
OpenAPI uses `PUT` for foreign amount and currency-rate changes, but source behavior differs by resource. Competence can upsert/split/merge; foreign amount update requires an existing id.

### Behavior 9: Manage changed payment shares and after-payment validity

Business goal:
Create, update, delete, and recalculate changed-payment shares that modify awarded benefit.

Domain context:
Changed payment shares alter calculation/payment outcomes and reset treatment progress.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`, `fagsakType=NORMAL`; capture `fagsakId`.
2. Use function `create treatment` (`POST /api/behandlinger`) with valid body and `fagsakId={fagsakId}`; capture `behandlingId`.
3. Use function `create changed payment share` (`POST /api/endretutbetalingandel/{behandlingId}`) with the same `behandlingId`; capture `endretUtbetalingAndelId` from returned treatment view.
4. Use function `update changed payment share` (`PUT /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`) with body `RestEndretUtbetalingAndel`.
5. To remove it, use function `delete changed payment share` (`DELETE /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`).
6. Use function `reset treatment to treatment result` (`POST /api/endretutbetalingandel/{behandlingId}/tilbakestill`) when a manual reset to result step is needed.

Optional verification workflow:
1. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) to inspect changed-payment shares and recalculated benefit.
2. Use function `find invalid after-payment periods` (`GET /api/behandlinger/{behandlingId}/personer-med-ugyldig-etterbetalingsperiode`) to inspect invalid after-payment periods.

Existing-state shortcuts:
- Existing editable treatment can replace setup.
- Existing changed-payment share can replace creation for update/delete if `endretUtbetalingAndelId` belongs to `behandlingId`.
- Direct database setup must include benefit-award state consistent enough for recalculation.

Parameter and value bindings:
- `behandlingId` scopes all changed-payment operations.
- `endretUtbetalingAndelId` is generated by creation and reused for update/delete.
- Update body values change calculation state and trigger treatment reset to result.

Business result:
Changed-payment share state is created, changed, or removed; awarded benefit is recalculated and the treatment is reset to treatment-result step.

Constraints and invariants:
- Treatment must be editable.
- Calculation update and step reset are side effects of create/update/delete.
- Invalid after-payment detection is read-only.

Failure and exceptional cases:
- Failing function: `update changed payment share`
  - Failure condition: non-editable treatment or unknown share id.
  - Why it fails: controller validates editability and service updates by id.
  - Violated prerequisite or constraint: share must exist in editable treatment.
- Failing function: `delete changed payment share`
  - Failure condition: unknown or mismatched `endretUtbetalingAndelId`.
  - Why it fails: service cannot remove a share that is not present.
  - Violated prerequisite or constraint: generated id must be reused correctly.
- Failing function: `create treatment`
  - Failure condition: setup case/treatment missing.
  - Why it fails: changed-payment functions are treatment-scoped.
  - Violated prerequisite or constraint: payment adjustment requires treatment.

Implementation notes:
These functions are not mere CRUD. They update awarded benefit and reset downstream treatment state.

### Behavior 10: Manage EEA refund and overpaid-currency periods

Business goal:
Record, list, update, and delete EEA refund periods and overpaid foreign-currency periods in a treatment.

Domain context:
Refund and overpaid-currency records document recovery/offset details associated with EEA or currency situations.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`, `fagsakType=NORMAL`; capture `fagsakId`.
2. Use function `create treatment` (`POST /api/behandlinger`) with valid body and `fagsakId={fagsakId}`; capture `behandlingId`.
3. Use function `add EØS refund period` (`POST /api/refusjon-eøs/behandlinger/{behandlingId}`) with body `fom=2026-01`, `tom=2026-03`, `refusjonsbeløp=1000`, `land=SE`, `refusjonAvklart=true`; capture `id`.
4. Use function `update EØS refund period` (`PUT /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`) with the same path ids and updated refund body.
5. Use function `add overpaid currency period` (`POST /api/feilutbetalt-valuta/behandling/{behandlingId}`) with body `fom=2026-01`, `tom=2026-03`, `feilutbetaltBeløp=500`, optional `erPerMåned=true`; capture `id`.
6. Use function `update overpaid currency period` (`PUT /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`) with updated body.
7. To clean up, use function `delete EØS refund period` and `delete overpaid currency period` with the captured ids.

Optional verification workflow:
1. Use function `list EØS refund periods` (`GET /api/refusjon-eøs/behandlinger/{behandlingId}`).
2. Use function `list overpaid currency periods` (`GET /api/feilutbetalt-valuta/behandling/{behandlingId}`).
3. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) to inspect treatment-level returned state.

Existing-state shortcuts:
- Existing editable treatment can replace setup.
- Existing refund/overpaid rows can replace add steps for update/delete, but ids must refer to intended records.
- Direct database setup can insert rows, but ownership must be manually checked because implementation does not fully enforce it for all operations.

Parameter and value bindings:
- `behandlingId` from treatment setup scopes add/list endpoints.
- Generated period `id` from add/list responses is reused by update/delete.
- For overpaid currency, omitted `erPerMåned` is replaced by the feature-toggle value.

Business result:
Refund and overpaid-currency periods exist, are changed, listed, or deleted for the treatment.

Constraints and invariants:
- Treatment must be editable for mutation.
- `erPerMåned` may be derived from feature toggle if omitted.
- Path structure implies child-resource ownership by `behandlingId`.

Failure and exceptional cases:
- Failing function: `update EØS refund period`
  - Failure condition: period id does not exist.
  - Why it fails: service lookup by id throws.
  - Violated prerequisite or constraint: update requires existing refund id.
- Failing function: `delete overpaid currency period`
  - Failure condition: period id does not exist.
  - Why it fails: service logs the period before deleting and throws on missing id.
  - Violated prerequisite or constraint: delete requires existing overpaid-currency id.
- Failing function: `create treatment`
  - Failure condition: treatment setup is missing or non-editable.
  - Why it fails: mutation is treatment-scoped and editability-protected.
  - Violated prerequisite or constraint: mutable treatment required.

Implementation notes:
Important discrepancy: the URL nests refund/overpaid records under `{behandlingId}`, but `RefusjonEøsService` update/delete and `FeilutbetaltValutaService` update/delete primarily load/delete by child `id`; they do not consistently verify that the child id belongs to the supplied `behandlingId`. This weakens ownership scoping implied by OpenAPI.

### Behavior 11: Record corrections for decision, after-payment, and small-child supplement

Business goal:
Store correction metadata for decisions, after-payment, and monthly small-child supplement state.

Domain context:
Caseworkers need to mark corrected decisions and adjust special supplement months without rebuilding the whole case externally.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`; capture `fagsakId`.
2. Use function `create treatment` (`POST /api/behandlinger`) with valid body; capture `behandlingId`.
3. Use function `create corrected decision metadata` (`POST /api/korrigertvedtak/behandling/{behandlingId}`) with body `KorrigerVedtakRequest`.
4. To remove active corrected-decision marker, use function `deactivate corrected decision metadata` (`PATCH /api/korrigertvedtak/behandling/{behandlingId}`).
5. Use function `create corrected after-payment metadata` (`POST /api/korrigertetterbetaling/behandling/{behandlingId}`) with body `KorrigertEtterbetalingRequest`.
6. To remove active corrected after-payment marker, use function `deactivate corrected after-payment metadata` (`PATCH /api/korrigertetterbetaling/behandling/{behandlingId}`).
7. Use function `add small child supplement correction` (`POST /api/småbarnstilleggkorrigering/behandling/{behandlingId}`) with body `årMåned=2026-05`.
8. Use function `remove small child supplement correction` (`DELETE /api/småbarnstilleggkorrigering/behandling/{behandlingId}`) with body `årMåned=2026-05`.

Optional verification workflow:
1. Use function `list corrected after-payment metadata` (`GET /api/korrigertetterbetaling/behandling/{behandlingId}`).
2. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`) to inspect treatment correction state where included.

Existing-state shortcuts:
- Existing editable treatment can replace setup.
- Existing active correction metadata can replace create step for deactivation.
- Direct database setup must link correction records to the same `behandlingId`.

Parameter and value bindings:
- `behandlingId` scopes all correction functions.
- `årMåned` used to add small-child supplement correction must be reused to remove the same month.
- Create corrected decision/after-payment deactivates any previous active correction of the same type.

Business result:
Correction metadata is active or inactive as requested. Small-child supplement correction exists for a month or is removed.

Constraints and invariants:
- Treatment must be editable.
- Corrected decision and corrected after-payment allow one active record at a time by deactivating previous active records.
- Deactivation with no active correction may return null/no explicit failure in implementation.

Failure and exceptional cases:
- Failing function: `create corrected decision metadata`
  - Failure condition: treatment is non-editable.
  - Why it fails: controller/service validates editability.
  - Violated prerequisite or constraint: correction metadata requires editable treatment.
- Failing function: `remove small child supplement correction`
  - Failure condition: treatment/domain validation fails for requested month.
  - Why it fails: supplement correction service validates treatment and month semantics.
  - Violated prerequisite or constraint: correction must be valid for treatment/month.
- Failing function: `create treatment`
  - Failure condition: missing required setup fields.
  - Why it fails: request validation.
  - Violated prerequisite or constraint: correction requires treatment.

Implementation notes:
There is no `GET` for corrected decision metadata comparable to corrected after-payment listing.

### Behavior 12: Generate, retrieve, preview, and send letters

Business goal:
Generate decision letters, preview/send manual letters, manage manual recipients, and preview repayment warnings.

Domain context:
Case handling produces formal communication tied to a treatment, decision, case, recipient, or repayment assessment.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`; capture `fagsakId`.
2. Use function `create treatment` (`POST /api/behandlinger`) with valid body; capture `behandlingId` and active `vedtakId`.
3. Use function `add letter recipient` (`POST /api/brevmottaker/{behandlingId}`) with body `RestBrevmottaker`; capture `mottakerId`.
4. Use function `preview treatment letter` (`POST /api/dokument/forhaandsvis-brev/{behandlingId}`) with body `ManueltBrevRequest`.
5. Use function `send treatment letter` (`POST /api/dokument/send-brev/{behandlingId}`) with the same treatment and compatible `ManueltBrevRequest`.
6. Use function `preview case letter` (`POST /api/dokument/fagsak/{fagsakId}/forhaandsvis-brev`) with body `ManueltBrevRequest`.
7. Use function `send case letter` (`POST /api/dokument/fagsak/{fagsakId}/send-brev`) with body `ManueltBrevRequest`.
8. Use function `generate decision letter` (`POST /api/dokument/vedtaksbrev/{vedtakId}`) with `vedtakId` from treatment.
9. Use function `preview repayment warning letter` (`POST /api/tilbakekreving/{behandlingId}/forhandsvis-varselbrev`) with body `fritekst=...` when repayment warning preview is needed.
10. Use function `delete letter recipient` (`DELETE /api/brevmottaker/{behandlingId}/{mottakerId}`) to remove manual recipient.

Optional verification workflow:
1. Use function `list letter recipients` (`GET /api/brevmottaker/{behandlingId}`).
2. Use function `retrieve decision letter` (`GET /api/dokument/vedtaksbrev/{vedtakId}`).
3. Use function `retrieve treatment` (`GET /api/behandlinger/{behandlingId}`).

Existing-state shortcuts:
- Existing case/treatment/decision can replace setup if `vedtakId` belongs to that treatment.
- Recipient setup can be skipped for letters that use default/generated recipient data.
- Direct database setup can create decision PDF state for retrieval, but `vedtakId` must remain treatment-scoped.

Parameter and value bindings:
- `fagsakId` scopes case letters.
- `behandlingId` scopes treatment letters, recipients, and repayment warning preview.
- `vedtakId` scopes decision letter generation/retrieval.
- `mottakerId` from add/list recipient is reused for delete.

Business result:
Letters are generated as PDFs, sent where applicable, stored for decisions, and manual recipients are added/listed/deleted.

Constraints and invariants:
- Treatment-letter recipient data is enriched from treatment person basis and work distribution.
- Decision-letter generation stores PDF bytes on the decision.
- Manual recipients require editable treatment; strict confidentiality rules can reject manual recipients.

Failure and exceptional cases:
- Failing function: `delete letter recipient`
  - Failure condition: `mottakerId` does not exist.
  - Why it fails: `BrevmottakerService.fjernBrevmottaker` throws.
  - Violated prerequisite or constraint: delete requires generated recipient id.
- Failing function: `retrieve decision letter`
  - Failure condition: decision letter has not been generated/stored.
  - Why it fails: document service cannot retrieve missing PDF.
  - Violated prerequisite or constraint: retrieval requires prior `generate decision letter`.
- Failing function: `preview repayment warning letter`
  - Failure condition: treatment missing or letter generation fails.
  - Why it fails: endpoint is treatment-scoped and delegates generation.
  - Violated prerequisite or constraint: repayment warning preview requires valid treatment.

Implementation notes:
Potential access discrepancy: case-scoped manual letter preview/send endpoints verify role but do not explicitly validate access to the supplied `fagsakId` in the controller, unlike treatment-scoped letter endpoints.

### Behavior 13: Maintain decision periods and letter explanations

Business goal:
List decision periods, update standard/free-text explanations, override change date, and generate letter explanation candidates.

Domain context:
Decision periods structure what the decision letter explains and when entitlement changes apply.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`; capture `fagsakId`.
2. Use function `create treatment` (`POST /api/behandlinger`) with valid body; capture `behandlingId`.
3. Use function `register application`, `validate conditions`, and `derive treatment result` in order to generate decision-period state for that treatment.
4. Use function `list decision periods` (`GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder`) and capture `vedtaksperiodeId`.
5. Use function `update standard explanations` (`PUT /api/vedtaksperioder/standardbegrunnelser/{vedtaksperiodeId}`) with explanation enum names.
6. Use function `update decision free texts` (`PUT /api/vedtaksperioder/fritekster/{vedtaksperiodeId}`) with body `RestPutVedtaksperiodeMedFritekster`.
7. Use function `regenerate decision periods` (`PUT /api/vedtaksperioder/endringstidspunkt`) with body containing `behandlingId` and overridden change-date values.

Optional verification workflow:
1. Use function `generate letter explanation texts` (`GET /api/vedtaksperioder/brevbegrunnelser/{vedtaksperiodeId}`).
2. Use function `list decision periods` again.
3. Use function `get change date` (`GET /api/behandlinger/{behandlingId}/endringstidspunkt`).

Existing-state shortcuts:
- If treatment already has generated decision periods, steps 1-3 can be skipped.
- Direct database setup can create periods, but `vedtaksperiodeId` must belong to the treatment and active decision state.

Parameter and value bindings:
- `behandlingId` scopes decision-period generation and listing.
- `vedtaksperiodeId` from listing is reused for explanation/free-text updates.
- Override change date consumes treatment-level id and regenerates period records.

Business result:
Decision periods have selected standard explanations and free texts; overridden change date can regenerate period structure.

Constraints and invariants:
- Explanation enum names must be convertible to known decision explanation enums.
- Decision periods must already exist before period-level updates.
- Regeneration can replace prior period state.

Failure and exceptional cases:
- Failing function: `update standard explanations`
  - Failure condition: invalid explanation enum or missing period id.
  - Why it fails: conversion or lookup fails.
  - Violated prerequisite or constraint: explanations must be known and period must exist.
- Failing function: `list decision periods`
  - Failure condition: treatment has not advanced far enough to create periods.
  - Why it fails: no generated decision periods are available.
  - Violated prerequisite or constraint: treatment result must be derived.
- Failing function: `derive treatment result`
  - Failure condition: prior application/condition state missing.
  - Why it fails: treatment-result step preconditions fail.
  - Violated prerequisite or constraint: workflow step order.

Implementation notes:
Decision-period work is tightly coupled to treatment-result derivation; it is not independent CRUD over arbitrary periods.

### Behavior 14: Exchange data with external benefit, tax, Infotrygd, collaborator, task, and journal systems

Business goal:
Read or send domain data through integrations used by other agencies and caseworker work queues.

Domain context:
The service is not isolated. It provides child-benefit data to BISYS, pension, tax, complaint, and statistics systems, and consumes task/journal/collaborator systems.

Starting point:
No prior service state for pure external lookup; complete workflow when linking to local case/treatment.

Required execution workflow:
1. Use function `retrieve BISYS extended benefit` (`POST /api/bisys/hent-utvidet-barnetrygd`) with the external request body for a person/period.
2. Use function `retrieve pension child benefit` (`POST /api/ekstern/pensjon/hent-barnetrygd`) with pension request body.
3. Use function `order pension yearly export` (`GET /api/ekstern/pensjon/bestill-personer-med-barnetrygd/{år}`) with `år=2026`.
4. Use function `list tax persons` (`GET /api/skatt/personer`) or `retrieve tax periods` (`POST /api/skatt/perioder`) with tax request body.
5. Use function `retrieve Infotrygd cases`, `retrieve Infotrygd benefits`, or `check ongoing Infotrygd case` with body identifying the applicant.
6. Use function `retrieve collaborator by organization` (`GET /api/samhandler/orgnr/{orgnr}`) with `orgnr=ORG1`, or `search collaborator` (`POST /api/samhandler/navn`) with name/location search body.
7. Use function `search tasks` (`POST /api/oppgave/hent-oppgaver`) with `RestFinnOppgaveRequest`; capture `oppgaveId`.
8. Use function `assign task` (`POST /api/oppgave/{oppgaveId}/fordel`) with query `saksbehandler=NAVIDENT`.
9. Use function `retrieve journalpost` (`GET /api/journalpost/{journalpostId}/hent`) and capture `dokumentInfoId`.
10. Use function `journal journalpost` (`POST /api/journalpost/{journalpostId}/journalfør/{oppgaveId}`) with query `journalfoerendeEnhet=ENHET1` and body `RestJournalføring` where every document has `dokumentTittel`.

Optional verification workflow:
1. Use function `retrieve journaling task data` (`GET /api/oppgave/{oppgaveId}`).
2. Use function `retrieve journal document resource` (`GET /api/journalpost/{journalpostId}/hent/{dokumentInfoId}`).
3. Use function `retrieve journal document PDF` (`GET /api/journalpost/{journalpostId}/dokument/{dokumentInfoId}`).
4. Use function `complete task` (`GET /api/oppgave/{oppgaveId}/ferdigstill`) or `complete task and link journalpost` (`POST /api/oppgave/{oppgaveId}/ferdigstillOgKnyttjournalpost`) when finishing the work queue item.

Existing-state shortcuts:
- External lookup functions can start with external system state only.
- Task assignment/completion can skip `search tasks` if `oppgaveId` is already known.
- Journal document retrieval can skip `retrieve journalpost` if `journalpostId` and `dokumentInfoId` are already known and valid.
- Direct database setup is insufficient for upstream-only state unless external systems also contain the records.

Parameter and value bindings:
- `oppgaveId` from task search is reused for assign/reset/complete/journal workflows.
- `journalpostId` and `dokumentInfoId` from journalpost metadata are reused for document retrieval.
- `journalpostId`, `oppgaveId`, and `journalfoerendeEnhet` bind journaling to a concrete task and unit.

Business result:
External benefit/tax/Infotrygd/collaborator data is retrieved; tasks can be assigned, completed, or linked; journalposts can be journaled and documents retrieved.

Constraints and invariants:
- Upstream systems control existence and failure.
- Journaling requires non-empty document titles.
- Task assignment requires `saksbehandler` query value.
- Task reset/complete are id-based and depend on external task state.

Failure and exceptional cases:
- Failing function: `search tasks`
  - Failure condition: invalid search body or upstream task failure.
  - Why it fails: controller catches errors and returns illegal-state response.
  - Violated prerequisite or constraint: task search must be valid/upstream available.
- Failing function: `journal journalpost`
  - Failure condition: at least one document lacks `dokumentTittel`.
  - Why it fails: controller throws functional error for null/blank titles.
  - Violated prerequisite or constraint: all journaled documents need titles.
- Failing function: `retrieve journal document PDF`
  - Failure condition: `dokumentInfoId` is not part of `journalpostId`.
  - Why it fails: upstream journal lookup fails.
  - Violated prerequisite or constraint: document id must be from the journalpost.

Implementation notes:
Many functions are integration facades. Their business behavior depends as much on upstream state as local state.

### Behavior 15: Handle complaints and complaint-triggered revisions

Business goal:
Create/list complaint treatments and allow the complaint system to check and create complaint revisions.

Domain context:
Complaint handling connects the child-benefit case to complaint workflows and fagsystem decision history.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`; capture `fagsakId`.
2. Use function `create complaint treatment` (`POST /api/fagsaker/{fagsakId}/opprett-klagebehandling`) with body `OpprettKlageDto`.
3. Use function `check complaint revision creation` (`GET /api/klage/fagsaker/{fagsakId}/kan-opprette-revurdering-klage`) from the authorized complaint client.
4. Use function `create complaint revision` (`POST /api/klage/fagsaker/{fagsakId}/opprett-revurdering-klage/`) from the same authorized complaint-client context.

Optional verification workflow:
1. Use function `list complaint treatments` (`GET /api/fagsaker/{fagsakId}/hent-klagebehandlinger`).
2. Use function `retrieve complaint decisions` (`GET /api/klage/fagsaker/{fagsakId}/vedtak`).

Existing-state shortcuts:
- Existing case can replace setup.
- Direct database setup can create complaint treatment state for listing, but complaint revision creation still requires authorized caller context and service validation.
- `check complaint revision creation` is a recommended precheck, but implementation of `create complaint revision` performs its own validation.

Parameter and value bindings:
- The same `fagsakId` scopes complaint treatment, revision precheck, revision creation, and decision retrieval.
- Complaint-client identity is a caller-context binding, not a path/body value.

Business result:
Complaint treatment state exists, complaint revision may be created, and complaint system can retrieve case decisions.

Constraints and invariants:
- Complaint revision endpoints require calls from the complaint client.
- Ordinary case access applies unless machine-to-machine decision retrieval bypass applies.
- Revision creation must be valid for the case’s current treatment state.

Failure and exceptional cases:
- Failing function: `check complaint revision creation`
  - Failure condition: caller is not recognized as complaint client.
  - Why it fails: controller checks `SikkerhetContext.kallKommerFraKlage()`.
  - Violated prerequisite or constraint: authorized complaint-client context required.
- Failing function: `create complaint revision`
  - Failure condition: caller not complaint client or service validation rejects revision.
  - Why it fails: controller and service validate caller and revision eligibility.
  - Violated prerequisite or constraint: complaint revision must be allowed for this case.
- Failing function: `create complaint treatment`
  - Failure condition: `fagsakId` missing or caller lacks access.
  - Why it fails: controller validates access to case and action.
  - Violated prerequisite or constraint: complaint treatment is case-scoped.

Implementation notes:
Complaint precheck and creation are exposed under `/api/klage`, while complaint treatment CRUD is exposed under `/api/fagsaker`.

### Behavior 16: Run rate change, reconciliation, statistics, event, and administrative operations

Business goal:
Trigger operational jobs, exports, statistics messages, event ingestion, and administrative repair routines.

Domain context:
The service includes operational APIs for scheduled or manual maintenance beyond normal caseworker flow.

Starting point:
No prior service state for job triggers; case/treatment state required for targeted jobs.

Required execution workflow:
1. Use function `create case` (`POST /api/fagsaker`) with `personIdent=P1`; capture `fagsakId` when targeting a case.
2. Use function `create treatment` (`POST /api/behandlinger`) with valid body; capture `behandlingId` when targeting a treatment.
3. Use function `check rate change eligibility` (`GET /api/satsendring/{fagsakId}/kan-kjore-satsendring`) with `fagsakId`.
4. Use one of `trigger rate change for case`, `trigger rate change for cases`, `run synchronous rate change`, or `trigger rate change from identities` with `fagsakId`, body case ids, or identity list.
5. Use function `run consistency dry run` (`POST /api/konsistensavstemming/dryrun`) before `run consistency reconciliation` (`POST /api/konsistensavstemming/run`) when validating reconciliation.
6. Use statistics reads/sends as needed: `retrieve treatment statistics`, `retrieve case statistics`, `register statistics sent`, `retrieve benefit statistics decisions`, `queue unsent benefit statistics`, `queue benefit statistics manually`, `resend migration statistics`.
7. Use event functions `handle identity event` and `handle transitional benefit event` with their event payloads.
8. Use admin repair functions such as `send payment orders administratively`, `run rate change without validation`, `find payment-order issues`, `resend corrected payment orders`, `populate support dates for treatment`, and `update case ongoing status` with their required ids/bodies.

Optional verification workflow:
1. Use function `retrieve internal statistics` (`GET /api/internstatistikk`).
2. Use function `retrieve application statistics` (`GET /api/internstatistikk/antallSoknader`).
3. Use function `find cases without latest rate` (`POST /api/satsendring/saker-uten-sats`).
4. Use function `find cases to close` (`GET /api/forvalter/finnFagsakerSomSkalAvsluttes`).
5. Use function `retrieve treatment` or `retrieve full case` for targeted case/treatment changes.

Existing-state shortcuts:
- Targeted operations can skip case/treatment setup when valid ids already exist.
- Bulk/admin jobs often operate from database state and can start from pre-existing production-like state.
- Direct database setup must preserve statuses, dates, rates, benefit shares, payment order versions, and migration markers expected by each job.

Parameter and value bindings:
- `fagsakId` binds rate-change and case-admin functions.
- `behandlingId` binds payment-order repair and support-date functions.
- `versjon` binds corrected payment-order resend to a treatment version.
- Event payload identities bind event ingestion to person/case lookup.
- `dryRun` path value controls whether migration-statistics resend mutates or only simulates.

Business result:
Jobs are queued or executed; statistics messages are read or marked sent; payment orders and support dates may be repaired; case ongoing status can be recalculated.

Constraints and invariants:
- Several admin endpoints catch per-item errors and continue, returning partial success details.
- Some functions are intentionally powerful and bypass normal validations, especially `run rate change without validation`.
- Consistency dry run does not perform the same persisted mutation as actual reconciliation.

Failure and exceptional cases:
- Failing function: `run synchronous rate change`
  - Failure condition: case is not eligible or has incompatible active treatment state.
  - Why it fails: rate-change service validation rejects the case.
  - Violated prerequisite or constraint: rate change requires eligible ongoing case state.
- Failing function: `resend corrected payment order version`
  - Failure condition: treatment/version cannot produce corrected payment order.
  - Why it fails: admin controller catches exception and returns it in `harFeil`.
  - Violated prerequisite or constraint: treatment payment state/version must exist.
- Failing function: `register statistics sent`
  - Failure condition: message id/type does not correspond to known unsent statistics state.
  - Why it fails: statistics service cannot mark unknown message correctly.
  - Violated prerequisite or constraint: message must exist in statistics state.

Implementation notes:
Operational APIs expose both safe checks and unsafe/repair actions. They should be treated as administrative capabilities rather than ordinary user workflows.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Safely enforce treatment ownership for refund and overpaid-currency child records

Priority:
Critical domain gap

Expected business goal:
A caller updating or deleting a refund or overpaid-currency period under `/behandlinger/{behandlingId}` should only affect records belonging to that treatment.

Why it is unsupported:
No function composition can make `update EØS refund period`, `delete EØS refund period`, `update overpaid currency period`, or `delete overpaid currency period` enforce ownership when the implementation loads by child id without consistently checking `behandlingId`.

Existing functions considered:
- `list EØS refund periods`: can list periods for one treatment, but cannot prevent a later update/delete using an id from another treatment.
- `update EØS refund period`: updates by id and ignores treatment ownership in service logic.
- `delete EØS refund period`: deletes by id.
- `list overpaid currency periods`: can list scoped periods, but cannot enforce mutation scope.
- `update overpaid currency period`: updates by id.
- `delete overpaid currency period`: deletes by id.

Missing capability:
A repository/service check that child period `id` belongs to path `behandlingId` before mutation.

Proof that function composition is insufficient:
A client can first obtain or guess a child id from another treatment. Chaining list/read functions does not change the mutation implementation. There is no preflight lock, ownership token, or scoped child lookup consumed by update/delete.

Evidence from existing functions/source:
`RefusjonEøsService.oppdaterRefusjonEøsPeriode` and `FeilutbetaltValutaService.oppdatertFeilutbetaltValutaPeriode` load by id. Delete functions also delete by id after logging. The OpenAPI path implies treatment scoping, but source does not fully enforce it.

Business impact:
A wrong child id can mutate or delete another treatment’s financial/currency documentation.

### Missing Behavior 2: Create foreign period amounts directly through the public API

Priority:
Critical domain gap

Expected business goal:
A caseworker should be able to add a new foreign period amount for a treatment as part of differential calculation.

Why it is unsupported:
The available function `update foreign period amount` requires an existing `id`; it loads the existing row to preserve `utbetalingsland`.

Existing functions considered:
- `update foreign period amount`: updates existing row only.
- `delete foreign period amount`: removes existing row.
- `retrieve EØS timelines`: reads derived timelines but does not create amounts.
- `retrieve treatment`: can expose existing amounts but cannot create one.

Missing capability:
A `POST` or true upsert behavior that can create initial foreign period amount state with required country, children, period, and amount.

Proof that function composition is insufficient:
Without an existing foreign-period row id, the controller cannot build the new domain object because it calls `getById(restUtenlandskPeriodebeløp.id)`. Delete-and-recreate is impossible because creation is the missing operation.

Evidence from existing functions/source:
`UtenlandskPeriodebeløpController.oppdaterUtenlandskPeriodebeløp` loads existing period by request `id`.

Business impact:
Differential calculation setup is incomplete through API-only workflows unless initial foreign period amount rows are created by some other internal process or database setup.

### Missing Behavior 3: Retrieve corrected decision metadata

Priority:
Important robustness gap

Expected business goal:
A caseworker should inspect active and historical corrected-decision metadata.

Why it is unsupported:
There are functions to create and deactivate corrected-decision metadata, but no corresponding list/retrieve function.

Existing functions considered:
- `create corrected decision metadata`: creates active metadata.
- `deactivate corrected decision metadata`: deactivates active metadata.
- `retrieve treatment`: may expose some treatment state but is not a dedicated history/list function.
- `list corrected after-payment metadata`: exists for after-payment, showing the asymmetry.

Missing capability:
`GET /api/korrigertvedtak/behandling/{behandlingId}` or equivalent.

Proof that function composition is insufficient:
Once metadata is created, no exact function can query the correction history or distinguish no correction from hidden active correction unless the treatment projection happens to expose it.

Evidence from existing functions/source:
Corrected after-payment has list support; corrected decision does not.

Business impact:
Auditability and operator confidence are weaker for corrected decisions than for corrected after-payment.

### Missing Behavior 4: Update an existing manual letter recipient

Priority:
Important robustness gap

Expected business goal:
A caseworker should correct a recipient address/name/type without deleting and recreating the recipient.

Why it is unsupported:
The API supports add, list, and delete only.

Existing functions considered:
- `add letter recipient`: creates a recipient.
- `list letter recipients`: reads recipients.
- `delete letter recipient`: removes recipient.

Missing capability:
A `PUT /api/brevmottaker/{behandlingId}/{mottakerId}` update endpoint.

Proof that function composition is insufficient:
Delete-and-recreate changes the recipient id and may lose audit continuity. It is not equivalent to correcting the existing recipient record.

Evidence from existing functions/source:
Only `POST`, `GET`, and `DELETE` recipient endpoints are available.

Business impact:
Recipient correction is more error-prone and less auditable.

### Missing Behavior 5: Public case closure or safe case deletion

Priority:
Important robustness gap

Expected business goal:
A caseworker or authorized admin should explicitly close a case or delete an erroneous empty case through a controlled domain action.

Why it is unsupported:
There is no ordinary case close/delete endpoint. Case status can only be recalculated in bulk by admin functions.

Existing functions considered:
- `update case ongoing status`: bulk recalculates case ongoing status.
- `find cases to close`: identifies cases to close.
- `retrieve full case`: read-only.
- `dismiss treatment`: dismisses a treatment, not the case.

Missing capability:
A scoped, audited case status transition endpoint or safe-delete endpoint with validation.

Proof that function composition is insufficient:
Dismissing all treatments does not necessarily mark the case closed. Bulk admin recalculation cannot close one requested case with explicit caller intent, validation, or result details.

Evidence from existing functions/source:
`FagsakService.oppdaterLøpendeStatusPåFagsaker` bulk-updates cases found by repository query; no public targeted close/delete exists.

Business impact:
Case lifecycle management depends on derived maintenance jobs, not explicit domain actions.

### Missing Behavior 6: Reevaluate all downstream calculations after every related data change

Priority:
Important robustness gap

Expected business goal:
Changing person basis, condition state, EEA records, refund data, corrections, or currency data should consistently recompute all dependent treatment results.

Why it is unsupported:
Some functions reset/recalculate, such as changed-payment share functions, while others only mutate records and return treatment view.

Existing functions considered:
- `update changed payment share`: recalculates awarded benefit and resets step.
- `upsert competence interval`: uses shared schema services with subscribers.
- `add EØS refund period`: stores refund period but does not visibly force full treatment-result derivation.
- `register manual death`: updates basis, but downstream recalculation depends on later workflow steps.

Missing capability:
A uniform “recalculate treatment from current basis” function or consistent event-driven recalculation contract.

Proof that function composition is insufficient:
A client can manually call later treatment steps, but only if treatment step state permits it. There is no single safe recomputation function that handles every affected dependency and step reset consistently.

Evidence from existing functions/source:
Different controllers call different services and side effects; some explicitly reset to treatment result while others only save rows.

Business impact:
Treatment state can become stale or require caseworker knowledge of hidden recalculation rules.

### Missing Behavior 7: Strong access validation for case-scoped manual letters

Priority:
Important robustness gap

Expected business goal:
Sending or previewing a case letter should validate that the caller has access to the specific `fagsakId`.

Why it is unsupported:
The case-letter controller methods verify role but do not explicitly call case access validation for `fagsakId`.

Existing functions considered:
- `preview case letter`: previews by `fagsakId`.
- `send case letter`: sends by `fagsakId`.
- `retrieve full case`: performs access validation, but calling it first does not bind an authorization token to the later letter function.

Missing capability:
Explicit `tilgangService.validerTilgangTilFagsak` or equivalent in case-letter endpoints.

Proof that function composition is insufficient:
A caller can call the letter endpoint directly. A prior successful case read is not required or consumed by send/preview.

Evidence from existing functions/source:
Treatment-letter endpoints validate treatment access; case-letter endpoints in `DokumentController` only verify role before generating/sending.

Business impact:
Case letters risk being previewed or sent by a caller with role but without case-specific access.

### Missing Behavior 8: Stable API workflow for full first-time treatment completion without external/manual data assumptions

Priority:
API ergonomics gap

Expected business goal:
A client should be able to create a case, create a treatment, provide all necessary basis/condition/payment data, and complete a decision fully through documented API calls.

Why it is unsupported:
Many required data structures are generated from register, application, condition, and calculation internals. Some necessary records, such as initial foreign period amounts, lack direct creation functions.

Existing functions considered:
- `create treatment`: initializes treatment but depends on person basis and required dates.
- `register application`: stores application data.
- `validate conditions`: requires valid condition assessment.
- `derive treatment result`: requires valid prior state.
- `update foreign period amount`: cannot create initial amount rows.

Missing capability:
A complete documented setup API for all basis and calculation records required by every treatment variant.

Proof that function composition is insufficient:
Chaining available functions can complete common flows only when generated basis and editable condition state already align. It cannot create every needed calculation input from empty service state.

Evidence from existing functions/source:
Treatment step functions depend on internal generated state and step validation; several domain records are update-only or derived.

Business impact:
Automation clients and tests must rely on database fixtures, internal generation, or partial workflows.

## Cross-Behavior Observations
- The service is case/treatment scoped, and generated ids are central. `fagsakId`, `behandlingId`, `vedtakId`, `vedtaksperiodeId`, and child-resource ids must be captured and reused exactly.
- Workflow behavior is stateful. Endpoint availability does not imply a function can run; treatment step, status, editability, role, and wait state decide success.
- Several functions are not pure CRUD. Changed-payment shares recalculate awarded benefit and reset treatment state. Decision-period regeneration can replace existing period structure. Case creation can publish statistics and create shadow case state.
- Validation is uneven. Competence and wait deadlines are strongly validated; EEA refund and overpaid-currency child ownership is weakly validated despite scoped paths.
- External integration functions depend on upstream systems and cannot be made complete by local API composition alone.
- Administrative endpoints include partial-success behavior and bypass/repair operations. They should be modeled separately from ordinary caseworker workflows.
- OpenAPI path structure generally matches controllers, but implementation may be narrower or weaker than the path implies, especially for update-only foreign amount behavior and treatment-scoped child-resource ownership.

## Coverage Summary
Supported domain areas:
case creation and lookup; treatment creation and step workflow; wait/resume; person basis refresh and condition edits; EEA competence, currency, refund, and overpaid-currency records; changed-payment shares; correction metadata; decision periods and letters; complaint integration; task and journal integration; external reporting; statistics; rate changes; reconciliation; administrative repair.

Partially supported domain areas:
foreign period amount lifecycle, corrected-decision inspection, manual recipient correction, consistent recalculation after all mutations, explicit case lifecycle transitions, and access validation for case-scoped letters.

Unsupported domain areas:
safe scoped ownership enforcement for some nested child records, direct API-only creation of all differential-calculation inputs, explicit targeted case close/delete, and a single complete public workflow that can establish every treatment prerequisite from empty state for every treatment variant.