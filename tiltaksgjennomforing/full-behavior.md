Analyzed only `src` and `tiltaksgjennomforing.json`. I found 79 documented OpenAPI operations and kept endpoint-level distinctions where one operation has multiple implementation-backed meanings. I did not execute the API.

### Function 1: list accessible agreements

Function name:
list accessible agreements

Core endpoint(s):
- `GET /avtaler`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns a paginated list of agreements visible to the logged-in party, filtered by the query fields.
- Invocation:
  Step 1: `GET /avtaler` with required path/query/body/form/header values
- Constraints:
  `innlogget-part` selects the role. Query fields from `AvtalePredicate` constrain the repository search. `page` and `size` are normalized with `Math.abs`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: the role cookie and token issuer are not a supported combination. This can be produced by direct database setup, or through this violating request pattern: Use an unsupported `innlogget-part` for the authenticated token.
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

### Function 2: create advisor agreement

Function name:
create advisor agreement

Core endpoint(s):
- `POST /avtaler`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Creates a new agreement for a participant and employer as a NAV advisor and returns a `Location` pointing to `/avtaler/{avtaleId}`.
- Invocation:
  Step 1: `POST /avtaler` with required path/query/body/form/header values
- Constraints:
  Body must contain `deltakerFnr`, `bedriftNr`, and `tiltakstype`. The logged-in advisor must have write access to the participant. The participant cannot be under 16; `SOMMERJOBB` also rejects participants over the age limit.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: advisor lacks write access to the participant. This can be produced by direct database setup, or through this violating request pattern: Use a `deltakerFnr` the advisor cannot access.
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

### Function 3: create Arena cleanup agreement

Function name:
create Arena cleanup agreement

Core endpoint(s):
- `POST /avtaler`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Creates a new advisor agreement and also marks it as an Arena cleanup agreement.
- Invocation:
  Step 1: `POST /avtaler` with required path/query/body/form/header values
- Constraints:
  Same creation constraints as Function 2. Query parameter `ryddeavtale=true` must be supplied; the created `{avtaleId}` is reused internally to save an `ArenaRyddeAvtale`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: agreement creation itself is invalid. This can be produced by direct database setup, or through this violating request pattern: Omit required body fields or use a participant the advisor cannot access.
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

### Function 4: create employer agreement

Function name:
create employer agreement

Core endpoint(s):
- `POST /avtaler/opprett-som-arbeidsgiver`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Creates a new agreement as an employer and returns a `Location` for `/avtaler/{avtaleId}`.
- Invocation:
  Step 1: `POST /avtaler/opprett-som-arbeidsgiver` with required path/query/body/form/header values
- Constraints:
  Body must contain `deltakerFnr`, `bedriftNr`, and `tiltakstype`. The employer must have Altinn access for the selected company and measure type.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: employer lacks Altinn access for `bedriftNr` and `tiltakstype`. This can be produced by direct database setup, or through this violating request pattern: Use a company or measure not present in the employer’s Altinn rights.
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

### Function 5: create mentor agreement as advisor

Function name:
create mentor agreement as advisor

Core endpoint(s):
- `POST /avtaler/opprett-mentor-avtale`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Creates a mentor agreement where the advisor is the creator.
- Invocation:
  Step 1: `POST /avtaler/opprett-mentor-avtale` with required path/query/body/form/header values
- Constraints:
  Body must include `deltakerFnr`, `mentorFnr`, `bedriftNr`, `tiltakstype`, and `avtalerolle=VEILEDER`. For the intended mentor workflow, `tiltakstype` should be `MENTOR`. `deltakerFnr` and `mentorFnr` must differ.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: participant and mentor have the same national identity number. This can be produced by direct database setup, or through this violating request pattern: Set `deltakerFnr` equal to `mentorFnr`.
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

### Function 6: create mentor agreement as employer

Function name:
create mentor agreement as employer

Core endpoint(s):
- `POST /avtaler/opprett-mentor-avtale`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Creates a mentor agreement where the employer is the creator.
- Invocation:
  Step 1: `POST /avtaler/opprett-mentor-avtale` with required path/query/body/form/header values
- Constraints:
  Body must include `avtalerolle=ARBEIDSGIVER`. The employer must have Altinn access for `bedriftNr` and `tiltakstype`. `deltakerFnr` and `mentorFnr` must differ.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: `avtalerolle` is neither `VEILEDER` nor `ARBEIDSGIVER`. This can be produced by direct database setup, or through this violating request pattern: Use `avtalerolle=DELTAKER`, `MENTOR`, or `BESLUTTER`.
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

### Function 7: check participant overlap

Function name:
check participant overlap

Core endpoint(s):
- `GET /avtaler/deltaker-allerede-paa-tiltak`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns existing agreements that overlap the participant, measure type, and optional period.
- Invocation:
  Step 1: `GET /avtaler/deltaker-allerede-paa-tiltak` with required path/query/body/form/header values
- Constraints:
  Requires `deltakerFnr` and `tiltakstype`. If `avtaleId`, `startDato`, and `sluttDato` are all provided, the check excludes that agreement and uses the full date interval; otherwise it checks from `startDato` or today.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: `avtaleId` is supplied but is not a UUID. This can be produced by direct database setup, or through this violating request pattern: Use a non-UUID `avtaleId`.
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

### Function 8: search agreements and save search

Function name:
search agreements and save search

Core endpoint(s):
- `POST /avtaler/sok`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns filtered agreements and stores or reuses a saved search id derived from the request body.
- Invocation:
  Step 1: `POST /avtaler/sok` with required path/query/body/form/header values
- Constraints:
  Body is `AvtalePredicate`. The response includes `sokeParametere`, `sorteringskolonne`, and `sokId`. If an identical hash exists, usage counters are updated; otherwise a new saved search is created.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: unsupported role/token combination. This can be produced by direct database setup, or through this violating request pattern: Use an `innlogget-part` role not valid for the authenticated issuer.
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

### Function 9: replay saved agreement search

Function name:
replay saved agreement search

Core endpoint(s):
- `GET /avtaler/sok`

Preconditions:
- A saved agreement search exists in `FilterSok` for the desired `AvtalePredicate`. This can be satisfied by directly inserting a `FilterSok` row keyed by `AvtalePredicate.generateHash()`, or by calling `POST /avtaler/sok` with the filter body and reusing the returned `sokId`.

Successful execution:
- Result:
  Looks up a saved search by `sokId`, reruns it, increments usage, and returns the same response shape as search.
- Invocation:
  Step 1: `GET /avtaler/sok` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/sok` produces `sokId`; `GET /avtaler/sok` consumes that exact `sokId`. The same role constraints apply to the result filtering.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: `sokId` is unknown. This can be produced by direct database setup, or through this violating request pattern: Omit the prior `POST /avtaler/sok` or use an unrelated `sokId`.
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

### Function 10: retrieve agreement by id

Function name:
retrieve agreement by id

Core endpoint(s):
- `GET /avtaler/{avtaleId}`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Retrieves the agreement identified by `{avtaleId}` for the selected logged-in party.
- Invocation:
  Step 1: `GET /avtaler/{avtaleId}` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler` returns `Location: /avtaler/{avtaleId}`; `GET /avtaler/{avtaleId}` consumes that `{avtaleId}`. The role in `innlogget-part` must have access to the agreement.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: agreement does not exist. This can be produced by direct database setup, or through this violating request pattern: Omit any agreement creation endpoint and use an unknown `{avtaleId}`.
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

### Function 11: retrieve agreement by agreement number

Function name:
retrieve agreement by agreement number

Core endpoint(s):
- `GET /avtaler/avtaleNr/{avtaleNr}`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Retrieves an agreement by its generated agreement number.
- Invocation:
  Step 1: `GET /avtaler/avtaleNr/{avtaleNr}` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler` produces `{avtaleId}` in `Location`. `GET /avtaler/{avtaleId}` returns the agreement’s `avtaleNr`. `GET /avtaler/avtaleNr/{avtaleNr}` consumes that exact `{avtaleNr}`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: `avtaleNr` is unknown. This can be produced by direct database setup, or through this violating request pattern: Use an `avtaleNr` not obtained from a created agreement.
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

### Function 12: list agreement versions

Function name:
list agreement versions

Core endpoint(s):
- `GET /avtaler/{avtaleId}/versjoner`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Returns all stored content versions for an agreement.
- Invocation:
  Step 1: `GET /avtaler/{avtaleId}/versjoner` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler` produces `{avtaleId}`; `GET /avtaler/{avtaleId}/versjoner` consumes it. The logged-in party must have access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: agreement is missing or inaccessible. This can be produced by direct database setup, or through this violating request pattern: Use an unknown `{avtaleId}` or a role without access.
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

### Function 13: update agreement

Function name:
update agreement

Core endpoint(s):
- `PUT /avtaler/{avtaleId}`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Updates mutable agreement content and, for subsidy-backed measures, recalculates subsidy periods.
- Invocation:
  Step 1: `PUT /avtaler/{avtaleId}` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler` produces `{avtaleId}`. `GET /avtaler/{avtaleId}` returns current `sistEndret`; `PUT /avtaler/{avtaleId}` sends `If-Unmodified-Since` not earlier than that value. Body is `EndreAvtale`. Existing approvals must be absent or revoked before editing.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The failure-causing state is present: stale or missing concurrency timestamp. This can be produced by direct database setup, or through this violating request pattern: Send an old `If-Unmodified-Since` value.
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

### Function 14: dry-run agreement update

Function name:
dry-run agreement update

Core endpoint(s):
- `PUT /avtaler/{avtaleId}/dry-run`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Validates and returns the would-be updated agreement without saving it.
- Invocation:
  Step 1: `PUT /avtaler/{avtaleId}/dry-run` with required path/query/body/form/header values
- Constraints:
  Same body and `If-Unmodified-Since` constraints as `PUT /avtaler/{avtaleId}`. The returned object is not persisted.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - A required party approval exists on the agreement. This can be satisfied by directly setting the relevant approval timestamp and approver fields on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn` with the appropriate `innlogget-part` role and a fresh `If-Unmodified-Since` value.
    - The failure-causing state is present: agreement has already been approved. This can be produced by direct database setup, or through this violating request pattern: Attempt dry-run editing after an approval exists.
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

### Function 15: share agreement with a party

Function name:
share agreement with party

Core endpoint(s):
- `POST /avtaler/{avtaleId}/del-med-avtalepart`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Registers a share event for the selected agreement party and creates corresponding notifications.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/del-med-avtalepart` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/{avtaleId}/del-med-avtalepart` body is an `Avtalerolle` such as `DELTAKER`, `ARBEIDSGIVER`, or `MENTOR`. The relevant phone number on the current agreement content must be a valid mobile number.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: phone number for the selected party is missing or invalid. This can be produced by direct database setup, or through this violating request pattern: Omit the content update that supplies a valid phone number.
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

### Function 16: approve agreement as participant

Function name:
approve agreement as participant

Core endpoint(s):
- `POST /avtaler/{avtaleId}/godkjenn`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Records participant approval on a fully filled agreement.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/godkjenn` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/{avtaleId}/godkjenn` uses `innlogget-part=DELTAKER` and `If-Unmodified-Since` from `GET /avtaler/{avtaleId}`. The participant must match the agreement’s `deltakerFnr`. All fields required for the measure must be filled.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The failure-causing state is present: required agreement fields are incomplete. This can be produced by direct database setup, or through this violating request pattern: Omit `PUT /avtaler/{avtaleId}` with the required fields.
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

### Function 17: approve agreement as employer

Function name:
approve agreement as employer

Core endpoint(s):
- `POST /avtaler/{avtaleId}/godkjenn`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Records employer approval on a fully filled agreement.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/godkjenn` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/{avtaleId}/godkjenn` uses `innlogget-part=ARBEIDSGIVER`, and the employer must have access to the agreement’s `bedriftNr` and `tiltakstype`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The failure-causing state is present: employer has already approved. This can be produced by direct database setup, or through this violating request pattern: Repeat employer approval without revoking approvals.
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

### Function 18: sign mentor confidentiality declaration

Function name:
sign mentor confidentiality declaration

Core endpoint(s):
- `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`
- `POST /avtaler/{avtaleId}/godkjenn`

Preconditions:
- A mentor agreement exists in the database with distinct `deltakerFnr` and `mentorFnr`, the selected `bedriftNr`, `tiltakstype=MENTOR`, and creator role metadata. This can be satisfied by directly inserting the corresponding `Avtale` and `AvtaleInnhold` rows, or by calling `POST /avtaler/opprett-mentor-avtale` with body fields `deltakerFnr`, `mentorFnr`, `bedriftNr`, `tiltakstype`, and `avtalerolle`; `{avtaleId}` must be obtained from the `Location` response header.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Records that the mentor has signed the confidentiality declaration.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring` with required path/query/body/form/header values
  Step 2: `POST /avtaler/{avtaleId}/godkjenn` only when this documented alias or alternate core endpoint is the direct endpoint being exercised
- Constraints:
  `POST /avtaler/opprett-mentor-avtale` produces `{avtaleId}`. `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring` must be called with `innlogget-part=MENTOR`, and `If-Unmodified-Since` must not be older than the agreement’s current `sistEndret`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A mentor agreement exists in the database with distinct `deltakerFnr` and `mentorFnr`, the selected `bedriftNr`, `tiltakstype=MENTOR`, and creator role metadata. This can be satisfied by directly inserting the corresponding `Avtale` and `AvtaleInnhold` rows, or by calling `POST /avtaler/opprett-mentor-avtale` with body fields `deltakerFnr`, `mentorFnr`, `bedriftNr`, `tiltakstype`, and `avtalerolle`; `{avtaleId}` must be obtained from the `Location` response header.
    - The failure-causing state is present: caller is not logged in as mentor. This can be produced by direct database setup, or through this violating request pattern: Use `innlogget-part` other than `MENTOR`.
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

### Function 19: approve agreement as advisor

Function name:
approve agreement as advisor

Core endpoint(s):
- `POST /avtaler/{avtaleId}/godkjenn`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- A required party approval exists on the agreement. This can be satisfied by directly setting the relevant approval timestamp and approver fields on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn` with the appropriate `innlogget-part` role and a fresh `If-Unmodified-Since` value.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- A required party approval exists on the agreement. This can be satisfied by directly setting the relevant approval timestamp and approver fields on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn` with the appropriate `innlogget-part` role and a fresh `If-Unmodified-Since` value.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Records advisor approval after participant and employer have approved. For measures not requiring decision-maker approval, this also makes the agreement entered.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/godkjenn` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/{avtaleId}/godkjenn` is participant approval, `POST /avtaler/{avtaleId}/godkjenn` is employer approval, and `POST /avtaler/{avtaleId}/godkjenn` is advisor approval with `innlogget-part=VEILEDER`. Each approval uses a fresh `If-Unmodified-Since` value.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The failure-causing state is present: participant or employer approval is missing. This can be produced by direct database setup, or through this violating request pattern: Call advisor approval before participant and employer approvals.
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

### Function 20: approve on behalf of participant

Function name:
approve on behalf of participant

Core endpoint(s):
- `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av`
- `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- A required party approval exists on the agreement. This can be satisfied by directly setting the relevant approval timestamp and approver fields on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn` with the appropriate `innlogget-part` role and a fresh `If-Unmodified-Since` value.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Advisor records both advisor approval and participant approval on behalf of the participant.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av` with required path/query/body/form/header values
  Step 2: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker` only when this documented alias or alternate core endpoint is the direct endpoint being exercised
- Constraints:
  `POST /avtaler/{avtaleId}/godkjenn` must be employer approval. `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker` body must set at least one participant reason field in `GodkjentPaVegneGrunn`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - A required party approval exists on the agreement. This can be satisfied by directly setting the relevant approval timestamp and approver fields on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn` with the appropriate `innlogget-part` role and a fresh `If-Unmodified-Since` value.
    - The failure-causing state is present: no on-behalf reason is selected. This can be produced by direct database setup, or through this violating request pattern: Send all participant reason booleans as false.
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

### Function 21: approve on behalf of employer

Function name:
approve on behalf of employer

Core endpoint(s):
- `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- A required party approval exists on the agreement. This can be satisfied by directly setting the relevant approval timestamp and approver fields on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn` with the appropriate `innlogget-part` role and a fresh `If-Unmodified-Since` value.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Advisor records both advisor approval and employer approval on behalf of the employer.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/{avtaleId}/godkjenn` must be participant approval. The measure must be `SOMMERJOBB`, `MIDLERTIDIG_LONNSTILSKUDD`, or `VARIG_LONNSTILSKUDD`. `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver` body must set at least one employer reason.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - A required party approval exists on the agreement. This can be satisfied by directly setting the relevant approval timestamp and approver fields on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn` with the appropriate `innlogget-part` role and a fresh `If-Unmodified-Since` value.
    - The failure-causing state is present: measure type is not supported for employer on-behalf approval. This can be produced by direct database setup, or through this violating request pattern: Use `ARBEIDSTRENING`, `INKLUDERINGSTILSKUDD`, or `MENTOR`.
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

### Function 22: approve on behalf of participant and employer

Function name:
approve on behalf of participant and employer

Core endpoint(s):
- `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Advisor records advisor, participant, and employer approvals in one operation.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with required path/query/body/form/header values
- Constraints:
  Measure must be `SOMMERJOBB`, `MIDLERTIDIG_LONNSTILSKUDD`, or `VARIG_LONNSTILSKUDD`. Body contains both reason objects, and each must select at least one reason.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - A required party approval exists on the agreement. This can be satisfied by directly setting the relevant approval timestamp and approver fields on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn` with the appropriate `innlogget-part` role and a fresh `If-Unmodified-Since` value.
    - The failure-causing state is present: participant or employer has already approved. This can be produced by direct database setup, or through this violating request pattern: Create a prior participant or employer approval before the combined call.
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

### Function 23: revoke approvals

Function name:
revoke approvals

Core endpoint(s):
- `POST /avtaler/{avtaleId}/opphev-godkjenninger`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- A required party approval exists on the agreement. This can be satisfied by directly setting the relevant approval timestamp and approver fields on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn` with the appropriate `innlogget-part` role and a fresh `If-Unmodified-Since` value.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Clears existing participant, employer, and advisor approvals on an agreement that has not yet been entered.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/opphev-godkjenninger` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/{avtaleId}/godkjenn` creates at least one approval. `POST /avtaler/{avtaleId}/opphev-godkjenninger` uses an `innlogget-part` allowed to revoke; employer cannot revoke after advisor approval, and no one can revoke after the agreement is entered.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: no approvals exist. This can be produced by direct database setup, or through this violating request pattern: Omit all approval endpoints.
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

### Function 24: mark agreement eligible for after-registration

Function name:
mark agreement eligible for after-registration

Core endpoint(s):
- `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Toggles an unentered agreement so it is approved for after-registration.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` with required path/query/body/form/header values
- Constraints:
  Agreement must exist, be accessible to a decision-maker, and not already be entered. Initial `godkjentForEtterregistrering` must be false.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - The failure-causing state is present: agreement is already entered. This can be produced by direct database setup, or through this violating request pattern: Enter the agreement before toggling the flag.
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

### Function 25: remove after-registration eligibility

Function name:
remove after-registration eligibility

Core endpoint(s):
- `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The agreement has the requested after-registration flag state before the core call. This can be satisfied by directly setting `godkjentForEtterregistrering` on the agreement content, or by calling `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` once to toggle the flag.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Toggles an unentered agreement so it is no longer approved for after-registration.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` enables the flag. `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` consumes the same `{avtaleId}` and disables the flag. Agreement must still not be entered.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: decision-maker access is missing. This can be produced by direct database setup, or through this violating request pattern: Call without a valid decision-maker role/group.
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

### Function 26: approve subsidy period

Function name:
approve subsidy period

Core endpoint(s):
- `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Decision-maker approves the current subsidy period; for the first decision-maker-controlled period, this also enters the agreement.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode` with required path/query/body/form/header values
- Constraints:
  Use a subsidy-backed measure with generated `tilskuddPeriode`. `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode` body `enhet` must be four digits and must exist in Norg2. Decision-maker cannot be the same NAV ident that approved the agreement.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - The failure-causing state is present: `enhet` is not four digits. This can be produced by direct database setup, or through this violating request pattern: Send a non-four-digit `enhet`.
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

### Function 27: reject subsidy period

Function name:
reject subsidy period

Core endpoint(s):
- `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Decision-maker rejects the current subsidy period with rejection causes and explanation.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` body must include a non-empty `avslagsårsaker` set and a non-blank `avslagsforklaring`. Current subsidy period must be `UBEHANDLET` and not too early to decide.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - The failure-causing state is present: rejection explanation is blank. This can be produced by direct database setup, or through this violating request pattern: Send empty `avslagsforklaring`.
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

### Function 28: send rejected subsidy period back

Function name:
send rejected subsidy period back

Core endpoint(s):
- `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- An active subsidy period has rejected status with rejection causes and explanation. This can be satisfied by directly setting `TilskuddPeriode` rejection fields, or by calling `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` with `avslagsårsaker` and `avslagsforklaring`.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Deactivates active rejected subsidy periods and creates new unhandled periods for correction.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` creates the rejected active period that `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter` processes. Advisor must have access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement has been annulled before the failing or follow-up operation. This can be satisfied by directly setting annulment fields on `Avtale`, or by calling `POST /avtaler/{avtaleId}/annuller` with `annullertGrunn` and a fresh `If-Unmodified-Since` header.
    - The failure-causing state is present: agreement is annulled or interrupted. This can be produced by direct database setup, or through this violating request pattern: Annul the agreement before sending it back.
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

### Function 29: shorten agreement

Function name:
shorten agreement

Core endpoint(s):
- `POST /avtaler/{avtaleId}/forkort`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Creates a new approved version with an earlier end date and adjusts subsidy periods.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/forkort` with required path/query/body/form/header values
- Constraints:
  Agreement must be advisor-approved. `POST /avtaler/{avtaleId}/forkort` body `sluttDato` must be before current end date, after any paid/sent-claim subsidy period, and `grunn` must be present; if `grunn=Annet`, `annetGrunn` must be present.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The failure-causing state is present: new end date is not before current end date. This can be produced by direct database setup, or through this violating request pattern: Send `sluttDato` equal to or after the current `sluttDato`.
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

### Function 30: dry-run agreement shortening

Function name:
dry-run agreement shortening

Core endpoint(s):
- `POST /avtaler/{avtaleId}/forkort-dry-run`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Returns the would-be shortened agreement without saving it.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/forkort-dry-run` with required path/query/body/form/header values
- Constraints:
  Same date constraints as shorten. The controller substitutes `"dry run"` as reason and does not persist.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: agreement is not advisor-approved. This can be produced by direct database setup, or through this violating request pattern: Omit the approval sequence.
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

### Function 31: extend agreement

Function name:
extend agreement

Core endpoint(s):
- `POST /avtaler/{avtaleId}/forleng`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Creates a new approved version with a later end date and adds or recalculates subsidy periods.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/forleng` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/{avtaleId}/forleng` body `sluttDato` must be after current `sluttDato` and satisfy measure-specific duration rules.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The failure-causing state is present: new end date is not after current end date. This can be produced by direct database setup, or through this violating request pattern: Send a same or earlier `sluttDato`.
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

### Function 32: dry-run agreement extension

Function name:
dry-run agreement extension

Core endpoint(s):
- `POST /avtaler/{avtaleId}/forleng-dry-run`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Returns the would-be extended agreement without saving it.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/forleng-dry-run` with required path/query/body/form/header values
- Constraints:
  Same constraints as extension. No persistence.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: agreement is not advisor-approved. This can be produced by direct database setup, or through this violating request pattern: Omit the approval sequence.
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

### Function 33: change subsidy calculation

Function name:
change subsidy calculation

Core endpoint(s):
- `POST /avtaler/{avtaleId}/endre-tilskuddsberegning`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Creates a new approved version with changed monthly salary, holiday pay rate, employer tax, and pension rate, then recalculates unhandled subsidy amounts.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/endre-tilskuddsberegning` with required path/query/body/form/header values
- Constraints:
  Agreement must be advisor-approved and measure must be `MIDLERTIDIG_LONNSTILSKUDD`, `VARIG_LONNSTILSKUDD`, or `SOMMERJOBB`. Body fields `manedslonn`, `feriepengesats`, `arbeidsgiveravgift`, and `otpSats` must be present.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: agreement is not approved by advisor. This can be produced by direct database setup, or through this violating request pattern: Omit the approval sequence.
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

### Function 34: dry-run subsidy calculation change

Function name:
dry-run subsidy calculation change

Core endpoint(s):
- `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Returns the would-be updated agreement after subsidy calculation changes without saving.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run` with required path/query/body/form/header values
- Constraints:
  Same measure, approval, and body constraints as the persistent calculation change. No save.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - The failure-causing state is present: required calculation input is missing. This can be produced by direct database setup, or through this violating request pattern: Omit one of `manedslonn`, `feriepengesats`, `arbeidsgiveravgift`, or `otpSats`.
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

### Function 35: change contact information

Function name:
change contact information

Core endpoint(s):
- `POST /avtaler/{avtaleId}/endre-kontaktinfo`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Creates a new approved version with changed participant, advisor, employer, and refund contact information.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/endre-kontaktinfo` with required path/query/body/form/header values
- Constraints:
  Agreement must be advisor-approved. Required contact fields must be non-empty.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - The failure-causing state is present: required contact field is missing. This can be produced by direct database setup, or through this violating request pattern: Omit one required name or phone field.
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

### Function 36: change job description

Function name:
change job description

Core endpoint(s):
- `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Creates a new approved version with changed title, work tasks, occupation code, position percent, and days per week.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse` with required path/query/body/form/header values
- Constraints:
  Agreement must be advisor-approved. Body fields required by `EndreStillingsbeskrivelse` must be present.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: agreement is not advisor-approved. This can be produced by direct database setup, or through this violating request pattern: Omit the approval sequence.
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

### Function 37: change follow-up and adaptation text

Function name:
change follow-up and adaptation text

Core endpoint(s):
- `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Creates a new approved version with changed follow-up and adaptation descriptions.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging` with required path/query/body/form/header values
- Constraints:
  Agreement must be advisor-approved. `oppfolging` and `tilrettelegging` must both be present.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - The failure-causing state is present: follow-up or adaptation text is missing. This can be produced by direct database setup, or through this violating request pattern: Omit `oppfolging` or `tilrettelegging`.
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

### Function 38: change work-training goals

Function name:
change work-training goals

Core endpoint(s):
- `POST /avtaler/{avtaleId}/endre-maal`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Replaces goals on an approved work-training agreement.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/endre-maal` with required path/query/body/form/header values
- Constraints:
  Agreement must have `tiltakstype=ARBEIDSTRENING` and be advisor-approved. Body `maal` must be non-empty, and each goal must have `beskrivelse` and `kategori`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - The failure-causing state is present: agreement is not work training. This can be produced by direct database setup, or through this violating request pattern: Create any other `tiltakstype`.
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

### Function 39: change inclusion subsidy expenses

Function name:
change inclusion subsidy expenses

Core endpoint(s):
- `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Replaces inclusion subsidy expense lines on an approved inclusion subsidy agreement.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd` with required path/query/body/form/header values
- Constraints:
  Agreement must have `tiltakstype=INKLUDERINGSTILSKUDD` and be advisor-approved. Expense list must be non-empty; each item must include `beløp` and `type`; total must not exceed the implementation limit.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - The failure-causing state is present: total inclusion subsidy amount is too high. This can be produced by direct database setup, or through this violating request pattern: Send expense lines whose total exceeds the allowed maximum.
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

### Function 40: change mentor details

Function name:
change mentor details

Core endpoint(s):
- `POST /avtaler/{avtaleId}/endre-om-mentor`

Preconditions:
- A mentor agreement exists in the database with distinct `deltakerFnr` and `mentorFnr`, the selected `bedriftNr`, `tiltakstype=MENTOR`, and creator role metadata. This can be satisfied by directly inserting the corresponding `Avtale` and `AvtaleInnhold` rows, or by calling `POST /avtaler/opprett-mentor-avtale` with body fields `deltakerFnr`, `mentorFnr`, `bedriftNr`, `tiltakstype`, and `avtalerolle`; `{avtaleId}` must be obtained from the `Location` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The mentor confidentiality approval exists on the mentor agreement. This can be satisfied by directly setting the mentor approval field on the current agreement content, or by calling `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring` as `innlogget-part=MENTOR` with a fresh `If-Unmodified-Since` header.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Creates a new approved version with changed mentor name, phone, tasks, hours, and hourly wage.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/endre-om-mentor` with required path/query/body/form/header values
- Constraints:
  Agreement must have `tiltakstype=MENTOR` and be advisor-approved. Mentor detail fields must be present.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - The failure-causing state is present: agreement is not a mentor agreement. This can be produced by direct database setup, or through this violating request pattern: Use a non-mentor agreement.
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

### Function 41: change cost center

Function name:
change cost center

Core endpoint(s):
- `POST /avtaler/{avtaleId}/endre-kostnadssted`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Sets the cost-center unit and unit name on unhandled or rejected subsidy periods.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/endre-kostnadssted` with required path/query/body/form/header values
- Constraints:
  The agreement must have generated subsidy periods that are not closed for editing. Body `enhet` must exist in Norg2. Agreement must not be entered.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - The failure-causing state is present: norg2 does not return a unit for `enhet`. This can be produced by direct database setup, or through this violating request pattern: Use an unknown unit number.
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

### Function 42: adjust Arena migration date

Function name:
adjust Arena migration date

Core endpoint(s):
- `POST /avtaler/{avtaleId}/juster-arena-migreringsdato`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Recomputes subsidy periods around a migration date and saves or updates the agreement’s Arena cleanup marker.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/juster-arena-migreringsdato` with required path/query/body/form/header values
- Constraints:
  Body contains `migreringsdato`. Agreement must not be entered. Generated periods ending before the migration date are marked `BEHANDLET_I_ARENA`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - The failure-causing state is present: agreement is already entered. This can be produced by direct database setup, or through this violating request pattern: Enter the agreement before adjusting migration date.
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

### Function 43: dry-run Arena migration date adjustment

Function name:
dry-run Arena migration date adjustment

Core endpoint(s):
- `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Returns the would-be agreement after recalculating periods around the migration date without saving it.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run` with required path/query/body/form/header values
- Constraints:
  Body contains `migreringsdato`. Advisor must have access. Unlike the persistent endpoint, the implementation does not explicitly reject entered agreements before recalculation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: agreement is missing. This can be produced by direct database setup, or through this violating request pattern: Omit the agreement creation endpoint.
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

### Function 44: get employer account number

Function name:
get employer account number

Core endpoint(s):
- `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Returns the employer’s bank account number for the agreement’s company.
- Invocation:
  Step 1: `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler` produces `{avtaleId}`. `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver` uses the agreement’s `bedriftNr` when calling the account register.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: account register has no company account. This can be produced by direct database setup, or through this violating request pattern: Use an agreement whose company is not found in the account register.
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

### Function 45: download agreement PDF

Function name:
download agreement PDF

Core endpoint(s):
- `GET /avtaler/{avtaleId}/pdf`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Returns a PDF representation of an advisor-approved agreement.
- Invocation:
  Step 1: `GET /avtaler/{avtaleId}/pdf` with required path/query/body/form/header values
- Constraints:
  Agreement must be approved by advisor before `GET /avtaler/{avtaleId}/pdf`. Response is `application/pdf`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: agreement is not approved by advisor. This can be produced by direct database setup, or through this violating request pattern: Omit advisor approval.
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

### Function 46: check whether Salesforce dialog should be shown

Function name:
check Salesforce dialog visibility

Core endpoint(s):
- `GET /avtaler/{avtaleId}/vis-salesforce-dialog`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Returns `true` when the agreement belongs to a configured Salesforce office, has `MIDLERTIDIG_LONNSTILSKUDD`, and is `GJENNOMFØRES` or `AVSLUTTET`; otherwise returns `false`.
- Invocation:
  Step 1: `GET /avtaler/{avtaleId}/vis-salesforce-dialog` with required path/query/body/form/header values
- Constraints:
  `GET /avtaler/{avtaleId}/vis-salesforce-dialog` consumes `{avtaleId}` from `POST /avtaler`. The current agreement state and configured office list determine the boolean.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: agreement is missing or inaccessible. This can be produced by direct database setup, or through this violating request pattern: Use an unknown `{avtaleId}` or unauthorized role.
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

### Function 47: refresh follow-up unit

Function name:
refresh follow-up unit

Core endpoint(s):
- `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Refreshes participant data, follow-up status, geographic unit, and follow-up unit name, then saves the agreement.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet` consumes `{avtaleId}` and must be called by an advisor with access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: participant has protected address code 6 when person data is refreshed. This can be produced by direct database setup, or through this violating request pattern: Use an agreement participant with code 6 protection.
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

### Function 48: take over agreement as advisor

Function name:
take over agreement as advisor

Core endpoint(s):
- `PUT /avtaler/{avtaleId}/overta`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Assigns the logged-in advisor as the agreement’s advisor and refreshes follow-up data.
- Invocation:
  Step 1: `PUT /avtaler/{avtaleId}/overta` with required path/query/body/form/header values
- Constraints:
  `PUT /avtaler/{avtaleId}/overta` consumes `{avtaleId}`. The logged-in advisor must have access and must not already be the agreement’s advisor.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: logged-in advisor is already the agreement advisor. This can be produced by direct database setup, or through this violating request pattern: Use the same advisor who created or already owns the agreement.
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

### Function 49: annul agreement

Function name:
annul agreement

Core endpoint(s):
- `POST /avtaler/{avtaleId}/annuller`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Annuls an agreement and annuls/removes eligible subsidy periods; if reason is `Feilregistrering`, the agreement is also marked as incorrectly registered.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/annuller` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler/{avtaleId}/annuller` body contains `annullertGrunn`. `If-Unmodified-Since` must not be stale. Agreement must not contain paid or refund-approved subsidy periods.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
    - The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
    - Advisor, participant, and employer approvals exist for the agreement through the combined on-behalf path. This can be satisfied by directly setting the three approval states and both reason objects on `AvtaleInnhold`, or by calling `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with participant and employer reason bodies.
    - A subsidy period has been approved for the agreement by a decision-maker with a valid four-digit `enhet`. This can be satisfied by directly setting the active `TilskuddPeriode` status and approval metadata, or by calling `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode` with body field `enhet`.
    - The failure-causing state is present: agreement contains a paid subsidy period. This can be produced by direct database setup, or through this violating request pattern: Annul after period/refund state blocks annulment.
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

### Function 50: soft-delete agreement

Function name:
soft-delete agreement

Core endpoint(s):
- `POST /avtaler/{avtaleId}/slettemerk`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Marks the agreement as deleted/hidden.
- Invocation:
  Step 1: `POST /avtaler/{avtaleId}/slettemerk` with required path/query/body/form/header values
- Constraints:
  Logged-in advisor must have access and must be configured in `SlettemerkeProperties.ident`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: advisor is not configured as a delete-marker admin. This can be produced by direct database setup, or through this violating request pattern: Call as a valid advisor not in the configured admin ident list.
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

### Function 51: list employer agreements for Min Side Arbeidsgiver

Function name:
list employer agreements

Core endpoint(s):
- `GET /avtaler/min-side-arbeidsgiver`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns all agreements for a company that the logged-in employer can view.
- Invocation:
  Step 1: `GET /avtaler/min-side-arbeidsgiver` with required path/query/body/form/header values
- Constraints:
  Query `bedriftNr` is required. Employer must have Altinn access to the company/measure; old annulled, interrupted, or ended agreements may be filtered out.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: caller is not an employer token/role. This can be produced by direct database setup, or through this violating request pattern: Call with non-employer token/role.
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

### Function 52: list decision-maker agreements

Function name:
list decision-maker agreements

Core endpoint(s):
- `GET /avtaler/beslutter-liste`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns paginated agreements whose subsidy periods match decision-maker filters and the decision-maker’s NAV units.
- Invocation:
  Step 1: `GET /avtaler/beslutter-liste` with required path/query/body/form/header values
- Constraints:
  Caller must be a decision-maker. If `tilskuddPeriodeStatus` is absent, implementation defaults to `UBEHANDLET`. If `tiltakstype` is absent, it searches `SOMMERJOBB`, `VARIG_LONNSTILSKUDD`, and `MIDLERTIDIG_LONNSTILSKUDD`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: decision-maker has no NAV units. This can be produced by direct database setup, or through this violating request pattern: Use a decision-maker identity with no AXsys units.
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

### Function 53: get logged-in user

Function name:
get logged-in user

Core endpoint(s):
- `GET /innlogget-bruker`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns role-specific information for the logged-in user.
- Invocation:
  Step 1: `GET /innlogget-bruker` with required path/query/body/form/header values
- Constraints:
  Although OpenAPI marks `innlogget-part` as optional, implementation requires it and throws if absent. The role must match the token issuer.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: `innlogget-part` cookie is absent. This can be produced by direct database setup, or through this violating request pattern: Omit `innlogget-part`.
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

### Function 54: look up organization

Function name:
look up organization

Core endpoint(s):
- `GET /organisasjoner`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns organization data for an employer unit.
- Invocation:
  Step 1: `GET /organisasjoner` with required path/query/body/form/header values
- Constraints:
  Query `bedriftNr` is required. Ereg result must not be a juridical unit or organization-led unit.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: ereg does not find the unit. This can be produced by direct database setup, or through this violating request pattern: Use an unknown `bedriftNr`.
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

### Function 55: get Altinn rights request URLs

Function name:
get Altinn rights request URLs

Core endpoint(s):
- `GET /be-om-altinn-rettighet-urler`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns URLs that let an employer request Altinn rights for each supported measure type.
- Invocation:
  Step 1: `GET /be-om-altinn-rettighet-urler` with required path/query/body/form/header values
- Constraints:
  Query `orgNr` is appended as `bedrift` to the configured base URL. The response maps supported `Tiltakstype` values to URLs.

Failure or exceptional branches:
- None identified in implementation beyond framework-level parameter binding, authentication, or external dependency failures.

Endpoint coverage:
- Covers:
  `GET /be-om-altinn-rettighet-urler`
- Distinct meaning:
  Build Altinn permission-request URLs.

### Function 56: get all code lists

Function name:
get all code lists

Core endpoint(s):
- `GET /kodeverk`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns both measure types and agreement statuses.
- Invocation:
  Step 1: `GET /kodeverk` with required path/query/body/form/header values
- Constraints:
  No resource state required.

Failure or exceptional branches:
- None identified in implementation beyond framework-level parameter binding, authentication, or external dependency failures.

Endpoint coverage:
- Covers:
  `GET /kodeverk`
- Distinct meaning:
  Combined code-list discovery.

### Function 57: get status code list

Function name:
get status code list

Core endpoint(s):
- `GET /kodeverk/statuser`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns all `Status` enum names and descriptions.
- Invocation:
  Step 1: `GET /kodeverk/statuser` with required path/query/body/form/header values
- Constraints:
  No resource state required.

Failure or exceptional branches:
- None identified in implementation beyond framework-level parameter binding, authentication, or external dependency failures.

Endpoint coverage:
- Covers:
  `GET /kodeverk/statuser`
- Distinct meaning:
  Status code-list discovery.

### Function 58: get measure type code list

Function name:
get measure type code list

Core endpoint(s):
- `GET /kodeverk/tiltakstyper`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns all `Tiltakstype` enum names, descriptions, treatment themes, and optional Arena codes.
- Invocation:
  Step 1: `GET /kodeverk/tiltakstyper` with required path/query/body/form/header values
- Constraints:
  No resource state required.

Failure or exceptional branches:
- None identified in implementation beyond framework-level parameter binding, authentication, or external dependency failures.

Endpoint coverage:
- Covers:
  `GET /kodeverk/tiltakstyper`
- Distinct meaning:
  Measure type code-list discovery.

### Function 59: evaluate feature toggles

Function name:
evaluate feature toggles

Core endpoint(s):
- `GET /feature`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns enabled/disabled values for requested feature names.
- Invocation:
  Step 1: `GET /feature` with required path/query/body/form/header values
- Constraints:
  Query `feature` is a list. Each feature name becomes a key in the returned map.

Failure or exceptional branches:
- None identified in implementation beyond framework-level parameter binding, authentication, or external dependency failures.

Endpoint coverage:
- Covers:
  `GET /feature`
- Distinct meaning:
  Boolean feature-toggle evaluation.

### Function 60: get feature variants

Function name:
get feature variants

Core endpoint(s):
- `GET /feature/variant`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns Unleash variant objects for requested feature names.
- Invocation:
  Step 1: `GET /feature/variant` with required path/query/body/form/header values
- Constraints:
  Query `feature` is a list. Logged-in user id is included in the Unleash context when available.

Failure or exceptional branches:
- None identified in implementation beyond framework-level parameter binding, authentication, or external dependency failures.

Endpoint coverage:
- Covers:
  `GET /feature/variant`
- Distinct meaning:
  Feature variant lookup.

### Function 61: health check

Function name:
run health check

Core endpoint(s):
- `GET /internal/healthcheck`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns `ok` if the database query succeeds.
- Invocation:
  Step 1: `GET /internal/healthcheck` with required path/query/body/form/header values
- Constraints:
  No specific resource state required; database must answer `select 'ok'`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: database query fails. This can be produced by direct database setup, or through this violating request pattern: Database unavailable or query cannot execute.
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

### Function 62: list overview notifications

Function name:
list overview notifications

Core endpoint(s):
- `GET /varsler/oversikt`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.

Successful execution:
- Result:
  Returns unread bell notifications for the logged-in party’s identifiers.
- Invocation:
  Step 1: `GET /varsler/oversikt` with required path/query/body/form/header values
- Constraints:
  Agreement creation and other agreement events create notifications through event listeners. `GET /varsler/oversikt` returns only `lest=false`, `bjelle=true`, and matching identifiers.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: unsupported role/token combination. This can be produced by direct database setup, or through this violating request pattern: Use invalid `innlogget-part` for the token.
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

### Function 63: list agreement modal notifications

Function name:
list agreement modal notifications

Core endpoint(s):
- `GET /varsler/avtale-modal`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.

Successful execution:
- Result:
  Returns unread bell notifications for a specific agreement and logged-in party.
- Invocation:
  Step 1: `GET /varsler/avtale-modal` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler` produces `{avtaleId}`; `GET /varsler/avtale-modal` uses query `avtaleId={avtaleId}` and filters by the logged-in party identifiers.

Failure or exceptional branches:
- None identified in implementation beyond framework-level parameter binding, authentication, or external dependency failures.

Endpoint coverage:
- Covers:
  `GET /varsler/avtale-modal`
- Distinct meaning:
  Agreement-scoped unread bell notification list.

### Function 64: list agreement notification log

Function name:
list agreement notification log

Core endpoint(s):
- `GET /varsler/avtale-logg`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.

Successful execution:
- Result:
  Returns all notifications for a specific agreement and receiver role.
- Invocation:
  Step 1: `GET /varsler/avtale-logg` with required path/query/body/form/header values
- Constraints:
  `GET /varsler/avtale-logg` uses `avtaleId` from `POST /avtaler` and checks the logged-in party’s access to the agreement before returning log entries.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: agreement id is unknown. This can be produced by direct database setup, or through this violating request pattern: Omit agreement creation and use an unknown `avtaleId`.
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

### Function 65: mark notification as read

Function name:
mark notification as read

Core endpoint(s):
- `POST /varsler/{varselId}/sett-til-lest`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- At least one unread bell notification exists for the logged-in party identifiers. This can be satisfied by directly inserting a `Varsel` row with `lest=false`, `bjelle=true`, and matching `identifikator`, or by using agreement operations that emit such notifications and then reading them with `GET /varsler/oversikt` to obtain `{varselId}`.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Marks one notification as read for the logged-in party.
- Invocation:
  Step 1: `POST /varsler/{varselId}/sett-til-lest` with required path/query/body/form/header values
- Constraints:
  `GET /varsler/oversikt` returns a notification id. `POST /varsler/{varselId}/sett-til-lest` consumes that exact `{varselId}`. Notification must belong to one of the logged-in party’s identifiers.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - At least one unread bell notification exists for the logged-in party identifiers. This can be satisfied by directly inserting a `Varsel` row with `lest=false`, `bjelle=true`, and matching `identifikator`, or by using agreement operations that emit such notifications and then reading them with `GET /varsler/oversikt` to obtain `{varselId}`.
    - The failure-causing state is present: notification does not belong to logged-in party. This can be produced by direct database setup, or through this violating request pattern: Reuse a `{varselId}` from another party’s notification list.
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

### Function 66: mark multiple notifications as read

Function name:
mark multiple notifications as read

Core endpoint(s):
- `POST /varsler/sett-alle-til-lest`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- At least one unread bell notification exists for the logged-in party identifiers. This can be satisfied by directly inserting a `Varsel` row with `lest=false`, `bjelle=true`, and matching `identifikator`, or by using agreement operations that emit such notifications and then reading them with `GET /varsler/oversikt` to obtain `{varselId}`.

Successful execution:
- Result:
  Marks each notification id in the request body as read.
- Invocation:
  Step 1: `POST /varsler/sett-alle-til-lest` with required path/query/body/form/header values
- Constraints:
  `GET /varsler/oversikt` produces notification ids. `POST /varsler/sett-alle-til-lest` body is an array of those ids and internally calls the single-notification read function for each.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - At least one unread bell notification exists for the logged-in party identifiers. This can be satisfied by directly inserting a `Varsel` row with `lest=false`, `bjelle=true`, and matching `identifikator`, or by using agreement operations that emit such notifications and then reading them with `GET /varsler/oversikt` to obtain `{varselId}`.
    - The failure-causing state is present: any body id is not readable by the logged-in party. This can be produced by direct database setup, or through this violating request pattern: Include at least one foreign or unknown notification id.
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

### Function 67: list unjournaled agreements

Function name:
list unjournaled agreements

Core endpoint(s):
- `GET /internal/avtaler`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Returns agreement versions that need journalføring, including subsidy periods.
- Invocation:
  Step 1: `GET /internal/avtaler` with required path/query/body/form/header values
- Constraints:
  Must be called by the configured system user. No specific agreement needs to be created for the endpoint to succeed, though it may return an empty list.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: caller is not the configured system user. This can be produced by direct database setup, or through this violating request pattern: Use a non-system token.
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

### Function 68: mark agreement versions as journaled

Function name:
mark agreement versions as journaled

Core endpoint(s):
- `PUT /internal/avtaler`

Preconditions:
- At least one agreement content version requiring journalføring exists, and its `{avtaleVersjonId}` is available. This can be satisfied by directly inserting or leaving an `AvtaleInnhold` row without `journalpostId`, or by calling `GET /internal/avtaler` as the system user and reusing the returned version id.

Successful execution:
- Result:
  Stores journal post ids on agreement content versions.
- Invocation:
  Step 1: `PUT /internal/avtaler` with required path/query/body/form/header values
- Constraints:
  `GET /internal/avtaler` returns agreement version ids. `PUT /internal/avtaler` body is a map from those UUIDs to journal post id strings. OpenAPI only says request body is `object`; implementation specifically expects `Map<UUID, String>`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: caller is not the configured system user. This can be produced by direct database setup, or through this violating request pattern: Use a non-system token.
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

### Function 69: recalculate wage subsidy for selected agreements

Function name:
recalculate wage subsidy

Core endpoint(s):
- `POST /utvikler-admin/reberegn`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.

Successful execution:
- Result:
  Recalculates missing wage subsidy totals for each selected agreement.
- Invocation:
  Step 1: `POST /utvikler-admin/reberegn` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler` produces `{avtaleId}`. `POST /utvikler-admin/reberegn` body is an array containing `{avtaleId}`. Caller must have developer-admin group access. Agreement must be a subsidy-backed measure and satisfy recalculation preconditions.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: caller lacks developer-admin access. This can be produced by direct database setup, or through this violating request pattern: Call without the configured developer group.
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

### Function 70: fix missing reduced-percent date in batch

Function name:
fix missing reduced-percent date

Core endpoint(s):
- `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Finds entered permanent wage-subsidy agreements missing reduced-percent data and recalculates reduced values and migrated periods.
- Invocation:
  Step 1: `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}` with required path/query/body/form/header values
- Constraints:
  `{migreringsDato}` must parse as `yyyy-MM-dd`. Caller must have developer-admin access. The endpoint scans repository state; no specific resource id is required.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: caller lacks developer-admin access. This can be produced by direct database setup, or through this violating request pattern: Call without developer-admin group membership.
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

### Function 71: dry-run missing reduced-percent date fix

Function name:
dry-run missing reduced-percent date fix

Core endpoint(s):
- `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Counts/logs permanent wage-subsidy agreements that would be repaired, without saving changes.
- Invocation:
  Step 1: `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}` with required path/query/body/form/header values
- Constraints:
  `{migreringsDato}` must parse as `yyyy-MM-dd`. Caller must have developer-admin access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: caller lacks developer-admin access. This can be produced by direct database setup, or through this violating request pattern: Call without developer-admin group membership.
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

### Function 72: generate subsidy periods for one agreement

Function name:
generate subsidy periods for agreement

Core endpoint(s):
- `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Generates subsidy periods for one agreement after an Arena migration date.
- Invocation:
  Step 1: `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler` produces `{avtaleId}`. `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}` consumes it and a `yyyy-MM-dd` migration date. Agreement must have sufficient salary/date fields for period generation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: agreement id is unknown. This can be produced by direct database setup, or through this violating request pattern: Omit agreement creation and use an unknown `{avtaleId}`.
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

### Function 73: recalculate unhandled subsidy periods

Function name:
recalculate unhandled subsidy periods

Core endpoint(s):
- `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Removes unhandled periods and recreates them from the first unhandled point through agreement end.
- Invocation:
  Step 1: `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}` with required path/query/body/form/header values
- Constraints:
  Agreement must be subsidy-backed and have period state. Caller must have developer-admin access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: agreement type is not subsidy-backed. This can be produced by direct database setup, or through this violating request pattern: Use `ARBEIDSTRENING`, `INKLUDERINGSTILSKUDD`, or `MENTOR`.
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

### Function 74: find subsidy period date-order problems

Function name:
find subsidy period date-order problems

Core endpoint(s):
- `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Scans temporary wage-subsidy agreements and logs periods whose start date is earlier than the previous sequence number’s start date.
- Invocation:
  Step 1: `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer` with required path/query/body/form/header values
- Constraints:
  Caller must have developer-admin access. The endpoint is diagnostic and returns no domain object.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: caller lacks developer-admin access. This can be produced by direct database setup, or through this violating request pattern: Call without developer-admin group membership.
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

### Function 75: annul subsidy period

Function name:
annul subsidy period

Core endpoint(s):
- `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Marks a subsidy period as annulled and emits an annulment event unless its refund status is expired.
- Invocation:
  Step 1: `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}` with required path/query/body/form/header values
- Constraints:
  `GET /avtaler/{avtaleId}` returns `tilskuddPeriode[].id`; `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}` consumes that `{tilskuddsperiodeId}`. Caller must have developer-admin access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: subsidy period id is unknown. This can be produced by direct database setup, or through this violating request pattern: Use a `{tilskuddsperiodeId}` not obtained from an agreement response.
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

### Function 76: annul and resend subsidy period as approved

Function name:
annul and resend approved subsidy period

Core endpoint(s):
- `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Annuls an existing subsidy period and creates a replacement period with approved status, reusing approval metadata.
- Invocation:
  Step 1: `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}` with required path/query/body/form/header values
- Constraints:
  `GET /avtaler/{avtaleId}` returns `{tilskuddsperiodeId}`. `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}` consumes it. Caller must have developer-admin access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: subsidy period id is unknown. This can be produced by direct database setup, or through this violating request pattern: Use an unknown `{tilskuddsperiodeId}`.
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

### Function 77: annul and generate unhandled subsidy period

Function name:
annul and generate unhandled subsidy period

Core endpoint(s):
- `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Annuls an existing subsidy period and creates a replacement with unhandled status.
- Invocation:
  Step 1: `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}` with required path/query/body/form/header values
- Constraints:
  `GET /avtaler/{avtaleId}` returns `{tilskuddsperiodeId}`. `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}` consumes it. Caller must have developer-admin access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
    - The failure-causing state is present: agreement measure is not subsidy-backed. This can be produced by direct database setup, or through this violating request pattern: Use a subsidy period associated with an unsupported measure type, if such invalid state exists.
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

### Function 78: annul and generate Arena-treated periods before date

Function name:
annul and generate Arena-treated periods

Core endpoint(s):
- `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The caller has loaded the agreement identified by `{avtaleId}` and reused response values such as `sistEndret`, `avtaleNr`, status, or generated subsidy-period ids as required by this function. Direct database setup can satisfy the same values by setting the agreement row and current content fields, but an API caller obtains them from `GET /avtaler/{avtaleId}` with a matching `innlogget-part` cookie.
- The agreement content has been filled or updated with the fields required by the measure type, and any generated subsidy periods have been recalculated. This can be satisfied by directly updating `AvtaleInnhold` and related `TilskuddPeriode` rows, or by calling `PUT /avtaler/{avtaleId}` with an `EndreAvtale` body and a fresh `If-Unmodified-Since` header from the agreement response.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Annuls all subsidy periods on an agreement ending before `{dato}` and creates replacements with `BEHANDLET_I_ARENA` status.
- Invocation:
  Step 1: `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler` produces `{avtaleId}`. `{dato}` must parse as `yyyy-MM-dd`. Caller must have developer-admin access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: agreement id is unknown. This can be produced by direct database setup, or through this violating request pattern: Use an unknown `{avtaleId}`.
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

### Function 79: patch selected agreements to data warehouse

Function name:
patch selected data warehouse messages

Core endpoint(s):
- `POST /utvikler-admin/dvh-melding/patch`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.

Successful execution:
- Result:
  Creates DVH patch message entities for selected agreement ids found in the repository.
- Invocation:
  Step 1: `POST /utvikler-admin/dvh-melding/patch` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler` produces `{avtaleId}`. `POST /utvikler-admin/dvh-melding/patch` body is `{"avtaleIder":[ "{avtaleId}" ]}`. Caller must have the configured DVH patch group. Unknown ids are ignored by `findAllById`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: caller lacks DVH patch group access. This can be produced by direct database setup, or through this violating request pattern: Call without the configured group.
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

### Function 80: patch all agreements to data warehouse

Function name:
patch all data warehouse messages

Core endpoint(s):
- `POST /utvikler-admin/dvh-melding/patchalleavtaler`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Creates DVH patch messages for all agreements.
- Invocation:
  Step 1: `POST /utvikler-admin/dvh-melding/patchalleavtaler` with required path/query/body/form/header values
- Constraints:
  Caller must have the configured DVH patch group. No specific agreement setup endpoint is required for the endpoint to run.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: caller lacks DVH patch group access. This can be produced by direct database setup, or through this violating request pattern: Call without the configured group.
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

### Function 81: send agreement event message for one agreement

Function name:
send event message for one agreement

Core endpoint(s):
- `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}`

Preconditions:
- An agreement exists in the database with the participant, employer company, measure type, access scope, and initial content needed for this function. This can be satisfied by directly inserting an `Avtale` row and its current `AvtaleInnhold`, or by calling `POST /avtaler` with body fields `deltakerFnr`, `bedriftNr`, and `tiltakstype` plus optional query `ryddeavtale`; the created `{avtaleId}` must be taken from the `Location: /avtaler/{avtaleId}` response header.
- The path id, query id, concurrency timestamp, or response-derived value used by the core endpoint must refer to the resource state described above; an API caller obtains it from the setup response such as `Location`, `sistEndret`, `sokId`, `avtaleNr`, `varselId`, or `tilskuddsperiode[].id`, while direct database setup must set and reuse the same value explicitly.

Successful execution:
- Result:
  Sends an agreement event message for one existing agreement.
- Invocation:
  Step 1: `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}` with required path/query/body/form/header values
- Constraints:
  `POST /avtaler` produces `{avtaleId}`. `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}` consumes it. Implementation loads the agreement and sends through `AvtaleHendelseService`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: agreement id is unknown. This can be produced by direct database setup, or through this violating request pattern: Omit agreement creation and use an unknown `{avtaleId}`.
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

### Function 82: send agreement event messages for all agreements

Function name:
send event messages for all agreements

Core endpoint(s):
- `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Sends agreement event messages for all agreements.
- Invocation:
  Step 1: `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler` with required path/query/body/form/header values
- Constraints:
  Caller must have developer-admin group access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: caller lacks developer-admin access. This can be produced by direct database setup, or through this violating request pattern: Call without developer-admin group membership.
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

### Function 83: dry-run event messages for all agreements

Function name:
dry-run event messages for all agreements

Core endpoint(s):
- `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler`

Preconditions:
- No persistent resource precondition is required beyond a valid authenticated caller, required cookies or query parameters, and any external service availability used by this endpoint.

Successful execution:
- Result:
  Performs the all-agreement event-message operation in dry-run mode.
- Invocation:
  Step 1: `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler` with required path/query/body/form/header values
- Constraints:
  Caller must have developer-admin group access. Implementation delegates to the dry-run service method.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state is present: caller lacks developer-admin access. This can be produced by direct database setup, or through this violating request pattern: Call without developer-admin group membership.
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
