Scope note: `src/main/java` contains only the EvoMaster wrapper, not the real application controllers. The available local evidence is the top-level `cwa-verification.json`, the identical `src/main/resources/api-docs.json`, and generated tests in `src/test`. The tests show implementation responses are flattened DTOs such as `registrationToken`, `tan`, `value`, and `testResult`, while OpenAPI documents deferred wrapper schemas.

### Function 1: create registration token from GUID

Function name:
create GUID registration token

Core endpoint(s):
- `POST /version/v1/registrationToken`

Preconditions:
- No existing `APP_SESSION` row represents the same GUID input. This can be satisfied by starting from an empty database, deleting any existing session for the same hashed GUID first, or directly ensuring no row exists with `HASHED_GUID = {key}` and `HASHED_GUID_DOB = {keyDob}` before calling `POST /version/v1/registrationToken`.

Successful execution:
- Result:
  The endpoint creates an application session for the supplied hashed GUID and date-of-birth hash and returns a `registrationToken` value that can be reused by TAN generation and test-result retrieval.
- Invocation:
  Step 1: `POST /version/v1/registrationToken` with JSON body fields `keyType = "GUID"`, `key = {hashedGuid}`, and `keyDob = {hashedGuidDob}`.
- Constraints:
  `keyType` must be one of the implementation-supported values and must be `GUID` for this function. `key` is the hashed GUID. `keyDob` is required by the observed implementation for `GUID`, even though the OpenAPI schema does not list it in the object-level `required` array. The response body contains `registrationToken`; generated tests show the runtime body is flattened rather than the deferred wrapper shape documented by OpenAPI.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid GUID registration request can be built because one or more required fields are omitted or invalid.
  - Failure endpoint:
    `POST /version/v1/registrationToken`
  - Why this fails:
    Generated tests show `400` when `key`, `keyType`, or GUID `keyDob` is missing, and when `keyType` is not one of the supported values.
  - Intentionally violated constraints:
    The request omits `key`, omits `keyType`, omits `keyDob` while using `keyType = "GUID"`, or sends an unsupported `keyType`.

- Branch 2:
  - Preconditions:
    - An application session already exists for the same GUID registration identity. This can be satisfied by directly inserting an `APP_SESSION` row with `HASHED_GUID = {hashedGuid}`, `HASHED_GUID_DOB = {hashedGuidDob}`, and source-of-truth state for a GUID session, or by first calling `POST /version/v1/registrationToken` with JSON body fields `keyType = "GUID"`, `key = {hashedGuid}`, and `keyDob = {hashedGuidDob}`.
    - The failing request reuses the same `keyType`, `key`, and `keyDob` values that identify the existing GUID session.
  - Failure endpoint:
    `POST /version/v1/registrationToken`
  - Why this fails:
    OpenAPI documents `400` when the GUID or TeleTAN already exists, and the implementation persists registration sessions in `APP_SESSION`.
  - Intentionally violated constraints:
    The GUID uniqueness requirement is violated by creating a second registration token for the same GUID and date-of-birth hash.

Endpoint coverage:
- Covers:
  `POST /version/v1/registrationToken`
- Distinct meaning:
  This endpoint directly creates a registration token from a GUID-style key.

### Function 2: create TeleTAN

Function name:
create TeleTAN

Core endpoint(s):
- `POST /version/v1/tan/teletan`

Preconditions:
- No endpoint-created resource state is required. Direct database setup is only needed to constrain collision or fault scenarios; normal successful execution can start without inserting `APP_SESSION` or `TAN` rows.

Successful execution:
- Result:
  The endpoint creates a human-readable TeleTAN and returns it in the flattened response field `value`.
- Invocation:
  Step 1: `POST /version/v1/tan/teletan` with the required `Authorization` header and, optionally, `X-CWA-TELETAN-TYPE = "EVENT"` for an implementation-supported TeleTAN type.
- Constraints:
  OpenAPI requires the `Authorization` header. Generated tests show arbitrary JSON-like token strings in the header can be accepted. The implementation also observes an undocumented `X-CWA-TELETAN-TYPE` header: omitted values and `EVENT` can succeed, while unsupported values can fail.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request has no `Authorization` header even though OpenAPI marks that header as required.
  - Failure endpoint:
    `POST /version/v1/tan/teletan`
  - Why this fails:
    OpenAPI requires `Authorization` for this endpoint.
  - Intentionally violated constraints:
    The required authorization-header relationship is omitted.

- Branch 2:
  - Preconditions:
    - The request carries `X-CWA-TELETAN-TYPE = {unsupportedType}`, where `{unsupportedType}` is not an implementation-supported type such as `EVENT`.
  - Failure endpoint:
    `POST /version/v1/tan/teletan`
  - Why this fails:
    Generated tests show `400` when `X-CWA-TELETAN-TYPE` contains an unsupported value.
  - Intentionally violated constraints:
    The TeleTAN type header is outside the implementation-supported enum, even though this header is not documented in OpenAPI.

Endpoint coverage:
- Covers:
  `POST /version/v1/tan/teletan`
- Distinct meaning:
  This endpoint directly generates a human-readable TeleTAN for call-line or internal use.

### Function 3: create registration token from TeleTAN

Function name:
exchange TeleTAN for registration token

Core endpoint(s):
- `POST /version/v1/registrationToken`

Preconditions:
- A TeleTAN exists and has not already been exchanged. This can be satisfied by directly inserting a `TAN` row whose hash corresponds to `{teleTanValue}`, whose type/source state marks it as a TeleTAN, and whose redeemed state allows exchange, or by calling `POST /version/v1/tan/teletan` with the required `Authorization` header and reading the returned `value`.
- The `{teleTanValue}` used as `key` in `POST /version/v1/registrationToken` must be the exact TeleTAN value established above. If the API establishes the TeleTAN, `{teleTanValue}` must be taken from the `value` field in the TeleTAN creation response.

Successful execution:
- Result:
  The endpoint consumes a valid TeleTAN identity and returns a registration token for the corresponding TeleTAN-backed application session.
- Invocation:
  Step 1: `POST /version/v1/registrationToken` with JSON body fields `keyType = "TELETAN"` and `key = {teleTanValue}`.
- Constraints:
  `keyType` must be `TELETAN`. `key` must identify an existing, unconsumed TeleTAN. `keyDob` is not required for TeleTAN registration. The same TeleTAN must not already have been exchanged for a registration token.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No `TAN` row exists for `{teleTanValue}`. This can be produced by starting from an empty database, deleting the TeleTAN beforehand, or intentionally not inserting it directly and not calling `POST /version/v1/tan/teletan`.
  - Failure endpoint:
    `POST /version/v1/registrationToken`
  - Why this fails:
    Generated tests show `400` for `keyType = "TELETAN"` with an arbitrary `key` when no matching TeleTAN exists.
  - Intentionally violated constraints:
    The required TeleTAN state is omitted and the request sends an unknown `key`.

- Branch 2:
  - Preconditions:
    - A TeleTAN exists for `{teleTanValue}`. This can be satisfied by directly inserting the corresponding `TAN` row or by calling `POST /version/v1/tan/teletan` and reading the returned `value`.
    - A registration token has already been created from `{teleTanValue}`. This can be satisfied by directly inserting an `APP_SESSION` row linked to the TeleTAN hash and marking the TeleTAN as consumed as required by the implementation, or by calling `POST /version/v1/registrationToken` once with `keyType = "TELETAN"` and `key = {teleTanValue}`.
    - The failing request reuses the same `{teleTanValue}` as `key`.
  - Failure endpoint:
    `POST /version/v1/registrationToken`
  - Why this fails:
    OpenAPI documents `400` when the GUID or TeleTAN already exists, and the function cannot create a second registration token for the same TeleTAN.
  - Intentionally violated constraints:
    The TeleTAN single-use uniqueness requirement is violated by exchanging the same TeleTAN more than once.

Endpoint coverage:
- Covers:
  `POST /version/v1/registrationToken`
- Distinct meaning:
  This endpoint directly creates a registration token from a TeleTAN rather than from a GUID.

### Function 4: generate TAN from registration token

Function name:
generate TAN

Core endpoint(s):
- `POST /version/v1/tan`

Preconditions:
- A valid application session with registration token `{registrationToken}` exists. This can be satisfied by directly inserting an `APP_SESSION` row whose `REGISTRATION_TOKEN_HASH` corresponds to `{registrationToken}`, or by calling `POST /version/v1/registrationToken` with valid GUID or TeleTAN request data and reading the returned `registrationToken`.
- The `{registrationToken}` sent to `POST /version/v1/tan` must be the exact token established above. If the API establishes the session, this value must be taken from the `registrationToken` field in the registration-token response.

Successful execution:
- Result:
  The endpoint generates a TAN associated with the existing registration token and returns the TAN in the flattened response field `tan`.
- Invocation:
  Step 1: `POST /version/v1/tan` with JSON body field `registrationToken = {registrationToken}` and optional `responsePadding`.
- Constraints:
  `registrationToken` is required and must match the UUID pattern documented by OpenAPI. The token must resolve to an existing `APP_SESSION` row. Generated tests show successful responses have status `201` and contain `tan`; they may also contain `responsePadding`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application session exists for `{registrationToken}`. This can be produced by starting from an empty database, deleting the session beforehand, or intentionally not inserting an `APP_SESSION` row directly and not calling `POST /version/v1/registrationToken`.
  - Failure endpoint:
    `POST /version/v1/tan`
  - Why this fails:
    OpenAPI documents `400` when the registration token does not exist, and generated tests confirm `400` for unknown tokens.
  - Intentionally violated constraints:
    The required registration-token session state is omitted while a syntactically token-like value is sent.

- Branch 2:
  - Preconditions:
    - The request body does not provide a usable `registrationToken` value.
  - Failure endpoint:
    `POST /version/v1/tan`
  - Why this fails:
    OpenAPI requires `registrationToken`, and generated tests show bad or unmatched token requests return `400`.
  - Intentionally violated constraints:
    The request omits `registrationToken` or sends a value that does not satisfy the expected registration-token format or lookup requirement.

Endpoint coverage:
- Covers:
  `POST /version/v1/tan`
- Distinct meaning:
  This endpoint directly converts an existing registration token into a TAN.

### Function 5: retrieve test result for GUID registration token

Function name:
retrieve GUID test result

Core endpoint(s):
- `POST /version/v1/testresult`

Preconditions:
- A GUID-backed application session with registration token `{registrationToken}` exists. This can be satisfied by directly inserting an `APP_SESSION` row whose `REGISTRATION_TOKEN_HASH` corresponds to `{registrationToken}`, whose `SOT` marks a hashed-GUID session, and whose `HASHED_GUID` and `HASHED_GUID_DOB` fields match the intended GUID identity, or by calling `POST /version/v1/registrationToken` with JSON body fields `keyType = "GUID"`, `key = {hashedGuid}`, and `keyDob = {hashedGuidDob}`.
- The `{registrationToken}` sent to `POST /version/v1/testresult` must be the exact token established above. If the API establishes the session, this value must be taken from the `registrationToken` field in the registration-token response.
- Any external lab-result state needed for GUID results must exist in the configured test-result service; this project exposes no OpenAPI endpoint for creating that external state, so direct API setup is not available in this directory.

Successful execution:
- Result:
  The endpoint retrieves the COVID-19 test result associated with the GUID-backed registration token and returns flattened fields such as `testResult`, `sc`, and `responsePadding`.
- Invocation:
  Step 1: `POST /version/v1/testresult` with JSON body field `registrationToken = {registrationToken}` and optional `responsePadding`.
- Constraints:
  `registrationToken` is required and must identify an existing GUID-backed `APP_SESSION`. For GUID sessions, the observed result depends on the configured external test-result service. OpenAPI documents a deferred wrapper response, but generated tests show flattened response fields.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No application session exists for `{registrationToken}`. This can be produced by starting from an empty database, deleting the session beforehand, or intentionally not inserting an `APP_SESSION` row directly and not calling `POST /version/v1/registrationToken`.
  - Failure endpoint:
    `POST /version/v1/testresult`
  - Why this fails:
    Generated tests confirm `400` for unknown registration tokens.
  - Intentionally violated constraints:
    The required registration-token session state is omitted.

- Branch 2:
  - Preconditions:
    - The request body does not provide a usable `registrationToken` value.
  - Failure endpoint:
    `POST /version/v1/testresult`
  - Why this fails:
    OpenAPI requires `registrationToken` in the request body, and the implementation rejects malformed or missing token bodies.
  - Intentionally violated constraints:
    The request omits `registrationToken` or sends a malformed token.

Endpoint coverage:
- Covers:
  `POST /version/v1/testresult`
- Distinct meaning:
  This endpoint directly retrieves a lab-backed test result for a GUID-derived registration token.

### Function 6: retrieve positive test result for TeleTAN registration token

Function name:
retrieve TeleTAN positive test result

Core endpoint(s):
- `POST /version/v1/testresult`

Preconditions:
- A TeleTAN exists and has not already been exchanged before registration. This can be satisfied by directly inserting a `TAN` row whose hash corresponds to `{teleTanValue}`, whose type/source state marks it as a TeleTAN, and whose redeemed state allows exchange, or by calling `POST /version/v1/tan/teletan` with the required `Authorization` header and reading the returned `value`.
- A TeleTAN-backed application session with registration token `{registrationToken}` exists. This can be satisfied by directly inserting an `APP_SESSION` row whose `REGISTRATION_TOKEN_HASH` corresponds to `{registrationToken}`, whose `TELE_TAN_HASH` corresponds to `{teleTanValue}`, and whose `SOT` marks a TeleTAN session, or by calling `POST /version/v1/registrationToken` with JSON body fields `keyType = "TELETAN"` and `key = {teleTanValue}`.
- The `{teleTanValue}` used to create the registration token must be the exact `value` returned by TeleTAN creation when the API establishes the TeleTAN, and `{registrationToken}` sent to `POST /version/v1/testresult` must be the exact `registrationToken` returned by the TeleTAN registration-token exchange.

Successful execution:
- Result:
  The endpoint retrieves the test result for a TeleTAN-derived registration token. OpenAPI states that if the registration token belongs to a TeleTAN, the result is always positive.
- Invocation:
  Step 1: `POST /version/v1/testresult` with JSON body field `registrationToken = {registrationToken}` and optional `responsePadding`.
- Constraints:
  The registration token must resolve to a TeleTAN-backed `APP_SESSION`. The TeleTAN-to-token value chain must be preserved exactly: TeleTAN `value` to registration-token `key`, then registration-token response value to test-result request.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No TeleTAN-backed application session exists for `{registrationToken}`. This can be produced by starting from an empty database, deleting the session beforehand, or intentionally not inserting the `APP_SESSION` row directly and not calling either `POST /version/v1/tan/teletan` or `POST /version/v1/registrationToken`.
  - Failure endpoint:
    `POST /version/v1/testresult`
  - Why this fails:
    The test-result endpoint requires an existing registration token, and generated tests confirm `400` for unknown tokens.
  - Intentionally violated constraints:
    The required TeleTAN-derived registration-token state is omitted.

- Branch 2:
  - Preconditions:
    - No TeleTAN exists for `{teleTanValue}`. This can be produced by starting from an empty database, deleting the TeleTAN beforehand, or intentionally not inserting the `TAN` row directly and not calling `POST /version/v1/tan/teletan`.
    - The request tries to create the prerequisite registration token with `keyType = "TELETAN"` and `key = {teleTanValue}` before any test-result request can use it.
  - Failure endpoint:
    `POST /version/v1/registrationToken`
  - Why this fails:
    An unknown TeleTAN cannot produce a registration token, so the sequence cannot reach a valid TeleTAN test-result lookup.
  - Intentionally violated constraints:
    The TeleTAN creation state is omitted and an arbitrary TeleTAN key is used for registration-token exchange.

Endpoint coverage:
- Covers:
  `POST /version/v1/testresult`
- Distinct meaning:
  This endpoint directly retrieves the special always-positive result for a TeleTAN-derived registration token.

### Function 7: verify issued TAN

Function name:
verify TAN

Core endpoint(s):
- `POST /version/v1/tan/verify`

Preconditions:
- A valid application session with registration token `{registrationToken}` exists. This can be satisfied by directly inserting an `APP_SESSION` row whose `REGISTRATION_TOKEN_HASH` corresponds to `{registrationToken}`, or by calling `POST /version/v1/registrationToken` with valid GUID or TeleTAN request data and reading the returned `registrationToken`.
- A TAN `{tan}` exists and was generated for `{registrationToken}`. This can be satisfied by directly inserting a `TAN` row whose `TAN_HASH` corresponds to `{tan}`, whose `TYPE` marks it as a TAN, and whose source/validity state allows verification, or by calling `POST /version/v1/tan` with JSON body field `registrationToken = {registrationToken}` and reading the returned `tan`.
- The `{tan}` sent to `POST /version/v1/tan/verify` must be the exact TAN established above. If the API establishes the TAN, this value must be taken from the `tan` field in the TAN generation response.

Successful execution:
- Result:
  The endpoint verifies that the supplied TAN was formerly issued by the verification server.
- Invocation:
  Step 1: `POST /version/v1/tan/verify` with JSON body field `tan = {tan}` and optional `responsePadding`.
- Constraints:
  `tan` is required and must satisfy the UUID pattern documented by OpenAPI. The TAN must resolve to an issued TAN record. OpenAPI documents `200` for a valid issued TAN; generated tests only show `404` branches for unknown or non-matching TANs.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No issued TAN record exists for `{tan}`. This can be produced by starting from an empty database, deleting the TAN beforehand, or intentionally not inserting a `TAN` row directly and not calling `POST /version/v1/registrationToken` or `POST /version/v1/tan`.
  - Failure endpoint:
    `POST /version/v1/tan/verify`
  - Why this fails:
    OpenAPI documents `404` when the TAN cannot be verified, and generated tests confirm `404` for arbitrary TAN values.
  - Intentionally violated constraints:
    The required issued-TAN state is omitted while a syntactically valid but unknown TAN is sent.

- Branch 2:
  - Preconditions:
    - A valid application session with registration token `{registrationToken}` exists. This can be satisfied by directly inserting the matching `APP_SESSION` row or by calling `POST /version/v1/registrationToken` with valid request data and reading the returned `registrationToken`.
    - A TAN `{issuedTan}` has been generated for `{registrationToken}`. This can be satisfied by directly inserting the matching `TAN` row or by calling `POST /version/v1/tan` with `registrationToken = {registrationToken}` and reading the returned `tan`.
    - The failing verification request sends `{differentTan}` instead of `{issuedTan}`.
  - Failure endpoint:
    `POST /version/v1/tan/verify`
  - Why this fails:
    Verification is tied to the issued TAN value; using a different TAN cannot match the generated TAN record.
  - Intentionally violated constraints:
    The generated TAN value is not reused correctly.

- Branch 3:
  - Preconditions:
    - The request body does not provide a usable `tan` value.
  - Failure endpoint:
    `POST /version/v1/tan/verify`
  - Why this fails:
    OpenAPI requires `tan` in the request body and documents `400` for bad requests.
  - Intentionally violated constraints:
    The request omits `tan` or sends a value that does not satisfy the TAN UUID pattern.

Endpoint coverage:
- Covers:
  `POST /version/v1/tan/verify`
- Distinct meaning:
  This endpoint directly checks whether a TAN was issued by this verification server.
