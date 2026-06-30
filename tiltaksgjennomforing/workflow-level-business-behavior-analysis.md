# Workflow-Level Business Behavior Analysis

## Behavior Index

| Behavior | Brief description |
|---|---|
| [Behavior 1: Obtain a Role-Scoped Agreement Worklist](#behavior-1) | A paginated, role-filtered set of non-deleted, non-error-registered agreements. |
| [Behavior 2: Save and Reuse a Filtered Agreement Search](#behavior-2) | The saved filter is replayed and its usage metadata is updated. |
| [Behavior 3: Enter a Work-Training Agreement](#behavior-3) | An entered `ARBEIDSTRENING` agreement with an effective approved content version. |
| [Behavior 4: Enter a Mentor Agreement](#behavior-4) | Entered mentor agreement with confidentiality, participant, employer, and advisor approvals. |
| [Behavior 5: Enter a Subsidy-Backed Agreement After Decision-Maker Approval](#behavior-5) | First current subsidy period is `GODKJENT`; agreement records decision-maker approval and is entered. |
| [Behavior 6: Reject a Pending Subsidy Period](#behavior-6) | The current active period is `AVSLÅTT` with causes, explanation, decision-maker, and timestamp; the agreement remains unentered. |
| [Behavior 7: Return a Rejected Subsidy Period for Re-decision](#behavior-7) | Each active rejected period is inactive and an active `UBEHANDLET` replacement exists. |
| [Behavior 8: Assign an Employer-Created Agreement to an Advisor](#behavior-8) | Agreement has `veilederNavIdent`, refreshed follow-up data, recalculated wage data/periods, and an assignment event. |
| [Behavior 9: Reopen and Revise a Partially Approved Draft](#behavior-9) | Revised draft is persisted with approvals cleared and awaits fresh approvals. |
| [Behavior 10: Authorize After-Registration](#behavior-10) | `godkjentForEtterregistrering=true` and authorization event recorded. |
| [Behavior 11: Remove After-Registration Authorization](#behavior-11) | Flag is false and removal event is recorded. |
| [Behavior 12: Shorten an Entered Agreement](#behavior-12) | A new `FORKORTE` content version with an earlier end date and adjusted subsidy periods. |
| [Behavior 13: Extend an Entered Agreement](#behavior-13) | New `FORLENGE` content version with later end date and adjusted/new periods. |
| [Behavior 14: Amend Subsidy Calculation](#behavior-14) | New `ENDRE_TILSKUDDSBEREGNING` version and recalculated unhandled periods. |
| [Behavior 15: Amend Agreement Contact Information](#behavior-15) | New `ENDRE_KONTAKTINFO` version. |
| [Behavior 16: Amend Job Description](#behavior-16) | New `ENDRE_STILLING` version. |
| [Behavior 17: Amend Follow-Up and Adaptation Text](#behavior-17) | New `ENDRE_OPPFØLGING_OG_TILRETTELEGGING` version. |
| [Behavior 18: Replace Work-Training Goals](#behavior-18) | New `ENDRE_MÅL` version with new goal UUIDs. |
| [Behavior 19: Replace Inclusion-Subsidy Expenses](#behavior-19) | New `ENDRE_INKLUDERINGSTILSKUDD` version with retained/requested and newly generated expense children. |
| [Behavior 20: Amend Mentor Details](#behavior-20) | New `ENDRE_OM_MENTOR` version. |
| [Behavior 21: Change Subsidy Cost Center Before Entry](#behavior-21) | Content and rebuilt periods carry the selected unit number/name. |
| [Behavior 22: Prepare an Arena-Cleanup Agreement at the Correct Migration Boundary](#behavior-22) | Cleanup draft has `ArenaRyddeAvtale.migreringsdato` and correctly rebuilt `BEHANDLET_I_ARENA`/`UBEHANDLET` periods. |
| [Behavior 23: Refresh Participant Follow-Up and NAV Units](#behavior-23) | Latest participant and routing fields are persisted. |
| [Behavior 24: Annul an Agreement](#behavior-24) | `ANNULLERT`, reason/timestamp stored, and eligible periods removed or annulled. |
| [Behavior 25: Soft-Delete an Agreement](#behavior-25) | `slettemerket=true`; ordinary reads/lists reject or omit it. |
| [Behavior 26: Obtain the Employer's Portal Agreement Portfolio](#behavior-26) | Employer-visible company list with sensitive termination reasons removed. |
| [Behavior 27: Obtain the Decision-Maker Work Queue](#behavior-27) | Paginated queue of decision-eligible agreements/periods. |
| [Behavior 28: Resolve a Valid Employer Organization](#behavior-28) | Valid `ArbeidsgiverOrganisasjon` details. |
| [Behavior 29: Obtain an Approved Agreement PDF](#behavior-29) | PDF bytes with inline agreement filename/content headers. |
| [Behavior 30: Review and Clear Agreement Notifications](#behavior-30) | Selected caller-owned notifications have `lest=true`. |
| [Behavior 31: Journal Approved Agreement Versions](#behavior-31) | Successfully journaled versions carry their external journal post ids and no longer appear in the work list. |
| [Behavior 32: Repair Missing Wage-Subsidy Totals on Selected Agreements](#behavior-32) | Derived wage-subsidy values are persisted for every successfully processed id. |
| [Behavior 33: Repair Missing Permanent-Subsidy Reduction Data](#behavior-33) | Eligible agreements have reduction date/sum and regenerated period state. |
| [Behavior 34: Backfill Subsidy Periods for One Agreement](#behavior-34) | New periods exist, with pre-migration periods marked `BEHANDLET_I_ARENA`. |
| [Behavior 35: Recalculate Unhandled Subsidy Periods](#behavior-35) | Unhandled tail is regenerated from the last approved end (or first period start), and sequence numbers are normalized. |
| [Behavior 36: Diagnose Subsidy-Period Date Ordering](#behavior-36) | Every detected date-order problem is logged with the known `{tilskuddsperiodeId}` obtained from the existing agreement state. |
| [Behavior 37: Annul One Subsidy Period](#behavior-37) | Period status is `ANNULLERT` and a cancellation event is registered. |
| [Behavior 38: Replace a Period and Resend It as Approved](#behavior-38) | Original is annulled/retained active for history and new approved child is added with copied approval attribution. |
| [Behavior 39: Replace a Period with an Unhandled Copy](#behavior-39) | Original annulled; active unhandled replacement with new UUID. |
| [Behavior 40: Reclassify Historical Periods as Arena-Treated](#behavior-40) | Matched originals are annulled and active replacements have `BEHANDLET_I_ARENA`. |
| [Behavior 41: Synchronize Selected Agreements to the Data Warehouse](#behavior-41) | One persisted DvhMeldingEntitet patch row for each selected id that exists. |
| [Behavior 42: Synchronize All Eligible Agreements to the Data Warehouse Asynchronously](#behavior-42) | The asynchronous bulk patch job is accepted for the full eligible population. |
| [Behavior 43: Backfill One Agreement Event Message](#behavior-43) | One current `STATUSENDRING` message row is persisted for the selected agreement. |
| [Behavior 44: Backfill All Agreement Event Messages Asynchronously](#behavior-44) | The asynchronous all-record message backfill is accepted. |

## Domain Summary

This service manages Norwegian labour-market measure agreements (`Avtale`) between a participant, an employer, a NAV advisor, and, for some measures, a mentor and a NAV decision-maker. The agreement is the main aggregate. It owns the current and historical `AvtaleInnhold`, approval timestamps, assignment to an advisor, derived lifecycle status, and subsidy periods (`TilskuddPeriode`). Supporting aggregates are saved searches (`FilterSok`), agreement notifications (`Varsel`), Arena-cleanup metadata (`ArenaRyddeAvtale`), journal markers on agreement versions, DVH messages, and agreement-event messages.

The externally meaningful outcomes are: a prepared draft; an agreement entered without decision-maker review; a subsidy-backed agreement entered after the first subsidy period is approved; a rejected subsidy period returned for correction; a versioned post-entry amendment; annulment or soft deletion; a role-specific work queue or document; journal completion; and operational repair or replay of persisted data. The primary actors are participant, employer, mentor, NAV advisor, NAV decision-maker, journal system, and authorized operations staff.

Implementation source is authoritative here. `full-behavior.md` supplies the 83 exact function names. `tiltaksgjennomforing.json` supplies the public contract. The implementation trace centers on `AvtaleController`, `Avtale`, `AvtaleInnhold`, the `Avtalepart` subclasses, `TilskuddPeriode`, and the internal/admin controllers under `src/main/java/no/nav/tag/tiltaksgjennomforing`.

Source-path convention for the method citations below: agreement endpoints and lifecycle rules are in `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java`, `Avtale.java`, `AvtaleInnhold.java`, `Avtalepart.java`, `Veileder.java`, `Arbeidsgiver.java`, `Deltaker.java`, `Mentor.java`, `Beslutter.java`, `TilskuddPeriode.java`, and `avtale/startOgSluttDatoStrategy/*.java`; operational behavior is in `avtale/AdminController.java`, `varsel/VarselController.java`, `journalfoering/InternalAvtaleController.java`, `datavarehus/InternalDvhMeldingProdusentController.java`, and `datadeling/AvtaleHendelseController.java`. Organization, account, and Arena result branches are in `orgenhet/EregService.java`, `okonomi/KontoregisterServiceImpl.java`, and `enhet/VeilarbArenaClient.java`. Paths are repository-relative.

## Supported Business Workflows

<a id="behavior-1"></a>
### Behavior 1: Obtain a Role-Scoped Agreement Worklist

Business goal:
Obtain the agreements currently visible to one participant, employer, mentor, advisor, or decision-maker context.

Primary actor(s):
Any agreement party.

Workflow boundary rationale:
The returned worklist is independently useful decision support. No created state is required and no successor operation is necessary to complete the listing goal, so the one-step rule is satisfied.

Starting state:
Existing domain state. Agreements may exist, but their creation is not part of the caller's listing goal.

Terminal business outcome:
A paginated, role-filtered set of non-deleted, non-error-registered agreements.

Required execution workflow:

#### Step 1: Obtain the worklist
- Use function `list accessible agreements` (`GET /avtaler`) with `innlogget-part=VEILEDER`, `tiltakstype=ARBEIDSTRENING`, `page=0`, `size=10`, and `sorteringskolonne=sistEndret` to obtain the worklist.
- Actor: selected agreement party.
- State before: zero or more agreements.
- State transition or decision: read-only filtering; no lifecycle transition.
- Output/state after: `avtaler`, paging totals, and current page.
- API-visible outputs: `200 OK` body with `avtaler`, `size`, `currentPage`, `totalItems`, and `totalPages`.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
`innlogget-part` determines the concrete `Avtalepart`; identifiers and object access further constrain results. Negative page/size values are normalized with `Math.abs`.

Business result and side effects:
No persistence. Mentor and employer projections redact domain-sensitive fields.

Constraints and invariants:
Soft-deleted and error-registered agreements are removed; employer visibility also expires twelve weeks after relevant end/termination dates.

Business failure branches:

#### Step 1 - `list accessible agreements`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
Unsupported issuer/role combinations are generic admission failures and are intentionally excluded. Evidence: `AvtaleController.hentAlleAvtalerInnloggetBrukerHarTilgangTil`, `Avtalepart.hentAlleAvtalerMedLesetilgang`, and role subclasses.

<a id="behavior-2"></a>
### Behavior 2: Save and Reuse a Filtered Agreement Search

Business goal:
Create a reusable filtered worklist and later reproduce it from a stable search id.

Primary actor(s):
Any agreement party.

Workflow boundary rationale:
The second function causally consumes the `sokId` created or reused by the first; treating either endpoint as a separate behavior would split one saved-search journey.

Starting state:
No prior saved-search state.

Terminal business outcome:
The saved filter is replayed and its usage metadata is updated.

Required execution workflow:

#### Step 1: Save or reuse a search
- Use function `search agreements and save search` (`POST /avtaler/sok`) with `body={tiltakstype:ARBEIDSTRENING,status:PÅBEGYNT}`, `sorteringskolonne=sistEndret`, `page=0`, `size=10`, and `innlogget-part=VEILEDER`.
- Actor: agreement party.
- State before: no matching `FilterSok`, or an identical hash already exists.
- State transition or decision: insert `FilterSok`, or increment `antallGangerSokt` and update `sistSoktTidspunkt`.
- Output/state after: filtered result plus `sokId`.
- API-visible outputs: `200 OK` body with the page keys plus `sokeParametere`, `sorteringskolonne`, and `sokId`.
- Handoff to later step: Step 2 consumes exactly this `sokId`.

#### Step 2: Replay the saved search
- Use function `replay saved agreement search` (`GET /avtaler/sok`) with Step 1 response-body `sokId`, `innlogget-part=VEILEDER`, `sorteringskolonne=sistEndret`, `page=0`, and `size=10`.
- Actor: agreement party.
- State before: saved filter exists.
- State transition or decision: usage count/time are updated and stored predicate is rerun.
- Output/state after: current filtered result and saved predicate.
- API-visible outputs: `200 OK` body with the page keys, `sokeParametere`, `sorteringskolonne`, and `sokId`; unknown ids yield empty values.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
`AvtalePredicate.generateHash()` becomes `sokId`; role identity is not stored in `FilterSok`, so replay results are re-filtered for the current caller.

Business result and side effects:
Saved-search usage metadata persists on both repeated POST and successful replay.

Constraints and invariants:
An unknown `sokId` returns a valid empty result with `sokId=""`; it is not a failure.

Business failure branches:

#### Step 1 - `search agreements and save search`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 2 - `replay saved agreement search`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
A hash collision is only logged; the existing filter is returned. Evidence: `AvtaleController.hentAlleAvtalerInnloggetBrukerHarTilgangTilMedPost/MedGet` and `FilterSok`.

<a id="behavior-3"></a>
### Behavior 3: Enter a Work-Training Agreement

Business goal:
Create, complete, and obtain all required approvals for a work-training agreement.

Primary actor(s):
NAV advisor, participant, and employer; an employer may initiate the draft.

Workflow boundary rationale:
Creation produces the `avtaleId`; update makes the `ARBEIDSTRENING` aggregate approvable; external approvals are prerequisites for advisor approval; advisor approval sets `avtaleInngått`. That entered state is the stable business boundary.

Starting state:
No prior service state.

Terminal business outcome:
An entered `ARBEIDSTRENING` agreement with an effective approved content version.

Required execution workflow:

#### Step 1: Create advisor-owned draft
- Use function `create advisor agreement` (`POST /avtaler`) with `body={deltakerFnr:"01039513753",bedriftNr:"111222333",tiltakstype:ARBEIDSTRENING}`.
- Actor: NAV advisor.
- State before: No agreement exists.
- State transition or decision: Creates the aggregate, version-1 content, advisor assignment, participant/unit data, and the creation event.
- Output/state after: A `PÅBEGYNT` advisor-owned work-training draft exists.
- API-visible outputs: `201 Created`; `Location: /avtaler/{avtaleId}`; no response body, `sistEndret`, or `Last-Modified`.
- Handoff to later step: Step 2 extracts `{avtaleId}` from `Location`.

#### Step 2: Read the initial freshness value
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `{avtaleId}` parsed from Step 1 `Location` and `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: The Step 1 draft exists, but Step 1 exposed no timestamp.
- State transition or decision: Reads rather than mutates the aggregate.
- Output/state after: The caller has the exact initial `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 3 uses response-body `sistEndret` as `If-Unmodified-Since`.

#### Step 3: Complete draft content
- Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with Step 1 `{avtaleId}`, `innlogget-part=VEILEDER`, Step 2 response-body `sistEndret` as `If-Unmodified-Since`, and `body={deltakerFornavn:"Kari",deltakerEtternavn:"Nordmann",deltakerTlf:"41234567",bedriftNavn:"Bedriften AS",arbeidsgiverFornavn:"Arne",arbeidsgiverEtternavn:"Arbeidsgiver",arbeidsgiverTlf:"42345678",veilederFornavn:"Vera",veilederEtternavn:"Veileder",veilederTlf:"43456789",startDato:"2026-07-01",sluttDato:"2026-12-31",oppfolging:"Monthly follow-up",tilrettelegging:"Adapted workstation",stillingprosent:80,arbeidsoppgaver:"Warehouse tasks",stillingstittel:"Warehouse assistant",stillingStyrk08:9334,stillingKonseptId:123,antallDagerPerUke:5,maal:[{beskrivelse:"Learn routines",kategori:ARBEIDSERFARING}]}`.
- Actor: NAV advisor.
- State before: The draft is editable and unapproved.
- State transition or decision: Validates the work-training interval/content and saves the completed current version.
- Output/state after: The draft is `MANGLER_GODKJENNING`.
- API-visible outputs: `200 OK` with an empty body and `Last-Modified` set from the saved agreement's `sistEndret`.
- Handoff to later step: Step 4 uses the `Last-Modified` response header as `If-Unmodified-Since`.

#### Step 4: Record participant approval
- Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=DELTAKER`, and Step 3 `Last-Modified` as `If-Unmodified-Since`.
- Actor: Participant.
- State before: Completed draft; no party has approved.
- State transition or decision: Sets `godkjentAvDeltaker` and emits `GodkjentAvDeltaker`.
- Output/state after: The participant approval is persisted.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 5 must read the new timestamp because Step 4 returns none.

#### Step 5: Refresh after participant approval
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the Step 1 id and `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Participant-approved draft.
- State transition or decision: Reads the post-approval aggregate.
- Output/state after: The caller has the participant-approved `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 6 uses response-body `sistEndret`.

#### Step 6: Record employer approval
- Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=ARBEIDSGIVER` and Step 5 `sistEndret` as `If-Unmodified-Since`.
- Actor: Employer.
- State before: Participant-approved completed draft.
- State transition or decision: Sets `godkjentAvArbeidsgiver` and emits `GodkjentAvArbeidsgiver`.
- Output/state after: Both external parties have approved.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 7 obtains the timestamp Step 6 does not return.

#### Step 7: Refresh after employer approval
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the Step 1 id and `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Both external approvals exist.
- State transition or decision: Reads the post-employer-approval aggregate.
- Output/state after: The caller has the current `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 8 uses response-body `sistEndret`.

#### Step 8: Enter the agreement
- Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=VEILEDER` and Step 7 `sistEndret` as `If-Unmodified-Since`.
- Actor: NAV advisor.
- State before: Complete assigned draft with participant and employer approvals.
- State transition or decision: Records advisor approval, `ikrafttredelsestidspunkt`, and `avtaleInngått`; emits advisor-approval and entry events.
- Output/state after: The non-decision agreement is entered.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:

Alternative Path A:
- Branch point: After required Step 3.
- Replaces: Required Steps 4 through 7.
- Retains or resumes: Required Step 8 resumes after A4.

#### Alternative Step A1: Approve as employer first
- Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=ARBEIDSGIVER` and Step 3 `Last-Modified` as `If-Unmodified-Since`.
- Actor: Employer.
- State before: Complete unapproved draft.
- State transition or decision: Records employer approval.
- Output/state after: Employer approval exists.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: A2 reads freshness.

#### Alternative Step A2: Refresh freshness
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with Step 1 id and `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Employer-approved draft.
- State transition or decision: Reads the aggregate.
- Output/state after: Current `sistEndret` is visible.
- API-visible outputs: `200 OK` `Avtale` body including `id` and `sistEndret`.
- Handoff to later step: A3 uses `sistEndret`.

#### Alternative Step A3: Approve as participant second
- Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=DELTAKER` and Alternative Step A2 response-body `sistEndret` as `If-Unmodified-Since`.
- Actor: Participant.
- State before: Employer-approved draft.
- State transition or decision: Records participant approval.
- Output/state after: Both external approvals exist.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: A4 reads freshness.

#### Alternative Step A4: Refresh for final approval
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with Step 1 id and `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Both external approvals exist.
- State transition or decision: Reads the aggregate.
- Output/state after: Current `sistEndret` is visible.
- API-visible outputs: `200 OK` `Avtale` body including `id` and `sistEndret`.
- Handoff to later step: Resume required Step 8.

Optional verification/supporting steps:

- Optional Step V1: Use function `check participant overlap` (`GET /avtaler/deltaker-allerede-paa-tiltak`) with `deltakerFnr=01039513753`, `tiltakstype=ARBEIDSTRENING`, `startDato=2026-07-01`, `sluttDato=2026-12-31`, and Step 1 `Location` `{avtaleId}` to support a duplicate/overlap decision.
- Optional Step V2: Use function `dry-run agreement update` (`PUT /avtaler/{avtaleId}/dry-run`) with Step 1 `Location` `{avtaleId}`, Step 2 response-body `sistEndret` as `If-Unmodified-Since`, `innlogget-part=VEILEDER`, and the concrete Step 3 `EndreAvtale` body to preview the update without saving.
- Optional Step V3: Use function `share agreement with a party` (`POST /avtaler/{avtaleId}/del-med-avtalepart`) with Step 1 `Location` `{avtaleId}` and JSON body `"DELTAKER"` to emit the participant review-handoff event.
- Optional Step V4: Use function `retrieve agreement by agreement number` (`GET /avtaler/avtaleNr/{avtaleNr}`) with Step 2 response-body `avtaleNr` and `innlogget-part=VEILEDER` to retrieve the aggregate by its generated number.
- Optional Step V5: Use function `list agreement versions` (`GET /avtaler/{avtaleId}/versjoner`) with Step 1 `Location` `{avtaleId}` and `innlogget-part=VEILEDER` to verify persisted content history.

Parameter, identity, and state bindings:
`Location` yields `avtaleId`; reads yield `sistEndret` and `avtaleNr`; each timestamp-changing operation requires the latest timestamp where the endpoint accepts `If-Unmodified-Since`. Participant/employer identities must match the concrete agreement; advisor object access is participant-scoped.

Business result and side effects:
The agreement is entered, current content remains version 1, journal eligibility begins, and synchronous domain listeners may create notifications/SMS/Gosys/DVH/event-message work. Event publication is tied to aggregate save.

Constraints and invariants:
All work-training fields must be complete; no approval may already exist when content is changed; the advisor approves last; on-behalf approval requires a reason; work-training date and duration limits are enforced at update.

Business failure branches:

#### Step 1 - `create advisor agreement`

##### Failure 1
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: advisor lacks write access to the participant.
- Why this is a business failure: participant eligibility/ownership is concrete.
- Violated state, rule, relationship, ownership, or eligibility condition: advisor-participant relationship.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkTilgangskontroll`

##### Failure 2
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: participant has protected address code 6.
- Why this is a business failure: participant protection blocks creation.
- Violated state, rule, relationship, ownership, or eligibility condition: protected-person rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 3
- Source discriminator: `SOMMERJOBB_IKKE_GAMMEL_NOK`
- Failure condition: participant is under 16 (the constructor applies it to every measure).
- Why this is a business failure: age eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: minimum age.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

##### Failure 4
- Source discriminator: `ENHET_FINNES_IKKE`
- Failure condition: the selected business or NAV unit cannot be resolved.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected business or NAV unit cannot be resolved.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.opprettAvtaleSomVeileder`

##### Failure 5
- Source discriminator: `ENHET_ER_JURIDISK`
- Failure condition: the selected organization number identifies a legal entity instead of a virksomhet.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected organization number identifies a legal entity instead of a virksomhet.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.opprettAvtaleSomVeileder`

##### Failure 6
- Source discriminator: `ENHET_ER_ORGLEDD`
- Failure condition: the selected organization number identifies an organizational link instead of a virksomhet.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected organization number identifies an organizational link instead of a virksomhet.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.opprettAvtaleSomVeileder`

Business failure coverage:
- Source-ledger business branches for this invocation: `6`
- Documented failure blocks for this invocation: `6`
- Coverage result: `Complete`

#### Step 3 - `update agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: `avtaleId` is absent.
- Why this is a business failure: target draft does not exist.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException("Ikke tilgang til avtale")`
- Failure condition: caller lacks object-scoped access.
- Why this is a business failure: concrete agreement ownership.
- Violated state, rule, relationship, ownership, or eligibility condition: participant/employer relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: agreement is annulled or interrupted.
- Why this is a business failure: terminal lifecycle state.
- Violated state, rule, relationship, ownership, or eligibility condition: editability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`

##### Failure 4
- Source discriminator: `TilgangskontrollException("Godkjenninger må oppheves før avtalen kan endres.")`
- Failure condition: any party has approved.
- Why this is a business failure: signed content is immutable until approvals are revoked.
- Violated state, rule, relationship, ownership, or eligibility condition: approval/content consistency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAvtalenKanEndres`

##### Failure 5
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: missing/stale `If-Unmodified-Since`.
- Why this is a business failure: optimistic concurrency protects the current draft.
- Violated state, rule, relationship, ownership, or eligibility condition: version match.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`

##### Failure 6
- Source discriminator: `START_ETTER_SLUTT`
- Failure condition: the proposed start date is after the proposed end date.
- Why this is a business failure: The requested agreement interval falls outside the applicable business date rule.
- Violated state, rule, relationship, ownership, or eligibility condition: the proposed start date is after the proposed end date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 7
- Source discriminator: `FORTIDLIG_STARTDATO`
- Failure condition: the proposed start is more than seven days in the past without after-registration authorization.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the proposed start is more than seven days in the past without after-registration authorization.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 8
- Source discriminator: `SLUTTDATO_GRENSE_NÅDD`
- Failure condition: the proposed end date is later than 2089-12-31.
- Why this is a business failure: The requested agreement interval falls outside the applicable business date rule.
- Violated state, rule, relationship, ownership, or eligibility condition: the proposed end date is later than 2089-12-31.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 9
- Source discriminator: `VARIGHET_FOR_LANG_ARBEIDSTRENING`
- Failure condition: the work-training end date is later than `startDato.plusMonths(18).minusDays(1)`.
- Why this is a business failure: The requested agreement interval falls outside the applicable business date rule.
- Violated state, rule, relationship, ownership, or eligibility condition: work training may last at most 18 months inclusive.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/ArbeidstreningStartOgSluttDatoStrategy.java — ArbeidstreningStartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 10
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: the advisor-side post-update PDL refresh reports address-protection code 6 for the agreement participant.
- Why this is a business failure: the concrete participant becomes ineligible for advisor processing even though the submitted agreement content itself passed validation.
- Violated state, rule, relationship, ownership, or eligibility condition: an advisor may not continue processing an address-protected participant through this agreement workflow.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreAvtale; Veileder.oppdaterePersondataFraPdlVedEndreAvtale; Veileder.sjekkKode6`; `src/main/java/no/nav/tag/tiltaksgjennomforing/exceptions/KanIkkeOppretteAvtalePåKode6Exception.java — KanIkkeOppretteAvtalePåKode6Exception constructor`

Business failure coverage:
- Source-ledger business branches for this invocation: `10`
- Documented failure blocks for this invocation: `10`
- Coverage result: `Complete`

#### Step 4 - `approve agreement as participant`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`

##### Failure 2
- Source discriminator: `TilgangskontrollException("Ikke tilgang til avtale")`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: stale timestamp.
- Why this is a business failure: approval must bind to reviewed content.
- Violated state, rule, relationship, ownership, or eligibility condition: version match.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`

##### Failure 4
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 5
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 6
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 7
- Source discriminator: `KAN_IKKE_GODKJENNE_DELTAKER_HAR_ALLEREDE_GODKJENT`
- Failure condition: duplicate participant approval.
- Why this is a business failure: duplicate lifecycle transition.
- Violated state, rule, relationship, ownership, or eligibility condition: single approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 8
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: terminal lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: approvability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Step 6 - `approve agreement as employer`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.harTilgangTilAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException("Ikke tilgang til avtale")`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.harTilgangTilAvtale`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: stale timestamp.
- Why this is a business failure: approval binds to reviewed version.
- Violated state, rule, relationship, ownership, or eligibility condition: concurrency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`

##### Failure 4
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 5
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 6
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 7
- Source discriminator: `KAN_IKKE_GODKJENNE_ARBEIDSGIVER_HAR_ALLEREDE_GODKJENT`
- Failure condition: employer already approved.
- Why this is a business failure: duplicate transition.
- Violated state, rule, relationship, ownership, or eligibility condition: single approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 8
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: terminal lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: approvability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Step 8 - `approve agreement as advisor`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: stale timestamp.
- Why this is a business failure: final approval binds to exact content.
- Violated state, rule, relationship, ownership, or eligibility condition: concurrency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`

##### Failure 4
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: `persondataService.erKode6(avtale.getDeltakerFnr())` returns true during advisor approval.
- Why this is a business failure: protection rule.
- Violated state, rule, relationship, ownership, or eligibility condition: advisor approval eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`;
  `src/main/java/no/nav/tag/tiltaksgjennomforing/exceptions/KanIkkeGodkjenneAvtalePåKode6Exception.java — KanIkkeGodkjenneAvtalePåKode6Exception constructor`

##### Failure 5
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 6
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 7
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 8
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 9
- Source discriminator: `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT`
- Failure condition: the advisor approval timestamp is already set on the current agreement content.
- Why this is a business failure: Advisor approval is a single lifecycle decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: advisor approval may be recorded only once for the current content version.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 10
- Source discriminator: `VEILEDER_SKAL_GODKJENNE_SIST`
- Failure condition: participant or employer approval is still missing when the advisor attempts final approval.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: participant or employer approval is still missing when the advisor attempts final approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 11
- Source discriminator: `DELTAKER_72_AAR`
- Failure condition: age at start/end exceeds measure limit.
- Why this is a business failure: The agreement would extend beyond the participant age allowed by the approval rule.
- Violated state, rule, relationship, ownership, or eligibility condition: age rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 12
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: approvability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

Business failure coverage:
- Source-ledger business branches for this invocation: `12`
- Documented failure blocks for this invocation: `12`
- Coverage result: `Complete`

#### Alternative Step A1 - `approve agreement as employer`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.harTilgangTilAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException("Ikke tilgang til avtale")`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.harTilgangTilAvtale`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: stale timestamp.
- Why this is a business failure: approval binds to reviewed version.
- Violated state, rule, relationship, ownership, or eligibility condition: concurrency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`

##### Failure 4
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 5
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 6
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 7
- Source discriminator: `KAN_IKKE_GODKJENNE_ARBEIDSGIVER_HAR_ALLEREDE_GODKJENT`
- Failure condition: employer already approved.
- Why this is a business failure: duplicate transition.
- Violated state, rule, relationship, ownership, or eligibility condition: single approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 8
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: terminal lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: approvability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Alternative Step A2 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Alternative Step A3 - `approve agreement as participant`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`

##### Failure 2
- Source discriminator: `TilgangskontrollException("Ikke tilgang til avtale")`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: stale timestamp.
- Why this is a business failure: approval must bind to reviewed content.
- Violated state, rule, relationship, ownership, or eligibility condition: version match.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`

##### Failure 4
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 5
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 6
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 7
- Source discriminator: `KAN_IKKE_GODKJENNE_DELTAKER_HAR_ALLEREDE_GODKJENT`
- Failure condition: duplicate participant approval.
- Why this is a business failure: duplicate lifecycle transition.
- Violated state, rule, relationship, ownership, or eligibility condition: single approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 8
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: terminal lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: approvability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Alternative Step A4 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Optional Step V1 - `check participant overlap`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Optional Step V2 - `dry-run agreement update`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: `{avtaleId}` does not resolve to an agreement.
- Why this is a business failure: the requested preview has no draft aggregate to evaluate.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected agreement must exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtaleDryRun`

##### Failure 2
- Source discriminator: `TilgangskontrollException("Ikke tilgang til avtale")`
- Failure condition: the advisor lacks the object-scoped participant relationship required for the selected agreement.
- Why this is a business failure: the preview evaluates a concrete participant-owned agreement.
- Violated state, rule, relationship, ownership, or eligibility condition: the advisor must have access to this agreement's participant.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgangOgEndreAvtale; Avtalepart.sjekkTilgang`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: a terminal agreement cannot be previewed as an editable draft.
- Violated state, rule, relationship, ownership, or eligibility condition: draft editing requires a non-terminal agreement.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale; Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`

##### Failure 4
- Source discriminator: `TilgangskontrollException("Godkjenninger må oppheves før avtalen kan endres.")`
- Failure condition: participant, employer, or advisor approval is still recorded on the current content.
- Why this is a business failure: previewing replacement terms while approvals remain would evaluate an edit that the persistent workflow cannot make.
- Violated state, rule, relationship, ownership, or eligibility condition: all approvals must be revoked before current content can change.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale; Avtale.sjekkOmAvtalenKanEndres`

##### Failure 5
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: `If-Unmodified-Since` is earlier than persisted `sistEndret`.
- Why this is a business failure: the preview must bind to the exact current draft version.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller's freshness value must not be stale.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale; Avtale.sjekkSistEndret`

##### Failure 6
- Source discriminator: `START_ETTER_SLUTT`
- Failure condition: the proposed `startDato` is after the proposed `sluttDato`.
- Why this is a business failure: the previewed agreement interval is internally impossible.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement start must not follow agreement end.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 7
- Source discriminator: `FORTIDLIG_STARTDATO`
- Failure condition: both dates are present, the agreement is not entered or after-registration-authorized, and `startDato.plusDays(7)` is before the current date.
- Why this is a business failure: the draft is too far backdated for ordinary registration.
- Violated state, rule, relationship, ownership, or eligibility condition: an unentered agreement may start more than seven days in the past only after after-registration authorization.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 8
- Source discriminator: `SLUTTDATO_GRENSE_NÅDD`
- Failure condition: the proposed `sluttDato` is after `2089-12-31`.
- Why this is a business failure: the requested agreement exceeds the service's maximum business date.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement end may not be later than 2089-12-31.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 9
- Source discriminator: `VARIGHET_FOR_LANG_ARBEIDSTRENING`
- Failure condition: the work-training end date is later than `startDato.plusMonths(18).minusDays(1)`.
- Why this is a business failure: the previewed work-training duration exceeds its measure limit.
- Violated state, rule, relationship, ownership, or eligibility condition: work training may last at most 18 months inclusive.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/ArbeidstreningStartOgSluttDatoStrategy.java — ArbeidstreningStartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 10
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: the advisor-side post-update PDL refresh reports address-protection code 6 for the participant.
- Why this is a business failure: the concrete participant is ineligible for continued advisor processing.
- Violated state, rule, relationship, ownership, or eligibility condition: address-protected participant data cannot be processed through this advisor update workflow.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreAvtale; Veileder.oppdaterePersondataFraPdlVedEndreAvtale; Veileder.sjekkKode6`; `src/main/java/no/nav/tag/tiltaksgjennomforing/exceptions/KanIkkeOppretteAvtalePåKode6Exception.java — KanIkkeOppretteAvtalePåKode6Exception constructor`

Business failure coverage:
- Source-ledger business branches for this invocation: `10`
- Documented failure blocks for this invocation: `10`
- Coverage result: `Complete`

#### Optional Step V3 - `share agreement with a party`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: share target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.delAvtaleMedAvtalepart`

##### Failure 2
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: agreement terminal.
- Why this is a business failure: closed agreement cannot be handed off.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.delMedAvtalepart`

##### Failure 3
- Source discriminator: `UGYLDIG_TLF`
- Failure condition: selected party's stored mobile number is invalid.
- Why this is a business failure: intended recipient cannot be reached.
- Violated state, rule, relationship, ownership, or eligibility condition: contact eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.delMedAvtalepart`

##### Failure 4
- Source discriminator: `IllegalArgumentException`
- Failure condition: role is not participant/employer/advisor/mentor.
- Why this is a business failure: no domain recipient exists.
- Violated state, rule, relationship, ownership, or eligibility condition: party relationship.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.telefonnummerTilAvtalepart`

Business failure coverage:
- Source-ledger business branches for this invocation: `4`
- Documented failure blocks for this invocation: `4`
- Coverage result: `Complete`

#### Optional Step V4 - `retrieve agreement by agreement number`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: number unknown.
- Why this is a business failure: aggregate absent.
- Violated state, rule, relationship, ownership, or eligibility condition: public identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.hentAvtaleFraAvtaleNr`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: caller cannot access found agreement.
- Why this is a business failure: ownership.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement-party relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`

Business failure coverage:
- Source-ledger business branches for this invocation: `2`
- Documented failure blocks for this invocation: `2`
- Coverage result: `Complete`

#### Optional Step V5 - `list agreement versions`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: id unknown.
- Why this is a business failure: version owner absent.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.hentAvtaleVersjoner`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: caller cannot access agreement.
- Why this is a business failure: history is object-scoped.
- Violated state, rule, relationship, ownership, or eligibility condition: ownership.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`

Business failure coverage:
- Source-ledger business branches for this invocation: `2`
- Documented failure blocks for this invocation: `2`
- Coverage result: `Complete`

#### Step 2 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 5 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 7 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
The update response exposes `Last-Modified`, but approval endpoints return no timestamp; clients must read the agreement again before the next timestamp-guarded approval. The share controller does not call `Veileder.sjekkTilgang`, a source-level access discrepancy.

<a id="behavior-4"></a>
### Behavior 4: Enter a Mentor Agreement

Business goal:
Create and enter a mentor agreement after confidentiality and party approvals.

Primary actor(s):
Advisor or employer initiator, mentor, participant, employer, and final NAV advisor.

Workflow boundary rationale:
Mentor creation carries `mentorFnr`; the mentor signature is a causal prerequisite for final advisor approval and full mentor visibility. Entry is a stable terminal milestone distinct from ordinary agreements.

Starting state:
No prior service state.

Terminal business outcome:
Entered mentor agreement with confidentiality, participant, employer, and advisor approvals.

Required execution workflow:

#### Step 1: Create mentor draft
- Use function `create mentor agreement as advisor` (`POST /avtaler/opprett-mentor-avtale`) with `body={deltakerFnr:"01039513753",mentorFnr:"23090170716",bedriftNr:"111222333",tiltakstype:MENTOR,avtalerolle:VEILEDER}`.
- Actor: NAV advisor.
- State before: No agreement exists.
- State transition or decision: Creates a mentor agreement with distinct participant and mentor identities.
- Output/state after: A `PÅBEGYNT` mentor draft exists.
- API-visible outputs: `201 Created`; `Location: /avtaler/{avtaleId}`; no response body, `sistEndret`, or `Last-Modified`.
- Handoff to later step: Step 2 extracts `{avtaleId}` from `Location`.

#### Step 2: Read the initial freshness value
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the Step 1 id and `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: The mentor draft exists; creation exposed no timestamp.
- State transition or decision: Reads the draft.
- Output/state after: The caller has initial `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 3 uses response-body `sistEndret`.

#### Step 3: Complete mentor content
- Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with Step 1 `{avtaleId}`, `innlogget-part=VEILEDER`, Step 2 response-body `sistEndret` as `If-Unmodified-Since`, and body fields `deltakerFornavn=Kari`, `deltakerEtternavn=Nordmann`, `deltakerTlf=41234567`, `bedriftNavn=Bedriften AS`, `arbeidsgiverFornavn=Arne`, `arbeidsgiverEtternavn=Arbeidsgiver`, `arbeidsgiverTlf=42345678`, `veilederFornavn=Vera`, `veilederEtternavn=Veileder`, `veilederTlf=43456789`, `startDato=2026-07-01`, `sluttDato=2026-12-31`, `oppfolging=Monthly follow-up`, and `tilrettelegging=Adapted workstation`, `mentorFornavn=Mona`, `mentorEtternavn=Mentor`, `mentorOppgaver=Daily guidance`, `mentorAntallTimer=20.0`, `mentorTlf=44567890`, `mentorTimelonn=350`, `harFamilietilknytning=false`, and `familietilknytningForklaring=null`.
- Actor: NAV advisor.
- State before: Editable, unapproved mentor draft.
- State transition or decision: Validates mentor duration/content and persists it.
- Output/state after: The mentor draft is complete.
- API-visible outputs: `200 OK` with an empty body and `Last-Modified` set from the saved agreement's `sistEndret`.
- Handoff to later step: Step 4 uses `Last-Modified`.

#### Step 4: Sign confidentiality declaration
- Use function `sign mentor confidentiality declaration` (`POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`) with Step 1 `{avtaleId}`, `innlogget-part=MENTOR`, and Step 3 `Last-Modified` as `If-Unmodified-Since`.
- Actor: Mentor.
- State before: Complete unsigned mentor draft.
- State transition or decision: Sets mentor confidentiality approval and emits `SignertAvMentor`.
- Output/state after: The mentor declaration is signed.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 5 obtains the new timestamp.

#### Step 5: Refresh after mentor signature
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Mentor-signed draft.
- State transition or decision: Reads the aggregate.
- Output/state after: The caller has current `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 6 uses response-body `sistEndret`.

#### Step 6: Record participant approval
- Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=DELTAKER` and Step 5 response-body `sistEndret` as `If-Unmodified-Since`.
- Actor: Participant.
- State before: Complete mentor-signed draft.
- State transition or decision: Records participant approval.
- Output/state after: Participant approval is persisted.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 7 refreshes the timestamp.

#### Step 7: Refresh after participant approval
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Participant-approved mentor draft.
- State transition or decision: Reads the aggregate.
- Output/state after: The caller has current `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 8 uses response-body `sistEndret`.

#### Step 8: Record employer approval
- Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=ARBEIDSGIVER` and Step 7 response-body `sistEndret` as `If-Unmodified-Since`.
- Actor: Employer.
- State before: Participant-approved mentor draft.
- State transition or decision: Records employer approval.
- Output/state after: Both external approvals are persisted.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 9 refreshes the timestamp.

#### Step 9: Refresh after employer approval
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Externally approved mentor draft.
- State transition or decision: Reads the aggregate.
- Output/state after: The caller has current `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 10 uses response-body `sistEndret`.

#### Step 10: Enter mentor agreement
- Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=VEILEDER` and Step 9 response-body `sistEndret` as `If-Unmodified-Since`.
- Actor: NAV advisor.
- State before: Complete assigned mentor draft with mentor, participant, and employer approvals.
- State transition or decision: Records advisor approval and entry.
- Output/state after: The mentor agreement is entered.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:

Alternative Path A:
- Branch point: No prior service state; employer initiates.
- Replaces: Required Steps 1 and 2.
- Retains or resumes: Required Steps 3 through 10 resume after A3.

#### Alternative Step A1: Create employer mentor draft
- Use function `create mentor agreement as employer` (`POST /avtaler/opprett-mentor-avtale`) with `body={deltakerFnr:"01039513753",mentorFnr:"23090170716",bedriftNr:"111222333",tiltakstype:MENTOR,avtalerolle:ARBEIDSGIVER}`.
- Actor: Employer.
- State before: No agreement exists.
- State transition or decision: Creates an unassigned mentor draft.
- Output/state after: Employer-created mentor draft exists.
- API-visible outputs: `201 Created` with `Location`; no body or freshness value.
- Handoff to later step: A2 uses the `Location` id.

#### Alternative Step A2: Assign advisor
- Use function `take over agreement as advisor` (`PUT /avtaler/{avtaleId}/overta`) with `{avtaleId}` parsed from Alternative Step A1 `Location` and no body.
- Actor: NAV advisor.
- State before: Unassigned mentor draft.
- State transition or decision: Refreshes follow-up state and assigns advisor.
- Output/state after: Draft is advisor-owned.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: A3 reads freshness.

#### Alternative Step A3: Read freshness
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Assigned mentor draft.
- State transition or decision: Reads aggregate.
- Output/state after: Current `sistEndret` is visible.
- API-visible outputs: `200 OK` `Avtale` body including `id` and `sistEndret`.
- Handoff to later step: Resume Step 3.

Alternative Path B:
- Branch point: After required Step 5.
- Replaces: Required Steps 6 through 10.
- Retains or resumes: B2 reaches the entered outcome.

#### Alternative Step B1: Approve as employer
- Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=ARBEIDSGIVER`, and Step 5 response-body `sistEndret` as `If-Unmodified-Since`.
- Actor: Employer.
- State before: Mentor-signed draft.
- State transition or decision: Records employer approval.
- Output/state after: Employer approval exists.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: B2 uses the id.

#### Alternative Step B2: Approve for participant and advisor
- Use function `approve on behalf of participant` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av`) with Step 1 `{avtaleId}` and `body={ikkeBankId=true,reservert=false,digitalKompetanse=false,arenaMigreringDeltaker=false}`.
- Actor: NAV advisor.
- State before: Mentor-signed employer-approved draft.
- State transition or decision: Records participant/advisor approval and reason; enters agreement.
- Output/state after: Mentor agreement is entered.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: None.

Optional verification/supporting steps:

- Optional Step V1: Use function `share agreement with a party` (`POST /avtaler/{avtaleId}/del-med-avtalepart`) with Step 1 `Location` `{avtaleId}` and JSON body `"MENTOR"` to notify the mentor after Step 3 has stored a valid `mentorTlf`.

Parameter, identity, and state bindings:
The creation `mentorFnr` binds the only mentor eligible to sign. `avtaleId` and successively refreshed `sistEndret` bind every approval.

Business result and side effects:
Signature and approval events drive notifications; entry makes the version journalable.

Constraints and invariants:
Participant and mentor must differ; mentor duration is at most 36 months for specially/permanently adapted effort and otherwise 6 months; mentor must sign before advisor approval.

Business failure branches:

#### Step 1 - `create mentor agreement as advisor`

##### Failure 1
- Source discriminator: `DELTAGER_OG_MENTOR_KAN_IKKE_HA_SAMME_FØDSELSNUMMER`
- Failure condition: identities equal.
- Why this is a business failure: mentor cannot be the participant.
- Violated state, rule, relationship, ownership, or eligibility condition: role separation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.opprettMentorAvtale`

##### Failure 2
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: the advisor lacks write access to the selected participant.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the advisor lacks write access to the selected participant.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettMentorAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`

##### Failure 3
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: PDL reports address-protection code 6 for the participant refreshed by the advisor path.
- Why this is a business failure: Address-protected participant data is a concrete eligibility bar for agreement processing in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: participant/employer relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 4
- Source discriminator: `SOMMERJOBB_IKKE_GAMMEL_NOK`
- Failure condition: The participant is under 16 when the agreement aggregate is constructed.
- Why this is a business failure: The participant fails the service-wide minimum-age eligibility rule.
- Violated state, rule, relationship, ownership, or eligibility condition: participant/employer relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

##### Failure 5
- Source discriminator: `ENHET_FINNES_IKKE`
- Failure condition: the selected business or NAV unit cannot be resolved.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected business or NAV unit cannot be resolved.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettMentorAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`

##### Failure 6
- Source discriminator: `ENHET_ER_JURIDISK`
- Failure condition: the selected organization number identifies a legal entity instead of a virksomhet.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected organization number identifies a legal entity instead of a virksomhet.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettMentorAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`

##### Failure 7
- Source discriminator: `ENHET_ER_ORGLEDD`
- Failure condition: the selected organization number identifies an organizational link instead of a virksomhet.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected organization number identifies an organizational link instead of a virksomhet.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettMentorAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`

Business failure coverage:
- Source-ledger business branches for this invocation: `7`
- Documented failure blocks for this invocation: `7`
- Coverage result: `Complete`

#### Step 3 - `update agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: mentor agreement missing.
- Why this is a business failure: target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: advisor/employer lacks agreement access.
- Why this is a business failure: concrete ownership.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement-party relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgangOgEndreAvtale`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: agreement annulled/interrupted.
- Why this is a business failure: terminal lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: editability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale`

##### Failure 4
- Source discriminator: `TilgangskontrollException`
- Failure condition: any approval exists.
- Why this is a business failure: signed mentor content cannot change.
- Violated state, rule, relationship, ownership, or eligibility condition: approval/content consistency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAvtalenKanEndres`

##### Failure 5
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: stale timestamp.
- Why this is a business failure: optimistic concurrency.
- Violated state, rule, relationship, ownership, or eligibility condition: version match.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`

##### Failure 6
- Source discriminator: `START_ETTER_SLUTT`
- Failure condition: start after end.
- Why this is a business failure: invalid agreement interval.
- Violated state, rule, relationship, ownership, or eligibility condition: date order.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 7
- Source discriminator: `FORTIDLIG_STARTDATO`
- Failure condition: unapproved backdated start.
- Why this is a business failure: after-registration rule.
- Violated state, rule, relationship, ownership, or eligibility condition: date eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 8
- Source discriminator: `SLUTTDATO_GRENSE_NÅDD`
- Failure condition: end after 2089-12-31.
- Why this is a business failure: persisted date boundary.
- Violated state, rule, relationship, ownership, or eligibility condition: maximum date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 9
- Source discriminator: `VARIGHET_FOR_LANG_MENTOR_36_MND`
- Failure condition: specially/permanently adapted participant interval exceeds 36 months.
- Why this is a business failure: mentor duration eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: qualified maximum.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 10
- Source discriminator: `VARIGHET_FOR_LANG_MENTOR_6_MND`
- Failure condition: other qualification interval exceeds 6 months.
- Why this is a business failure: mentor duration eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: ordinary maximum.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 11
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: refreshed PDL state is protected.
- Why this is a business failure: protected participant processing.
- Violated state, rule, relationship, ownership, or eligibility condition: code-6 rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

Business failure coverage:
- Source-ledger business branches for this invocation: `11`
- Documented failure blocks for this invocation: `11`
- Coverage result: `Complete`

#### Step 4 - `sign mentor confidentiality declaration`

##### Failure 1
- Source discriminator: `TiltaksgjennomforingException("Du må være mentor for å signere her")`
- Failure condition: caller role is not mentor.
- Why this is a business failure: only the named domain actor may sign.
- Violated state, rule, relationship, ownership, or eligibility condition: declaration ownership.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.mentorGodkjennTaushetserklæring`

##### Failure 2
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`

##### Failure 3
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`

##### Failure 4
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`

##### Failure 5
- Source discriminator: `KAN_IKKE_GODKJENNE_MENTOR_HAR_ALLEREDE_GODKJENT`
- Failure condition: duplicate signature.
- Why this is a business failure: duplicate transition.
- Violated state, rule, relationship, ownership, or eligibility condition: single declaration.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForMentor`

Business failure coverage:
- Source-ledger business branches for this invocation: `5`
- Documented failure blocks for this invocation: `5`
- Coverage result: `Complete`

#### Step 6 - `approve agreement as participant`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: participant fnr differs.
- Why this is a business failure: concrete approval ownership.
- Violated state, rule, relationship, ownership, or eligibility condition: participant relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Deltaker.java — Deltaker.harTilgangTilAvtale`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: stale timestamp.
- Why this is a business failure: approval binds reviewed content.
- Violated state, rule, relationship, ownership, or eligibility condition: version match.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: approvability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 5
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: required mentor content incomplete.
- Why this is a business failure: readiness.
- Violated state, rule, relationship, ownership, or eligibility condition: content completeness.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`

##### Failure 6
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: no advisor assigned.
- Why this is a business failure: agreement responsibility missing.
- Violated state, rule, relationship, ownership, or eligibility condition: assignment.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`

##### Failure 7
- Source discriminator: `KAN_IKKE_GODKJENNE_DELTAKER_HAR_ALLEREDE_GODKJENT`
- Failure condition: duplicate approval.
- Why this is a business failure: invalid repeated transition.
- Violated state, rule, relationship, ownership, or eligibility condition: single approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

Business failure coverage:
- Source-ledger business branches for this invocation: `7`
- Documented failure blocks for this invocation: `7`
- Coverage result: `Complete`

#### Step 8 - `approve agreement as employer`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: employer lacks company/measure access or visibility expired.
- Why this is a business failure: concrete organization ownership.
- Violated state, rule, relationship, ownership, or eligibility condition: employer relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.harTilgangTilAvtale`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: stale timestamp.
- Why this is a business failure: approval binds reviewed version.
- Violated state, rule, relationship, ownership, or eligibility condition: concurrency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: approvability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 5
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: mentor content incomplete.
- Why this is a business failure: readiness.
- Violated state, rule, relationship, ownership, or eligibility condition: completeness.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`

##### Failure 6
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: advisor absent.
- Why this is a business failure: responsibility missing.
- Violated state, rule, relationship, ownership, or eligibility condition: assignment.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`

##### Failure 7
- Source discriminator: `KAN_IKKE_GODKJENNE_ARBEIDSGIVER_HAR_ALLEREDE_GODKJENT`
- Failure condition: duplicate employer approval.
- Why this is a business failure: invalid repeated transition.
- Violated state, rule, relationship, ownership, or eligibility condition: single approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

Business failure coverage:
- Source-ledger business branches for this invocation: `7`
- Documented failure blocks for this invocation: `7`
- Coverage result: `Complete`

#### Step 10 - `approve agreement as advisor`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: stale timestamp.
- Why this is a business failure: final approval binds exact content.
- Violated state, rule, relationship, ownership, or eligibility condition: concurrency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`

##### Failure 4
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: participant protected.
- Why this is a business failure: protection.
- Violated state, rule, relationship, ownership, or eligibility condition: advisor processing eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 5
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: Arena status missing/invalid/ineligible.
- Why this is a business failure: participant measure eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: qualification.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 6
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 7
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 8
- Source discriminator: `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The advisor approval timestamp is already set on the current agreement content.
- Why this is a business failure: Advisor approval is a single lifecycle decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: named prerequisite.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 9
- Source discriminator: `VEILEDER_SKAL_GODKJENNE_SIST`
- Failure condition: participant or employer approval is still missing when the advisor attempts final approval.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: participant or employer approval is still missing when the advisor attempts final approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 10
- Source discriminator: `MENTOR_MÅ_SIGNERE_TAUSHETSERKLÆRING`
- Failure condition: Step 3 absent.
- Why this is a business failure: confidentiality prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: mentor declaration.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 11
- Source discriminator: `DELTAKER_72_AAR`
- Failure condition: participant exceeds end-date age rule.
- Why this is a business failure: age eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: participant limit.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`

##### Failure 12
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: approvability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`

Business failure coverage:
- Source-ledger business branches for this invocation: `12`
- Documented failure blocks for this invocation: `12`
- Coverage result: `Complete`

#### Alternative Step A1 - `create mentor agreement as employer`

##### Failure 1
- Source discriminator: `DELTAGER_OG_MENTOR_KAN_IKKE_HA_SAMME_FØDSELSNUMMER`
- Failure condition: same identities.
- Why this is a business failure: role separation.
- Violated state, rule, relationship, ownership, or eligibility condition: mentor relationship.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.opprettMentorAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException("Har ikke tilgang på tiltak i valgt bedrift")`
- Failure condition: employer lacks company/measure right.
- Why this is a business failure: concrete employer eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: Altinn-derived relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.opprettMentorAvtale`

##### Failure 3
- Source discriminator: `Opprett Mentor fant ingen avtale å behandle`
- Failure condition: `avtalerolle` is neither advisor nor employer.
- Why this is a business failure: no valid creator role.
- Violated state, rule, relationship, ownership, or eligibility condition: creator responsibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.opprettMentorAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

#### Alternative Step A2 - `take over agreement as advisor`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.settNyVeilederPåAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.settNyVeilederPåAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`

##### Failure 3
- Source discriminator: `ER_ALLEREDE_VEILEDER`
- Failure condition: the logged-in advisor is already the agreement's assigned `veilederNavIdent`.
- Why this is a business failure: A takeover is a transfer of responsibility and has no business meaning when responsibility is already assigned to the caller.
- Violated state, rule, relationship, ownership, or eligibility condition: takeover requires responsibility to move from a different advisor or from an unassigned state.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`

##### Failure 4
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 5
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 6
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNSTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 7
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNSTILSKUDD_FEIL`
- Failure condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 8
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: assignability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.overtaAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Alternative Step A3 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Alternative Step B1 - `approve agreement as employer`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: employer lacks company/measure access or visibility expired.
- Why this is a business failure: concrete organization ownership.
- Violated state, rule, relationship, ownership, or eligibility condition: employer relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.harTilgangTilAvtale`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: stale timestamp.
- Why this is a business failure: approval binds reviewed version.
- Violated state, rule, relationship, ownership, or eligibility condition: concurrency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: approvability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 5
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: mentor content incomplete.
- Why this is a business failure: readiness.
- Violated state, rule, relationship, ownership, or eligibility condition: completeness.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`

##### Failure 6
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: advisor absent.
- Why this is a business failure: responsibility missing.
- Violated state, rule, relationship, ownership, or eligibility condition: assignment.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjenn`

##### Failure 7
- Source discriminator: `KAN_IKKE_GODKJENNE_ARBEIDSGIVER_HAR_ALLEREDE_GODKJENT`
- Failure condition: duplicate employer approval.
- Why this is a business failure: invalid repeated transition.
- Violated state, rule, relationship, ownership, or eligibility condition: single approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

Business failure coverage:
- Source-ledger business branches for this invocation: `7`
- Documented failure blocks for this invocation: `7`
- Coverage result: `Complete`

#### Alternative Step B2 - `approve on behalf of participant`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 3
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: target/access/protection fails.
- Why this is a business failure: Address-protected participant data makes the agreement ineligible for advisor approval in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: identity/scope/protection.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 4
- Source discriminator: `DELTAKER_HAR_GODKJENT`
- Failure condition: the participant approval timestamp is already present when the advisor attempts to approve on the participant's behalf.
- Why this is a business failure: An advisor may not overwrite an approval already made by the participant.
- Violated state, rule, relationship, ownership, or eligibility condition: party order.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 5
- Source discriminator: `ARBEIDSGIVER_SKAL_GODKJENNE_FOER_VEILEDER`
- Failure condition: the employer approval timestamp is absent when the advisor attempts to approve on the participant's behalf.
- Why this is a business failure: The on-behalf participant path still requires prior employer consent.
- Violated state, rule, relationship, ownership, or eligibility condition: party order.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 6
- Source discriminator: `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT`
- Failure condition: the advisor approval timestamp is already present on the current content.
- Why this is a business failure: Advisor approval is a single lifecycle decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: party order.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 7
- Source discriminator: `GODKJENT_PAA_VEGNE_GRUNN_MAA_VELGES`
- Failure condition: no reason.
- Why this is a business failure: auditability.
- Violated state, rule, relationship, ownership, or eligibility condition: justification.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneGrunn.java — GodkjentPaVegneGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvArbeidsgiverGrunn.java — GodkjentPaVegneAvArbeidsgiverGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.java — GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.valgtMinstEnGrunn`

##### Failure 8
- Source discriminator: `MENTOR_MÅ_SIGNERE_TAUSHETSERKLÆRING`
- Failure condition: mentor signature missing.
- Why this is a business failure: confidentiality prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: declaration.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 9
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: required mentor content is incomplete.
- Why this is a business failure: On-behalf approval cannot finalize incomplete terms.
- Violated state, rule, relationship, ownership, or eligibility condition: approval readiness.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkOmAltErKlarTilGodkjenning`

##### Failure 10
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: participant follow-up classification is unavailable.
- Why this is a business failure: Measure eligibility cannot be established.
- Violated state, rule, relationship, ownership, or eligibility condition: participant eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 11
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot be approved.
- Violated state, rule, relationship, ownership, or eligibility condition: terminal lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`

Business failure coverage:
- Source-ledger business branches for this invocation: `11`
- Documented failure blocks for this invocation: `11`
- Coverage result: `Complete`

#### Optional Step V1 - `share agreement with a party`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: share target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.delAvtaleMedAvtalepart`

##### Failure 2
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: closed agreement cannot be handed off.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.delMedAvtalepart`

##### Failure 3
- Source discriminator: `UGYLDIG_TLF`
- Failure condition: selected party phone invalid.
- Why this is a business failure: recipient unreachable.
- Violated state, rule, relationship, ownership, or eligibility condition: contact eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.delAvtaleMedAvtalepart`

##### Failure 4
- Source discriminator: `IllegalArgumentException`
- Failure condition: selected role is not a supported agreement party.
- Why this is a business failure: recipient relation absent.
- Violated state, rule, relationship, ownership, or eligibility condition: party role.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.telefonnummerTilAvtalepart`

Business failure coverage:
- Source-ledger business branches for this invocation: `4`
- Documented failure blocks for this invocation: `4`
- Coverage result: `Complete`

#### Step 2 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 5 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 7 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 9 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
The generic `/godkjenn` route can also call `Mentor.godkjennForAvtalepart`, but `full-behavior.md` names the dedicated confidentiality endpoint as the primary function and the workflow uses it explicitly.

<a id="behavior-5"></a>
### Behavior 5: Enter a Subsidy-Backed Agreement After Decision-Maker Approval

Business goal:
Create, approve, and obtain subsidy-period authorization for a wage-subsidy or summer-job agreement.

Primary actor(s):
Advisor, participant, employer, and a different NAV decision-maker.

Workflow boundary rationale:
Advisor approval alone does not set `avtaleInngått` for decision measures. `approve subsidy period` consumes the calculated current period and advisor identity, then enters the agreement. This is one causal workflow.

Starting state:
No prior service state.

Terminal business outcome:
First current subsidy period is `GODKJENT`; agreement records decision-maker approval and is entered.

Required execution workflow:

#### Step 1: Create subsidy draft
- Use function `create advisor agreement` (`POST /avtaler`) with `body={deltakerFnr:"01039513753",bedriftNr:"111222333",tiltakstype:MIDLERTIDIG_LONNSTILSKUDD}`.
- Actor: NAV advisor.
- State before: No agreement exists.
- State transition or decision: Creates an advisor-owned wage-subsidy draft.
- Output/state after: A `PÅBEGYNT` draft exists.
- API-visible outputs: `201 Created`; `Location: /avtaler/{avtaleId}`; no response body, `sistEndret`, or `Last-Modified`.
- Handoff to later step: Step 2 extracts `{avtaleId}` from `Location`.

#### Step 2: Read initial freshness and generated identity
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with Step 1 id and `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Creation exposed no timestamp.
- State transition or decision: Reads the draft.
- Output/state after: The caller has `sistEndret` and `avtaleNr`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 3 uses response-body `sistEndret`.

#### Step 3: Calculate and persist subsidy content
- Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with Step 1 `{avtaleId}`, `innlogget-part=VEILEDER`, Step 2 response-body `sistEndret` as `If-Unmodified-Since`, and body fields `deltakerFornavn=Kari`, `deltakerEtternavn=Nordmann`, `deltakerTlf=41234567`, `bedriftNavn=Bedriften AS`, `arbeidsgiverFornavn=Arne`, `arbeidsgiverEtternavn=Arbeidsgiver`, `arbeidsgiverTlf=42345678`, `veilederFornavn=Vera`, `veilederEtternavn=Veileder`, `veilederTlf=43456789`, `startDato=2026-07-01`, `sluttDato=2026-12-31`, `oppfolging=Monthly follow-up`, and `tilrettelegging=Adapted workstation`, `stillingprosent=80`, `arbeidsoppgaver=Production work`, `stillingstittel=Operator`, `stillingStyrk08=8212`, `stillingKonseptId=321`, `antallDagerPerUke=5`, `arbeidsgiverKontonummer=12345678903`, `lonnstilskuddProsent=40`, `manedslonn=40000`, `feriepengesats=0.12`, `arbeidsgiveravgift=0.141`, `otpSats=0.02`, `harFamilietilknytning=false`, and `stillingstype=FAST`.
- Actor: NAV advisor.
- State before: Editable unapproved wage-subsidy draft.
- State transition or decision: Calculates derived wage values and creates active unhandled subsidy periods.
- Output/state after: A complete subsidy draft and its period schedule exist.
- API-visible outputs: `200 OK` with an empty body and `Last-Modified` set from the saved agreement's `sistEndret`.
- Handoff to later step: Step 4 uses `Last-Modified`.

#### Step 4: Record participant approval
- Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=DELTAKER` and Step 3 `Last-Modified` as `If-Unmodified-Since`.
- Actor: Participant.
- State before: Complete subsidy draft.
- State transition or decision: Records participant approval.
- Output/state after: Participant approval is persisted.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 5 refreshes the timestamp.

#### Step 5: Refresh after participant approval
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Participant-approved subsidy draft.
- State transition or decision: Reads the aggregate.
- Output/state after: The caller has current `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 6 uses response-body `sistEndret`.

#### Step 6: Record employer approval
- Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=ARBEIDSGIVER` and Step 5 response-body `sistEndret` as `If-Unmodified-Since`.
- Actor: Employer.
- State before: Participant-approved subsidy draft.
- State transition or decision: Records employer approval.
- Output/state after: Both external approvals exist.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 7 refreshes the timestamp.

#### Step 7: Refresh after employer approval
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Externally approved subsidy draft.
- State transition or decision: Reads the aggregate.
- Output/state after: The caller has current `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 8 uses response-body `sistEndret`.

#### Step 8: Approve as advisor and hand off
- Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=VEILEDER` and Step 7 response-body `sistEndret` as `If-Unmodified-Since`.
- Actor: NAV advisor.
- State before: Both external approvals exist.
- State transition or decision: Records advisor approval without entering this decision-backed agreement.
- Output/state after: The first active unhandled period is ready for a decision-maker.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 9 uses the same `{avtaleId}`; it does not require a freshness header.

#### Step 9: Approve current subsidy period and enter agreement
- Use function `approve subsidy period` (`POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`) with Step 1 `{avtaleId}` and `enhet=1234`; the decision-maker identity comes from the caller and must differ from the advisor's `godkjentAvNavIdent`.
- Actor: NAV decision-maker.
- State before: Advisor-approved wage-subsidy agreement with an active unhandled current period.
- State transition or decision: Validates object-scoped participant access, unit existence, segregation of duties, period status, and `kanBesluttesFom`; marks the period `GODKJENT` and enters the agreement.
- Output/state after: The period is approved and the agreement has `godkjentAvBeslutter` plus `avtaleInngått`.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:

Alternative Path A:
- Branch point: No prior service state; employer initiates.
- Replaces: Required Steps 1 and 2.
- Retains or resumes: Required Steps 3 through 9 resume and reach the same decision approval.

#### Alternative Step A1: Create employer subsidy draft
- Use function `create employer agreement` (`POST /avtaler/opprett-som-arbeidsgiver`) with `body={deltakerFnr:"01039513753",bedriftNr:"111222333",tiltakstype:MIDLERTIDIG_LONNSTILSKUDD}`.
- Actor: Employer.
- State before: No agreement exists.
- State transition or decision: Creates unassigned draft.
- Output/state after: Employer draft exists.
- API-visible outputs: `201 Created` with `Location`; no body or freshness value.
- Handoff to later step: A2 uses `Location` id.

#### Alternative Step A2: Assign advisor
- Use function `take over agreement as advisor` (`PUT /avtaler/{avtaleId}/overta`) with `{avtaleId}` parsed from Alternative Step A1 `Location` and no body.
- Actor: NAV advisor.
- State before: Unassigned draft.
- State transition or decision: Refreshes and assigns.
- Output/state after: Draft is advisor-owned.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: A3 reads freshness.

#### Alternative Step A3: Read freshness
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Assigned draft.
- State transition or decision: Reads aggregate.
- Output/state after: Current `sistEndret` is visible.
- API-visible outputs: `200 OK` `Avtale` body including `id` and `sistEndret`.
- Handoff to later step: Resume Step 3.

Alternative Path B:
- Branch point: After required Step 3.
- Replaces: Required Steps 4 through 8.
- Retains or resumes: Required Step 9 resumes after B2.

#### Alternative Step B1: Approve as employer first
- Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=ARBEIDSGIVER`, and Step 3 `Last-Modified` as `If-Unmodified-Since`.
- Actor: Employer.
- State before: Complete draft.
- State transition or decision: Records employer approval.
- Output/state after: Employer approval exists.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: B2 uses id.

#### Alternative Step B2: Approve for participant and advisor
- Use function `approve on behalf of participant` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av`) with Step 1 `{avtaleId}` and `body={ikkeBankId=true,reservert=false,digitalKompetanse=false,arenaMigreringDeltaker=false}`.
- Actor: NAV advisor.
- State before: Employer-approved draft.
- State transition or decision: Records participant/advisor approval.
- Output/state after: Ready for decision.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: Resume Step 9.

Alternative Path C:
- Branch point: After required Step 3.
- Replaces: Required Steps 4 through 8.
- Retains or resumes: Required Step 9 resumes after C2.

#### Alternative Step C1: Approve as participant
- Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=DELTAKER`, and Step 3 `Last-Modified` as `If-Unmodified-Since`.
- Actor: Participant.
- State before: Complete draft.
- State transition or decision: Records participant approval.
- Output/state after: Participant approval exists.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: C2 uses id.

#### Alternative Step C2: Approve for employer and advisor
- Use function `approve on behalf of employer` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver`) with Step 1 `{avtaleId}` and `body={klarerIkkeGiFaTilgang=true,vetIkkeHvemSomKanGiTilgang=false,farIkkeTilgangPersonvern=false,arenaMigreringArbeidsgiver=false}`.
- Actor: NAV advisor.
- State before: Participant-approved draft.
- State transition or decision: Records employer/advisor approval.
- Output/state after: Ready for decision.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: Resume Step 9.

Alternative Path D:
- Branch point: After required Step 3.
- Replaces: Required Steps 4 through 8.
- Retains or resumes: Required Step 9 resumes after D1.

#### Alternative Step D1: Approve for both parties and advisor
- Use function `approve on behalf of participant and employer` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`) with Step 1 `{avtaleId}` and `body={godkjentPaVegneAvArbeidsgiverGrunn:{klarerIkkeGiFaTilgang:true,vetIkkeHvemSomKanGiTilgang:false,farIkkeTilgangPersonvern:false,arenaMigreringArbeidsgiver:false},godkjentPaVegneAvDeltakerGrunn:{ikkeBankId:true,reservert:false,digitalKompetanse:false,arenaMigreringDeltaker:false}}`.
- Actor: NAV advisor.
- State before: Complete unapproved draft.
- State transition or decision: Records all three approvals and provenance.
- Output/state after: Ready for decision.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: Resume Step 9.

Optional verification/supporting steps:

- Optional Step V1: Use function `get employer account number` (`GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver`) with Step 1 `Location` `{avtaleId}` and `innlogget-part=VEILEDER` to obtain financial setup data.
- Optional Step V2: Use function `list decision-maker agreements` (`GET /avtaler/beslutter-liste`) with `tilskuddPeriodeStatus=UBEHANDLET`, `navEnhet=1234`, `sorteringskolonne=startDato`, `page=0`, `size=20`, and `sorteringOrder=ASC` to locate the pending period.
- Optional Step V3: Use function `download agreement PDF` (`GET /avtaler/{avtaleId}/pdf`) with Step 1 `Location` `{avtaleId}` and `innlogget-part=VEILEDER` after advisor approval to obtain the approved document.

Parameter, identity, and state bindings:
The calculated period remains inside the aggregate; Step 6 chooses `gjeldendeTilskuddsperiode`. The decision-maker NAV ident must differ from `godkjentAvNavIdent`. Unit name is validated through Norg2.

Business result and side effects:
Subsidy approval event includes resend number when applicable; entry triggers journal and outbound integration listeners.

Constraints and invariants:
Calculation must be complete; advisor and decision-maker must differ; only an active unhandled period within its decision window can be approved.

Business failure branches:

#### Step 1 - `create advisor agreement`

##### Failure 1
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: the advisor lacks write access to the selected participant.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the advisor lacks write access to the selected participant.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettAvtale`

##### Failure 2
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: PDL reports address-protection code 6 for the selected participant during advisor-side creation.
- Why this is a business failure: Address-protected participant data is a concrete eligibility bar for agreement processing in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: PDL reports address-protection code 6 for the participant refreshed by the advisor path.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 3
- Source discriminator: `SOMMERJOBB_IKKE_GAMMEL_NOK`
- Failure condition: the participant is under 16 when the agreement constructor evaluates the supplied national identity number.
- Why this is a business failure: The participant fails the service-wide minimum-age eligibility rule.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant is under 16 when the agreement aggregate is constructed.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

##### Failure 4
- Source discriminator: `SOMMERJOBB_FOR_GAMMEL`
- Failure condition: for a `SOMMERJOBB` creation variant, the participant is over 30 on 1 January of the creation year.
- Why this is a business failure: The participant fails the summer-job age eligibility rule at creation.
- Violated state, rule, relationship, ownership, or eligibility condition: For a `SOMMERJOBB` agreement, the participant is over 30 on 1 January of the creation year.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

##### Failure 5
- Source discriminator: `ENHET_FINNES_IKKE`
- Failure condition: the selected business or NAV unit cannot be resolved.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected business or NAV unit cannot be resolved.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`

##### Failure 6
- Source discriminator: `ENHET_ER_JURIDISK`
- Failure condition: the selected organization number identifies a legal entity instead of a virksomhet.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected organization number identifies a legal entity instead of a virksomhet.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`

##### Failure 7
- Source discriminator: `ENHET_ER_ORGLEDD`
- Failure condition: the selected organization number identifies an organizational link instead of a virksomhet.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected organization number identifies an organizational link instead of a virksomhet.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`

Business failure coverage:
- Source-ledger business branches for this invocation: `7`
- Documented failure blocks for this invocation: `7`
- Coverage result: `Complete`

#### Step 3 - `update agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale`

##### Failure 4
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale`

##### Failure 5
- Source discriminator: `START_ETTER_SLUTT`
- Failure condition: the proposed start date is after the proposed end date.
- Why this is a business failure: The requested agreement interval falls outside the applicable business date rule.
- Violated state, rule, relationship, ownership, or eligibility condition: the proposed start date is after the proposed end date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 6
- Source discriminator: `FORTIDLIG_STARTDATO`
- Failure condition: the proposed start is more than seven days in the past without after-registration authorization.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the proposed start is more than seven days in the past without after-registration authorization.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 7
- Source discriminator: `SLUTTDATO_GRENSE_NÅDD`
- Failure condition: the proposed end date is later than 2089-12-31.
- Why this is a business failure: The requested agreement interval falls outside the applicable business date rule.
- Violated state, rule, relationship, ownership, or eligibility condition: the proposed end date is later than 2089-12-31.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 8
- Source discriminator: `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_12_MND`
- Failure condition: A temporary wage-subsidy interval exceeds 12 months for `SITUASJONSBESTEMT_INNSATS` or a missing qualification group.
- Why this is a business failure: The requested interval falls outside the selected measure's duration or seasonal eligibility window.
- Violated state, rule, relationship, ownership, or eligibility condition: named date/duration rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 9
- Source discriminator: `VARIGHET_FOR_LANG_MIDLERTIDIG_LONNSTILSKUDD_24_MND`
- Failure condition: A temporary wage-subsidy interval exceeds 24 months for `SPESIELT_TILPASSET_INNSATS` or `VARIG_TILPASSET_INNSATS`.
- Why this is a business failure: The requested interval falls outside the selected measure's duration or seasonal eligibility window.
- Violated state, rule, relationship, ownership, or eligibility condition: named date/duration rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/MidlertidigLonnstilskuddStartOgSluttDatoStrategy.java — MidlertidigLonnstilskuddStartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 10
- Source discriminator: `SOMMERJOBB_FOR_TIDLIG`
- Failure condition: A summer-job start date, or its end date, is before 1 June of its year.
- Why this is a business failure: The requested interval falls outside the selected measure's duration or seasonal eligibility window.
- Violated state, rule, relationship, ownership, or eligibility condition: named date/duration rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 11
- Source discriminator: `SOMMERJOBB_FOR_SENT`
- Failure condition: A summer-job start date is after 31 August, or its end date is after 27 September.
- Why this is a business failure: The requested interval falls outside the selected measure's duration or seasonal eligibility window.
- Violated state, rule, relationship, ownership, or eligibility condition: named date/duration rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 12
- Source discriminator: `SOMMERJOBB_FOR_LANG_VARIGHET`
- Failure condition: The summer-job interval exceeds four weeks inclusive.
- Why this is a business failure: The requested interval falls outside the selected measure's duration or seasonal eligibility window.
- Violated state, rule, relationship, ownership, or eligibility condition: named date/duration rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/SommerjobbStartOgSluttDatoStrategy.java — SommerjobbStartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 13
- Source discriminator: `FEIL_OTP_SATS`
- Failure condition: `otpSats` is less than `0.0` or greater than `0.3`.
- Why this is a business failure: The pension rate is a financial input used to calculate the employer cost and subsidy amount.
- Violated state, rule, relationship, ownership, or eligibility condition: financial rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/LonnstilskuddStrategy.java — LonnstilskuddStrategy.endre`

##### Failure 14
- Source discriminator: `LONNSTILSKUDD_PROSENT_ER_UGYLDIG`
- Failure condition: `lonnstilskuddProsent` is not one of the percentages allowed for the concrete wage-subsidy subtype and qualification group.
- Why this is a business failure: The selected measure permits only its configured subsidy percentages.
- Violated state, rule, relationship, ownership, or eligibility condition: financial rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/MidlertidigLonnstilskuddStrategy.java — MidlertidigLonnstilskuddStrategy.sjekktilskuddsprosentSats`

Business failure coverage:
- Source-ledger business branches for this invocation: `14`
- Documented failure blocks for this invocation: `14`
- Coverage result: `Complete`

#### Step 4 - `approve agreement as participant`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 5
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 6
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 7
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 8
- Source discriminator: `KAN_IKKE_GODKJENNE_DELTAKER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The participant approval timestamp is already set on the current agreement content.
- Why this is a business failure: Participant consent is a single decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Step 6 - `approve agreement as employer`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 5
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 6
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 7
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 8
- Source discriminator: `KAN_IKKE_GODKJENNE_ARBEIDSGIVER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The employer approval timestamp is already set on the current agreement content.
- Why this is a business failure: Employer consent is a single decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The employer approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Step 8 - `approve agreement as advisor`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 4
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Why this is a business failure: Address-protected participant data makes the agreement ineligible for advisor approval in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 5
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 6
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 7
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 8
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 9
- Source discriminator: `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The advisor approval timestamp is already set on the current agreement content.
- Why this is a business failure: Advisor approval is a single lifecycle decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The advisor approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 10
- Source discriminator: `VEILEDER_SKAL_GODKJENNE_SIST`
- Failure condition: participant or employer approval is still missing when the advisor attempts final approval.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: participant or employer approval is still missing when the advisor attempts final approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 11
- Source discriminator: `SOMMERJOBB_FOR_GAMMEL_FRA_OPPSTARTDATO`
- Failure condition: For a summer-job agreement, the participant is over 30 at the agreement start date.
- Why this is a business failure: The participant fails the summer-job age eligibility rule at the actual start date.
- Violated state, rule, relationship, ownership, or eligibility condition: For a summer-job agreement, the participant is over 30 at the agreement start date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 12
- Source discriminator: `DELTAKER_72_AAR`
- Failure condition: For a non-summer-job agreement, the participant is over 72 at the agreement end date.
- Why this is a business failure: The agreement would extend beyond the participant age allowed by the approval rule.
- Violated state, rule, relationship, ownership, or eligibility condition: For a non-summer-job agreement, the participant is over 72 at the agreement end date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 13
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 14
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 15
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNSTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 16
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNSTILSKUDD_FEIL`
- Failure condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

Business failure coverage:
- Source-ledger business branches for this invocation: `16`
- Documented failure blocks for this invocation: `16`
- Coverage result: `Complete`

#### Step 9 - `approve subsidy period`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.sjekkTilgang`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.sjekkTilgang`

##### Failure 3
- Source discriminator: `ENHET_FINNES_IKKE`
- Failure condition: Norg2 has no selected unit.
- Why this is a business failure: cost attribution unit is invalid.
- Violated state, rule, relationship, ownership, or eligibility condition: NAV-unit relationship.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.godkjennTilskuddsperiode`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: agreement annulled/interrupted.
- Why this is a business failure: terminal lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: decisionability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennTilskuddsperiode`

##### Failure 5
- Source discriminator: `TILSKUDDSPERIODE_KAN_KUN_BEHANDLES_VED_INNGAATT_AVTALE`
- Failure condition: advisor has not approved (despite wording).
- Why this is a business failure: advisor handoff missing.
- Violated state, rule, relationship, ownership, or eligibility condition: decision prerequisite.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennTilskuddsperiode`

##### Failure 6
- Source discriminator: `TILSKUDDSPERIODE_ENHET_FIRE_SIFFER`
- Failure condition: unit absent/non-four-digit.
- Why this is a business failure: persisted decision attribution invalid.
- Violated state, rule, relationship, ownership, or eligibility condition: unit format.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennTilskuddsperiode`

##### Failure 7
- Source discriminator: `TILSKUDDSPERIODE_IKKE_GODKJENNE_EGNE`
- Failure condition: decision-maker is approving own advisor decision.
- Why this is a business failure: segregation of duties.
- Violated state, rule, relationship, ownership, or eligibility condition: actor separation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennTilskuddsperiode`

##### Failure 8
- Source discriminator: `TILSKUDDSPERIODE_ER_ALLEREDE_BEHANDLET`
- Failure condition: current period is not unhandled.
- Why this is a business failure: invalid state transition.
- Violated state, rule, relationship, ownership, or eligibility condition: period lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/TilskuddPeriode.java — TilskuddPeriode.sjekkOmKanBehandles`

##### Failure 9
- Source discriminator: `TILSKUDDSPERIODE_BEHANDLE_FOR_TIDLIG`
- Failure condition: decision date precedes `kanBesluttesFom`.
- Why this is a business failure: budget/decision window.
- Violated state, rule, relationship, ownership, or eligibility condition: temporal eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/TilskuddPeriode.java — TilskuddPeriode.sjekkOmKanBehandles`

Business failure coverage:
- Source-ledger business branches for this invocation: `9`
- Documented failure blocks for this invocation: `9`
- Coverage result: `Complete`

#### Alternative Step A1 - `create employer agreement`

##### Failure 1
- Source discriminator: `TilgangskontrollException("Har ikke tilgang på tiltak i valgt bedrift")`
- Failure condition: employer lacks company/measure right.
- Why this is a business failure: concrete organization eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: Altinn relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.opprettAvtale`

##### Failure 2
- Source discriminator: `SOMMERJOBB_IKKE_GAMMEL_NOK`
- Failure condition: The participant is under 16 when the agreement aggregate is constructed.
- Why this is a business failure: The participant fails the service-wide minimum-age eligibility rule.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant is under 16 when the agreement aggregate is constructed.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

##### Failure 3
- Source discriminator: `SOMMERJOBB_FOR_GAMMEL`
- Failure condition: For a `SOMMERJOBB` agreement, the participant is over 30 on 1 January of the creation year.
- Why this is a business failure: The participant fails the summer-job age eligibility rule at creation.
- Violated state, rule, relationship, ownership, or eligibility condition: For a `SOMMERJOBB` agreement, the participant is over 30 on 1 January of the creation year.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

#### Alternative Step A2 - `take over agreement as advisor`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.overtaAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.overtaAvtale`

##### Failure 3
- Source discriminator: `ER_ALLEREDE_VEILEDER`
- Failure condition: target/access/ownership/lifecycle fails.
- Why this is a business failure: A takeover is a transfer of responsibility and has no business meaning when responsibility is already assigned to the caller.
- Violated state, rule, relationship, ownership, or eligibility condition: The logged-in advisor is already the agreement's assigned `veilederNavIdent`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.overtaAvtale`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.overtaAvtale`

##### Failure 5
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 6
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 7
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNSTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 8
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNSTILSKUDD_FEIL`
- Failure condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Alternative Step A3 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Alternative Step B1 - `approve agreement as employer`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 5
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 6
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 7
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 8
- Source discriminator: `KAN_IKKE_GODKJENNE_ARBEIDSGIVER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The employer approval timestamp is already set on the current agreement content.
- Why this is a business failure: Employer consent is a single decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The employer approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Alternative Step B2 - `approve on behalf of participant`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 2
- Source discriminator: `DELTAKER_HAR_GODKJENT`
- Failure condition: The participant has already approved, so an advisor cannot replace that decision with an on-behalf approval.
- Why this is a business failure: An advisor may not overwrite an approval already made by the participant.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant has already approved, so an advisor cannot replace that decision with an on-behalf approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 3
- Source discriminator: `ARBEIDSGIVER_SKAL_GODKJENNE_FOER_VEILEDER`
- Failure condition: The employer approval timestamp is absent when the advisor tries to approve on behalf of the participant.
- Why this is a business failure: The on-behalf participant path still requires prior employer consent.
- Violated state, rule, relationship, ownership, or eligibility condition: The employer approval timestamp is absent when the advisor tries to approve on behalf of the participant.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 4
- Source discriminator: `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The advisor approval timestamp is already set on the current agreement content.
- Why this is a business failure: Advisor approval is a single lifecycle decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The advisor approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 5
- Source discriminator: `GODKJENT_PAA_VEGNE_GRUNN_MAA_VELGES`
- Failure condition: Every boolean reason field in the applicable on-behalf reason object is `false`.
- Why this is a business failure: An on-behalf approval must retain an auditable business reason.
- Violated state, rule, relationship, ownership, or eligibility condition: Every boolean reason field in the applicable on-behalf reason object is `false`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneGrunn.java — GodkjentPaVegneGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvArbeidsgiverGrunn.java — GodkjentPaVegneAvArbeidsgiverGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.java — GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.valgtMinstEnGrunn`

##### Failure 6
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 7
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Why this is a business failure: Address-protected participant data makes the agreement ineligible for advisor approval in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 8
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 9
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 10
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNSTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 11
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNSTILSKUDD_FEIL`
- Failure condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 12
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 13
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

##### Failure 14
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltaker`

Business failure coverage:
- Source-ledger business branches for this invocation: `14`
- Documented failure blocks for this invocation: `14`
- Coverage result: `Complete`

#### Alternative Step C1 - `approve agreement as participant`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 5
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 6
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 7
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 8
- Source discriminator: `KAN_IKKE_GODKJENNE_DELTAKER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The participant approval timestamp is already set on the current agreement content.
- Why this is a business failure: Participant consent is a single decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Alternative Step C2 - `approve on behalf of employer`

##### Failure 1
- Source discriminator: `GODKJENN_PAA_VEGNE_AV_FEIL_TILTAKSTYPE`
- Failure condition: measure is not summer job or wage subsidy.
- Why this is a business failure: operation eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: measure rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`

##### Failure 2
- Source discriminator: `ARBEIDSGIVER_HAR_GODKJENT`
- Failure condition: the employer approval timestamp is already set on the current agreement content.
- Why this is a business failure: An advisor may not overwrite an approval already made by the employer.
- Violated state, rule, relationship, ownership, or eligibility condition: an on-behalf approval cannot replace the employer's own recorded consent.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`

##### Failure 3
- Source discriminator: `DELTAKER_SKAL_GODKJENNE_FOER_VEILEDER`
- Failure condition: the participant approval timestamp is absent when the advisor tries to approve on behalf of the employer.
- Why this is a business failure: The on-behalf employer path still requires prior participant consent.
- Violated state, rule, relationship, ownership, or eligibility condition: participant consent must precede an advisor's on-behalf employer approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`

##### Failure 4
- Source discriminator: `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT`
- Failure condition: the advisor approval timestamp is already set on the current agreement content.
- Why this is a business failure: Advisor approval is a single lifecycle decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: advisor approval may be recorded only once for the current content version.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`

##### Failure 5
- Source discriminator: `GODKJENT_PAA_VEGNE_GRUNN_MAA_VELGES`
- Failure condition: no employer reason.
- Why this is a business failure: auditability.
- Violated state, rule, relationship, ownership, or eligibility condition: recorded justification.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneGrunn.java — GodkjentPaVegneGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvArbeidsgiverGrunn.java — GodkjentPaVegneAvArbeidsgiverGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.java — GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.valgtMinstEnGrunn`

##### Failure 6
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

##### Failure 7
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

##### Failure 8
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Why this is a business failure: Address-protected participant data makes the agreement ineligible for advisor approval in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 9
- Source discriminator: `SOMMERJOBB_FOR_GAMMEL_FRA_OPPSTARTDATO`
- Failure condition: For a summer-job agreement, the participant is over 30 at the agreement start date.
- Why this is a business failure: The participant fails the summer-job age eligibility rule at the actual start date.
- Violated state, rule, relationship, ownership, or eligibility condition: For a summer-job agreement, the participant is over 30 at the agreement start date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

##### Failure 10
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

##### Failure 11
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

##### Failure 12
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

##### Failure 13
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNSTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

##### Failure 14
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNSTILSKUDD_FEIL`
- Failure condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

Business failure coverage:
- Source-ledger business branches for this invocation: `14`
- Documented failure blocks for this invocation: `14`
- Coverage result: `Complete`

#### Alternative Step D1 - `approve on behalf of participant and employer`

##### Failure 1
- Source discriminator: `GODKJENN_PAA_VEGNE_AV_FEIL_TILTAKSTYPE`
- Failure condition: the agreement measure is not `SOMMERJOBB`, `MIDLERTIDIG_LONNSTILSKUDD`, or `VARIG_LONNSTILSKUDD`.
- Why this is a business failure: Employer on-behalf approval is limited to the decision-backed and summer-job measures named by the domain rule.
- Violated state, rule, relationship, ownership, or eligibility condition: measure/approval state.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`

##### Failure 2
- Source discriminator: `DELTAKER_HAR_GODKJENT`
- Failure condition: the participant approval timestamp is already present when combined on-behalf approval is attempted.
- Why this is a business failure: An advisor may not overwrite an approval already made by the participant.
- Violated state, rule, relationship, ownership, or eligibility condition: measure/approval state.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`

##### Failure 3
- Source discriminator: `ARBEIDSGIVER_HAR_GODKJENT`
- Failure condition: the employer approval timestamp is already present when combined on-behalf approval is attempted.
- Why this is a business failure: An advisor may not overwrite an approval already made by the employer.
- Violated state, rule, relationship, ownership, or eligibility condition: measure/approval state.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`

##### Failure 4
- Source discriminator: `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT`
- Failure condition: the advisor approval timestamp is already present when combined on-behalf approval is attempted.
- Why this is a business failure: Advisor approval is a single lifecycle decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: measure/approval state.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`

##### Failure 5
- Source discriminator: `GODKJENT_PAA_VEGNE_GRUNN_MAA_VELGES`
- Failure condition: either embedded reason object selects none.
- Why this is a business failure: auditability.
- Violated state, rule, relationship, ownership, or eligibility condition: justification.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneGrunn.java — GodkjentPaVegneGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvArbeidsgiverGrunn.java — GodkjentPaVegneAvArbeidsgiverGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.java — GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.valgtMinstEnGrunn`

##### Failure 6
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjennTilskuddsperiode`

##### Failure 7
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjennTilskuddsperiode`

##### Failure 8
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Why this is a business failure: Address-protected participant data makes the agreement ineligible for advisor approval in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 9
- Source discriminator: `SOMMERJOBB_FOR_GAMMEL_FRA_OPPSTARTDATO`
- Failure condition: For a summer-job agreement, the participant is over 30 at the agreement start date.
- Why this is a business failure: The participant fails the summer-job age eligibility rule at the actual start date.
- Violated state, rule, relationship, ownership, or eligibility condition: For a summer-job agreement, the participant is over 30 at the agreement start date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjennTilskuddsperiode`

##### Failure 10
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjennTilskuddsperiode`

##### Failure 11
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjennTilskuddsperiode`

##### Failure 12
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjennTilskuddsperiode`

##### Failure 13
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNSTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjennTilskuddsperiode`

##### Failure 14
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNSTILSKUDD_FEIL`
- Failure condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.godkjennTilskuddsperiode`

Business failure coverage:
- Source-ledger business branches for this invocation: `14`
- Documented failure blocks for this invocation: `14`
- Coverage result: `Complete`

#### Optional Step V1 - `get employer account number`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.hentBedriftKontonummer`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.hentBedriftKontonummer`

##### Failure 3
- Source discriminator: `KONTOREGISTER_FEIL_BEDRIFT_IKKE_FUNNET`
- Failure condition: account registry has no selected employer.
- Why this is a business failure: employer payment account is absent.
- Violated state, rule, relationship, ownership, or eligibility condition: employer financial eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/okonomi/KontoregisterServiceImpl.java — KontoregisterServiceImpl.hentKontonummer`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

#### Optional Step V2 - `list decision-maker agreements`

##### Failure 1
- Source discriminator: `NavEnhetIkkeFunnetException`
- Failure condition: decision-maker has no NAV units.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: unit assignment.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.finnGodkjenteAvtalerMedTilskuddsperiodestatusOgNavEnheterListe`

##### Failure 2
- Source discriminator: `NAV_ENHET_IKKE_FUNNET`
- Failure condition: the decision-maker has no NAV unit available for a work queue.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the decision-maker has no NAV unit available for a work queue.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.finnGodkjenteAvtalerMedTilskuddsperiodestatusOgNavEnheterListe`

Business failure coverage:
- Source-ledger business branches for this invocation: `2`
- Documented failure blocks for this invocation: `2`
- Coverage result: `Complete`

#### Optional Step V3 - `download agreement PDF`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.hentAvtalePdf`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.hentAvtalePdf`

##### Failure 3
- Source discriminator: `KAN_IKKE_LASTE_NED_PDF`
- Failure condition: the agreement has no advisor approval timestamp.
- Why this is a business failure: the PDF represents approved agreement terms and is not a valid business document before NAV approval.
- Violated state, rule, relationship, ownership, or eligibility condition: PDF generation requires an advisor-approved agreement.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.hentAvtalePdf`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

#### Step 2 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 5 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 7 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
If no current period exists, Step 6 dereferences null rather than raising a named business exception; this is an implementation defect, not an invented failure discriminator.

<a id="behavior-6"></a>
### Behavior 6: Reject a Pending Subsidy Period

Business goal:
Prepare a subsidy-backed agreement and record a reasoned decision-maker rejection instead of entering it.

Primary actor(s):
Advisor, participant, employer, and NAV decision-maker.

Workflow boundary rationale:
The setup through advisor approval is causally required; rejection is a materially different terminal state from approval, so it is split from Behavior 5.

Starting state:
No prior service state.

Terminal business outcome:
The current active period is `AVSLÅTT` with causes, explanation, decision-maker, and timestamp; the agreement remains unentered.

Required execution workflow:

#### Step 1: Create subsidy draft
- Use function `create advisor agreement` (`POST /avtaler`) with `body={deltakerFnr:"01039513753",bedriftNr:"111222333",tiltakstype:MIDLERTIDIG_LONNSTILSKUDD}`.
- Actor: NAV advisor.
- State before: No agreement exists.
- State transition or decision: Creates an advisor-owned wage-subsidy draft.
- Output/state after: A `PÅBEGYNT` draft exists.
- API-visible outputs: `201 Created`; `Location: /avtaler/{avtaleId}`; no response body, `sistEndret`, or `Last-Modified`.
- Handoff to later step: Step 2 extracts `{avtaleId}` from `Location`.

#### Step 2: Read initial freshness and generated identity
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with Step 1 id and `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Creation exposed no timestamp.
- State transition or decision: Reads the draft.
- Output/state after: The caller has `sistEndret` and `avtaleNr`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 3 uses response-body `sistEndret`.

#### Step 3: Calculate and persist subsidy content
- Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with Step 1 `{avtaleId}`, `innlogget-part=VEILEDER`, Step 2 response-body `sistEndret` as `If-Unmodified-Since`, and body fields `deltakerFornavn=Kari`, `deltakerEtternavn=Nordmann`, `deltakerTlf=41234567`, `bedriftNavn=Bedriften AS`, `arbeidsgiverFornavn=Arne`, `arbeidsgiverEtternavn=Arbeidsgiver`, `arbeidsgiverTlf=42345678`, `veilederFornavn=Vera`, `veilederEtternavn=Veileder`, `veilederTlf=43456789`, `startDato=2026-07-01`, `sluttDato=2026-12-31`, `oppfolging=Monthly follow-up`, and `tilrettelegging=Adapted workstation`, `stillingprosent=80`, `arbeidsoppgaver=Production work`, `stillingstittel=Operator`, `stillingStyrk08=8212`, `stillingKonseptId=321`, `antallDagerPerUke=5`, `arbeidsgiverKontonummer=12345678903`, `lonnstilskuddProsent=40`, `manedslonn=40000`, `feriepengesats=0.12`, `arbeidsgiveravgift=0.141`, `otpSats=0.02`, `harFamilietilknytning=false`, and `stillingstype=FAST`.
- Actor: NAV advisor.
- State before: Editable unapproved wage-subsidy draft.
- State transition or decision: Calculates derived wage values and creates active unhandled subsidy periods.
- Output/state after: A complete subsidy draft and its period schedule exist.
- API-visible outputs: `200 OK` with an empty body and `Last-Modified` set from the saved agreement's `sistEndret`.
- Handoff to later step: Step 4 uses `Last-Modified`.

#### Step 4: Record participant approval
- Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=DELTAKER` and Step 3 `Last-Modified` as `If-Unmodified-Since`.
- Actor: Participant.
- State before: Complete subsidy draft.
- State transition or decision: Records participant approval.
- Output/state after: Participant approval is persisted.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 5 refreshes the timestamp.

#### Step 5: Refresh after participant approval
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Participant-approved subsidy draft.
- State transition or decision: Reads the aggregate.
- Output/state after: The caller has current `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 6 uses response-body `sistEndret`.

#### Step 6: Record employer approval
- Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=ARBEIDSGIVER` and Step 5 response-body `sistEndret` as `If-Unmodified-Since`.
- Actor: Employer.
- State before: Participant-approved subsidy draft.
- State transition or decision: Records employer approval.
- Output/state after: Both external approvals exist.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 7 refreshes the timestamp.

#### Step 7: Refresh after employer approval
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Externally approved subsidy draft.
- State transition or decision: Reads the aggregate.
- Output/state after: The caller has current `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 8 uses response-body `sistEndret`.

#### Step 8: Approve as advisor and hand off
- Use function `approve agreement as advisor` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=VEILEDER` and Step 7 response-body `sistEndret` as `If-Unmodified-Since`.
- Actor: NAV advisor.
- State before: Both external approvals exist.
- State transition or decision: Records advisor approval without entering this decision-backed agreement.
- Output/state after: The first active unhandled period is ready for a decision-maker.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 9 uses the same `{avtaleId}`; it does not require a freshness header.

#### Step 9: Reject current subsidy period
- Use function `reject subsidy period` (`POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`) with Step 1 `{avtaleId}` and `body={avslagsårsaker:[FEIL_I_PROSENTSATS],avslagsforklaring:"Correct the wage basis"}`.
- Actor: NAV decision-maker.
- State before: Advisor-approved wage-subsidy agreement with an active unhandled current period.
- State transition or decision: Validates object-scoped access and rejection content; changes the current period from `UBEHANDLET` to `AVSLÅTT` and emits the rejection event.
- Output/state after: The current subsidy period is rejected; the agreement itself is not entered.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:

Alternative Path A:
- Branch point: No prior service state; employer initiates.
- Replaces: Required Steps 1 and 2.
- Retains or resumes: Required Steps 3 through 9 resume and reach the same decision rejection.

#### Alternative Step A1: Create employer subsidy draft
- Use function `create employer agreement` (`POST /avtaler/opprett-som-arbeidsgiver`) with `body={deltakerFnr:"01039513753",bedriftNr:"111222333",tiltakstype:MIDLERTIDIG_LONNSTILSKUDD}`.
- Actor: Employer.
- State before: No agreement exists.
- State transition or decision: Creates unassigned draft.
- Output/state after: Employer draft exists.
- API-visible outputs: `201 Created` with `Location`; no body or freshness value.
- Handoff to later step: A2 uses `Location` id.

#### Alternative Step A2: Assign advisor
- Use function `take over agreement as advisor` (`PUT /avtaler/{avtaleId}/overta`) with `{avtaleId}` parsed from Alternative Step A1 `Location` and no body.
- Actor: NAV advisor.
- State before: Unassigned draft.
- State transition or decision: Refreshes and assigns.
- Output/state after: Draft is advisor-owned.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: A3 reads freshness.

#### Alternative Step A3: Read freshness
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Assigned draft.
- State transition or decision: Reads aggregate.
- Output/state after: Current `sistEndret` is visible.
- API-visible outputs: `200 OK` `Avtale` body including `id` and `sistEndret`.
- Handoff to later step: Resume Step 3.

Alternative Path B:
- Branch point: After required Step 3.
- Replaces: Required Steps 4 through 8.
- Retains or resumes: Required Step 9 resumes after B2.

#### Alternative Step B1: Approve as employer first
- Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=ARBEIDSGIVER`, and Step 3 `Last-Modified` as `If-Unmodified-Since`.
- Actor: Employer.
- State before: Complete draft.
- State transition or decision: Records employer approval.
- Output/state after: Employer approval exists.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: B2 uses id.

#### Alternative Step B2: Approve for participant and advisor
- Use function `approve on behalf of participant` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av`) with Step 1 `{avtaleId}` and `body={ikkeBankId=true,reservert=false,digitalKompetanse=false,arenaMigreringDeltaker=false}`.
- Actor: NAV advisor.
- State before: Employer-approved draft.
- State transition or decision: Records participant/advisor approval.
- Output/state after: Ready for decision.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: Resume Step 9.

Alternative Path C:
- Branch point: After required Step 3.
- Replaces: Required Steps 4 through 8.
- Retains or resumes: Required Step 9 resumes after C2.

#### Alternative Step C1: Approve as participant
- Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=DELTAKER`, and Step 3 `Last-Modified` as `If-Unmodified-Since`.
- Actor: Participant.
- State before: Complete draft.
- State transition or decision: Records participant approval.
- Output/state after: Participant approval exists.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: C2 uses id.

#### Alternative Step C2: Approve for employer and advisor
- Use function `approve on behalf of employer` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver`) with Step 1 `{avtaleId}` and `body={klarerIkkeGiFaTilgang=true,vetIkkeHvemSomKanGiTilgang=false,farIkkeTilgangPersonvern=false,arenaMigreringArbeidsgiver=false}`.
- Actor: NAV advisor.
- State before: Participant-approved draft.
- State transition or decision: Records employer/advisor approval.
- Output/state after: Ready for decision.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: Resume Step 9.

Alternative Path D:
- Branch point: After required Step 3.
- Replaces: Required Steps 4 through 8.
- Retains or resumes: Required Step 9 resumes after D1.

#### Alternative Step D1: Approve for both parties and advisor
- Use function `approve on behalf of participant and employer` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`) with Step 1 `{avtaleId}` and `body={godkjentPaVegneAvArbeidsgiverGrunn:{klarerIkkeGiFaTilgang:true,vetIkkeHvemSomKanGiTilgang:false,farIkkeTilgangPersonvern:false,arenaMigreringArbeidsgiver:false},godkjentPaVegneAvDeltakerGrunn:{ikkeBankId:true,reservert:false,digitalKompetanse:false,arenaMigreringDeltaker:false}}`.
- Actor: NAV advisor.
- State before: Complete unapproved draft.
- State transition or decision: Records all three approvals and provenance.
- Output/state after: Ready for decision.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: Resume Step 9.

Optional verification/supporting steps:
- Optional Step V1: Use function `list decision-maker agreements` (`GET /avtaler/beslutter-liste`) with `tilskuddPeriodeStatus=UBEHANDLET`, `navEnhet=1234`, `sorteringskolonne=startDato`, `page=0`, `size=20`, and `sorteringOrder=ASC` to locate pending work.

Parameter, identity, and state bindings:
Same ids/identities as Behavior 5; rejection targets the aggregate's derived current period, not a request period id.

Business result and side effects:
Rejection does not enter the agreement. Event listeners can notify the responsible advisor/employer.

Constraints and invariants:
Only unhandled, decision-eligible periods may be rejected; explanation and at least one cause are mandatory.

Business failure branches:

#### Step 1 - `create advisor agreement`

##### Failure 1
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: the advisor lacks write access to the selected participant.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the advisor lacks write access to the selected participant.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`

##### Failure 2
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: PDL reports address-protection code 6 for the participant refreshed by the advisor path.
- Why this is a business failure: Address-protected participant data is a concrete eligibility bar for agreement processing in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: named access/protection/age/organization rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 3
- Source discriminator: `SOMMERJOBB_IKKE_GAMMEL_NOK`
- Failure condition: The participant is under 16 when the agreement aggregate is constructed.
- Why this is a business failure: The participant fails the service-wide minimum-age eligibility rule.
- Violated state, rule, relationship, ownership, or eligibility condition: named access/protection/age/organization rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

##### Failure 4
- Source discriminator: `SOMMERJOBB_FOR_GAMMEL`
- Failure condition: For a `SOMMERJOBB` agreement, the participant is over 30 on 1 January of the creation year.
- Why this is a business failure: The participant fails the summer-job age eligibility rule at creation.
- Violated state, rule, relationship, ownership, or eligibility condition: named access/protection/age/organization rule.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

##### Failure 5
- Source discriminator: `ENHET_FINNES_IKKE`
- Failure condition: the selected business or NAV unit cannot be resolved.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected business or NAV unit cannot be resolved.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`

##### Failure 6
- Source discriminator: `ENHET_ER_JURIDISK`
- Failure condition: the selected organization number identifies a legal entity instead of a virksomhet.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected organization number identifies a legal entity instead of a virksomhet.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`

##### Failure 7
- Source discriminator: `ENHET_ER_ORGLEDD`
- Failure condition: the selected organization number identifies an organizational link instead of a virksomhet.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected organization number identifies an organizational link instead of a virksomhet.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`

Business failure coverage:
- Source-ledger business branches for this invocation: `7`
- Documented failure blocks for this invocation: `7`
- Coverage result: `Complete`

#### Step 3 - `update agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale`

##### Failure 4
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale`

##### Failure 5
- Source discriminator: `START_ETTER_SLUTT`
- Failure condition: the proposed start date is after the proposed end date.
- Why this is a business failure: The requested agreement interval falls outside the applicable business date rule.
- Violated state, rule, relationship, ownership, or eligibility condition: the proposed start date is after the proposed end date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 6
- Source discriminator: `FORTIDLIG_STARTDATO`
- Failure condition: the proposed start is more than seven days in the past without after-registration authorization.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the proposed start is more than seven days in the past without after-registration authorization.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 7
- Source discriminator: `SLUTTDATO_GRENSE_NÅDD`
- Failure condition: the proposed end date is later than 2089-12-31.
- Why this is a business failure: The requested agreement interval falls outside the applicable business date rule.
- Violated state, rule, relationship, ownership, or eligibility condition: the proposed end date is later than 2089-12-31.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 8
- Source discriminator: `FEIL_OTP_SATS`
- Failure condition: `otpSats` is below 0.0 or above 0.3.
- Why this is a business failure: The pension rate is a financial input used to calculate the employer cost and subsidy amount.
- Violated state, rule, relationship, ownership, or eligibility condition: `otpSats` is below 0.0 or above 0.3.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/LonnstilskuddStrategy.java — LonnstilskuddStrategy.endre`

##### Failure 9
- Source discriminator: `LONNSTILSKUDD_PROSENT_ER_UGYLDIG`
- Failure condition: For the selected temporary wage-subsidy agreement, `lonnstilskuddProsent` is neither 40 nor 60.
- Why this is a business failure: The selected measure permits only its configured subsidy percentages.
- Violated state, rule, relationship, ownership, or eligibility condition: For the selected temporary wage-subsidy agreement, `lonnstilskuddProsent` is neither 40 nor 60.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/MidlertidigLonnstilskuddStrategy.java — MidlertidigLonnstilskuddStrategy.sjekktilskuddsprosentSats`

Business failure coverage:
- Source-ledger business branches for this invocation: `9`
- Documented failure blocks for this invocation: `9`
- Coverage result: `Complete`

#### Step 4 - `approve agreement as participant`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 5
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 6
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 7
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 8
- Source discriminator: `KAN_IKKE_GODKJENNE_DELTAKER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The participant approval timestamp is already set on the current agreement content.
- Why this is a business failure: Participant consent is a single decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Step 6 - `approve agreement as employer`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 5
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 6
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 7
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 8
- Source discriminator: `KAN_IKKE_GODKJENNE_ARBEIDSGIVER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The employer approval timestamp is already set on the current agreement content.
- Why this is a business failure: Employer consent is a single decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The employer approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Step 8 - `approve agreement as advisor`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 4
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Why this is a business failure: Address-protected participant data makes the agreement ineligible for advisor approval in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 5
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 6
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 7
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 8
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 9
- Source discriminator: `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The advisor approval timestamp is already set on the current agreement content.
- Why this is a business failure: Advisor approval is a single lifecycle decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The advisor approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 10
- Source discriminator: `VEILEDER_SKAL_GODKJENNE_SIST`
- Failure condition: participant or employer approval is still missing when the advisor attempts final approval.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: participant or employer approval is still missing when the advisor attempts final approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 11
- Source discriminator: `SOMMERJOBB_FOR_GAMMEL_FRA_OPPSTARTDATO`
- Failure condition: For a summer-job agreement, the participant is over 30 at the agreement start date.
- Why this is a business failure: The participant fails the summer-job age eligibility rule at the actual start date.
- Violated state, rule, relationship, ownership, or eligibility condition: For a summer-job agreement, the participant is over 30 at the agreement start date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 12
- Source discriminator: `DELTAKER_72_AAR`
- Failure condition: For a non-summer-job agreement, the participant is over 72 at the agreement end date.
- Why this is a business failure: The agreement would extend beyond the participant age allowed by the approval rule.
- Violated state, rule, relationship, ownership, or eligibility condition: For a non-summer-job agreement, the participant is over 72 at the agreement end date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 13
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 14
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 15
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNSTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

##### Failure 16
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNSTILSKUDD_FEIL`
- Failure condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForAvtalepart`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeileder`

Business failure coverage:
- Source-ledger business branches for this invocation: `16`
- Documented failure blocks for this invocation: `16`
- Coverage result: `Complete`

#### Step 9 - `reject subsidy period`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.avslåTilskuddsperiode`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.avslåTilskuddsperiode`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.avslåTilskuddsperiode`

##### Failure 4
- Source discriminator: `TILSKUDDSPERIODE_KAN_KUN_BEHANDLES_VED_INNGAATT_AVTALE`
- Failure condition: terminal agreement; missing advisor approval.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: decisionability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.avslåTilskuddsperiode`

##### Failure 5
- Source discriminator: `TILSKUDDSPERIODE_ER_ALLEREDE_BEHANDLET`
- Failure condition: the selected subsidy period is not in the required unhandled or annulled source state.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected subsidy period is not in the required unhandled or annulled source state.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/TilskuddPeriode.java — TilskuddPeriode.sjekkOmKanBehandles`

##### Failure 6
- Source discriminator: `TILSKUDDSPERIODE_BEHANDLE_FOR_TIDLIG`
- Failure condition: the decision date precedes the period's `kanBesluttesFom`.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the decision date precedes the period's `kanBesluttesFom`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/TilskuddPeriode.java — TilskuddPeriode.sjekkOmKanBehandles`

##### Failure 7
- Source discriminator: `TILSKUDDSPERIODE_AVSLAGSFORKLARING_PAAKREVD`
- Failure condition: blank explanation.
- Why this is a business failure: rejection must be reasoned.
- Violated state, rule, relationship, ownership, or eligibility condition: decision auditability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/TilskuddPeriode.java — TilskuddPeriode.avslå`

##### Failure 8
- Source discriminator: `TILSKUDDSPERIODE_INGEN_AVSLAGSAARSAKER`
- Failure condition: empty cause set.
- Why this is a business failure: rejection classification required.
- Violated state, rule, relationship, ownership, or eligibility condition: decision auditability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/TilskuddPeriode.java — TilskuddPeriode.avslå`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Alternative Step A1 - `create employer agreement`

##### Failure 1
- Source discriminator: `TilgangskontrollException("Har ikke tilgang på tiltak i valgt bedrift")`
- Failure condition: employer lacks selected company/measure right.
- Why this is a business failure: concrete employer eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: Altinn-derived relationship.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.opprettAvtale`

##### Failure 2
- Source discriminator: `SOMMERJOBB_IKKE_GAMMEL_NOK`
- Failure condition: The participant is under 16 when the agreement aggregate is constructed.
- Why this is a business failure: The participant fails the service-wide minimum-age eligibility rule.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant is under 16 when the agreement aggregate is constructed.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

##### Failure 3
- Source discriminator: `SOMMERJOBB_FOR_GAMMEL`
- Failure condition: For a `SOMMERJOBB` agreement, the participant is over 30 on 1 January of the creation year.
- Why this is a business failure: The participant fails the summer-job age eligibility rule at creation.
- Violated state, rule, relationship, ownership, or eligibility condition: For a `SOMMERJOBB` agreement, the participant is over 30 on 1 January of the creation year.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

#### Alternative Step A2 - `take over agreement as advisor`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.overtaAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.overtaAvtale`

##### Failure 3
- Source discriminator: `ER_ALLEREDE_VEILEDER`
- Failure condition: The logged-in advisor is already the agreement's assigned `veilederNavIdent`.
- Why this is a business failure: A takeover is a transfer of responsibility and has no business meaning when responsibility is already assigned to the caller.
- Violated state, rule, relationship, ownership, or eligibility condition: The logged-in advisor is already the agreement's assigned `veilederNavIdent`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.overtaAvtale`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.overtaAvtale`

##### Failure 5
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 6
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 7
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNSTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 8
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNSTILSKUDD_FEIL`
- Failure condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Alternative Step A3 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Alternative Step B1 - `approve agreement as employer`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 5
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 6
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 7
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 8
- Source discriminator: `KAN_IKKE_GODKJENNE_ARBEIDSGIVER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The employer approval timestamp is already set on the current agreement content.
- Why this is a business failure: Employer consent is a single decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The employer approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Alternative Step B2 - `approve on behalf of participant`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 2
- Source discriminator: `DELTAKER_HAR_GODKJENT`
- Failure condition: The participant has already approved, so an advisor cannot replace that decision with an on-behalf approval.
- Why this is a business failure: An advisor may not overwrite an approval already made by the participant.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant has already approved, so an advisor cannot replace that decision with an on-behalf approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 3
- Source discriminator: `ARBEIDSGIVER_SKAL_GODKJENNE_FOER_VEILEDER`
- Failure condition: The employer approval timestamp is absent when the advisor tries to approve on behalf of the participant.
- Why this is a business failure: The on-behalf participant path still requires prior employer consent.
- Violated state, rule, relationship, ownership, or eligibility condition: The employer approval timestamp is absent when the advisor tries to approve on behalf of the participant.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 4
- Source discriminator: `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The advisor approval timestamp is already set on the current agreement content.
- Why this is a business failure: Advisor approval is a single lifecycle decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The advisor approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 5
- Source discriminator: `GODKJENT_PAA_VEGNE_GRUNN_MAA_VELGES`
- Failure condition: Every boolean reason field in the applicable on-behalf reason object is `false`.
- Why this is a business failure: An on-behalf approval must retain an auditable business reason.
- Violated state, rule, relationship, ownership, or eligibility condition: Every boolean reason field in the applicable on-behalf reason object is `false`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneGrunn.java — GodkjentPaVegneGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvArbeidsgiverGrunn.java — GodkjentPaVegneAvArbeidsgiverGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.java — GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.valgtMinstEnGrunn`

##### Failure 6
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 7
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Why this is a business failure: Address-protected participant data makes the agreement ineligible for advisor approval in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 8
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 9
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 10
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNSTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 11
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNSTILSKUDD_FEIL`
- Failure condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 12
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 13
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

##### Failure 14
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgDeltaker`

Business failure coverage:
- Source-ledger business branches for this invocation: `14`
- Documented failure blocks for this invocation: `14`
- Coverage result: `Complete`

#### Alternative Step C1 - `approve agreement as participant`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 3
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 5
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 6
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 7
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 8
- Source discriminator: `KAN_IKKE_GODKJENNE_DELTAKER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The participant approval timestamp is already set on the current agreement content.
- Why this is a business failure: Participant consent is a single decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Alternative Step C2 - `approve on behalf of employer`

##### Failure 1
- Source discriminator: `GODKJENN_PAA_VEGNE_AV_FEIL_TILTAKSTYPE`
- Failure condition: The agreement measure is not `SOMMERJOBB`, `MIDLERTIDIG_LONNSTILSKUDD`, or `VARIG_LONNSTILSKUDD`.
- Why this is a business failure: Employer on-behalf approval is limited to the decision-backed and summer-job measures named by the domain rule.
- Violated state, rule, relationship, ownership, or eligibility condition: The agreement measure is not `SOMMERJOBB`, `MIDLERTIDIG_LONNSTILSKUDD`, or `VARIG_LONNSTILSKUDD`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`

##### Failure 2
- Source discriminator: `ARBEIDSGIVER_HAR_GODKJENT`
- Failure condition: The employer has already approved, so the advisor cannot replace that decision with an on-behalf approval.
- Why this is a business failure: An advisor may not overwrite an approval already made by the employer.
- Violated state, rule, relationship, ownership, or eligibility condition: The employer has already approved, so the advisor cannot replace that decision with an on-behalf approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`

##### Failure 3
- Source discriminator: `DELTAKER_SKAL_GODKJENNE_FOER_VEILEDER`
- Failure condition: The participant approval timestamp is absent when the advisor tries to approve on behalf of the employer.
- Why this is a business failure: The on-behalf employer path still requires prior participant consent.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant approval timestamp is absent when the advisor tries to approve on behalf of the employer.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`

##### Failure 4
- Source discriminator: `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The advisor approval timestamp is already set on the current agreement content.
- Why this is a business failure: Advisor approval is a single lifecycle decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The advisor approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgArbeidsgiver`

##### Failure 5
- Source discriminator: `GODKJENT_PAA_VEGNE_GRUNN_MAA_VELGES`
- Failure condition: Every boolean reason field in the applicable on-behalf reason object is `false`.
- Why this is a business failure: An on-behalf approval must retain an auditable business reason.
- Violated state, rule, relationship, ownership, or eligibility condition: Every boolean reason field in the applicable on-behalf reason object is `false`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneGrunn.java — GodkjentPaVegneGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvArbeidsgiverGrunn.java — GodkjentPaVegneAvArbeidsgiverGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.java — GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.valgtMinstEnGrunn`

##### Failure 6
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

##### Failure 7
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

##### Failure 8
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Why this is a business failure: Address-protected participant data makes the agreement ineligible for advisor approval in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 9
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

##### Failure 10
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

##### Failure 11
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNSTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

##### Failure 12
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNSTILSKUDD_FEIL`
- Failure condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.godkjennForVeilederOgArbeidsgiver`

Business failure coverage:
- Source-ledger business branches for this invocation: `12`
- Documented failure blocks for this invocation: `12`
- Coverage result: `Complete`

#### Alternative Step D1 - `approve on behalf of participant and employer`

##### Failure 1
- Source discriminator: `GODKJENN_PAA_VEGNE_AV_FEIL_TILTAKSTYPE`
- Failure condition: The agreement measure is not `SOMMERJOBB`, `MIDLERTIDIG_LONNSTILSKUDD`, or `VARIG_LONNSTILSKUDD`.
- Why this is a business failure: Employer on-behalf approval is limited to the decision-backed and summer-job measures named by the domain rule.
- Violated state, rule, relationship, ownership, or eligibility condition: The agreement measure is not `SOMMERJOBB`, `MIDLERTIDIG_LONNSTILSKUDD`, or `VARIG_LONNSTILSKUDD`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`

##### Failure 2
- Source discriminator: `DELTAKER_HAR_GODKJENT`
- Failure condition: The participant has already approved, so an advisor cannot replace that decision with an on-behalf approval.
- Why this is a business failure: An advisor may not overwrite an approval already made by the participant.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant has already approved, so an advisor cannot replace that decision with an on-behalf approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`

##### Failure 3
- Source discriminator: `ARBEIDSGIVER_HAR_GODKJENT`
- Failure condition: The employer has already approved, so the advisor cannot replace that decision with an on-behalf approval.
- Why this is a business failure: An advisor may not overwrite an approval already made by the employer.
- Violated state, rule, relationship, ownership, or eligibility condition: The employer has already approved, so the advisor cannot replace that decision with an on-behalf approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`

##### Failure 4
- Source discriminator: `KAN_IKKE_GODKJENNE_VEILEDER_HAR_ALLEREDE_GODKJENT`
- Failure condition: The advisor approval timestamp is already set on the current agreement content.
- Why this is a business failure: Advisor approval is a single lifecycle decision bound to the current content version.
- Violated state, rule, relationship, ownership, or eligibility condition: The advisor approval timestamp is already set on the current agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForVeilederOgDeltakerOgArbeidsgiver`

##### Failure 5
- Source discriminator: `GODKJENT_PAA_VEGNE_GRUNN_MAA_VELGES`
- Failure condition: Every boolean reason field in the applicable on-behalf reason object is `false`.
- Why this is a business failure: An on-behalf approval must retain an auditable business reason.
- Violated state, rule, relationship, ownership, or eligibility condition: Every boolean reason field in the applicable on-behalf reason object is `false`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneGrunn.java — GodkjentPaVegneGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvArbeidsgiverGrunn.java — GodkjentPaVegneAvArbeidsgiverGrunn.valgtMinstEnGrunn`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.java — GodkjentPaVegneAvDeltakerOgArbeidsgiverGrunn.valgtMinstEnGrunn`

##### Failure 6
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.avslåTilskuddsperiode`

##### Failure 7
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.avslåTilskuddsperiode`

##### Failure 8
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Why this is a business failure: Address-protected participant data makes the agreement ineligible for advisor approval in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 9
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.avslåTilskuddsperiode`

##### Failure 10
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.avslåTilskuddsperiode`

##### Failure 11
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNSTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.avslåTilskuddsperiode`

##### Failure 12
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNSTILSKUDD_FEIL`
- Failure condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.avslåTilskuddsperiode`

Business failure coverage:
- Source-ledger business branches for this invocation: `12`
- Documented failure blocks for this invocation: `12`
- Coverage result: `Complete`

#### Optional Step V1 - `list decision-maker agreements`

##### Failure 1
- Source discriminator: `NAV_ENHET_IKKE_FUNNET`
- Failure condition: decision-maker has no NAV units.
- Why this is a business failure: no organizational decision scope.
- Violated state, rule, relationship, ownership, or eligibility condition: unit assignment.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.hentNavEnheter`

Business failure coverage:
- Source-ledger business branches for this invocation: `1`
- Documented failure blocks for this invocation: `1`
- Coverage result: `Complete`

#### Step 2 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 5 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 7 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
The source does not enforce a decision-maker-different-from-advisor rule for rejection, only for approval.

<a id="behavior-7"></a>
### Behavior 7: Return a Rejected Subsidy Period for Re-decision

Business goal:
Replace active rejected periods with fresh unhandled copies so corrected agreement information can be decided again.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
An existing rejection is a stable outcome of Behavior 6. Requeueing it is a new correction goal; no API predecessor belongs to this goal and the function itself reaches the requeued state, satisfying the one-step rule.

Starting state:
Existing advisor-accessible agreement with one or more active `AVSLÅTT` periods; this state is created by the separate rejection workflow.

Terminal business outcome:
Each active rejected period is inactive and an active `UBEHANDLET` replacement exists.

Required execution workflow:

#### Step 1: Requeue rejected period(s)
- Use function `send rejected subsidy period back` (`POST /avtaler/{avtaleId}/send-tilbake-til-beslutter`) with the pre-existing rejected agreement's known `{avtaleId}` and no body.
- Actor: advisor.
- State before: active rejected period(s).
- State transition or decision: deactivate and clone each as unhandled with new id.
- Output/state after: pending decision state.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: a later decision may use Behavior 5 Step 6 or Behavior 6 Step 6.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
The agreement id selects all active rejected periods; period attributes and sequence are copied internally.

Business result and side effects:
No event is registered by `sendTilbakeTilBeslutter`; only persisted active flags/new child rows change.

Constraints and invariants:
If no active rejected period exists, the operation is a valid no-op.

Business failure branches:

#### Step 1 - `send rejected subsidy period back`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement absent.
- Why this is a business failure: correction target missing.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.sendTilbakeTilBeslutter`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: advisor lacks participant access.
- Why this is a business failure: correction is agreement-scoped.
- Violated state, rule, relationship, ownership, or eligibility condition: advisor-participant relationship.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sendTilbakeTilBeslutter`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: agreement annulled/interrupted.
- Why this is a business failure: terminal aggregate cannot re-enter decisioning.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sendTilbakeTilBeslutter`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

Implementation notes:
The replacement receives a new UUID but retains sequence, amount, percentage, and date interval (`TilskuddPeriode.deaktiverOgLagNyUbehandlet`).

<a id="behavior-8"></a>
### Behavior 8: Assign an Employer-Created Agreement to an Advisor

Business goal:
Move an employer-started unassigned draft into a named advisor's responsibility.

Primary actor(s):
Employer initiator and NAV advisor.

Workflow boundary rationale:
Employer creation is the API-realizable producer of `erUfordelt`; takeover consumes that state and is the meaningful ownership outcome.

Starting state:
No prior service state.

Terminal business outcome:
Agreement has `veilederNavIdent`, refreshed follow-up data, recalculated wage data/periods, and an assignment event.

Required execution workflow:

#### Step 1: Create unassigned employer draft
- Use function `create employer agreement` (`POST /avtaler/opprett-som-arbeidsgiver`) with `body={deltakerFnr:"01039513753",bedriftNr:"111222333",tiltakstype:MIDLERTIDIG_LONNSTILSKUDD}`.
- Actor: employer.
- State before: no agreement.
- State transition or decision: create `opprettetAvArbeidsgiver=true`, advisor null.
- Output/state after: `avtaleId`.
- API-visible outputs: `201 Created`; `Location: /avtaler/{avtaleId}`; no response body, `sistEndret`, or `Last-Modified`.
- Handoff to later step: advisor consumes id.

#### Step 2: Take ownership
- Use function `take over agreement as advisor` (`PUT /avtaler/{avtaleId}/overta`) with Step 1 id.
- Actor: NAV advisor.
- State before: unassigned accessible draft.
- State transition or decision: refresh Arena follow-up, assign advisor, recalculate, generate periods when previously unassigned.
- Output/state after: assigned draft and `AvtaleOpprettetAvArbeidsgiverErFordelt` event.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: separate entry workflow can continue.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
`Location` from Step 1 yields id; logged-in advisor ident becomes `veilederNavIdent`.

Business result and side effects:
Assignment, possible calculation/period replacement, `sistEndret`, and event persist atomically with aggregate save.

Constraints and invariants:
Advisor needs participant access and valid Arena qualification; assigning the existing owner is rejected.

Business failure branches:

#### Step 1 - `create employer agreement`

##### Failure 1
- Source discriminator: `TilgangskontrollException("Har ikke tilgang på tiltak i valgt bedrift")`
- Failure condition: employer lacks company/measure right.
- Why this is a business failure: concrete employer eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: Altinn-derived relationship.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.opprettAvtale`

##### Failure 2
- Source discriminator: `required participant/company null guard`
- Failure condition: party identity absent.
- Why this is a business failure: aggregate relationship cannot be created.
- Violated state, rule, relationship, ownership, or eligibility condition: required domain identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.opprettAvtaleSomArbeidsgiver`

##### Failure 3
- Source discriminator: `SOMMERJOBB_IKKE_GAMMEL_NOK`
- Failure condition: participant under 16.
- Why this is a business failure: age eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: minimum age.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

##### Failure 4
- Source discriminator: `SOMMERJOBB_FOR_GAMMEL`
- Failure condition: summer-job participant over January age limit.
- Why this is a business failure: measure age eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: maximum age.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

Business failure coverage:
- Source-ledger business branches for this invocation: `4`
- Documented failure blocks for this invocation: `4`
- Coverage result: `Complete`

#### Step 2 - `take over agreement as advisor`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: id absent.
- Why this is a business failure: assignment target missing.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.settNyVeilederPåAvtale`

##### Failure 2
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.hentOppfølgingFraArena`; `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 3
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.hentOppfølgingFraArena`; `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 4
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: a temporary-wage-subsidy, summer-job, or mentor agreement has a qualification group outside the implemented eligible set.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: follow-up qualification.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.hentOppfølgingFraArena`; `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 5
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNTILSKUDD_FEIL`
- Failure condition: a permanent-wage-subsidy agreement has a qualification group outside the implemented eligible set.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: follow-up qualification.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.hentOppfølgingFraArena`; `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 6
- Source discriminator: `TilgangskontrollException`
- Failure condition: advisor lacks participant access.
- Why this is a business failure: assignment scope.
- Violated state, rule, relationship, ownership, or eligibility condition: advisor-participant relationship.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`

##### Failure 7
- Source discriminator: `ER_ALLEREDE_VEILEDER`
- Failure condition: caller already owns agreement.
- Why this is a business failure: duplicate assignment.
- Violated state, rule, relationship, ownership, or eligibility condition: ownership transition.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.overtaAvtale`

##### Failure 8
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: terminal lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: assignability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.overtaAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

Implementation notes:
The Arena refresh occurs before the object-access check inside `overtaAvtale`; a failed access decision can therefore occur after an upstream business lookup but before persistence.

<a id="behavior-9"></a>
### Behavior 9: Reopen and Revise a Partially Approved Draft

Business goal:
Withdraw approvals from an unentered draft, revise the agreed content, and return it to approval-ready state.

Primary actor(s):
Advisor or employer, plus a party who previously approved.

Workflow boundary rationale:
Creation/update produces a draft; an approval makes it immutable; revocation causally re-enables update; the second update is the correction outcome.

Starting state:
No prior service state.

Terminal business outcome:
Revised draft is persisted with approvals cleared and awaits fresh approvals.

Required execution workflow:

#### Step 1: Create draft
- Use function `create advisor agreement` (`POST /avtaler`) with `body={deltakerFnr:"01039513753",bedriftNr:"111222333",tiltakstype:ARBEIDSTRENING}`.
- Actor: NAV advisor.
- State before: No agreement exists.
- State transition or decision: Creates the draft.
- Output/state after: A `PÅBEGYNT` draft exists.
- API-visible outputs: `201 Created`; `Location: /avtaler/{avtaleId}`; no response body, `sistEndret`, or `Last-Modified`.
- Handoff to later step: Step 2 extracts the id.

#### Step 2: Read initial freshness
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with Step 1 id and `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Creation returned no timestamp.
- State transition or decision: Reads the draft.
- Output/state after: The caller has `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 3 uses it.

#### Step 3: Complete original content
- Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with Step 1 `{avtaleId}`, `innlogget-part=VEILEDER`, Step 2 response-body `sistEndret` as `If-Unmodified-Since`, and body fields `deltakerFornavn=Kari`, `deltakerEtternavn=Nordmann`, `deltakerTlf=41234567`, `bedriftNavn=Bedriften AS`, `arbeidsgiverFornavn=Arne`, `arbeidsgiverEtternavn=Arbeidsgiver`, `arbeidsgiverTlf=42345678`, `veilederFornavn=Vera`, `veilederEtternavn=Veileder`, `veilederTlf=43456789`, `startDato=2026-07-01`, `sluttDato=2026-12-31`, `oppfolging=Monthly follow-up`, and `tilrettelegging=Adapted workstation`, `stillingprosent=80`, `arbeidsoppgaver=Warehouse tasks`, `stillingstittel=Assistant`, `stillingStyrk08=9334`, `stillingKonseptId=123`, `antallDagerPerUke=5`, and `maal=[{beskrivelse=Original goal,kategori=ARBEIDSERFARING}]`.
- Actor: NAV advisor.
- State before: Editable unapproved draft.
- State transition or decision: Persists complete original terms.
- Output/state after: The draft is approval-ready.
- API-visible outputs: `200 OK` with an empty body and `Last-Modified` set from the saved agreement's `sistEndret`.
- Handoff to later step: Step 4 uses `Last-Modified`.

#### Step 4: Record a partial approval
- Use function `approve agreement as participant` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=DELTAKER` and Step 3 `Last-Modified` as `If-Unmodified-Since`.
- Actor: Participant.
- State before: Complete unapproved draft.
- State transition or decision: Records participant approval.
- Output/state after: The content is signed by the participant.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 5 needs only the agreement id.

#### Step 5: Revoke approvals
- Use function `revoke approvals` (`POST /avtaler/{avtaleId}/opphev-godkjenninger`) with Step 1 id and `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: A party approval exists; the agreement is not entered.
- State transition or decision: Clears participant, employer, and advisor approval fields and emits the revocation event.
- Output/state after: The draft is editable again.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: Step 6 must read the timestamp because Step 5 returns none.

#### Step 6: Refresh after revocation
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Approvals have been cleared.
- State transition or decision: Reads the reopened draft.
- Output/state after: The caller has the post-revocation `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 7 uses it.

#### Step 7: Persist revised content
- Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with Step 1 `{avtaleId}`, `innlogget-part=VEILEDER`, Step 6 response-body `sistEndret` as `If-Unmodified-Since`, and `body={deltakerFornavn:"Kari",deltakerEtternavn:"Nordmann",deltakerTlf:"41234567",bedriftNavn:"Bedriften AS",arbeidsgiverFornavn:"Arne",arbeidsgiverEtternavn:"Arbeidsgiver",arbeidsgiverTlf:"42345678",veilederFornavn:"Vera",veilederEtternavn:"Veileder",veilederTlf:"43456789",startDato:"2026-07-01",sluttDato:"2026-12-31",oppfolging:"Monthly follow-up",tilrettelegging:"Adapted workstation",stillingprosent:80,arbeidsoppgaver:"Warehouse tasks",stillingstittel:"Assistant",stillingStyrk08:9334,stillingKonseptId:123,antallDagerPerUke:5,maal:[{beskrivelse:"Corrected goal",kategori:ARBEIDSERFARING}]}`.
- Actor: NAV advisor.
- State before: Reopened editable draft.
- State transition or decision: Validates and persists the corrected current version.
- Output/state after: A revised approval-ready draft exists.
- API-visible outputs: `200 OK` with an empty body and `Last-Modified` set from the saved agreement's `sistEndret`.
- Handoff to later step: None.

Alternative valid workflow paths:

Alternative Path A:
- Branch point: After required Step 3.
- Replaces: Required Steps 4 and 5.
- Retains or resumes: Required Steps 6 and 7 resume after A2.

#### Alternative Step A1: Approve as employer
- Use function `approve agreement as employer` (`POST /avtaler/{avtaleId}/godkjenn`) with Step 1 `{avtaleId}`, `innlogget-part=ARBEIDSGIVER`, and Step 3 `Last-Modified` as `If-Unmodified-Since`.
- Actor: Employer.
- State before: Complete draft.
- State transition or decision: Records employer approval.
- Output/state after: Partially approved draft.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: A2 uses id.

#### Alternative Step A2: Revoke as employer
- Use function `revoke approvals` (`POST /avtaler/{avtaleId}/opphev-godkjenninger`) with `innlogget-part=ARBEIDSGIVER`.
- Actor: Employer.
- State before: Employer-approved unentered draft.
- State transition or decision: Clears approvals.
- Output/state after: Draft is editable.
- API-visible outputs: `200 OK` with no body or refreshed timestamp.
- Handoff to later step: Resume Step 6.

Optional verification/supporting steps:
- Optional Step V1: Use function `dry-run agreement update` (`PUT /avtaler/{avtaleId}/dry-run`) after Step 6 and before Step 7, with Step 1 `Location` `{avtaleId}`, Step 6 response-body `sistEndret` as `If-Unmodified-Since`, `innlogget-part=VEILEDER`, and the Step 7 corrected `EndreAvtale` body.

Parameter, identity, and state bindings:
All steps reuse id; timestamps must be refreshed after approval/revocation.

Business result and side effects:
Revocation emits role-specific old-value event; revised subsidy drafts may remove/recreate unhandled periods.

Constraints and invariants:
At least one approval must exist; entered agreements cannot be reopened; employer cannot revoke after advisor approval.

Business failure branches:

#### Step 1 - `create advisor agreement`

##### Failure 1
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: the advisor lacks write access to the selected participant.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the advisor lacks write access to the selected participant.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettAvtale`

##### Failure 2
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: PDL reports address-protection code 6 for the participant refreshed by the advisor path.
- Why this is a business failure: Address-protected participant data is a concrete eligibility bar for agreement processing in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: PDL reports address-protection code 6 for the participant refreshed by the advisor path.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

Business failure coverage:
- Source-ledger business branches for this invocation: `2`
- Documented failure blocks for this invocation: `2`
- Coverage result: `Complete`

#### Step 3 - `update agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: `{avtaleId}` does not resolve to an agreement.
- Why this is a business failure: the requested update has no draft aggregate to change.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected agreement must exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException("Ikke tilgang til avtale")`
- Failure condition: the advisor lacks the object-scoped participant relationship required for the selected agreement.
- Why this is a business failure: the update changes a concrete participant-owned agreement.
- Violated state, rule, relationship, ownership, or eligibility condition: the advisor must have access to this agreement's participant.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgangOgEndreAvtale; Avtalepart.sjekkTilgang`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: a terminal agreement cannot return to editable draft state.
- Violated state, rule, relationship, ownership, or eligibility condition: draft editing requires a non-terminal agreement.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale; Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`

##### Failure 4
- Source discriminator: `TilgangskontrollException("Godkjenninger må oppheves før avtalen kan endres.")`
- Failure condition: participant, employer, or advisor approval is still recorded on the current content.
- Why this is a business failure: signed terms may not be replaced until their approvals are revoked.
- Violated state, rule, relationship, ownership, or eligibility condition: all approvals must be absent before current content changes.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale; Avtale.sjekkOmAvtalenKanEndres`

##### Failure 5
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: `If-Unmodified-Since` is earlier than persisted `sistEndret`.
- Why this is a business failure: the update must bind to the exact content version the actor reviewed.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller's freshness value must not be stale.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale; Avtale.sjekkSistEndret`

##### Failure 6
- Source discriminator: `START_ETTER_SLUTT`
- Failure condition: the proposed `startDato` is after the proposed `sluttDato`.
- Why this is a business failure: the requested agreement interval is internally impossible.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement start must not follow agreement end.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 7
- Source discriminator: `FORTIDLIG_STARTDATO`
- Failure condition: both dates are present, the agreement is not entered or after-registration-authorized, and `startDato.plusDays(7)` is before the current date.
- Why this is a business failure: the draft is too far backdated for ordinary registration.
- Violated state, rule, relationship, ownership, or eligibility condition: an unentered agreement may start more than seven days in the past only after after-registration authorization.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 8
- Source discriminator: `SLUTTDATO_GRENSE_NÅDD`
- Failure condition: the proposed `sluttDato` is after `2089-12-31`.
- Why this is a business failure: the requested agreement exceeds the service's maximum business date.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement end may not be later than 2089-12-31.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 9
- Source discriminator: `VARIGHET_FOR_LANG_ARBEIDSTRENING`
- Failure condition: the work-training end date is later than `startDato.plusMonths(18).minusDays(1)`.
- Why this is a business failure: the requested work-training duration exceeds its measure limit.
- Violated state, rule, relationship, ownership, or eligibility condition: work training may last at most 18 months inclusive.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/ArbeidstreningStartOgSluttDatoStrategy.java — ArbeidstreningStartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 10
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: the advisor-side post-update PDL refresh reports address-protection code 6 for the participant.
- Why this is a business failure: the concrete participant is ineligible for continued advisor processing.
- Violated state, rule, relationship, ownership, or eligibility condition: address-protected participant data cannot be processed through this advisor update workflow.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreAvtale; Veileder.oppdaterePersondataFraPdlVedEndreAvtale; Veileder.sjekkKode6`; `src/main/java/no/nav/tag/tiltaksgjennomforing/exceptions/KanIkkeOppretteAvtalePåKode6Exception.java — KanIkkeOppretteAvtalePåKode6Exception constructor`

Business failure coverage:
- Source-ledger business branches for this invocation: `10`
- Documented failure blocks for this invocation: `10`
- Coverage result: `Complete`
#### Step 4 - `approve agreement as participant`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 2
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

##### Failure 4
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.godkjennAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForDeltaker`

Business failure coverage:
- Source-ledger business branches for this invocation: `4`
- Documented failure blocks for this invocation: `4`
- Coverage result: `Complete`

#### Step 5 - `revoke approvals`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.opphevGodkjenninger`

##### Failure 2
- Source discriminator: `KAN_IKKE_OPPHEVE`
- Failure condition: the employer attempts revocation after advisor approval exists.
- Why this is a business failure: after NAV approval, the employer may no longer reopen the agreement's consent sequence.
- Violated state, rule, relationship, ownership, or eligibility condition: employer revocation is allowed only before advisor approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Arbeidsgiver.java — Arbeidsgiver.kanOppheveGodkjenninger`

##### Failure 3
- Source discriminator: `KAN_IKKE_OPPHEVE`
- Failure condition: no participant, employer, or advisor approval exists to revoke.
- Why this is a business failure: revocation requires an existing consent decision; otherwise it would record a reversal with no business subject.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one party approval must exist before approvals can be revoked.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.opphevGodkjenninger`

##### Failure 4
- Source discriminator: `KAN_IKKE_OPPHEVE_GODKJENNINGER_VED_INNGAATT_AVTALE`
- Failure condition: agreement already entered.
- Why this is a business failure: stable entered milestone.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle finality.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.opphevGodkjenninger`

Business failure coverage:
- Source-ledger business branches for this invocation: `4`
- Documented failure blocks for this invocation: `4`
- Coverage result: `Complete`

#### Step 7 - `update agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: `{avtaleId}` does not resolve to an agreement.
- Why this is a business failure: the requested update has no draft aggregate to change.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected agreement must exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException("Ikke tilgang til avtale")`
- Failure condition: the advisor lacks the object-scoped participant relationship required for the selected agreement.
- Why this is a business failure: the update changes a concrete participant-owned agreement.
- Violated state, rule, relationship, ownership, or eligibility condition: the advisor must have access to this agreement's participant.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgangOgEndreAvtale; Avtalepart.sjekkTilgang`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: a terminal agreement cannot return to editable draft state.
- Violated state, rule, relationship, ownership, or eligibility condition: draft editing requires a non-terminal agreement.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale; Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`

##### Failure 4
- Source discriminator: `TilgangskontrollException("Godkjenninger må oppheves før avtalen kan endres.")`
- Failure condition: participant, employer, or advisor approval is still recorded on the current content.
- Why this is a business failure: signed terms may not be replaced until their approvals are revoked.
- Violated state, rule, relationship, ownership, or eligibility condition: all approvals must be absent before current content changes.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale; Avtale.sjekkOmAvtalenKanEndres`

##### Failure 5
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: `If-Unmodified-Since` is earlier than persisted `sistEndret`.
- Why this is a business failure: the update must bind to the exact content version the actor reviewed.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller's freshness value must not be stale.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale; Avtale.sjekkSistEndret`

##### Failure 6
- Source discriminator: `START_ETTER_SLUTT`
- Failure condition: the proposed `startDato` is after the proposed `sluttDato`.
- Why this is a business failure: the requested agreement interval is internally impossible.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement start must not follow agreement end.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 7
- Source discriminator: `FORTIDLIG_STARTDATO`
- Failure condition: both dates are present, the agreement is not entered or after-registration-authorized, and `startDato.plusDays(7)` is before the current date.
- Why this is a business failure: the draft is too far backdated for ordinary registration.
- Violated state, rule, relationship, ownership, or eligibility condition: an unentered agreement may start more than seven days in the past only after after-registration authorization.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 8
- Source discriminator: `SLUTTDATO_GRENSE_NÅDD`
- Failure condition: the proposed `sluttDato` is after `2089-12-31`.
- Why this is a business failure: the requested agreement exceeds the service's maximum business date.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement end may not be later than 2089-12-31.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 9
- Source discriminator: `VARIGHET_FOR_LANG_ARBEIDSTRENING`
- Failure condition: the work-training end date is later than `startDato.plusMonths(18).minusDays(1)`.
- Why this is a business failure: the requested work-training duration exceeds its measure limit.
- Violated state, rule, relationship, ownership, or eligibility condition: work training may last at most 18 months inclusive.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/ArbeidstreningStartOgSluttDatoStrategy.java — ArbeidstreningStartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 10
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: the advisor-side post-update PDL refresh reports address-protection code 6 for the participant.
- Why this is a business failure: the concrete participant is ineligible for continued advisor processing.
- Violated state, rule, relationship, ownership, or eligibility condition: address-protected participant data cannot be processed through this advisor update workflow.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreAvtale; Veileder.oppdaterePersondataFraPdlVedEndreAvtale; Veileder.sjekkKode6`; `src/main/java/no/nav/tag/tiltaksgjennomforing/exceptions/KanIkkeOppretteAvtalePåKode6Exception.java — KanIkkeOppretteAvtalePåKode6Exception constructor`

Business failure coverage:
- Source-ledger business branches for this invocation: `10`
- Documented failure blocks for this invocation: `10`
- Coverage result: `Complete`
#### Alternative Step A1 - `approve agreement as employer`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 2
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the supplied freshness timestamp is missing or older than persisted `sistEndret`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 4
- Source discriminator: `ALT_MA_VAERE_FYLT_UT`
- Failure condition: at least one subtype-required agreement field is missing.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one subtype-required agreement field is missing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 5
- Source discriminator: `MANGLER_BEREGNING`
- Failure condition: a decision-backed measure lacks its calculated subsidy amount.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: a decision-backed measure lacks its calculated subsidy amount.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 6
- Source discriminator: `MANGLER_VEILEDER_PÅ_AVTALE`
- Failure condition: the agreement has no assigned advisor.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement has no assigned advisor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

##### Failure 7
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.godkjennForArbeidsgiver`

Business failure coverage:
- Source-ledger business branches for this invocation: `7`
- Documented failure blocks for this invocation: `7`
- Coverage result: `Complete`

#### Alternative Step A2 - `revoke approvals`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.opphevGodkjenninger`

##### Failure 2
- Source discriminator: `KAN_IKKE_OPPHEVE`
- Failure condition: no participant, employer, or advisor approval exists to revoke.
- Why this is a business failure: revocation requires an existing consent decision; otherwise it would record a reversal with no business subject.
- Violated state, rule, relationship, ownership, or eligibility condition: at least one party approval must exist before approvals can be revoked.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.opphevGodkjenninger`

##### Failure 3
- Source discriminator: `KAN_IKKE_OPPHEVE_GODKJENNINGER_VED_INNGAATT_AVTALE`
- Failure condition: agreement already entered.
- Why this is a business failure: stable entered milestone.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle finality.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.opphevGodkjenninger`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

#### Optional Step V1 - `dry-run agreement update`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: `{avtaleId}` does not resolve to an agreement.
- Why this is a business failure: the requested update has no draft aggregate to change.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected agreement must exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException("Ikke tilgang til avtale")`
- Failure condition: the advisor lacks the object-scoped participant relationship required for the selected agreement.
- Why this is a business failure: the update changes a concrete participant-owned agreement.
- Violated state, rule, relationship, ownership, or eligibility condition: the advisor must have access to this agreement's participant.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgangOgEndreAvtale; Avtalepart.sjekkTilgang`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: a terminal agreement cannot return to editable draft state.
- Violated state, rule, relationship, ownership, or eligibility condition: draft editing requires a non-terminal agreement.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale; Avtale.sjekkAtIkkeAvtaleErAnnullertEllerAvbrutt`

##### Failure 4
- Source discriminator: `TilgangskontrollException("Godkjenninger må oppheves før avtalen kan endres.")`
- Failure condition: participant, employer, or advisor approval is still recorded on the current content.
- Why this is a business failure: signed terms may not be replaced until their approvals are revoked.
- Violated state, rule, relationship, ownership, or eligibility condition: all approvals must be absent before current content changes.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale; Avtale.sjekkOmAvtalenKanEndres`

##### Failure 5
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: `If-Unmodified-Since` is earlier than persisted `sistEndret`.
- Why this is a business failure: the update must bind to the exact content version the actor reviewed.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller's freshness value must not be stale.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreAvtale; Avtale.sjekkSistEndret`

##### Failure 6
- Source discriminator: `START_ETTER_SLUTT`
- Failure condition: the proposed `startDato` is after the proposed `sluttDato`.
- Why this is a business failure: the requested agreement interval is internally impossible.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement start must not follow agreement end.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 7
- Source discriminator: `FORTIDLIG_STARTDATO`
- Failure condition: both dates are present, the agreement is not entered or after-registration-authorized, and `startDato.plusDays(7)` is before the current date.
- Why this is a business failure: the draft is too far backdated for ordinary registration.
- Violated state, rule, relationship, ownership, or eligibility condition: an unentered agreement may start more than seven days in the past only after after-registration authorization.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 8
- Source discriminator: `SLUTTDATO_GRENSE_NÅDD`
- Failure condition: the proposed `sluttDato` is after `2089-12-31`.
- Why this is a business failure: the requested agreement exceeds the service's maximum business date.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement end may not be later than 2089-12-31.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/StartOgSluttDatoStrategy.java — StartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 9
- Source discriminator: `VARIGHET_FOR_LANG_ARBEIDSTRENING`
- Failure condition: the work-training end date is later than `startDato.plusMonths(18).minusDays(1)`.
- Why this is a business failure: the requested work-training duration exceeds its measure limit.
- Violated state, rule, relationship, ownership, or eligibility condition: work training may last at most 18 months inclusive.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/startOgSluttDatoStrategy/ArbeidstreningStartOgSluttDatoStrategy.java — ArbeidstreningStartOgSluttDatoStrategy.sjekkStartOgSluttDato`

##### Failure 10
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: the advisor-side post-update PDL refresh reports address-protection code 6 for the participant.
- Why this is a business failure: the concrete participant is ineligible for continued advisor processing.
- Violated state, rule, relationship, ownership, or eligibility condition: address-protected participant data cannot be processed through this advisor update workflow.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreAvtale; Veileder.oppdaterePersondataFraPdlVedEndreAvtale; Veileder.sjekkKode6`; `src/main/java/no/nav/tag/tiltaksgjennomforing/exceptions/KanIkkeOppretteAvtalePåKode6Exception.java — KanIkkeOppretteAvtalePåKode6Exception constructor`

Business failure coverage:
- Source-ledger business branches for this invocation: `10`
- Documented failure blocks for this invocation: `10`
- Coverage result: `Complete`
#### Step 2 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 6 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
`opphevGodkjenninger` does not perform `sjekkTilgang` on the agreement; actor-role rules are enforced, but advisor participant-scope is not checked in this controller path.

<a id="behavior-10"></a>
### Behavior 10: Authorize After-Registration

Business goal:
Allow a specific unentered agreement to use a start date that would normally be too far in the past.

Primary actor(s):
NAV decision-maker.

Workflow boundary rationale:
The draft is legitimately pre-existing; authorizing after-registration is a new decision goal with a stable persisted flag. No successor is required to complete the authorization, satisfying the one-step rule.

Starting state:
Existing unentered, non-terminal agreement; creation belongs to a separate drafting workflow.

Terminal business outcome:
`godkjentForEtterregistrering=true` and authorization event recorded.

Required execution workflow:

#### Step 1: Toggle authorization on
- Use function `mark agreement eligible for after-registration` (`POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`) for a currently false flag.
- Actor: decision-maker.
- State before: unentered false flag.
- State transition or decision: toggle true.
- Output/state after: updated agreement and `GodkjentForEtterregistrering` event.
- API-visible outputs: `200 OK` with the saved serialized `Avtale` body.
- Handoff to later step: later update may pass `FORTIDLIG_STARTDATO` guard.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Decision-maker identity is stored in event; response returns updated aggregate.

Business result and side effects:
Flag and `sistEndret` persist.

Constraints and invariants:
Only unentered, non-terminal accessible agreement may be toggled.

Business failure branches:

#### Step 1 - `mark agreement eligible for after-registration`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.setOmAvtalenKanEtterregistreres`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.setOmAvtalenKanEtterregistreres`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: authorizability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.togglegodkjennEtterregistrering`

##### Failure 4
- Source discriminator: `KAN_IKKE_MERKES_FOR_ETTERREGISTRERING_AVTALE_GODKJENT`
- Failure condition: agreement already entered.
- Why this is a business failure: authorization must precede entry.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle ordering.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.setOmAvtalenKanEtterregistreres`

Business failure coverage:
- Source-ledger business branches for this invocation: `4`
- Documented failure blocks for this invocation: `4`
- Coverage result: `Complete`

Implementation notes:
The endpoint is a toggle, not an idempotent setter; the exact function meaning depends on the pre-state.

<a id="behavior-11"></a>
### Behavior 11: Remove After-Registration Authorization

Business goal:
Withdraw after-registration permission from a specific draft.

Primary actor(s):
NAV decision-maker.

Workflow boundary rationale:
This is an independent reversal goal over an existing authorized draft and reaches its complete false-flag outcome in one step.

Starting state:
Existing unentered agreement with `godkjentForEtterregistrering=true`.

Terminal business outcome:
Flag is false and removal event is recorded.

Required execution workflow:

#### Step 1: Toggle authorization off
- Use function `remove after-registration eligibility` (`POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`) when the flag is true.
- Actor: decision-maker.
- State before: authorized draft.
- State transition or decision: toggle false.
- Output/state after: ordinary date rules restored; `FjernetEtterregistrering` event.
- API-visible outputs: `200 OK` with the saved serialized `Avtale` body.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Same endpoint as Behavior 10; pre-state distinguishes function semantics.

Business result and side effects:
Flag, timestamp, and removal event persist.

Constraints and invariants:
Same access/lifecycle constraints as authorization.

Business failure branches:

#### Step 1 - `remove after-registration eligibility`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.setOmAvtalenKanEtterregistreres`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: decision-maker lacks participant access.
- Why this is a business failure: concrete decision scope.
- Violated state, rule, relationship, ownership, or eligibility condition: eligibility relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.setOmAvtalenKanEtterregistreres`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: agreement terminal.
- Why this is a business failure: lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: toggle eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.togglegodkjennEtterregistrering`

##### Failure 4
- Source discriminator: `KAN_IKKE_MERKES_FOR_ETTERREGISTRERING_AVTALE_GODKJENT`
- Failure condition: agreement entered.
- Why this is a business failure: flag frozen after entry.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle ordering.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.setOmAvtalenKanEtterregistreres`

Business failure coverage:
- Source-ledger business branches for this invocation: `4`
- Documented failure blocks for this invocation: `4`
- Coverage result: `Complete`

Implementation notes:
A concurrent double invocation can re-enable the flag because the API exposes no desired boolean or concurrency header.

<a id="behavior-12"></a>
### Behavior 12: Shorten an Entered Agreement

Business goal:
Create an approved shorter agreement version while preserving already committed reimbursement boundaries.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
An entered agreement is a stable outcome of an earlier entry workflow. Shortening is a new amendment goal; the persistent function completes it, while the dry run is only a preflight. No earlier API step belongs to the amendment goal.

Starting state:
Existing advisor-approved, non-terminal agreement.

Terminal business outcome:
A new `FORKORTE` content version with an earlier end date and adjusted subsidy periods.

Required execution workflow:

#### Step 1: Persist the shorter version
- Use function `shorten agreement` (`POST /avtaler/{avtaleId}/forkort`) with the caller-known entered-agreement `{avtaleId}`, `If-Unmodified-Since="Wed, 01 Jul 2026 00:00:00 GMT"`, and `body={sluttDato:"2026-11-30",grunn:"Changed business need",annetGrunn:""}` where the date is earlier than the current end.
- Actor: advisor.
- State before: entered/advisor-approved agreement.
- State transition or decision: clone approved content, shorten end date, return rejected periods, remove/annul/truncate later periods, and emit amendment/period events.
- Output/state after: new current version and updated `sistEndret`.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
- Optional Step V1: Use function `dry-run agreement shortening` (`POST /avtaler/{avtaleId}/forkort-dry-run`) with the caller-known entered-agreement `{avtaleId}`, `If-Unmodified-Since="Wed, 01 Jul 2026 00:00:00 GMT"`, and `body={sluttDato:"2026-11-30",grunn:"dry run",annetGrunn:""}` to preview the in-memory aggregate.
- Optional Step V2: Use function `list agreement versions` (`GET /avtaler/{avtaleId}/versjoner`) with the known entered agreement `{avtaleId}` and `innlogget-part=VEILEDER` to verify the new version after persistence.

Parameter, identity, and state bindings:
The amendment consumes the current end date and active reimbursement states. The accepted `If-Unmodified-Since` header is not actually checked by either controller method.

Business result and side effects:
Content version increments; unhandled periods after the new end are removed, approved ones are annulled or shortened, and `AvtaleForkortet`/period events may be emitted.

Constraints and invariants:
New end must be earlier, not before protected paid/claim periods, and valid for the measure; a reason is mandatory for persistence.

Business failure branches:

#### Step 1 - `shorten agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forkortAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.forkortAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forkortAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.forkortAvtale`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: annulled/interrupted agreement.
- Why this is a business failure: terminal lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: amendability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`

##### Failure 4
- Source discriminator: `KAN_IKKE_FORKORTE_IKKE_GODKJENT_AVTALE`
- Failure condition: advisor approval absent.
- Why this is a business failure: shortening is post-approval.
- Violated state, rule, relationship, ownership, or eligibility condition: amendment prerequisite.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forkortAvtale`

##### Failure 5
- Source discriminator: `KAN_IKKE_FORKORTE_ETTER_SLUTTDATO`
- Failure condition: proposed end is equal to/after current end.
- Why this is a business failure: operation is not a shortening.
- Violated state, rule, relationship, ownership, or eligibility condition: temporal direction.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forkortAvtale`

##### Failure 6
- Source discriminator: `KAN_IKKE_FORKORTE_FOR_UTBETALT_TILSKUDDSPERIODE`
- Failure condition: new end precedes the last period with `SENDT_KRAV`, `UTBETALT`, `UTBETALING_FEILET`, `GODKJENT_MINUSBELØP`, or `GODKJENT_NULLBELØP`.
- Why this is a business failure: committed reimbursement cannot be cut away.
- Violated state, rule, relationship, ownership, or eligibility condition: financial finality.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`

##### Failure 7
- Source discriminator: `START_ETTER_SLUTT`
- Failure condition: new interval violates a source date guard.
- Why this is a business failure: measure eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: period validity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkStartOgSluttDato`

##### Failure 8
- Source discriminator: `KAN_IKKE_FORKORTE_GRUNN_MANGLER`
- Failure condition: blank reason or `Annet` without detail.
- Why this is a business failure: amendment must be auditable.
- Violated state, rule, relationship, ownership, or eligibility condition: reason requirement.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `8`
- Documented failure blocks for this invocation: `8`
- Coverage result: `Complete`

#### Optional Step V1 - `dry-run agreement shortening`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forkortAvtaleDryRun`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.forkortAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forkortAvtaleDryRun`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.forkortAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forkortAvtaleDryRun`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.forkortAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`

##### Failure 4
- Source discriminator: `KAN_IKKE_FORKORTE_IKKE_GODKJENT_AVTALE`
- Failure condition: The agreement has no advisor approval.
- Why this is a business failure: Shortening is a post-approval amendment and therefore requires an entered advisor-approved baseline.
- Violated state, rule, relationship, ownership, or eligibility condition: The agreement has no advisor approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forkortAvtaleDryRun`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.forkortAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`

##### Failure 5
- Source discriminator: `KAN_IKKE_FORKORTE_ETTER_SLUTTDATO`
- Failure condition: The proposed shortened end date is equal to or later than the current end date.
- Why this is a business failure: A shortening must actually reduce the approved agreement interval.
- Violated state, rule, relationship, ownership, or eligibility condition: The proposed shortened end date is equal to or later than the current end date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forkortAvtaleDryRun`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.forkortAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`

##### Failure 6
- Source discriminator: `KAN_IKKE_FORKORTE_FOR_UTBETALT_TILSKUDDSPERIODE`
- Failure condition: The proposed end date precedes the end of the latest period whose reimbursement status is `SENDT_KRAV`, `UTBETALT`, `UTBETALING_FEILET`, `GODKJENT_MINUSBELØP`, or `GODKJENT_NULLBELØP`.
- Why this is a business failure: The agreement cannot be shortened behind a reimbursement period with a financially committed status.
- Violated state, rule, relationship, ownership, or eligibility condition: The proposed end date precedes the end of the latest period whose reimbursement status is `SENDT_KRAV`, `UTBETALT`, `UTBETALING_FEILET`, `GODKJENT_MINUSBELØP`, or `GODKJENT_NULLBELØP`.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forkortAvtaleDryRun`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.forkortAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forkortAvtale`

- The missing-reason branch is not reachable because the controller substitutes `"dry run"`.

Business failure coverage:
- Source-ledger business branches for this invocation: `6`
- Documented failure blocks for this invocation: `6`
- Coverage result: `Complete`

#### Optional Step V2 - `list agreement versions`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: version owner absent.
- Violated state, rule, relationship, ownership, or eligibility condition: identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.hentAvtaleVersjoner`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: caller cannot access agreement.
- Why this is a business failure: history ownership.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement-party relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.hentVersjoner`

Business failure coverage:
- Source-ledger business branches for this invocation: `2`
- Documented failure blocks for this invocation: `2`
- Coverage result: `Complete`

Implementation notes:
The OpenAPI-visible concurrency header suggests protection, but both shortening methods ignore `sistEndret`.

<a id="behavior-13"></a>
### Behavior 13: Extend an Entered Agreement

Business goal:
Create an approved longer agreement version and extend its subsidy coverage.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
Extension is an independent post-entry amendment; the existing entered agreement belongs to an earlier workflow and the persistent endpoint reaches the complete versioned outcome.

Starting state:
Existing advisor-approved, non-terminal agreement.

Terminal business outcome:
New `FORLENGE` content version with later end date and adjusted/new periods.

Required execution workflow:

#### Step 1: Persist extension
- Use function `extend agreement` (`POST /avtaler/{avtaleId}/forleng`) with the caller-known entered-agreement `{avtaleId}`, `If-Unmodified-Since="Wed, 01 Jul 2026 00:00:00 GMT"`, and `body={sluttDato:"2027-03-31"}`, later than the current end.
- Actor: advisor.
- State before: entered/advisor-approved agreement.
- State transition or decision: refresh participant follow-up eligibility, clone content, extend date, requeue rejected periods, and extend/generate periods.
- Output/state after: new approved version and `AvtaleForlenget` event.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
- Optional Step V1: Use function `dry-run agreement extension` (`POST /avtaler/{avtaleId}/forleng-dry-run`) with the caller-known entered-agreement `{avtaleId}`, `If-Unmodified-Since="Wed, 01 Jul 2026 00:00:00 GMT"`, and `body={sluttDato:"2027-03-31"}` to preview the extension.

Parameter, identity, and state bindings:
The old end date anchors additional period calculation; advisor identity is recorded on the event. Both extension endpoints syntactically require the parseable `If-Unmodified-Since` value shown above, but their controller methods do not compare it with persisted `sistEndret`.

Business result and side effects:
Content version increments; periods may replace the last unhandled period, annul a partial approved unpaid period, or append from the old end.

Constraints and invariants:
New end must be later and within measure duration/date eligibility; follow-up classification is revalidated.

Business failure branches:

#### Step 1 - `extend agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.forlengAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.forlengAvtale`

##### Failure 3
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: Arena status missing or participant not eligible for selected measure.
- Why this is a business failure: current participant eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: qualification.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkOgHentOppfølgingStatus`; `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: amendability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.forlengAvtale`

##### Failure 5
- Source discriminator: `KAN_IKKE_FORLENGE_IKKE_GODKJENT_AVTALE`
- Failure condition: advisor approval absent.
- Why this is a business failure: post-entry operation.
- Violated state, rule, relationship, ownership, or eligibility condition: approval prerequisite.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtale`

##### Failure 6
- Source discriminator: `KAN_IKKE_FORLENGE_FEIL_SLUTTDATO`
- Failure condition: proposed end not later.
- Why this is a business failure: not an extension.
- Violated state, rule, relationship, ownership, or eligibility condition: temporal direction.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtale`

##### Failure 7
- Source discriminator: `START_ETTER_SLUTT`
- Failure condition: the proposed start date is after the proposed end date.
- Why this is a business failure: The requested agreement interval falls outside the applicable business date rule.
- Violated state, rule, relationship, ownership, or eligibility condition: the proposed start date is after the proposed end date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtale`

##### Failure 8
- Source discriminator: `SLUTTDATO_GRENSE_NÅDD`
- Failure condition: the proposed end date is later than 2089-12-31.
- Why this is a business failure: The requested agreement interval falls outside the applicable business date rule.
- Violated state, rule, relationship, ownership, or eligibility condition: the proposed end date is later than 2089-12-31.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtale`

##### Failure 9
- Source discriminator: `FORLENG_MIDLERTIDIG_IKKE_TILGJENGELIG`
- Failure condition: period-calculation dates fall into the unsupported reduction-date branch.
- Why this is a business failure: calculation invariant.
- Violated state, rule, relationship, ownership, or eligibility condition: reduced-subsidy interval consistency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/RegnUtTilskuddsperioderForAvtale.java — RegnUtTilskuddsperioderForAvtale.beregnTilskuddsperioderForAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `9`
- Documented failure blocks for this invocation: `9`
- Coverage result: `Complete`

#### Optional Step V1 - `dry-run agreement extension`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtaleDryRun`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtaleDryRun`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtaleDryRun`

##### Failure 4
- Source discriminator: `KAN_IKKE_FORLENGE_IKKE_GODKJENT_AVTALE`
- Failure condition: The agreement has no advisor approval.
- Why this is a business failure: Extension is a post-approval amendment and therefore requires an entered advisor-approved baseline.
- Violated state, rule, relationship, ownership, or eligibility condition: The agreement has no advisor approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtaleDryRun`

##### Failure 5
- Source discriminator: `KAN_IKKE_FORLENGE_FEIL_SLUTTDATO`
- Failure condition: The proposed extended end date is equal to or earlier than the current end date.
- Why this is a business failure: An extension must actually increase the approved agreement interval.
- Violated state, rule, relationship, ownership, or eligibility condition: The proposed extended end date is equal to or earlier than the current end date.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtaleDryRun`

##### Failure 6
- Source discriminator: `FORLENG_MIDLERTIDIG_IKKE_TILGJENGELIG`
- Failure condition: The reduction date lies after the requested subsidy-period interval, a date relation that the period calculator does not support.
- Why this is a business failure: The calculator cannot produce a valid reduced-rate period set for this date relationship.
- Violated state, rule, relationship, ownership, or eligibility condition: The reduction date lies after the requested subsidy-period interval, a date relation that the period calculator does not support.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/RegnUtTilskuddsperioderForAvtale.java — RegnUtTilskuddsperioderForAvtale.beregnTilskuddsperioderForAvtale`

##### Failure 7
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: no usable participant follow-up classification is available.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: no usable participant follow-up classification is available.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtaleDryRun`

##### Failure 8
- Source discriminator: `KVALIFISERINGSGRUPPE_IKKE_RETTIGHET`
- Failure condition: the participant's qualification group grants no measure right.
- Why this is a business failure: The selected domain operation is not valid for this agreement measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group grants no measure right.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtaleDryRun`

##### Failure 9
- Source discriminator: `KVALIFISERINGSGRUPPE_MIDLERTIDIG_LONNSTILSKUDD_OG_SOMMERJOBB_FEIL`
- Failure condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for temporary wage subsidy or summer job.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtaleDryRun`

##### Failure 10
- Source discriminator: `KVALIFISERINGSGRUPPE_VARIG_LONNSTILSKUDD_FEIL`
- Failure condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Why this is a business failure: The subsidy-period operation would conflict with the period's financial or decision lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: the participant's qualification group is ineligible for permanent wage subsidy.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.forlengAvtaleDryRun`

Business failure coverage:
- Source-ledger business branches for this invocation: `10`
- Documented failure blocks for this invocation: `10`
- Coverage result: `Complete`

Implementation notes:
Both endpoints accept but ignore the concurrency timestamp.

<a id="behavior-14"></a>
### Behavior 14: Amend Subsidy Calculation

Business goal:
Replace wage-subsidy calculation inputs on an approved subsidy agreement and update open period amounts.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
This is a new financial amendment over a stable approved agreement. The persistent function reaches the complete outcome; dry-run is a preflight.

Starting state:
Existing advisor-approved subsidy-backed agreement.

Terminal business outcome:
New `ENDRE_TILSKUDDSBEREGNING` version and recalculated unhandled periods.

Required execution workflow:

#### Step 1: Persist calculation amendment
- Use function `change subsidy calculation` (`POST /avtaler/{avtaleId}/endre-tilskuddsberegning`) with `body={arbeidsgiveravgift:0.141,feriepengesats:0.12,manedslonn:42000,otpSats:0.02}`.
- Actor: advisor.
- State before: approved wage-subsidy/summer-job agreement.
- State transition or decision: clone content, recalculate totals, requeue rejected periods, update unhandled amounts.
- Output/state after: new effective version and event.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
- Optional Step V1: Use function `dry-run subsidy calculation change` (`POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run`) with the known entered agreement `{avtaleId}` and `body={arbeidsgiveravgift:0.141,feriepengesats:0.12,manedslonn:42000,otpSats:0.02}`.

Parameter, identity, and state bindings:
Existing dates, percentage, and period rows combine with new financial inputs.

Business result and side effects:
New version, current financial totals, updated period amounts, and `TilskuddsberegningEndret` event.

Constraints and invariants:
Only the three subsidy measures are eligible; all four inputs are required.

Business failure branches:

#### Step 1 - `change subsidy calculation`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE`
- Failure condition: measure is not summer job or wage subsidy.
- Why this is a business failure: calculation has no meaning for measure.
- Violated state, rule, relationship, ownership, or eligibility condition: measure eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.krevEnAvTiltakstyper`

##### Failure 5
- Source discriminator: `KAN_IKKE_ENDRE_OKONOMI_IKKE_GODKJENT_AVTALE`
- Failure condition: advisor approval absent.
- Why this is a business failure: post-approval amendment.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle prerequisite.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreTilskuddsberegning`

##### Failure 6
- Source discriminator: `KAN_IKKE_ENDRE_OKONOMI_UGYLDIG_INPUT`
- Failure condition: any required financial input null.
- Why this is a business failure: calculation cannot be completed.
- Violated state, rule, relationship, ownership, or eligibility condition: financial completeness.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreTilskuddsberegning`

##### Failure 7
- Source discriminator: `FORLENG_MIDLERTIDIG_IKKE_TILGJENGELIG`
- Failure condition: recalculation hits unsupported reduced-period date relation.
- Why this is a business failure: financial period invariant.
- Violated state, rule, relationship, ownership, or eligibility condition: calculation consistency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/RegnUtTilskuddsperioderForAvtale.java — RegnUtTilskuddsperioderForAvtale.beregnTilskuddsperioderForAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `7`
- Documented failure blocks for this invocation: `7`
- Coverage result: `Complete`

#### Optional Step V1 - `dry-run subsidy calculation change`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreTilskuddsberegningDryRun`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreTilskuddsberegningDryRun`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreTilskuddsberegningDryRun`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE`
- Failure condition: The agreement measure is outside the concrete amendment or subsidy-period operation's allowed measure set.
- Why this is a business failure: The requested amendment has business meaning only for its explicitly supported measure subtype.
- Violated state, rule, relationship, ownership, or eligibility condition: The agreement measure is outside the concrete amendment or subsidy-period operation's allowed measure set.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreTilskuddsberegningDryRun`

##### Failure 5
- Source discriminator: `KAN_IKKE_ENDRE_OKONOMI_IKKE_GODKJENT_AVTALE`
- Failure condition: The agreement has no advisor approval.
- Why this is a business failure: Financial amendment is permitted only after advisor approval establishes the baseline terms.
- Violated state, rule, relationship, ownership, or eligibility condition: The agreement has no advisor approval.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreTilskuddsberegningDryRun`

##### Failure 6
- Source discriminator: `KAN_IKKE_ENDRE_OKONOMI_UGYLDIG_INPUT`
- Failure condition: At least one of `arbeidsgiveravgift`, `feriepengesats`, `manedslonn`, or `otpSats` is null.
- Why this is a business failure: All four financial inputs are required to recalculate the subsidy consistently.
- Violated state, rule, relationship, ownership, or eligibility condition: At least one of `arbeidsgiveravgift`, `feriepengesats`, `manedslonn`, or `otpSats` is null.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreTilskuddsberegningDryRun`

##### Failure 7
- Source discriminator: `FORLENG_MIDLERTIDIG_IKKE_TILGJENGELIG`
- Failure condition: The reduction date lies after the requested subsidy-period interval, a date relation that the period calculator does not support.
- Why this is a business failure: The calculator cannot produce a valid reduced-rate period set for this date relationship.
- Violated state, rule, relationship, ownership, or eligibility condition: The reduction date lies after the requested subsidy-period interval, a date relation that the period calculator does not support.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/RegnUtTilskuddsperioderForAvtale.java — RegnUtTilskuddsperioderForAvtale.beregnTilskuddsperioderForAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `7`
- Documented failure blocks for this invocation: `7`
- Coverage result: `Complete`

Implementation notes:
Unlike draft update, the financial amendment method does not re-check OTP range; it only rejects missing values.

<a id="behavior-15"></a>
### Behavior 15: Amend Agreement Contact Information

Business goal:
Create an approved version with corrected participant, employer, and advisor contact details.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
An entered agreement is a stable prerequisite from a prior workflow; this one endpoint completes a distinct contact-correction goal with no required successor.

Starting state:
Existing advisor-approved agreement.

Terminal business outcome:
New `ENDRE_KONTAKTINFO` version.

Required execution workflow:

#### Step 1: Persist contact amendment
- Use function `change contact information` (`POST /avtaler/{avtaleId}/endre-kontaktinfo`) with `body={deltakerFornavn:"Kari",deltakerEtternavn:"Nordmann",deltakerTlf:"41234567",veilederFornavn:"Vera",veilederEtternavn:"Veileder",veilederTlf:"43456789",arbeidsgiverFornavn:"Arne",arbeidsgiverEtternavn:"Arbeidsgiver",arbeidsgiverTlf:"42345678",refusjonKontaktperson:{refusjonKontaktpersonFornavn:"Rita",refusjonKontaktpersonEtternavn:"Refusjon",refusjonKontaktpersonTlf:"45678901",ønskerVarslingOmRefusjon:true}}`.
- Actor: advisor.
- State before: approved non-terminal agreement.
- State transition or decision: clone approved version, replace contact fields, requeue rejected periods.
- Output/state after: new effective contact version and event.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Advisor ident is event actor; agreement id selects current content.

Business result and side effects:
Version/timestamp/event persist; rejected periods may be replaced as unhandled.

Constraints and invariants:
Every named contact field must be non-empty.

Business failure branches:

#### Step 1 - `change contact information`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreKontaktinfo`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreKontaktinfo`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreKontaktinfo`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_KONTAKTINFO_GRUNN_IKKE_GODKJENT_AVTALE`
- Failure condition: advisor approval absent.
- Why this is a business failure: this is an approved-version amendment.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle prerequisite.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreKontaktInformasjon`

##### Failure 5
- Source discriminator: `KAN_IKKE_ENDRE_KONTAKTINFO_GRUNN_MANGLER`
- Failure condition: any required name/phone blank.
- Why this is a business failure: incomplete contact set.
- Violated state, rule, relationship, ownership, or eligibility condition: contact completeness.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreKontaktinfo`

Business failure coverage:
- Source-ledger business branches for this invocation: `5`
- Documented failure blocks for this invocation: `5`
- Coverage result: `Complete`

Implementation notes:
Phone format itself is not validated here; it is validated later when sharing.

<a id="behavior-16"></a>
### Behavior 16: Amend Job Description

Business goal:
Create an approved version with corrected job title, tasks, occupation ids, position percentage, and days per week.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
This distinct post-entry amendment completes in one endpoint over stable prior agreement state.

Starting state:
Existing advisor-approved agreement.

Terminal business outcome:
New `ENDRE_STILLING` version.

Required execution workflow:

#### Step 1: Persist job amendment
- Use function `change job description` (`POST /avtaler/{avtaleId}/endre-stillingbeskrivelse`) with `body={stillingstittel:"Warehouse assistant",arbeidsoppgaver:"Warehouse tasks",stillingStyrk08:9334,stillingKonseptId:123,stillingprosent:80,antallDagerPerUke:5}`.
- Actor: advisor.
- State before: approved non-terminal agreement.
- State transition or decision: clone and replace job fields; requeue rejected periods.
- Output/state after: new version and event.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Current agreement id and advisor ident.

Business result and side effects:
Version, effective time, timestamp, and `StillingsbeskrivelseEndret` persist.

Constraints and invariants:
All six job fields are required.

Business failure branches:

#### Step 1 - `change job description`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreStillingbeskrivelse`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreStillingbeskrivelse`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreStillingbeskrivelse`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_STILLINGSBESKRIVELSE_GRUNN_IKKE_GODKJENT_AVTALE`
- Failure condition: advisor approval absent.
- Why this is a business failure: post-approval amendment.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreStillingsbeskrivelse`

##### Failure 5
- Source discriminator: `KAN_IKKE_ENDRE_STILLINGSBESKRIVELSE_GRUNN_MANGLER`
- Failure condition: any required job field missing.
- Why this is a business failure: incomplete employment description.
- Violated state, rule, relationship, ownership, or eligibility condition: content completeness.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreStillingbeskrivelse`

Business failure coverage:
- Source-ledger business branches for this invocation: `5`
- Documented failure blocks for this invocation: `5`
- Coverage result: `Complete`

Implementation notes:
No concurrency header is used.

<a id="behavior-17"></a>
### Behavior 17: Amend Follow-Up and Adaptation Text

Business goal:
Create an approved version with revised follow-up and workplace adaptation commitments.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
This is an independent post-entry content goal completed by one mutating function.

Starting state:
Existing advisor-approved agreement.

Terminal business outcome:
New `ENDRE_OPPFØLGING_OG_TILRETTELEGGING` version.

Required execution workflow:

#### Step 1: Persist revised commitments
- Use function `change follow-up and adaptation text` (`POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging`) with `body={oppfolging:"Monthly follow-up",tilrettelegging:"Adapted workstation"}`.
- Actor: advisor.
- State before: approved non-terminal agreement.
- State transition or decision: clone content, replace texts, requeue rejected periods.
- Output/state after: effective version/event.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Agreement id selects current content.

Business result and side effects:
Version/effective time/event persist.

Constraints and invariants:
Both texts are mandatory.

Business failure branches:

#### Step 1 - `change follow-up and adaptation text`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreOppfølgingOgTilrettelegging`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreOppfølgingOgTilrettelegging`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreOppfølgingOgTilrettelegging`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_OPPFØLGING_OG_TILRETTELEGGING_GRUNN_IKKE_GODKJENT_AVTALE`
- Failure condition: advisor approval absent.
- Why this is a business failure: post-approval amendment.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreOppfølgingOgTilrettelegging`

##### Failure 5
- Source discriminator: `KAN_IKKE_ENDRE_OPPFØLGING_OG_TILRETTELEGGING_GRUNN_MANGLER`
- Failure condition: either text absent.
- Why this is a business failure: incomplete commitments.
- Violated state, rule, relationship, ownership, or eligibility condition: content completeness.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreOppfølgingOgTilrettelegging`

Business failure coverage:
- Source-ledger business branches for this invocation: `5`
- Documented failure blocks for this invocation: `5`
- Coverage result: `Complete`

Implementation notes:
None.

<a id="behavior-18"></a>
### Behavior 18: Replace Work-Training Goals

Business goal:
Replace the complete set of goals on an approved work-training agreement.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
The replacement set and new child ids are a distinct terminal outcome over stable entered state; no successor is required.

Starting state:
Existing advisor-approved `ARBEIDSTRENING` agreement.

Terminal business outcome:
New `ENDRE_MÅL` version with new goal UUIDs.

Required execution workflow:

#### Step 1: Replace goals
- Use function `change work-training goals` (`POST /avtaler/{avtaleId}/endre-maal`) with `body={maal:[{beskrivelse:"Learn warehouse routines",kategori:ARBEIDSERFARING}]}`.
- Actor: advisor.
- State before: approved work-training agreement.
- State transition or decision: clone content, clear copied goals, create new goal children, requeue rejected periods.
- Output/state after: new version and `MålEndret` event.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Request goal ids are not reused; service generates new UUIDs.

Business result and side effects:
Old current-version goal relationships are replaced in the new version.

Constraints and invariants:
Measure must be work training; list and every description/category must be present.

Business failure branches:

#### Step 1 - `change work-training goals`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreMål`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreMål`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.endreMål`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE`
- Failure condition: measure is not `ARBEIDSTRENING`.
- Why this is a business failure: goals belong to that measure.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate subtype.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.krevEnAvTiltakstyper`

##### Failure 5
- Source discriminator: `KAN_IKKE_ENDRE_MAAL_IKKE_INNGAATT_AVTALE`
- Failure condition: advisor approval absent.
- Why this is a business failure: versioned amendment prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreMål`

##### Failure 6
- Source discriminator: `KAN_IKKE_ENDRE_MAAL_TOM_LISTE`
- Failure condition: no goals.
- Why this is a business failure: replacement cannot leave required set empty.
- Violated state, rule, relationship, ownership, or eligibility condition: goal cardinality.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreMål`

##### Failure 7
- Source discriminator: `KAN_IKKE_ENDRE_MAAL_IKKE_BESKRIVELSE_ELLER_KATEGORI`
- Failure condition: a goal lacks either field.
- Why this is a business failure: invalid goal child.
- Violated state, rule, relationship, ownership, or eligibility condition: child completeness.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreMål`

Business failure coverage:
- Source-ledger business branches for this invocation: `7`
- Documented failure blocks for this invocation: `7`
- Coverage result: `Complete`

Implementation notes:
Historical versions retain their copied child rows.

<a id="behavior-19"></a>
### Behavior 19: Replace Inclusion-Subsidy Expenses

Business goal:
Create a new approved expense set while preserving client/server synchronization with the prior set.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
Expense replacement and generated ids are a complete independent amendment outcome.

Starting state:
Existing advisor-approved `INKLUDERINGSTILSKUDD` agreement whose `{avtaleId}` is known to the caller. The persisted expense-child ids are not yet caller-visible and must be read.

Terminal business outcome:
New `ENDRE_INKLUDERINGSTILSKUDD` version with retained/requested and newly generated expense children.

Required execution workflow:

#### Step 1: Read the current expense-child identities
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the caller-known `{avtaleId}` and `innlogget-part=VEILEDER`.
- Actor: advisor.
- State before: approved inclusion agreement; the caller does not yet know every persisted `inkluderingstilskuddsutgift[].id`.
- State transition or decision: read-only retrieval of the current content and its expense children.
- Output/state after: persisted state is unchanged and every current expense-child id is visible to the caller.
- API-visible outputs: `200 OK` with the serialized `Avtale` body, including current `inkluderingstilskuddsutgift[]` objects and their `id`, `beløp`, and `type` fields.
- Handoff to later step: Step 2 reuses every Step 1 response-body `inkluderingstilskuddsutgift[].id`.

#### Step 2: Replace expenses
- Use function `change inclusion subsidy expenses` (`POST /avtaler/{avtaleId}/endre-inkluderingstilskudd`) with the caller-known `{avtaleId}` and `body={inkluderingstilskuddsutgift:[{id:"11111111-1111-4111-8111-111111111111",beløp:5000,type:UTSTYR},{id:null,beløp:3000,type:OPPLÆRING}]}`, where `11111111-1111-4111-8111-111111111111` represents the exact existing child id returned by Step 1 and every other pre-existing child is likewise included.
- Actor: advisor.
- State before: approved inclusion agreement.
- State transition or decision: validate amount/children/synchronization, clone content, add new generated children.
- Output/state after: new version/event.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
The agreement id is caller-known starting state. Every previous child id comes from Step 1 and must appear in Step 2; new children use `id=null` and receive generated UUIDs that are persisted but not returned by Step 2.

Business result and side effects:
Version/effective time/event persist; rejected periods are requeued.

Constraints and invariants:
Only inclusion agreements; non-empty list; maximum 136000 in this amendment method; every child has type/amount; previous-id count must match.

Business failure branches:

#### Step 2 - `change inclusion subsidy expenses`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreInkluderingstilskudd`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreInkluderingstilskudd`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreInkluderingstilskudd`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE`
- Failure condition: not inclusion subsidy.
- Why this is a business failure: subtype rule.
- Violated state, rule, relationship, ownership, or eligibility condition: measure eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.krevEnAvTiltakstyper`

##### Failure 5
- Source discriminator: `KAN_IKKE_ENDRE_INKLUDERINGSTILSKUDD_IKKE_INNGAATT_AVTALE`
- Failure condition: advisor approval absent.
- Why this is a business failure: post-entry amendment.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreInkluderingstilskudd`

##### Failure 6
- Source discriminator: `KAN_IKKE_ENDRE_INKLUDERINGSTILSKUDD_TOM_LISTE`
- Failure condition: request empty.
- Why this is a business failure: required expense set absent.
- Violated state, rule, relationship, ownership, or eligibility condition: cardinality.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreInkluderingstilskudd`

##### Failure 7
- Source discriminator: `INKLUDERINGSTILSKUDD_SUM_FOR_HØY`
- Failure condition: request total exceeds 136000.
- Why this is a business failure: subsidy cap.
- Violated state, rule, relationship, ownership, or eligibility condition: amount ceiling.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/InkluderingstilskuddStrategy.java — InkluderingstilskuddStrategy.sjekkTotalBeløp`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreInkluderingstilskudd`

##### Failure 8
- Source discriminator: `KAN_IKKE_ENDRE_INKLUDERINGSTILSKUDD_IKKE_BELOP_ELLER_TYPE`
- Failure condition: a child lacks amount/type.
- Why this is a business failure: invalid expense.
- Violated state, rule, relationship, ownership, or eligibility condition: child completeness.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreInkluderingstilskudd`

##### Failure 9
- Source discriminator: `KAN_IKKE_ENDRE_INKLUDERINGSTILSKUDD_TOM_LISTE`
- Failure condition: prior child count differs from request children carrying ids.
- Why this is a business failure: stale client would lose expenses.
- Violated state, rule, relationship, ownership, or eligibility condition: collection version synchronization.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreInkluderingstilskudd`

Business failure coverage:
- Source-ledger business branches for this invocation: `9`
- Documented failure blocks for this invocation: `9`
- Coverage result: `Complete`

#### Step 1 - `retrieve agreement by id`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: expense owner absent.
- Violated state, rule, relationship, ownership, or eligibility condition: identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.hentAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: caller cannot access agreement.
- Why this is a business failure: expense ids are agreement-scoped.
- Violated state, rule, relationship, ownership, or eligibility condition: ownership.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.hent`

Business failure coverage:
- Source-ledger business branches for this invocation: `2`
- Documented failure blocks for this invocation: `2`
- Coverage result: `Complete`

Implementation notes:
The 136000 amendment cap differs from the draft strategy's 136700 constant, an implementation inconsistency.

<a id="behavior-20"></a>
### Behavior 20: Amend Mentor Details

Business goal:
Create an approved mentor version with revised identity display, phone, pay, hours, and tasks.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
Distinct mentor-content amendment over a stable entered agreement, complete in one operation.

Starting state:
Existing advisor-approved `MENTOR` agreement.

Terminal business outcome:
New `ENDRE_OM_MENTOR` version.

Required execution workflow:

#### Step 1: Persist mentor details
- Use function `change mentor details` (`POST /avtaler/{avtaleId}/endre-om-mentor`) with `body={mentorFornavn:"Mona",mentorEtternavn:"Mentor",mentorTlf:"44567890",mentorOppgaver:"Daily guidance",mentorAntallTimer:20.0,mentorTimelonn:350}`.
- Actor: advisor.
- State before: approved mentor agreement.
- State transition or decision: clone and replace mentor fields; requeue rejected periods.
- Output/state after: new version/event.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
This changes content fields, not the aggregate `mentorFnr` relationship.

Business result and side effects:
Version/effective time/event persist.

Constraints and invariants:
Measure must be mentor; all six mentor fields required.

Business failure branches:

#### Step 1 - `change mentor details`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreOmMentor`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreOmMentor`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: the agreement is annulled or interrupted.
- Why this is a business failure: A terminal agreement cannot accept another lifecycle mutation without contradicting its recorded history.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement is annulled or interrupted.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreOmMentor`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE`
- Failure condition: not mentor.
- Why this is a business failure: subtype rule.
- Violated state, rule, relationship, ownership, or eligibility condition: measure.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.krevEnAvTiltakstyper`

##### Failure 5
- Source discriminator: `KAN_IKKE_ENDRE_OM_MENTOR_IKKE_INNGAATT_AVTALE`
- Failure condition: advisor approval absent.
- Why this is a business failure: post-entry amendment.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.endreOmMentor`

##### Failure 6
- Source discriminator: `KAN_IKKE_ENDRE_OM_MENTOR_UGYLDIG_INPUT`
- Failure condition: any required mentor field missing.
- Why this is a business failure: incomplete mentor terms.
- Violated state, rule, relationship, ownership, or eligibility condition: content completeness.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreOmMentor`

Business failure coverage:
- Source-ledger business branches for this invocation: `6`
- Documented failure blocks for this invocation: `6`
- Coverage result: `Complete`

Implementation notes:
No new confidentiality signature is required after changing these fields.

<a id="behavior-21"></a>
### Behavior 21: Change Subsidy Cost Center Before Entry

Business goal:
Move all still-editable subsidy periods to a valid NAV cost center before the agreement is entered.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
An existing calculated draft is legitimate prior state; cost-center correction is a complete independent goal, and period rebuild is internal to the one function.

Starting state:
Existing unentered subsidy agreement with active unhandled or rejected periods.

Terminal business outcome:
Content and rebuilt periods carry the selected unit number/name.

Required execution workflow:

#### Step 1: Change cost center
- Use function `change cost center` (`POST /avtaler/{avtaleId}/endre-kostnadssted`) with `body={enhet:"1234"}` where Norg2 resolves unit `1234`.
- Actor: advisor.
- State before: unentered draft with open periods.
- State transition or decision: resolve unit name, set cost center, remove/rebuild unhandled periods.
- Output/state after: updated aggregate returned.
- API-visible outputs: `200 OK` with the saved serialized `Avtale` body.
- Handoff to later step: later approvals use rebuilt periods.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Request unit is persisted both in content and generated periods.

Business result and side effects:
Aggregate/child rows change; no explicit domain event is registered.

Constraints and invariants:
Unit must exist; at least one active unhandled/rejected period; agreement unentered and non-terminal.

Business failure branches:

#### Step 1 - `change cost center`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreKostnadssted`

##### Failure 2
- Source discriminator: `ENHET_FINNES_IKKE`
- Failure condition: Norg2 cannot resolve unit.
- Why this is a business failure: invalid cost center.
- Violated state, rule, relationship, ownership, or eligibility condition: NAV-unit relationship.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.oppdatereKostnadssted`

##### Failure 3
- Source discriminator: `TILSKUDDSPERIODE_ER_IKKE_SATT`
- Failure condition: no active unhandled/rejected period.
- Why this is a business failure: no editable subsidy scope.
- Violated state, rule, relationship, ownership, or eligibility condition: child-state prerequisite.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.oppdatereKostnadssted`

##### Failure 4
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: terminal agreement.
- Why this is a business failure: lifecycle.
- Violated state, rule, relationship, ownership, or eligibility condition: editability.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.oppdatereKostnadsstedForTilskuddsperioder`

##### Failure 5
- Source discriminator: `KAN_IKKE_OPPDATERE_KOSTNADSSTED_INGAATT_AVTALE`
- Failure condition: already entered.
- Why this is a business failure: cost center is frozen at entry.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreKostnadssted`

Business failure coverage:
- Source-ledger business branches for this invocation: `5`
- Documented failure blocks for this invocation: `5`
- Coverage result: `Complete`

Implementation notes:
`Veileder.oppdatereKostnadssted` does not call object-scoped `sjekkTilgang`; this is a source-level authorization discrepancy.

<a id="behavior-22"></a>
### Behavior 22: Prepare an Arena-Cleanup Agreement at the Correct Migration Boundary

Business goal:
Create a cleanup-marked subsidy draft, calculate it, and persist the migration date that separates Arena-treated from new periods.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
Creation produces the cleanup relationship; update supplies the values needed to calculate periods; migration adjustment consumes both and persists the cleanup boundary. These functions are causally ordered.

Starting state:
No prior service state.

Terminal business outcome:
Cleanup draft has `ArenaRyddeAvtale.migreringsdato` and correctly rebuilt `BEHANDLET_I_ARENA`/`UBEHANDLET` periods.

Required execution workflow:

#### Step 1: Create cleanup draft
- Use function `create Arena cleanup agreement` (`POST /avtaler?ryddeavtale=true`) with query `ryddeavtale=true` and `body={deltakerFnr:"01039513753",bedriftNr:"111222333",tiltakstype:MIDLERTIDIG_LONNSTILSKUDD}`.
- Actor: NAV advisor.
- State before: No agreement or cleanup marker exists.
- State transition or decision: Creates the draft and its `ArenaRyddeAvtale` relationship in the same transaction.
- Output/state after: A cleanup-marked subsidy draft exists.
- API-visible outputs: `201 Created`; `Location: /avtaler/{avtaleId}`; no response body, `sistEndret`, or `Last-Modified`.
- Handoff to later step: Step 2 extracts `{avtaleId}`.

#### Step 2: Read initial freshness
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with Step 1 id and `innlogget-part=VEILEDER`.
- Actor: NAV advisor.
- State before: Creation exposed no timestamp.
- State transition or decision: Reads the cleanup draft.
- Output/state after: The caller has initial `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale`, including `id`, `avtaleNr`, `sistEndret`, current content, approvals, status, and subsidy periods visible to the caller.
- Handoff to later step: Step 3 uses it.

#### Step 3: Complete calculation inputs
- Use function `update agreement` (`PUT /avtaler/{avtaleId}`) with Step 1 `{avtaleId}`, `innlogget-part=VEILEDER`, Step 2 response-body `sistEndret` as `If-Unmodified-Since`, and body fields `deltakerFornavn=Kari`, `deltakerEtternavn=Nordmann`, `deltakerTlf=41234567`, `bedriftNavn=Bedriften AS`, `arbeidsgiverFornavn=Arne`, `arbeidsgiverEtternavn=Arbeidsgiver`, `arbeidsgiverTlf=42345678`, `veilederFornavn=Vera`, `veilederEtternavn=Veileder`, `veilederTlf=43456789`, `startDato=2026-07-01`, `sluttDato=2026-12-31`, `oppfolging=Monthly follow-up`, and `tilrettelegging=Adapted workstation`, `stillingprosent=80`, `arbeidsoppgaver=Production work`, `stillingstittel=Operator`, `stillingStyrk08=8212`, `stillingKonseptId=321`, `antallDagerPerUke=5`, `arbeidsgiverKontonummer=12345678903`, `lonnstilskuddProsent=40`, `manedslonn=40000`, `feriepengesats=0.12`, `arbeidsgiveravgift=0.141`, `otpSats=0.02`, `harFamilietilknytning=false`, and `stillingstype=FAST`.
- Actor: NAV advisor.
- State before: Editable cleanup draft.
- State transition or decision: Persists calculation input and generated periods.
- Output/state after: The cleanup draft has a period schedule.
- API-visible outputs: `200 OK` with an empty body and `Last-Modified` set from the saved agreement's `sistEndret`.
- Handoff to later step: Step 4 uses the agreement id; this endpoint has no freshness parameter.

#### Step 4: Persist migration date and rebuild
- Use function `adjust Arena migration date` (`POST /avtaler/{avtaleId}/juster-arena-migreringsdato`) with Step 1 id and `body={migreringsdato:"2026-08-01"}`.
- Actor: NAV advisor.
- State before: Unentered cleanup draft with generated periods.
- State transition or decision: Rebuilds periods around the migration boundary and updates the cleanup marker.
- Output/state after: The cleanup agreement has the requested migration boundary and rebuilt periods.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
- Optional Step V1: Use function `dry-run Arena migration date adjustment` (`POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run`) with Step 1 `Location` `{avtaleId}` and `body={migreringsdato:"2026-08-01"}` after required Step 3 and before required Step 4 to inspect the in-memory period result.

Parameter, identity, and state bindings:
Step 1 id binds cleanup row; Step 2 dates/totals feed Step 3 calculation; same migration date should be used in preview and persistence.

Business result and side effects:
Cleanup row and aggregate are saved transactionally; period-annulment events can be emitted.

Constraints and invariants:
Persistent adjustment rejects entered agreements; period generation only occurs when required fields and non-terminal status permit it.

Business failure branches:

#### Step 1 - `create Arena cleanup agreement`

##### Failure 1
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: the advisor lacks write access to the selected participant.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the advisor lacks write access to the selected participant.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettAvtale`

##### Failure 2
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: PDL reports address-protection code 6 for the participant refreshed by the advisor path.
- Why this is a business failure: Address-protected participant data is a concrete eligibility bar for agreement processing in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: PDL reports address-protection code 6 for the participant refreshed by the advisor path.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 3
- Source discriminator: `SOMMERJOBB_IKKE_GAMMEL_NOK`
- Failure condition: The participant is under 16 when the agreement aggregate is constructed.
- Why this is a business failure: The participant fails the service-wide minimum-age eligibility rule.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant is under 16 when the agreement aggregate is constructed.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

##### Failure 4
- Source discriminator: `SOMMERJOBB_FOR_GAMMEL`
- Failure condition: For a `SOMMERJOBB` agreement, the participant is over 30 on 1 January of the creation year.
- Why this is a business failure: The participant fails the summer-job age eligibility rule at creation.
- Violated state, rule, relationship, ownership, or eligibility condition: For a `SOMMERJOBB` agreement, the participant is over 30 on 1 January of the creation year.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale constructors`

##### Failure 5
- Source discriminator: `ENHET_FINNES_IKKE`
- Failure condition: the selected business or NAV unit cannot be resolved.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected business or NAV unit cannot be resolved.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettAvtale`

##### Failure 6
- Source discriminator: `ENHET_ER_JURIDISK`
- Failure condition: the selected organization number identifies a legal entity instead of a virksomhet.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected organization number identifies a legal entity instead of a virksomhet.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettAvtale`

##### Failure 7
- Source discriminator: `ENHET_ER_ORGLEDD`
- Failure condition: the selected organization number identifies an organizational link instead of a virksomhet.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected organization number identifies an organizational link instead of a virksomhet.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.opprettAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `7`
- Documented failure blocks for this invocation: `7`
- Coverage result: `Complete`

#### Step 3 - `update agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 2
- Source discriminator: `FEIL_OTP_SATS`
- Failure condition: `otpSats` is below 0.0 or above 0.3.
- Why this is a business failure: The pension rate is a financial input used to calculate the employer cost and subsidy amount.
- Violated state, rule, relationship, ownership, or eligibility condition: `otpSats` is below 0.0 or above 0.3.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/LonnstilskuddStrategy.java — LonnstilskuddStrategy.endre`

##### Failure 3
- Source discriminator: `LONNSTILSKUDD_PROSENT_ER_UGYLDIG`
- Failure condition: For the selected temporary wage-subsidy agreement, `lonnstilskuddProsent` is neither 40 nor 60.
- Why this is a business failure: The selected measure permits only its configured subsidy percentages.
- Violated state, rule, relationship, ownership, or eligibility condition: For the selected temporary wage-subsidy agreement, `lonnstilskuddProsent` is neither 40 nor 60.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/MidlertidigLonnstilskuddStrategy.java — MidlertidigLonnstilskuddStrategy.sjekktilskuddsprosentSats`

##### Failure 4
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.endreAvtale`

##### Failure 5
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Why this is a business failure: Address-protected participant data makes the agreement ineligible for advisor approval in this service.
- Violated state, rule, relationship, ownership, or eligibility condition: The participant has address-protection code 6 when the advisor attempts approval or refreshes agreement content.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

Business failure coverage:
- Source-ledger business branches for this invocation: `5`
- Documented failure blocks for this invocation: `5`
- Coverage result: `Complete`

#### Step 4 - `adjust Arena migration date`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkTilgang`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkTilgang`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_ARENA_MIGRERINGSDATO_INNGAATT_AVTALE`
- Failure condition: agreement entered.
- Why this is a business failure: migration boundary is frozen after entry.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.justerArenaMigreringsdato`

##### Failure 4
- Source discriminator: `FORLENG_MIDLERTIDIG_IKKE_TILGJENGELIG`
- Failure condition: new calculation hits unsupported reduction-date relation.
- Why this is a business failure: period invariant.
- Violated state, rule, relationship, ownership, or eligibility condition: calculation consistency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/RegnUtTilskuddsperioderForAvtale.java — RegnUtTilskuddsperioderForAvtale.beregnTilskuddsperioderForAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `4`
- Documented failure blocks for this invocation: `4`
- Coverage result: `Complete`

#### Optional Step V1 - `dry-run Arena migration date adjustment`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.justerArenaMigreringsdatoDryRun`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.justerArenaMigreringsdatoDryRun`

##### Failure 3
- Source discriminator: `FORLENG_MIDLERTIDIG_IKKE_TILGJENGELIG`
- Failure condition: The reduction date lies after the requested subsidy-period interval, a date relation that the period calculator does not support.
- Why this is a business failure: The calculator cannot produce a valid reduced-rate period set for this date relationship.
- Violated state, rule, relationship, ownership, or eligibility condition: The reduction date lies after the requested subsidy-period interval, a date relation that the period calculator does not support.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/RegnUtTilskuddsperioderForAvtale.java — RegnUtTilskuddsperioderForAvtale.beregnTilskuddsperioderForAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

#### Step 2 - `retrieve agreement by id`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
Dry-run mutates the loaded entity in memory with `dryRun=false` passed to the domain method, but no transaction/save is declared; persistent endpoint alone blocks entered agreements. For paid/sent-claim periods, cleanup logs and continues rather than rejecting.

<a id="behavior-23"></a>
### Behavior 23: Refresh Participant Follow-Up and NAV Units

Business goal:
Refresh a specific agreement's participant name, follow-up classification, geographic unit, and unit names from authoritative sources.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
External participant/routing state legitimately changes outside this service. Refreshing it is a complete independent goal over an existing agreement and has no required successor.

Starting state:
Existing advisor-accessible agreement; external PDL/Arena/Norg2 state may have changed.

Terminal business outcome:
Latest participant and routing fields are persisted.

Required execution workflow:

#### Step 1: Refresh routing state
- Use function `refresh follow-up unit` (`POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet`) with the known `{avtaleId}` and no body.
- Actor: advisor.
- State before: possibly stale participant/unit fields.
- State transition or decision: reload PDL name/protection, Arena follow-up/eligibility, geographic and follow-up units.
- Output/state after: updated agreement returned.
- API-visible outputs: `200 OK` with the saved serialized `Avtale` body.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Agreement participant fnr drives all lookups.

Business result and side effects:
Aggregate saved; no explicit domain event or `sistEndretNå` call occurs.

Constraints and invariants:
Advisor needs object access; protected participants and ineligible classifications reject.

Business failure branches:

#### Step 1 - `refresh follow-up unit`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.hentAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.hentAvtale`

##### Failure 3
- Source discriminator: `IKKE_TILGANG_TIL_DELTAKER`
- Failure condition: refreshed PDL data shows code 6.
- Why this is a business failure: participant protection.
- Violated state, rule, relationship, ownership, or eligibility condition: protected-person processing.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkKode6`

##### Failure 4
- Source discriminator: `HENTING_AV_INNSATS_BEHOV_FEILET`
- Failure condition: Arena classification missing/invalid/ineligible.
- Why this is a business failure: concrete participant measure eligibility.
- Violated state, rule, relationship, ownership, or eligibility condition: follow-up qualification.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.sjekkOgHentOppfølgingStatus`; `src/main/java/no/nav/tag/tiltaksgjennomforing/enhet/VeilarbArenaClient.java — VeilarbArenaClient.sjekkStatus`

Business failure coverage:
- Source-ledger business branches for this invocation: `4`
- Documented failure blocks for this invocation: `4`
- Coverage result: `Complete`

Implementation notes:
The endpoint is not annotated `@Transactional`; repository save still persists the final aggregate, but no concurrency guard is used.

<a id="behavior-24"></a>
### Behavior 24: Annul an Agreement

Business goal:
Terminate a mistaken or no-longer-valid agreement and cancel eligible subsidy periods.

Primary actor(s):
NAV advisor.

Workflow boundary rationale:
Annulment is a new termination goal over legitimately pre-existing agreement state; creating or entering the agreement belongs to a prior behavior. A required read precedes the mutation because the annulment endpoint enforces a freshness value that no starting-state persistence fact exposes to an API caller.

Starting state:
Existing non-terminal agreement with no paid or approved-refund period. The caller knows `{avtaleId}` but does not yet know the current `sistEndret` value.

Terminal business outcome:
`ANNULLERT`, reason/timestamp stored, and eligible periods removed or annulled.

Required execution workflow:

#### Step 1: Read the freshness value
- Use function `retrieve agreement by id` (`GET /avtaler/{avtaleId}`) with the caller-known `{avtaleId}` and `innlogget-part=VEILEDER`.
- Actor: advisor.
- State before: the non-terminal agreement exists, but its freshness value is not yet known to the API caller.
- State transition or decision: reads the current aggregate without mutation.
- Output/state after: persisted state is unchanged and the caller has the current `sistEndret`.
- API-visible outputs: `200 OK` with the serialized `Avtale` body including `id` and `sistEndret`.
- Handoff to later step: Step 2 uses response-body `sistEndret` as `If-Unmodified-Since`.

#### Step 2: Annul aggregate
- Use function `annul agreement` (`POST /avtaler/{avtaleId}/annuller`) with the caller-known `{avtaleId}`, Step 1 response-body `sistEndret` as `If-Unmodified-Since`, and `body={annullertGrunn:"Agreement created in error"}`.
- Actor: advisor.
- State before: non-terminal agreement.
- State transition or decision: protect paid/refunded periods, remove unhandled periods, annul approved periods, set annulment fields; assign advisor if unassigned.
- Output/state after: terminal agreement and event.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
The caller supplies the starting-state `{avtaleId}` to both steps. Step 1 response-body `sistEndret` is the exact Step 2 freshness header; advisor ident is stored if previously unassigned and on the event.

Business result and side effects:
`Feilregistrering` reason additionally sets `feilregistrert=true`; period/annul events may create notifications and outbound messages.

Constraints and invariants:
No paid/corrected or approved-claim period; agreement not already annulled/interrupted.

Business failure branches:

#### Step 1 - `retrieve agreement by id`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the caller-known `{avtaleId}` no longer resolves to an agreement.
- Why this is a business failure: the termination workflow has no aggregate to inspect or annul.
- Violated state, rule, relationship, ownership, or eligibility condition: the selected agreement must still exist when freshness is obtained.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.hentAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException("Ikke tilgang til avtale")`
- Failure condition: the advisor does not have the object-scoped participant relationship required to read the selected agreement.
- Why this is a business failure: the freshness and agreement details belong to a concrete participant-scoped aggregate.
- Violated state, rule, relationship, ownership, or eligibility condition: the advisor must have access to this agreement's participant.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.hentAvtale; Avtalepart.sjekkTilgang`

Business failure coverage:
- Source-ledger business branches for this invocation: `2`
- Documented failure blocks for this invocation: `2`
- Coverage result: `Complete`

#### Step 2 - `annul agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: termination target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.annuller`

##### Failure 2
- Source discriminator: `SAMTIDIGE_ENDRINGER`
- Failure condition: stale/missing timestamp.
- Why this is a business failure: termination binds current state.
- Violated state, rule, relationship, ownership, or eligibility condition: concurrency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.annullerAvtale`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkSistEndret`

##### Failure 3
- Source discriminator: `AVTALE_INNEHOLDER_UTBETALT_TILSKUDDSPERIODE`
- Failure condition: any period has `UTBETALT` or `KORRIGERT`.
- Why this is a business failure: paid subsidy is financially final.
- Violated state, rule, relationship, ownership, or eligibility condition: annulment eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.sjekkAtIkkeAvtalenInneholderUtbetaltTilskuddsperiode`

##### Failure 4
- Source discriminator: `AVTALE_INNEHOLDER_TILSKUDDSPERIODE_MED_GODKJENT_REFUSJON`
- Failure condition: period has sent/approved claim status.
- Why this is a business failure: approved reimbursement blocks aggregate annulment.
- Violated state, rule, relationship, ownership, or eligibility condition: financial finality.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.annuller`

##### Failure 5
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: agreement already annulled or interrupted.
- Why this is a business failure: terminal transition duplicate/incompatible.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.annuller`

Business failure coverage:
- Source-ledger business branches for this invocation: `5`
- Documented failure blocks for this invocation: `5`
- Coverage result: `Complete`

Implementation notes:
The controller path does not call advisor object-scoped `sjekkTilgang`; only generic advisor admission and concurrency/domain guards apply.

<a id="behavior-25"></a>
### Behavior 25: Soft-Delete an Agreement

Business goal:
Conceal a specific agreement from all ordinary party access without physically deleting it.

Primary actor(s):
Configured NAV cleanup advisor.

Workflow boundary rationale:
Soft deletion is an independent operational business outcome over existing data and completes in one operation; prior creation is not part of the concealment goal.

Starting state:
Existing visible agreement.

Terminal business outcome:
`slettemerket=true`; ordinary reads/lists reject or omit it.

Required execution workflow:

#### Step 1: Mark deleted
- Use function `soft-delete agreement` (`POST /avtaler/{avtaleId}/slettemerk`) with the known `{avtaleId}` and no body.
- Actor: configured cleanup advisor.
- State before: visible agreement.
- State transition or decision: set flag and register `AvtaleSlettemerket`.
- Output/state after: hidden agreement.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Advisor ident is event actor.

Business result and side effects:
Flag/event persist; no child rows are removed.

Constraints and invariants:
Caller must have concrete agreement access. The configured delete-marker admin gate is generic endpoint admission and excluded.

Business failure branches:

#### Step 1 - `soft-delete agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement absent.
- Why this is a business failure: target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.slettemerk`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: advisor lacks participant access or agreement already soft-deleted.
- Why this is a business failure: concrete ownership/visibility.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement access.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Veileder.java — Veileder.slettemerk`; `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.harTilgang`

Business failure coverage:
- Source-ledger business branches for this invocation: `2`
- Documented failure blocks for this invocation: `2`
- Coverage result: `Complete`

Implementation notes:
Repeated deletion fails the object-access check because `harTilgang` returns false for an already marked agreement.

<a id="behavior-26"></a>
### Behavior 26: Obtain the Employer's Portal Agreement Portfolio

Business goal:
Obtain all currently employer-visible agreements for one company in Min Side Arbeidsgiver.

Primary actor(s):
Employer.

Workflow boundary rationale:
The returned portal portfolio is a complete read-only actor goal; creation or later mutation is not required.

Starting state:
Existing domain state; agreements originate from prior independent workflows.

Terminal business outcome:
Employer-visible company list with sensitive termination reasons removed.

Required execution workflow:

#### Step 1: List company portfolio
- Use function `list employer agreements for Min Side Arbeidsgiver` (`GET /avtaler/min-side-arbeidsgiver`) with `bedriftNr=111222333`.
- Actor: employer.
- State before: zero or more company agreements.
- State transition or decision: filter by concrete Altinn company/measure access and time visibility.
- Output/state after: portfolio list.
- API-visible outputs: `200 OK` with a JSON array of accessible `Avtale` objects.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
`bedriftNr` is filtered against employer rights.

Business result and side effects:
Read-only; returns empty when no accessible agreements.

Constraints and invariants:
Termination reasons and qualification group are redacted; old terminated agreements expire from access.

Business failure branches:

#### Step 1 - `list employer agreements for Min Side Arbeidsgiver`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
Requesting a company without rights produces an empty filtered list rather than a rejection.

<a id="behavior-27"></a>
### Behavior 27: Obtain the Decision-Maker Work Queue

Business goal:
Obtain pending or filtered subsidy decisions within the decision-maker's NAV-unit and participant scope.

Primary actor(s):
NAV decision-maker.

Workflow boundary rationale:
The queue is independently meaningful work allocation; no mutation is necessary to complete the listing goal.

Starting state:
Existing advisor-approved subsidy agreements.

Terminal business outcome:
Paginated queue of decision-eligible agreements/periods.

Required execution workflow:

#### Step 1: Fetch decision queue
- Use function `list decision-maker agreements` (`GET /avtaler/beslutter-liste`) with `tilskuddPeriodeStatus=UBEHANDLET`, `navEnhet=1234`, `sorteringskolonne=startDato`, `page=0`, `size=20`, and `sorteringOrder=ASC`.
- Actor: decision-maker.
- State before: pending decisions may exist.
- State transition or decision: query assigned units, default status `UBEHANDLET`, three-month horizon, and post-filter participant access.
- Output/state after: queue and paging metadata.
- API-visible outputs: `200 OK` body with `avtaler`, `size`, `currentPage`, `totalItems`, and `totalPages`.
- Handoff to later step: period decision may be a separate workflow.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Decision-maker NAV units define default `navEnhet` set.

Business result and side effects:
Read-only.

Constraints and invariants:
Only summer job and wage-subsidy measures are included by default.

Business failure branches:

#### Step 1 - `list decision-maker agreements`

##### Failure 1
- Source discriminator: `NavEnhetIkkeFunnetException`
- Failure condition: decision-maker has no NAV units.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: unit assignment.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.finnGodkjenteAvtalerMedTilskuddsperiodestatusOgNavEnheterListe`

##### Failure 2
- Source discriminator: `NAV_ENHET_IKKE_FUNNET`
- Failure condition: the decision-maker has no NAV unit available for a work queue.
- Why this is a business failure: Accepting this condition would persist a domain result that does not satisfy the operation's stated business prerequisite.
- Violated state, rule, relationship, ownership, or eligibility condition: the decision-maker has no NAV unit available for a work queue.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Beslutter.java — Beslutter.finnGodkjenteAvtalerMedTilskuddsperiodestatusOgNavEnheterListe`

Business failure coverage:
- Source-ledger business branches for this invocation: `2`
- Documented failure blocks for this invocation: `2`
- Coverage result: `Complete`

Implementation notes:
Rows failing participant access are silently removed after paging totals are calculated, so returned content count may not match metadata.

<a id="behavior-28"></a>
### Behavior 28: Resolve a Valid Employer Organization

Business goal:
Confirm a company number identifies a virksomhet suitable for an agreement and obtain its business name.

Primary actor(s):
Advisor or employer UI user.

Workflow boundary rationale:
The lookup result is complete employer-selection decision support. It requires no service state or successor, satisfying the read-only one-step rule.

Starting state:
No prior service state.

Terminal business outcome:
Valid `ArbeidsgiverOrganisasjon` details.

Required execution workflow:

#### Step 1: Resolve organization
- Use function `look up organization` (`GET /organisasjoner`) with `bedriftNr=111222333`.
- Actor: agreement creator.
- State before: candidate company number.
- State transition or decision: Ereg type classification.
- Output/state after: organization domain object.
- API-visible outputs: `200 OK` with `Organisasjon` fields from the resolved virksomhet.
- Handoff to later step: value may be used in creation, but lookup goal is complete.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Company number is reused if caller later creates an agreement.

Business result and side effects:
Read-only.

Constraints and invariants:
Only virksomhet is accepted, not juridisk enhet or organisasjonsledd.

Business failure branches:

#### Step 1 - `look up organization`

##### Failure 1
- Source discriminator: `ENHET_FINNES_IKKE`
- Failure condition: Ereg has no matching unit.
- Why this is a business failure: selected employer does not exist.
- Violated state, rule, relationship, ownership, or eligibility condition: organization identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/EregService.java — EregService.hentVirksomhet`

##### Failure 2
- Source discriminator: `ENHET_ER_JURIDISK`
- Failure condition: number names a legal entity.
- Why this is a business failure: agreement requires virksomhet.
- Violated state, rule, relationship, ownership, or eligibility condition: organization type.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/OrganisasjonController.java — OrganisasjonController.hentVirksomhet`

##### Failure 3
- Source discriminator: `ENHET_ER_ORGLEDD`
- Failure condition: number names an organizational link.
- Why this is a business failure: agreement requires virksomhet.
- Violated state, rule, relationship, ownership, or eligibility condition: organization type.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/orgenhet/OrganisasjonController.java — OrganisasjonController.hentVirksomhet`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

Implementation notes:
`EregService` maps all HTTP client exceptions, including availability failures, to `ENHET_FINNES_IKKE`; only the concrete not-found meaning is retained here.

<a id="behavior-29"></a>
### Behavior 29: Obtain an Approved Agreement PDF

Business goal:
Obtain the official rendered agreement document after advisor approval.

Primary actor(s):
Any agreement party with object access.

Workflow boundary rationale:
The PDF is an independently meaningful artifact. The approved agreement is a stable prerequisite produced by an earlier workflow; no successor is required.

Starting state:
Existing advisor-approved accessible agreement.

Terminal business outcome:
PDF bytes with inline agreement filename/content headers.

Required execution workflow:

#### Step 1: Download document
- Use function `download agreement PDF` (`GET /avtaler/{avtaleId}/pdf`) with the known `{avtaleId}` and `innlogget-part=VEILEDER`.
- Actor: agreement party.
- State before: advisor-approved agreement.
- State transition or decision: object access and approval gate, then render.
- Output/state after: PDF artifact.
- API-visible outputs: `200 OK` with PDF bytes, `Content-Type: application/pdf`, inline `Content-Disposition`, and `Content-Length`.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
- Optional Step V1: Use function `check whether Salesforce dialog should be shown` (`GET /avtaler/{avtaleId}/vis-salesforce-dialog`) with the known `{avtaleId}` and `innlogget-part=VEILEDER` to decide whether a temporary-wage-subsidy follow-up dialog applies.

Parameter, identity, and state bindings:
Role affects both access and PDF rendering.

Business result and side effects:
Read-only document generation.

Constraints and invariants:
Advisor approval is required; `avtaleInngått` is not explicitly required.

Business failure branches:

#### Step 1 - `download agreement PDF`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.hentAvtalePdf`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.hentAvtalePdf`

##### Failure 3
- Source discriminator: `KAN_IKKE_LASTE_NED_PDF`
- Failure condition: advisor approval absent.
- Why this is a business failure: document is not official yet.
- Violated state, rule, relationship, ownership, or eligibility condition: artifact readiness.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.hentAvtalePdf`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

#### Optional Step V1 - `check whether Salesforce dialog should be shown`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the referenced domain aggregate or child resource does not exist.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: the referenced domain aggregate or child resource does not exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.visSalesforceDialog`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Why this is a business failure: The operation is scoped to a concrete agreement-party or resource-ownership relationship that does not include this caller.
- Violated state, rule, relationship, ownership, or eligibility condition: the caller lacks the persisted object-scoped relationship required for this aggregate.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AvtaleController.java — AvtaleController.visSalesforceDialog`

Business failure coverage:
- Source-ledger business branches for this invocation: `2`
- Documented failure blocks for this invocation: `2`
- Coverage result: `Complete`

Implementation notes:
The dialog returns true only for configured follow-up units, temporary wage subsidy, and derived `GJENNOMFØRES`/`AVSLUTTET`; false is a valid decision result.

<a id="behavior-30"></a>
### Behavior 30: Review and Clear Agreement Notifications

Business goal:
Find unread agreement work addressed to the caller and mark handled notifications read.

Primary actor(s):
Any agreement party.

Workflow boundary rationale:
The list supplies owned `varselId` values; the later mutation consumes them and completes the notification-handling goal. Single and bulk marking are equivalent variants.

Starting state:
Existing unread `Varsel` rows produced by prior agreement events.

Terminal business outcome:
Selected caller-owned notifications have `lest=true`.

Required execution workflow:

#### Step 1: Discover unread notifications
- Use function `list overview notifications` (`GET /varsler/oversikt`) with `innlogget-part=VEILEDER`.
- Actor: agreement party.
- State before: zero or more unread bell notifications.
- State transition or decision: role identifiers filter owned notifications.
- Output/state after: unread notifications including `varselId` and `avtaleId`.
- API-visible outputs: `200 OK` with unread bell-visible `Varsel` objects, including each `id`.
- Handoff to later step: Step 2 consumes a selected id.

#### Step 2: Mark one handled notification
- Use function `mark notification as read` (`POST /varsler/{varselId}/sett-til-lest`) with `{varselId}` from one Step 1 response item field `id` and `innlogget-part=VEILEDER`.
- Actor: agreement party.
- State before: owned unread notification.
- State transition or decision: set `lest=true`.
- Output/state after: notification no longer appears in unread views.
- API-visible outputs: `200 OK` with an empty body.
- Handoff to later step: None.

Alternative valid workflow paths:

Alternative Path A:
- Branch point: After required Step 1.
- Replaces: Required Step 2.
- Retains or resumes: A1 completes the same read-state goal for every selected id.

#### Alternative Step A1: Mark selected notifications read
- Use function `mark multiple notifications as read` (`POST /varsler/sett-alle-til-lest`) with caller-owned `varselIder` from Step 1 and the same `innlogget-part`.
- Actor: Agreement party.
- State before: Selected notifications are unread.
- State transition or decision: Runs each owned-id update in one transaction.
- Output/state after: All selected notifications are read.
- API-visible outputs: `200 OK` empty body.
- Handoff to later step: None.

Optional verification/supporting steps:
- Optional Step V1: Use function `list agreement modal notifications` (`GET /varsler/avtale-modal`) with Step 1 response-item `avtaleId` and `innlogget-part=VEILEDER` for the unread subset.
- Optional Step V2: Use function `list agreement notification log` (`GET /varsler/avtale-logg`) with Step 1 response-item `avtaleId` and `innlogget-part=VEILEDER` for full role-specific history.

Parameter, identity, and state bindings:
`varselId` from Step 1 must be readable through the same caller's `Avtalepart.identifikatorer`; employer can own notifications through multiple company identifiers.

Business result and side effects:
Read flag persists. Bulk implementation calls the single-item method sequentially inside one transaction.

Constraints and invariants:
Only owned notifications can be marked; agreement log additionally requires agreement access.

Business failure branches:

#### Step 1 - `list overview notifications`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 2 - `mark notification as read`

##### Failure 1
- Source discriminator: `NullPointerException at varsel.settTilLest()` after `VarselRepository.findByIdAndIdentifikatorIn` returns `null`
- Failure condition: no notification with `{varselId}` belongs to any identifier of the selected `innlogget-part`, whether because the notification is absent or belongs to another receiver.
- Why this is a business failure: the caller cannot complete or take ownership of another receiver's notification.
- Violated state, rule, relationship, ownership, or eligibility condition: the notification must exist and be owned by one of the caller's concrete identifiers.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/varsel/VarselController.java — VarselController.settTilLest`

Business failure coverage:
- Source-ledger business branches for this invocation: `1`
- Documented failure blocks for this invocation: `1`
- Coverage result: `Complete`

#### Alternative Step A1 - `mark multiple notifications as read`

##### Failure 1
- Source discriminator: `findByIdAndIdentifikatorIn`
- Failure condition: at least one id is missing/not owned.
- Why this is a business failure: mixed list violates concrete ownership.
- Violated state, rule, relationship, ownership, or eligibility condition: all-or-nothing receiver scope.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/varsel/VarselController.java — VarselController.settFlereVarslerTilLest`

Business failure coverage:
- Source-ledger business branches for this invocation: `1`
- Documented failure blocks for this invocation: `1`
- Coverage result: `Complete`

#### Optional Step V1 - `list agreement modal notifications`
None. The repository filters by caller identifiers; unknown agreement id yields an empty valid list.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Optional Step V2 - `list agreement notification log`

##### Failure 1
- Source discriminator: `NoSuchElementException`
- Failure condition: `avtaleRepository.findById(avtaleId)` returns empty and `orElseThrow()` emits `NoSuchElementException`.
- Why this is a business failure: notification history cannot be scoped to a non-existent agreement.
- Violated state, rule, relationship, ownership, or eligibility condition: the agreement that owns the requested notification history must exist.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/varsel/VarselController.java — VarselController.hentAlleVarslerForAvtale`

##### Failure 2
- Source discriminator: `TilgangskontrollException`
- Failure condition: caller cannot access agreement.
- Why this is a business failure: communication history ownership.
- Violated state, rule, relationship, ownership, or eligibility condition: agreement-party relation.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtalepart.java — Avtalepart.sjekkTilgang`

Business failure coverage:
- Source-ledger business branches for this invocation: `2`
- Documented failure blocks for this invocation: `2`
- Coverage result: `Complete`

Implementation notes:
Single-item ownership failure manifests as a null dereference rather than a deliberate domain exception. The bulk transaction rolls back earlier read flags if a later id fails.

<a id="behavior-31"></a>
### Behavior 31: Journal Approved Agreement Versions

Business goal:
Export every approved, unjournaled agreement version and record the journal post id returned for each version.

Primary actor(s):
Journal integration system.

Workflow boundary rationale:
The GET produces version UUIDs and journal payloads; the PUT consumes those exact UUIDs with externally created journal post ids. The two operations are one causal integration workflow.

Starting state:
Existing advisor-approved `AvtaleInnhold` versions with `journalpostId=null`.

Terminal business outcome:
Successfully journaled versions carry their external journal post ids and no longer appear in the work list.

Required execution workflow:

#### Step 1: Export unjournaled versions
- Use function `list unjournaled agreements` (`GET /internal/avtaler`) with no path, query, or body values to retrieve journal payloads containing each `avtaleVersjonId`.
- Actor: configured journal system user.
- State before: approved version(s) without marker.
- State transition or decision: map each version and its aggregate periods to `AvtaleTilJournalfoering`.
- Output/state after: payloads keyed by version UUID.
- API-visible outputs: `200 OK` with `AvtaleTilJournalfoering` objects whose payload includes the agreement-version UUID needed by Step 2.
- Handoff to later step: external journal service returns one `journalpostId` per UUID.

#### Step 2: Mark journal completion
- Use function `mark agreement versions as journaled` (`PUT /internal/avtaler`) with JSON body `{"{avtaleVersjonIdFromStep1}":"453997128"}`, where the UUID key is Step 1 response-body `avtaleVersjonId` and the string value is the journal system's returned journal-post id.
- Actor: journal system.
- State before: exported versions.
- State transition or decision: set marker on every found version and save all.
- Output/state after: journal completion persisted.
- API-visible outputs: `200 OK` with an empty body.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
The id is `AvtaleInnhold.id`, not `Avtale.id`. External journal post id is the map value.

Business result and side effects:
Only version rows change. Unknown map keys are silently ignored by `findAllById`.

Constraints and invariants:
Only advisor-approved, unmarked versions are exported by repository query.

Business failure branches:

#### Step 1 - `list unjournaled agreements`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Step 2 - `mark agreement versions as journaled`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
Configured system-user validation is a generic endpoint gate and excluded. The PUT does not verify that every supplied UUID exists or that journal ids are non-null.

<a id="behavior-32"></a>
### Behavior 32: Repair Missing Wage-Subsidy Totals on Selected Agreements

Business goal:
Recalculate derived wage-subsidy totals for a selected set whose base fields exist but `sumLonnstilskudd` is missing.

Primary actor(s):
Authorized operations staff.

Workflow boundary rationale:
This is an operational batch action over existing records, expressly allowed to start from persisted state. It reaches the repair outcome directly; no API predecessor/successor belongs to the repair goal.

Starting state:
Existing selected subsidy agreements with missing derived sum and complete base inputs.

Terminal business outcome:
Derived wage-subsidy values are persisted for every successfully processed id.

Required execution workflow:

#### Step 1: Recalculate selected records
- Use function `recalculate wage subsidy for selected agreements` (`POST /utvikler-admin/reberegn`) with JSON body `["11111111-1111-4111-8111-111111111111"]`, representing an existing selected agreement id already known to operations staff.
- Actor: operations staff.
- State before: selected inconsistent aggregates.
- State transition or decision: load, validate, recalculate, and save each in request order.
- Output/state after: repaired values for processed agreements.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Each list UUID is independently loaded and saved.

Business result and side effects:
Agreement content changes; no domain event is registered. The method is not transactional across the list, so earlier saves remain if a later id fails.

Constraints and invariants:
Only three subsidy measures; aggregate non-terminal; sum must be null while percentage, tax, holiday rate, monthly wage, and OTP exist.

Business failure branches:

#### Step 1 - `recalculate wage subsidy for selected agreements`

##### Failure 1
- Source discriminator: `NoSuchElementException`
- Failure condition: `avtaleRepository.findById(avtaleId)` returns empty for one selected UUID and the no-argument `orElseThrow()` emits `NoSuchElementException`.
- Why this is a business failure: the selected repair item has no agreement aggregate to recalculate.
- Violated state, rule, relationship, ownership, or eligibility condition: every selected UUID must identify an existing agreement.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.reberegnLønnstilskudd`

##### Failure 2
- Source discriminator: `KAN_IKKE_ENDRE_ANNULLERT_AVTALE`
- Failure condition: selected agreement terminal.
- Why this is a business failure: closed aggregate cannot be repaired by this method.
- Violated state, rule, relationship, ownership, or eligibility condition: lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.reberegnLønnstilskudd`

##### Failure 3
- Source discriminator: `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE`
- Failure condition: selected measure is not subsidy-backed.
- Why this is a business failure: derived wage-subsidy values are defined only for the three subsidy-backed measures accepted by this repair.
- Violated state, rule, relationship, ownership, or eligibility condition: measure eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.krevEnAvTiltakstyper`

##### Failure 4
- Source discriminator: `KAN_IKKE_REBEREGNE`
- Failure condition: sum already exists or any required base field is missing.
- Why this is a business failure: record does not match repair invariant.
- Violated state, rule, relationship, ownership, or eligibility condition: repair eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.reberegnLønnstilskudd`

Business failure coverage:
- Source-ledger business branches for this invocation: `4`
- Documented failure blocks for this invocation: `4`
- Coverage result: `Complete`

Implementation notes:
Developer-admin group validation is excluded; partial persistence across ids is business-significant.

<a id="behavior-33"></a>
### Behavior 33: Repair Missing Permanent-Subsidy Reduction Data

Business goal:
Preview and then repair entered permanent wage-subsidy agreements missing reduction date/reduced sum, rebuilding migration-aware periods.

Primary actor(s):
Authorized operations staff.

Workflow boundary rationale:
Dry-run is a preflight over the same selected population; persistent batch is the terminal repair and shares the same migration-date input.

Starting state:
Existing entered `VARIG_LONNSTILSKUDD` rows with null reduction date; state legitimately predates/falls outside current write path.

Terminal business outcome:
Eligible agreements have reduction date/sum and regenerated period state.

Required execution workflow:

#### Step 1: Persist batch repair
- Use function `fix missing reduced-percent date in batch` (`POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}`) with `migreringsDato=2026-08-01`.
- Actor: operations staff.
- State before: eligible historical inconsistencies.
- State transition or decision: repository-select entered permanent subsidy, further filter percentage >67, duration >12 months, non-terminal/non-null sum, recalculate reduction, rebuild periods, save.
- Output/state after: eligible rows repaired; ineligible rows skipped.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
- Optional Step V1: Use function `dry-run missing reduced-percent date fix` (`POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}`) with `migreringsDato=2026-08-01` to count/log eligible rows without saving.

Parameter, identity, and state bindings:
Use identical migration date for preview and persistence.

Business result and side effects:
Each eligible aggregate is saved within the controller transaction; period changes and annulment events may occur.

Constraints and invariants:
The controller filter is narrower than the repository query and avoids domain rejection for ordinary data.

Business failure branches:

#### Step 1 - `fix missing reduced-percent date in batch`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Optional Step V1 - `dry-run missing reduced-percent date fix`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
The dry-run path never invokes domain calculation and ignores `migreringsDato` except as a bound path value. In the persistent path, `nyeTilskuddsperioderEtterMigreringFraArena` returning false is a valid logged skip, not failure.

<a id="behavior-34"></a>
### Behavior 34: Backfill Subsidy Periods for One Agreement

Business goal:
Generate migration-aware subsidy periods for a single historical agreement.

Primary actor(s):
Authorized operations staff.

Workflow boundary rationale:
Historical persisted state is the legitimate input to this one-step repair; the function internally calculates and saves the complete child-row outcome.

Starting state:
Existing agreement with sufficient calculation fields and a state eligible for period generation.

Terminal business outcome:
New periods exist, with pre-migration periods marked `BEHANDLET_I_ARENA`.

Required execution workflow:

#### Step 1: Generate periods
- Use function `generate subsidy periods for one agreement` (`POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}`) with the known `{avtaleId}` and `migreringsDato=2026-08-01`.
- Actor: operations staff.
- State before: missing/incorrect period children.
- State transition or decision: calculate and add eligible periods.
- Output/state after: aggregate saved.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Path agreement id and migration date drive calculation.

Business result and side effects:
Child periods persist; approved unpaid periods may be annulled during cleanup.

Constraints and invariants:
The declared starting state excludes incomplete or terminal agreements; those inputs produce a different successful no-op result.

Business failure branches:

#### Step 1 - `generate subsidy periods for one agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement id unknown.
- Why this is a business failure: repair target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.lagTilskuddsperioderPåEnAvtale`

##### Failure 2
- Source discriminator: `FORLENG_MIDLERTIDIG_IKKE_TILGJENGELIG`
- Failure condition: period inputs and reduction boundary hit unsupported relation.
- Why this is a business failure: calculation invariant.
- Violated state, rule, relationship, ownership, or eligibility condition: interval consistency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/RegnUtTilskuddsperioderForAvtale.java — RegnUtTilskuddsperioderForAvtale.beregnTilskuddsperioderForAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `2`
- Documented failure blocks for this invocation: `2`
- Coverage result: `Complete`

Implementation notes:
The method can return false internally while endpoint still succeeds and saves unchanged aggregate.

<a id="behavior-35"></a>
### Behavior 35: Recalculate Unhandled Subsidy Periods

Business goal:
Remove and regenerate only the unhandled tail of a selected subsidy agreement after data correction.

Primary actor(s):
Authorized operations staff.

Workflow boundary rationale:
This is a complete targeted repair over existing records; no API predecessor belongs to the operational goal.

Starting state:
Existing subsidy agreement with period collection.

Terminal business outcome:
Unhandled tail is regenerated from the last approved end (or first period start), and sequence numbers are normalized.

Required execution workflow:

#### Step 1: Recalculate open tail
- Use function `recalculate unhandled subsidy periods` (`POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}`) with the known `{avtaleId}` and no body.
- Actor: operations staff.
- State before: existing period collection.
- State transition or decision: delete unhandled children, calculate replacement tail, renumber all.
- Output/state after: repaired collection persisted.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Last approved period determines new start; otherwise implementation uses collection first element.

Business result and side effects:
Child rows/sequence change; no explicit domain event.

Constraints and invariants:
Measure must be subsidy-backed and collection must provide an anchor.

Business failure branches:

#### Step 1 - `recalculate unhandled subsidy periods`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement missing.
- Why this is a business failure: repair target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.reberegnUbehandledeTilskuddsperioder`

##### Failure 2
- Source discriminator: `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE`
- Failure condition: non-subsidy measure.
- Why this is a business failure: period repair inapplicable.
- Violated state, rule, relationship, ownership, or eligibility condition: measure eligibility.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.reberegnUbehandledeTilskuddsperioder`

##### Failure 3
- Source discriminator: `NoSuchElementException`
- Failure condition: after removing every `UBEHANDLET` period, no `GODKJENT` period and no other period remain, so `tilskuddPeriode.first()` emits `NoSuchElementException`.
- Why this is a business failure: regeneration has no persisted period boundary from which to calculate the replacement tail.
- Violated state, rule, relationship, ownership, or eligibility condition: the post-removal collection must retain an approved or other period anchor.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.reberegnUbehandledeTilskuddsperioder`

##### Failure 4
- Source discriminator: `FORLENG_MIDLERTIDIG_IKKE_TILGJENGELIG`
- Failure condition: replacement calculation hits unsupported reduction relation.
- Why this is a business failure: calculation invariant.
- Violated state, rule, relationship, ownership, or eligibility condition: interval consistency.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/RegnUtTilskuddsperioderForAvtale.java — RegnUtTilskuddsperioderForAvtale.beregnTilskuddsperioderForAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `4`
- Documented failure blocks for this invocation: `4`
- Coverage result: `Complete`

Implementation notes:
The method does not explicitly preserve prior non-approved/non-unhandled statuses when choosing its start anchor.

<a id="behavior-36"></a>
### Behavior 36: Diagnose Subsidy-Period Date Ordering

Business goal:
Find entered temporary-wage-subsidy agreements whose period dates regress relative to sequence numbers.

Primary actor(s):
Authorized operations staff.

Workflow boundary rationale:
The diagnostic log is the independently meaningful operational result; it is read-only and complete in one invocation.

Starting state:
Existing entered temporary wage-subsidy records.

Terminal business outcome:
Every detected date-order problem is logged with the known `{tilskuddsperiodeId}` obtained from the existing agreement state.

Required execution workflow:

#### Step 1: Run diagnostic
- Use function `find subsidy period date-order problems` (`POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer`) to scan eligible agreements.
- Actor: operations staff.
- State before: period data may be inconsistent.
- State transition or decision: compare each sequence >1 to previous sequence start.
- Output/state after: warnings identify problems; no persistence.
- API-visible outputs: `200 OK` with no body; findings exist only in logs.
- Handoff to later step: separate repair may be chosen.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Repository scan selects temporary wage subsidy, entered, non-empty collections.

Business result and side effects:
Logs only.

Constraints and invariants:
Sequence numbers are expected contiguous.

Business failure branches:

#### Step 1 - `find subsidy period date-order problems`

##### Failure 1
- Source discriminator: `NoSuchElementException`
- Failure condition: a period has sequence >1 but predecessor sequence is absent.
- Why this is a business failure: The selected aggregate or child is the business subject of this operation; without it, the requested outcome has no valid target.
- Violated state, rule, relationship, ownership, or eligibility condition: period continuity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.finnTilskuddsperioderMedFeilDatoer`

Business failure coverage:
- Source-ledger business branches for this invocation: `1`
- Documented failure blocks for this invocation: `1`
- Coverage result: `Complete`

Implementation notes:
The diagnostic can fail on a sequence gap before reporting all date regressions.

<a id="behavior-37"></a>
### Behavior 37: Annul One Subsidy Period

Business goal:
Mark one selected period annulled and emit cancellation information where applicable.

Primary actor(s):
Authorized operations staff.

Workflow boundary rationale:
Targeted operational correction starts from existing child state and reaches its complete outcome in one function.

Starting state:
Existing subsidy period whose `refusjonStatus` is not `UTGÅTT`.

Terminal business outcome:
Period status is `ANNULLERT` and a cancellation event is registered.

Required execution workflow:

#### Step 1: Annul period
- Use function `annul subsidy period` (`POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}`) with the known `{tilskuddsperiodeId}` obtained from the existing agreement state.
- Actor: operations staff.
- State before: selected period.
- State transition or decision: set status to `ANNULLERT`, register the event, and save period and agreement.
- Output/state after: annulled period.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Period repository relation supplies owning agreement.

Business result and side effects:
Period and aggregate are saved; `TilskuddsperiodeAnnullert` is emitted.

Constraints and invariants:
Expired reimbursement is outside the declared starting state because it has a different successful no-op result.

Business failure branches:

#### Step 1 - `annul subsidy period`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: period id unknown.
- Why this is a business failure: child target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: period identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.annullerTilskuddsperiode`

Business failure coverage:
- Source-ledger business branches for this invocation: `1`
- Documented failure blocks for this invocation: `1`
- Coverage result: `Complete`

Implementation notes:
No source state guard prevents repeated annulment; it can emit another event unless reimbursement is expired.

<a id="behavior-38"></a>
### Behavior 38: Replace a Period and Resend It as Approved

Business goal:
Annul a selected period and create an active approved copy for downstream resend.

Primary actor(s):
Authorized operations staff.

Workflow boundary rationale:
The function atomically performs both causal child transitions and emits the resend event; existing period state is legitimate repair input.

Starting state:
Existing subsidy-backed period suitable for annulment.

Terminal business outcome:
Original is annulled/retained active for history and new approved child is added with copied approval attribution.

Required execution workflow:

#### Step 1: Annul and resend
- Use function `annul and resend subsidy period as approved` (`POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}`) with the known `{tilskuddsperiodeId}` obtained from the existing agreement state.
- Actor: operations staff.
- State before: selected period.
- State transition or decision: annul original, create approved replacement, calculate resend number, emit approved event.
- Output/state after: replacement persisted.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: downstream producer consumes event.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Replacement copies dates, amount, percentage, advisor/unit approval data and sequence but gets new UUID.

Business result and side effects:
Aggregate saved transactionally; annul and approved events registered.

Constraints and invariants:
Owning measure must be subsidy-backed; original must actually reach annulled state.

Business failure branches:

#### Step 1 - `annul and resend subsidy period as approved`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: period absent.
- Why this is a business failure: target missing.
- Violated state, rule, relationship, ownership, or eligibility condition: child identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.annullerOgResendTilskuddsperiode`

##### Failure 2
- Source discriminator: `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE`
- Failure condition: owning agreement not subsidy-backed.
- Why this is a business failure: replacement semantics inapplicable.
- Violated state, rule, relationship, ownership, or eligibility condition: measure.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.lagNyGodkjentTilskuddsperiodeFraAnnullertPeriode`

##### Failure 3
- Source discriminator: `TILSKUDDSPERIODE_ER_ALLEREDE_BEHANDLET`
- Failure condition: `annullerTilskuddsperiode` did not leave original `ANNULLERT` (notably `RefusjonStatus.UTGÅTT`).
- Why this is a business failure: replacement requires an annulled source.
- Violated state, rule, relationship, ownership, or eligibility condition: child lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.annullerOgResendTilskuddsperiode`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

Implementation notes:
The error code wording is counterintuitive: it is raised when the source is not annulled at replacement time.

<a id="behavior-39"></a>
### Behavior 39: Replace a Period with an Unhandled Copy

Business goal:
Annul a selected period and return an equivalent copy to ordinary decisioning.

Primary actor(s):
Authorized operations staff.

Workflow boundary rationale:
One operation reaches the complete correction state over existing child data.

Starting state:
Existing subsidy-backed period.

Terminal business outcome:
Original annulled; active unhandled replacement with new UUID.

Required execution workflow:

#### Step 1: Annul and regenerate
- Use function `annul and generate unhandled subsidy period` (`POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}`) with the known `{tilskuddsperiodeId}` obtained from the existing agreement state.
- Actor: operations staff.
- State before: selected period.
- State transition or decision: annul original and clone as unhandled.
- Output/state after: new decision work item.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: ordinary decision workflow may consume it.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Child id supplies owner and source data.

Business result and side effects:
Aggregate saved; annulment event may be emitted.

Constraints and invariants:
Same measure/source-state rules as approved replacement.

Business failure branches:

#### Step 1 - `annul and generate unhandled subsidy period`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: period absent.
- Why this is a business failure: target missing.
- Violated state, rule, relationship, ownership, or eligibility condition: child identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.annullerOgGenererTilskuddsperiode`

##### Failure 2
- Source discriminator: `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE`
- Failure condition: owning measure not subsidy-backed.
- Why this is a business failure: period semantics inapplicable.
- Violated state, rule, relationship, ownership, or eligibility condition: measure.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.lagNyTilskuddsperiodeFraAnnullertPeriode`

##### Failure 3
- Source discriminator: `TILSKUDDSPERIODE_ER_ALLEREDE_BEHANDLET`
- Failure condition: source is not annulled after attempted annulment, notably expired reimbursement.
- Why this is a business failure: invalid replacement source.
- Violated state, rule, relationship, ownership, or eligibility condition: child lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.annullerOgGenererTilskuddsperiode`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

Implementation notes:
Replacement loses decision attribution and unit because `deaktiverOgLagNyUbehandlet` copies only calculation/interval/sequence fields.

<a id="behavior-40"></a>
### Behavior 40: Reclassify Historical Periods as Arena-Treated

Business goal:
For one agreement, replace periods ending before a cutoff with Arena-treated copies.

Primary actor(s):
Authorized operations staff.

Workflow boundary rationale:
This is a complete historical-data repair over existing aggregate state; no API-created predecessor belongs to it.

Starting state:
Existing subsidy agreement with periods before cutoff.

Terminal business outcome:
Matched originals are annulled and active replacements have `BEHANDLET_I_ARENA`.

Required execution workflow:

#### Step 1: Reclassify period set
- Use function `annul and generate Arena-treated periods before date` (`POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}`) with the known `{avtaleId}` and `dato=2026-08-01`.
- Actor: operations staff.
- State before: historical periods.
- State transition or decision: query all ending before date, annul each, clone as Arena-treated.
- Output/state after: corrected period set saved.
- API-visible outputs: `200 OK` with no response body and no refreshed `sistEndret`/`Last-Modified` header.
- Handoff to later step: None.

Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
Cutoff is exclusive (`sluttDatoBefore`).

Business result and side effects:
One transaction across all matched periods; annulment events may be emitted.

Constraints and invariants:
Owning agreement must be subsidy-backed; every source must become annulled.

Business failure branches:

#### Step 1 - `annul and generate Arena-treated periods before date`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: agreement unknown.
- Why this is a business failure: repair target absent.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.annullerOgGenererBehandletIArenaPerioder`

##### Failure 2
- Source discriminator: `KAN_IKKE_ENDRE_FEIL_TILTAKSTYPE`
- Failure condition: owning agreement not subsidy-backed.
- Why this is a business failure: period subtype inapplicable.
- Violated state, rule, relationship, ownership, or eligibility condition: measure.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/Avtale.java — Avtale.lagNyBehandletIArenaTilskuddsperiodeFraAnnullertPeriode`

##### Failure 3
- Source discriminator: `TILSKUDDSPERIODE_ER_ALLEREDE_BEHANDLET`
- Failure condition: any selected source is not `ANNULLERT` after annul attempt, notably expired reimbursement.
- Why this is a business failure: invalid replacement source.
- Violated state, rule, relationship, ownership, or eligibility condition: child lifecycle.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/avtale/AdminController.java — AdminController.annullerOgGenererBehandletIArenaPerioder`

Business failure coverage:
- Source-ledger business branches for this invocation: `3`
- Documented failure blocks for this invocation: `3`
- Coverage result: `Complete`

Implementation notes:
The declared starting state includes at least one matched period. A failure on one period rolls back the transaction.

<a id="behavior-41"></a>
### Behavior 41: Synchronize Selected Agreements to the Data Warehouse

Business goal:
Create data-warehouse patch messages for an explicitly selected set of agreement ids.

Primary actor(s):
Authorized DVH operations staff.

Workflow boundary rationale:
This selected operation is synchronous, accepts explicit ids without entered/advisor-approved filtering, and silently ignores unknown ids. Those guarantees differ from the filtered asynchronous all-agreement operation, so it is a separate behavior.

Starting state:
Existing agreements requiring targeted resynchronization; the operator already knows selected agreement id `11111111-1111-4111-8111-111111111111`.

Terminal business outcome:
One persisted DvhMeldingEntitet patch row for each selected id that exists.

Required execution workflow:

#### Step 1: Patch selected agreements
- Use function `patch selected agreements to data warehouse` (`POST /utvikler-admin/dvh-melding/patch`) with `body={avtaleIder:["11111111-1111-4111-8111-111111111111"]}`, where that UUID is the caller-known selected agreement id declared in the starting state.
- Actor: Authorized DVH operations staff.
- State before: Zero or more selected ids exist in the agreement repository.
- State transition or decision: Loads only existing ids, constructs each current Avro snapshot, and saves one patch message row per found agreement.
- Output/state after: All found selected agreements have patch rows when the synchronous call returns; missing ids were ignored.
- API-visible outputs: `200 OK` with no response body.
- Handoff to later step: None.


Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
The operator supplies the selected UUIDs. Message UUID and timestamp are generated internally and are not returned.

Business result and side effects:
Agreement state is unchanged. The persisted message rows are later eligible for Kafka production.

Constraints and invariants:
This endpoint does not apply the all-agreement path's entered/advisor-approved eligibility filter.

Business failure branches:

#### Step 1 - `patch selected agreements to data warehouse`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
Unknown ids are successful ignored items because findAllById returns only found rows. Evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/datavarehus/InternalDvhMeldingProdusentController.java — InternalDvhMeldingProdusentController.patcheAvtale`.

<a id="behavior-42"></a>
### Behavior 42: Synchronize All Eligible Agreements to the Data Warehouse Asynchronously

Business goal:
Start a bulk repair that creates data-warehouse patch messages for every entered, advisor-approved agreement.

Primary actor(s):
Authorized DVH operations staff.

Workflow boundary rationale:
The all-agreement function filters eligibility and dispatches an asynchronous service. HTTP completion is acceptance, not completion of message-row persistence, so it cannot be a variant of Behavior 41.

Starting state:
Existing agreement population; no selected ids are required.

Terminal business outcome:
The asynchronous bulk patch job is accepted for the full eligible population.

Required execution workflow:

#### Step 1: Start full eligible-population patch
- Use function `patch all agreements to data warehouse` (`POST /utvikler-admin/dvh-melding/patchalleavtaler`) with no body.
- Actor: Authorized DVH operations staff.
- State before: Zero or more entered agreements exist.
- State transition or decision: Returns after invoking the async service; the worker later filters out records lacking advisor approval and saves one patch row per eligible agreement.
- Output/state after: HTTP acceptance has completed; eventual row creation remains asynchronous.
- API-visible outputs: `200 OK` with no response body.
- Handoff to later step: None.


Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
The implementation generates each message UUID and records system as the performing actor; no generated value is returned.

Business result and side effects:
Eventually, each eligible record gets its own transaction-scoped patch row. Worker failures after dispatch are not represented by the HTTP response.

Constraints and invariants:
Only records with avtaleInngått and advisor approval are eligible.

Business failure branches:

#### Step 1 - `patch all agreements to data warehouse`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
The async boundary is `src/main/java/no/nav/tag/tiltaksgjennomforing/datavarehus/DvhAvtalePatchService.java — DvhAvtalePatchService.lagDvhPatchMeldingForAlleAvtaler`.

<a id="behavior-43"></a>
### Behavior 43: Backfill One Agreement Event Message

Business goal:
Create one current-state agreement event message for a specifically selected agreement.

Primary actor(s):
Operations staff.

Workflow boundary rationale:
This synchronous selected-id function returns only after attempting one row and rejects an unknown target. The bulk function is asynchronous and best-effort per item, so the two paths are separate behaviors.

Starting state:
Existing agreement with a known `avtaleId` whose current content is serializable by the event-message mapper.

Terminal business outcome:
One current `STATUSENDRING` message row is persisted for the selected agreement.

Required execution workflow:

#### Step 1: Backfill selected agreement
- Use function `send agreement event message for one agreement` (`POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}`) with the pre-existing target's known `{avtaleId}` and no body.
- Actor: Operations staff.
- State before: The selected agreement exists.
- State transition or decision: Serializes current agreement/content and attempts to save one `AvtaleMeldingEntitet`; JSON serialization failure is caught and only logged.
- Output/state after: One selected message row exists.
- API-visible outputs: `200 OK` with no response body.
- Handoff to later step: None.


Alternative valid workflow paths:
None.

Optional verification/supporting steps:
None.

Parameter, identity, and state bindings:
The path id selects the aggregate. Message UUID and timestamp are generated internally and not returned.

Business result and side effects:
Agreement state is unchanged; the selected event-message row is saved synchronously.

Constraints and invariants:
The private bulk filter is not consulted. Serialization failure is outside the declared serializable starting state and produces a different successful no-op result.

Business failure branches:

#### Step 1 - `send agreement event message for one agreement`

##### Failure 1
- Source discriminator: `RessursFinnesIkkeException`
- Failure condition: the selected agreement id is absent.
- Why this is a business failure: the requested replay target does not exist.
- Violated state, rule, relationship, ownership, or eligibility condition: aggregate identity.
- Implementation evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/datadeling/AvtaleHendelseController.java — AvtaleHendelseController.sendMeldingForEnAvtale`

Business failure coverage:
- Source-ledger business branches for this invocation: `1`
- Documented failure blocks for this invocation: `1`
- Coverage result: `Complete`

Implementation notes:
The controller omits its own developer-group check on this one-item route. That generic admission discrepancy is not a business failure.

<a id="behavior-44"></a>
### Behavior 44: Backfill All Agreement Event Messages Asynchronously

Business goal:
Start a bulk backfill over the entire agreement population, optionally previewing serialization first.

Primary actor(s):
Authorized operations staff.

Workflow boundary rationale:
Both persistent and dry-run functions use the same all-record scan and asynchronous completion model; dry-run is a preflight for the persistent bulk action.

Starting state:
Existing agreement population.

Terminal business outcome:
The asynchronous all-record message backfill is accepted.

Required execution workflow:

#### Step 1: Start all-record event backfill
- Use function `send agreement event messages for all agreements` (`POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler`) with no body.
- Actor: Authorized operations staff.
- State before: Zero or more agreements exist.
- State transition or decision: Dispatches an asynchronous scan; each normally serialized agreement later gets a STATUSENDRING row, while per-record JSON failures are logged and skipped.
- Output/state after: HTTP acceptance is complete; eventual backfill may be partial.
- API-visible outputs: `200 OK` with no response body.
- Handoff to later step: None.


Alternative valid workflow paths:
None.

Optional verification/supporting steps:
- Optional Step V1: Use function `dry-run event messages for all agreements` (`POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler`) with no body to asynchronously serialize every agreement without saving message rows.

Parameter, identity, and state bindings:
No ids are supplied. Each persistent row receives an internally generated UUID and timestamp; neither is returned.

Business result and side effects:
Agreement state is unchanged. The async worker saves rows best-effort and catches per-record serialization failures.

Constraints and invariants:
The bulk eligibility method currently returns true for every record.

Business failure branches:

#### Step 1 - `send agreement event messages for all agreements`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

#### Optional Step V1 - `dry-run event messages for all agreements`
None.

Business failure coverage:
- Source-ledger business branches for this invocation: `0`
- Documented failure blocks for this invocation: `0`
- Coverage result: `Complete`

Implementation notes:
Async behavior is implemented in `src/main/java/no/nav/tag/tiltaksgjennomforing/datadeling/AvtaleHendelseService.java — AvtaleHendelseService.sendAvtaleHendelseMeldingPåAlleAvtaler` and sendAvtaleHendelseMeldingPåAlleAvtalerDRYRun.

## Function-to-Workflow Map

| Function | Behavior(s) | Exact step or supporting role | Reuse explanation |
|---|---|---|---|
| `list accessible agreements` | 1 | Behavior 1 – Step 1 | Standalone worklist. |
| `create advisor agreement` | 3, 5, 6, 9 | Behavior 3 – Step 1; Behavior 5 – Step 1; Behavior 6 – Step 1; Behavior 9 – Step 1 | Shared producer for distinct entry/rejection/revision outcomes. |
| `create Arena cleanup agreement` | 22 | Behavior 22 – Step 1 | Cleanup-specific creation. |
| `create employer agreement` | 5, 6, 8 | Behavior 5 – Alternative Step A1; Behavior 6 – Alternative Step A1; Behavior 8 – Step 1 | Creates unassigned variant used by entry and assignment. |
| `create mentor agreement as advisor` | 4 | Behavior 4 – Step 1 | Mentor journey. |
| `create mentor agreement as employer` | 4 | Behavior 4 – Alternative Step A1 | Same mentor outcome with different initiator. |
| `check participant overlap` | 3 | Behavior 3 – Optional Step V1 | Decision support before/during draft. |
| `search agreements and save search` | 2 | Behavior 2 – Step 1 | Produces `sokId`. |
| `replay saved agreement search` | 2 | Behavior 2 – Step 2 | Consumes `sokId`. |
| `retrieve agreement by id` | 3, 4, 5, 6, 9, 19, 22, 24 | Behavior 3 – Step 2; Behavior 3 – Step 5; Behavior 3 – Step 7; Behavior 3 – Alternative Step A2; Behavior 3 – Alternative Step A4; Behavior 4 – Step 2; Behavior 4 – Step 5; Behavior 4 – Step 7; Behavior 4 – Step 9; Behavior 4 – Alternative Step A3; Behavior 5 – Step 2; Behavior 5 – Step 5; Behavior 5 – Step 7; Behavior 5 – Alternative Step A3; Behavior 6 – Step 2; Behavior 6 – Step 5; Behavior 6 – Step 7; Behavior 6 – Alternative Step A3; Behavior 9 – Step 2; Behavior 9 – Step 6; Behavior 19 – Step 1; Behavior 22 – Step 2; Behavior 24 – Step 1 | Supplies current state and child ids. |
| `retrieve agreement by agreement number` | 3 | Behavior 3 – Optional Step V4 | Alternate lookup. |
| `list agreement versions` | 3, 12 | Behavior 3 – Optional Step V5; Behavior 12 – Optional Step V2 | Verifies version outcomes. |
| `update agreement` | 3, 4, 5, 6, 9, 22 | Behavior 3 – Step 3; Behavior 4 – Step 3; Behavior 5 – Step 3; Behavior 6 – Step 3; Behavior 9 – Step 3; Behavior 9 – Step 7; Behavior 22 – Step 3 | Shared causal step for distinct lifecycle outcomes. |
| `dry-run agreement update` | 3, 9 | Behavior 3 – Optional Step V2; Behavior 9 – Optional Step V1 | Same rules, no persistence. |
| `share agreement with a party` | 3, 4 | Behavior 3 – Optional Step V3; Behavior 4 – Optional Step V1 | Reused by each explicitly listed workflow invocation. |
| `approve agreement as participant` | 3, 4, 5, 6, 9 | Behavior 3 – Step 4; Behavior 3 – Alternative Step A3; Behavior 4 – Step 6; Behavior 5 – Step 4; Behavior 5 – Alternative Step C1; Behavior 6 – Step 4; Behavior 6 – Alternative Step C1; Behavior 9 – Step 4 | Required external approval or partial state setup. |
| `approve agreement as employer` | 3, 4, 5, 6, 9 | Behavior 3 – Step 6; Behavior 3 – Alternative Step A1; Behavior 4 – Step 8; Behavior 4 – Alternative Step B1; Behavior 5 – Step 6; Behavior 5 – Alternative Step B1; Behavior 6 – Step 6; Behavior 6 – Alternative Step B1; Behavior 9 – Alternative Step A1 | Required external approval or revision setup. |
| `sign mentor confidentiality declaration` | 4 | Behavior 4 – Step 4 | Mentor-specific prerequisite. |
| `approve agreement as advisor` | 3, 4, 5, 6 | Behavior 3 – Step 8; Behavior 4 – Step 10; Behavior 5 – Step 8; Behavior 6 – Step 8 | Enters non-decision or hands subsidy agreement to decision-maker. |
| `approve on behalf of participant` | 4, 5, 6 | Behavior 4 – Alternative Step B2; Behavior 5 – Alternative Step B2; Behavior 6 – Alternative Step B2 | Replaces participant plus advisor steps where allowed. |
| `approve on behalf of employer` | 5, 6 | Behavior 5 – Alternative Step C2; Behavior 6 – Alternative Step C2 | Only subsidy/summer-job measures. |
| `approve on behalf of participant and employer` | 5, 6 | Behavior 5 – Alternative Step D1; Behavior 6 – Alternative Step D1 | Combined subsidy/summer-job path. |
| `revoke approvals` | 9 | Behavior 9 – Step 5; Behavior 9 – Alternative Step A2 | Causally re-enables update. |
| `mark agreement eligible for after-registration` | 10 | Behavior 10 – Step 1 | Standalone authorization milestone. |
| `remove after-registration eligibility` | 11 | Behavior 11 – Step 1 | Standalone reversal. |
| `approve subsidy period` | 5 | Behavior 5 – Step 9 | Approval terminal outcome. |
| `reject subsidy period` | 6 | Behavior 6 – Step 9 | Rejection terminal outcome. |
| `send rejected subsidy period back` | 7 | Behavior 7 – Step 1 | Correction/requeue outcome. |
| `shorten agreement` | 12 | Behavior 12 – Step 1 | Versioned amendment. |
| `dry-run agreement shortening` | 12 | Behavior 12 – Optional Step V1 | Preflight. |
| `extend agreement` | 13 | Behavior 13 – Step 1 | Versioned amendment. |
| `dry-run agreement extension` | 13 | Behavior 13 – Optional Step V1 | Preflight. |
| `change subsidy calculation` | 14 | Behavior 14 – Step 1 | Financial amendment. |
| `dry-run subsidy calculation change` | 14 | Behavior 14 – Optional Step V1 | Preflight. |
| `change contact information` | 15 | Behavior 15 – Step 1 | Contact amendment. |
| `change job description` | 16 | Behavior 16 – Step 1 | Job amendment. |
| `change follow-up and adaptation text` | 17 | Behavior 17 – Step 1 | Commitment amendment. |
| `change work-training goals` | 18 | Behavior 18 – Step 1 | Goal replacement. |
| `change inclusion subsidy expenses` | 19 | Behavior 19 – Step 2 | Expense replacement. |
| `change mentor details` | 20 | Behavior 20 – Step 1 | Mentor amendment. |
| `change cost center` | 21 | Behavior 21 – Step 1 | Pre-entry period/cost-center update. |
| `adjust Arena migration date` | 22 | Behavior 22 – Step 4 | Persists cleanup boundary. |
| `dry-run Arena migration date adjustment` | 22 | Behavior 22 – Optional Step V1 | Preflight. |
| `get employer account number` | 5 | Behavior 5 – Optional Step V1 | Financial setup. |
| `download agreement PDF` | 5, 29 | Behavior 5 – Optional Step V3; Behavior 29 – Step 1 | Independently meaningful document, also useful after advisor approval. |
| `check whether Salesforce dialog should be shown` | 29 | Behavior 29 – Optional Step V1 | Used only for this workflow outcome. |
| `refresh follow-up unit` | 23 | Behavior 23 – Step 1 | Standalone external-state refresh. |
| `take over agreement as advisor` | 4, 5, 6, 8 | Behavior 4 – Alternative Step A2; Behavior 5 – Alternative Step A2; Behavior 6 – Alternative Step A2; Behavior 8 – Step 2 | Shared employer-created draft handoff. |
| `annul agreement` | 24 | Behavior 24 – Step 2 | Termination outcome. |
| `soft-delete agreement` | 25 | Behavior 25 – Step 1 | Concealment outcome. |
| `list employer agreements for Min Side Arbeidsgiver` | 26 | Behavior 26 – Step 1 | Used only for this workflow outcome. |
| `list decision-maker agreements` | 5, 6, 27 | Behavior 5 – Optional Step V2; Behavior 6 – Optional Step V1; Behavior 27 – Step 1 | Queue supports decisions and is independently useful. |
| `look up organization` | 28 | Behavior 28 – Step 1 | Employer-selection decision support. |
| `list overview notifications` | 30 | Behavior 30 – Step 1 | Produces ids. |
| `list agreement modal notifications` | 30 | Behavior 30 – Optional Step V1 | Scoped unread view. |
| `list agreement notification log` | 30 | Behavior 30 – Optional Step V2 | History view. |
| `mark notification as read` | 30 | Behavior 30 – Step 2 | Single completion. |
| `mark multiple notifications as read` | 30 | Behavior 30 – Alternative Step A1 | Bulk equivalent. |
| `list unjournaled agreements` | 31 | Behavior 31 – Step 1 | Produces version ids/payloads. |
| `mark agreement versions as journaled` | 31 | Behavior 31 – Step 2 | Consumes journal results. |
| `recalculate wage subsidy for selected agreements` | 32 | Behavior 32 – Step 1 | Used only for this workflow outcome. |
| `fix missing reduced-percent date in batch` | 33 | Behavior 33 – Step 1 | Used only for this workflow outcome. |
| `dry-run missing reduced-percent date fix` | 33 | Behavior 33 – Optional Step V1 | Preflight/count. |
| `generate subsidy periods for one agreement` | 34 | Behavior 34 – Step 1 | Used only for this workflow outcome. |
| `recalculate unhandled subsidy periods` | 35 | Behavior 35 – Step 1 | Open-tail repair. |
| `find subsidy period date-order problems` | 36 | Behavior 36 – Step 1 | Diagnostic goal. |
| `annul subsidy period` | 37 | Behavior 37 – Step 1 | Child correction. |
| `annul and resend subsidy period as approved` | 38 | Behavior 38 – Step 1 | Used only for this workflow outcome. |
| `annul and generate unhandled subsidy period` | 39 | Behavior 39 – Step 1 | Unhandled replacement. |
| `annul and generate Arena-treated periods before date` | 40 | Behavior 40 – Step 1 | Used only for this workflow outcome. |
| `patch selected agreements to data warehouse` | 41 | Behavior 41 – Step 1 | Used only for this workflow outcome. |
| `patch all agreements to data warehouse` | 42 | Behavior 42 – Step 1 | Used only for this workflow outcome. |
| `send agreement event message for one agreement` | 43 | Behavior 43 – Step 1 | Used only for this workflow outcome. |
| `send agreement event messages for all agreements` | 44 | Behavior 44 – Step 1 | Used only for this workflow outcome. |
| `dry-run event messages for all agreements` | 44 | Behavior 44 – Optional Step V1 | All replay preflight. |

Excluded functions (`get logged-in user`, `get Altinn rights request URLs`, `get all code lists`, `get status code list`, `get measure type code list`, `evaluate feature toggles`, `get feature variants`, and `health check`) intentionally do not appear in this map because they are generic caller-context, link/configuration, feature-configuration, code-list, or operational-health capabilities rather than domain workflows.

## Cross-Workflow Observations

- Lifecycle status is derived, not stored: annulment/interruption first, then entered plus dates, then content completeness. A caller cannot safely infer editability from status text alone; approval flags, period states, and terminal flags matter.
- Advisor approval is the final party approval, but it is not always agreement entry. Decision measures enter only on first subsidy-period approval. This is the most important actor handoff in the service.
- `sistEndret` protects ordinary update and party approval, but many high-impact endpoints either have no concurrency header or accept one and ignore it (shorten/extend). Post-entry amendments are therefore last-write-wins at the API layer.
- Domain events are registered on the `Avtale` aggregate and published when the repository saves it. Synchronous listeners create notifications and integration rows; transactional/async producers mean some externally visible effects occur after commit. Admin functions that mutate without registering an event can leave downstream systems stale unless replay workflows are used.
- Transaction boundaries vary materially. Most single aggregate controller methods are transactional. Notification bulk marking is all-or-nothing. Arena historical replacement is all-or-nothing. Selected wage recalculation is not batch-transactional and can partially persist before a later item fails. All-record DVH/event replays are asynchronous, so endpoint return is not completion.
- Several implementation access discrepancies are source-backed: sharing, approval revocation, cost-center change, and agreement annulment omit the advisor object-access check used by neighboring operations; the single-agreement event replay omits its controller's group check. These are recorded as notes, not invented success guarantees.
- Unknown saved search and missing selected DVH ids yield valid empty/ignored results; missing notification ownership instead causes a null dereference. The analysis follows those concrete semantics rather than normalizing them.
- OpenAPI/function documentation understates branch granularity. Implementation adds measure-specific duration/date limits, Arena qualification outcomes, code-6 protection, approval sequencing, period decision windows, paid/refund finality, child-collection synchronization, and organization-type failures.
- The misleadingly named `KanIkkeOppretteAvtalePåKode6Exception` and `KanIkkeGodkjenneAvtalePåKode6Exception` constructors both pass `Feilkode.IKKE_TILGANG_TIL_DELTAKER` to `FeilkodeException`; the failure ledgers therefore use the emitted `IKKE_TILGANG_TIL_DELTAKER` discriminator rather than invented class-name-like codes. Evidence: `src/main/java/no/nav/tag/tiltaksgjennomforing/exceptions/KanIkkeOppretteAvtalePåKode6Exception.java` and `src/main/java/no/nav/tag/tiltaksgjennomforing/exceptions/KanIkkeGodkjenneAvtalePåKode6Exception.java`.
- Some dry runs are imperfect previews: Arena migration dry-run lacks the persistent endpoint's entered-agreement guard; event dry-run serializes but does not create message rows; missing-reduction dry-run only counts and does not execute domain calculations.
- Cap inconsistency is implementation-backed: draft inclusion strategy uses 136700 while post-entry inclusion expense amendment rejects totals above 136000.
- Capabilities not composable through the inventoried public functions include interruption/restoration and reimbursement lifecycle changes; their domain methods/events exist, but no corresponding function in `full-behavior.md` can complete those journeys.
