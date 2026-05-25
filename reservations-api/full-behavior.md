### Function 1: register user and issue access token

Function name:
register user and issue access token

Core endpoint(s):
- `POST /api/user/register`

Preconditions:
- No user with `username = {username}` exists in the `users` collection. This can be satisfied by starting from a database without that username, deleting that user directly, or avoiding any prior successful `POST /api/user/register` call with the same `username`.
- No user with `email = {email}` exists in the `users` collection. This can be satisfied by starting from a database without that email, deleting that user directly, or avoiding any prior successful `POST /api/user/register` call with the same `email`.

Successful execution:
- Result:
  The endpoint creates a new user record and returns an `AuthResponse.accessToken` JWT for that user.
- Invocation:
  Step 1: `POST /api/user/register` with JSON body fields `username = {username}`, `email = {email}`, and `password = {base64Password}`.
- Constraints:
  `username`, `email`, and `password` must be nonblank, and `email` must satisfy `@Email`. The implementation decodes `password` from Base64, stores a BCrypt hash of the decoded password, assigns role `USER`, authenticates the newly saved user, and signs a JWT. OpenAPI documents `password` as a plain string and does not state the Base64 requirement; the source code requires Base64 decoding before saving and authentication.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A user with `username = {username}` already exists in the `users` collection. This can be satisfied by directly inserting a `User` document with that username or by calling `POST /api/user/register` once with `username = {username}`, any valid unused `email`, and a Base64-encoded `password`.
  - Failure endpoint:
    `POST /api/user/register`
  - Why this fails:
    The controller checks `userService.existsByUsername({username})` before checking email or saving, throws `DuplicateUserException`, and the exception handler returns `409 CONFLICT`.
  - Intentionally violated constraints:
    The username uniqueness requirement is violated by attempting to register an already used `username`.
- Branch 2:
  - Preconditions:
    - A user with `email = {email}` already exists in the `users` collection while `username = {newUsername}` is unused. This can be satisfied by directly inserting a `User` document with that email or by calling `POST /api/user/register` once with `email = {email}`, a different valid `username`, and a Base64-encoded `password`.
  - Failure endpoint:
    `POST /api/user/register`
  - Why this fails:
    The username check passes, then `userService.existsByEmail({email})` returns true, causing `DuplicateUserException` and `409 CONFLICT`.
  - Intentionally violated constraints:
    The email uniqueness requirement is violated by attempting to register an already used `email`.
- Branch 3:
  - Preconditions:
    - No database state is required; the failure is caused entirely by invalid request-body values.
  - Failure endpoint:
    `POST /api/user/register`
  - Why this fails:
    Bean validation rejects the request before user creation.
  - Intentionally violated constraints:
    `username`, `email`, or `password` is blank or missing, or `email` does not satisfy `@Email`.
- Branch 4:
  - Preconditions:
    - No database state is required; the failure is caused by a malformed `password` field.
  - Failure endpoint:
    `POST /api/user/register`
  - Why this fails:
    The controller directly calls `Base64.getDecoder().decode(registerRequest.getPassword())`; malformed Base64 raises an unhandled `IllegalArgumentException` because there is no custom handler for this case.
  - Intentionally violated constraints:
    `password` is not decodable as Base64 even though the implementation requires Base64 input.

Endpoint coverage:
- Covers:
  `POST /api/user/register`
- Distinct meaning:
  This endpoint is the direct user-account creation capability and also issues the initial JWT.

### Function 2: log in user and issue access token

Function name:
log in user and issue access token

Core endpoint(s):
- `POST /api/user/login`

Preconditions:
- A user with `username = {username}` exists in the `users` collection, has role `USER` or another Spring Security authority, and has a stored BCrypt password hash matching the decoded value of `{base64Password}`. This can be satisfied by directly inserting a compatible `User` document or by calling `POST /api/user/register` with `username = {username}`, any valid unused `email`, and `password = {base64Password}`.

Successful execution:
- Result:
  The endpoint authenticates the existing user and returns a new `AuthResponse.accessToken` JWT.
- Invocation:
  Step 1: `POST /api/user/login` with JSON body fields `username = {username}` and `password = {base64Password}`.
- Constraints:
  `username` and `password` must be nonblank. The `password` request value must be Base64-encoded, and the decoded password must match the stored BCrypt hash for `username`. OpenAPI documents `password` only as a string; the source code requires Base64 decoding before authentication.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user with `username = {username}` exists in the `users` collection. This can be produced by starting from an empty database, deleting that user directly, or intentionally not inserting the user directly and not calling `POST /api/user/register` for that username.
  - Failure endpoint:
    `POST /api/user/login`
  - Why this fails:
    Authentication cannot load a matching user through `UserDetailsServiceImpl`, so no `AuthResponse` is produced.
  - Intentionally violated constraints:
    The required existing user state was omitted.
- Branch 2:
  - Preconditions:
    - A user with `username = {username}` exists with a stored BCrypt password hash matching decoded password `{password1}`. This can be satisfied by direct database insertion or by calling `POST /api/user/register` with `username = {username}` and `password = Base64({password1})`.
  - Failure endpoint:
    `POST /api/user/login`
  - Why this fails:
    The controller decodes the submitted password and passes it to `AuthenticationManager`; authentication rejects it because decoded `{password2}` does not match the stored hash.
  - Intentionally violated constraints:
    The login request uses `password = Base64({password2})` for a different decoded password than the one used to create the user.
- Branch 3:
  - Preconditions:
    - No database state is required; the failure is caused entirely by invalid request-body values.
  - Failure endpoint:
    `POST /api/user/login`
  - Why this fails:
    Bean validation rejects the request body before authentication.
  - Intentionally violated constraints:
    `username` or `password` is blank or missing.
- Branch 4:
  - Preconditions:
    - No database state is required; the failure is caused by a malformed `password` field.
  - Failure endpoint:
    `POST /api/user/login`
  - Why this fails:
    The controller directly decodes the submitted password with `Base64.getDecoder().decode(...)`; malformed Base64 raises an unhandled `IllegalArgumentException`.
  - Intentionally violated constraints:
    `password` is not decodable as Base64 even though the implementation requires Base64 input.

Endpoint coverage:
- Covers:
  `POST /api/user/login`
- Distinct meaning:
  This endpoint is the direct credential-authentication capability and issues a JWT for an already existing user.

### Function 3: create reservation

Function name:
create reservation

Core endpoint(s):
- `POST /api/reservation`

Preconditions:
- An authenticated user exists and a valid bearer JWT is available for the request. This can be satisfied by directly inserting a `User` document with a valid role and signing a compatible JWT with the application secret, or by calling `POST /api/user/register` or `POST /api/user/login` and reusing the returned `AuthResponse.accessToken`.

Successful execution:
- Result:
  The endpoint creates a reservation document and returns its `uuid`, `reservationFor`, `reservationFrom`, and `reservationTo`.
- Invocation:
  Step 1: `POST /api/reservation` with header `Authorization: Bearer {accessToken}` and JSON body fields `reservationFor = {reservationFor}`, `reservationFrom = {from}`, `reservationTo = {to}`, and `createdAt = {createdAt}`.
- Constraints:
  `reservationFor` must be nonblank. `reservationFrom`, `reservationTo`, and `createdAt` must be non-null and must use the implementation format `yyyy-MM-dd HH:mm:ss`; OpenAPI only documents these fields as `date-time`. `reservationFrom` must not be after `reservationTo`, `reservationFrom` must be in the future relative to server time, and neither the requested start nor end may be strictly inside an existing reservation interval. The overlap check is global across all reservations and is not scoped by user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated security context exists for the request. This can be produced by omitting `Authorization`, using a value without the `Bearer ` prefix, using an expired or invalid JWT, or using a token whose subject cannot be loaded from the `users` collection.
  - Failure endpoint:
    `POST /api/reservation`
  - Why this fails:
    Spring Security protects reservation endpoints and returns `401 UNAUTHORIZED` when the JWT filter does not establish authentication.
  - Intentionally violated constraints:
    The valid bearer token requirement was omitted or invalidated.
- Branch 2:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
  - Failure endpoint:
    `POST /api/reservation`
  - Why this fails:
    `ReservationServiceImpl.validateDates` detects `reservationFrom.isAfter(reservationTo)`, throws `ReservationException`, and the exception handler returns `422 UNPROCESSABLE_ENTITY`.
  - Intentionally violated constraints:
    The request sets `reservationFrom` later than `reservationTo`.
- Branch 3:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
  - Failure endpoint:
    `POST /api/reservation`
  - Why this fails:
    `ReservationServiceImpl.validateDates` compares server `LocalDateTime.now()` with `reservationFrom`, throws `ReservationException` when the start is in the past, and the exception handler returns `422 UNPROCESSABLE_ENTITY`.
  - Intentionally violated constraints:
    The request sets `reservationFrom` before the current server time.
- Branch 4:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
    - An existing reservation with interval `[{existingFrom}, {existingTo}]` is present in the `reservations` collection. This can be satisfied by directly inserting a `Reservation` document or by calling `POST /api/reservation` once with a valid bearer token and a non-overlapping future interval, then retaining the returned `uuid`.
  - Failure endpoint:
    `POST /api/reservation`
  - Why this fails:
    The repository query `findAllBetween(from, to)` finds an existing reservation when the new start or end is strictly inside the existing interval, and the service throws `ReservationException` with `422 UNPROCESSABLE_ENTITY`.
  - Intentionally violated constraints:
    The new interval has `{existingFrom} < {from} < {existingTo}` or `{existingFrom} < {to} < {existingTo}`.
- Branch 5:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
  - Failure endpoint:
    `POST /api/reservation`
  - Why this fails:
    Bean validation or Jackson date parsing rejects the request before a reservation is inserted.
  - Intentionally violated constraints:
    `reservationFor` is blank or missing, `reservationFrom`, `reservationTo`, or `createdAt` is missing, or a date field does not parse as `yyyy-MM-dd HH:mm:ss`.

Endpoint coverage:
- Covers:
  `POST /api/reservation`
- Distinct meaning:
  This endpoint is the direct reservation-creation capability.

### Function 4: list all reservations

Function name:
list all reservations

Core endpoint(s):
- `GET /api/reservation`

Preconditions:
- An authenticated user exists and a valid bearer JWT is available for the request. This can be satisfied by directly inserting a `User` document with a valid role and signing a compatible JWT with the application secret, or by calling `POST /api/user/register` or `POST /api/user/login` and reusing the returned `AuthResponse.accessToken`.

Successful execution:
- Result:
  The endpoint returns all reservation documents sorted by `reservationFrom` descending.
- Invocation:
  Step 1: `GET /api/reservation` with header `Authorization: Bearer {accessToken}`.
- Constraints:
  No reservation document is required for success; an empty `reservations` collection returns `200 OK` with an empty JSON array. The implementation sorts with `Sort.by("reservationFrom").descending()`. The security configuration contains both `GET /api/reservation/**` for `ADMIN` or `USER` and a later `GET /api/reservation` rule for `ADMIN`; because the broader reservation rule is evaluated first in source order, the endpoint is effectively available to authenticated `USER` tokens produced by registration or login.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated security context exists for the request. This can be produced by omitting `Authorization`, using a value without the `Bearer ` prefix, using an expired or invalid JWT, or using a token whose subject cannot be loaded from the `users` collection.
  - Failure endpoint:
    `GET /api/reservation`
  - Why this fails:
    Spring Security protects reservation endpoints and returns `401 UNAUTHORIZED` when the JWT filter does not establish authentication.
  - Intentionally violated constraints:
    The valid bearer token requirement was omitted or invalidated.
- Branch 2:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
    - No reservation documents exist in the `reservations` collection. This can be satisfied by starting from an empty collection, deleting reservations directly, or intentionally not calling `POST /api/reservation`.
  - Failure endpoint:
    `GET /api/reservation`
  - Why this fails:
    This does not fail; the implementation returns `200 OK` with an empty JSON array.
  - Intentionally violated constraints:
    No failure constraint is violated; the reservation setup state is optional for this list function.

Endpoint coverage:
- Covers:
  `GET /api/reservation`
- Distinct meaning:
  This endpoint is the direct global reservation collection read capability.

### Function 5: list reservations for user value

Function name:
list reservations for user value

Core endpoint(s):
- `GET /api/reservation/{username}`

Preconditions:
- An authenticated user exists and a valid bearer JWT is available for the request. This can be satisfied by directly inserting a `User` document with a valid role and signing a compatible JWT with the application secret, or by calling `POST /api/user/register` or `POST /api/user/login` and reusing the returned `AuthResponse.accessToken`.
- A reservation with `reservationFor = {username}` exists in the `reservations` collection if the caller expects a non-empty result. This can be satisfied by directly inserting a `Reservation` document with that `reservationFor` value or by calling `POST /api/reservation` with `Authorization: Bearer {accessToken}` and body field `reservationFor = {username}`. The `uuid` produced by that creation call is not required by the list endpoint.

Successful execution:
- Result:
  The endpoint returns reservations whose `reservationFor` field exactly equals the path `{username}`, sorted by `reservationFrom` descending.
- Invocation:
  Step 1: `GET /api/reservation/{username}` with path value `{username}` and header `Authorization: Bearer {accessToken}`.
- Constraints:
  `{username}` must be nonblank. The implementation does not verify that `{username}` is a registered user and does not require it to match the JWT subject; it only filters reservation documents by the stored `reservationFor` string.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated security context exists for the request. This can be produced by omitting `Authorization`, using a value without the `Bearer ` prefix, using an expired or invalid JWT, or using a token whose subject cannot be loaded from the `users` collection.
  - Failure endpoint:
    `GET /api/reservation/{username}`
  - Why this fails:
    Spring Security protects reservation endpoints and returns `401 UNAUTHORIZED` when the JWT filter does not establish authentication.
  - Intentionally violated constraints:
    The valid bearer token requirement was omitted or invalidated.
- Branch 2:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
  - Failure endpoint:
    `GET /api/reservation/{username}`
  - Why this fails:
    The controller path variable has `@NotBlank`; a blank or whitespace path value triggers `ConstraintViolationException`, and the exception handler returns `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    `{username}` is blank or whitespace rather than a nonblank path value.
- Branch 3:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
    - No reservation with `reservationFor = {username}` exists in the `reservations` collection. This can be satisfied by direct database cleanup or by intentionally not calling `POST /api/reservation` with `reservationFor = {username}`.
  - Failure endpoint:
    `GET /api/reservation/{username}`
  - Why this fails:
    This does not fail; the implementation returns `200 OK` with an empty JSON array.
  - Intentionally violated constraints:
    No failure constraint is violated; the matching reservation state is optional for this filtered list function.

Endpoint coverage:
- Covers:
  `GET /api/reservation/{username}`
- Distinct meaning:
  This endpoint is the direct reservation read capability filtered by the `reservationFor` field.

### Function 6: update reservation

Function name:
update reservation

Core endpoint(s):
- `PUT /api/reservation`

Preconditions:
- An authenticated user exists and a valid bearer JWT is available for the request. This can be satisfied by directly inserting a `User` document with a valid role and signing a compatible JWT with the application secret, or by calling `POST /api/user/register` or `POST /api/user/login` and reusing the returned `AuthResponse.accessToken`.
- A reservation with `uuid = {reservationUuid}` exists in the `reservations` collection. This can be satisfied by directly inserting a `Reservation` document with that `uuid` or by calling `POST /api/reservation` with `Authorization: Bearer {accessToken}` and valid body fields, then reusing the returned `ReservationResponse.uuid`.
- The `{reservationUuid}` used in the `PUT /api/reservation` request body must identify the reservation described above. If the API is used to establish the reservation, `{reservationUuid}` must be obtained from the `POST /api/reservation` response.

Successful execution:
- Result:
  The endpoint updates the target reservation's `uuid`, `reservationFor`, `reservationFrom`, and `reservationTo` fields and returns the updated reservation response.
- Invocation:
  Step 1: `PUT /api/reservation` with header `Authorization: Bearer {accessToken}` and JSON body fields `uuid = {reservationUuid}`, `reservationFor = {reservationFor}`, `reservationFrom = {from}`, and `reservationTo = {to}`.
- Constraints:
  `uuid` and `reservationFor` must be nonblank. `reservationFrom` and `reservationTo` must be non-null and must use the implementation format `yyyy-MM-dd HH:mm:ss`; OpenAPI only documents these fields as `date-time`. `reservationFrom` must not be after `reservationTo`, `reservationFrom` must be in the future relative to server time, and the updated start or end must not be strictly inside any existing reservation interval. The overlap check is global and runs before loading the target reservation, so updating to a time inside another reservation's interval fails; changing an existing reservation to an interval inside its own original boundaries can also be rejected because the query does not exclude the target reservation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated security context exists for the request. This can be produced by omitting `Authorization`, using a value without the `Bearer ` prefix, using an expired or invalid JWT, or using a token whose subject cannot be loaded from the `users` collection.
  - Failure endpoint:
    `PUT /api/reservation`
  - Why this fails:
    Spring Security protects reservation endpoints and returns `401 UNAUTHORIZED` when the JWT filter does not establish authentication.
  - Intentionally violated constraints:
    The valid bearer token requirement was omitted or invalidated.
- Branch 2:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
    - A reservation exists with `uuid = {existingUuid}`. This can be satisfied by directly inserting a `Reservation` document or by calling `POST /api/reservation` and capturing its returned `uuid`.
    - No reservation exists with `uuid = {missingUuid}`. This can be satisfied by direct database cleanup or by intentionally not using the `uuid` returned from `POST /api/reservation`.
  - Failure endpoint:
    `PUT /api/reservation`
  - Why this fails:
    `repository.findByUuid({missingUuid})` returns no model, and the mapper receives no valid target reservation before save. The implementation has no not-found handling here, so this is an unhandled implementation error rather than a documented `404`.
  - Intentionally violated constraints:
    The request body uses `uuid = {missingUuid}` instead of the existing reservation's generated `uuid`.
- Branch 3:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
    - A reservation with `uuid = {reservationUuid}` exists. This can be satisfied by direct insertion or by calling `POST /api/reservation` and reusing the returned `uuid`.
  - Failure endpoint:
    `PUT /api/reservation`
  - Why this fails:
    `ReservationServiceImpl.validateDates` detects `reservationFrom.isAfter(reservationTo)`, throws `ReservationException`, and the exception handler returns `422 UNPROCESSABLE_ENTITY`.
  - Intentionally violated constraints:
    The update request sets `reservationFrom` later than `reservationTo`.
- Branch 4:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
    - A reservation with `uuid = {reservationUuid}` exists. This can be satisfied by direct insertion or by calling `POST /api/reservation` and reusing the returned `uuid`.
  - Failure endpoint:
    `PUT /api/reservation`
  - Why this fails:
    `ReservationServiceImpl.validateDates` compares server `LocalDateTime.now()` with `reservationFrom`, throws `ReservationException` when the start is in the past, and the exception handler returns `422 UNPROCESSABLE_ENTITY`.
  - Intentionally violated constraints:
    The update request sets `reservationFrom` before the current server time.
- Branch 5:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
    - A reservation with interval `[{existingFrom}, {existingTo}]` exists in the `reservations` collection. This can be satisfied by directly inserting a `Reservation` document or by calling `POST /api/reservation` once with a valid bearer token and retaining the returned `uuid`.
    - A different reservation with `uuid = {reservationUuid}` exists and is the target of the update. This can be satisfied by directly inserting a second `Reservation` document or by calling `POST /api/reservation` with a non-overlapping future interval and retaining that second returned `uuid`.
  - Failure endpoint:
    `PUT /api/reservation`
  - Why this fails:
    The repository query `findAllBetween(from, to)` finds the existing reservation when the updated start or end is strictly inside that interval, and the service throws `ReservationException` with `422 UNPROCESSABLE_ENTITY`.
  - Intentionally violated constraints:
    The updated interval has `{existingFrom} < {from} < {existingTo}` or `{existingFrom} < {to} < {existingTo}`.
- Branch 6:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
    - A reservation with `uuid = {reservationUuid}` exists if the invalid request is meant to reach service-level update logic. This can be satisfied by direct insertion or by calling `POST /api/reservation` and reusing the returned `uuid`.
  - Failure endpoint:
    `PUT /api/reservation`
  - Why this fails:
    Bean validation or Jackson date parsing rejects the update request before a successful save.
  - Intentionally violated constraints:
    `uuid` or `reservationFor` is blank or missing, `reservationFrom` or `reservationTo` is missing, or a date field does not parse as `yyyy-MM-dd HH:mm:ss`.

Endpoint coverage:
- Covers:
  `PUT /api/reservation`
- Distinct meaning:
  This endpoint is the direct reservation-update capability, selecting the target reservation by request-body `uuid`.

### Function 7: delete reservation

Function name:
delete reservation

Core endpoint(s):
- `DELETE /api/reservation/{reservationUuid}`

Preconditions:
- An authenticated user exists and a valid bearer JWT is available for the request. This can be satisfied by directly inserting a `User` document with a valid role and signing a compatible JWT with the application secret, or by calling `POST /api/user/register` or `POST /api/user/login` and reusing the returned `AuthResponse.accessToken`.
- A reservation with `uuid = {reservationUuid}` exists in the `reservations` collection if the caller expects an actual row to be removed. This can be satisfied by directly inserting a `Reservation` document with that `uuid` or by calling `POST /api/reservation` with `Authorization: Bearer {accessToken}` and valid body fields, then reusing the returned `ReservationResponse.uuid`.
- The `{reservationUuid}` path value used by `DELETE /api/reservation/{reservationUuid}` must identify the reservation described above. If the API is used to establish the reservation, `{reservationUuid}` must be obtained from the `POST /api/reservation` response.

Successful execution:
- Result:
  The endpoint deletes the reservation identified by `{reservationUuid}` and returns an empty response body.
- Invocation:
  Step 1: `DELETE /api/reservation/{reservationUuid}` with path value `{reservationUuid}` and header `Authorization: Bearer {accessToken}`.
- Constraints:
  `{reservationUuid}` must be nonblank. The implementation deletes by repository id and does not verify ownership, `reservationFor`, or JWT subject. It also does not check existence before deletion.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated security context exists for the request. This can be produced by omitting `Authorization`, using a value without the `Bearer ` prefix, using an expired or invalid JWT, or using a token whose subject cannot be loaded from the `users` collection.
  - Failure endpoint:
    `DELETE /api/reservation/{reservationUuid}`
  - Why this fails:
    Spring Security protects reservation endpoints and returns `401 UNAUTHORIZED` when the JWT filter does not establish authentication.
  - Intentionally violated constraints:
    The valid bearer token requirement was omitted or invalidated.
- Branch 2:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
  - Failure endpoint:
    `DELETE /api/reservation/{reservationUuid}`
  - Why this fails:
    The controller path variable has `@NotBlank`; a blank or whitespace path value triggers `ConstraintViolationException`, and the exception handler returns `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    `{reservationUuid}` is blank or whitespace rather than a nonblank path value.
- Branch 3:
  - Preconditions:
    - An authenticated user exists and a valid bearer JWT is available. This can be satisfied by direct user/token setup or by calling `POST /api/user/register` or `POST /api/user/login` and reusing `AuthResponse.accessToken`.
    - A reservation with `uuid = {existingUuid}` exists in the `reservations` collection. This can be satisfied by directly inserting a `Reservation` document or by calling `POST /api/reservation` and capturing the returned `uuid`.
    - No reservation exists with `uuid = {wrongUuid}`. This can be satisfied by direct database cleanup or by intentionally not using the `uuid` returned from `POST /api/reservation`.
  - Failure endpoint:
    `DELETE /api/reservation/{reservationUuid}`
  - Why this fails:
    This does not fail at the API layer. The implementation calls `repository.deleteById({wrongUuid})` without first checking existence, so a wrong nonblank UUID can still produce an OK empty response while leaving the intended reservation untouched.
  - Intentionally violated constraints:
    The path value uses `{wrongUuid}` instead of the generated `uuid` for the reservation intended for deletion.

Endpoint coverage:
- Covers:
  `DELETE /api/reservation/{reservationUuid}`
- Distinct meaning:
  This endpoint is the direct reservation-deletion capability, selecting the target reservation by path UUID.
