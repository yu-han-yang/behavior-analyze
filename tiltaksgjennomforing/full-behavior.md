Analyzed only `src` and `tiltaksgjennomforing.json`. I found 79 documented operations; all are covered below. I did not execute the API.

### Behavior 1: list accessible agreements

Behavior name:
list accessible agreements

Successful execution:
- Result:
  Returns a paginated list of agreements visible to the logged-in party, filtered by the query fields.
- Endpoint sequence:
  Step 1: `GET /avtaler`
- Constraints:
  `innlogget-part` selects the role. Query fields from `AvtalePredicate` constrain the repository search. `page` and `size` are normalized with `Math.abs`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The role cookie and token issuer are not a supported combination.
  - Endpoint group:
    Step 1: `GET /avtaler`
  - Failure endpoint:
    `GET /avtaler`
  - Why this fails:
    `InnloggingService.hentAvtalepart` rejects invalid issuer-role combinations.
  - Intentionally violated constraints:
    Use an unsupported `innlogget-part` for the authenticated token.

Endpoint coverage:
- Covers:
  `GET /avtaler`
- Distinct meaning:
  Role-scoped agreement listing.

### Behavior 2: create advisor agreement

Behavior name:
create advisor agreement

Successful execution:
- Result:
  Creates a new agreement for a participant and employer as a NAV advisor and returns a `Location` pointing to `/avtaler/{avtaleId}`.
- Endpoint sequence:
  Step 1: `POST /avtaler`
- Constraints:
  Body must contain `deltakerFnr`, `bedriftNr`, and `tiltakstype`. The logged-in advisor must have write access to the participant. The participant cannot be under 16; `SOMMERJOBB` also rejects participants over the age limit.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Advisor lacks write access to the participant.
  - Endpoint group:
    Step 1: `POST /avtaler`
  - Failure endpoint:
    `POST /avtaler`
  - Why this fails:
    `Veileder.sjekkTilgangskontroll` throws access failure before saving.
  - Intentionally violated constraints:
    Use a `deltakerFnr` the advisor cannot access.

Endpoint coverage:
- Covers:
  `POST /avtaler`
- Distinct meaning:
  Normal advisor-created agreement.

### Behavior 3: create Arena cleanup agreement

Behavior name:
create Arena cleanup agreement

Successful execution:
- Result:
  Creates a new advisor agreement and also marks it as an Arena cleanup agreement.
- Endpoint sequence:
  Step 1: `POST /avtaler`
- Constraints:
  Same creation constraints as Behavior 2. Query parameter `ryddeavtale=true` must be supplied; the created `{avtaleId}` is reused internally to save an `ArenaRyddeAvtale`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement creation itself is invalid.
  - Endpoint group:
    Step 1: `POST /avtaler`
  - Failure endpoint:
    `POST /avtaler`
  - Why this fails:
    The cleanup marker is only saved after a valid agreement is created.
  - Intentionally violated constraints:
    Omit required body fields or use a participant the advisor cannot access.

Endpoint coverage:
- Covers:
  `POST /avtaler`
- Distinct meaning:
  Advisor-created agreement with `ryddeavtale=true`.

### Behavior 4: create employer agreement

Behavior name:
create employer agreement

Successful execution:
- Result:
  Creates a new agreement as an employer and returns a `Location` for `/avtaler/{avtaleId}`.
- Endpoint sequence:
  Step 1: `POST /avtaler/opprett-som-arbeidsgiver`
- Constraints:
  Body must contain `deltakerFnr`, `bedriftNr`, and `tiltakstype`. The employer must have Altinn access for the selected company and measure type.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Employer lacks Altinn access for `bedriftNr` and `tiltakstype`.
  - Endpoint group:
    Step 1: `POST /avtaler/opprett-som-arbeidsgiver`
  - Failure endpoint:
    `POST /avtaler/opprett-som-arbeidsgiver`
  - Why this fails:
    `Arbeidsgiver.tilgangTilBedriftVedOpprettelseAvAvtale` rejects the creation.
  - Intentionally violated constraints:
    Use a company or measure not present in the employer’s Altinn rights.

Endpoint coverage:
- Covers:
  `POST /avtaler/opprett-som-arbeidsgiver`
- Distinct meaning:
  Employer-created agreement.

### Behavior 5: create mentor agreement as advisor

Behavior name:
create mentor agreement as advisor

Successful execution:
- Result:
  Creates a mentor agreement where the advisor is the creator.
- Endpoint sequence:
  Step 1: `POST /avtaler/opprett-mentor-avtale`
- Constraints:
  Body must include `deltakerFnr`, `mentorFnr`, `bedriftNr`, `tiltakstype`, and `avtalerolle=VEILEDER`. For the intended mentor workflow, `tiltakstype` should be `MENTOR`. `deltakerFnr` and `mentorFnr` must differ.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Participant and mentor have the same national identity number.
  - Endpoint group:
    Step 1: `POST /avtaler/opprett-mentor-avtale`
  - Failure endpoint:
    `POST /avtaler/opprett-mentor-avtale`
  - Why this fails:
    The controller explicitly rejects equal `deltakerFnr` and `mentorFnr`.
  - Intentionally violated constraints:
    Set `deltakerFnr` equal to `mentorFnr`.

Endpoint coverage:
- Covers:
  `POST /avtaler/opprett-mentor-avtale`
- Distinct meaning:
  Mentor agreement created through advisor role.

### Behavior 6: create mentor agreement as employer

Behavior name:
create mentor agreement as employer

Successful execution:
- Result:
  Creates a mentor agreement where the employer is the creator.
- Endpoint sequence:
  Step 1: `POST /avtaler/opprett-mentor-avtale`
- Constraints:
  Body must include `avtalerolle=ARBEIDSGIVER`. The employer must have Altinn access for `bedriftNr` and `tiltakstype`. `deltakerFnr` and `mentorFnr` must differ.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `avtalerolle` is neither `VEILEDER` nor `ARBEIDSGIVER`.
  - Endpoint group:
    Step 1: `POST /avtaler/opprett-mentor-avtale`
  - Failure endpoint:
    `POST /avtaler/opprett-mentor-avtale`
  - Why this fails:
    The implementation never creates an agreement and throws `Opprett Mentor fant ingen avtale å behandle`.
  - Intentionally violated constraints:
    Use `avtalerolle=DELTAKER`, `MENTOR`, or `BESLUTTER`.

Endpoint coverage:
- Covers:
  `POST /avtaler/opprett-mentor-avtale`
- Distinct meaning:
  Mentor agreement created through employer role.

### Behavior 7: check participant overlap

Behavior name:
check participant overlap

Successful execution:
- Result:
  Returns existing agreements that overlap the participant, measure type, and optional period.
- Endpoint sequence:
  Step 1: `GET /avtaler/deltaker-allerede-paa-tiltak`
- Constraints:
  Requires `deltakerFnr` and `tiltakstype`. If `avtaleId`, `startDato`, and `sluttDato` are all provided, the check excludes that agreement and uses the full date interval; otherwise it checks from `startDato` or today.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `avtaleId` is supplied but is not a UUID.
  - Endpoint group:
    Step 1: `GET /avtaler/deltaker-allerede-paa-tiltak`
  - Failure endpoint:
    `GET /avtaler/deltaker-allerede-paa-tiltak`
  - Why this fails:
    The controller calls `UUID.fromString(avtaleId)`.
  - Intentionally violated constraints:
    Use a non-UUID `avtaleId`.

Endpoint coverage:
- Covers:
  `GET /avtaler/deltaker-allerede-paa-tiltak`
- Distinct meaning:
  Overlap search before or during agreement editing.

### Behavior 8: search agreements and save search

Behavior name:
search agreements and save search

Successful execution:
- Result:
  Returns filtered agreements and stores or reuses a saved search id derived from the request body.
- Endpoint sequence:
  Step 1: `POST /avtaler/sok`
- Constraints:
  Body is `AvtalePredicate`. The response includes `sokeParametere`, `sorteringskolonne`, and `sokId`. If an identical hash exists, usage counters are updated; otherwise a new saved search is created.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Unsupported role/token combination.
  - Endpoint group:
    Step 1: `POST /avtaler/sok`
  - Failure endpoint:
    `POST /avtaler/sok`
  - Why this fails:
    The search is role-scoped through `InnloggingService.hentAvtalepart`.
  - Intentionally violated constraints:
    Use an `innlogget-part` role not valid for the authenticated issuer.

Endpoint coverage:
- Covers:
  `POST /avtaler/sok`
- Distinct meaning:
  Filtered search plus persisted search id.

### Behavior 9: replay saved agreement search

Behavior name:
replay saved agreement search

Successful execution:
- Result:
  Looks up a saved search by `sokId`, reruns it, increments usage, and returns the same response shape as search.
- Endpoint sequence:
  Step 1: `POST /avtaler/sok`
  Step 2: `GET /avtaler/sok`
- Constraints:
  Step 1 produces `sokId`; Step 2 consumes that exact `sokId`. The same role constraints apply to the result filtering.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `sokId` is unknown.
  - Endpoint group:
    Step 1: `GET /avtaler/sok`
  - Failure endpoint:
    `GET /avtaler/sok`
  - Why this fails:
    It does not throw; it returns an empty result with `sokId=""`, which is an exceptional successful branch.
  - Intentionally violated constraints:
    Omit the prior `POST /avtaler/sok` or use an unrelated `sokId`.

Endpoint coverage:
- Covers:
  `GET /avtaler/sok`
- Distinct meaning:
  Replaying a previously saved search.

### Behavior 10: retrieve agreement by id

Behavior name:
retrieve agreement by id

Successful execution:
- Result:
  Retrieves the agreement identified by `{avtaleId}` for the selected logged-in party.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
- Constraints:
  Step 1 returns `Location: /avtaler/{avtaleId}`; Step 2 consumes that `{avtaleId}`. The role in `innlogget-part` must have access to the agreement.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement does not exist.
  - Endpoint group:
    Step 1: `GET /avtaler/{avtaleId}`
  - Failure endpoint:
    `GET /avtaler/{avtaleId}`
  - Why this fails:
    `Avtalepart.hentAvtale` throws when the repository cannot find the id.
  - Intentionally violated constraints:
    Omit any agreement creation endpoint and use an unknown `{avtaleId}`.

Endpoint coverage:
- Covers:
  `GET /avtaler/{avtaleId}`
- Distinct meaning:
  Resource-scoped agreement read.

### Behavior 11: retrieve agreement by agreement number

Behavior name:
retrieve agreement by agreement number

Successful execution:
- Result:
  Retrieves an agreement by its generated agreement number.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `GET /avtaler/avtaleNr/{avtaleNr}`
- Constraints:
  Step 1 produces `{avtaleId}` in `Location`. Step 2 returns the agreement’s `avtaleNr`. Step 3 consumes that exact `{avtaleNr}`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `avtaleNr` is unknown.
  - Endpoint group:
    Step 1: `GET /avtaler/avtaleNr/{avtaleNr}`
  - Failure endpoint:
    `GET /avtaler/avtaleNr/{avtaleNr}`
  - Why this fails:
    Repository lookup by `avtaleNr` throws resource-not-found.
  - Intentionally violated constraints:
    Use an `avtaleNr` not obtained from a created agreement.

Endpoint coverage:
- Covers:
  `GET /avtaler/avtaleNr/{avtaleNr}`
- Distinct meaning:
  Agreement lookup by public agreement number.

### Behavior 12: list agreement versions

Behavior name:
list agreement versions

Successful execution:
- Result:
  Returns all stored content versions for an agreement.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}/versjoner`
- Constraints:
  Step 1 produces `{avtaleId}`; Step 2 consumes it. The logged-in party must have access.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is missing or inaccessible.
  - Endpoint group:
    Step 1: `GET /avtaler/{avtaleId}/versjoner`
  - Failure endpoint:
    `GET /avtaler/{avtaleId}/versjoner`
  - Why this fails:
    The implementation first loads the agreement and checks access.
  - Intentionally violated constraints:
    Use an unknown `{avtaleId}` or a role without access.

Endpoint coverage:
- Covers:
  `GET /avtaler/{avtaleId}/versjoner`
- Distinct meaning:
  Agreement history read.

### Behavior 13: update agreement

Behavior name:
update agreement

Successful execution:
- Result:
  Updates mutable agreement content and, for subsidy-backed measures, recalculates subsidy periods.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
- Constraints:
  Step 1 produces `{avtaleId}`. Step 2 returns current `sistEndret`; Step 3 sends `If-Unmodified-Since` not earlier than that value. Body is `EndreAvtale`. Existing approvals must be absent or revoked before editing.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Stale or missing concurrency timestamp.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
  - Failure endpoint:
    `PUT /avtaler/{avtaleId}`
  - Why this fails:
    `Avtale.sjekkSistEndret` rejects a timestamp before the current `sistEndret`.
  - Intentionally violated constraints:
    Send an old `If-Unmodified-Since` value.

Endpoint coverage:
- Covers:
  `PUT /avtaler/{avtaleId}`
- Distinct meaning:
  Persist content updates.

### Behavior 14: dry-run agreement update

Behavior name:
dry-run agreement update

Successful execution:
- Result:
  Validates and returns the would-be updated agreement without saving it.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}/dry-run`
- Constraints:
  Same body and `If-Unmodified-Since` constraints as `PUT /avtaler/{avtaleId}`. The returned object is not persisted.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement has already been approved.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `GET /avtaler/{avtaleId}`
    Step 5: `POST /avtaler/{avtaleId}/godkjenn`
    Step 6: `PUT /avtaler/{avtaleId}/dry-run`
  - Failure endpoint:
    `PUT /avtaler/{avtaleId}/dry-run`
  - Why this fails:
    Agreements with approvals cannot be edited until approvals are revoked.
  - Intentionally violated constraints:
    Attempt dry-run editing after an approval exists.

Endpoint coverage:
- Covers:
  `PUT /avtaler/{avtaleId}/dry-run`
- Distinct meaning:
  Non-persistent validation of agreement edits.

### Behavior 15: share agreement with a party

Behavior name:
share agreement with party

Successful execution:
- Result:
  Registers a share event for the selected agreement party and creates corresponding notifications.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/del-med-avtalepart`
- Constraints:
  Step 4 body is an `Avtalerolle` such as `DELTAKER`, `ARBEIDSGIVER`, or `MENTOR`. The relevant phone number on the current agreement content must be a valid mobile number.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Phone number for the selected party is missing or invalid.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `POST /avtaler/{avtaleId}/del-med-avtalepart`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/del-med-avtalepart`
  - Why this fails:
    `Avtale.delMedAvtalepart` validates the party’s phone number before registering the event.
  - Intentionally violated constraints:
    Omit the content update that supplies a valid phone number.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/del-med-avtalepart`
- Distinct meaning:
  Share agreement with a participant, employer, or mentor.

### Behavior 16: approve agreement as participant

Behavior name:
approve agreement as participant

Successful execution:
- Result:
  Records participant approval on a fully filled agreement.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `GET /avtaler/{avtaleId}`
  Step 5: `POST /avtaler/{avtaleId}/godkjenn`
- Constraints:
  Step 5 uses `innlogget-part=DELTAKER` and `If-Unmodified-Since` from Step 4. The participant must match the agreement’s `deltakerFnr`. All fields required for the measure must be filled.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Required agreement fields are incomplete.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `POST /avtaler/{avtaleId}/godkjenn`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/godkjenn`
  - Why this fails:
    `sjekkOmAltErKlarTilGodkjenning` rejects incomplete agreement content.
  - Intentionally violated constraints:
    Omit `PUT /avtaler/{avtaleId}` with the required fields.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/godkjenn`
- Distinct meaning:
  Participant approval.

### Behavior 17: approve agreement as employer

Behavior name:
approve agreement as employer

Successful execution:
- Result:
  Records employer approval on a fully filled agreement.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `GET /avtaler/{avtaleId}`
  Step 5: `POST /avtaler/{avtaleId}/godkjenn`
- Constraints:
  Step 5 uses `innlogget-part=ARBEIDSGIVER`, and the employer must have access to the agreement’s `bedriftNr` and `tiltakstype`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Employer has already approved.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `GET /avtaler/{avtaleId}`
    Step 5: `POST /avtaler/{avtaleId}/godkjenn`
    Step 6: `GET /avtaler/{avtaleId}`
    Step 7: `POST /avtaler/{avtaleId}/godkjenn`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/godkjenn`
  - Why this fails:
    `godkjennForArbeidsgiver` rejects duplicate employer approval.
  - Intentionally violated constraints:
    Repeat employer approval without revoking approvals.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/godkjenn`
- Distinct meaning:
  Employer approval.

### Behavior 18: sign mentor confidentiality declaration

Behavior name:
sign mentor confidentiality declaration

Successful execution:
- Result:
  Records that the mentor has signed the confidentiality declaration.
- Endpoint sequence:
  Step 1: `POST /avtaler/opprett-mentor-avtale`
  Step 2: `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`
- Constraints:
  Step 1 produces `{avtaleId}`. Step 2 must be called with `innlogget-part=MENTOR`, and `If-Unmodified-Since` must not be older than the agreement’s current `sistEndret`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller is not logged in as mentor.
  - Endpoint group:
    Step 1: `POST /avtaler/opprett-mentor-avtale`
    Step 2: `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`
  - Why this fails:
    The controller explicitly checks `avtalepart.rolle() == MENTOR`.
  - Intentionally violated constraints:
    Use `innlogget-part` other than `MENTOR`.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`
- Distinct meaning:
  Explicit mentor confidentiality signing.
- Covers:
  `POST /avtaler/{avtaleId}/godkjenn`
- Distinct meaning:
  The generic approval endpoint also signs for `innlogget-part=MENTOR`.

### Behavior 19: approve agreement as advisor

Behavior name:
approve agreement as advisor

Successful execution:
- Result:
  Records advisor approval after participant and employer have approved. For measures not requiring decision-maker approval, this also makes the agreement entered.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `GET /avtaler/{avtaleId}`
  Step 5: `POST /avtaler/{avtaleId}/godkjenn`
  Step 6: `GET /avtaler/{avtaleId}`
  Step 7: `POST /avtaler/{avtaleId}/godkjenn`
  Step 8: `GET /avtaler/{avtaleId}`
  Step 9: `POST /avtaler/{avtaleId}/godkjenn`
- Constraints:
  Step 5 is participant approval, Step 7 is employer approval, and Step 9 is advisor approval with `innlogget-part=VEILEDER`. Each approval uses a fresh `If-Unmodified-Since` value.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Participant or employer approval is missing.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `GET /avtaler/{avtaleId}`
    Step 5: `POST /avtaler/{avtaleId}/godkjenn`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/godkjenn`
  - Why this fails:
    Advisor approval must happen last.
  - Intentionally violated constraints:
    Call advisor approval before participant and employer approvals.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/godkjenn`
- Distinct meaning:
  Advisor final approval.

### Behavior 20: approve on behalf of participant

Behavior name:
approve on behalf of participant

Successful execution:
- Result:
  Advisor records both advisor approval and participant approval on behalf of the participant.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `GET /avtaler/{avtaleId}`
  Step 5: `POST /avtaler/{avtaleId}/godkjenn`
  Step 6: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker`
- Constraints:
  Step 5 must be employer approval. Step 6 body must set at least one participant reason field in `GodkjentPaVegneGrunn`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No on-behalf reason is selected.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `GET /avtaler/{avtaleId}`
    Step 5: `POST /avtaler/{avtaleId}/godkjenn`
    Step 6: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker`
  - Why this fails:
    `GodkjentPaVegneGrunn.valgtMinstEnGrunn` rejects an all-false body.
  - Intentionally violated constraints:
    Send all participant reason booleans as false.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av`
- Distinct meaning:
  Alias for participant on-behalf approval.
- Covers:
  `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker`
- Distinct meaning:
  Participant on-behalf approval.

### Behavior 21: approve on behalf of employer

Behavior name:
approve on behalf of employer

Successful execution:
- Result:
  Advisor records both advisor approval and employer approval on behalf of the employer.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `GET /avtaler/{avtaleId}`
  Step 5: `POST /avtaler/{avtaleId}/godkjenn`
  Step 6: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver`
- Constraints:
  Step 5 must be participant approval. The measure must be `SOMMERJOBB`, `MIDLERTIDIG_LONNSTILSKUDD`, or `VARIG_LONNSTILSKUDD`. Step 6 body must set at least one employer reason.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Measure type is not supported for employer on-behalf approval.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `GET /avtaler/{avtaleId}`
    Step 5: `POST /avtaler/{avtaleId}/godkjenn`
    Step 6: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver`
  - Why this fails:
    Implementation rejects measure types outside the three subsidized decision-maker measures.
  - Intentionally violated constraints:
    Use `ARBEIDSTRENING`, `INKLUDERINGSTILSKUDD`, or `MENTOR`.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver`
- Distinct meaning:
  Employer on-behalf approval.

### Behavior 22: approve on behalf of participant and employer

Behavior name:
approve on behalf of participant and employer

Successful execution:
- Result:
  Advisor records advisor, participant, and employer approvals in one operation.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
- Constraints:
  Measure must be `SOMMERJOBB`, `MIDLERTIDIG_LONNSTILSKUDD`, or `VARIG_LONNSTILSKUDD`. Body contains both reason objects, and each must select at least one reason.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Participant or employer has already approved.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `GET /avtaler/{avtaleId}`
    Step 5: `POST /avtaler/{avtaleId}/godkjenn`
    Step 6: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  - Why this fails:
    The combined endpoint only applies when neither participant nor employer has approved yet.
  - Intentionally violated constraints:
    Create a prior participant or employer approval before the combined call.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
- Distinct meaning:
  Combined on-behalf approval.

### Behavior 23: revoke approvals

Behavior name:
revoke approvals

Successful execution:
- Result:
  Clears existing participant, employer, and advisor approvals on an agreement that has not yet been entered.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `GET /avtaler/{avtaleId}`
  Step 5: `POST /avtaler/{avtaleId}/godkjenn`
  Step 6: `POST /avtaler/{avtaleId}/opphev-godkjenninger`
- Constraints:
  Step 5 creates at least one approval. Step 6 uses an `innlogget-part` allowed to revoke; employer cannot revoke after advisor approval, and no one can revoke after the agreement is entered.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No approvals exist.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `POST /avtaler/{avtaleId}/opphev-godkjenninger`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/opphev-godkjenninger`
  - Why this fails:
    The implementation requires at least one approval to revoke.
  - Intentionally violated constraints:
    Omit all approval endpoints.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/opphev-godkjenninger`
- Distinct meaning:
  Revoke agreement approvals.

### Behavior 24: mark agreement eligible for after-registration

Behavior name:
mark agreement eligible for after-registration

Successful execution:
- Result:
  Toggles an unentered agreement so it is approved for after-registration.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`
- Constraints:
  Agreement must exist, be accessible to a decision-maker, and not already be entered. Initial `godkjentForEtterregistrering` must be false.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is already entered.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`
  - Why this fails:
    Entered agreements cannot be marked for after-registration.
  - Intentionally violated constraints:
    Enter the agreement before toggling the flag.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`
- Distinct meaning:
  Toggle from false to true.

### Behavior 25: remove after-registration eligibility

Behavior name:
remove after-registration eligibility

Successful execution:
- Result:
  Toggles an unentered agreement so it is no longer approved for after-registration.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`
  Step 3: `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`
- Constraints:
  Step 2 enables the flag. Step 3 consumes the same `{avtaleId}` and disables the flag. Agreement must still not be entered.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Decision-maker access is missing.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`
  - Why this fails:
    The controller calls `innloggingService.hentBeslutter()`.
  - Intentionally violated constraints:
    Call without a valid decision-maker role/group.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`
- Distinct meaning:
  Toggle from true to false.

### Behavior 26: approve subsidy period

Behavior name:
approve subsidy period

Successful execution:
- Result:
  Decision-maker approves the current subsidy period; for the first decision-maker-controlled period, this also enters the agreement.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`
- Constraints:
  Use a subsidy-backed measure with generated `tilskuddPeriode`. Step 5 body `enhet` must be four digits and must exist in Norg2. Decision-maker cannot be the same NAV ident that approved the agreement.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `enhet` is not four digits.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`
  - Why this fails:
    `Avtale.godkjennTilskuddsperiode` requires `enhet` to match `^\d{4}$`.
  - Intentionally violated constraints:
    Send a non-four-digit `enhet`.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`
- Distinct meaning:
  Decision-maker subsidy period approval.

### Behavior 27: reject subsidy period

Behavior name:
reject subsidy period

Successful execution:
- Result:
  Decision-maker rejects the current subsidy period with rejection causes and explanation.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`
- Constraints:
  Step 5 body must include a non-empty `avslagsårsaker` set and a non-blank `avslagsforklaring`. Current subsidy period must be `UBEHANDLET` and not too early to decide.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Rejection explanation is blank.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`
  - Why this fails:
    `TilskuddPeriode.avslå` requires a non-blank explanation.
  - Intentionally violated constraints:
    Send empty `avslagsforklaring`.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`
- Distinct meaning:
  Decision-maker subsidy period rejection.

### Behavior 28: send rejected subsidy period back

Behavior name:
send rejected subsidy period back

Successful execution:
- Result:
  Deactivates active rejected subsidy periods and creates new unhandled periods for correction.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`
  Step 6: `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter`
- Constraints:
  Step 5 creates the rejected active period that Step 6 processes. Advisor must have access.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is annulled or interrupted.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `POST /avtaler/{avtaleId}/annuller`
    Step 4: `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter`
  - Why this fails:
    `sendTilbakeTilBeslutter` rejects annulled/interrupted agreements.
  - Intentionally violated constraints:
    Annul the agreement before sending it back.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter`
- Distinct meaning:
  Reopen rejected subsidy periods.

### Behavior 29: shorten agreement

Behavior name:
shorten agreement

Successful execution:
- Result:
  Creates a new approved version with an earlier end date and adjusts subsidy periods.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `GET /avtaler/{avtaleId}`
  Step 6: `POST /avtaler/{avtaleId}/forkort`
- Constraints:
  Agreement must be advisor-approved. Step 6 body `sluttDato` must be before current end date, after any paid/sent-claim subsidy period, and `grunn` must be present; if `grunn=Annet`, `annetGrunn` must be present.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    New end date is not before current end date.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `GET /avtaler/{avtaleId}`
    Step 6: `POST /avtaler/{avtaleId}/forkort`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/forkort`
  - Why this fails:
    The implementation rejects shortening to the same or a later end date.
  - Intentionally violated constraints:
    Send `sluttDato` equal to or after the current `sluttDato`.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/forkort`
- Distinct meaning:
  Persist agreement shortening.

### Behavior 30: dry-run agreement shortening

Behavior name:
dry-run agreement shortening

Successful execution:
- Result:
  Returns the would-be shortened agreement without saving it.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `GET /avtaler/{avtaleId}`
  Step 6: `POST /avtaler/{avtaleId}/forkort-dry-run`
- Constraints:
  Same date constraints as shorten. The controller substitutes `"dry run"` as reason and does not persist.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is not advisor-approved.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `POST /avtaler/{avtaleId}/forkort-dry-run`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/forkort-dry-run`
  - Why this fails:
    `forkortAvtale` requires advisor approval.
  - Intentionally violated constraints:
    Omit the approval sequence.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/forkort-dry-run`
- Distinct meaning:
  Non-persistent shortening preview.

### Behavior 31: extend agreement

Behavior name:
extend agreement

Successful execution:
- Result:
  Creates a new approved version with a later end date and adds or recalculates subsidy periods.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `GET /avtaler/{avtaleId}`
  Step 6: `POST /avtaler/{avtaleId}/forleng`
- Constraints:
  Step 6 body `sluttDato` must be after current `sluttDato` and satisfy measure-specific duration rules.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    New end date is not after current end date.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `GET /avtaler/{avtaleId}`
    Step 6: `POST /avtaler/{avtaleId}/forleng`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/forleng`
  - Why this fails:
    The implementation rejects extension unless `sluttDato` increases.
  - Intentionally violated constraints:
    Send a same or earlier `sluttDato`.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/forleng`
- Distinct meaning:
  Persist agreement extension.

### Behavior 32: dry-run agreement extension

Behavior name:
dry-run agreement extension

Successful execution:
- Result:
  Returns the would-be extended agreement without saving it.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `GET /avtaler/{avtaleId}`
  Step 6: `POST /avtaler/{avtaleId}/forleng-dry-run`
- Constraints:
  Same constraints as extension. No persistence.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is not advisor-approved.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `POST /avtaler/{avtaleId}/forleng-dry-run`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/forleng-dry-run`
  - Why this fails:
    Extension is only allowed on advisor-approved agreements.
  - Intentionally violated constraints:
    Omit the approval sequence.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/forleng-dry-run`
- Distinct meaning:
  Non-persistent extension preview.

### Behavior 33: change subsidy calculation

Behavior name:
change subsidy calculation

Successful execution:
- Result:
  Creates a new approved version with changed monthly salary, holiday pay rate, employer tax, and pension rate, then recalculates unhandled subsidy amounts.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `POST /avtaler/{avtaleId}/endre-tilskuddsberegning`
- Constraints:
  Agreement must be advisor-approved and measure must be `MIDLERTIDIG_LONNSTILSKUDD`, `VARIG_LONNSTILSKUDD`, or `SOMMERJOBB`. Body fields `manedslonn`, `feriepengesats`, `arbeidsgiveravgift`, and `otpSats` must be present.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is not approved by advisor.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `POST /avtaler/{avtaleId}/endre-tilskuddsberegning`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/endre-tilskuddsberegning`
  - Why this fails:
    Economy changes require an advisor-approved agreement.
  - Intentionally violated constraints:
    Omit the approval sequence.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/endre-tilskuddsberegning`
- Distinct meaning:
  Persist subsidy calculation change.

### Behavior 34: dry-run subsidy calculation change

Behavior name:
dry-run subsidy calculation change

Successful execution:
- Result:
  Returns the would-be updated agreement after subsidy calculation changes without saving.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run`
- Constraints:
  Same measure, approval, and body constraints as the persistent calculation change. No save.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Required calculation input is missing.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run`
  - Why this fails:
    The implementation requires all calculation inputs.
  - Intentionally violated constraints:
    Omit one of `manedslonn`, `feriepengesats`, `arbeidsgiveravgift`, or `otpSats`.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run`
- Distinct meaning:
  Non-persistent subsidy calculation preview.

### Behavior 35: change contact information

Behavior name:
change contact information

Successful execution:
- Result:
  Creates a new approved version with changed participant, advisor, employer, and refund contact information.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `POST /avtaler/{avtaleId}/endre-kontaktinfo`
- Constraints:
  Agreement must be advisor-approved. Required contact fields must be non-empty.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Required contact field is missing.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `POST /avtaler/{avtaleId}/endre-kontaktinfo`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/endre-kontaktinfo`
  - Why this fails:
    `endreKontaktInformasjon` rejects missing contact fields.
  - Intentionally violated constraints:
    Omit one required name or phone field.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/endre-kontaktinfo`
- Distinct meaning:
  Post-approval contact update.

### Behavior 36: change job description

Behavior name:
change job description

Successful execution:
- Result:
  Creates a new approved version with changed title, work tasks, occupation code, position percent, and days per week.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse`
- Constraints:
  Agreement must be advisor-approved. Body fields required by `EndreStillingsbeskrivelse` must be present.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is not advisor-approved.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse`
  - Why this fails:
    Job description changes are only allowed on approved agreements.
  - Intentionally violated constraints:
    Omit the approval sequence.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse`
- Distinct meaning:
  Post-approval job description update.

### Behavior 37: change follow-up and adaptation text

Behavior name:
change follow-up and adaptation text

Successful execution:
- Result:
  Creates a new approved version with changed follow-up and adaptation descriptions.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging`
- Constraints:
  Agreement must be advisor-approved. `oppfolging` and `tilrettelegging` must both be present.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Follow-up or adaptation text is missing.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging`
  - Why this fails:
    The implementation rejects missing text fields.
  - Intentionally violated constraints:
    Omit `oppfolging` or `tilrettelegging`.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging`
- Distinct meaning:
  Post-approval follow-up/adaptation update.

### Behavior 38: change work-training goals

Behavior name:
change work-training goals

Successful execution:
- Result:
  Replaces goals on an approved work-training agreement.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `POST /avtaler/{avtaleId}/endre-maal`
- Constraints:
  Agreement must have `tiltakstype=ARBEIDSTRENING` and be advisor-approved. Body `maal` must be non-empty, and each goal must have `beskrivelse` and `kategori`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is not work training.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `POST /avtaler/{avtaleId}/endre-maal`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/endre-maal`
  - Why this fails:
    `endreMål` requires `Tiltakstype.ARBEIDSTRENING`.
  - Intentionally violated constraints:
    Create any other `tiltakstype`.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/endre-maal`
- Distinct meaning:
  Post-approval goal replacement.

### Behavior 39: change inclusion subsidy expenses

Behavior name:
change inclusion subsidy expenses

Successful execution:
- Result:
  Replaces inclusion subsidy expense lines on an approved inclusion subsidy agreement.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd`
- Constraints:
  Agreement must have `tiltakstype=INKLUDERINGSTILSKUDD` and be advisor-approved. Expense list must be non-empty; each item must include `beløp` and `type`; total must not exceed the implementation limit.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Total inclusion subsidy amount is too high.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd`
  - Why this fails:
    `endreInkluderingstilskudd` rejects totals above the configured code limit.
  - Intentionally violated constraints:
    Send expense lines whose total exceeds the allowed maximum.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd`
- Distinct meaning:
  Post-approval inclusion subsidy expense update.

### Behavior 40: change mentor details

Behavior name:
change mentor details

Successful execution:
- Result:
  Creates a new approved version with changed mentor name, phone, tasks, hours, and hourly wage.
- Endpoint sequence:
  Step 1: `POST /avtaler/opprett-mentor-avtale`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`
  Step 5: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 6: `POST /avtaler/{avtaleId}/endre-om-mentor`
- Constraints:
  Agreement must have `tiltakstype=MENTOR` and be advisor-approved. Mentor detail fields must be present.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is not a mentor agreement.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `POST /avtaler/{avtaleId}/endre-om-mentor`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/endre-om-mentor`
  - Why this fails:
    `endreOmMentor` requires `Tiltakstype.MENTOR`.
  - Intentionally violated constraints:
    Use a non-mentor agreement.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/endre-om-mentor`
- Distinct meaning:
  Post-approval mentor detail update.

### Behavior 41: change cost center

Behavior name:
change cost center

Successful execution:
- Result:
  Sets the cost-center unit and unit name on unhandled or rejected subsidy periods.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/endre-kostnadssted`
- Constraints:
  The agreement must have generated subsidy periods that are not closed for editing. Body `enhet` must exist in Norg2. Agreement must not be entered.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Norg2 does not return a unit for `enhet`.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/endre-kostnadssted`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/endre-kostnadssted`
  - Why this fails:
    `Veileder.oppdatereKostnadssted` throws `ENHET_FINNES_IKKE`.
  - Intentionally violated constraints:
    Use an unknown unit number.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/endre-kostnadssted`
- Distinct meaning:
  Cost-center update for subsidy periods.

### Behavior 42: adjust Arena migration date

Behavior name:
adjust Arena migration date

Successful execution:
- Result:
  Recomputes subsidy periods around a migration date and saves or updates the agreement’s Arena cleanup marker.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/juster-arena-migreringsdato`
- Constraints:
  Body contains `migreringsdato`. Agreement must not be entered. Generated periods ending before the migration date are marked `BEHANDLET_I_ARENA`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is already entered.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `POST /avtaler/{avtaleId}/juster-arena-migreringsdato`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/juster-arena-migreringsdato`
  - Why this fails:
    The controller rejects migration-date changes on entered agreements.
  - Intentionally violated constraints:
    Enter the agreement before adjusting migration date.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/juster-arena-migreringsdato`
- Distinct meaning:
  Persistent Arena migration-date adjustment.

### Behavior 43: dry-run Arena migration date adjustment

Behavior name:
dry-run Arena migration date adjustment

Successful execution:
- Result:
  Returns the would-be agreement after recalculating periods around the migration date without saving it.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run`
- Constraints:
  Body contains `migreringsdato`. Advisor must have access. Unlike the persistent endpoint, the implementation does not explicitly reject entered agreements before recalculation.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is missing.
  - Endpoint group:
    Step 1: `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run`
  - Why this fails:
    Repository lookup by `{avtaleId}` throws resource-not-found.
  - Intentionally violated constraints:
    Omit the agreement creation endpoint.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run`
- Distinct meaning:
  Non-persistent Arena migration-date preview.

### Behavior 44: get employer account number

Behavior name:
get employer account number

Successful execution:
- Result:
  Returns the employer’s bank account number for the agreement’s company.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver`
- Constraints:
  Step 1 produces `{avtaleId}`. Step 2 uses the agreement’s `bedriftNr` when calling the account register.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Account register has no company account.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver`
  - Failure endpoint:
    `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver`
  - Why this fails:
    The real account-register client maps 404 to `KONTOREGISTER_FEIL_BEDRIFT_IKKE_FUNNET`.
  - Intentionally violated constraints:
    Use an agreement whose company is not found in the account register.

Endpoint coverage:
- Covers:
  `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver`
- Distinct meaning:
  Employer account lookup through agreement company.

### Behavior 45: download agreement PDF

Behavior name:
download agreement PDF

Successful execution:
- Result:
  Returns a PDF representation of an advisor-approved agreement.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
  Step 5: `GET /avtaler/{avtaleId}/pdf`
- Constraints:
  Agreement must be approved by advisor before Step 5. Response is `application/pdf`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is not approved by advisor.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}/pdf`
  - Failure endpoint:
    `GET /avtaler/{avtaleId}/pdf`
  - Why this fails:
    The controller throws `KAN_IKKE_LASTE_NED_PDF`.
  - Intentionally violated constraints:
    Omit advisor approval.

Endpoint coverage:
- Covers:
  `GET /avtaler/{avtaleId}/pdf`
- Distinct meaning:
  PDF generation/download.

### Behavior 46: check whether Salesforce dialog should be shown

Behavior name:
check Salesforce dialog visibility

Successful execution:
- Result:
  Returns `true` when the agreement belongs to a configured Salesforce office, has `MIDLERTIDIG_LONNSTILSKUDD`, and is `GJENNOMFØRES` or `AVSLUTTET`; otherwise returns `false`.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}/vis-salesforce-dialog`
- Constraints:
  Step 2 consumes `{avtaleId}` from Step 1. The current agreement state and configured office list determine the boolean.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is missing or inaccessible.
  - Endpoint group:
    Step 1: `GET /avtaler/{avtaleId}/vis-salesforce-dialog`
  - Failure endpoint:
    `GET /avtaler/{avtaleId}/vis-salesforce-dialog`
  - Why this fails:
    The controller loads the agreement through access-checked `hentAvtale`.
  - Intentionally violated constraints:
    Use an unknown `{avtaleId}` or unauthorized role.

Endpoint coverage:
- Covers:
  `GET /avtaler/{avtaleId}/vis-salesforce-dialog`
- Distinct meaning:
  UI eligibility check for Salesforce dialog.

### Behavior 47: refresh follow-up unit

Behavior name:
refresh follow-up unit

Successful execution:
- Result:
  Refreshes participant data, follow-up status, geographic unit, and follow-up unit name, then saves the agreement.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet`
- Constraints:
  Step 2 consumes `{avtaleId}` and must be called by an advisor with access.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Participant has protected address code 6 when person data is refreshed.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet`
  - Why this fails:
    Person-data refresh uses the same code-6 guard as agreement creation/update.
  - Intentionally violated constraints:
    Use an agreement participant with code 6 protection.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet`
- Distinct meaning:
  Refresh external follow-up/unit information.

### Behavior 48: take over agreement as advisor

Behavior name:
take over agreement as advisor

Successful execution:
- Result:
  Assigns the logged-in advisor as the agreement’s advisor and refreshes follow-up data.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `PUT /avtaler/{avtaleId}/overta`
- Constraints:
  Step 2 consumes `{avtaleId}`. The logged-in advisor must have access and must not already be the agreement’s advisor.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Logged-in advisor is already the agreement advisor.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `PUT /avtaler/{avtaleId}/overta`
  - Failure endpoint:
    `PUT /avtaler/{avtaleId}/overta`
  - Why this fails:
    `Veileder.overtaAvtale` throws `ER_ALLEREDE_VEILEDER`.
  - Intentionally violated constraints:
    Use the same advisor who created or already owns the agreement.

Endpoint coverage:
- Covers:
  `PUT /avtaler/{avtaleId}/overta`
- Distinct meaning:
  Advisor reassignment/takeover.

### Behavior 49: annul agreement

Behavior name:
annul agreement

Successful execution:
- Result:
  Annuls an agreement and annuls/removes eligible subsidy periods; if reason is `Feilregistrering`, the agreement is also marked as incorrectly registered.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `POST /avtaler/{avtaleId}/annuller`
- Constraints:
  Step 3 body contains `annullertGrunn`. `If-Unmodified-Since` must not be stale. Agreement must not contain paid or refund-approved subsidy periods.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement contains a paid subsidy period.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /avtaler/{avtaleId}`
    Step 3: `PUT /avtaler/{avtaleId}`
    Step 4: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`
    Step 5: `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`
    Step 6: `POST /avtaler/{avtaleId}/annuller`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/annuller`
  - Why this fails:
    `sjekkAtIkkeAvtalenInneholderUtbetaltTilskuddsperiode` blocks annulment when a period is paid or refund-approved.
  - Intentionally violated constraints:
    Annul after period/refund state blocks annulment.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/annuller`
- Distinct meaning:
  Agreement annulment.

### Behavior 50: soft-delete agreement

Behavior name:
soft-delete agreement

Successful execution:
- Result:
  Marks the agreement as deleted/hidden.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `POST /avtaler/{avtaleId}/slettemerk`
- Constraints:
  Logged-in advisor must have access and must be configured in `SlettemerkeProperties.ident`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Advisor is not configured as a delete-marker admin.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `POST /avtaler/{avtaleId}/slettemerk`
  - Failure endpoint:
    `POST /avtaler/{avtaleId}/slettemerk`
  - Why this fails:
    `Veileder.slettemerk` checks configured allowed NAV idents.
  - Intentionally violated constraints:
    Call as a valid advisor not in the configured admin ident list.

Endpoint coverage:
- Covers:
  `POST /avtaler/{avtaleId}/slettemerk`
- Distinct meaning:
  Soft deletion / hidden agreement.

### Behavior 51: list employer agreements for Min Side Arbeidsgiver

Behavior name:
list employer agreements

Successful execution:
- Result:
  Returns all agreements for a company that the logged-in employer can view.
- Endpoint sequence:
  Step 1: `GET /avtaler/min-side-arbeidsgiver`
- Constraints:
  Query `bedriftNr` is required. Employer must have Altinn access to the company/measure; old annulled, interrupted, or ended agreements may be filtered out.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller is not an employer token/role.
  - Endpoint group:
    Step 1: `GET /avtaler/min-side-arbeidsgiver`
  - Failure endpoint:
    `GET /avtaler/min-side-arbeidsgiver`
  - Why this fails:
    The controller calls `innloggingService.hentArbeidsgiver()`.
  - Intentionally violated constraints:
    Call with non-employer token/role.

Endpoint coverage:
- Covers:
  `GET /avtaler/min-side-arbeidsgiver`
- Distinct meaning:
  Employer company agreement list.

### Behavior 52: list decision-maker agreements

Behavior name:
list decision-maker agreements

Successful execution:
- Result:
  Returns paginated agreements whose subsidy periods match decision-maker filters and the decision-maker’s NAV units.
- Endpoint sequence:
  Step 1: `GET /avtaler/beslutter-liste`
- Constraints:
  Caller must be a decision-maker. If `tilskuddPeriodeStatus` is absent, implementation defaults to `UBEHANDLET`. If `tiltakstype` is absent, it searches `SOMMERJOBB`, `VARIG_LONNSTILSKUDD`, and `MIDLERTIDIG_LONNSTILSKUDD`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Decision-maker has no NAV units.
  - Endpoint group:
    Step 1: `GET /avtaler/beslutter-liste`
  - Failure endpoint:
    `GET /avtaler/beslutter-liste`
  - Why this fails:
    `Beslutter.finnGodkjente...` throws `NAV_ENHET_IKKE_FUNNET`.
  - Intentionally violated constraints:
    Use a decision-maker identity with no AXsys units.

Endpoint coverage:
- Covers:
  `GET /avtaler/beslutter-liste`
- Distinct meaning:
  Decision-maker work queue/list.

### Behavior 53: get logged-in user

Behavior name:
get logged-in user

Successful execution:
- Result:
  Returns role-specific information for the logged-in user.
- Endpoint sequence:
  Step 1: `GET /innlogget-bruker`
- Constraints:
  Although OpenAPI marks `innlogget-part` as optional, implementation requires it and throws if absent. The role must match the token issuer.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `innlogget-part` cookie is absent.
  - Endpoint group:
    Step 1: `GET /innlogget-bruker`
  - Failure endpoint:
    `GET /innlogget-bruker`
  - Why this fails:
    The implementation calls `orElseThrow(IkkeValgtPartException::new)`.
  - Intentionally violated constraints:
    Omit `innlogget-part`.

Endpoint coverage:
- Covers:
  `GET /innlogget-bruker`
- Distinct meaning:
  Current authenticated user lookup.

### Behavior 54: look up organization

Behavior name:
look up organization

Successful execution:
- Result:
  Returns organization data for an employer unit.
- Endpoint sequence:
  Step 1: `GET /organisasjoner`
- Constraints:
  Query `bedriftNr` is required. Ereg result must not be a juridical unit or organization-led unit.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Ereg does not find the unit.
  - Endpoint group:
    Step 1: `GET /organisasjoner`
  - Failure endpoint:
    `GET /organisasjoner`
  - Why this fails:
    `EregService` maps Ereg client errors to `ENHET_FINNES_IKKE`.
  - Intentionally violated constraints:
    Use an unknown `bedriftNr`.

Endpoint coverage:
- Covers:
  `GET /organisasjoner`
- Distinct meaning:
  Employer-unit lookup.

### Behavior 55: get Altinn rights request URLs

Behavior name:
get Altinn rights request URLs

Successful execution:
- Result:
  Returns URLs that let an employer request Altinn rights for each supported measure type.
- Endpoint sequence:
  Step 1: `GET /be-om-altinn-rettighet-urler`
- Constraints:
  Query `orgNr` is appended as `bedrift` to the configured base URL. The response maps supported `Tiltakstype` values to URLs.

Failure or exceptional branches:
- None identified in implementation beyond missing query parameter binding.

Endpoint coverage:
- Covers:
  `GET /be-om-altinn-rettighet-urler`
- Distinct meaning:
  Build Altinn permission-request URLs.

### Behavior 56: get all code lists

Behavior name:
get all code lists

Successful execution:
- Result:
  Returns both measure types and agreement statuses.
- Endpoint sequence:
  Step 1: `GET /kodeverk`
- Constraints:
  No resource state required.

Failure or exceptional branches:
- None identified in implementation.

Endpoint coverage:
- Covers:
  `GET /kodeverk`
- Distinct meaning:
  Combined code-list discovery.

### Behavior 57: get status code list

Behavior name:
get status code list

Successful execution:
- Result:
  Returns all `Status` enum names and descriptions.
- Endpoint sequence:
  Step 1: `GET /kodeverk/statuser`
- Constraints:
  No resource state required.

Failure or exceptional branches:
- None identified in implementation.

Endpoint coverage:
- Covers:
  `GET /kodeverk/statuser`
- Distinct meaning:
  Status code-list discovery.

### Behavior 58: get measure type code list

Behavior name:
get measure type code list

Successful execution:
- Result:
  Returns all `Tiltakstype` enum names, descriptions, treatment themes, and optional Arena codes.
- Endpoint sequence:
  Step 1: `GET /kodeverk/tiltakstyper`
- Constraints:
  No resource state required.

Failure or exceptional branches:
- None identified in implementation.

Endpoint coverage:
- Covers:
  `GET /kodeverk/tiltakstyper`
- Distinct meaning:
  Measure type code-list discovery.

### Behavior 59: evaluate feature toggles

Behavior name:
evaluate feature toggles

Successful execution:
- Result:
  Returns enabled/disabled values for requested feature names.
- Endpoint sequence:
  Step 1: `GET /feature`
- Constraints:
  Query `feature` is a list. Each feature name becomes a key in the returned map.

Failure or exceptional branches:
- None identified in implementation beyond missing query parameter binding.

Endpoint coverage:
- Covers:
  `GET /feature`
- Distinct meaning:
  Boolean feature-toggle evaluation.

### Behavior 60: get feature variants

Behavior name:
get feature variants

Successful execution:
- Result:
  Returns Unleash variant objects for requested feature names.
- Endpoint sequence:
  Step 1: `GET /feature/variant`
- Constraints:
  Query `feature` is a list. Logged-in user id is included in the Unleash context when available.

Failure or exceptional branches:
- None identified in implementation beyond missing query parameter binding.

Endpoint coverage:
- Covers:
  `GET /feature/variant`
- Distinct meaning:
  Feature variant lookup.

### Behavior 61: health check

Behavior name:
run health check

Successful execution:
- Result:
  Returns `ok` if the database query succeeds.
- Endpoint sequence:
  Step 1: `GET /internal/healthcheck`
- Constraints:
  No specific resource state required; database must answer `select 'ok'`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Database query fails.
  - Endpoint group:
    Step 1: `GET /internal/healthcheck`
  - Failure endpoint:
    `GET /internal/healthcheck`
  - Why this fails:
    The controller directly returns the JDBC query result.
  - Intentionally violated constraints:
    Database unavailable or query cannot execute.

Endpoint coverage:
- Covers:
  `GET /internal/healthcheck`
- Distinct meaning:
  Internal health probe.

### Behavior 62: list overview notifications

Behavior name:
list overview notifications

Successful execution:
- Result:
  Returns unread bell notifications for the logged-in party’s identifiers.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /varsler/oversikt`
- Constraints:
  Agreement creation and other agreement events create notifications through event listeners. Step 2 returns only `lest=false`, `bjelle=true`, and matching identifiers.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Unsupported role/token combination.
  - Endpoint group:
    Step 1: `GET /varsler/oversikt`
  - Failure endpoint:
    `GET /varsler/oversikt`
  - Why this fails:
    Notification lookup is scoped through `hentAvtalepart`.
  - Intentionally violated constraints:
    Use invalid `innlogget-part` for the token.

Endpoint coverage:
- Covers:
  `GET /varsler/oversikt`
- Distinct meaning:
  Unread bell notification list.

### Behavior 63: list agreement modal notifications

Behavior name:
list agreement modal notifications

Successful execution:
- Result:
  Returns unread bell notifications for a specific agreement and logged-in party.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /varsler/avtale-modal`
- Constraints:
  Step 1 produces `{avtaleId}`; Step 2 uses query `avtaleId={avtaleId}` and filters by the logged-in party identifiers.

Failure or exceptional branches:
- None identified in implementation for unknown agreement id; it returns an empty list if no notifications match.

Endpoint coverage:
- Covers:
  `GET /varsler/avtale-modal`
- Distinct meaning:
  Agreement-scoped unread bell notification list.

### Behavior 64: list agreement notification log

Behavior name:
list agreement notification log

Successful execution:
- Result:
  Returns all notifications for a specific agreement and receiver role.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /varsler/avtale-logg`
- Constraints:
  Step 2 uses `avtaleId` from Step 1 and checks the logged-in party’s access to the agreement before returning log entries.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement id is unknown.
  - Endpoint group:
    Step 1: `GET /varsler/avtale-logg`
  - Failure endpoint:
    `GET /varsler/avtale-logg`
  - Why this fails:
    The controller calls `avtaleRepository.findById(avtaleId).orElseThrow()`.
  - Intentionally violated constraints:
    Omit agreement creation and use an unknown `avtaleId`.

Endpoint coverage:
- Covers:
  `GET /varsler/avtale-logg`
- Distinct meaning:
  Full agreement notification log.

### Behavior 65: mark notification as read

Behavior name:
mark notification as read

Successful execution:
- Result:
  Marks one notification as read for the logged-in party.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /varsler/oversikt`
  Step 3: `POST /varsler/{varselId}/sett-til-lest`
- Constraints:
  Step 2 returns a notification id. Step 3 consumes that exact `{varselId}`. Notification must belong to one of the logged-in party’s identifiers.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Notification does not belong to logged-in party.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /varsler/oversikt`
    Step 3: `POST /varsler/{varselId}/sett-til-lest`
  - Failure endpoint:
    `POST /varsler/{varselId}/sett-til-lest`
  - Why this fails:
    Repository lookup by id and identifiers returns null, then `varsel.settTilLest()` fails.
  - Intentionally violated constraints:
    Reuse a `{varselId}` from another party’s notification list.

Endpoint coverage:
- Covers:
  `POST /varsler/{varselId}/sett-til-lest`
- Distinct meaning:
  Single notification read marking.

### Behavior 66: mark multiple notifications as read

Behavior name:
mark multiple notifications as read

Successful execution:
- Result:
  Marks each notification id in the request body as read.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /varsler/oversikt`
  Step 3: `POST /varsler/sett-alle-til-lest`
- Constraints:
  Step 2 produces notification ids. Step 3 body is an array of those ids and internally calls the single-notification read behavior for each.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Any body id is not readable by the logged-in party.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `GET /varsler/oversikt`
    Step 3: `POST /varsler/sett-alle-til-lest`
  - Failure endpoint:
    `POST /varsler/sett-alle-til-lest`
  - Why this fails:
    The loop delegates to `settTilLest`; one invalid id causes the same failure as the single-read endpoint.
  - Intentionally violated constraints:
    Include at least one foreign or unknown notification id.

Endpoint coverage:
- Covers:
  `POST /varsler/sett-alle-til-lest`
- Distinct meaning:
  Bulk notification read marking.

### Behavior 67: list unjournaled agreements

Behavior name:
list unjournaled agreements

Successful execution:
- Result:
  Returns agreement versions that need journalføring, including subsidy periods.
- Endpoint sequence:
  Step 1: `GET /internal/avtaler`
- Constraints:
  Must be called by the configured system user. No specific agreement needs to be created for the endpoint to succeed, though it may return an empty list.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller is not the configured system user.
  - Endpoint group:
    Step 1: `GET /internal/avtaler`
  - Failure endpoint:
    `GET /internal/avtaler`
  - Why this fails:
    `innloggingService.validerSystembruker` enforces system issuer and configured id.
  - Intentionally violated constraints:
    Use a non-system token.

Endpoint coverage:
- Covers:
  `GET /internal/avtaler`
- Distinct meaning:
  Internal journal export queue.

### Behavior 68: mark agreement versions as journaled

Behavior name:
mark agreement versions as journaled

Successful execution:
- Result:
  Stores journal post ids on agreement content versions.
- Endpoint sequence:
  Step 1: `GET /internal/avtaler`
  Step 2: `PUT /internal/avtaler`
- Constraints:
  Step 1 returns agreement version ids. Step 2 body is a map from those UUIDs to journal post id strings. OpenAPI only says request body is `object`; implementation specifically expects `Map<UUID, String>`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller is not the configured system user.
  - Endpoint group:
    Step 1: `PUT /internal/avtaler`
  - Failure endpoint:
    `PUT /internal/avtaler`
  - Why this fails:
    The implementation validates the system user before updating.
  - Intentionally violated constraints:
    Use a non-system token.

Endpoint coverage:
- Covers:
  `PUT /internal/avtaler`
- Distinct meaning:
  Internal journal completion update.

### Behavior 69: recalculate wage subsidy for selected agreements

Behavior name:
recalculate wage subsidy

Successful execution:
- Result:
  Recalculates missing wage subsidy totals for each selected agreement.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `POST /utvikler-admin/reberegn`
- Constraints:
  Step 1 produces `{avtaleId}`. Step 2 body is an array containing `{avtaleId}`. Caller must have developer-admin group access. Agreement must be a subsidy-backed measure and satisfy recalculation preconditions.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller lacks developer-admin access.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/reberegn`
  - Failure endpoint:
    `POST /utvikler-admin/reberegn`
  - Why this fails:
    `AdminController.sjekkTilgang` throws forbidden.
  - Intentionally violated constraints:
    Call without the configured developer group.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/reberegn`
- Distinct meaning:
  Admin recalculation for selected agreements.

### Behavior 70: fix missing reduced-percent date in batch

Behavior name:
fix missing reduced-percent date

Successful execution:
- Result:
  Finds entered permanent wage-subsidy agreements missing reduced-percent data and recalculates reduced values and migrated periods.
- Endpoint sequence:
  Step 1: `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}`
- Constraints:
  `{migreringsDato}` must parse as `yyyy-MM-dd`. Caller must have developer-admin access. The endpoint scans repository state; no specific resource id is required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller lacks developer-admin access.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}`
  - Failure endpoint:
    `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}`
  - Why this fails:
    Admin group check is enforced before the batch job.
  - Intentionally violated constraints:
    Call without developer-admin group membership.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}`
- Distinct meaning:
  Persistent batch repair.

### Behavior 71: dry-run missing reduced-percent date fix

Behavior name:
dry-run missing reduced-percent date fix

Successful execution:
- Result:
  Counts/logs permanent wage-subsidy agreements that would be repaired, without saving changes.
- Endpoint sequence:
  Step 1: `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}`
- Constraints:
  `{migreringsDato}` must parse as `yyyy-MM-dd`. Caller must have developer-admin access.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller lacks developer-admin access.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}`
  - Failure endpoint:
    `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}`
  - Why this fails:
    Admin group check is enforced.
  - Intentionally violated constraints:
    Call without developer-admin group membership.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}`
- Distinct meaning:
  Non-persistent batch repair preview.

### Behavior 72: generate subsidy periods for one agreement

Behavior name:
generate subsidy periods for agreement

Successful execution:
- Result:
  Generates subsidy periods for one agreement after an Arena migration date.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}`
- Constraints:
  Step 1 produces `{avtaleId}`. Step 4 consumes it and a `yyyy-MM-dd` migration date. Agreement must have sufficient salary/date fields for period generation.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement id is unknown.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}`
  - Failure endpoint:
    `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}`
  - Why this fails:
    Repository lookup throws resource-not-found.
  - Intentionally violated constraints:
    Omit agreement creation and use an unknown `{avtaleId}`.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}`
- Distinct meaning:
  Admin period generation for one agreement.

### Behavior 73: recalculate unhandled subsidy periods

Behavior name:
recalculate unhandled subsidy periods

Successful execution:
- Result:
  Removes unhandled periods and recreates them from the first unhandled point through agreement end.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}`
- Constraints:
  Agreement must be subsidy-backed and have period state. Caller must have developer-admin access.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement type is not subsidy-backed.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}`
  - Failure endpoint:
    `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}`
  - Why this fails:
    `krevEnAvTiltakstyper` rejects unsupported measures.
  - Intentionally violated constraints:
    Use `ARBEIDSTRENING`, `INKLUDERINGSTILSKUDD`, or `MENTOR`.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}`
- Distinct meaning:
  Admin recalculation of unhandled periods.

### Behavior 74: find subsidy period date-order problems

Behavior name:
find subsidy period date-order problems

Successful execution:
- Result:
  Scans temporary wage-subsidy agreements and logs periods whose start date is earlier than the previous sequence number’s start date.
- Endpoint sequence:
  Step 1: `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer`
- Constraints:
  Caller must have developer-admin access. The endpoint is diagnostic and returns no domain object.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller lacks developer-admin access.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer`
  - Failure endpoint:
    `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer`
  - Why this fails:
    Admin group check is enforced.
  - Intentionally violated constraints:
    Call without developer-admin group membership.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer`
- Distinct meaning:
  Admin diagnostic scan.

### Behavior 75: annul subsidy period

Behavior name:
annul subsidy period

Successful execution:
- Result:
  Marks a subsidy period as annulled and emits an annulment event unless its refund status is expired.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `GET /avtaler/{avtaleId}`
  Step 5: `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}`
- Constraints:
  Step 4 returns `tilskuddPeriode[].id`; Step 5 consumes that `{tilskuddsperiodeId}`. Caller must have developer-admin access.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Subsidy period id is unknown.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}`
  - Failure endpoint:
    `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}`
  - Why this fails:
    `tilskuddPeriodeRepository.findById` throws resource-not-found.
  - Intentionally violated constraints:
    Use a `{tilskuddsperiodeId}` not obtained from an agreement response.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}`
- Distinct meaning:
  Admin subsidy period annulment.

### Behavior 76: annul and resend subsidy period as approved

Behavior name:
annul and resend approved subsidy period

Successful execution:
- Result:
  Annuls an existing subsidy period and creates a replacement period with approved status, reusing approval metadata.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `GET /avtaler/{avtaleId}`
  Step 5: `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}`
- Constraints:
  Step 4 returns `{tilskuddsperiodeId}`. Step 5 consumes it. Caller must have developer-admin access.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Subsidy period id is unknown.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}`
  - Failure endpoint:
    `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}`
  - Why this fails:
    Repository lookup fails before annulment/replacement.
  - Intentionally violated constraints:
    Use an unknown `{tilskuddsperiodeId}`.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}`
- Distinct meaning:
  Admin annul-and-resend-approved workflow.

### Behavior 77: annul and generate unhandled subsidy period

Behavior name:
annul and generate unhandled subsidy period

Successful execution:
- Result:
  Annuls an existing subsidy period and creates a replacement with unhandled status.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `GET /avtaler/{avtaleId}`
  Step 5: `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}`
- Constraints:
  Step 4 returns `{tilskuddsperiodeId}`. Step 5 consumes it. Caller must have developer-admin access.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement measure is not subsidy-backed.
  - Endpoint group:
    Step 1: `POST /avtaler`
    Step 2: `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}`
  - Failure endpoint:
    `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}`
  - Why this fails:
    Replacement generation requires one of the subsidy-backed measure types.
  - Intentionally violated constraints:
    Use a subsidy period associated with an unsupported measure type, if such invalid state exists.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}`
- Distinct meaning:
  Admin annul-and-regenerate-unhandled workflow.

### Behavior 78: annul and generate Arena-treated periods before date

Behavior name:
annul and generate Arena-treated periods

Successful execution:
- Result:
  Annuls all subsidy periods on an agreement ending before `{dato}` and creates replacements with `BEHANDLET_I_ARENA` status.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `GET /avtaler/{avtaleId}`
  Step 3: `PUT /avtaler/{avtaleId}`
  Step 4: `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}`
- Constraints:
  Step 1 produces `{avtaleId}`. `{dato}` must parse as `yyyy-MM-dd`. Caller must have developer-admin access.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement id is unknown.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}`
  - Failure endpoint:
    `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}`
  - Why this fails:
    Repository lookup by `{avtaleId}` throws resource-not-found.
  - Intentionally violated constraints:
    Use an unknown `{avtaleId}`.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}`
- Distinct meaning:
  Admin conversion of earlier periods to Arena-treated state.

### Behavior 79: patch selected agreements to data warehouse

Behavior name:
patch selected data warehouse messages

Successful execution:
- Result:
  Creates DVH patch message entities for selected agreement ids found in the repository.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `POST /utvikler-admin/dvh-melding/patch`
- Constraints:
  Step 1 produces `{avtaleId}`. Step 2 body is `{"avtaleIder":[ "{avtaleId}" ]}`. Caller must have the configured DVH patch group. Unknown ids are ignored by `findAllById`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller lacks DVH patch group access.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/dvh-melding/patch`
  - Failure endpoint:
    `POST /utvikler-admin/dvh-melding/patch`
  - Why this fails:
    `InternalDvhMeldingProdusentController.sjekkTilgang` throws forbidden.
  - Intentionally violated constraints:
    Call without the configured group.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/dvh-melding/patch`
- Distinct meaning:
  Admin DVH patch for selected agreements.

### Behavior 80: patch all agreements to data warehouse

Behavior name:
patch all data warehouse messages

Successful execution:
- Result:
  Creates DVH patch messages for all agreements.
- Endpoint sequence:
  Step 1: `POST /utvikler-admin/dvh-melding/patchalleavtaler`
- Constraints:
  Caller must have the configured DVH patch group. No specific agreement setup endpoint is required for the endpoint to run.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller lacks DVH patch group access.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/dvh-melding/patchalleavtaler`
  - Failure endpoint:
    `POST /utvikler-admin/dvh-melding/patchalleavtaler`
  - Why this fails:
    The controller enforces group access.
  - Intentionally violated constraints:
    Call without the configured group.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/dvh-melding/patchalleavtaler`
- Distinct meaning:
  Admin DVH patch for all agreements.

### Behavior 81: send agreement event message for one agreement

Behavior name:
send event message for one agreement

Successful execution:
- Result:
  Sends an agreement event message for one existing agreement.
- Endpoint sequence:
  Step 1: `POST /avtaler`
  Step 2: `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}`
- Constraints:
  Step 1 produces `{avtaleId}`. Step 2 consumes it. Implementation loads the agreement and sends through `AvtaleHendelseService`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement id is unknown.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}`
  - Failure endpoint:
    `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}`
  - Why this fails:
    Repository lookup throws resource-not-found.
  - Intentionally violated constraints:
    Omit agreement creation and use an unknown `{avtaleId}`.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}`
- Distinct meaning:
  Admin-triggered event publication for one agreement. Implementation does not call the same developer group check used by the all-agreements endpoints.

### Behavior 82: send agreement event messages for all agreements

Behavior name:
send event messages for all agreements

Successful execution:
- Result:
  Sends agreement event messages for all agreements.
- Endpoint sequence:
  Step 1: `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler`
- Constraints:
  Caller must have developer-admin group access.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller lacks developer-admin access.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler`
  - Failure endpoint:
    `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler`
  - Why this fails:
    `AvtaleHendelseController.sjekkTilgang` throws forbidden.
  - Intentionally violated constraints:
    Call without developer-admin group membership.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler`
- Distinct meaning:
  Persistent event publication for all agreements.

### Behavior 83: dry-run event messages for all agreements

Behavior name:
dry-run event messages for all agreements

Successful execution:
- Result:
  Performs the all-agreement event-message operation in dry-run mode.
- Endpoint sequence:
  Step 1: `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler`
- Constraints:
  Caller must have developer-admin group access. Implementation delegates to the dry-run service method.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller lacks developer-admin access.
  - Endpoint group:
    Step 1: `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler`
  - Failure endpoint:
    `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler`
  - Why this fails:
    Developer group access is checked before the service call.
  - Intentionally violated constraints:
    Call without developer-admin group membership.

Endpoint coverage:
- Covers:
  `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler`
- Distinct meaning:
  Non-persistent event publication preview.

Unclear or auxiliary endpoints:
None.