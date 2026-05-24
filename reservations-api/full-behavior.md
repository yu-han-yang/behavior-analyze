### Behavior 1: register user and receive access token

Behavior name:
register user and receive access token

Successful execution:
- Result:
  This behavior creates a new user and returns an `accessToken`.
- Endpoint sequence:
  Step 1: `POST /api/user/register` with `username`, `email`, and Base64-encoded `password`.
- Constraints:
  `username`, `email`, and `password` must be nonblank; `email` must be valid email syntax. The implementation decodes `password` from Base64, stores a BCrypt hash, assigns role `USER`, authenticates the new user, and returns `AuthResponse.accessToken`. OpenAPI does not state the Base64 password requirement, but the source requires it.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `username` is already used.
  - Endpoint group:
    Step 1: `POST /api/user/register` with `username = U`.
    Step 2: `POST /api/user/register` again with `username = U`.
  - Failure endpoint:
    `POST /api/user/register`
  - Why this fails:
    The controller checks `existsByUsername` first and throws `DuplicateUserException`, returned as `409 CONFLICT`.
  - Intentionally violated constraints:
    The second registration reuses the first registration’s `username`.

- Branch 2:
  - Unsatisfied condition:
    `email` is already used.
  - Endpoint group:
    Step 1: `POST /api/user/register` with `email = E` and `username = U1`.
    Step 2: `POST /api/user/register` with `email = E` and `username = U2`.
  - Failure endpoint:
    `POST /api/user/register`
  - Why this fails:
    The username check passes, then `existsByEmail` fails and returns `409 CONFLICT`.
  - Intentionally violated constraints:
    The second registration reuses the first registration’s `email`.

- Branch 3:
  - Unsatisfied condition:
    Required fields are blank, missing, or `email` is invalid.
  - Endpoint group:
    Step 1: `POST /api/user/register`
  - Failure endpoint:
    `POST /api/user/register`
  - Why this fails:
    Bean validation rejects the request before user creation.
  - Intentionally violated constraints:
    `username`, `email`, or `password` violates `@NotBlank`, or `email` violates `@Email`.

- Branch 4:
  - Unsatisfied condition:
    `password` is not valid Base64.
  - Endpoint group:
    Step 1: `POST /api/user/register`
  - Failure endpoint:
    `POST /api/user/register`
  - Why this fails:
    The controller calls Base64 decoding directly; the implementation has no custom handler for malformed Base64.
  - Intentionally violated constraints:
    `password` is not decodable as Base64.

Endpoint coverage:
- Covers:
  `POST /api/user/register`
- Distinct meaning:
  Creates a user account and returns a JWT access token.

### Behavior 2: log in user and receive access token

Behavior name:
log in user and receive access token

Successful execution:
- Result:
  This behavior authenticates an existing user and returns a new `accessToken`.
- Endpoint sequence:
  Step 1: `POST /api/user/register` with `username = U`, `email = E`, and Base64-encoded `password = P`.
  Step 2: `POST /api/user/login` with `username = U` and the same Base64-encoded `password = P`.
- Constraints:
  Step 2 must reuse the same `username` and decoded password established by Step 1. `password` must be Base64-encoded because the implementation decodes it before authentication. If the user already exists outside this sequence, Step 1 can be replaced by that existing state.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The user does not exist.
  - Endpoint group:
    Step 1: `POST /api/user/login`
  - Failure endpoint:
    `POST /api/user/login`
  - Why this fails:
    Authentication cannot load or verify the user, so no `AuthResponse` is produced.
  - Intentionally violated constraints:
    The prerequisite `POST /api/user/register` for that `username` is intentionally omitted.

- Branch 2:
  - Unsatisfied condition:
    The password does not match the registered user.
  - Endpoint group:
    Step 1: `POST /api/user/register` with `username = U` and Base64-encoded `password = P1`.
    Step 2: `POST /api/user/login` with `username = U` and Base64-encoded `password = P2`.
  - Failure endpoint:
    `POST /api/user/login`
  - Why this fails:
    Authentication receives the decoded wrong password and rejects it.
  - Intentionally violated constraints:
    Step 2 uses a different decoded password from Step 1.

- Branch 3:
  - Unsatisfied condition:
    `username` or `password` is blank or missing.
  - Endpoint group:
    Step 1: `POST /api/user/login`
  - Failure endpoint:
    `POST /api/user/login`
  - Why this fails:
    Bean validation rejects the request body.
  - Intentionally violated constraints:
    `username` or `password` violates `@NotBlank`.

- Branch 4:
  - Unsatisfied condition:
    `password` is not valid Base64.
  - Endpoint group:
    Step 1: `POST /api/user/login`
  - Failure endpoint:
    `POST /api/user/login`
  - Why this fails:
    The controller decodes the password directly before authentication.
  - Intentionally violated constraints:
    `password` is not decodable as Base64.

Endpoint coverage:
- Covers:
  `POST /api/user/login`
- Distinct meaning:
  Authenticates an existing user and returns a JWT access token.

### Behavior 3: create reservation

Behavior name:
create reservation

Successful execution:
- Result:
  This behavior creates a reservation and returns its `uuid`, `reservationFor`, `reservationFrom`, and `reservationTo`.
- Endpoint sequence:
  Step 1: `POST /api/user/register` to obtain `AuthResponse.accessToken`.
  Step 2: `POST /api/reservation` with `Authorization: Bearer {accessToken}`.
- Constraints:
  Step 2 requires nonblank `reservationFor`, non-null `reservationFrom`, non-null `reservationTo`, and non-null `createdAt`. The source expects date strings formatted as `yyyy-MM-dd HH:mm:ss`, while OpenAPI only says `date-time`. `reservationFrom` must not be after `reservationTo`, and `reservationFrom` must be in the future. The overlap check is global, not per user: the request must not have its start or end strictly inside an existing reservation interval.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No valid bearer token is supplied.
  - Endpoint group:
    Step 1: `POST /api/reservation`
  - Failure endpoint:
    `POST /api/reservation`
  - Why this fails:
    Reservation endpoints are protected by bearer JWT security.
  - Intentionally violated constraints:
    The `accessToken` from `POST /api/user/register` or `POST /api/user/login` is omitted or invalid.

- Branch 2:
  - Unsatisfied condition:
    `reservationFrom` is after `reservationTo`.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `POST /api/reservation` with `reservationFrom > reservationTo`.
  - Failure endpoint:
    `POST /api/reservation`
  - Why this fails:
    `validateDates` throws `ReservationException`, returned as `422 UNPROCESSABLE_ENTITY`.
  - Intentionally violated constraints:
    Start date is later than end date.

- Branch 3:
  - Unsatisfied condition:
    `reservationFrom` is in the past.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `POST /api/reservation` with `reservationFrom` before current server time.
  - Failure endpoint:
    `POST /api/reservation`
  - Why this fails:
    `validateDates` rejects past starts with `ReservationException`.
  - Intentionally violated constraints:
    Reservation start is not in the future.

- Branch 4:
  - Unsatisfied condition:
    The new reservation overlaps an existing reservation according to the implementation query.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `POST /api/reservation` with interval `[A, B]`.
    Step 3: `POST /api/reservation` with interval `[C, D]` where `A < C < B` or `A < D < B`.
  - Failure endpoint:
    `POST /api/reservation`
  - Why this fails:
    `findAllBetween` returns an existing reservation and the service throws `ReservationException`.
  - Intentionally violated constraints:
    The second reservation’s start or end is strictly inside the first reservation’s interval.

- Branch 5:
  - Unsatisfied condition:
    Required request fields are blank, missing, or incorrectly formatted.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `POST /api/reservation`
  - Failure endpoint:
    `POST /api/reservation`
  - Why this fails:
    Bean validation or JSON date parsing rejects the request.
  - Intentionally violated constraints:
    `reservationFor`, `reservationFrom`, `reservationTo`, or `createdAt` violates the source request model.

Endpoint coverage:
- Covers:
  `POST /api/reservation`
- Distinct meaning:
  Creates a new reservation.

### Behavior 4: list all reservations

Behavior name:
list all reservations

Successful execution:
- Result:
  This behavior retrieves all reservations, sorted by `reservationFrom` descending.
- Endpoint sequence:
  Step 1: `POST /api/user/register` to obtain `AuthResponse.accessToken`.
  Step 2: `GET /api/reservation` with `Authorization: Bearer {accessToken}`.
- Constraints:
  No reservation creation endpoint is required because this is a global collection read and can succeed with an empty list. The implementation sorts by `reservationFrom` descending. OpenAPI documents bearer security but not role details.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No valid bearer token is supplied.
  - Endpoint group:
    Step 1: `GET /api/reservation`
  - Failure endpoint:
    `GET /api/reservation`
  - Why this fails:
    The endpoint is protected by Spring Security.
  - Intentionally violated constraints:
    The bearer token from registration or login is omitted or invalid.

- Branch 2:
  - Unsatisfied condition:
    No reservations exist.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `GET /api/reservation`
  - Failure endpoint:
    `GET /api/reservation`
  - Why this fails:
    It does not fail; the implementation returns `200 OK` with an empty JSON array.
  - Intentionally violated constraints:
    No reservation setup endpoint is executed before the list call.

Endpoint coverage:
- Covers:
  `GET /api/reservation`
- Distinct meaning:
  Lists the global reservation collection.

### Behavior 5: list reservations for user

Behavior name:
list reservations for user

Successful execution:
- Result:
  This behavior retrieves reservations whose `reservationFor` value equals the path `{username}`.
- Endpoint sequence:
  Step 1: `POST /api/user/register` with `username = U` to obtain `AuthResponse.accessToken`.
  Step 2: `POST /api/reservation` with `Authorization: Bearer {accessToken}` and `reservationFor = U`.
  Step 3: `GET /api/reservation/{username}` with `{username} = U` and `Authorization: Bearer {accessToken}`.
- Constraints:
  Step 3’s `{username}` must equal Step 2’s `reservationFor` to return that reservation. The implementation does not verify that `{username}` is an actual registered user; it only filters reservations by the string field `reservationFor`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No valid bearer token is supplied.
  - Endpoint group:
    Step 1: `GET /api/reservation/{username}`
  - Failure endpoint:
    `GET /api/reservation/{username}`
  - Why this fails:
    The endpoint is protected by bearer JWT security.
  - Intentionally violated constraints:
    The bearer token is omitted or invalid.

- Branch 2:
  - Unsatisfied condition:
    `{username}` is blank or whitespace.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `GET /api/reservation/{username}` with a blank or whitespace `{username}`.
  - Failure endpoint:
    `GET /api/reservation/{username}`
  - Why this fails:
    The path variable has `@NotBlank`, and the exception handler returns `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    `{username}` is not a nonblank path value.

- Branch 3:
  - Unsatisfied condition:
    No reservation has `reservationFor` equal to `{username}`.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `GET /api/reservation/{username}` with a nonblank `{username}` that was not used as `reservationFor`.
  - Failure endpoint:
    `GET /api/reservation/{username}`
  - Why this fails:
    It does not fail; the implementation returns `200 OK` with an empty array.
  - Intentionally violated constraints:
    The reservation-creation prerequisite for that `{username}` is intentionally omitted.

Endpoint coverage:
- Covers:
  `GET /api/reservation/{username}`
- Distinct meaning:
  Lists reservations filtered by `reservationFor`.

### Behavior 6: update reservation

Behavior name:
update reservation

Successful execution:
- Result:
  This behavior updates an existing reservation’s `reservationFor`, `reservationFrom`, and `reservationTo`.
- Endpoint sequence:
  Step 1: `POST /api/user/register` to obtain `AuthResponse.accessToken`.
  Step 2: `POST /api/reservation` with `Authorization: Bearer {accessToken}`.
  Step 3: `PUT /api/reservation` with `Authorization: Bearer {accessToken}` and body `uuid` equal to the `uuid` returned by Step 2.
- Constraints:
  Step 3 must reuse Step 2’s response `uuid`. `uuid` and `reservationFor` must be nonblank; `reservationFrom` and `reservationTo` must be non-null and use the source date format `yyyy-MM-dd HH:mm:ss`. The same date rules as creation apply. Because validation runs before loading the target reservation and does not exclude the target reservation, the updated interval must avoid being caught by the global overlap query, or use the exact original boundaries.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No valid bearer token is supplied.
  - Endpoint group:
    Step 1: `PUT /api/reservation`
  - Failure endpoint:
    `PUT /api/reservation`
  - Why this fails:
    The endpoint is protected by bearer JWT security.
  - Intentionally violated constraints:
    The bearer token is omitted or invalid.

- Branch 2:
  - Unsatisfied condition:
    The `uuid` does not identify an existing reservation.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `POST /api/reservation` and capture returned `uuid = R1`.
    Step 3: `PUT /api/reservation` with `uuid = R2`, where `R2` is not `R1`.
  - Failure endpoint:
    `PUT /api/reservation`
  - Why this fails:
    `repository.findByUuid(R2)` returns no model, and the implementation has no not-found handling before mapping/saving. This is an unhandled implementation error rather than a documented `404`.
  - Intentionally violated constraints:
    Step 3 does not reuse the `uuid` produced by Step 2.

- Branch 3:
  - Unsatisfied condition:
    `reservationFrom` is after `reservationTo`.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `POST /api/reservation` and capture returned `uuid`.
    Step 3: `PUT /api/reservation` with that `uuid` and `reservationFrom > reservationTo`.
  - Failure endpoint:
    `PUT /api/reservation`
  - Why this fails:
    `validateDates` throws `ReservationException`, returned as `422 UNPROCESSABLE_ENTITY`.
  - Intentionally violated constraints:
    Updated start date is later than updated end date.

- Branch 4:
  - Unsatisfied condition:
    `reservationFrom` is in the past.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `POST /api/reservation` and capture returned `uuid`.
    Step 3: `PUT /api/reservation` with that `uuid` and past `reservationFrom`.
  - Failure endpoint:
    `PUT /api/reservation`
  - Why this fails:
    `validateDates` rejects past starts.
  - Intentionally violated constraints:
    Updated reservation start is not in the future.

- Branch 5:
  - Unsatisfied condition:
    The updated interval overlaps an existing reservation according to the implementation query.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `POST /api/reservation` with interval `[A, B]`.
    Step 3: `POST /api/reservation` with a non-overlapping interval `[C, D]` and capture its `uuid`.
    Step 4: `PUT /api/reservation` with Step 3’s `uuid` and interval `[E, F]` where `A < E < B` or `A < F < B`.
  - Failure endpoint:
    `PUT /api/reservation`
  - Why this fails:
    The global overlap query finds the reservation from Step 2 and the service returns `422 UNPROCESSABLE_ENTITY`.
  - Intentionally violated constraints:
    The updated start or end is strictly inside another reservation’s interval.

- Branch 6:
  - Unsatisfied condition:
    Required request fields are blank, missing, or incorrectly formatted.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `POST /api/reservation` and capture returned `uuid`.
    Step 3: `PUT /api/reservation`
  - Failure endpoint:
    `PUT /api/reservation`
  - Why this fails:
    Bean validation or JSON date parsing rejects the update body.
  - Intentionally violated constraints:
    `uuid`, `reservationFor`, `reservationFrom`, or `reservationTo` violates the request model.

Endpoint coverage:
- Covers:
  `PUT /api/reservation`
- Distinct meaning:
  Updates an existing reservation selected by request-body `uuid`.

### Behavior 7: delete reservation

Behavior name:
delete reservation

Successful execution:
- Result:
  This behavior deletes an existing reservation by UUID and returns an empty response body.
- Endpoint sequence:
  Step 1: `POST /api/user/register` to obtain `AuthResponse.accessToken`.
  Step 2: `POST /api/reservation` with `Authorization: Bearer {accessToken}`.
  Step 3: `DELETE /api/reservation/{reservationUuid}` with `{reservationUuid}` equal to the `uuid` returned by Step 2 and `Authorization: Bearer {accessToken}`.
- Constraints:
  Step 3 must reuse Step 2’s response `uuid` to delete the reservation that was created. `{reservationUuid}` must be nonblank. The implementation does not check ownership or `reservationFor`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No valid bearer token is supplied.
  - Endpoint group:
    Step 1: `DELETE /api/reservation/{reservationUuid}`
  - Failure endpoint:
    `DELETE /api/reservation/{reservationUuid}`
  - Why this fails:
    The endpoint is protected by bearer JWT security.
  - Intentionally violated constraints:
    The bearer token is omitted or invalid.

- Branch 2:
  - Unsatisfied condition:
    `{reservationUuid}` is blank or whitespace.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `DELETE /api/reservation/{reservationUuid}` with a blank or whitespace `{reservationUuid}`.
  - Failure endpoint:
    `DELETE /api/reservation/{reservationUuid}`
  - Why this fails:
    The path variable has `@NotBlank`, and the exception handler returns `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    `{reservationUuid}` is not a nonblank path value.

- Branch 3:
  - Unsatisfied condition:
    `{reservationUuid}` does not identify the reservation intended for deletion.
  - Endpoint group:
    Step 1: `POST /api/user/register` to obtain `accessToken`.
    Step 2: `POST /api/reservation` and capture returned `uuid = R1`.
    Step 3: `DELETE /api/reservation/{reservationUuid}` with `{reservationUuid} = R2`, where `R2` is not `R1`.
  - Failure endpoint:
    `DELETE /api/reservation/{reservationUuid}`
  - Why this fails:
    It does not fail at the API layer; the implementation calls `repository.deleteById` without first checking existence, so a wrong nonblank UUID can still produce an OK empty response while leaving the created reservation untouched.
  - Intentionally violated constraints:
    Step 3 does not reuse the `uuid` produced by Step 2.

Endpoint coverage:
- Covers:
  `DELETE /api/reservation/{reservationUuid}`
- Distinct meaning:
  Deletes a reservation selected by path UUID.

### Unclear or auxiliary endpoints

No OpenAPI endpoint is unclear or purely auxiliary. Every documented endpoint is mapped to at least one behavior above.