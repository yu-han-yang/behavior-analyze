# Domain-Level Behavior Analysis

## Domain Summary

This service is a Corona-Warn-App verification service. Its domain is centered on proving that a user is entitled to retrieve a COVID-19 test result and, when appropriate, obtain a TAN that authorizes diagnosis-key submission.

Main resources and concepts are:

- Registration token: an application-session token created from either a lab GUID identity or a TeleTAN.
- GUID registration: a lab-backed path using a hashed GUID and hashed date of birth.
- TeleTAN: a human-readable TAN issued by an internal/call-line workflow.
- TAN: a UUID-like transaction number generated from a registration token and later verified.
- Test result: retrieved through a registration token; TeleTAN-derived registration tokens are documented as always positive.
- Application session: persisted server-side state linking registration token hashes to GUID or TeleTAN source-of-truth state.
- TAN records: persisted server-side state for TeleTANs and generated TANs.

Local source coverage is limited: `src/main/java` contains only the EvoMaster wrapper, not the real application controllers. The strongest local implementation evidence is therefore the OpenAPI file plus generated tests and observed database tables `APP_SESSION` and `TAN`.

## Available Function Inventory

### Registration Token Functions

- `create GUID registration token`
  - Core endpoint: `POST /version/v1/registrationToken`
  - Domain meaning: creates an application session and registration token from a GUID-style lab identity using `keyType="GUID"`, `key={hashedGuid}`, and `keyDob={hashedGuidDob}`.

- `exchange TeleTAN for registration token`
  - Core endpoint: `POST /version/v1/registrationToken`
  - Domain meaning: consumes an existing TeleTAN value and creates a TeleTAN-backed application session and registration token using `keyType="TELETAN"` and `key={teleTanValue}`.

### TeleTAN Functions

- `create TeleTAN`
  - Core endpoint: `POST /version/v1/tan/teletan`
  - Domain meaning: issues a human-readable TeleTAN for call-line or internal use. OpenAPI requires `Authorization`; implementation tests also show an undocumented `X-CWA-TELETAN-TYPE` header, with `EVENT` accepted and unsupported values rejected or faulting.

### TAN Functions

- `generate TAN`
  - Core endpoint: `POST /version/v1/tan`
  - Domain meaning: generates a diagnosis-key submission TAN from an existing registration token.

- `verify TAN`
  - Core endpoint: `POST /version/v1/tan/verify`
  - Domain meaning: checks whether a TAN was previously issued by the verification server.

### Test Result Functions

- `retrieve GUID test result`
  - Core endpoint: `POST /version/v1/testresult`
  - Domain meaning: retrieves the COVID-19 test result for a GUID-backed registration token.

- `retrieve TeleTAN positive test result`
  - Core endpoint: `POST /version/v1/testresult`
  - Domain meaning: retrieves the special TeleTAN-backed test result; OpenAPI states TeleTAN registration tokens always yield a positive result.

## Supported Business Behaviors

### Behavior 1: Register a Lab GUID Identity

Business goal:
Create a verification session for a user who has a lab-issued test GUID identity.

Domain context:
This is the entry point for the connected-lab verification path. The user supplies hashed lab identity material and receives a registration token used for test-result retrieval and TAN generation.

Starting point:
No prior service state for the same GUID identity.

Required execution workflow:
1. Use function `create GUID registration token` (`POST /version/v1/registrationToken`) with JSON body `keyType="GUID"`, `key={hashedGuid}`, and `keyDob={hashedGuidDob}` to create an application session and receive `registrationToken={registrationToken}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup step can be skipped in the main no-state workflow because the core behavior is the session creation.
- Direct database setup can replace the endpoint only if an `APP_SESSION` row already exists with matching `HASHED_GUID={hashedGuid}`, `HASHED_GUID_DOB={hashedGuidDob}`, source-of-truth `SOT="HASHED_GUID"`, and a `REGISTRATION_TOKEN_HASH` corresponding to the token that later functions will use.
- The uniqueness requirement still applies: the same GUID/date-of-birth identity must not already have an application session when this function is called.

Parameter and value bindings:
- `key={hashedGuid}` and `keyDob={hashedGuidDob}` together identify the GUID registration identity.
- The response field `registrationToken` must be captured and reused as `registrationToken` in `generate TAN` and `retrieve GUID test result`.
- The implementation stores a hash of the registration token, so later calls bind by hash lookup rather than by exposing database ids.

Business result:
A GUID-backed application session exists. The client holds a registration token linked to the supplied hashed GUID and hashed date of birth.

Constraints and invariants:
- `keyType` must be `GUID`.
- `key` is required.
- `keyDob` is required by observed implementation behavior for GUID, even though the OpenAPI schema does not list it in the object-level required array.
- Reusing the same GUID identity for another registration token is rejected.
- OpenAPI documents deferred wrapper response schemas, but generated tests show flattened runtime responses containing `registrationToken`.

Failure and exceptional cases:
- Failing function: `create GUID registration token`
  - Failure condition: `key`, `keyType`, or GUID `keyDob` is missing.
  - Why it fails: generated tests show `400` for missing required registration request fields.
  - Violated prerequisite or constraint: a GUID registration must provide both hashed GUID and hashed date-of-birth material.

- Failing function: `create GUID registration token`
  - Failure condition: `keyType` is unsupported, such as `"EVOMASTER"`.
  - Why it fails: implementation-supported values are limited to known key types.
  - Violated prerequisite or constraint: registration source must be `GUID` or `TELETAN`.

- Failing function: `create GUID registration token`
  - Failure condition: an application session already exists for the same GUID identity.
  - Why it fails: OpenAPI documents `400` for existing GUID or TeleTAN identity, and persisted sessions are stored in `APP_SESSION`.
  - Violated prerequisite or constraint: one registration token per GUID identity.

Implementation notes:
The OpenAPI typo “hasehd GUID” is only documentation. Runtime responses are flattened DTOs rather than the documented deferred result wrapper.

### Behavior 2: Issue a TeleTAN

Business goal:
Create a human-readable TeleTAN that can be given to a user through an internal or call-line process.

Domain context:
TeleTANs provide an alternative to connected-lab GUID registration. They can later be exchanged for a registration token and are treated as positive-test proof.

Starting point:
No prior service state is required.

Required execution workflow:
1. Use function `create TeleTAN` (`POST /version/v1/tan/teletan`) with header `Authorization={authorizationToken}` and optionally header `X-CWA-TELETAN-TYPE="EVENT"` to create a TeleTAN and receive `value={teleTanValue}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup step is needed.
- Direct database setup can substitute for issued state only if a `TAN` row exists whose hash corresponds to `{teleTanValue}`, with `TYPE="TELETAN"`, `SOT` marking TeleTAN-related state, and `REDEEMED=false` for later exchange.
- The core issuance action cannot be skipped if the business goal is to issue a new TeleTAN through the API.

Parameter and value bindings:
- The response field `value` is the TeleTAN value that must be reused as `key` in `exchange TeleTAN for registration token`.
- `X-CWA-TELETAN-TYPE`, when used, determines TeleTAN type state such as `EVENT`; the header is implementation-observed but absent from OpenAPI.

Business result:
A TeleTAN record exists and the caller receives a human-readable TeleTAN value.

Constraints and invariants:
- OpenAPI requires the `Authorization` header.
- Generated tests show arbitrary JSON-like authorization strings can be accepted; strong authorization validation is not demonstrated.
- Omitted `X-CWA-TELETAN-TYPE` can succeed.
- `X-CWA-TELETAN-TYPE="EVENT"` can succeed.
- Unsupported TeleTAN types can return `400`, and some malformed/header combinations produced `500` through request-size/filter paths in generated tests.

Failure and exceptional cases:
- Failing function: `create TeleTAN`
  - Failure condition: missing `Authorization` header.
  - Why it fails: OpenAPI marks `Authorization` as required.
  - Violated prerequisite or constraint: internal TeleTAN issuance requires an authorization header.

- Failing function: `create TeleTAN`
  - Failure condition: unsupported `X-CWA-TELETAN-TYPE={unsupportedType}`.
  - Why it fails: generated tests show unsupported type headers can return `400`.
  - Violated prerequisite or constraint: TeleTAN type must be implementation-supported.

Implementation notes:
`X-CWA-TELETAN-TYPE` is an implementation-observed header not documented by OpenAPI. The tests also show possible `500` responses for some invalid header/request combinations, which indicates brittle filter or validation behavior.

### Behavior 3: Convert a TeleTAN into a Registration Token

Business goal:
Let a user turn a previously issued TeleTAN into an application registration token.

Domain context:
This is the user-facing continuation of the TeleTAN call-line flow. It creates the same kind of registration-token handle used by test-result and TAN endpoints, but the source-of-truth is TeleTAN rather than connected-lab GUID.

Starting point:
No prior service state, using an API-created TeleTAN.

Required execution workflow:
1. Use function `create TeleTAN` (`POST /version/v1/tan/teletan`) with header `Authorization={authorizationToken}` and optionally `X-CWA-TELETAN-TYPE="EVENT"` to receive `value={teleTanValue}`.
2. Use function `exchange TeleTAN for registration token` (`POST /version/v1/registrationToken`) with JSON body `keyType="TELETAN"` and `key={teleTanValue}` to receive `registrationToken={registrationToken}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped if an equivalent unredeemed TeleTAN already exists.
- Direct database setup can replace step 1 if a valid `TAN` row exists for `{teleTanValue}` with TeleTAN type/source state and unredeemed status.
- The TeleTAN value used in step 2 must exactly match the issued or inserted TeleTAN value.
- The core exchange in step 2 cannot be skipped if the behavior is to obtain a registration token through the API.

Parameter and value bindings:
- `value={teleTanValue}` returned by `create TeleTAN` is reused exactly as `key={teleTanValue}` in `exchange TeleTAN for registration token`.
- The response field `registrationToken` from step 2 is reused by `retrieve TeleTAN positive test result` and `generate TAN`.
- The TeleTAN record and application session are linked internally through TeleTAN hash state.

Business result:
A TeleTAN-backed application session exists. The TeleTAN has been consumed for registration-token creation, and the caller holds the resulting registration token.

Constraints and invariants:
- `keyType` must be `TELETAN`.
- `keyDob` is not required for TeleTAN registration.
- The TeleTAN must exist and must not already have been exchanged.
- The same TeleTAN cannot produce multiple registration tokens.

Failure and exceptional cases:
- Failing function: `create TeleTAN`
  - Failure condition: missing `Authorization` or unsupported `X-CWA-TELETAN-TYPE`.
  - Why it fails: authorization is required by OpenAPI; unsupported TeleTAN type is rejected by implementation tests.
  - Violated prerequisite or constraint: valid internal TeleTAN issuance request.

- Failing function: `exchange TeleTAN for registration token`
  - Failure condition: `key={teleTanValue}` does not identify an existing TeleTAN.
  - Why it fails: generated tests show `400` for arbitrary TeleTAN keys with no matching TAN row.
  - Violated prerequisite or constraint: TeleTAN must be issued before exchange.

- Failing function: `exchange TeleTAN for registration token`
  - Failure condition: the same TeleTAN is exchanged more than once.
  - Why it fails: OpenAPI documents `400` when GUID or TeleTAN already exists; the implementation persists session state.
  - Violated prerequisite or constraint: TeleTAN is single-use for registration-token creation.

Implementation notes:
OpenAPI exposes one registration-token endpoint for both GUID and TeleTAN paths. The domain distinction is determined by `keyType`.

### Behavior 4: Generate a Diagnosis-Key Submission TAN

Business goal:
Produce a TAN that authorizes a client to submit diagnosis keys.

Domain context:
A registration token proves that the user has entered the verification flow. The generated TAN is the actual submission credential.

Starting point:
No prior service state, using a GUID-backed registration token as the main API-realizable setup path. A TeleTAN-backed registration token is an alternative setup path.

Required execution workflow:
1. Use function `create GUID registration token` (`POST /version/v1/registrationToken`) with JSON body `keyType="GUID"`, `key={hashedGuid}`, and `keyDob={hashedGuidDob}` to receive `registrationToken={registrationToken}`.
2. Use function `generate TAN` (`POST /version/v1/tan`) with JSON body `registrationToken={registrationToken}` and optional `responsePadding={padding}` to receive `tan={tan}`.

Alternative setup path:
1. Use function `create TeleTAN` (`POST /version/v1/tan/teletan`) with header `Authorization={authorizationToken}` to receive `value={teleTanValue}`.
2. Use function `exchange TeleTAN for registration token` (`POST /version/v1/registrationToken`) with JSON body `keyType="TELETAN"` and `key={teleTanValue}` to receive `registrationToken={registrationToken}`.
3. Use function `generate TAN` (`POST /version/v1/tan`) with JSON body `registrationToken={registrationToken}` to receive `tan={tan}`.

Optional verification workflow:
1. Use function `verify TAN` (`POST /version/v1/tan/verify`) with JSON body `tan={tan}` and optional `responsePadding={padding}` to verify that the generated TAN is recognized as issued.

Existing-state shortcuts:
- The registration setup step can be skipped if a valid `APP_SESSION` already exists and the caller has the exact corresponding plaintext `registrationToken`.
- Direct database setup can replace registration setup if `APP_SESSION.REGISTRATION_TOKEN_HASH` corresponds to the plaintext token used in `generate TAN`.
- The core `generate TAN` step cannot be skipped for this behavior.
- If verifying afterward, the exact generated TAN must be available; a different UUID-like value is not equivalent.

Parameter and value bindings:
- `registrationToken` returned by the registration-token function is reused exactly in `generate TAN`.
- If `responsePadding` is supplied to `generate TAN`, generated tests show a `responsePadding` field may be returned.
- `tan` returned by `generate TAN` is reused exactly in `verify TAN`.

Business result:
A TAN record exists for the registration-token-backed application session. The caller receives a TAN usable for downstream diagnosis-key submission and internal verification.

Constraints and invariants:
- `registrationToken` is required and must match the UUID-like pattern documented by OpenAPI.
- The registration token must resolve to an existing `APP_SESSION`.
- Generated tests show successful responses return status `201` and flattened field `tan`.
- Directly corrupted database state can cause `500`, for example invalid counter/session combinations in generated tests.

Failure and exceptional cases:
- Failing function: `create GUID registration token`
  - Failure condition: missing GUID fields or duplicate GUID identity.
  - Why it fails: implementation rejects incomplete or duplicate registration setup.
  - Violated prerequisite or constraint: valid unique registration-token setup.

- Failing function: `create TeleTAN`
  - Failure condition: TeleTAN authorization/type request is invalid.
  - Why it fails: OpenAPI requires `Authorization`; implementation rejects unsupported type headers.
  - Violated prerequisite or constraint: valid TeleTAN issuance setup.

- Failing function: `exchange TeleTAN for registration token`
  - Failure condition: unknown or already exchanged TeleTAN.
  - Why it fails: exchange requires an existing unconsumed TeleTAN.
  - Violated prerequisite or constraint: TeleTAN state must exist and be single-use.

- Failing function: `generate TAN`
  - Failure condition: unknown, malformed, or missing `registrationToken`.
  - Why it fails: endpoint looks up the token hash in `APP_SESSION`; generated tests show `400` for unknown tokens.
  - Violated prerequisite or constraint: an application session must already exist.

Implementation notes:
OpenAPI says the `generate TAN` response is a deferred wrapper, but tests show a direct JSON body with `tan` and sometimes `responsePadding`.

### Behavior 5: Retrieve a GUID-Backed COVID-19 Test Result

Business goal:
Allow a user registered through a connected-lab GUID to retrieve their COVID-19 test result.

Domain context:
This is the lab-result lookup path. The verification server maps the registration token to stored GUID identity material and calls or represents the configured test-result service.

Starting point:
No prior verification-service state. External lab-result state may need to already exist outside this API because this service exposes no function to create it.

Required execution workflow:
1. Use function `create GUID registration token` (`POST /version/v1/registrationToken`) with JSON body `keyType="GUID"`, `key={hashedGuid}`, and `keyDob={hashedGuidDob}` to receive `registrationToken={registrationToken}`.
2. Use function `retrieve GUID test result` (`POST /version/v1/testresult`) with JSON body `registrationToken={registrationToken}` and optional `responsePadding={padding}` to retrieve fields such as `testResult`, `sc`, and `responsePadding`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped if a GUID-backed `APP_SESSION` already exists and the caller has the exact corresponding plaintext `registrationToken`.
- Direct database setup can create the local session state by inserting `APP_SESSION` with `SOT="HASHED_GUID"`, matching `HASHED_GUID`, `HASHED_GUID_DOB`, and `REGISTRATION_TOKEN_HASH`.
- Direct database setup in this service does not create external lab-result-service state. That external state, if required by deployment, must still exist or be simulated by environment configuration.

Parameter and value bindings:
- `registrationToken` returned by `create GUID registration token` is reused exactly in `retrieve GUID test result`.
- Internally, the registration token binds to the GUID identity fields used for external result lookup.
- `responsePadding`, if supplied, is request padding and may be reflected or supplemented in the response.

Business result:
The caller receives the test-result payload for the GUID-backed registration token. The local application session remains available for subsequent TAN generation.

Constraints and invariants:
- The registration token must exist and must refer to a GUID-backed application session.
- The service has no API for writing or correcting lab-result state.
- Generated tests show `400` for unknown tokens.
- Generated tests also show `500` can occur with malformed direct database state or test-result service failures.

Failure and exceptional cases:
- Failing function: `create GUID registration token`
  - Failure condition: missing `key`, missing `keyType`, missing GUID `keyDob`, unsupported `keyType`, or duplicate GUID identity.
  - Why it fails: registration-token creation validates source fields and uniqueness.
  - Violated prerequisite or constraint: valid unique GUID session must be created first.

- Failing function: `retrieve GUID test result`
  - Failure condition: no application session exists for `registrationToken={registrationToken}`.
  - Why it fails: endpoint cannot map the token to GUID identity state; generated tests confirm `400`.
  - Violated prerequisite or constraint: registration token must be known.

- Failing function: `retrieve GUID test result`
  - Failure condition: malformed or missing `registrationToken`.
  - Why it fails: OpenAPI requires `registrationToken`; implementation rejects unusable token bodies.
  - Violated prerequisite or constraint: request must contain a usable registration token.

Implementation notes:
The EvoMaster wrapper configures `--cwa-testresult-server.url=http://cwa-testresult-server:8088`. The local service API does not expose the test-result server’s data-creation workflow.

### Behavior 6: Retrieve a TeleTAN-Backed Positive Test Result

Business goal:
Let a user with a TeleTAN-backed registration token retrieve a positive test result.

Domain context:
TeleTANs are treated as proof supplied through an internal/call-line process. OpenAPI explicitly states that if the registration token belongs to a TeleTAN, the result is always positive.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create TeleTAN` (`POST /version/v1/tan/teletan`) with header `Authorization={authorizationToken}` and optionally `X-CWA-TELETAN-TYPE="EVENT"` to receive `value={teleTanValue}`.
2. Use function `exchange TeleTAN for registration token` (`POST /version/v1/registrationToken`) with JSON body `keyType="TELETAN"` and `key={teleTanValue}` to receive `registrationToken={registrationToken}`.
3. Use function `retrieve TeleTAN positive test result` (`POST /version/v1/testresult`) with JSON body `registrationToken={registrationToken}` and optional `responsePadding={padding}` to retrieve the positive result.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped if a valid unredeemed TeleTAN already exists.
- Steps 1 and 2 can be skipped if a TeleTAN-backed `APP_SESSION` already exists and the caller has the corresponding plaintext `registrationToken`.
- Direct database setup can insert both `TAN` and `APP_SESSION` rows, but the TeleTAN hash, registration-token hash, source-of-truth, and redeemed state must be mutually consistent.
- The test-result retrieval step is the core behavior action and cannot be skipped.

Parameter and value bindings:
- TeleTAN `value` from step 1 is reused exactly as `key` in step 2.
- `registrationToken` from step 2 is reused exactly in step 3.
- Internal `APP_SESSION.SOT` must identify the session as TeleTAN-backed for the special positive-result behavior.

Business result:
The caller receives a TeleTAN-derived test result. The result is positive by documented domain rule, and the registration token remains usable for TAN generation.

Constraints and invariants:
- TeleTAN must exist before exchange.
- TeleTAN exchange is single-use.
- The registration token must resolve to TeleTAN-backed session state.
- Generated tests show TeleTAN-backed test-result responses with `testResult=2.0` and an `sc` timestamp-like value.

Failure and exceptional cases:
- Failing function: `create TeleTAN`
  - Failure condition: missing authorization header or unsupported TeleTAN type.
  - Why it fails: issuance request does not satisfy OpenAPI/implementation constraints.
  - Violated prerequisite or constraint: valid internal TeleTAN issuance.

- Failing function: `exchange TeleTAN for registration token`
  - Failure condition: unknown or already consumed TeleTAN.
  - Why it fails: exchange cannot create a session without an unredeemed TeleTAN record.
  - Violated prerequisite or constraint: TeleTAN must be known and unused.

- Failing function: `retrieve TeleTAN positive test result`
  - Failure condition: unknown `registrationToken`.
  - Why it fails: endpoint cannot resolve token hash to an application session; generated tests show `400`.
  - Violated prerequisite or constraint: TeleTAN-backed application session must exist.

Implementation notes:
Generated tests show headers unrelated to OpenAPI, such as `X-CWA-TELETAN-TYPE`, can be present on test-result requests but do not define the core result behavior.

### Behavior 7: Verify an Issued TAN

Business goal:
Confirm that a TAN presented to an internal verifier was issued by this verification server.

Domain context:
This supports downstream trust in diagnosis-key submission: the verifier can reject arbitrary UUIDs and accept previously issued TANs.

Starting point:
No prior service state, using a GUID-backed registration token as setup.

Required execution workflow:
1. Use function `create GUID registration token` (`POST /version/v1/registrationToken`) with JSON body `keyType="GUID"`, `key={hashedGuid}`, and `keyDob={hashedGuidDob}` to receive `registrationToken={registrationToken}`.
2. Use function `generate TAN` (`POST /version/v1/tan`) with JSON body `registrationToken={registrationToken}` to receive `tan={tan}`.
3. Use function `verify TAN` (`POST /version/v1/tan/verify`) with JSON body `tan={tan}` and optional `responsePadding={padding}` to verify the issued TAN.

Alternative setup path:
1. Use function `create TeleTAN` (`POST /version/v1/tan/teletan`) with header `Authorization={authorizationToken}` to receive `value={teleTanValue}`.
2. Use function `exchange TeleTAN for registration token` (`POST /version/v1/registrationToken`) with JSON body `keyType="TELETAN"` and `key={teleTanValue}` to receive `registrationToken={registrationToken}`.
3. Use function `generate TAN` (`POST /version/v1/tan`) with JSON body `registrationToken={registrationToken}` to receive `tan={tan}`.
4. Use function `verify TAN` (`POST /version/v1/tan/verify`) with JSON body `tan={tan}` to verify it.

Optional verification workflow:
None.

Existing-state shortcuts:
- Registration-token setup can be skipped if a valid application session and plaintext registration token already exist.
- TAN generation can be skipped only if an issued TAN already exists and the exact plaintext TAN is available.
- Direct database setup can insert `TAN` state, but `TAN_HASH` must correspond to the plaintext `tan`, and `TYPE`/source state must mark it as an issued TAN.
- The core verification step cannot be skipped.

Parameter and value bindings:
- `registrationToken` from setup is reused in `generate TAN`.
- `tan` returned by `generate TAN` is reused exactly in `verify TAN`.
- Sending `{differentTan}` instead of `{tan}` intentionally breaks the binding and returns not verified.

Business result:
The service confirms whether the supplied TAN was issued. For an unknown TAN, the business result is negative verification with `404`.

Constraints and invariants:
- `tan` is required and must match the UUID-like TAN pattern.
- OpenAPI documents `200` for valid issued TAN and `404` when it cannot be verified.
- Generated tests only demonstrate `404` branches for arbitrary or mismatched TANs; the success path is documented by OpenAPI and represented in `full-behavior.md`.

Failure and exceptional cases:
- Failing function: `create GUID registration token`
  - Failure condition: invalid or duplicate GUID setup.
  - Why it fails: registration session cannot be established.
  - Violated prerequisite or constraint: valid registration-token setup.

- Failing function: `generate TAN`
  - Failure condition: unknown or missing `registrationToken`.
  - Why it fails: no application session resolves from the supplied token.
  - Violated prerequisite or constraint: TAN must be generated from an existing registration token.

- Failing function: `verify TAN`
  - Failure condition: no issued TAN record exists for `tan={tan}`.
  - Why it fails: implementation cannot match the supplied TAN hash; generated tests show `404`.
  - Violated prerequisite or constraint: TAN must have been issued by this server.

- Failing function: `verify TAN`
  - Failure condition: request sends `{differentTan}` instead of the generated `{tan}`.
  - Why it fails: verification is value-specific.
  - Violated prerequisite or constraint: exact generated TAN value must be reused.

Implementation notes:
The verification endpoint is internal-controller tagged in OpenAPI but has no documented `Authorization` header, unlike TeleTAN creation.

### Behavior 8: Complete GUID-Based Verification and Submission Authorization

Business goal:
Register a lab test, retrieve its result, generate a submission TAN, and verify that TAN as issued.

Domain context:
This is the full connected-lab flow exposed by the service: a GUID identity becomes a registration token, the token retrieves test state, and the same token produces a TAN for diagnosis-key submission.

Starting point:
No prior verification-service state. External lab-result state may need to pre-exist outside this API.

Required execution workflow:
1. Use function `create GUID registration token` (`POST /version/v1/registrationToken`) with JSON body `keyType="GUID"`, `key={hashedGuid}`, and `keyDob={hashedGuidDob}` to receive `registrationToken={registrationToken}`.
2. Use function `retrieve GUID test result` (`POST /version/v1/testresult`) with JSON body `registrationToken={registrationToken}` and optional `responsePadding={padding}` to inspect the lab-backed test result.
3. Use function `generate TAN` (`POST /version/v1/tan`) with JSON body `registrationToken={registrationToken}` and optional `responsePadding={padding}` to receive `tan={tan}`.
4. Use function `verify TAN` (`POST /version/v1/tan/verify`) with JSON body `tan={tan}` to verify the TAN as issued.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped if a GUID-backed application session and plaintext registration token already exist.
- Direct database setup can create the local `APP_SESSION`, but external lab-result state is not creatable by this service.
- Step 2 can be skipped only if the business workflow does not require inspecting the result before submission authorization.
- Step 3 cannot be skipped unless an equivalent issued TAN already exists and the exact plaintext TAN is available.
- Step 4 cannot be skipped if the behavior includes internal verification.

Parameter and value bindings:
- The same `registrationToken` created in step 1 is reused in both step 2 and step 3.
- The same GUID identity fields bind the registration token to lab-result lookup.
- The `tan` generated in step 3 is reused exactly in step 4.

Business result:
A GUID-backed application session exists, the associated test result has been retrieved, a TAN has been generated, and the TAN is verifiable as issued.

Constraints and invariants:
- The flow depends on exact token reuse.
- The service does not enforce, in the exposed API evidence, that TAN generation only happens after a positive test-result retrieval.
- Unknown tokens fail independently in both test-result retrieval and TAN generation.
- Invalid direct database state can produce `500` rather than clean domain errors.

Failure and exceptional cases:
- Failing function: `create GUID registration token`
  - Failure condition: invalid GUID registration body or duplicate GUID identity.
  - Why it fails: cannot create the application session.
  - Violated prerequisite or constraint: unique complete GUID registration.

- Failing function: `retrieve GUID test result`
  - Failure condition: token does not resolve to an application session or external test-result service faults.
  - Why it fails: lookup requires local session and external result availability.
  - Violated prerequisite or constraint: valid GUID-backed registration and result infrastructure.

- Failing function: `generate TAN`
  - Failure condition: the registration token from step 1 is not reused exactly.
  - Why it fails: token hash lookup fails.
  - Violated prerequisite or constraint: exact token continuity.

- Failing function: `verify TAN`
  - Failure condition: the TAN from step 3 is not reused exactly.
  - Why it fails: TAN hash lookup fails and returns `404`.
  - Violated prerequisite or constraint: exact TAN continuity.

Implementation notes:
This complete flow is API-realizable for local verification-server state, but not for creating external lab-result data.

### Behavior 9: Complete TeleTAN-Based Positive Verification and Submission Authorization

Business goal:
Issue a TeleTAN, convert it into a registration token, retrieve the positive result, generate a submission TAN, and verify the TAN.

Domain context:
This is the complete call-line-assisted flow. It bypasses GUID lab lookup and uses TeleTAN state as the basis for a positive verification path.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create TeleTAN` (`POST /version/v1/tan/teletan`) with header `Authorization={authorizationToken}` and optionally `X-CWA-TELETAN-TYPE="EVENT"` to receive `value={teleTanValue}`.
2. Use function `exchange TeleTAN for registration token` (`POST /version/v1/registrationToken`) with JSON body `keyType="TELETAN"` and `key={teleTanValue}` to receive `registrationToken={registrationToken}`.
3. Use function `retrieve TeleTAN positive test result` (`POST /version/v1/testresult`) with JSON body `registrationToken={registrationToken}` to retrieve the positive test result.
4. Use function `generate TAN` (`POST /version/v1/tan`) with JSON body `registrationToken={registrationToken}` to receive `tan={tan}`.
5. Use function `verify TAN` (`POST /version/v1/tan/verify`) with JSON body `tan={tan}` to verify the issued TAN.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped if an unredeemed TeleTAN already exists and its plaintext value is known.
- Steps 1 and 2 can be skipped if a TeleTAN-backed application session already exists and the plaintext registration token is known.
- Step 4 can be skipped only if an issued TAN already exists for the intended authorization outcome and the exact plaintext TAN is known.
- Direct database setup must preserve TeleTAN hash, registration-token hash, `SOT="TELETAN"` session state, and TAN hash consistency.

Parameter and value bindings:
- `value={teleTanValue}` from step 1 is reused exactly as registration `key` in step 2.
- `registrationToken={registrationToken}` from step 2 is reused in both step 3 and step 4.
- `tan={tan}` from step 4 is reused exactly in step 5.
- The TeleTAN-backed source-of-truth is what makes step 3 use the always-positive result rule.

Business result:
The service has issued and consumed a TeleTAN, created a TeleTAN-backed application session, returned a positive test result, generated a TAN, and verified that TAN as issued.

Constraints and invariants:
- TeleTAN is single-use for registration-token exchange.
- TeleTAN result positivity is documented by OpenAPI.
- TAN generation appears to depend on registration-token existence, not on a separate positive-result state transition.
- No endpoint exposes TeleTAN or TAN lifecycle status after the flow.

Failure and exceptional cases:
- Failing function: `create TeleTAN`
  - Failure condition: missing authorization or unsupported TeleTAN type.
  - Why it fails: TeleTAN issuance validation rejects the request.
  - Violated prerequisite or constraint: valid internal TeleTAN issuance.

- Failing function: `exchange TeleTAN for registration token`
  - Failure condition: unknown, mismatched, or already exchanged TeleTAN value.
  - Why it fails: exchange requires exact unredeemed TeleTAN state.
  - Violated prerequisite or constraint: exact TeleTAN value continuity and single-use state.

- Failing function: `retrieve TeleTAN positive test result`
  - Failure condition: registration token is missing or not known.
  - Why it fails: no application session resolves from token hash.
  - Violated prerequisite or constraint: TeleTAN-backed registration session.

- Failing function: `generate TAN`
  - Failure condition: registration token from step 2 is not reused exactly.
  - Why it fails: token lookup fails.
  - Violated prerequisite or constraint: exact registration-token continuity.

- Failing function: `verify TAN`
  - Failure condition: generated TAN from step 4 is not reused exactly.
  - Why it fails: TAN verification lookup fails.
  - Violated prerequisite or constraint: exact TAN continuity.

Implementation notes:
This flow is fully API-realizable within the exposed service and does not require external lab-result setup.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Create or Update Lab Test Result State

Priority:
Critical domain gap

Expected business goal:
A lab, test-result server, or test harness should be able to create, update, or simulate the COVID-19 result associated with a GUID identity.

Why it is unsupported:
This service exposes only registration, test-result retrieval, TeleTAN, TAN generation, and TAN verification. No function creates or updates external lab-result data.

Existing functions considered:
- `create GUID registration token`: creates local application-session state but not lab result state.
- `retrieve GUID test result`: reads or proxies result state but cannot create or change it.
- `generate TAN`: creates a submission TAN, unrelated to lab result storage.

Missing capability:
An endpoint or integration API for writing test-result records by GUID/hash/date-of-birth identity, or a documented test-result-server setup function.

Proof that function composition is insufficient:
Chaining registration-token creation and test-result retrieval can only query whatever result state already exists externally. It cannot create, prevent, modify, or repair the external lab result that determines the returned GUID result.

Evidence from existing functions/source:
The EvoMaster wrapper configures a test-result-server URL, and `full-behavior.md` notes no OpenAPI endpoint in this directory creates the external state.

Business impact:
End-to-end GUID lab workflows cannot be fully established through this API alone.

### Missing Behavior 2: List or Retrieve Existing Registration Sessions, TeleTANs, or TANs

Priority:
Important robustness gap

Expected business goal:
Operators or clients should be able to inspect issued registration tokens, TeleTAN status, TAN status, or application-session state.

Why it is unsupported:
All exposed endpoints are POST commands. There are no GET/list/search endpoints for sessions, TeleTANs, or TANs.

Existing functions considered:
- `create GUID registration token`: returns only the newly created registration token.
- `create TeleTAN`: returns only the newly created TeleTAN value.
- `generate TAN`: returns only the newly generated TAN.
- `verify TAN`: can check one TAN but does not return inventory or ownership/session metadata.

Missing capability:
List/retrieve endpoints for application sessions, TeleTAN records, TAN records, and their status fields.

Proof that function composition is insufficient:
A caller who loses a plaintext token or TeleTAN cannot recover it from any function. Hash-only database state cannot be queried through the API, and verification only answers a single supplied TAN value.

Evidence from existing functions/source:
OpenAPI paths contain only `/registrationToken`, `/tan/teletan`, `/tan`, `/testresult`, and `/tan/verify`, all as POST operations.

Business impact:
Operational support, audit, troubleshooting, and idempotent client recovery are weak.

### Missing Behavior 3: Revoke, Expire, or Delete Registration Tokens, TeleTANs, or TANs

Priority:
Important robustness gap

Expected business goal:
Operators should be able to revoke mistakenly issued TeleTANs, invalidate registration tokens, expire stale sessions, or cancel issued TANs.

Why it is unsupported:
No delete, revoke, expire, deactivate, or patch endpoint exists.

Existing functions considered:
- `exchange TeleTAN for registration token`: consumes TeleTANs only by exchanging them.
- `verify TAN`: checks a TAN but does not revoke it.
- `generate TAN`: creates TANs but does not manage lifecycle after creation.

Missing capability:
Explicit lifecycle endpoints or state transitions for invalidation and expiry.

Proof that function composition is insufficient:
Using or exchanging a token is not equivalent to revocation. It may create additional state and cannot express cancellation, administrative invalidation, or reasoned expiry.

Evidence from existing functions/source:
The observed database has validity and redeemed-like fields, but no exposed API updates them except normal exchange/generation behavior.

Business impact:
Incorrectly issued credentials remain difficult or impossible to neutralize through supported API workflows.

### Missing Behavior 4: Strongly Authenticate TeleTAN Issuance

Priority:
Critical domain gap

Expected business goal:
Only authorized internal actors should be able to issue TeleTANs.

Why it is unsupported or weak:
OpenAPI requires an `Authorization` header, but generated tests show arbitrary JSON-like token strings accepted for successful TeleTAN creation. No available function validates or introspects authorization.

Existing functions considered:
- `create TeleTAN`: requires the header syntactically but observed tests do not prove real authorization.
- `verify TAN`: verifies TAN values, not issuer authorization.

Missing capability:
A real authorization validation rule, documented token format, permission model, or authentication failure behavior.

Proof that function composition is insufficient:
Passing arbitrary header values cannot establish authenticated operator identity. No other function creates, validates, or binds authorization credentials.

Evidence from existing functions/source:
Generated tests successfully call `create TeleTAN` with arbitrary `Authorization` header contents.

Business impact:
TeleTAN issuance is a high-trust action; weak authorization would allow unauthorized positive-result paths.

### Missing Behavior 5: Enforce Result-Gated TAN Generation

Priority:
Important robustness gap

Expected business goal:
A TAN for diagnosis-key submission should be issued only after the service confirms the relevant test result or positive entitlement.

Why it is unsupported or unclear:
`generate TAN` requires only a valid registration token. The exposed workflow does not show a persisted “positive result checked” prerequisite.

Existing functions considered:
- `retrieve GUID test result`: retrieves result but does not expose a state transition that `generate TAN` depends on.
- `retrieve TeleTAN positive test result`: returns positive result but is not required by `generate TAN`.
- `generate TAN`: accepts registration token directly.

Missing capability:
A state rule requiring positive/eligible test result before TAN generation, or a combined endpoint that checks result and issues TAN atomically.

Proof that function composition is insufficient:
Calling test-result retrieval before TAN generation is voluntary. Existing functions cannot force that ordering or prevent TAN generation for an ineligible result if the registration token is otherwise valid.

Evidence from existing functions/source:
OpenAPI description for `generate TAN` says it generates a TAN from a registration token; no documented result-status prerequisite appears.

Business impact:
Diagnosis-key submission authorization may be decoupled from confirmed positive status.

### Missing Behavior 6: Idempotent Recovery for Duplicate Registration or TeleTAN Exchange

Priority:
API ergonomics gap

Expected business goal:
If a client retries after a timeout, it should be able to recover the existing registration token or know whether the prior operation succeeded.

Why it is unsupported:
Duplicate GUID or TeleTAN exchange returns `400` rather than exposing the existing token or an idempotency key.

Existing functions considered:
- `create GUID registration token`: rejects duplicate identity.
- `exchange TeleTAN for registration token`: rejects already exchanged TeleTAN.
- `generate TAN`: requires the registration token, so it cannot recover a lost one.

Missing capability:
Idempotency keys, safe retry semantics, or lookup-by-identity recovery.

Proof that function composition is insufficient:
Once the plaintext registration token is lost, only its hash remains server-side. No endpoint can retrieve it, and repeating the creation request fails.

Evidence from existing functions/source:
`full-behavior.md` documents duplicate GUID/TeleTAN registration as failure; OpenAPI documents `400` for existing GUID/TeleTAN.

Business impact:
Clients can strand users after network timeouts or lost responses.

### Missing Behavior 7: Validate or Normalize TeleTAN Type Through the Public Contract

Priority:
API ergonomics gap

Expected business goal:
Clients should know which TeleTAN types are supported and receive consistent validation errors.

Why it is unsupported:
The `X-CWA-TELETAN-TYPE` header is not documented in OpenAPI, but implementation tests show it affects behavior.

Existing functions considered:
- `create TeleTAN`: accepts omitted type and `EVENT`, rejects some unsupported values.
- `exchange TeleTAN for registration token`: consumes the resulting TeleTAN but does not document type behavior.

Missing capability:
OpenAPI parameter documentation and stable enum/schema for TeleTAN type.

Proof that function composition is insufficient:
No function can discover supported type values or normalize an unsupported type before issuance. Trial-and-error may produce `400` or even `500`.

Evidence from existing functions/source:
OpenAPI documents only `Authorization` for `POST /version/v1/tan/teletan`; generated tests include `X-CWA-TELETAN-TYPE`.

Business impact:
Integrators may send undocumented values and encounter inconsistent production behavior.

## Cross-Behavior Observations

- The API is command-heavy and lacks read/list/admin lifecycle endpoints.
- Registration tokens and TANs are plaintext values returned once and then stored by hash; losing plaintext values blocks recovery.
- OpenAPI and implementation-observed behavior disagree on response shape: OpenAPI documents deferred wrapper schemas, while tests show flattened fields such as `registrationToken`, `tan`, `value`, `testResult`, and `sc`.
- GUID `keyDob` is effectively required by implementation for `keyType="GUID"`, but not listed in the OpenAPI object-level `required` array.
- `X-CWA-TELETAN-TYPE` affects TeleTAN creation but is absent from OpenAPI.
- TeleTAN issuance authorization appears syntactic in generated tests; strong authorization is not evidenced.
- Some invalid states or malformed combinations return `500` rather than domain-level `400`/`404`.
- TAN generation appears registration-token-gated, not explicitly result-gated.
- TeleTAN-backed test results are documented as always positive.
- The local repository does not include the real controllers, so implementation conclusions rely on generated tests and observed persisted table shapes.

## Coverage Summary

Supported domain areas:
- GUID-based registration-token creation.
- TeleTAN issuance.
- TeleTAN-to-registration-token exchange.
- Test-result retrieval for GUID and TeleTAN registration tokens.
- TAN generation from registration tokens.
- TAN verification by value.

Partially supported domain areas:
- Complete GUID lab workflow, because external lab-result state cannot be created through this API.
- TeleTAN issuance security, because authorization is documented but strong validation is not evidenced.
- Submission authorization, because TAN generation is supported but result-gated eligibility is not clearly enforced.

Unsupported domain areas:
- Lab-result creation or update.
- Listing, searching, retrieving, revoking, expiring, or deleting sessions and TANs.
- Idempotent recovery after duplicate registration or lost responses.
- Administrative audit and lifecycle management.
- Publicly documented TeleTAN type discovery and validation.