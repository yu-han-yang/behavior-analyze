### Function 1: create case

Function name:
create case

Core endpoint(s):
- `POST /api/fagsaker`

Preconditions:
- No matching fagsak already exists for the same person identity, fagsak type, and institution scope when the desired effect is creation. This can be satisfied by starting from a database without that fagsak or by deleting/directly omitting the matching fagsak state.

Successful execution:
- Result:
  This function creates a new fagsak for `personIdent` when no matching fagsak already exists.
- Invocation:
  Step 1: `POST /api/fagsaker` with body `personIdent` and optional `fagsakType`.
- Constraints:
  If `fagsakType` is `INSTITUSJON`, the body must include `institusjon.orgNummer`. The returned `RestMinimalFagsak.id` is the `{fagsakId}` used by later case-scoped endpoints.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Institution fagsak creation is requested without `institusjon.orgNummer`. This intentionally creates or omits the state described by: `fagsakType=INSTITUSJON` but no `institusjon.orgNummer`.
  - Failure endpoint:
    `POST /api/fagsaker`
  - Why this fails:
    `FagsakService.hentEllerOpprettFagsak` throws a functional error when institution organization number is missing.
  - Intentionally violated constraints:
    `fagsakType=INSTITUSJON` but no `institusjon.orgNummer`.

Endpoint coverage:
- Covers:
  `POST /api/fagsaker`
- Distinct meaning:
  Creates a new fagsak when no existing fagsak matches the actor, type, and institution key.

### Function 2: return existing case

Function name:
return existing case

Core endpoint(s):
- `POST /api/fagsaker`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker` with `personIdent`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function returns an existing fagsak instead of creating a duplicate.
- Invocation:
  Step 1: `POST /api/fagsaker` again with the same `personIdent`, same `fagsakType`, and same institution organization number if applicable.
- Constraints:
  The second call must use the same case identity values as the first call.

Failure or exceptional branches:
- None identified beyond access checks and external person lookup failures.

Endpoint coverage:
- Covers:
  `POST /api/fagsaker`
- Distinct meaning:
  Retrieves an already existing fagsak for the same person/type instead of creating a new one.

### Function 3: retrieve full case

Function name:
retrieve full case

Core endpoint(s):
- `GET /api/fagsaker/{fagsakId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function retrieves the full fagsak, including treatments and repayment treatments.
- Invocation:
  Step 1: `GET /api/fagsaker/{fagsakId}`
- Constraints:
  `{fagsakId}` must be the `id` returned by `POST /api/fagsaker`.

Failure or exceptional branches:
- None identified beyond missing fagsak/access lookup failures.

Endpoint coverage:
- Covers:
  `GET /api/fagsaker/{fagsakId}`
- Distinct meaning:
  Reads full fagsak details.

### Function 4: retrieve minimal case

Function name:
retrieve minimal case

Core endpoint(s):
- `GET /api/fagsaker/minimal/{fagsakId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function retrieves a compact fagsak view.
- Invocation:
  Step 1: `GET /api/fagsaker/minimal/{fagsakId}`
- Constraints:
  `{fagsakId}` must come from the fagsak creation/retrieval response.

Failure or exceptional branches:
- None identified beyond missing fagsak/access lookup failures.

Endpoint coverage:
- Covers:
  `GET /api/fagsaker/minimal/{fagsakId}`
- Distinct meaning:
  Reads minimal fagsak details.

### Function 5: find minimal case for person

Function name:
find minimal case for person

Core endpoint(s):
- `POST /api/fagsaker/hent-fagsak-paa-person`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function finds the minimal fagsak for a given person and fagsak type.
- Invocation:
  Step 1: `POST /api/fagsaker/hent-fagsak-paa-person`
- Constraints:
  The core request body `personIdent` must match the person established by the fagsak precondition, and `fagsakType` must match the created fagsak type.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No fagsak exists for the requested person/type. This intentionally creates or omits the state described by: The required state normally established by `POST /api/fagsaker` was omitted.
  - Failure endpoint:
    `POST /api/fagsaker/hent-fagsak-paa-person`
  - Why this fails:
    `FagsakService.hentMinimalFagsakForPerson` returns a failure resource when no fagsak is found.
  - Intentionally violated constraints:
    The required state normally established by `POST /api/fagsaker` was omitted.

Endpoint coverage:
- Covers:
  `POST /api/fagsaker/hent-fagsak-paa-person`
- Distinct meaning:
  Looks up one minimal fagsak by person and type.

### Function 6: find all minimal cases for person

Function name:
find all minimal cases for person

Core endpoint(s):
- `POST /api/fagsaker/hent-fagsaker-paa-person`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function returns all minimal fagsaker for a person filtered by requested fagsak types.
- Invocation:
  Step 1: `POST /api/fagsaker/hent-fagsaker-paa-person`
- Constraints:
  The core request body `personIdent` must match the person established by the fagsak precondition; `fagsakTyper` must include the created fagsak type.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No fagsaker exist for the requested person/types. This intentionally creates or omits the state described by: The required state normally established by `POST /api/fagsaker` was omitted.
  - Failure endpoint:
    `POST /api/fagsaker/hent-fagsaker-paa-person`
  - Why this fails:
    The service returns a failure resource when the filtered list is empty.
  - Intentionally violated constraints:
    The required state normally established by `POST /api/fagsaker` was omitted.

Endpoint coverage:
- Covers:
  `POST /api/fagsaker/hent-fagsaker-paa-person`
- Distinct meaning:
  Looks up multiple minimal fagsaker for a person.

### Function 7: search case participants

Function name:
search case participants

Core endpoint(s):
- `POST /api/fagsaker/sok`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function searches fagsak participants for a person identifier.
- Invocation:
  Step 1: `POST /api/fagsaker/sok`
- Constraints:
  Body must include the search person identifier.

Failure or exceptional branches:
- None identified in project source.

Endpoint coverage:
- Covers:
  `POST /api/fagsaker/sok`
- Distinct meaning:
  Searches participant records.

### Function 8: search cases where person participates

Function name:
search cases where person participates

Core endpoint(s):
- `POST /api/fagsaker/sok/fagsaker-hvor-person-er-deltaker`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns fagsak ids where the person is the applicant or receives ongoing ordinary child benefit.
- Invocation:
  Step 1: `POST /api/fagsaker/sok/fagsaker-hvor-person-er-deltaker`
- Constraints:
  Body must include `personIdent`.

Failure or exceptional branches:
- None identified beyond person lookup/access failures.

Endpoint coverage:
- Covers:
  `POST /api/fagsaker/sok/fagsaker-hvor-person-er-deltaker`
- Distinct meaning:
  Searches fagsaker where the person is a participant.

### Function 9: search cases with ongoing benefit for person

Function name:
search cases with ongoing benefit for person

Core endpoint(s):
- `POST /api/fagsaker/sok/fagsaker-hvor-person-mottar-lopende-ytelse`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns fagsaker where the person receives ongoing ordinary or extended child benefit.
- Invocation:
  Step 1: `POST /api/fagsaker/sok/fagsaker-hvor-person-mottar-lopende-ytelse`
- Constraints:
  Body must include `personIdent`.

Failure or exceptional branches:
- None identified beyond person lookup/access failures.

Endpoint coverage:
- Covers:
  `POST /api/fagsaker/sok/fagsaker-hvor-person-mottar-lopende-ytelse`
- Distinct meaning:
  Searches fagsaker with ongoing benefit for the person.

### Function 10: resolve case participants from applicant and children

Function name:
resolve case participants

Core endpoint(s):
- `POST /api/fagsaker/sok/fagsakdeltagere`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns fagsak participants for an applicant and child identifiers.
- Invocation:
  Step 1: `POST /api/fagsaker/sok/fagsakdeltagere`
- Constraints:
  Body must include applicant `personIdent`; `barnasIdenter` are resolved to actor ids.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Actor lookup or participant lookup fails. This intentionally creates or omits the state described by: Invalid or unresolvable applicant/child identifiers.
  - Failure endpoint:
    `POST /api/fagsaker/sok/fagsakdeltagere`
  - Why this fails:
    The controller catches failures and returns a failure resource with the exception message.
  - Intentionally violated constraints:
    Invalid or unresolvable applicant/child identifiers.

Endpoint coverage:
- Covers:
  `POST /api/fagsaker/sok/fagsakdeltagere`
- Distinct meaning:
  Resolves participants for manual fagsak context.

### Function 11: check open repayment case

Function name:
check open repayment case

Core endpoint(s):
- `GET /api/fagsaker/{fagsakId}/har-apen-tilbakekreving`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function checks whether a fagsak has an open repayment treatment.
- Invocation:
  Step 1: `GET /api/fagsaker/{fagsakId}/har-apen-tilbakekreving`
- Constraints:
  `{fagsakId}` must come from the relevant setup response.

Failure or exceptional branches:
- None identified beyond missing fagsak lookup failures.

Endpoint coverage:
- Covers:
  `GET /api/fagsaker/{fagsakId}/har-apen-tilbakekreving`
- Distinct meaning:
  Boolean check for open repayment.

### Function 12: create repayment treatment

Function name:
create repayment treatment

Core endpoint(s):
- `GET /api/fagsaker/{fagsakId}/opprett-tilbakekreving`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function manually creates a repayment treatment for a fagsak.
- Invocation:
  Step 1: `GET /api/fagsaker/{fagsakId}/opprett-tilbakekreving`
- Constraints:
  `{fagsakId}` must come from the relevant setup response; caller must have caseworker permission.

Failure or exceptional branches:
- None identified beyond missing fagsak/access failures.

Endpoint coverage:
- Covers:
  `GET /api/fagsaker/{fagsakId}/opprett-tilbakekreving`
- Distinct meaning:
  Creates a repayment treatment.

### Function 13: create treatment

Function name:
create treatment

Core endpoint(s):
- `POST /api/behandlinger`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function creates a new treatment on a fagsak and initializes person basis and an active decision record.
- Invocation:
  Step 1: `POST /api/behandlinger`
- Constraints:
  The core request body `fagsakId` must be the id from the relevant setup response. `søkersIdent` must be the applicant for the fagsak. If `behandlingType` is `FØRSTEGANGSBEHANDLING` or `REVURDERING` with `behandlingÅrsak=SØKNAD`, `søknadMottattDato` is required. Manual migration requires `nyMigreringsdato`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The fagsak id does not exist. This intentionally creates or omits the state described by: The fagsak state normally established by `POST /api/fagsaker` was omitted or an unrelated `fagsakId` was used.
  - Failure endpoint:
    `POST /api/behandlinger`
  - Why this fails:
    `BehandlingService.opprettBehandling` throws when the fagsak cannot be found.
  - Intentionally violated constraints:
    The fagsak state normally established by `POST /api/fagsaker` was omitted or an unrelated `fagsakId` was used.
- Branch 2:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A required request field is missing for the selected treatment type/reason. This intentionally creates or omits the state described by: Missing `søkersIdent`, `nyMigreringsdato`, or `søknadMottattDato`.
  - Failure endpoint:
    `POST /api/behandlinger`
  - Why this fails:
    `NyBehandling` validates blank applicant id, missing migration date, and missing application received date.
  - Intentionally violated constraints:
    Missing `søkersIdent`, `nyMigreringsdato`, or `søknadMottattDato`.

Endpoint coverage:
- Covers:
  `POST /api/behandlinger`
- Distinct meaning:
  Creates a new treatment when no active blocking treatment exists.

### Function 14: restart active early treatment

Function name:
restart active early treatment

Core endpoint(s):
- `POST /api/behandlinger`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- An active treatment exists on the fagsak and has not reached `BESLUTTE_VEDTAK`. This can be satisfied by directly inserting an active early-stage `Behandling` linked to the fagsak, or by calling `POST /api/behandlinger` with the same `{fagsakId}` before the core request.

Successful execution:
- Result:
  This function reuses an active treatment that has not reached the decision step, resets it to the first step, and returns it.
- Invocation:
  Step 1: `POST /api/behandlinger` again for the same `fagsakId`.
- Constraints:
  The active treatment established by the treatment setup endpoint must still be before `BESLUTTE_VEDTAK`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - An active treatment exists on that fagsak and has been advanced to the decision step or later. This can be satisfied by directly setting the persisted treatment step state at or after `BESLUTTE_VEDTAK`, or by creating the treatment with `POST /api/behandlinger` and invoking the required step endpoints until the decision stage is reached.
    - The fagsak already has an active treatment at or after the decision step. This intentionally creates or omits the state described by: Attempted to create/restart while an active decision-stage treatment exists.
  - Failure endpoint:
    `POST /api/behandlinger`
  - Why this fails:
    The implementation throws a functional error when an active unfinished treatment is at or after the decision step.
  - Intentionally violated constraints:
    Attempted to create/restart while an active decision-stage treatment exists.

Endpoint coverage:
- Covers:
  `POST /api/behandlinger`
- Distinct meaning:
  Reinitializes an existing active early-stage treatment instead of creating a new treatment.

### Function 15: queue treatment from birth event

Function name:
queue treatment from birth event

Core endpoint(s):
- `PUT /api/behandlinger`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function creates a background task to process a birth event.
- Invocation:
  Step 1: `PUT /api/behandlinger`
- Constraints:
  Body must include `morsIdent` and `barnasIdenter`. The endpoint creates a task, not an immediate treatment.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Task creation fails. This intentionally creates or omits the state described by: Invalid event payload or task repository failure.
  - Failure endpoint:
    `PUT /api/behandlinger`
  - Why this fails:
    The controller catches task creation errors and returns an illegal state resource.
  - Intentionally violated constraints:
    Invalid event payload or task repository failure.

Endpoint coverage:
- Covers:
  `PUT /api/behandlinger`
- Distinct meaning:
  Queues automated birth-event handling.

### Function 16: retrieve treatment

Function name:
retrieve treatment

Core endpoint(s):
- `GET /api/behandlinger/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function retrieves the extended treatment view.
- Invocation:
  Step 1: `GET /api/behandlinger/{behandlingId}`
- Constraints:
  `{behandlingId}` must come from the treatment setup response as `RestUtvidetBehandling.behandlingId`.

Failure or exceptional branches:
- None identified beyond missing treatment/access failures.

Endpoint coverage:
- Covers:
  `GET /api/behandlinger/{behandlingId}`
- Distinct meaning:
  Reads extended treatment details.

### Function 17: change treatment theme

Function name:
change treatment theme

Core endpoint(s):
- `PUT /api/behandlinger/{behandlingId}/behandlingstema`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function changes the treatment category and subcategory.
- Invocation:
  Step 1: `PUT /api/behandlinger/{behandlingId}/behandlingstema`
- Constraints:
  The core request body must include `behandlingKategori` and `behandlingUnderkategori`; treatment must be editable.

Failure or exceptional branches:
- None identified beyond non-editable treatment/access failures.

Endpoint coverage:
- Covers:
  `PUT /api/behandlinger/{behandlingId}/behandlingstema`
- Distinct meaning:
  Updates treatment theme.

### Function 18: find invalid after-payment periods

Function name:
find invalid after-payment periods

Core endpoint(s):
- `GET /api/behandlinger/{behandlingId}/personer-med-ugyldig-etterbetalingsperiode`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function returns person identifiers with invalid after-payment periods in the treatment.
- Invocation:
  Step 1: `GET /api/behandlinger/{behandlingId}/personer-med-ugyldig-etterbetalingsperiode`
- Constraints:
  `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- None identified beyond missing treatment/access failures.

Endpoint coverage:
- Covers:
  `GET /api/behandlinger/{behandlingId}/personer-med-ugyldig-etterbetalingsperiode`
- Distinct meaning:
  Validates after-payment periods.

### Function 19: get change date

Function name:
get change date

Core endpoint(s):
- `GET /api/behandlinger/{behandlingId}/endringstidspunkt`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function retrieves the overridden change date if set, otherwise the calculated change date for the treatment.
- Invocation:
  Step 1: `GET /api/behandlinger/{behandlingId}/endringstidspunkt`
- Constraints:
  `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- None identified beyond missing treatment failures.

Endpoint coverage:
- Covers:
  `GET /api/behandlinger/{behandlingId}/endringstidspunkt`
- Distinct meaning:
  Reads treatment change date.

### Function 20: register application step

Function name:
register application

Core endpoint(s):
- `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function stores application data for a treatment and advances the step flow.
- Invocation:
  Step 1: `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`
- Constraints:
  The core request body must be `RestRegistrerSøknad`. Treatment must be on or allowed to execute the application-registration step, not on wait, and not closed.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - The treatment has registered application data and has later been advanced beyond the application-registration step. This can be satisfied by directly storing the application basis and later step state, or by calling `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad` and then invoking a later step endpoint before retrying registration.
    - Treatment is on a later step that cannot execute this step. This intentionally creates or omits the state described by: Required step ordering.
  - Failure endpoint:
    `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`
  - Why this fails:
    `StegService` rejects executing a caseworker step that is earlier than the current treatment step.
  - Intentionally violated constraints:
    Required step ordering.

Endpoint coverage:
- Covers:
  `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`
- Distinct meaning:
  Completes the application-registration step.

### Function 21: validate conditions step

Function name:
validate conditions

Core endpoint(s):
- `POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The treatment has registered application data and has advanced past the application-registration step. This can be satisfied by directly storing the application basis and setting the treatment step state past application registration, or by calling `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`.

Successful execution:
- Result:
  This function validates the active condition assessment and advances the treatment.
- Invocation:
  Step 1: `POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering`
- Constraints:
  The treatment must have completed the required prior step and must not be on wait or closed.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - An active wait state exists for the treatment. This can be satisfied by directly inserting an active wait record linked to the treatment and setting the treatment status to waiting, or by calling `POST /api/sett-på-vent/{behandlingId}`.
    - Treatment is set on wait. This intentionally creates or omits the state described by: Treatment status is `SATT_PÅ_VENT`.
  - Failure endpoint:
    `POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering`
  - Why this fails:
    `StegService.validerBehandlingIkkeSattPåVent` rejects step execution while waiting.
  - Intentionally violated constraints:
    Treatment status is `SATT_PÅ_VENT`.

Endpoint coverage:
- Covers:
  `POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering`
- Distinct meaning:
  Completes condition-assessment validation.

### Function 22: validate treatment result

Function name:
validate treatment result

Core endpoint(s):
- `GET /api/behandlinger/{behandlingId}/steg/behandlingsresultat/valider`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The treatment has registered application data and has advanced past the application-registration step. This can be satisfied by directly storing the application basis and setting the treatment step state past application registration, or by calling `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`.
- The treatment has a valid condition assessment and has advanced past the condition-assessment step. This can be satisfied by directly storing a valid condition assessment and setting the treatment step state past condition assessment, or by calling `POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering`.

Successful execution:
- Result:
  This function pre-validates that treatment result derivation can be performed.
- Invocation:
  Step 1: `GET /api/behandlinger/{behandlingId}/steg/behandlingsresultat/valider`
- Constraints:
  Treatment must be ready for the treatment-result step.

Failure or exceptional branches:
- None identified beyond step pre-validation failures.

Endpoint coverage:
- Covers:
  `GET /api/behandlinger/{behandlingId}/steg/behandlingsresultat/valider`
- Distinct meaning:
  Validates result derivation without advancing the step.

### Function 23: derive treatment result

Function name:
derive treatment result

Core endpoint(s):
- `POST /api/behandlinger/{behandlingId}/steg/behandlingsresultat`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The treatment has registered application data and has advanced past the application-registration step. This can be satisfied by directly storing the application basis and setting the treatment step state past application registration, or by calling `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`.
- The treatment has a valid condition assessment and has advanced past the condition-assessment step. This can be satisfied by directly storing a valid condition assessment and setting the treatment step state past condition assessment, or by calling `POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering`.

Successful execution:
- Result:
  This function derives and stores the treatment result, then advances the step flow.
- Invocation:
  Step 1: `POST /api/behandlinger/{behandlingId}/steg/behandlingsresultat`
- Constraints:
  Prior steps must be complete; treatment must be editable and not on wait.

Failure or exceptional branches:
- None identified beyond step validation failures.

Endpoint coverage:
- Covers:
  `POST /api/behandlinger/{behandlingId}/steg/behandlingsresultat`
- Distinct meaning:
  Completes treatment-result step.

### Function 24: assess repayment in treatment flow

Function name:
assess repayment

Core endpoint(s):
- `POST /api/behandlinger/{behandlingId}/steg/tilbakekreving`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The treatment has registered application data and has advanced past the application-registration step. This can be satisfied by directly storing the application basis and setting the treatment step state past application registration, or by calling `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`.
- The treatment has a valid condition assessment and has advanced past the condition-assessment step. This can be satisfied by directly storing a valid condition assessment and setting the treatment step state past condition assessment, or by calling `POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering`.
- The treatment result has been derived and stored for the treatment. This can be satisfied by directly storing the derived treatment result and decision-period state, or by calling `POST /api/behandlinger/{behandlingId}/steg/behandlingsresultat`.

Successful execution:
- Result:
  This function stores repayment assessment data and advances from the repayment step.
- Invocation:
  Step 1: `POST /api/behandlinger/{behandlingId}/steg/tilbakekreving`
- Constraints:
  The core request body may be `RestTilbakekreving` or null; treatment must be on the repayment assessment step.

Failure or exceptional branches:
- None identified beyond step validation failures.

Endpoint coverage:
- Covers:
  `POST /api/behandlinger/{behandlingId}/steg/tilbakekreving`
- Distinct meaning:
  Completes repayment assessment in the treatment workflow.

### Function 25: send treatment to decision maker

Function name:
send to decision maker

Core endpoint(s):
- `POST /api/behandlinger/{behandlingId}/steg/send-til-beslutter`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The treatment has registered application data and has advanced past the application-registration step. This can be satisfied by directly storing the application basis and setting the treatment step state past application registration, or by calling `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`.
- The treatment has a valid condition assessment and has advanced past the condition-assessment step. This can be satisfied by directly storing a valid condition assessment and setting the treatment step state past condition assessment, or by calling `POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering`.
- The treatment result has been derived and stored for the treatment. This can be satisfied by directly storing the derived treatment result and decision-period state, or by calling `POST /api/behandlinger/{behandlingId}/steg/behandlingsresultat`.
- The repayment assessment state has been stored for the treatment. This can be satisfied by directly storing the repayment assessment state for the treatment, or by calling `POST /api/behandlinger/{behandlingId}/steg/tilbakekreving`.

Successful execution:
- Result:
  This function sends a treatment to a decision maker and may automatically decide it when automatic decision rules apply.
- Invocation:
  Step 1: `POST /api/behandlinger/{behandlingId}/steg/send-til-beslutter`
- Constraints:
  The core request requires query `behandlendeEnhet`. Prior workflow steps must be complete.

Failure or exceptional branches:
- None identified beyond step validation failures.

Endpoint coverage:
- Covers:
  `POST /api/behandlinger/{behandlingId}/steg/send-til-beslutter`
- Distinct meaning:
  Sends the treatment for decision.

### Function 26: decide and implement treatment

Function name:
decide treatment

Core endpoint(s):
- `POST /api/behandlinger/{behandlingId}/steg/iverksett-vedtak`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The treatment has registered application data and has advanced past the application-registration step. This can be satisfied by directly storing the application basis and setting the treatment step state past application registration, or by calling `POST /api/behandlinger/{behandlingId}/steg/registrer-søknad`.
- The treatment has a valid condition assessment and has advanced past the condition-assessment step. This can be satisfied by directly storing a valid condition assessment and setting the treatment step state past condition assessment, or by calling `POST /api/behandlinger/{behandlingId}/steg/vilkårsvurdering`.
- The treatment result has been derived and stored for the treatment. This can be satisfied by directly storing the derived treatment result and decision-period state, or by calling `POST /api/behandlinger/{behandlingId}/steg/behandlingsresultat`.
- The repayment assessment state has been stored for the treatment. This can be satisfied by directly storing the repayment assessment state for the treatment, or by calling `POST /api/behandlinger/{behandlingId}/steg/tilbakekreving`.
- The treatment has been sent to a decision maker and has the decision-stage state required by the core endpoint. This can be satisfied by directly setting the treatment as sent to the decision maker with the required decision state, or by calling `POST /api/behandlinger/{behandlingId}/steg/send-til-beslutter`.

Successful execution:
- Result:
  This function records the decision maker’s decision and advances implementation.
- Invocation:
  Step 1: `POST /api/behandlinger/{behandlingId}/steg/iverksett-vedtak`
- Constraints:
  The core request body must be `RestBeslutningPåVedtak`. Caller must have decision-maker role except special manual migration logic visible in `StegService`.

Failure or exceptional branches:
- None identified beyond step validation and decision validation failures.

Endpoint coverage:
- Covers:
  `POST /api/behandlinger/{behandlingId}/steg/iverksett-vedtak`
- Distinct meaning:
  Performs the decision step.

### Function 27: dismiss treatment

Function name:
dismiss treatment

Core endpoint(s):
- `PUT /api/behandlinger/{behandlingId}/steg/henlegg`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function dismisses an active treatment and sends any required dismissal letter.
- Invocation:
  Step 1: `PUT /api/behandlinger/{behandlingId}/steg/henlegg`
- Constraints:
  Body must include `årsak` and `begrunnelse`. Treatment must not already have been sent to external services. Technical maintenance dismissal requires the relevant feature toggle; technical treatment dismissal requires the technical-change toggle.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - The treatment has already been advanced or sent to an external flow before dismissal is attempted. This can be satisfied by direct database setup that places the treatment in the same state, or by invoking the relevant endpoint flow with matching ids and scope values.
    - Treatment has been sent to external services or the dismissal type is not allowed. This intentionally creates or omits the state described by: Attempted dismissal after external sending or with a disallowed `årsak`.
  - Failure endpoint:
    `PUT /api/behandlinger/{behandlingId}/steg/henlegg`
  - Why this fails:
    Controller validation calls `validerBehandlingIkkeSendtTilEksterneTjenester` and dismissal-type validation before invoking the step.
  - Intentionally violated constraints:
    Attempted dismissal after external sending or with a disallowed `årsak`.

Endpoint coverage:
- Covers:
  `PUT /api/behandlinger/{behandlingId}/steg/henlegg`
- Distinct meaning:
  Dismisses the treatment.

### Function 28: register institution and guardian

Function name:
register institution and guardian

Core endpoint(s):
- `POST /api/behandlinger/{behandlingId}/steg/registrer-institusjon-og-verge`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function stores institution and/or guardian information for a treatment step.
- Invocation:
  Step 1: `POST /api/behandlinger/{behandlingId}/steg/registrer-institusjon-og-verge`
- Constraints:
  Body must produce either a non-null institution or guardian object when converted by `RestRegistrerInstitusjonOgVerge`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Request contains neither valid institution data nor valid guardian data. This intentionally creates or omits the state described by: Both converted institution and guardian are null.
  - Failure endpoint:
    `POST /api/behandlinger/{behandlingId}/steg/registrer-institusjon-og-verge`
  - Why this fails:
    The controller returns a failure resource when guardian information is invalid.
  - Intentionally violated constraints:
    Both converted institution and guardian are null.

Endpoint coverage:
- Covers:
  `POST /api/behandlinger/{behandlingId}/steg/registrer-institusjon-og-verge`
- Distinct meaning:
  Completes the institution/guardian registration step.

### Function 29: add child to treatment basis

Function name:
add child to basis

Core endpoint(s):
- `POST /api/behandlinger/{behandlingId}/legg-til-barn`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function adds a child to the treatment person basis and resets the treatment to condition assessment.
- Invocation:
  Step 1: `POST /api/behandlinger/{behandlingId}/legg-til-barn`
- Constraints:
  The core request body must include `barnIdent`. Treatment must be editable.

Failure or exceptional branches:
- None identified beyond non-editable treatment/person lookup failures.

Endpoint coverage:
- Covers:
  `POST /api/behandlinger/{behandlingId}/legg-til-barn`
- Distinct meaning:
  Adds a child to person basis.

### Function 30: set treatment on wait

Function name:
set treatment on wait

Core endpoint(s):
- `POST /api/sett-på-vent/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function marks an active treatment as waiting and extends open task deadlines.
- Invocation:
  Step 1: `POST /api/sett-på-vent/{behandlingId}`
- Constraints:
  Body must include future/current `frist` and `årsak`. Treatment status must be `UTREDES`, active, and not already on wait.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Treatment is already on wait. This intentionally creates or omits the state described by: Duplicate active wait record.
  - Failure endpoint:
    `POST /api/sett-på-vent/{behandlingId}`
  - Why this fails:
    `validerBehandlingKanSettesPåVent` rejects an existing active wait record.
  - Intentionally violated constraints:
    Duplicate active wait record.
- Branch 2:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Deadline is before the current date. This intentionally creates or omits the state described by: `frist` is before today.
  - Failure endpoint:
    `POST /api/sett-på-vent/{behandlingId}`
  - Why this fails:
    `validerFristErFremITiden` rejects past deadlines.
  - Intentionally violated constraints:
    `frist` is before today.

Endpoint coverage:
- Covers:
  `POST /api/sett-på-vent/{behandlingId}`
- Distinct meaning:
  Places a treatment on wait.

### Function 31: update wait

Function name:
update wait

Core endpoint(s):
- `PUT /api/sett-på-vent/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- An active wait state exists for the treatment. This can be satisfied by directly inserting an active wait record linked to the treatment and setting the treatment status to waiting, or by calling `POST /api/sett-på-vent/{behandlingId}`.

Successful execution:
- Result:
  This function changes the active wait deadline and/or reason.
- Invocation:
  Step 1: `PUT /api/sett-på-vent/{behandlingId}`
- Constraints:
  The core request body must change `frist` or `årsak`; new `frist` must not be in the past.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Treatment is not currently on wait. This intentionally creates or omits the state described by: The required state normally established by `POST /api/sett-på-vent/{behandlingId}` was omitted.
  - Failure endpoint:
    `PUT /api/sett-på-vent/{behandlingId}`
  - Why this fails:
    The service throws when the treatment is not currently on wait.
  - Intentionally violated constraints:
    The required state normally established by `POST /api/sett-på-vent/{behandlingId}` was omitted.
- Branch 2:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - An active wait state exists for the treatment. This can be satisfied by directly inserting an active wait record linked to the treatment and setting the treatment status to waiting, or by calling `POST /api/sett-på-vent/{behandlingId}`.
    - New wait values equal the existing wait values. This intentionally creates or omits the state described by: Same `frist` and same `årsak`.
  - Failure endpoint:
    `PUT /api/sett-på-vent/{behandlingId}`
  - Why this fails:
    The service rejects updates that do not actually change deadline or reason.
  - Intentionally violated constraints:
    Same `frist` and same `årsak`.

Endpoint coverage:
- Covers:
  `PUT /api/sett-på-vent/{behandlingId}`
- Distinct meaning:
  Updates active wait metadata.

### Function 32: resume treatment from wait

Function name:
resume treatment

Core endpoint(s):
- `PUT /api/sett-på-vent/{behandlingId}/fortsettbehandling`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- An active wait state exists for the treatment. This can be satisfied by directly inserting an active wait record linked to the treatment and setting the treatment status to waiting, or by calling `POST /api/sett-på-vent/{behandlingId}`.

Successful execution:
- Result:
  This function takes a treatment off wait, deactivates the wait record, and sets treatment status back to investigation.
- Invocation:
  Step 1: `PUT /api/sett-på-vent/{behandlingId}/fortsettbehandling`
- Constraints:
  Treatment status must be `SATT_PÅ_VENT`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Treatment is not on wait. This intentionally creates or omits the state described by: The required setup wait endpoint was omitted.
  - Failure endpoint:
    `PUT /api/sett-på-vent/{behandlingId}/fortsettbehandling`
  - Why this fails:
    `gjenopptaBehandling` requires an active wait record and status `SATT_PÅ_VENT`.
  - Intentionally violated constraints:
    The required setup wait endpoint was omitted.

Endpoint coverage:
- Covers:
  `PUT /api/sett-på-vent/{behandlingId}/fortsettbehandling`
- Distinct meaning:
  Resumes a waiting treatment.

### Function 33: retrieve full person information

Function name:
retrieve full person information

Core endpoint(s):
- `GET /api/person`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function retrieves person information with relations and register information, or masked information if access is missing.
- Invocation:
  Step 1: `GET /api/person`
- Constraints:
  Header `personIdent` is used by the implementation. The OpenAPI also exposes `personIdentBody`, but the source logic does not use it.

Failure or exceptional branches:
- None identified beyond person lookup/upstream failures.

Endpoint coverage:
- Covers:
  `GET /api/person`
- Distinct meaning:
  Reads full person details.

### Function 34: retrieve simple person information

Function name:
retrieve simple person information

Core endpoint(s):
- `GET /api/person/enkel`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function retrieves simpler person information, or masked information if access is missing.
- Invocation:
  Step 1: `GET /api/person/enkel`
- Constraints:
  Header `personIdent` is used by the implementation.

Failure or exceptional branches:
- None identified beyond person lookup/upstream failures.

Endpoint coverage:
- Covers:
  `GET /api/person/enkel`
- Distinct meaning:
  Reads simple person details.

### Function 35: retrieve person address

Function name:
retrieve person address

Core endpoint(s):
- `GET /api/person/adresse`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function retrieves person name and address, or masked information if access is missing.
- Invocation:
  Step 1: `GET /api/person/adresse`
- Constraints:
  Header `personIdent` is required.

Failure or exceptional branches:
- None identified beyond person lookup/upstream failures.

Endpoint coverage:
- Covers:
  `GET /api/person/adresse`
- Distinct meaning:
  Reads person address information.

### Function 36: refresh register information

Function name:
refresh register information

Core endpoint(s):
- `GET /api/person/oppdater-registeropplysninger/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function refreshes register information on a treatment and returns the updated treatment.
- Invocation:
  Step 1: `GET /api/person/oppdater-registeropplysninger/{behandlingId}`
- Constraints:
  `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- None identified beyond missing treatment/access failures.

Endpoint coverage:
- Covers:
  `GET /api/person/oppdater-registeropplysninger/{behandlingId}`
- Distinct meaning:
  Updates treatment register information from external person data.

### Function 37: register manual death

Function name:
register manual death

Core endpoint(s):
- `POST /api/person/registrer-manuell-dodsfall/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function manually records death information for a person in the treatment basis.
- Invocation:
  Step 1: `POST /api/person/registrer-manuell-dodsfall/{behandlingId}`
- Constraints:
  Body must include `personIdent`, `dødsfallDato`, and `begrunnelse`; person must belong to the treatment context.

Failure or exceptional branches:
- None identified beyond treatment/person validation failures.

Endpoint coverage:
- Covers:
  `POST /api/person/registrer-manuell-dodsfall/{behandlingId}`
- Distinct meaning:
  Adds manual death data to a treatment.

### Function 38: add condition

Function name:
add condition

Core endpoint(s):
- `POST /api/vilkaarsvurdering/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function adds a new condition to the condition assessment and resets later steps.
- Invocation:
  Step 1: `POST /api/vilkaarsvurdering/{behandlingId}`
- Constraints:
  Body must be `RestNyttVilkår`. Treatment must be editable.

Failure or exceptional branches:
- None identified beyond non-editable treatment/condition validation failures.

Endpoint coverage:
- Covers:
  `POST /api/vilkaarsvurdering/{behandlingId}`
- Distinct meaning:
  Adds a condition.

### Function 39: update condition

Function name:
update condition

Core endpoint(s):
- `PUT /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function updates an existing condition result and resets later steps.
- Invocation:
  Step 1: `PUT /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}`
- Constraints:
  `{vilkaarId}` must be one of the condition ids returned in `RestUtvidetBehandling.personResultater` from the treatment setup response or a later treatment read. Body must be `RestPersonResultat`.

Failure or exceptional branches:
- None identified beyond missing condition/editability failures.

Endpoint coverage:
- Covers:
  `PUT /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}`
- Distinct meaning:
  Updates an existing condition result.

### Function 40: delete condition period

Function name:
delete condition period

Core endpoint(s):
- `DELETE /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function deletes one condition period for a person and resets later steps.
- Invocation:
  Step 1: `DELETE /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}`
- Constraints:
  `{vilkaarId}` must identify a condition on the treatment. Request body is the person identifier whose actor owns the condition period.

Failure or exceptional branches:
- None identified beyond missing condition/person/editability failures.

Endpoint coverage:
- Covers:
  `DELETE /api/vilkaarsvurdering/{behandlingId}/{vilkaarId}`
- Distinct meaning:
  Deletes one condition period.

### Function 41: delete condition

Function name:
delete condition

Core endpoint(s):
- `DELETE /api/vilkaarsvurdering/{behandlingId}/vilkaar`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function deletes a condition according to the request payload and resets later steps.
- Invocation:
  Step 1: `DELETE /api/vilkaarsvurdering/{behandlingId}/vilkaar`
- Constraints:
  Body must be `RestSlettVilkår`; treatment must be editable.

Failure or exceptional branches:
- None identified beyond condition/editability validation failures.

Endpoint coverage:
- Covers:
  `DELETE /api/vilkaarsvurdering/{behandlingId}/vilkaar`
- Distinct meaning:
  Deletes a condition from the assessment.

### Function 42: update other assessment

Function name:
update other assessment

Core endpoint(s):
- `PUT /api/vilkaarsvurdering/{behandlingId}/annenvurdering/{annenVurderingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function updates another-assessment record for the treatment.
- Invocation:
  Step 1: `PUT /api/vilkaarsvurdering/{behandlingId}/annenvurdering/{annenVurderingId}`
- Constraints:
  `{annenVurderingId}` must identify an existing other-assessment record for the treatment. Body must be `RestAnnenVurdering`.

Failure or exceptional branches:
- None identified beyond missing assessment/editability failures.

Endpoint coverage:
- Covers:
  `PUT /api/vilkaarsvurdering/{behandlingId}/annenvurdering/{annenVurderingId}`
- Distinct meaning:
  Updates another assessment record.

### Function 43: list condition explanation texts

Function name:
list condition explanation texts

Core endpoint(s):
- `GET /api/vilkaarsvurdering/vilkaarsbegrunnelser`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns available decision explanation texts linked to conditions.
- Invocation:
  Step 1: `GET /api/vilkaarsvurdering/vilkaarsbegrunnelser`
- Constraints:
  No case-specific prerequisite is required.

Failure or exceptional branches:
- None identified in project source.

Endpoint coverage:
- Covers:
  `GET /api/vilkaarsvurdering/vilkaarsbegrunnelser`
- Distinct meaning:
  Lists condition explanation metadata.

### Function 44: create or replace competence interval

Function name:
upsert competence interval

Core endpoint(s):
- `PUT /api/kompetanse/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function creates, replaces, splits, or merges competence periods for a treatment based on period and children.
- Invocation:
  Step 1: `PUT /api/kompetanse/{behandlingId}`
- Constraints:
  Body must include `fom`, at least one `barnIdenter`, and activity/result fields as required by the business validation. `fom` must not be after `tom`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Missing `fom`, empty children, or `fom > tom`. This intentionally creates or omits the state described by: Invalid `RestKompetanse` period or child list.
  - Failure endpoint:
    `PUT /api/kompetanse/{behandlingId}`
  - Why this fails:
    `KompetanseController.validerOppdatering` rejects invalid period and missing children.
  - Intentionally violated constraints:
    Invalid `RestKompetanse` period or child list.
- Branch 2:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Activity value is invalid for whether the other parent is covered by Norwegian legislation. This intentionally creates or omits the state described by: Incompatible `søkersAktivitet` or `annenForeldersAktivitet`.
  - Failure endpoint:
    `PUT /api/kompetanse/{behandlingId}`
  - Why this fails:
    Controller validation rejects incompatible activity combinations.
  - Intentionally violated constraints:
    Incompatible `søkersAktivitet` or `annenForeldersAktivitet`.

Endpoint coverage:
- Covers:
  `PUT /api/kompetanse/{behandlingId}`
- Distinct meaning:
  Upserts competence periods.

### Function 45: delete competence interval

Function name:
delete competence interval

Core endpoint(s):
- `DELETE /api/kompetanse/{behandlingId}/{kompetanseId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- A competence interval exists for the treatment. This can be satisfied by directly inserting the competence period row linked to the treatment and children, or by calling `PUT /api/kompetanse/{behandlingId}`. The generated {kompetanseId} from the treatment view must be reused by the core endpoint when required.

Successful execution:
- Result:
  This function removes the content of an existing competence period for the treatment and recalculates the period set.
- Invocation:
  Step 1: `DELETE /api/kompetanse/{behandlingId}/{kompetanseId}`
- Constraints:
  `{kompetanseId}` must be obtained from the treatment response after the setup endpoint or a later `GET /api/behandlinger/{behandlingId}`. The competence record must belong to `{behandlingId}`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - A competence interval exists for the treatment. This can be satisfied by directly inserting the competence period row linked to the treatment and children, or by calling `PUT /api/kompetanse/{behandlingId}`. The generated {kompetanseId} from the treatment view must be reused by the core endpoint when required.
    - The state described by "create another treatment" already exists before the core endpoint is invoked. This can be satisfied by direct database setup that places the treatment or resource in the same state, or by invoking the relevant endpoint flow with matching ids and scope values.
    - The competence id belongs to another treatment. This intentionally creates or omits the state described by: Mismatched treatment id and competence id.
  - Failure endpoint:
    `DELETE /api/kompetanse/{behandlingId}/{kompetanseId}`
  - Why this fails:
    Shared `PeriodeOgBarnSkjemaService.slettSkjema` rejects deleting a schema not connected to the submitted treatment.
  - Intentionally violated constraints:
    Mismatched treatment id and competence id.

Endpoint coverage:
- Covers:
  `DELETE /api/kompetanse/{behandlingId}/{kompetanseId}`
- Distinct meaning:
  Deletes one competence interval.

### Function 46: update foreign period amount

Function name:
update foreign period amount

Core endpoint(s):
- `PUT /api/differanseberegning/utenlandskperidebeløp/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function updates foreign period amount data and recalculates monthly amount from amount and interval.
- Invocation:
  Step 1: `PUT /api/differanseberegning/utenlandskperidebeløp/{behandlingId}`
- Constraints:
  Body must be `RestUtenlandskPeriodebeløp`; `id` must identify an existing period amount because the implementation loads it to preserve `utbetalingsland`. `beløp` must not be negative.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Negative amount. This intentionally creates or omits the state described by: `beløp < 0`.
  - Failure endpoint:
    `PUT /api/differanseberegning/utenlandskperidebeløp/{behandlingId}`
  - Why this fails:
    `RestUtenlandskPeriodebeløp.beløp` has a decimal-min validation requiring a non-negative amount.
  - Intentionally violated constraints:
    `beløp < 0`.

Endpoint coverage:
- Covers:
  `PUT /api/differanseberegning/utenlandskperidebeløp/{behandlingId}`
- Distinct meaning:
  Updates foreign amount periods.

### Function 47: delete foreign period amount

Function name:
delete foreign period amount

Core endpoint(s):
- `DELETE /api/differanseberegning/utenlandskperidebeløp/{behandlingId}/{utenlandskPeriodebeløpId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- A foreign period amount exists for the treatment. This can be satisfied by directly inserting the foreign period amount row linked to the treatment and children, or by calling `PUT /api/differanseberegning/utenlandskperidebeløp/{behandlingId}`. The generated {utenlandskPeriodebeløpId} from the treatment view must be reused by the core endpoint when required.

Successful execution:
- Result:
  This function deletes a foreign period amount interval from a treatment.
- Invocation:
  Step 1: `DELETE /api/differanseberegning/utenlandskperidebeløp/{behandlingId}/{utenlandskPeriodebeløpId}`
- Constraints:
  `{utenlandskPeriodebeløpId}` must be obtained from the treatment response after the update/read and must belong to `{behandlingId}`.

Failure or exceptional branches:
- None identified beyond id/treatment mismatch handled by shared schema deletion.

Endpoint coverage:
- Covers:
  `DELETE /api/differanseberegning/utenlandskperidebeløp/{behandlingId}/{utenlandskPeriodebeløpId}`
- Distinct meaning:
  Deletes one foreign period amount.

### Function 48: update currency rate from ECB

Function name:
update currency rate from ECB

Core endpoint(s):
- `PUT /api/differanseberegning/valutakurs/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function updates a currency-rate period and fetches a new exchange rate from ECB when currency code or rate date changes.
- Invocation:
  Step 1: `PUT /api/differanseberegning/valutakurs/{behandlingId}`
- Constraints:
  Body must include existing `id`, `valutakode`, and `valutakursdato` when a rate should be fetched. This branch applies unless `valutakode=ISK` and `valutakursdato` is before `2018-02-01`.

Failure or exceptional branches:
- None identified beyond missing id, external ECB failure, or schema validation.

Endpoint coverage:
- Covers:
  `PUT /api/differanseberegning/valutakurs/{behandlingId}`
- Distinct meaning:
  Updates a currency-rate period using ECB.

### Function 49: set historical ISK currency rate manually

Function name:
set historical ISK rate manually

Core endpoint(s):
- `PUT /api/differanseberegning/valutakurs/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function stores a manually supplied rate for Icelandic krona before 2018-02-01 instead of fetching ECB data.
- Invocation:
  Step 1: `PUT /api/differanseberegning/valutakurs/{behandlingId}`
- Constraints:
  Body must have `valutakode=ISK`, `valutakursdato` before `2018-02-01`, and `kurs` supplied manually.

Failure or exceptional branches:
- None identified beyond schema/treatment validation failures.

Endpoint coverage:
- Covers:
  `PUT /api/differanseberegning/valutakurs/{behandlingId}`
- Distinct meaning:
  Manual historical ISK rate branch.

### Function 50: delete currency rate

Function name:
delete currency rate

Core endpoint(s):
- `DELETE /api/differanseberegning/valutakurs/{behandlingId}/{valutakursId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- A currency-rate period exists for the treatment. This can be satisfied by directly inserting the currency-rate row linked to the treatment and foreign amount period, or by calling `PUT /api/differanseberegning/valutakurs/{behandlingId}`. The generated {valutakursId} from the treatment view must be reused by the core endpoint when required.

Successful execution:
- Result:
  This function deletes a currency-rate period from a treatment.
- Invocation:
  Step 1: `DELETE /api/differanseberegning/valutakurs/{behandlingId}/{valutakursId}`
- Constraints:
  `{valutakursId}` must be obtained from the treatment response and belong to `{behandlingId}`.

Failure or exceptional branches:
- None identified beyond id/treatment mismatch handled by shared schema deletion.

Endpoint coverage:
- Covers:
  `DELETE /api/differanseberegning/valutakurs/{behandlingId}/{valutakursId}`
- Distinct meaning:
  Deletes a currency-rate period.

### Function 51: create changed payment share

Function name:
create changed payment share

Core endpoint(s):
- `POST /api/endretutbetalingandel/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function creates an empty changed-payment share and resets the treatment to treatment-result state.
- Invocation:
  Step 1: `POST /api/endretutbetalingandel/{behandlingId}`
- Constraints:
  Treatment must be editable. The created id is returned in `RestUtvidetBehandling.endretUtbetalingAndeler`.

Failure or exceptional branches:
- None identified beyond non-editable treatment failures.

Endpoint coverage:
- Covers:
  `POST /api/endretutbetalingandel/{behandlingId}`
- Distinct meaning:
  Creates an empty changed-payment share.

### Function 52: update changed payment share

Function name:
update changed payment share

Core endpoint(s):
- `PUT /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- A changed-payment share exists for the treatment. This can be satisfied by directly inserting the changed-payment-share row linked to the treatment, or by calling `POST /api/endretutbetalingandel/{behandlingId}`. The generated {endretUtbetalingAndelId} from the response or treatment view must be reused by the core endpoint.

Successful execution:
- Result:
  This function fills or updates a changed-payment share, recalculates benefits, and resets treatment result.
- Invocation:
  Step 1: `PUT /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`
- Constraints:
  `{endretUtbetalingAndelId}` must be obtained from the setup endpoint response. Body `personIdent` must identify a person on the treatment. Period must be within granted benefit and must not overlap incompatible changed-payment shares.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - A changed-payment share exists for the treatment. This can be satisfied by directly inserting the changed-payment-share row linked to the treatment, or by calling `POST /api/endretutbetalingandel/{behandlingId}`. The generated {endretUtbetalingAndelId} from the response or treatment view must be reused by the core endpoint.
    - Person is not present in treatment basis. This intentionally creates or omits the state described by: `personIdent` does not belong to the treatment.
  - Failure endpoint:
    `PUT /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`
  - Why this fails:
    The service resolves `restEndretUtbetalingAndel.personIdent` against treatment persons.
  - Intentionally violated constraints:
    `personIdent` does not belong to the treatment.

Endpoint coverage:
- Covers:
  `PUT /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`
- Distinct meaning:
  Updates a changed-payment share.

### Function 53: delete changed payment share

Function name:
delete changed payment share

Core endpoint(s):
- `DELETE /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- A changed-payment share exists for the treatment. This can be satisfied by directly inserting the changed-payment-share row linked to the treatment, or by calling `POST /api/endretutbetalingandel/{behandlingId}`. The generated {endretUtbetalingAndelId} from the response or treatment view must be reused by the core endpoint.

Successful execution:
- Result:
  This function deletes a changed-payment share, recalculates benefits, and resets treatment result.
- Invocation:
  Step 1: `DELETE /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`
- Constraints:
  `{endretUtbetalingAndelId}` must be obtained from the setup endpoint response.

Failure or exceptional branches:
- None identified beyond missing id/treatment calculation failures.

Endpoint coverage:
- Covers:
  `DELETE /api/endretutbetalingandel/{behandlingId}/{endretUtbetalingAndelId}`
- Distinct meaning:
  Deletes a changed-payment share.

### Function 54: reset treatment to treatment result

Function name:
reset treatment to treatment result

Core endpoint(s):
- `POST /api/endretutbetalingandel/{behandlingId}/tilbakestill`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function resets the treatment to the treatment-result step after payment-share changes.
- Invocation:
  Step 1: `POST /api/endretutbetalingandel/{behandlingId}/tilbakestill`
- Constraints:
  Treatment must be editable.

Failure or exceptional branches:
- None identified beyond non-editable treatment failures.

Endpoint coverage:
- Covers:
  `POST /api/endretutbetalingandel/{behandlingId}/tilbakestill`
- Distinct meaning:
  Explicit treatment-result reset.

### Function 55: retrieve EØS timelines

Function name:
retrieve EØS timelines

Core endpoint(s):
- `GET /api/tidslinjer/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function returns calculated timelines for an EØS treatment.
- Invocation:
  Step 1: `GET /api/tidslinjer/{behandlingId}`
- Constraints:
  `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- None identified beyond missing timeline/treatment failures.

Endpoint coverage:
- Covers:
  `GET /api/tidslinjer/{behandlingId}`
- Distinct meaning:
  Reads EØS timeline data.

### Function 56: add EØS refund period

Function name:
add EØS refund period

Core endpoint(s):
- `POST /api/refusjon-eøs/behandlinger/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function adds an EØS refund period to a treatment.
- Invocation:
  Step 1: `POST /api/refusjon-eøs/behandlinger/{behandlingId}`
- Constraints:
  Body must be `RestRefusjonEøs`. Treatment must be editable. The saved period id is visible in the returned treatment’s `refusjonEøs` list.

Failure or exceptional branches:
- None identified beyond non-editable treatment failures.

Endpoint coverage:
- Covers:
  `POST /api/refusjon-eøs/behandlinger/{behandlingId}`
- Distinct meaning:
  Adds a refund period.

### Function 57: list EØS refund periods

Function name:
list EØS refund periods

Core endpoint(s):
- `GET /api/refusjon-eøs/behandlinger/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function lists EØS refund periods for a treatment.
- Invocation:
  Step 1: `GET /api/refusjon-eøs/behandlinger/{behandlingId}`
- Constraints:
  `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- None identified beyond missing treatment/access failures.

Endpoint coverage:
- Covers:
  `GET /api/refusjon-eøs/behandlinger/{behandlingId}`
- Distinct meaning:
  Lists refund periods.

### Function 58: update EØS refund period

Function name:
update EØS refund period

Core endpoint(s):
- `PUT /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- An EØS refund period exists for the treatment. This can be satisfied by directly inserting the EØS refund period row linked to the treatment, or by calling `POST /api/refusjon-eøs/behandlinger/{behandlingId}`. The generated refund-period {id} must be reused by the core endpoint when required.

Successful execution:
- Result:
  This function updates an existing EØS refund period.
- Invocation:
  Step 1: `PUT /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`
- Constraints:
  `{id}` must be obtained from the setup endpoint response or a later `GET /api/refusjon-eøs/behandlinger/{behandlingId}`. Body must be `RestRefusjonEøs`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Refund period id does not exist. This intentionally creates or omits the state described by: The required setup refund-period creation endpoint was omitted or wrong `{id}` was used.
  - Failure endpoint:
    `PUT /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`
  - Why this fails:
    `RefusjonEøsService.hentRefusjonEøs` throws when the id is missing.
  - Intentionally violated constraints:
    The required setup refund-period creation endpoint was omitted or wrong `{id}` was used.

Endpoint coverage:
- Covers:
  `PUT /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`
- Distinct meaning:
  Updates a refund period.

### Function 59: delete EØS refund period

Function name:
delete EØS refund period

Core endpoint(s):
- `DELETE /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- An EØS refund period exists for the treatment. This can be satisfied by directly inserting the EØS refund period row linked to the treatment, or by calling `POST /api/refusjon-eøs/behandlinger/{behandlingId}`. The generated refund-period {id} must be reused by the core endpoint when required.

Successful execution:
- Result:
  This function deletes an existing EØS refund period.
- Invocation:
  Step 1: `DELETE /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`
- Constraints:
  `{id}` must be obtained from the setup endpoint response or a later refund-period list response.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Refund period id does not exist. This intentionally creates or omits the state described by: The required setup refund-period creation endpoint was omitted.
  - Failure endpoint:
    `DELETE /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`
  - Why this fails:
    The service looks up the refund period before deleting and throws if missing.
  - Intentionally violated constraints:
    The required setup refund-period creation endpoint was omitted.

Endpoint coverage:
- Covers:
  `DELETE /api/refusjon-eøs/behandlinger/{behandlingId}/perioder/{id}`
- Distinct meaning:
  Deletes a refund period.

### Function 60: add overpaid currency period

Function name:
add overpaid currency period

Core endpoint(s):
- `POST /api/feilutbetalt-valuta/behandling/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function adds a period with overpaid currency amount to a treatment.
- Invocation:
  Step 1: `POST /api/feilutbetalt-valuta/behandling/{behandlingId}`
- Constraints:
  Body must be `RestFeilutbetaltValuta`. If `erPerMåned` is omitted, the feature toggle value determines the stored monthly flag.

Failure or exceptional branches:
- None identified beyond treatment/access failures.

Endpoint coverage:
- Covers:
  `POST /api/feilutbetalt-valuta/behandling/{behandlingId}`
- Distinct meaning:
  Adds an overpaid currency period.

### Function 61: list overpaid currency periods

Function name:
list overpaid currency periods

Core endpoint(s):
- `GET /api/feilutbetalt-valuta/behandling/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function lists overpaid currency periods for a treatment.
- Invocation:
  Step 1: `GET /api/feilutbetalt-valuta/behandling/{behandlingId}`
- Constraints:
  `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- None identified beyond missing treatment/access failures.

Endpoint coverage:
- Covers:
  `GET /api/feilutbetalt-valuta/behandling/{behandlingId}`
- Distinct meaning:
  Lists overpaid currency periods.

### Function 62: update overpaid currency period

Function name:
update overpaid currency period

Core endpoint(s):
- `PUT /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- An overpaid-currency period exists for the treatment. This can be satisfied by directly inserting the overpaid-currency period row linked to the treatment, or by calling `POST /api/feilutbetalt-valuta/behandling/{behandlingId}`. The generated period {id} must be reused by the core endpoint when required.

Successful execution:
- Result:
  This function updates an existing overpaid currency period.
- Invocation:
  Step 1: `PUT /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`
- Constraints:
  `{id}` must come from the created period in the setup endpoint response or a later list response.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Period id does not exist. This intentionally creates or omits the state described by: The required setup creation endpoint was omitted or wrong `{id}` was used.
  - Failure endpoint:
    `PUT /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`
  - Why this fails:
    The service looks up the period by id and throws if not found.
  - Intentionally violated constraints:
    The required setup creation endpoint was omitted or wrong `{id}` was used.

Endpoint coverage:
- Covers:
  `PUT /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`
- Distinct meaning:
  Updates overpaid currency period data.

### Function 63: delete overpaid currency period

Function name:
delete overpaid currency period

Core endpoint(s):
- `DELETE /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- An overpaid-currency period exists for the treatment. This can be satisfied by directly inserting the overpaid-currency period row linked to the treatment, or by calling `POST /api/feilutbetalt-valuta/behandling/{behandlingId}`. The generated period {id} must be reused by the core endpoint when required.

Successful execution:
- Result:
  This function deletes an existing overpaid currency period.
- Invocation:
  Step 1: `DELETE /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`
- Constraints:
  `{id}` must come from the created period in the setup endpoint response or a later list response.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Period id does not exist. This intentionally creates or omits the state described by: The required setup creation endpoint was omitted.
  - Failure endpoint:
    `DELETE /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`
  - Why this fails:
    The service logs the period before deleting and throws if id is missing.
  - Intentionally violated constraints:
    The required setup creation endpoint was omitted.

Endpoint coverage:
- Covers:
  `DELETE /api/feilutbetalt-valuta/behandling/{behandlingId}/periode/{id}`
- Distinct meaning:
  Deletes an overpaid currency period.

### Function 64: create corrected decision metadata

Function name:
create corrected decision metadata

Core endpoint(s):
- `POST /api/korrigertvedtak/behandling/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function creates active corrected-decision metadata on a treatment; any previous active correction is deactivated.
- Invocation:
  Step 1: `POST /api/korrigertvedtak/behandling/{behandlingId}`
- Constraints:
  Body must be `KorrigerVedtakRequest`. Treatment must be editable.

Failure or exceptional branches:
- None identified beyond non-editable treatment failures.

Endpoint coverage:
- Covers:
  `POST /api/korrigertvedtak/behandling/{behandlingId}`
- Distinct meaning:
  Sets corrected-decision metadata.

### Function 65: deactivate corrected decision metadata

Function name:
deactivate corrected decision metadata

Core endpoint(s):
- `PATCH /api/korrigertvedtak/behandling/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The resource state produced by `POST /api/korrigertvedtak/behandling/{behandlingId}` exists with matching path, query, body, form, and header values. This can be satisfied by directly inserting corrected-decision metadata linked to the treatment, or by calling `POST /api/korrigertvedtak/behandling/{behandlingId}`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function marks active corrected-decision metadata inactive.
- Invocation:
  Step 1: `PATCH /api/korrigertvedtak/behandling/{behandlingId}`
- Constraints:
  An active correction must already exist. Treatment must be editable.

Failure or exceptional branches:
- None identified beyond implementation-level access, lookup, validation, or external integration failures; if no active correction exists, service returns null without explicit failure.

Endpoint coverage:
- Covers:
  `PATCH /api/korrigertvedtak/behandling/{behandlingId}`
- Distinct meaning:
  Deactivates corrected-decision metadata.

### Function 66: create corrected after-payment metadata

Function name:
create corrected after-payment metadata

Core endpoint(s):
- `POST /api/korrigertetterbetaling/behandling/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function creates active corrected after-payment metadata on a treatment; any previous active correction is deactivated.
- Invocation:
  Step 1: `POST /api/korrigertetterbetaling/behandling/{behandlingId}`
- Constraints:
  Body must be `KorrigertEtterbetalingRequest`. Treatment must be editable.

Failure or exceptional branches:
- None identified beyond non-editable treatment failures.

Endpoint coverage:
- Covers:
  `POST /api/korrigertetterbetaling/behandling/{behandlingId}`
- Distinct meaning:
  Sets corrected after-payment metadata.

### Function 67: list corrected after-payment metadata

Function name:
list corrected after-payment metadata

Core endpoint(s):
- `GET /api/korrigertetterbetaling/behandling/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function lists all corrected after-payment records for a treatment.
- Invocation:
  Step 1: `GET /api/korrigertetterbetaling/behandling/{behandlingId}`
- Constraints:
  `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- None identified beyond missing treatment/access failures.

Endpoint coverage:
- Covers:
  `GET /api/korrigertetterbetaling/behandling/{behandlingId}`
- Distinct meaning:
  Lists corrected after-payment records.

### Function 68: deactivate corrected after-payment metadata

Function name:
deactivate corrected after-payment metadata

Core endpoint(s):
- `PATCH /api/korrigertetterbetaling/behandling/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The resource state produced by `POST /api/korrigertetterbetaling/behandling/{behandlingId}` exists with matching path, query, body, form, and header values. This can be satisfied by directly inserting corrected after-payment metadata linked to the treatment, or by calling `POST /api/korrigertetterbetaling/behandling/{behandlingId}`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function marks active corrected after-payment metadata inactive.
- Invocation:
  Step 1: `PATCH /api/korrigertetterbetaling/behandling/{behandlingId}`
- Constraints:
  An active correction must already exist. Treatment must be editable.

Failure or exceptional branches:
- None identified beyond implementation-level access, lookup, validation, or external integration failures; if no active correction exists, service returns null without explicit failure.

Endpoint coverage:
- Covers:
  `PATCH /api/korrigertetterbetaling/behandling/{behandlingId}`
- Distinct meaning:
  Deactivates corrected after-payment metadata.

### Function 69: add small child supplement correction

Function name:
add small child supplement correction

Core endpoint(s):
- `POST /api/småbarnstilleggkorrigering/behandling/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function adds small child supplement for the requested month on a treatment.
- Invocation:
  Step 1: `POST /api/småbarnstilleggkorrigering/behandling/{behandlingId}`
- Constraints:
  Body must include `årMåned`. Treatment must be editable.

Failure or exceptional branches:
- None identified beyond treatment/editability/domain validation failures.

Endpoint coverage:
- Covers:
  `POST /api/småbarnstilleggkorrigering/behandling/{behandlingId}`
- Distinct meaning:
  Adds a monthly small child supplement correction.

### Function 70: remove small child supplement correction

Function name:
remove small child supplement correction

Core endpoint(s):
- `DELETE /api/småbarnstilleggkorrigering/behandling/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The resource state produced by `POST /api/småbarnstilleggkorrigering/behandling/{behandlingId}` exists with matching path, query, body, form, and header values. This can be satisfied by directly inserting the small-child-supplement correction linked to the treatment, or by calling `POST /api/småbarnstilleggkorrigering/behandling/{behandlingId}`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function removes small child supplement for the requested month on a treatment.
- Invocation:
  Step 1: `DELETE /api/småbarnstilleggkorrigering/behandling/{behandlingId}`
- Constraints:
  The core request body must include the same `årMåned` to remove.

Failure or exceptional branches:
- None identified beyond treatment/editability/domain validation failures.

Endpoint coverage:
- Covers:
  `DELETE /api/småbarnstilleggkorrigering/behandling/{behandlingId}`
- Distinct meaning:
  Removes a monthly small child supplement correction.

### Function 71: preview repayment warning letter

Function name:
preview repayment warning letter

Core endpoint(s):
- `POST /api/tilbakekreving/{behandlingId}/forhandsvis-varselbrev`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function generates a preview PDF for a repayment warning letter.
- Invocation:
  Step 1: `POST /api/tilbakekreving/{behandlingId}/forhandsvis-varselbrev`
- Constraints:
  Body must include `fritekst`.

Failure or exceptional branches:
- None identified beyond missing treatment/access/generation failures.

Endpoint coverage:
- Covers:
  `POST /api/tilbakekreving/{behandlingId}/forhandsvis-varselbrev`
- Distinct meaning:
  Previews repayment warning letter.

### Function 72: generate decision letter

Function name:
generate decision letter

Core endpoint(s):
- `POST /api/dokument/vedtaksbrev/{vedtakId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The required setup state described as "use `RestUtvidetBehandling.vedtak.id` from the treatment setup response." exists. This can be satisfied by direct database setup that creates the same state and scope values, or by performing the described setup action through the API.

Successful execution:
- Result:
  This function generates and stores the PDF for a decision.
- Invocation:
  Step 1: `POST /api/dokument/vedtaksbrev/{vedtakId}`
- Constraints:
  `{vedtakId}` must come from the treatment’s active `vedtak.id`.

Failure or exceptional branches:
- None identified beyond missing vedtak/access/generation failures.

Endpoint coverage:
- Covers:
  `POST /api/dokument/vedtaksbrev/{vedtakId}`
- Distinct meaning:
  Generates and saves a decision letter.

### Function 73: retrieve decision letter

Function name:
retrieve decision letter

Core endpoint(s):
- `GET /api/dokument/vedtaksbrev/{vedtakId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The resource state produced by `POST /api/dokument/vedtaksbrev/{vedtakId}` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `POST /api/dokument/vedtaksbrev/{vedtakId}`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function retrieves a generated decision letter PDF.
- Invocation:
  Step 1: `GET /api/dokument/vedtaksbrev/{vedtakId}`
- Constraints:
  `{vedtakId}` must be the active decision id from the treatment; the letter-generation setup ensures a letter exists.

Failure or exceptional branches:
- None identified beyond missing vedtak/letter/access failures.

Endpoint coverage:
- Covers:
  `GET /api/dokument/vedtaksbrev/{vedtakId}`
- Distinct meaning:
  Retrieves stored decision letter.

### Function 74: preview manual letter for treatment

Function name:
preview treatment letter

Core endpoint(s):
- `POST /api/dokument/forhaandsvis-brev/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function generates a preview of a manual letter tied to a treatment.
- Invocation:
  Step 1: `POST /api/dokument/forhaandsvis-brev/{behandlingId}`
- Constraints:
  Body must be `ManueltBrevRequest`. Recipient data is enriched from treatment person basis and work distribution.

Failure or exceptional branches:
- None identified beyond missing treatment/letter-generation failures.

Endpoint coverage:
- Covers:
  `POST /api/dokument/forhaandsvis-brev/{behandlingId}`
- Distinct meaning:
  Previews a treatment-scoped manual letter.

### Function 75: send manual letter for treatment

Function name:
send treatment letter

Core endpoint(s):
- `POST /api/dokument/send-brev/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function generates and sends a manual letter tied to a treatment.
- Invocation:
  Step 1: `POST /api/dokument/send-brev/{behandlingId}`
- Constraints:
  Body must be `ManueltBrevRequest`. `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- None identified beyond missing treatment/send failures.

Endpoint coverage:
- Covers:
  `POST /api/dokument/send-brev/{behandlingId}`
- Distinct meaning:
  Sends a treatment-scoped manual letter.

### Function 76: preview manual letter for case

Function name:
preview case letter

Core endpoint(s):
- `POST /api/dokument/fagsak/{fagsakId}/forhaandsvis-brev`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function generates a preview of a manual letter tied directly to a fagsak.
- Invocation:
  Step 1: `POST /api/dokument/fagsak/{fagsakId}/forhaandsvis-brev`
- Constraints:
  `{fagsakId}` must come from the relevant setup response. Body must be `ManueltBrevRequest`.

Failure or exceptional branches:
- None identified beyond missing fagsak/letter-generation failures.

Endpoint coverage:
- Covers:
  `POST /api/dokument/fagsak/{fagsakId}/forhaandsvis-brev`
- Distinct meaning:
  Previews a fagsak-scoped manual letter.

### Function 77: send manual letter for case

Function name:
send case letter

Core endpoint(s):
- `POST /api/dokument/fagsak/{fagsakId}/send-brev`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function generates and sends a manual letter tied directly to a fagsak.
- Invocation:
  Step 1: `POST /api/dokument/fagsak/{fagsakId}/send-brev`
- Constraints:
  `{fagsakId}` must come from the relevant setup response. Body must be `ManueltBrevRequest`.

Failure or exceptional branches:
- None identified beyond missing fagsak/send failures.

Endpoint coverage:
- Covers:
  `POST /api/dokument/fagsak/{fagsakId}/send-brev`
- Distinct meaning:
  Sends a fagsak-scoped manual letter.

### Function 78: add letter recipient

Function name:
add letter recipient

Core endpoint(s):
- `POST /api/brevmottaker/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function adds a manual letter recipient to a treatment.
- Invocation:
  Step 1: `POST /api/brevmottaker/{behandlingId}`
- Constraints:
  Body must be `RestBrevmottaker`. Treatment must be editable. Strictly confidential person rules may reject manual recipients.

Failure or exceptional branches:
- None identified beyond editability and recipient validation failures.

Endpoint coverage:
- Covers:
  `POST /api/brevmottaker/{behandlingId}`
- Distinct meaning:
  Adds a letter recipient.

### Function 79: list letter recipients

Function name:
list letter recipients

Core endpoint(s):
- `GET /api/brevmottaker/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function lists manual letter recipients for a treatment.
- Invocation:
  Step 1: `GET /api/brevmottaker/{behandlingId}`
- Constraints:
  `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- None identified beyond missing treatment/access failures.

Endpoint coverage:
- Covers:
  `GET /api/brevmottaker/{behandlingId}`
- Distinct meaning:
  Lists letter recipients.

### Function 80: delete letter recipient

Function name:
delete letter recipient

Core endpoint(s):
- `DELETE /api/brevmottaker/{behandlingId}/{mottakerId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- A manual letter recipient exists for the treatment. This can be satisfied by directly inserting the manual letter-recipient row linked to the treatment, or by calling `POST /api/brevmottaker/{behandlingId}`. The generated {mottakerId} must be reused by the core endpoint when required.

Successful execution:
- Result:
  This function deletes a manual letter recipient from a treatment.
- Invocation:
  Step 1: `DELETE /api/brevmottaker/{behandlingId}/{mottakerId}`
- Constraints:
  `{mottakerId}` must be obtained from the setup endpoint response or later recipient list.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Recipient id does not exist. This intentionally creates or omits the state described by: The required setup recipient creation endpoint was omitted.
  - Failure endpoint:
    `DELETE /api/brevmottaker/{behandlingId}/{mottakerId}`
  - Why this fails:
    `BrevmottakerService.fjernBrevmottaker` throws when the recipient id is missing.
  - Intentionally violated constraints:
    The required setup recipient creation endpoint was omitted.

Endpoint coverage:
- Covers:
  `DELETE /api/brevmottaker/{behandlingId}/{mottakerId}`
- Distinct meaning:
  Deletes a letter recipient.

### Function 81: update standard decision-period explanations

Function name:
update standard explanations

Core endpoint(s):
- `PUT /api/vedtaksperioder/standardbegrunnelser/{vedtaksperiodeId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The required setup state described as "advance treatment far enough to generate decision periods." exists. This can be satisfied by direct database setup that creates the same state and scope values, or by performing the described setup action through the API.
- The resource state produced by `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function updates standard and EØS standard explanations on a decision period.
- Invocation:
  Step 1: `PUT /api/vedtaksperioder/standardbegrunnelser/{vedtaksperiodeId}`
- Constraints:
  `{vedtaksperiodeId}` must come from the decision-period setup. Body must contain explanation enum names convertible by `IVedtakBegrunnelse.konverterTilEnumVerdi`.

Failure or exceptional branches:
- None identified beyond missing decision period or invalid explanation enum failures.

Endpoint coverage:
- Covers:
  `PUT /api/vedtaksperioder/standardbegrunnelser/{vedtaksperiodeId}`
- Distinct meaning:
  Updates standard explanations on a decision period.

### Function 82: update decision-period free texts

Function name:
update decision free texts

Core endpoint(s):
- `PUT /api/vedtaksperioder/fritekster/{vedtaksperiodeId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The required setup state described as "advance treatment far enough to generate decision periods." exists. This can be satisfied by direct database setup that creates the same state and scope values, or by performing the described setup action through the API.
- The resource state produced by `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function updates free-text explanations on a decision period.
- Invocation:
  Step 1: `PUT /api/vedtaksperioder/fritekster/{vedtaksperiodeId}`
- Constraints:
  `{vedtaksperiodeId}` must come from the decision-period setup. Body must be `RestPutVedtaksperiodeMedFritekster`.

Failure or exceptional branches:
- None identified beyond missing decision period failures.

Endpoint coverage:
- Covers:
  `PUT /api/vedtaksperioder/fritekster/{vedtaksperiodeId}`
- Distinct meaning:
  Updates free-text explanations.

### Function 83: override change date and regenerate decision periods

Function name:
regenerate decision periods

Core endpoint(s):
- `PUT /api/vedtaksperioder/endringstidspunkt`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function stores an overridden change date and regenerates decision periods through the first change date.
- Invocation:
  Step 1: `PUT /api/vedtaksperioder/endringstidspunkt`
- Constraints:
  Body must include `behandlingId`; that id must identify the prepared treatment.

Failure or exceptional branches:
- None identified beyond treatment/access/regeneration failures.

Endpoint coverage:
- Covers:
  `PUT /api/vedtaksperioder/endringstidspunkt`
- Distinct meaning:
  Regenerates decision periods from overridden change date.

### Function 84: generate letter explanation texts for period

Function name:
generate letter explanation texts

Core endpoint(s):
- `GET /api/vedtaksperioder/brevbegrunnelser/{vedtaksperiodeId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
- The required setup state described as "advance treatment far enough to generate decision periods." exists. This can be satisfied by direct database setup that creates the same state and scope values, or by performing the described setup action through the API.
- The resource state produced by `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function generates the actual letter explanation texts for one decision period.
- Invocation:
  Step 1: `GET /api/vedtaksperioder/brevbegrunnelser/{vedtaksperiodeId}`
- Constraints:
  `{vedtaksperiodeId}` must come from the decision-period setup.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - A generated explanation has an unknown type. This intentionally creates or omits the state described by: The decision period contains unsupported explanation data.
  - Failure endpoint:
    `GET /api/vedtaksperioder/brevbegrunnelser/{vedtaksperiodeId}`
  - Why this fails:
    The controller throws `Feil("Ukjent begrunnelsestype")` for unrecognized explanation data.
  - Intentionally violated constraints:
    The decision period contains unsupported explanation data.

Endpoint coverage:
- Covers:
  `GET /api/vedtaksperioder/brevbegrunnelser/{vedtaksperiodeId}`
- Distinct meaning:
  Generates human-readable letter explanations.

### Function 85: list decision periods

Function name:
list decision periods

Core endpoint(s):
- `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function lists decision periods with explanations for a treatment.
- Invocation:
  Step 1: `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder`
- Constraints:
  `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- None identified beyond missing treatment failures.

Endpoint coverage:
- Covers:
  `GET /api/vedtaksperioder/behandling/{behandlingId}/hent-vedtaksperioder`
- Distinct meaning:
  Lists decision periods.

### Function 86: retrieve treatment log

Function name:
retrieve treatment log

Core endpoint(s):
- `GET /api/logg/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function returns log entries for a treatment.
- Invocation:
  Step 1: `GET /api/logg/{behandlingId}`
- Constraints:
  `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Log retrieval fails. This intentionally creates or omits the state described by: Invalid treatment id or repository failure.
  - Failure endpoint:
    `GET /api/logg/{behandlingId}`
  - Why this fails:
    The controller catches retrieval errors and returns a bad-request resource.
  - Intentionally violated constraints:
    Invalid treatment id or repository failure.

Endpoint coverage:
- Covers:
  `GET /api/logg/{behandlingId}`
- Distinct meaning:
  Reads treatment audit log.

### Function 87: retrieve extended child benefit for BISYS

Function name:
retrieve BISYS extended benefit

Core endpoint(s):
- `POST /api/bisys/hent-utvidet-barnetrygd`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns periods with extended child benefit and small child supplement for a person for BISYS.
- Invocation:
  Step 1: `POST /api/bisys/hent-utvidet-barnetrygd`
- Constraints:
  Body must include `personIdent` and `fraDato`. `fraDato` cannot be more than five years before the current date.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `fraDato` is too old. This intentionally creates or omits the state described by: `fraDato < today - 5 years`.
  - Failure endpoint:
    `POST /api/bisys/hent-utvidet-barnetrygd`
  - Why this fails:
    The controller throws a BAD_REQUEST external service error when `fraDato` is older than five years.
  - Intentionally violated constraints:
    `fraDato < today - 5 years`.
- Branch 2:
  - Preconditions:
    - Person is not found in PDL. This intentionally creates or omits the state described by: Unknown `personIdent`.
  - Failure endpoint:
    `POST /api/bisys/hent-utvidet-barnetrygd`
  - Why this fails:
    A PDL not-found `Feil` is converted to a BAD_REQUEST external service error.
  - Intentionally violated constraints:
    Unknown `personIdent`.

Endpoint coverage:
- Covers:
  `POST /api/bisys/hent-utvidet-barnetrygd`
- Distinct meaning:
  External BISYS benefit lookup.

### Function 88: retrieve child benefit for pension

Function name:
retrieve pension child benefit

Core endpoint(s):
- `POST /api/ekstern/pensjon/hent-barnetrygd`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns child benefit and related cases for a person for Pensjon.
- Invocation:
  Step 1: `POST /api/ekstern/pensjon/hent-barnetrygd`
- Constraints:
  Body must include `ident` and `fraDato`. `fraDato` cannot be more than two years before the current date.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `fraDato` is too old. This intentionally creates or omits the state described by: `fraDato < today - 2 years`.
  - Failure endpoint:
    `POST /api/ekstern/pensjon/hent-barnetrygd`
  - Why this fails:
    The controller throws a BAD_REQUEST external service error when `fraDato` is older than two years.
  - Intentionally violated constraints:
    `fraDato < today - 2 years`.

Endpoint coverage:
- Covers:
  `POST /api/ekstern/pensjon/hent-barnetrygd`
- Distinct meaning:
  External Pensjon benefit lookup.

### Function 89: order pension child-benefit persons for year

Function name:
order pension yearly export

Core endpoint(s):
- `GET /api/ekstern/pensjon/bestill-personer-med-barnetrygd/{år}`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function creates a task/Kafka flow to export persons with child benefit for a given year.
- Invocation:
  Step 1: `GET /api/ekstern/pensjon/bestill-personer-med-barnetrygd/{år}`
- Constraints:
  `{år}` must be an integer from 1970 through 2300.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Year is outside the allowed range. This intentionally creates or omits the state described by: `{år}` outside `1970..2300`.
  - Failure endpoint:
    `GET /api/ekstern/pensjon/bestill-personer-med-barnetrygd/{år}`
  - Why this fails:
    The controller throws `IllegalArgumentException`.
  - Intentionally violated constraints:
    `{år}` outside `1970..2300`.

Endpoint coverage:
- Covers:
  `GET /api/ekstern/pensjon/bestill-personer-med-barnetrygd/{år}`
- Distinct meaning:
  Orders yearly pension export.

### Function 90: list persons with extended child benefit for tax

Function name:
list tax persons

Core endpoint(s):
- `GET /api/skatt/personer`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns persons with extended child benefit for a tax year, using real data when the feature toggle is enabled and test data otherwise.
- Invocation:
  Step 1: `GET /api/skatt/personer`
- Constraints:
  Query `aar` is required and must parse as a year string.

Failure or exceptional branches:
- None identified beyond invalid `aar` parse/upstream failures.

Endpoint coverage:
- Covers:
  `GET /api/skatt/personer`
- Distinct meaning:
  Lists persons for Skatteetaten.

### Function 91: list persons with extended child benefit for tax test endpoint

Function name:
list tax persons test

Core endpoint(s):
- `GET /api/skatt/personer/test`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns persons with extended child benefit from the service test path.
- Invocation:
  Step 1: `GET /api/skatt/personer/test`
- Constraints:
  Query `aar` is required.

Failure or exceptional branches:
- None identified beyond invalid `aar`/upstream failures.

Endpoint coverage:
- Covers:
  `GET /api/skatt/personer/test`
- Distinct meaning:
  Test variant that always calls `skatteetatenService`.

### Function 92: retrieve tax periods

Function name:
retrieve tax periods

Core endpoint(s):
- `POST /api/skatt/perioder`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns periods with extended child benefit for requested identifiers and year.
- Invocation:
  Step 1: `POST /api/skatt/perioder`
- Constraints:
  Body must be `SkatteetatenPerioderRequest` with `identer` and `aar`.

Failure or exceptional branches:
- None identified beyond request validation/upstream failures.

Endpoint coverage:
- Covers:
  `POST /api/skatt/perioder`
- Distinct meaning:
  Retrieves tax period data.

### Function 93: retrieve tax periods test variant

Function name:
retrieve tax periods test

Core endpoint(s):
- `POST /api/skatt/perioder/test`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns periods with extended child benefit through the test endpoint.
- Invocation:
  Step 1: `POST /api/skatt/perioder/test`
- Constraints:
  Body must be `SkatteetatenPerioderRequest`.

Failure or exceptional branches:
- None identified beyond request validation/upstream failures.

Endpoint coverage:
- Covers:
  `POST /api/skatt/perioder/test`
- Distinct meaning:
  Test variant that always calls `skatteetatenService`.

### Function 94: retrieve Infotrygd cases

Function name:
retrieve Infotrygd cases

Core endpoint(s):
- `POST /api/infotrygd/hent-infotrygdsaker-for-soker`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function retrieves Infotrygd child-benefit cases for an applicant, with masked response when access is missing.
- Invocation:
  Step 1: `POST /api/infotrygd/hent-infotrygdsaker-for-soker`
- Constraints:
  Body must include `ident`.

Failure or exceptional branches:
- None identified beyond person/upstream failures.

Endpoint coverage:
- Covers:
  `POST /api/infotrygd/hent-infotrygdsaker-for-soker`
- Distinct meaning:
  Retrieves Infotrygd case list.

### Function 95: retrieve Infotrygd benefits

Function name:
retrieve Infotrygd benefits

Core endpoint(s):
- `POST /api/infotrygd/hent-infotrygdstonader-for-soker`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function retrieves Infotrygd benefit periods for an applicant, with masked response when access is missing.
- Invocation:
  Step 1: `POST /api/infotrygd/hent-infotrygdstonader-for-soker`
- Constraints:
  Body must include `ident`.

Failure or exceptional branches:
- None identified beyond person/upstream failures.

Endpoint coverage:
- Covers:
  `POST /api/infotrygd/hent-infotrygdstonader-for-soker`
- Distinct meaning:
  Retrieves Infotrygd benefit list.

### Function 96: check ongoing Infotrygd case

Function name:
check ongoing Infotrygd case

Core endpoint(s):
- `POST /api/infotrygd/har-lopende-sak`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function checks whether the person has an ongoing Infotrygd case.
- Invocation:
  Step 1: `POST /api/infotrygd/har-lopende-sak`
- Constraints:
  Body must include `ident`.

Failure or exceptional branches:
- None identified beyond upstream failures.

Endpoint coverage:
- Covers:
  `POST /api/infotrygd/har-lopende-sak`
- Distinct meaning:
  Boolean ongoing Infotrygd check.

### Function 97: retrieve collaborator by organization number

Function name:
retrieve collaborator by organization

Core endpoint(s):
- `GET /api/samhandler/orgnr/{orgnr}`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function retrieves TSS collaborator information for an organization number.
- Invocation:
  Step 1: `GET /api/samhandler/orgnr/{orgnr}`
- Constraints:
  `{orgnr}` is passed to the institution service and copied into the response.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Collaborator is not found. This intentionally creates or omits the state described by: Unknown organization number.
  - Failure endpoint:
    `GET /api/samhandler/orgnr/{orgnr}`
  - Why this fails:
    Not-found exceptions are converted to a functional 404 error.
  - Intentionally violated constraints:
    Unknown organization number.

Endpoint coverage:
- Covers:
  `GET /api/samhandler/orgnr/{orgnr}`
- Distinct meaning:
  Retrieves collaborator by organization number.

### Function 98: search collaborator by name or location

Function name:
search collaborator

Core endpoint(s):
- `POST /api/samhandler/navn`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function searches collaborator information by name, postal code, or area.
- Invocation:
  Step 1: `POST /api/samhandler/navn`
- Constraints:
  Body must contain at least one of `navn`, `postnummer`, or `område`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - All search criteria are null. This intentionally creates or omits the state described by: `navn == null`, `postnummer == null`, and `område == null`.
  - Failure endpoint:
    `POST /api/samhandler/navn`
  - Why this fails:
    The controller throws BAD_REQUEST when no search variable is supplied.
  - Intentionally violated constraints:
    `navn == null`, `postnummer == null`, and `område == null`.

Endpoint coverage:
- Covers:
  `POST /api/samhandler/navn`
- Distinct meaning:
  Searches collaborators.

### Function 99: create complaint treatment

Function name:
create complaint treatment

Core endpoint(s):
- `POST /api/fagsaker/{fagsakId}/opprett-klagebehandling`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function creates a complaint treatment for a fagsak through the internal complaint integration.
- Invocation:
  Step 1: `POST /api/fagsaker/{fagsakId}/opprett-klagebehandling`
- Constraints:
  `{fagsakId}` must come from the relevant setup response. Body must be `OpprettKlageDto`.

Failure or exceptional branches:
- None identified beyond fagsak/access/klage-service failures.

Endpoint coverage:
- Covers:
  `POST /api/fagsaker/{fagsakId}/opprett-klagebehandling`
- Distinct meaning:
  Creates complaint treatment.

### Function 100: list complaint treatments

Function name:
list complaint treatments

Core endpoint(s):
- `GET /api/fagsaker/{fagsakId}/hent-klagebehandlinger`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function lists complaint treatments for a fagsak.
- Invocation:
  Step 1: `GET /api/fagsaker/{fagsakId}/hent-klagebehandlinger`
- Constraints:
  `{fagsakId}` must come from the relevant setup response.

Failure or exceptional branches:
- None identified beyond fagsak/access/service failures.

Endpoint coverage:
- Covers:
  `GET /api/fagsaker/{fagsakId}/hent-klagebehandlinger`
- Distinct meaning:
  Lists complaint treatments.

### Function 101: check if complaint revision can be created

Function name:
check complaint revision creation

Core endpoint(s):
- `GET /api/klage/fagsaker/{fagsakId}/kan-opprette-revurdering-klage`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function lets the complaint system check whether a complaint-triggered revision can be created.
- Invocation:
  Step 1: `GET /api/klage/fagsaker/{fagsakId}/kan-opprette-revurdering-klage`
- Constraints:
  The request must come from the authorized complaint client according to `SikkerhetContext.kallKommerFraKlage()`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - Caller is not the complaint client. This intentionally creates or omits the state described by: Caller context is not recognized as Klage.
  - Failure endpoint:
    `GET /api/klage/fagsaker/{fagsakId}/kan-opprette-revurdering-klage`
  - Why this fails:
    The controller throws when the call does not come from an authorized complaint client.
  - Intentionally violated constraints:
    Caller context is not recognized as Klage.

Endpoint coverage:
- Covers:
  `GET /api/klage/fagsaker/{fagsakId}/kan-opprette-revurdering-klage`
- Distinct meaning:
  External complaint precheck.

### Function 102: create complaint revision

Function name:
create complaint revision

Core endpoint(s):
- `POST /api/klage/fagsaker/{fagsakId}/opprett-revurdering-klage/`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- The resource state produced by `GET /api/klage/fagsaker/{fagsakId}/kan-opprette-revurdering-klage` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `GET /api/klage/fagsaker/{fagsakId}/kan-opprette-revurdering-klage`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function creates a revision treatment for a complaint.
- Invocation:
  Step 1: `POST /api/klage/fagsaker/{fagsakId}/opprett-revurdering-klage/`
- Constraints:
  Request must come from the complaint client; `{fagsakId}` must be the same across the related validation and execution requests.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - Caller is not the complaint client. This intentionally creates or omits the state described by: Caller context is not recognized as Klage.
  - Failure endpoint:
    `POST /api/klage/fagsaker/{fagsakId}/opprett-revurdering-klage/`
  - Why this fails:
    The controller checks `SikkerhetContext.kallKommerFraKlage()`.
  - Intentionally violated constraints:
    Caller context is not recognized as Klage.

Endpoint coverage:
- Covers:
  `POST /api/klage/fagsaker/{fagsakId}/opprett-revurdering-klage/`
- Distinct meaning:
  External complaint revision creation.

### Function 103: retrieve decisions for complaint system

Function name:
retrieve complaint decisions

Core endpoint(s):
- `GET /api/klage/fagsaker/{fagsakId}/vedtak`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function returns fagsystem decisions for a fagsak to the complaint system.
- Invocation:
  Step 1: `GET /api/klage/fagsaker/{fagsakId}/vedtak`
- Constraints:
  If the caller is not machine-to-machine, ordinary fagsak access is checked.

Failure or exceptional branches:
- None identified beyond fagsak/access/service failures.

Endpoint coverage:
- Covers:
  `GET /api/klage/fagsaker/{fagsakId}/vedtak`
- Distinct meaning:
  Retrieves fagsystem decisions for complaint use.

### Function 104: search tasks

Function name:
search tasks

Core endpoint(s):
- `POST /api/oppgave/hent-oppgaver`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function searches external tasks through the oppgave integration.
- Invocation:
  Step 1: `POST /api/oppgave/hent-oppgaver`
- Constraints:
  Body must be `RestFinnOppgaveRequest`, converted to a task search request.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Task search fails. This intentionally creates or omits the state described by: Invalid search body or upstream task service failure.
  - Failure endpoint:
    `POST /api/oppgave/hent-oppgaver`
  - Why this fails:
    The controller catches errors and returns an illegal-state response when task retrieval fails.
  - Intentionally violated constraints:
    Invalid search body or upstream task service failure.

Endpoint coverage:
- Covers:
  `POST /api/oppgave/hent-oppgaver`
- Distinct meaning:
  Searches tasks.

### Function 105: assign task

Function name:
assign task

Core endpoint(s):
- `POST /api/oppgave/{oppgaveId}/fordel`

Preconditions:
- The resource state produced by `POST /api/oppgave/hent-oppgaver` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `POST /api/oppgave/hent-oppgaver`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function assigns an external task to a caseworker.
- Invocation:
  Step 1: `POST /api/oppgave/{oppgaveId}/fordel`
- Constraints:
  `{oppgaveId}` should come from the task search result. The core request query `saksbehandler` is required.

Failure or exceptional branches:
- None identified beyond upstream task assignment failures.

Endpoint coverage:
- Covers:
  `POST /api/oppgave/{oppgaveId}/fordel`
- Distinct meaning:
  Assigns a task.

### Function 106: reset task assignment

Function name:
reset task assignment

Core endpoint(s):
- `POST /api/oppgave/{oppgaveId}/tilbakestill`

Preconditions:
- The resource state produced by `POST /api/oppgave/hent-oppgaver` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `POST /api/oppgave/hent-oppgaver`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function clears assignment on an external task.
- Invocation:
  Step 1: `POST /api/oppgave/{oppgaveId}/tilbakestill`
- Constraints:
  `{oppgaveId}` should come from the task search result.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The resource state produced by `POST /api/oppgave/hent-oppgaver` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `POST /api/oppgave/hent-oppgaver`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.
    - Assignment reset fails upstream. This intentionally creates or omits the state described by: Invalid or unresettable `oppgaveId`.
  - Failure endpoint:
    `POST /api/oppgave/{oppgaveId}/tilbakestill`
  - Why this fails:
    The controller catches errors and returns illegal state.
  - Intentionally violated constraints:
    Invalid or unresettable `oppgaveId`.

Endpoint coverage:
- Covers:
  `POST /api/oppgave/{oppgaveId}/tilbakestill`
- Distinct meaning:
  Resets task assignment.

### Function 107: retrieve manual journaling task data

Function name:
retrieve journaling task data

Core endpoint(s):
- `GET /api/oppgave/{oppgaveId}`

Preconditions:
- The resource state produced by `POST /api/oppgave/hent-oppgaver` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `POST /api/oppgave/hent-oppgaver`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function retrieves task, person, fagsak, and optional journalpost data needed for manual journaling.
- Invocation:
  Step 1: `GET /api/oppgave/{oppgaveId}`
- Constraints:
  `{oppgaveId}` should come from the relevant setup response. If the task has actor id or journalpost id, those are used to enrich the response.

Failure or exceptional branches:
- None identified beyond missing task/upstream failures.

Endpoint coverage:
- Covers:
  `GET /api/oppgave/{oppgaveId}`
- Distinct meaning:
  Reads data for manual journaling.

### Function 108: complete task

Function name:
complete task

Core endpoint(s):
- `GET /api/oppgave/{oppgaveId}/ferdigstill`

Preconditions:
- The resource state produced by `POST /api/oppgave/hent-oppgaver` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `POST /api/oppgave/hent-oppgaver`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function completes/closes an external task.
- Invocation:
  Step 1: `GET /api/oppgave/{oppgaveId}/ferdigstill`
- Constraints:
  `{oppgaveId}` should come from the relevant setup response.

Failure or exceptional branches:
- None identified beyond missing task/upstream completion failures.

Endpoint coverage:
- Covers:
  `GET /api/oppgave/{oppgaveId}/ferdigstill`
- Distinct meaning:
  Completes a task.

### Function 109: complete task and link journalpost

Function name:
complete task and link journalpost

Core endpoint(s):
- `POST /api/oppgave/{oppgaveId}/ferdigstillOgKnyttjournalpost`

Preconditions:
- The resource state produced by `POST /api/oppgave/hent-oppgaver` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `POST /api/oppgave/hent-oppgaver`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function links a journalpost to a fagsak and completes the task.
- Invocation:
  Step 1: `POST /api/oppgave/{oppgaveId}/ferdigstillOgKnyttjournalpost`
- Constraints:
  `{oppgaveId}` should come from the relevant setup response. Body must be `RestFerdigstillOppgaveKnyttJournalpost`.

Failure or exceptional branches:
- None identified beyond missing task/journalpost/linking failures.

Endpoint coverage:
- Covers:
  `POST /api/oppgave/{oppgaveId}/ferdigstillOgKnyttjournalpost`
- Distinct meaning:
  Completes a task while linking a journalpost.

### Function 110: retrieve deadlines for open extended child-benefit treatments

Function name:
retrieve open treatment deadlines

Core endpoint(s):
- `POST /api/oppgave/hent-frister-for-apne-utvidet-barnetrygd-behandlinger`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns deadlines for open extended child-benefit treatment tasks.
- Invocation:
  Step 1: `POST /api/oppgave/hent-frister-for-apne-utvidet-barnetrygd-behandlinger`
- Constraints:
  No case-specific prerequisite is required.

Failure or exceptional branches:
- None identified in project source.

Endpoint coverage:
- Covers:
  `POST /api/oppgave/hent-frister-for-apne-utvidet-barnetrygd-behandlinger`
- Distinct meaning:
  Retrieves open treatment task deadlines.

### Function 111: clear application ownership on tasks

Function name:
clear application task ownership

Core endpoint(s):
- `POST /api/oppgave/fjern-behandles-av-applikasjon`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function removes `behandlesAvApplikasjon` for a list of task ids.
- Invocation:
  Step 1: `POST /api/oppgave/fjern-behandles-av-applikasjon`
- Constraints:
  Body must be an array of task ids.

Failure or exceptional branches:
- None identified beyond upstream task update failures.

Endpoint coverage:
- Covers:
  `POST /api/oppgave/fjern-behandles-av-applikasjon`
- Distinct meaning:
  Clears application ownership marker on tasks.

### Function 112: retrieve journalpost

Function name:
retrieve journalpost

Core endpoint(s):
- `GET /api/journalpost/{journalpostId}/hent`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function retrieves metadata for a journalpost.
- Invocation:
  Step 1: `GET /api/journalpost/{journalpostId}/hent`
- Constraints:
  `{journalpostId}` must identify an existing journalpost in the external journal system.

Failure or exceptional branches:
- None identified beyond upstream not-found failures.

Endpoint coverage:
- Covers:
  `GET /api/journalpost/{journalpostId}/hent`
- Distinct meaning:
  Retrieves journalpost metadata.

### Function 113: list journalposts for user

Function name:
list user journalposts

Core endpoint(s):
- `POST /api/journalpost/for-bruker`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function retrieves journalposts for a person.
- Invocation:
  Step 1: `POST /api/journalpost/for-bruker`
- Constraints:
  Body must be `PersonIdent`.

Failure or exceptional branches:
- None identified beyond upstream failures.

Endpoint coverage:
- Covers:
  `POST /api/journalpost/for-bruker`
- Distinct meaning:
  Lists journalposts for a user.

### Function 114: retrieve journal document as resource

Function name:
retrieve journal document resource

Core endpoint(s):
- `GET /api/journalpost/{journalpostId}/hent/{dokumentInfoId}`

Preconditions:
- The resource state produced by `GET /api/journalpost/{journalpostId}/hent` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `GET /api/journalpost/{journalpostId}/hent`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function retrieves a journal document as a `Ressurs<ByteArray>`.
- Invocation:
  Step 1: `GET /api/journalpost/{journalpostId}/hent/{dokumentInfoId}`
- Constraints:
  `{dokumentInfoId}` must come from the document list in the retrieved journalpost.

Failure or exceptional branches:
- None identified beyond upstream not-found failures.

Endpoint coverage:
- Covers:
  `GET /api/journalpost/{journalpostId}/hent/{dokumentInfoId}`
- Distinct meaning:
  Retrieves document bytes wrapped in a resource.

### Function 115: retrieve journal document as PDF

Function name:
retrieve journal document PDF

Core endpoint(s):
- `GET /api/journalpost/{journalpostId}/dokument/{dokumentInfoId}`

Preconditions:
- The resource state produced by `GET /api/journalpost/{journalpostId}/hent` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `GET /api/journalpost/{journalpostId}/hent`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function retrieves a journal document as raw PDF bytes.
- Invocation:
  Step 1: `GET /api/journalpost/{journalpostId}/dokument/{dokumentInfoId}`
- Constraints:
  `{dokumentInfoId}` must come from the retrieved journalpost.

Failure or exceptional branches:
- None identified beyond upstream not-found failures.

Endpoint coverage:
- Covers:
  `GET /api/journalpost/{journalpostId}/dokument/{dokumentInfoId}`
- Distinct meaning:
  Retrieves document bytes with PDF media type.

### Function 116: journal a journalpost

Function name:
journal journalpost

Core endpoint(s):
- `POST /api/journalpost/{journalpostId}/journalfør/{oppgaveId}`

Preconditions:
- The resource state produced by `GET /api/journalpost/{journalpostId}/hent` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `GET /api/journalpost/{journalpostId}/hent`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.
- The resource state produced by `POST /api/oppgave/hent-oppgaver` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `POST /api/oppgave/hent-oppgaver`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function journals a journalpost to a fagsak/treatment context.
- Invocation:
  Step 1: `POST /api/journalpost/{journalpostId}/journalfør/{oppgaveId}`
- Constraints:
  `{journalpostId}` must identify the journalpost. `{oppgaveId}` should identify the related task. Query `journalfoerendeEnhet` is required. Body `RestJournalføring.dokumenter` must all have non-empty `dokumentTittel`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The resource state produced by `GET /api/journalpost/{journalpostId}/hent` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `GET /api/journalpost/{journalpostId}/hent`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.
    - The resource state produced by `POST /api/oppgave/hent-oppgaver` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `POST /api/oppgave/hent-oppgaver`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.
    - At least one document lacks title. This intentionally creates or omits the state described by: Blank or missing document title.
  - Failure endpoint:
    `POST /api/journalpost/{journalpostId}/journalfør/{oppgaveId}`
  - Why this fails:
    The controller throws a functional error when any `dokumentTittel` is null or blank.
  - Intentionally violated constraints:
    Blank or missing document title.

Endpoint coverage:
- Covers:
  `POST /api/journalpost/{journalpostId}/journalfør/{oppgaveId}`
- Distinct meaning:
  Journals incoming document data.

### Function 117: retrieve feature toggles

Function name:
retrieve feature toggles

Core endpoint(s):
- `POST /api/feature`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns enabled/disabled values for requested feature toggles.
- Invocation:
  Step 1: `POST /api/feature`
- Constraints:
  Body must be an array of toggle ids.

Failure or exceptional branches:
- None identified in project source.

Endpoint coverage:
- Covers:
  `POST /api/feature`
- Distinct meaning:
  Reads feature-toggle states.

### Function 118: check person access

Function name:
check person access

Core endpoint(s):
- `POST /api/tilgang`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns whether the current caseworker has access to a person and that person’s address protection grading.
- Invocation:
  Step 1: `POST /api/tilgang`
- Constraints:
  Body must include `brukerIdent`.

Failure or exceptional branches:
- None identified beyond person lookup/upstream access-service failures.

Endpoint coverage:
- Covers:
  `POST /api/tilgang`
- Distinct meaning:
  Checks access and discretion code.

### Function 119: handle identity event

Function name:
handle identity event

Core endpoint(s):
- `POST /api/ident`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function creates a task for a new identity/PDL identity event.
- Invocation:
  Step 1: `POST /api/ident`
- Constraints:
  Body must be `PersonIdent`.

Failure or exceptional branches:
- None identified beyond task creation failures.

Endpoint coverage:
- Covers:
  `POST /api/ident`
- Distinct meaning:
  Queues identity-event handling.

### Function 120: handle transitional benefit decision event

Function name:
handle transitional benefit event

Core endpoint(s):
- `POST /api/overgangsstonad`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function creates a task for a transitional benefit decision event.
- Invocation:
  Step 1: `POST /api/overgangsstonad`
- Constraints:
  Body must include `ident`.

Failure or exceptional branches:
- None identified beyond task repository failures.

Endpoint coverage:
- Covers:
  `POST /api/overgangsstonad`
- Distinct meaning:
  Queues transitional-benefit event handling.

### Function 121: trigger rate change for one case

Function name:
trigger rate change for case

Core endpoint(s):
- `GET /api/satsendring/kjorsatsendring/{fagsakId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function creates a rate-change task for one fagsak.
- Invocation:
  Step 1: `GET /api/satsendring/kjorsatsendring/{fagsakId}`
- Constraints:
  `{fagsakId}` must come from the relevant setup response.

Failure or exceptional branches:
- None identified beyond fagsak/task failures.

Endpoint coverage:
- Covers:
  `GET /api/satsendring/kjorsatsendring/{fagsakId}`
- Distinct meaning:
  Queues rate change for one fagsak.

### Function 122: trigger rate change for multiple cases

Function name:
trigger rate change for cases

Core endpoint(s):
- `POST /api/satsendring/kjorsatsendring`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function creates rate-change tasks for a set of fagsaker.
- Invocation:
  Step 1: `POST /api/satsendring/kjorsatsendring`
- Constraints:
  The core request body must be a set of fagsak ids, including ids created directly or known from documented fagsak lookup endpoints.

Failure or exceptional branches:
- None identified beyond per-case task failures.

Endpoint coverage:
- Covers:
  `POST /api/satsendring/kjorsatsendring`
- Distinct meaning:
  Queues rate change for multiple fagsaker.

### Function 123: run synchronous rate change for case

Function name:
run synchronous rate change

Core endpoint(s):
- `PUT /api/satsendring/{fagsakId}/kjor-satsendring-synkront`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- The resource state produced by `GET /api/satsendring/{fagsakId}/kan-kjore-satsendring` exists with matching path, query, body, form, and header values. This can be satisfied by direct database setup that creates the same resource state, parent-child relationship, ids, and scope values, or by calling `GET /api/satsendring/{fagsakId}/kan-kjore-satsendring`. Any generated id, response value, or scoped relationship from that setup must be reused by the core endpoint.

Successful execution:
- Result:
  This function performs a manual synchronous rate change for a fagsak.
- Invocation:
  Step 1: `PUT /api/satsendring/{fagsakId}/kjor-satsendring-synkront`
- Constraints:
  `{fagsakId}` must be the same across the related validation and execution requests. the rate-change eligibility check should return true.

Failure or exceptional branches:
- None identified beyond access/domain validation failures in rate-change service.

Endpoint coverage:
- Covers:
  `PUT /api/satsendring/{fagsakId}/kjor-satsendring-synkront`
- Distinct meaning:
  Runs rate change synchronously.

### Function 124: check if rate change can run

Function name:
check rate change eligibility

Core endpoint(s):
- `GET /api/satsendring/{fagsakId}/kan-kjore-satsendring`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function returns whether a manual rate change can be run for a fagsak.
- Invocation:
  Step 1: `GET /api/satsendring/{fagsakId}/kan-kjore-satsendring`
- Constraints:
  `{fagsakId}` must come from the relevant setup response.

Failure or exceptional branches:
- None identified beyond missing fagsak/service failures.

Endpoint coverage:
- Covers:
  `GET /api/satsendring/{fagsakId}/kan-kjore-satsendring`
- Distinct meaning:
  Checks manual rate-change eligibility.

### Function 125: trigger rate change from identities

Function name:
trigger rate change from identities

Core endpoint(s):
- `POST /api/satsendring/kjorsatsendringForListeMedIdenter`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function checks each supplied person identifier and creates rate-change tasks where old rates are detected.
- Invocation:
  Step 1: `POST /api/satsendring/kjorsatsendringForListeMedIdenter`
- Constraints:
  Body must be a set of identifiers.

Failure or exceptional branches:
- None identified beyond per-ident service failures.

Endpoint coverage:
- Covers:
  `POST /api/satsendring/kjorsatsendringForListeMedIdenter`
- Distinct meaning:
  Queues rate changes based on identifiers.

### Function 126: queue dismissal for treatments with long deadlines

Function name:
queue long-deadline dismissals

Core endpoint(s):
- `POST /api/satsendring/henleggBehandlingerMedLangFristSenereEnn/{valideringsdato}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function creates dismissal tasks for treatments whose task deadline is after the supplied validation date.
- Invocation:
  Step 1: `POST /api/satsendring/henleggBehandlingerMedLangFristSenereEnn/{valideringsdato}`
- Constraints:
  The core request body must be a set of treatment ids as strings. `{valideringsdato}` must parse as a date after one month from today.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Validation date is invalid or not more than one month ahead. This intentionally creates or omits the state described by: Invalid or too-early `{valideringsdato}`.
  - Failure endpoint:
    `POST /api/satsendring/henleggBehandlingerMedLangFristSenereEnn/{valideringsdato}`
  - Why this fails:
    The controller returns bad request when the date is invalid.
  - Intentionally violated constraints:
    Invalid or too-early `{valideringsdato}`.

Endpoint coverage:
- Covers:
  `POST /api/satsendring/henleggBehandlingerMedLangFristSenereEnn/{valideringsdato}`
- Distinct meaning:
  Queues technical-maintenance dismissals for treatments.

### Function 127: find ongoing cases without latest rate

Function name:
find cases without latest rate

Core endpoint(s):
- `POST /api/satsendring/saker-uten-sats`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function starts asynchronous identification of ongoing fagsaker missing the latest rate and returns a call id.
- Invocation:
  Step 1: `POST /api/satsendring/saker-uten-sats`
- Constraints:
  No case-specific prerequisite is required.

Failure or exceptional branches:
- None identified in controller source.

Endpoint coverage:
- Covers:
  `POST /api/satsendring/saker-uten-sats`
- Distinct meaning:
  Starts background search for cases without latest rate.

### Function 128: run consistency reconciliation dry run

Function name:
run consistency dry run

Core endpoint(s):
- `POST /api/konsistensavstemming/dryrun`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function creates a consistency-reconciliation task without sending to the economy system.
- Invocation:
  Step 1: `POST /api/konsistensavstemming/dryrun`
- Constraints:
  No body is required.

Failure or exceptional branches:
- None identified beyond task/batch persistence failures.

Endpoint coverage:
- Covers:
  `POST /api/konsistensavstemming/dryrun`
- Distinct meaning:
  Starts reconciliation dry run.

### Function 129: run consistency reconciliation

Function name:
run consistency reconciliation

Core endpoint(s):
- `POST /api/konsistensavstemming/run`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function creates a consistency-reconciliation task that sends to the economy system.
- Invocation:
  Step 1: `POST /api/konsistensavstemming/run`
- Constraints:
  Body must include `triggerTid`, which becomes the task trigger time and reconciliation date.

Failure or exceptional branches:
- None identified beyond task/batch persistence failures.

Endpoint coverage:
- Covers:
  `POST /api/konsistensavstemming/run`
- Distinct meaning:
  Starts real reconciliation.

### Function 130: retrieve internal statistics

Function name:
retrieve internal statistics

Core endpoint(s):
- `GET /api/internstatistikk`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns counts for fagsaker and unfinished treatments.
- Invocation:
  Step 1: `GET /api/internstatistikk`
- Constraints:
  No resource-specific prerequisite is required.

Failure or exceptional branches:
- None identified in controller source.

Endpoint coverage:
- Covers:
  `GET /api/internstatistikk`
- Distinct meaning:
  Reads internal aggregate statistics.

### Function 131: retrieve application statistics

Function name:
retrieve application statistics

Core endpoint(s):
- `GET /api/internstatistikk/antallSoknader`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns application statistics for a date range.
- Invocation:
  Step 1: `GET /api/internstatistikk/antallSoknader`
- Constraints:
  Optional query parameters `fom` and `tom` are dates. If omitted, the implementation defaults to the current four-month period.

Failure or exceptional branches:
- None identified beyond date parsing failures.

Endpoint coverage:
- Covers:
  `GET /api/internstatistikk/antallSoknader`
- Distinct meaning:
  Reads application statistics.

### Function 132: retrieve treatment statistics message

Function name:
retrieve treatment statistics

Core endpoint(s):
- `GET /api/saksstatistikk/behandling/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function maps a treatment to a DVH treatment statistics payload.
- Invocation:
  Step 1: `GET /api/saksstatistikk/behandling/{behandlingId}`
- Constraints:
  `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Statistics mapping fails. This intentionally creates or omits the state described by: Missing/incomplete treatment state for mapping.
  - Failure endpoint:
    `GET /api/saksstatistikk/behandling/{behandlingId}`
  - Why this fails:
    Controller logs and rethrows mapping errors.
  - Intentionally violated constraints:
    Missing/incomplete treatment state for mapping.

Endpoint coverage:
- Covers:
  `GET /api/saksstatistikk/behandling/{behandlingId}`
- Distinct meaning:
  Maps treatment statistics.

### Function 133: retrieve case statistics message

Function name:
retrieve case statistics

Core endpoint(s):
- `GET /api/saksstatistikk/sak/{fagsakId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function maps a fagsak to a DVH case statistics payload.
- Invocation:
  Step 1: `GET /api/saksstatistikk/sak/{fagsakId}`
- Constraints:
  `{fagsakId}` must come from the relevant setup response.

Failure or exceptional branches:
- None identified beyond mapping errors.

Endpoint coverage:
- Covers:
  `GET /api/saksstatistikk/sak/{fagsakId}`
- Distinct meaning:
  Maps case statistics.

### Function 134: register statistics message as sent

Function name:
register statistics sent

Core endpoint(s):
- `POST /api/saksstatistikk/registrer-sendt-fra-statistikk`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function stores an intermediate statistics record marked with sent timestamp so it is not resent.
- Invocation:
  Step 1: `POST /api/saksstatistikk/registrer-sendt-fra-statistikk`
- Constraints:
  Body must include `offset`, `type`, `json`, and `sendtTidspunkt`. `json` must contain `funksjonellId`, `versjon`, and either `sakId` or `behandlingId` depending on `type`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - JSON lacks required fields. This intentionally creates or omits the state described by: Missing `funksjonellId`, `versjon`, `sakId`, or `behandlingId`.
  - Failure endpoint:
    `POST /api/saksstatistikk/registrer-sendt-fra-statistikk`
  - Why this fails:
    The controller reads required JSON nodes and rethrows parsing/null errors.
  - Intentionally violated constraints:
    Missing `funksjonellId`, `versjon`, `sakId`, or `behandlingId`.

Endpoint coverage:
- Covers:
  `POST /api/saksstatistikk/registrer-sendt-fra-statistikk`
- Distinct meaning:
  Marks statistics message as sent.

### Function 135: retrieve benefit statistics decisions

Function name:
retrieve benefit statistics decisions

Core endpoint(s):
- `POST /api/stonadsstatistikk/vedtakV2`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function returns DVH V2 decision payloads for a list of treatments.
- Invocation:
  Step 1: `POST /api/stonadsstatistikk/vedtakV2`
- Constraints:
  The core request body must be a list containing treatment ids such as the `{behandlingId}` from the treatment setup response.

Failure or exceptional branches:
- None identified beyond mapping errors.

Endpoint coverage:
- Covers:
  `POST /api/stonadsstatistikk/vedtakV2`
- Distinct meaning:
  Maps treatment ids to benefit statistics payloads.

### Function 136: queue unsent benefit statistics

Function name:
queue unsent benefit statistics

Core endpoint(s):
- `POST /api/stonadsstatistikk/send-til-dvh`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function creates publish tasks for treatment statistics that have not already been sent.
- Invocation:
  Step 1: `POST /api/stonadsstatistikk/send-til-dvh`
- Constraints:
  The core request body must be a list of treatment ids. Existing sent messages are skipped.

Failure or exceptional branches:
- None identified beyond mapping/task failures.

Endpoint coverage:
- Covers:
  `POST /api/stonadsstatistikk/send-til-dvh`
- Distinct meaning:
  Queues only unsent benefit statistics.

### Function 137: queue benefit statistics manually

Function name:
queue benefit statistics manually

Core endpoint(s):
- `POST /api/stonadsstatistikk/send-til-dvh-manuell`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function creates publish tasks for the supplied treatments without checking whether they were already sent.
- Invocation:
  Step 1: `POST /api/stonadsstatistikk/send-til-dvh-manuell`
- Constraints:
  The core request body must be a list of treatment ids.

Failure or exceptional branches:
- None identified beyond mapping/task failures.

Endpoint coverage:
- Covers:
  `POST /api/stonadsstatistikk/send-til-dvh-manuell`
- Distinct meaning:
  Manually queues benefit statistics.

### Function 138: resend manual migration statistics

Function name:
resend migration statistics

Core endpoint(s):
- `POST /api/stonadsstatistikk/ettersend-manuell-migrering/{dryRun}`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function scans manual migration treatments and creates publish tasks for eligible unsent implemented treatments unless dry-run is true.
- Invocation:
  Step 1: `POST /api/stonadsstatistikk/ettersend-manuell-migrering/{dryRun}`
- Constraints:
  `{dryRun}` controls whether tasks are actually created.

Failure or exceptional branches:
- None identified in controller source.

Endpoint coverage:
- Covers:
  `POST /api/stonadsstatistikk/ettersend-manuell-migrering/{dryRun}`
- Distinct meaning:
  Backfills benefit statistics for manual migrations.

### Function 139: finish admin task list

Function name:
finish admin task list

Core endpoint(s):
- `POST /api/forvalter/ferdigstill-oppgaver`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function attempts to complete each task id in the request and reports how many failed.
- Invocation:
  Step 1: `POST /api/forvalter/ferdigstill-oppgaver`
- Constraints:
  Body must be a list of task ids.

Failure or exceptional branches:
- None identified as endpoint failure; per-task errors are counted in the success response.

Endpoint coverage:
- Covers:
  `POST /api/forvalter/ferdigstill-oppgaver`
- Distinct meaning:
  Administrative bulk task completion.

### Function 140: restart small child supplement job

Function name:
restart small child supplement job

Core endpoint(s):
- `POST /api/forvalter/start-manuell-restart-av-smaabarnstillegg-jobb/skalOppretteOppgaver/{skalOppretteOppgaver}`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function triggers manual restart logic for small child supplement cases.
- Invocation:
  Step 1: `POST /api/forvalter/start-manuell-restart-av-smaabarnstillegg-jobb/skalOppretteOppgaver/{skalOppretteOppgaver}`
- Constraints:
  `{skalOppretteOppgaver}` controls whether tasks are created.

Failure or exceptional branches:
- None identified in controller source.

Endpoint coverage:
- Covers:
  `POST /api/forvalter/start-manuell-restart-av-smaabarnstillegg-jobb/skalOppretteOppgaver/{skalOppretteOppgaver}`
- Distinct meaning:
  Administrative restart of supplement job.

### Function 141: send payment orders administratively

Function name:
send payment orders administratively

Core endpoint(s):
- `POST /api/forvalter/lag-og-send-utbetalingsoppdrag-til-økonomi`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function attempts to generate and send payment orders to the economy system for supplied treatments.
- Invocation:
  Step 1: `POST /api/forvalter/lag-og-send-utbetalingsoppdrag-til-økonomi`
- Constraints:
  Body must be a set of treatment ids. Per-treatment errors are logged and do not fail the whole endpoint.

Failure or exceptional branches:
- None identified as endpoint failure; per-treatment failures are swallowed/logged.

Endpoint coverage:
- Covers:
  `POST /api/forvalter/lag-og-send-utbetalingsoppdrag-til-økonomi`
- Distinct meaning:
  Administrative payment-order sending.

### Function 142: run rate change without validation

Function name:
run rate change without validation

Core endpoint(s):
- `POST /api/forvalter/kjor-satsendring-uten-validering`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function runs simplified rate change for supplied fagsaker without the normal validation path.
- Invocation:
  Step 1: `POST /api/forvalter/kjor-satsendring-uten-validering`
- Constraints:
  Body must be a list of fagsak ids. Per-case failures are logged.

Failure or exceptional branches:
- None identified as endpoint failure; per-case exceptions are caught.

Endpoint coverage:
- Covers:
  `POST /api/forvalter/kjor-satsendring-uten-validering`
- Distinct meaning:
  Administrative unvalidated rate change.

### Function 143: identify payments over 100 percent

Function name:
identify payments over 100 percent

Core endpoint(s):
- `POST /api/forvalter/identifiser-utbetalinger-over-100-prosent`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function starts background identification of payments over 100 percent and returns a call id.
- Invocation:
  Step 1: `POST /api/forvalter/identifiser-utbetalinger-over-100-prosent`
- Constraints:
  No body is required.

Failure or exceptional branches:
- None identified in controller source.

Endpoint coverage:
- Covers:
  `POST /api/forvalter/identifiser-utbetalinger-over-100-prosent`
- Distinct meaning:
  Starts administrative background analysis.

### Function 144: find treatments with potentially incorrect payment orders

Function name:
find payment-order issues

Core endpoint(s):
- `POST /api/forvalter/finnBehandlingerMedPotensieltFeilUtbetalingsoppdrag`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function identifies treatments that may require payment-order patching.
- Invocation:
  Step 1: `POST /api/forvalter/finnBehandlingerMedPotensieltFeilUtbetalingsoppdrag`
- Constraints:
  No body is required.

Failure or exceptional branches:
- None identified beyond service validation failures.

Endpoint coverage:
- Covers:
  `POST /api/forvalter/finnBehandlingerMedPotensieltFeilUtbetalingsoppdrag`
- Distinct meaning:
  Administrative identification of payment-order issues.

### Function 145: check treatments for incorrect cessation date

Function name:
check incorrect cessation dates

Core endpoint(s):
- `POST /api/forvalter/sjekkOmTilkjentYtelseForBehandlingHarUkorrektOpphørsdato`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function validates payment-order cessation dates for supplied treatments and returns those with errors.
- Invocation:
  Step 1: `POST /api/forvalter/sjekkOmTilkjentYtelseForBehandlingHarUkorrektOpphørsdato`
- Constraints:
  Body must be a list of treatment ids.

Failure or exceptional branches:
- None identified beyond service validation failures.

Endpoint coverage:
- Covers:
  `POST /api/forvalter/sjekkOmTilkjentYtelseForBehandlingHarUkorrektOpphørsdato`
- Distinct meaning:
  Administrative validation of cessation dates.

### Function 146: resend corrected payment orders for treatments

Function name:
resend corrected payment orders

Core endpoint(s):
- `POST /api/forvalter/sendKorrigertUtbetalingsoppdragForBehandlinger`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function attempts to generate and implement corrected payment orders for a list of treatments and returns successes and failures.
- Invocation:
  Step 1: `POST /api/forvalter/sendKorrigertUtbetalingsoppdragForBehandlinger`
- Constraints:
  Body must be a list of treatment ids.

Failure or exceptional branches:
- None identified as endpoint failure; per-treatment failures are returned in `harFeil`.

Endpoint coverage:
- Covers:
  `POST /api/forvalter/sendKorrigertUtbetalingsoppdragForBehandlinger`
- Distinct meaning:
  Bulk corrected payment-order resend.

### Function 147: resend corrected payment order for one treatment version

Function name:
resend corrected payment order version

Core endpoint(s):
- `POST /api/forvalter/sendKorrigertUtbetalingsoppdragForBehandling/{behandlingId}/{versjon}`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function attempts corrected payment-order implementation for one treatment and version.
- Invocation:
  Step 1: `POST /api/forvalter/sendKorrigertUtbetalingsoppdragForBehandling/{behandlingId}/{versjon}`
- Constraints:
  `{behandlingId}` identifies the treatment; `{versjon}` selects the payment-order version.

Failure or exceptional branches:
- None identified as endpoint failure; failures are returned in `harFeil`.

Endpoint coverage:
- Covers:
  `POST /api/forvalter/sendKorrigertUtbetalingsoppdragForBehandling/{behandlingId}/{versjon}`
- Distinct meaning:
  Single corrected payment-order resend.

### Function 148: populate support start/end for one treatment

Function name:
populate support dates for treatment

Core endpoint(s):
- `POST /api/forvalter/populer-stonad-fom-tom/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function populates support-from and support-to dates for one treatment.
- Invocation:
  Step 1: `POST /api/forvalter/populer-stonad-fom-tom/{behandlingId}`
- Constraints:
  `{behandlingId}` must come from the treatment setup response.

Failure or exceptional branches:
- None identified beyond service failures.

Endpoint coverage:
- Covers:
  `POST /api/forvalter/populer-stonad-fom-tom/{behandlingId}`
- Distinct meaning:
  Administrative support-date population for one treatment.

### Function 149: populate support start/end for multiple treatments

Function name:
populate support dates in bulk

Core endpoint(s):
- `POST /api/forvalter/populer-stonad-fom-tom-alle/{limit}`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function finds active treatments missing support end date up to `limit` and attempts to populate dates.
- Invocation:
  Step 1: `POST /api/forvalter/populer-stonad-fom-tom-alle/{limit}`
- Constraints:
  `{limit}` controls how many treatments are processed. Per-treatment failures are logged.

Failure or exceptional branches:
- None identified as endpoint failure.

Endpoint coverage:
- Covers:
  `POST /api/forvalter/populer-stonad-fom-tom-alle/{limit}`
- Distinct meaning:
  Bulk administrative support-date population.

### Function 150: find cases that should be closed

Function name:
find cases to close

Core endpoint(s):
- `GET /api/forvalter/finnFagsakerSomSkalAvsluttes`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function returns fagsak ids that should be closed.
- Invocation:
  Step 1: `GET /api/forvalter/finnFagsakerSomSkalAvsluttes`
- Constraints:
  No resource-specific prerequisite is required.

Failure or exceptional branches:
- None identified in controller source.

Endpoint coverage:
- Covers:
  `GET /api/forvalter/finnFagsakerSomSkalAvsluttes`
- Distinct meaning:
  Administrative discovery of closable cases.

### Function 151: update ongoing status on cases

Function name:
update case ongoing status

Core endpoint(s):
- `POST /api/forvalter/oppdaterLøpendeStatusPåFagsaker`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function updates ongoing/closed status for cases that should be closed.
- Invocation:
  Step 1: `POST /api/forvalter/oppdaterLøpendeStatusPåFagsaker`
- Constraints:
  No request body is used.

Failure or exceptional branches:
- None identified in controller source.

Endpoint coverage:
- Covers:
  `POST /api/forvalter/oppdaterLøpendeStatusPåFagsaker`
- Distinct meaning:
  Administrative fagsak status update.

### Function 152: find open cases with multiple migration treatments and ongoing Infotrygd case

Function name:
find migration duplicates with Infotrygd

Core endpoint(s):
- `GET /api/forvalter/finnÅpneFagsakerMedFlereMigreringsbehandlingerOgLøpendeSakIInfotrygd`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function lists open fagsaker with multiple migration treatments and an ongoing Infotrygd case.
- Invocation:
  Step 1: `GET /api/forvalter/finnÅpneFagsakerMedFlereMigreringsbehandlingerOgLøpendeSakIInfotrygd`
- Constraints:
  No resource-specific prerequisite is required.

Failure or exceptional branches:
- None identified in controller source.

Endpoint coverage:
- Covers:
  `GET /api/forvalter/finnÅpneFagsakerMedFlereMigreringsbehandlingerOgLøpendeSakIInfotrygd`
- Distinct meaning:
  Administrative migration anomaly query.

### Function 153: find open cases with multiple migration treatments

Function name:
find migration duplicates

Core endpoint(s):
- `GET /api/forvalter/finnÅpneFagsakerMedFlereMigreringsbehandlinger`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function lists open fagsaker with multiple migration treatments.
- Invocation:
  Step 1: `GET /api/forvalter/finnÅpneFagsakerMedFlereMigreringsbehandlinger`
- Constraints:
  No resource-specific prerequisite is required.

Failure or exceptional branches:
- None identified in controller source.

Endpoint coverage:
- Covers:
  `GET /api/forvalter/finnÅpneFagsakerMedFlereMigreringsbehandlinger`
- Distinct meaning:
  Administrative migration anomaly query.

### Function 154: fill empty condition start dates in preprod

Function name:
fill condition dates in preprod

Core endpoint(s):
- `PUT /api/preprod/{behandlingId}/fyll-ut-vilkarsvurdering`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function fills missing condition start dates with birth dates for a treatment in preprod/local environments.
- Invocation:
  Step 1: `PUT /api/preprod/{behandlingId}/fyll-ut-vilkarsvurdering`
- Constraints:
  Environment must be preprod or dev-postgres-preprod, not prod.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Endpoint is called in prod or another unsupported profile. This intentionally creates or omits the state described by: Unsupported runtime profile.
  - Failure endpoint:
    `PUT /api/preprod/{behandlingId}/fyll-ut-vilkarsvurdering`
  - Why this fails:
    The controller explicitly throws when active profiles are prod or not preprod/dev-postgres-preprod.
  - Intentionally violated constraints:
    Unsupported runtime profile.

Endpoint coverage:
- Covers:
  `PUT /api/preprod/{behandlingId}/fyll-ut-vilkarsvurdering`
- Distinct meaning:
  Preprod-only test data mutation.

### Function 155: trigger test autobrev scheduler

Function name:
trigger test autobrev scheduler

Core endpoint(s):
- `GET /internal/autobrev`
- `GET /testverktoy/autobrev`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function creates an autobrev scheduler task in dev/preprod; in prod it returns a no-op message.
- Invocation:
  Step 1: `GET /internal/autobrev`
- Constraints:
  Same implementation is also exposed as `GET /testverktoy/autobrev`.

Failure or exceptional branches:
- None identified beyond implementation-level access, lookup, validation, or external integration failures.

Endpoint coverage:
- Covers:
  `GET /internal/autobrev`
  `GET /testverktoy/autobrev`
- Distinct meaning:
  Internal/test scheduler trigger.

### Function 156: trigger test rate change

Function name:
trigger test rate change

Core endpoint(s):
- `GET /internal/test-satsendring/{fagsakId}`
- `GET /testverktoy/test-satsendring/{fagsakId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.

Successful execution:
- Result:
  This function creates a satsendring task in dev/preprod; in prod it returns a no-op message.
- Invocation:
  Step 1: `GET /internal/test-satsendring/{fagsakId}`
- Constraints:
  Same implementation is also exposed as `GET /testverktoy/test-satsendring/{fagsakId}`. `{fagsakId}` must identify a fagsak.

Failure or exceptional branches:
- None identified beyond fagsak/task failures in non-prod.

Endpoint coverage:
- Covers:
  `GET /internal/test-satsendring/{fagsakId}`
  `GET /testverktoy/test-satsendring/{fagsakId}`
- Distinct meaning:
  Internal/test rate-change trigger.

### Function 157: trigger test transitional-benefit event

Function name:
trigger test transitional event

Core endpoint(s):
- `POST /internal/vedtak-om-overgangsstønad`
- `POST /testverktoy/vedtak-om-overgangsstønad`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function runs small child supplement treatment logic for a transitional-benefit event in dev/preprod; in prod it returns a no-op message.
- Invocation:
  Step 1: `POST /internal/vedtak-om-overgangsstønad`
- Constraints:
  Same implementation is also exposed as `POST /testverktoy/vedtak-om-overgangsstønad`. Body must be `PersonIdent`.

Failure or exceptional branches:
- None identified beyond person/service failures in non-prod.

Endpoint coverage:
- Covers:
  `POST /internal/vedtak-om-overgangsstønad`
  `POST /testverktoy/vedtak-om-overgangsstønad`
- Distinct meaning:
  Internal/test transitional-benefit event trigger.

### Function 158: trigger test birth event

Function name:
trigger test birth event

Core endpoint(s):
- `POST /internal/foedselshendelse`
- `POST /testverktoy/foedselshendelse`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function creates a birth-event processing task in dev/preprod; in prod it returns a no-op message.
- Invocation:
  Step 1: `POST /internal/foedselshendelse`
- Constraints:
  Same implementation is also exposed as `POST /testverktoy/foedselshendelse`. Body must include `morsIdent` and `barnasIdenter`.

Failure or exceptional branches:
- None identified beyond task creation failures in non-prod.

Endpoint coverage:
- Covers:
  `POST /internal/foedselshendelse`
  `POST /testverktoy/foedselshendelse`
- Distinct meaning:
  Internal/test birth-event trigger.

### Function 159: trigger internal consistency reconciliation tasks

Function name:
trigger internal reconciliation tasks

Core endpoint(s):
- `GET /internal/kjor-intern-konsistensavstemming/{maksAntallTasker}`
- `GET /testverktoy/kjor-intern-konsistensavstemming/{maksAntallTasker}`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function creates internal reconciliation tasks in dev/preprod; in prod it returns a no-op message.
- Invocation:
  Step 1: `GET /internal/kjor-intern-konsistensavstemming/{maksAntallTasker}`
- Constraints:
  Same implementation is also exposed as `GET /testverktoy/kjor-intern-konsistensavstemming/{maksAntallTasker}`. `{maksAntallTasker}` controls task count.

Failure or exceptional branches:
- None identified beyond task creation failures in non-prod.

Endpoint coverage:
- Covers:
  `GET /internal/kjor-intern-konsistensavstemming/{maksAntallTasker}`
  `GET /testverktoy/kjor-intern-konsistensavstemming/{maksAntallTasker}`
- Distinct meaning:
  Internal/test reconciliation task trigger.

### Function 160: trigger taking treatments off wait after deadline

Function name:
trigger wait-deadline resume task

Core endpoint(s):
- `GET /internal/ta-behandlinger-etter-ventefrist-av-vent`
- `GET /testverktoy/ta-behandlinger-etter-ventefrist-av-vent`

Preconditions:
- None beyond a valid request payload, required caller permissions, and any external lookup or integration state described in constraints.

Successful execution:
- Result:
  This function creates a task to take treatments off wait after their wait deadline in dev/preprod; in prod it returns a no-op message.
- Invocation:
  Step 1: `GET /internal/ta-behandlinger-etter-ventefrist-av-vent`
- Constraints:
  Same implementation is also exposed as `GET /testverktoy/ta-behandlinger-etter-ventefrist-av-vent`.

Failure or exceptional branches:
- None identified beyond task creation failures in non-prod.

Endpoint coverage:
- Covers:
  `GET /internal/ta-behandlinger-etter-ventefrist-av-vent`
  `GET /testverktoy/ta-behandlinger-etter-ventefrist-av-vent`
- Distinct meaning:
  Internal/test wait-deadline task trigger.

### Function 161: retrieve raw simulation for treatment in test tool

Function name:
retrieve test simulation

Core endpoint(s):
- `GET /internal/hent-simulering-pa-behandling/{behandlingId}`
- `GET /testverktoy/hent-simulering-pa-behandling/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function retrieves raw simulation receivers for a treatment.
- Invocation:
  Step 1: `GET /internal/hent-simulering-pa-behandling/{behandlingId}`
- Constraints:
  Same implementation is also exposed as `GET /testverktoy/hent-simulering-pa-behandling/{behandlingId}`.

Failure or exceptional branches:
- None identified beyond missing simulation/treatment failures.

Endpoint coverage:
- Covers:
  `GET /internal/hent-simulering-pa-behandling/{behandlingId}`
  `GET /testverktoy/hent-simulering-pa-behandling/{behandlingId}`
- Distinct meaning:
  Internal/test simulation read.

### Function 162: retrieve explanation test text

Function name:
retrieve explanation test text

Core endpoint(s):
- `GET /internal/behandling/{behandlingId}/begrunnelsetest`
- `GET /testverktoy/behandling/{behandlingId}/begrunnelsetest`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function returns generated explanation test text for a treatment in dev/preprod.
- Invocation:
  Step 1: `GET /internal/behandling/{behandlingId}/begrunnelsetest`
- Constraints:
  Same implementation is also exposed as `GET /testverktoy/behandling/{behandlingId}/begrunnelsetest`. Environment must be dev/preprod.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Endpoint is called in prod. This intentionally creates or omits the state described by: Prod environment.
  - Failure endpoint:
    `GET /internal/behandling/{behandlingId}/begrunnelsetest`
  - Why this fails:
    The controller throws BAD_REQUEST in prod.
  - Intentionally violated constraints:
    Prod environment.

Endpoint coverage:
- Covers:
  `GET /internal/behandling/{behandlingId}/begrunnelsetest`
  `GET /testverktoy/behandling/{behandlingId}/begrunnelsetest`
- Distinct meaning:
  Internal/test explanation output.

### Function 163: retrieve decision-period test text

Function name:
retrieve decision-period test text

Core endpoint(s):
- `GET /internal/behandling/{behandlingId}/vedtaksperiodertest`
- `GET /testverktoy/behandling/{behandlingId}/vedtaksperiodertest`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function returns generated decision-period test text for a treatment in dev/preprod.
- Invocation:
  Step 1: `GET /internal/behandling/{behandlingId}/vedtaksperiodertest`
- Constraints:
  Same implementation is also exposed as `GET /testverktoy/behandling/{behandlingId}/vedtaksperiodertest`. Environment must be dev/preprod.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Endpoint is called in prod. This intentionally creates or omits the state described by: Prod environment.
  - Failure endpoint:
    `GET /internal/behandling/{behandlingId}/vedtaksperiodertest`
  - Why this fails:
    The controller throws BAD_REQUEST in prod.
  - Intentionally violated constraints:
    Prod environment.

Endpoint coverage:
- Covers:
  `GET /internal/behandling/{behandlingId}/vedtaksperiodertest`
  `GET /testverktoy/behandling/{behandlingId}/vedtaksperiodertest`
- Distinct meaning:
  Internal/test decision-period output.

### Function 164: redirect to frontend treatment URL

Function name:
redirect to treatment UI

Core endpoint(s):
- `GET /internal/redirect/behandling/{behandlingId}`
- `GET /testverktoy/redirect/behandling/{behandlingId}`

Preconditions:
- A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
- A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.

Successful execution:
- Result:
  This function returns a 302 redirect to the frontend URL for a treatment.
- Invocation:
  Step 1: `GET /internal/redirect/behandling/{behandlingId}`
- Constraints:
  Same implementation is also exposed as `GET /testverktoy/redirect/behandling/{behandlingId}`. Hostname is derived from environment.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A fagsak exists for the relevant person identity, fagsak type, and institution scope. This can be satisfied by directly inserting the fagsak, actor, and person-ident state with matching type and institution scope, or by calling `POST /api/fagsaker`. The returned fagsak id must be reused as the {fagsakId} or request body fagsak reference for the core endpoint.
    - A treatment exists on the relevant fagsak. This can be satisfied by directly inserting a Behandling row linked to the fagsak with matching treatment type, reason, applicant, and required dates, or by calling `POST /api/behandlinger`. The returned treatment id must be reused as {behandlingId} for the core endpoint.
    - Environment profile cannot be mapped to a frontend hostname. This intentionally creates or omits the state described by: Unsupported environment.
  - Failure endpoint:
    `GET /internal/redirect/behandling/{behandlingId}`
  - Why this fails:
    The controller raises an error when it cannot derive the runtime environment.
  - Intentionally violated constraints:
    Unsupported environment.

Endpoint coverage:
- Covers:
  `GET /internal/redirect/behandling/{behandlingId}`
  `GET /testverktoy/redirect/behandling/{behandlingId}`
- Distinct meaning:
  Redirects to treatment UI.

### Auxiliary endpoints not converted to functions

The Swagger file documents these endpoints, but no controller implementation is present under this project’s `src` directory. They appear to come from a shared task-processing dependency, so implementation branches cannot be inferred from the allowed source files:

- `GET /api/task/{id}`
- `GET /api/task/v2`
- `GET /api/task/logg/{id}`
- `GET /api/task/ferdigNaaFeiletFoer`
- `GET /api/task/callId/{callId}`
- `GET /api/task/antall-til-oppfolging`
- `PUT /api/task/rekjor`
- `PUT /api/task/rekjorAlle`
- `PUT /api/task/kommenter`
- `PUT /api/task/avvikshaandter`
