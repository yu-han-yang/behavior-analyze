# Domain-Level Behavior Analysis

## Domain Summary

The `tiltaksgjennomforing` service exposes the `Tiltaksgjennomføring API` API. This analysis is derived from the service OpenAPI/Swagger contract at `tiltaksgjennomforing.json` and the endpoint structure it declares.

The core business concepts are:

- avtale-controller: endpoint group for avtale controller behavior.
- be-om-altinn-rettighet-urler-controller: endpoint group for be om altinn rettighet urler controller behavior.
- feature-toggle-controller: endpoint group for feature toggle controller behavior.
- innlogget-bruker-controller: endpoint group for innlogget bruker controller behavior.
- internal-avtale-controller: endpoint group for internal avtale controller behavior.
- health-check-controller: endpoint group for health check controller behavior.
- kodeverk-controller: endpoint group for kodeverk controller behavior.
- organisasjon-controller: endpoint group for organisasjon controller behavior.
- admin-controller: endpoint group for admin controller behavior.
- avtale-hendelse-controller: endpoint group for avtale hendelse controller behavior.
- internal-dvh-melding-produsent-controller: endpoint group for internal dvh melding produsent controller behavior.
- varsel-controller: endpoint group for varsel controller behavior.

Contract-level limits: this document captures externally visible business behavior from the API contract. Implementation-only validation, persistence side effects, authorization rules, and asynchronous processing details may be stricter than what is visible here.

## Available Function Inventory

### admin-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `annuller og generer behandlet iarena perioder` | `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}` | Creates or executes dato business state. |
| `annuller og generer tilskuddsperiode` | `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}` | Creates or executes tilskuddsperiode id business state. |
| `annuller og resend tilskuddsperiode` | `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}` | Creates or executes tilskuddsperiode id business state. |
| `annuller tilskuddsperiode` | `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}` | Creates or executes tilskuddsperiode id business state. |
| `finn tilskuddsperioder med feil datoer` | `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer` | Creates or executes finn avtaler med tilskuddsperioder feil datoer business state. |
| `lag tilskuddsperioder p en avtale` | `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}` | Creates or executes migrerings dato business state. |
| `reberegn l nnstilskudd` | `POST /utvikler-admin/reberegn` | Creates or executes reberegn business state. |
| `reberegn varig l nnstilskudd som ikke har redusert dato dry run` | `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}` | Creates or executes migrerings dato business state. |
| `reberegn varig l nnstilskudd som ikke har redusert dato` | `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}` | Creates or executes migrerings dato business state. |
| `reberegn ubehandlede tilskuddsperioder` | `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}` | Creates or executes avtale id business state. |

### avtale-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `hent alle avtaler innlogget bruker har tilgang til` | `GET /avtaler` | Reads avtaler information. |
| `opprett avtale som veileder` | `POST /avtaler` | Creates or executes avtaler business state. |
| `hent fra avtale nr` | `GET /avtaler/avtaleNr/{avtaleNr}` | Reads avtale nr information. |
| `finn godkjente avtaler med tilskuddsperiodestatus og nav enheter liste` | `GET /avtaler/beslutter-liste` | Reads beslutter liste information. |
| `sjekk om deltaker allerede er registrert paa tiltak` | `GET /avtaler/deltaker-allerede-paa-tiltak` | Reads deltaker allerede paa tiltak information. |
| `hent alle avtaler for min side arbeidsgiver` | `GET /avtaler/min-side-arbeidsgiver` | Reads min side arbeidsgiver information. |
| `opprett mentor avtale` | `POST /avtaler/opprett-mentor-avtale` | Creates or executes opprett mentor avtale business state. |
| `opprett avtale som arbeidsgiver` | `POST /avtaler/opprett-som-arbeidsgiver` | Creates or executes opprett som arbeidsgiver business state. |
| `hent alle avtaler innlogget bruker har tilgang til med get` | `GET /avtaler/sok` | Reads sok information. |
| `hent alle avtaler innlogget bruker har tilgang til med post` | `POST /avtaler/sok` | Creates or executes sok business state. |
| `hent` | `GET /avtaler/{avtaleId}` | Reads avtale id information. |
| `endre avtale` | `PUT /avtaler/{avtaleId}` | Replaces avtale id state. |
| `annuller` | `POST /avtaler/{avtaleId}/annuller` | Creates or executes annuller business state. |
| `avsl tilskuddsperiode` | `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` | Creates or executes avslag tilskuddsperiode business state. |
| `del avtale med avtalepart` | `POST /avtaler/{avtaleId}/del-med-avtalepart` | Creates or executes del med avtalepart business state. |
| `endre avtale dry run` | `PUT /avtaler/{avtaleId}/dry-run` | Replaces dry run state. |
| `endre inkluderingstilskudd` | `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd` | Creates or executes endre inkluderingstilskudd business state. |
| `endre kontaktinfo` | `POST /avtaler/{avtaleId}/endre-kontaktinfo` | Creates or executes endre kontaktinfo business state. |
| `endre kostnadssted` | `POST /avtaler/{avtaleId}/endre-kostnadssted` | Creates or executes endre kostnadssted business state. |
| `endre m l` | `POST /avtaler/{avtaleId}/endre-maal` | Creates or executes endre maal business state. |
| `endre om mentor` | `POST /avtaler/{avtaleId}/endre-om-mentor` | Creates or executes endre om mentor business state. |
| `endre oppf lging og tilrettelegging` | `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging` | Creates or executes endre oppfolging og tilrettelegging business state. |
| `endre stillingbeskrivelse` | `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse` | Creates or executes endre stillingbeskrivelse business state. |
| `endre tilskuddsberegning` | `POST /avtaler/{avtaleId}/endre-tilskuddsberegning` | Creates or executes endre tilskuddsberegning business state. |
| `endre tilskuddsberegning dry run` | `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run` | Creates or executes endre tilskuddsberegning dry run business state. |
| `forkort avtale` | `POST /avtaler/{avtaleId}/forkort` | Creates or executes forkort business state. |
| `forkort avtale dry run` | `POST /avtaler/{avtaleId}/forkort-dry-run` | Creates or executes forkort dry run business state. |
| `forleng avtale` | `POST /avtaler/{avtaleId}/forleng` | Creates or executes forleng business state. |
| `forleng avtale dry run` | `POST /avtaler/{avtaleId}/forleng-dry-run` | Creates or executes forleng dry run business state. |
| `godkjenn` | `POST /avtaler/{avtaleId}/godkjenn` | Creates or executes godkjenn business state. |
| `godkjenn pa vegne av 1` | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av` | Creates or executes godkjenn paa vegne av business state. |
| `godkjenn pa vegne av arbeidsgiver` | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver` | Creates or executes godkjenn paa vegne av arbeidsgiver business state. |
| `godkjenn pa vegne av` | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker` | Creates or executes godkjenn paa vegne av deltaker business state. |
| `godkjenn pa vegne av deltaker og arbeidsgiver` | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` | Creates or executes godkjenn paa vegne av deltaker og arbeidsgiver business state. |
| `godkjenn tilskuddsperiode` | `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode` | Creates or executes godkjenn tilskuddsperiode business state. |
| `juster arena migreringsdato` | `POST /avtaler/{avtaleId}/juster-arena-migreringsdato` | Creates or executes juster arena migreringsdato business state. |
| `juster arena migreringsdato dry run` | `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run` | Creates or executes dry run business state. |
| `hent bedrift kontonummer` | `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver` | Reads kontonummer arbeidsgiver information. |
| `mentor godkjenn taushetserkl ring` | `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring` | Creates or executes mentor godkjenn taushetserkl ring business state. |
| `oppdater oppf lgings enhet` | `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet` | Creates or executes oppdater oppf lgings enhet business state. |
| `opphev godkjenninger` | `POST /avtaler/{avtaleId}/opphev-godkjenninger` | Creates or executes opphev godkjenninger business state. |
| `sett ny veileder p avtale` | `PUT /avtaler/{avtaleId}/overta` | Replaces overta state. |
| `hent avtale pdf` | `GET /avtaler/{avtaleId}/pdf` | Reads pdf information. |
| `send tilbake til beslutter` | `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter` | Creates or executes send tilbake til beslutter business state. |
| `set om avtalen kan etterregistreres` | `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` | Creates or executes set om avtalen kan etterregistreres business state. |
| `slettemerk` | `POST /avtaler/{avtaleId}/slettemerk` | Creates or executes slettemerk business state. |
| `hent versjoner` | `GET /avtaler/{avtaleId}/versjoner` | Reads versjoner information. |
| `vis salesforce dialog` | `GET /avtaler/{avtaleId}/vis-salesforce-dialog` | Reads vis salesforce dialog information. |

### avtale-hendelse-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `dry send melding alle avtaler` | `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler` | Creates or executes dry send melding alle avtaler business state. |
| `send melding alle avtaler` | `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler` | Creates or executes send melding alle avtaler business state. |
| `send melding for en avtale` | `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}` | Creates or executes avtale id business state. |

### be-om-altinn-rettighet-urler-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `be om rettighet urler` | `GET /be-om-altinn-rettighet-urler` | Reads be om altinn rettighet urler information. |

### feature-toggle-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `feature` | `GET /feature` | Reads feature information. |
| `variant` | `GET /feature/variant` | Reads variant information. |

### health-check-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `healthcheck` | `GET /internal/healthcheck` | Reads healthcheck information. |

### innlogget-bruker-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `hent innlogget bruker` | `GET /innlogget-bruker` | Reads innlogget bruker information. |

### internal-avtale-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `hent ikke journalfoerte avtaler` | `GET /internal/avtaler` | Reads avtaler information. |
| `journalfoer avtaler` | `PUT /internal/avtaler` | Replaces avtaler state. |

### internal-dvh-melding-produsent-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `patche avtale` | `POST /utvikler-admin/dvh-melding/patch` | Creates or executes patch business state. |
| `patch alle avtaler` | `POST /utvikler-admin/dvh-melding/patchalleavtaler` | Creates or executes patchalleavtaler business state. |

### kodeverk-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `get` | `GET /kodeverk` | Reads kodeverk information. |
| `statuser` | `GET /kodeverk/statuser` | Reads statuser information. |
| `tiltakstyper` | `GET /kodeverk/tiltakstyper` | Reads tiltakstyper information. |

### organisasjon-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `hent virksomhet` | `GET /organisasjoner` | Reads organisasjoner information. |

### varsel-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `hent alle varsler for avtale` | `GET /varsler/avtale-logg` | Reads avtale logg information. |
| `hent varsler med bjelle for avtale` | `GET /varsler/avtale-modal` | Reads avtale modal information. |
| `hent varsler med bjelle for oversikt` | `GET /varsler/oversikt` | Reads oversikt information. |
| `sett flere varsler til lest` | `POST /varsler/sett-alle-til-lest` | Creates or executes sett alle til lest business state. |
| `sett til lest` | `POST /varsler/{varselId}/sett-til-lest` | Creates or executes sett til lest business state. |

## Supported Business Behaviors

### Behavior 1: Hent Alle Avtaler Innlogget Bruker Har Tilgang Til

Business goal:
Reads avtaler information.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `GET /avtaler`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent alle avtaler innlogget bruker har tilgang til` (`GET /avtaler`) with query: queryParametre required, sorteringskolonne optional, page optional, size optional; cookie: innlogget-part required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `queryParametre`, `sorteringskolonne`, `page`, `size` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `queryParametre`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent alle avtaler innlogget bruker har tilgang til`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 2: Opprett Avtale Som Veileder

Business goal:
Creates or executes avtaler business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `opprett avtale som veileder` (`POST /avtaler`) with query: ryddeavtale optional; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `ryddeavtale` filter, page, or modify the operation result.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `opprett avtale som veileder`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 3: Hent Fra Avtale Nr

Business goal:
Reads avtale nr information.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `GET /avtaler/avtaleNr/{avtaleNr}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent fra avtale nr` (`GET /avtaler/avtaleNr/{avtaleNr}`) with path: avtaleNr required; cookie: innlogget-part required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `avtaleNr` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `avtaleNr`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent fra avtale nr`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 4: Finn Godkjente Avtaler Med Tilskuddsperiodestatus Og Nav Enheter Liste

Business goal:
Reads beslutter liste information.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `GET /avtaler/beslutter-liste`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `finn godkjente avtaler med tilskuddsperiodestatus og nav enheter liste` (`GET /avtaler/beslutter-liste`) with query: queryParametre required, sorteringskolonne optional, page optional, size optional, sorteringOrder optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `queryParametre`, `sorteringskolonne`, `page`, `size`, `sorteringOrder` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `queryParametre`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `finn godkjente avtaler med tilskuddsperiodestatus og nav enheter liste`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 5: Sjekk Om Deltaker Allerede Er Registrert Paa Tiltak

Business goal:
Reads deltaker allerede paa tiltak information.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `GET /avtaler/deltaker-allerede-paa-tiltak`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `sjekk om deltaker allerede er registrert paa tiltak` (`GET /avtaler/deltaker-allerede-paa-tiltak`) with query: deltakerFnr required, tiltakstype required, startDato optional, sluttDato optional, avtaleId optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `deltakerFnr`, `tiltakstype`, `startDato`, `sluttDato`, `avtaleId` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `deltakerFnr`, `tiltakstype`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `sjekk om deltaker allerede er registrert paa tiltak`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 6: Hent Alle Avtaler For Min Side Arbeidsgiver

Business goal:
Reads min side arbeidsgiver information.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `GET /avtaler/min-side-arbeidsgiver`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent alle avtaler for min side arbeidsgiver` (`GET /avtaler/min-side-arbeidsgiver`) with query: bedriftNr required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bedriftNr` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `bedriftNr`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent alle avtaler for min side arbeidsgiver`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 7: Opprett Mentor Avtale

Business goal:
Creates or executes opprett mentor avtale business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/opprett-mentor-avtale`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `opprett mentor avtale` (`POST /avtaler/opprett-mentor-avtale`) with required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `opprett mentor avtale`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 8: Opprett Avtale Som Arbeidsgiver

Business goal:
Creates or executes opprett som arbeidsgiver business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/opprett-som-arbeidsgiver`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `opprett avtale som arbeidsgiver` (`POST /avtaler/opprett-som-arbeidsgiver`) with required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `opprett avtale som arbeidsgiver`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 9: Hent Alle Avtaler Innlogget Bruker Har Tilgang Til Med Get

Business goal:
Reads sok information.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `GET /avtaler/sok`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent alle avtaler innlogget bruker har tilgang til med get` (`GET /avtaler/sok`) with query: sokId required, sorteringskolonne optional, page optional, size optional; cookie: innlogget-part required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `sokId`, `sorteringskolonne`, `page`, `size` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `sokId`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent alle avtaler innlogget bruker har tilgang til med get`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 10: Hent Alle Avtaler Innlogget Bruker Har Tilgang Til Med Post

Business goal:
Creates or executes sok business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/sok`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `hent alle avtaler innlogget bruker har tilgang til med post` (`POST /avtaler/sok`) with cookie: innlogget-part required; query: sorteringskolonne optional, page optional, size optional; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `sorteringskolonne`, `page`, `size` filter, page, or modify the operation result.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `innlogget-part`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent alle avtaler innlogget bruker har tilgang til med post`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 11: Hent

Business goal:
Reads avtale id information.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `GET /avtaler/{avtaleId}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent` (`GET /avtaler/{avtaleId}`) with path: avtaleId required; cookie: innlogget-part required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `avtaleId`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 12: Endre Avtale

Business goal:
Replaces avtale id state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `PUT /avtaler/{avtaleId}`.

Starting point:
The target resource identified by the path should exist unless the implementation treats the request as an upsert.

Required execution workflow:
1. Use function `endre avtale` (`PUT /avtaler/{avtaleId}`) with path: avtaleId required; header: If-Unmodified-Since required; cookie: innlogget-part required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the update operation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The addressed resource is replaced with the submitted representation or command result.

Constraints and invariants:
- Required request values: `avtaleId`, `If-Unmodified-Since`, `innlogget-part`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `endre avtale`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 13: Annuller

Business goal:
Creates or executes annuller business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/annuller`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `annuller` (`POST /avtaler/{avtaleId}/annuller`) with path: avtaleId required; header: If-Unmodified-Since required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`, `If-Unmodified-Since`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `annuller`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 14: Avsl Tilskuddsperiode

Business goal:
Creates or executes avslag tilskuddsperiode business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `avsl tilskuddsperiode` (`POST /avtaler/{avtaleId}/avslag-tilskuddsperiode`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `avsl tilskuddsperiode`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 15: Del Avtale Med Avtalepart

Business goal:
Creates or executes del med avtalepart business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/del-med-avtalepart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `del avtale med avtalepart` (`POST /avtaler/{avtaleId}/del-med-avtalepart`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `del avtale med avtalepart`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 16: Endre Avtale Dry Run

Business goal:
Replaces dry run state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `PUT /avtaler/{avtaleId}/dry-run`.

Starting point:
The target resource identified by the path should exist unless the implementation treats the request as an upsert.

Required execution workflow:
1. Use function `endre avtale dry run` (`PUT /avtaler/{avtaleId}/dry-run`) with path: avtaleId required; header: If-Unmodified-Since required; cookie: innlogget-part required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the update operation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The addressed resource is replaced with the submitted representation or command result.

Constraints and invariants:
- Required request values: `avtaleId`, `If-Unmodified-Since`, `innlogget-part`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `endre avtale dry run`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 17: Endre Inkluderingstilskudd

Business goal:
Creates or executes endre inkluderingstilskudd business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `endre inkluderingstilskudd` (`POST /avtaler/{avtaleId}/endre-inkluderingstilskudd`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `endre inkluderingstilskudd`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 18: Endre Kontaktinfo

Business goal:
Creates or executes endre kontaktinfo business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/endre-kontaktinfo`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `endre kontaktinfo` (`POST /avtaler/{avtaleId}/endre-kontaktinfo`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `endre kontaktinfo`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 19: Endre Kostnadssted

Business goal:
Creates or executes endre kostnadssted business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/endre-kostnadssted`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `endre kostnadssted` (`POST /avtaler/{avtaleId}/endre-kostnadssted`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `endre kostnadssted`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 20: Endre M L

Business goal:
Creates or executes endre maal business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/endre-maal`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `endre m l` (`POST /avtaler/{avtaleId}/endre-maal`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `endre m l`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 21: Endre Om Mentor

Business goal:
Creates or executes endre om mentor business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/endre-om-mentor`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `endre om mentor` (`POST /avtaler/{avtaleId}/endre-om-mentor`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `endre om mentor`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 22: Endre Oppf Lging Og Tilrettelegging

Business goal:
Creates or executes endre oppfolging og tilrettelegging business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `endre oppf lging og tilrettelegging` (`POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `endre oppf lging og tilrettelegging`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 23: Endre Stillingbeskrivelse

Business goal:
Creates or executes endre stillingbeskrivelse business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `endre stillingbeskrivelse` (`POST /avtaler/{avtaleId}/endre-stillingbeskrivelse`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `endre stillingbeskrivelse`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 24: Endre Tilskuddsberegning

Business goal:
Creates or executes endre tilskuddsberegning business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/endre-tilskuddsberegning`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `endre tilskuddsberegning` (`POST /avtaler/{avtaleId}/endre-tilskuddsberegning`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `endre tilskuddsberegning`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 25: Endre Tilskuddsberegning Dry Run

Business goal:
Creates or executes endre tilskuddsberegning dry run business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `endre tilskuddsberegning dry run` (`POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `endre tilskuddsberegning dry run`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 26: Forkort Avtale

Business goal:
Creates or executes forkort business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/forkort`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `forkort avtale` (`POST /avtaler/{avtaleId}/forkort`) with path: avtaleId required; header: If-Unmodified-Since required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`, `If-Unmodified-Since`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `forkort avtale`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 27: Forkort Avtale Dry Run

Business goal:
Creates or executes forkort dry run business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/forkort-dry-run`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `forkort avtale dry run` (`POST /avtaler/{avtaleId}/forkort-dry-run`) with path: avtaleId required; header: If-Unmodified-Since required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`, `If-Unmodified-Since`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `forkort avtale dry run`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 28: Forleng Avtale

Business goal:
Creates or executes forleng business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/forleng`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `forleng avtale` (`POST /avtaler/{avtaleId}/forleng`) with path: avtaleId required; header: If-Unmodified-Since required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`, `If-Unmodified-Since`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `forleng avtale`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 29: Forleng Avtale Dry Run

Business goal:
Creates or executes forleng dry run business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/forleng-dry-run`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `forleng avtale dry run` (`POST /avtaler/{avtaleId}/forleng-dry-run`) with path: avtaleId required; header: If-Unmodified-Since required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`, `If-Unmodified-Since`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `forleng avtale dry run`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 30: Godkjenn

Business goal:
Creates or executes godkjenn business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/godkjenn`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `godkjenn` (`POST /avtaler/{avtaleId}/godkjenn`) with path: avtaleId required; header: If-Unmodified-Since required; cookie: innlogget-part required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`, `If-Unmodified-Since`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `godkjenn`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 31: Godkjenn Pa Vegne Av 1

Business goal:
Creates or executes godkjenn paa vegne av business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `godkjenn pa vegne av 1` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `godkjenn pa vegne av 1`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 32: Godkjenn Pa Vegne Av Arbeidsgiver

Business goal:
Creates or executes godkjenn paa vegne av arbeidsgiver business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `godkjenn pa vegne av arbeidsgiver` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `godkjenn pa vegne av arbeidsgiver`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 33: Godkjenn Pa Vegne Av

Business goal:
Creates or executes godkjenn paa vegne av deltaker business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `godkjenn pa vegne av` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `godkjenn pa vegne av`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 34: Godkjenn Pa Vegne Av Deltaker Og Arbeidsgiver

Business goal:
Creates or executes godkjenn paa vegne av deltaker og arbeidsgiver business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `godkjenn pa vegne av deltaker og arbeidsgiver` (`POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `godkjenn pa vegne av deltaker og arbeidsgiver`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 35: Godkjenn Tilskuddsperiode

Business goal:
Creates or executes godkjenn tilskuddsperiode business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `godkjenn tilskuddsperiode` (`POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `godkjenn tilskuddsperiode`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 36: Juster Arena Migreringsdato

Business goal:
Creates or executes juster arena migreringsdato business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/juster-arena-migreringsdato`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `juster arena migreringsdato` (`POST /avtaler/{avtaleId}/juster-arena-migreringsdato`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `juster arena migreringsdato`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 37: Juster Arena Migreringsdato Dry Run

Business goal:
Creates or executes dry run business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `juster arena migreringsdato dry run` (`POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run`) with path: avtaleId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `juster arena migreringsdato dry run`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 38: Hent Bedrift Kontonummer

Business goal:
Reads kontonummer arbeidsgiver information.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent bedrift kontonummer` (`GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver`) with path: avtaleId required; cookie: innlogget-part required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `avtaleId`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent bedrift kontonummer`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 39: Mentor Godkjenn Taushetserkl Ring

Business goal:
Creates or executes mentor godkjenn taushetserkl ring business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `mentor godkjenn taushetserkl ring` (`POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring`) with path: avtaleId required; header: If-Unmodified-Since required; cookie: innlogget-part required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`, `If-Unmodified-Since`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `mentor godkjenn taushetserkl ring`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 40: Oppdater Oppf Lgings Enhet

Business goal:
Creates or executes oppdater oppf lgings enhet business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `oppdater oppf lgings enhet` (`POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet`) with path: avtaleId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `oppdater oppf lgings enhet`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 41: Opphev Godkjenninger

Business goal:
Creates or executes opphev godkjenninger business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/opphev-godkjenninger`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `opphev godkjenninger` (`POST /avtaler/{avtaleId}/opphev-godkjenninger`) with path: avtaleId required; cookie: innlogget-part required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `opphev godkjenninger`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 42: Sett Ny Veileder P Avtale

Business goal:
Replaces overta state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `PUT /avtaler/{avtaleId}/overta`.

Starting point:
The target resource identified by the path should exist unless the implementation treats the request as an upsert.

Required execution workflow:
1. Use function `sett ny veileder p avtale` (`PUT /avtaler/{avtaleId}/overta`) with path: avtaleId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the update operation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
The addressed resource is replaced with the submitted representation or command result.

Constraints and invariants:
- Required request values: `avtaleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `sett ny veileder p avtale`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 43: Hent Avtale Pdf

Business goal:
Reads pdf information.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `GET /avtaler/{avtaleId}/pdf`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent avtale pdf` (`GET /avtaler/{avtaleId}/pdf`) with path: avtaleId required; cookie: innlogget-part required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `avtaleId`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent avtale pdf`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 44: Send Tilbake Til Beslutter

Business goal:
Creates or executes send tilbake til beslutter business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `send tilbake til beslutter` (`POST /avtaler/{avtaleId}/send-tilbake-til-beslutter`) with path: avtaleId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `send tilbake til beslutter`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 45: Set Om Avtalen Kan Etterregistreres

Business goal:
Creates or executes set om avtalen kan etterregistreres business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `set om avtalen kan etterregistreres` (`POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres`) with path: avtaleId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `set om avtalen kan etterregistreres`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 46: Slettemerk

Business goal:
Creates or executes slettemerk business state.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `POST /avtaler/{avtaleId}/slettemerk`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `slettemerk` (`POST /avtaler/{avtaleId}/slettemerk`) with path: avtaleId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `slettemerk`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 47: Hent Versjoner

Business goal:
Reads versjoner information.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `GET /avtaler/{avtaleId}/versjoner`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent versjoner` (`GET /avtaler/{avtaleId}/versjoner`) with path: avtaleId required; cookie: innlogget-part required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `avtaleId`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent versjoner`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 48: Vis Salesforce Dialog

Business goal:
Reads vis salesforce dialog information.

Domain context:
This behavior belongs to the `avtale-controller` capability area and operates through `GET /avtaler/{avtaleId}/vis-salesforce-dialog`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `vis salesforce dialog` (`GET /avtaler/{avtaleId}/vis-salesforce-dialog`) with path: avtaleId required; cookie: innlogget-part required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `avtaleId`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `vis salesforce dialog`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 49: Be Om Rettighet Urler

Business goal:
Reads be om altinn rettighet urler information.

Domain context:
This behavior belongs to the `be-om-altinn-rettighet-urler-controller` capability area and operates through `GET /be-om-altinn-rettighet-urler`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `be om rettighet urler` (`GET /be-om-altinn-rettighet-urler`) with query: orgNr required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `orgNr` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `orgNr`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `be om rettighet urler`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 50: Feature

Business goal:
Reads feature information.

Domain context:
This behavior belongs to the `feature-toggle-controller` capability area and operates through `GET /feature`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `feature` (`GET /feature`) with query: feature required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `feature` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `feature`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `feature`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 51: Variant

Business goal:
Reads variant information.

Domain context:
This behavior belongs to the `feature-toggle-controller` capability area and operates through `GET /feature/variant`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `variant` (`GET /feature/variant`) with query: feature required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `feature` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `feature`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `variant`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 52: Hent Innlogget Bruker

Business goal:
Reads innlogget bruker information.

Domain context:
This behavior belongs to the `innlogget-bruker-controller` capability area and operates through `GET /innlogget-bruker`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent innlogget bruker` (`GET /innlogget-bruker`) with cookie: innlogget-part optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent innlogget bruker`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 53: Hent Ikke Journalfoerte Avtaler

Business goal:
Reads avtaler information.

Domain context:
This behavior belongs to the `internal-avtale-controller` capability area and operates through `GET /internal/avtaler`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent ikke journalfoerte avtaler` (`GET /internal/avtaler`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent ikke journalfoerte avtaler`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 54: Journalfoer Avtaler

Business goal:
Replaces avtaler state.

Domain context:
This behavior belongs to the `internal-avtale-controller` capability area and operates through `PUT /internal/avtaler`.

Starting point:
The target resource identified by the path should exist unless the implementation treats the request as an upsert.

Required execution workflow:
1. Use function `journalfoer avtaler` (`PUT /internal/avtaler`) with required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the update operation.

Parameter and value bindings:
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The addressed resource is replaced with the submitted representation or command result.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `journalfoer avtaler`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 55: Healthcheck

Business goal:
Reads healthcheck information.

Domain context:
This behavior belongs to the `health-check-controller` capability area and operates through `GET /internal/healthcheck`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `healthcheck` (`GET /internal/healthcheck`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `healthcheck`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 56: Get

Business goal:
Reads kodeverk information.

Domain context:
This behavior belongs to the `kodeverk-controller` capability area and operates through `GET /kodeverk`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `get` (`GET /kodeverk`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `get`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 57: Statuser

Business goal:
Reads statuser information.

Domain context:
This behavior belongs to the `kodeverk-controller` capability area and operates through `GET /kodeverk/statuser`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `statuser` (`GET /kodeverk/statuser`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `statuser`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 58: Tiltakstyper

Business goal:
Reads tiltakstyper information.

Domain context:
This behavior belongs to the `kodeverk-controller` capability area and operates through `GET /kodeverk/tiltakstyper`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `tiltakstyper` (`GET /kodeverk/tiltakstyper`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `tiltakstyper`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 59: Hent Virksomhet

Business goal:
Reads organisasjoner information.

Domain context:
This behavior belongs to the `organisasjon-controller` capability area and operates through `GET /organisasjoner`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent virksomhet` (`GET /organisasjoner`) with query: bedriftNr required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bedriftNr` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `bedriftNr`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent virksomhet`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 60: Annuller Og Generer Behandlet Iarena Perioder

Business goal:
Creates or executes dato business state.

Domain context:
This behavior belongs to the `admin-controller` capability area and operates through `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `annuller og generer behandlet iarena perioder` (`POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}`) with path: avtaleId required, dato required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId`, `dato` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`, `dato`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `annuller og generer behandlet iarena perioder`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 61: Annuller Og Generer Tilskuddsperiode

Business goal:
Creates or executes tilskuddsperiode id business state.

Domain context:
This behavior belongs to the `admin-controller` capability area and operates through `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `annuller og generer tilskuddsperiode` (`POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}`) with path: tilskuddsperiodeId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `tilskuddsperiodeId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `tilskuddsperiodeId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `annuller og generer tilskuddsperiode`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 62: Annuller Og Resend Tilskuddsperiode

Business goal:
Creates or executes tilskuddsperiode id business state.

Domain context:
This behavior belongs to the `admin-controller` capability area and operates through `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `annuller og resend tilskuddsperiode` (`POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}`) with path: tilskuddsperiodeId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `tilskuddsperiodeId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `tilskuddsperiodeId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `annuller og resend tilskuddsperiode`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 63: Annuller Tilskuddsperiode

Business goal:
Creates or executes tilskuddsperiode id business state.

Domain context:
This behavior belongs to the `admin-controller` capability area and operates through `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `annuller tilskuddsperiode` (`POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}`) with path: tilskuddsperiodeId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `tilskuddsperiodeId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `tilskuddsperiodeId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `annuller tilskuddsperiode`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 64: Dry Send Melding Alle Avtaler

Business goal:
Creates or executes dry send melding alle avtaler business state.

Domain context:
This behavior belongs to the `avtale-hendelse-controller` capability area and operates through `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `dry send melding alle avtaler` (`POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `dry send melding alle avtaler`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 65: Send Melding Alle Avtaler

Business goal:
Creates or executes send melding alle avtaler business state.

Domain context:
This behavior belongs to the `avtale-hendelse-controller` capability area and operates through `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `send melding alle avtaler` (`POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `send melding alle avtaler`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 66: Send Melding For En Avtale

Business goal:
Creates or executes avtale id business state.

Domain context:
This behavior belongs to the `avtale-hendelse-controller` capability area and operates through `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `send melding for en avtale` (`POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}`) with path: avtaleId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `send melding for en avtale`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 67: Patche Avtale

Business goal:
Creates or executes patch business state.

Domain context:
This behavior belongs to the `internal-dvh-melding-produsent-controller` capability area and operates through `POST /utvikler-admin/dvh-melding/patch`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `patche avtale` (`POST /utvikler-admin/dvh-melding/patch`) with required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `patche avtale`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 68: Patch Alle Avtaler

Business goal:
Creates or executes patchalleavtaler business state.

Domain context:
This behavior belongs to the `internal-dvh-melding-produsent-controller` capability area and operates through `POST /utvikler-admin/dvh-melding/patchalleavtaler`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `patch alle avtaler` (`POST /utvikler-admin/dvh-melding/patchalleavtaler`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `patch alle avtaler`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 69: Finn Tilskuddsperioder Med Feil Datoer

Business goal:
Creates or executes finn avtaler med tilskuddsperioder feil datoer business state.

Domain context:
This behavior belongs to the `admin-controller` capability area and operates through `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `finn tilskuddsperioder med feil datoer` (`POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `finn tilskuddsperioder med feil datoer`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 70: Lag Tilskuddsperioder P En Avtale

Business goal:
Creates or executes migrerings dato business state.

Domain context:
This behavior belongs to the `admin-controller` capability area and operates through `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `lag tilskuddsperioder p en avtale` (`POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}`) with path: avtaleId required, migreringsDato required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId`, `migreringsDato` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`, `migreringsDato`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `lag tilskuddsperioder p en avtale`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 71: Reberegn L Nnstilskudd

Business goal:
Creates or executes reberegn business state.

Domain context:
This behavior belongs to the `admin-controller` capability area and operates through `POST /utvikler-admin/reberegn`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `reberegn l nnstilskudd` (`POST /utvikler-admin/reberegn`) with required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `reberegn l nnstilskudd`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 72: Reberegn Varig L Nnstilskudd Som Ikke Har Redusert Dato Dry Run

Business goal:
Creates or executes migrerings dato business state.

Domain context:
This behavior belongs to the `admin-controller` capability area and operates through `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `reberegn varig l nnstilskudd som ikke har redusert dato dry run` (`POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}`) with path: migreringsDato required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `migreringsDato` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `migreringsDato`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `reberegn varig l nnstilskudd som ikke har redusert dato dry run`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 73: Reberegn Varig L Nnstilskudd Som Ikke Har Redusert Dato

Business goal:
Creates or executes migrerings dato business state.

Domain context:
This behavior belongs to the `admin-controller` capability area and operates through `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `reberegn varig l nnstilskudd som ikke har redusert dato` (`POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}`) with path: migreringsDato required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `migreringsDato` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `migreringsDato`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `reberegn varig l nnstilskudd som ikke har redusert dato`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 74: Reberegn Ubehandlede Tilskuddsperioder

Business goal:
Creates or executes avtale id business state.

Domain context:
This behavior belongs to the `admin-controller` capability area and operates through `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `reberegn ubehandlede tilskuddsperioder` (`POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}`) with path: avtaleId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `avtaleId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `avtaleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `reberegn ubehandlede tilskuddsperioder`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 75: Hent Alle Varsler For Avtale

Business goal:
Reads avtale logg information.

Domain context:
This behavior belongs to the `varsel-controller` capability area and operates through `GET /varsler/avtale-logg`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent alle varsler for avtale` (`GET /varsler/avtale-logg`) with query: avtaleId required; cookie: innlogget-part required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `avtaleId` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `avtaleId`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent alle varsler for avtale`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 76: Hent Varsler Med Bjelle For Avtale

Business goal:
Reads avtale modal information.

Domain context:
This behavior belongs to the `varsel-controller` capability area and operates through `GET /varsler/avtale-modal`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent varsler med bjelle for avtale` (`GET /varsler/avtale-modal`) with query: avtaleId required; cookie: innlogget-part required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `avtaleId` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `avtaleId`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent varsler med bjelle for avtale`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 77: Hent Varsler Med Bjelle For Oversikt

Business goal:
Reads oversikt information.

Domain context:
This behavior belongs to the `varsel-controller` capability area and operates through `GET /varsler/oversikt`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `hent varsler med bjelle for oversikt` (`GET /varsler/oversikt`) with cookie: innlogget-part required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `hent varsler med bjelle for oversikt`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 78: Sett Flere Varsler Til Lest

Business goal:
Creates or executes sett alle til lest business state.

Domain context:
This behavior belongs to the `varsel-controller` capability area and operates through `POST /varsler/sett-alle-til-lest`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `sett flere varsler til lest` (`POST /varsler/sett-alle-til-lest`) with cookie: innlogget-part required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `innlogget-part`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `sett flere varsler til lest`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 79: Sett Til Lest

Business goal:
Creates or executes sett til lest business state.

Domain context:
This behavior belongs to the `varsel-controller` capability area and operates through `POST /varsler/{varselId}/sett-til-lest`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `sett til lest` (`POST /varsler/{varselId}/sett-til-lest`) with path: varselId required; cookie: innlogget-part required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `varselId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `varselId`, `innlogget-part`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `sett til lest`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.
