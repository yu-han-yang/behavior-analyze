# Domain-Level Behavior Analysis

## Domain Summary
This service implements a basic reservation system with two main domain areas: user accounts for authentication and reservations for scheduling. Users can register or log in to receive JWT access tokens. Authenticated users can create, list, update, and delete reservation records. A reservation has a generated identifier, a free-form `reservationFor` value, a start time, an end time, and an internal `createdAt` timestamp.

The implementation is simpler than a full booking system: reservations are not owned by the authenticated user, `reservationFor` is only a string filter, conflict checks are global, and there is no single-reservation retrieval endpoint.

## Available Function Inventory

### User and Authentication
- `register user and issue access token`  
  Core endpoint: `POST /api/user/register`  
  Domain meaning: creates a user account with unique username and email, stores a BCrypt-hashed decoded password, assigns role `USER`, and returns a JWT.

- `log in user and issue access token`  
  Core endpoint: `POST /api/user/login`  
  Domain meaning: authenticates an existing user by username and decoded password, then returns a JWT.

### Reservations
- `create reservation`  
  Core endpoint: `POST /api/reservation`  
  Domain meaning: creates a future reservation interval for a supplied `reservationFor` string if date validation and global overlap checks pass.

- `list all reservations`  
  Core endpoint: `GET /api/reservation`  
  Domain meaning: returns all reservation records sorted by `reservationFrom` descending.

- `list reservations for user value`  
  Core endpoint: `GET /api/reservation/{username}`  
  Domain meaning: returns reservations whose stored `reservationFor` exactly equals the path `username` value.

- `update reservation`  
  Core endpoint: `PUT /api/reservation`  
  Domain meaning: updates an existing reservation identified by request-body `uuid`.

- `delete reservation`  
  Core endpoint: `DELETE /api/reservation/{reservationUuid}`  
  Domain meaning: deletes a reservation by generated reservation id.

## Supported Business Behaviors

### Behavior 1: Register a New User and Start an Authenticated Session

Business goal:  
Create a new user account and immediately obtain an access token for protected reservation operations.

Domain context:  
Registration is the service’s account onboarding workflow. It also creates the first usable session token, so no separate login is required after successful registration.

Starting point:  
No prior service state for the selected username or email.

Required execution workflow:
1. Use function `register user and issue access token` (`POST /api/user/register`) with JSON body `username={username}`, `email={email}`, `password={base64Password}` to create the user and receive `AuthResponse.accessToken={accessToken}`.

Optional verification workflow:
1. Use function `list all reservations` (`GET /api/reservation`) with header `Authorization: Bearer {accessToken}` to verify the token can authenticate a protected reservation read.

Existing-state shortcuts:
- If an equivalent user already exists, this behavior cannot skip registration because the core goal is to create a new account.
- Direct database setup can insert a `users` document with `username`, `email`, BCrypt password hash, and role, but that is not equivalent to API registration because it does not issue a token.
- If a token is all that is needed and the user already exists, use `log in user and issue access token` instead.

Parameter and value bindings:
- `password={base64Password}` must decode to the cleartext password used for authentication.
- The same `username` and decoded password are saved, then used internally for authentication before the JWT is generated.
- The returned `accessToken` is reused as `Authorization: Bearer {accessToken}` on reservation functions.

Business result:
A user exists in the `users` collection with role `USER`, unique username and email, and a BCrypt-hashed decoded password. A JWT for that username is returned.

Constraints and invariants:
- `username`, `email`, and `password` must be nonblank.
- `email` must satisfy email validation.
- Username and email uniqueness are checked before insertion, but no database-level uniqueness constraint is visible.
- OpenAPI documents `password` as a plain string; implementation requires Base64 input.

Failure and exceptional cases:
- Failing function: `register user and issue access token`  
  Failure condition: `username` already exists.  
  Why it fails: the controller checks `existsByUsername` first and throws `DuplicateUserException`.  
  Violated prerequisite or constraint: username uniqueness.

- Failing function: `register user and issue access token`  
  Failure condition: `email` already exists while username is unused.  
  Why it fails: the controller checks `existsByEmail` after username and returns conflict.  
  Violated prerequisite or constraint: email uniqueness.

- Failing function: `register user and issue access token`  
  Failure condition: blank username, blank password, blank email, or invalid email.  
  Why it fails: bean validation rejects the request.  
  Violated prerequisite or constraint: required account fields and email format.

- Failing function: `register user and issue access token`  
  Failure condition: `password` is not valid Base64.  
  Why it fails: `Base64.getDecoder().decode(...)` raises an unhandled `IllegalArgumentException`.  
  Violated prerequisite or constraint: implementation-only Base64 password requirement.

Implementation notes:
The source decodes the password twice: once for hashing and once for immediate authentication. The OpenAPI contract does not describe this requirement.

### Behavior 2: Log In an Existing User

Business goal:  
Authenticate an existing user and obtain a JWT for reservation operations.

Domain context:  
Login is the recurring session creation workflow after an account already exists.

Starting point:  
No prior reservation state, but an account must be created through the API in the workflow.

Required execution workflow:
1. Use function `register user and issue access token` (`POST /api/user/register`) with body `username={username}`, `email={email}`, `password={base64Password}` to create the account if it does not already exist.
2. Use function `log in user and issue access token` (`POST /api/user/login`) with body `username={username}`, `password={base64Password}` to authenticate the same user and receive a new `AuthResponse.accessToken={loginAccessToken}`.

Optional verification workflow:
1. Use function `list all reservations` (`GET /api/reservation`) with header `Authorization: Bearer {loginAccessToken}` to verify the login token works.

Existing-state shortcuts:
- Step 1 can be skipped if a user with `username={username}` already exists with a BCrypt hash matching the decoded `{base64Password}` and a valid role.
- Direct database setup can insert such a user, but the password must be BCrypt-hashed and the role must map to a Spring authority such as `USER` or `ADMIN`.

Parameter and value bindings:
- The same `username` is used in registration and login.
- The login `password={base64Password}` must decode to the same cleartext password used during registration.
- The returned `loginAccessToken` is the token to reuse on protected reservation calls.

Business result:
No new domain resource is created by login itself. The result is a valid JWT for an existing user.

Constraints and invariants:
- `username` and `password` must be nonblank.
- Password must be Base64-encoded, although OpenAPI only says string.
- The token subject is the username, and the security filter later loads that username from the user store.

Failure and exceptional cases:
- Failing function: `register user and issue access token`  
  Failure condition: duplicate username or email during setup.  
  Why it fails: uniqueness checks reject the setup call.  
  Violated prerequisite or constraint: account identity must be unused for API setup.

- Failing function: `log in user and issue access token`  
  Failure condition: no user exists for `username`.  
  Why it fails: Spring Security cannot load the user.  
  Violated prerequisite or constraint: existing account.

- Failing function: `log in user and issue access token`  
  Failure condition: decoded password does not match stored hash.  
  Why it fails: authentication manager rejects the credentials.  
  Violated prerequisite or constraint: credential match.

- Failing function: `log in user and issue access token`  
  Failure condition: malformed Base64 password.  
  Why it fails: password decoding throws before authentication.  
  Violated prerequisite or constraint: implementation-only Base64 password requirement.

Implementation notes:
Login returns only `accessToken`; there is no refresh token, logout, password reset, user profile read, or token revocation behavior.

### Behavior 3: Create a Future Reservation

Business goal:  
Add a new reservation interval to the schedule.

Domain context:  
This is the core booking behavior. A user with a JWT submits a person or user label and a future time interval.

Starting point:  
No prior service state.

Required execution workflow:
1. Use function `register user and issue access token` (`POST /api/user/register`) with body `username={authUsername}`, `email={email}`, `password={base64Password}` to create an authenticated user and capture `accessToken`.
2. Use function `create reservation` (`POST /api/reservation`) with header `Authorization: Bearer {accessToken}` and body `reservationFor={reservationFor}`, `reservationFrom={from}`, `reservationTo={to}`, `createdAt={createdAt}` to create the reservation.

Optional verification workflow:
1. Use function `list reservations for user value` (`GET /api/reservation/{username}`) with path `username={reservationFor}` and header `Authorization: Bearer {accessToken}` to inspect reservations for that label.
2. Use function `list all reservations` (`GET /api/reservation`) with header `Authorization: Bearer {accessToken}` to inspect the global schedule.

Existing-state shortcuts:
- Step 1 can be skipped if a valid bearer token already exists for a stored user.
- Direct database setup can insert a valid user and signed JWT, or insert reservation records directly, but direct reservation insertion bypasses date and overlap validation.
- Existing schedule state can be empty or can contain reservations whose intervals do not conflict under the implementation’s overlap query.

Parameter and value bindings:
- `accessToken` from registration is reused in the `Authorization` header.
- `reservationFor` is stored as a free-form string and can later be reused as path `username` in `list reservations for user value`.
- `reservationFrom`, `reservationTo`, and `createdAt` must use `yyyy-MM-dd HH:mm:ss`, not the OpenAPI `date-time` shape.
- The generated response `uuid` is reused by update and delete behaviors.

Business result:
A reservation document exists with generated `uuid`, supplied `reservationFor`, supplied `reservationFrom`, supplied `reservationTo`, and supplied internal `createdAt`. The response exposes `uuid`, `reservationFor`, `reservationFrom`, and `reservationTo`.

Constraints and invariants:
- Reservation endpoints require a valid bearer JWT.
- `reservationFor` must be nonblank.
- `reservationFrom`, `reservationTo`, and `createdAt` are required.
- `reservationFrom` must not be after `reservationTo`.
- `reservationFrom` must be in the future relative to server time.
- Conflict checking is global across all reservations and is not scoped by user or resource.
- The conflict query only checks whether the new start or end is strictly inside an existing interval; exact boundary contact and some enclosing/equal interval cases are not fully covered.

Failure and exceptional cases:
- Failing function: `register user and issue access token`  
  Failure condition: duplicate account values or invalid account body.  
  Why it fails: setup cannot produce the required JWT.  
  Violated prerequisite or constraint: valid authenticated user setup.

- Failing function: `create reservation`  
  Failure condition: missing, malformed, expired, or invalid bearer token.  
  Why it fails: Spring Security does not establish authentication.  
  Violated prerequisite or constraint: authenticated reservation access.

- Failing function: `create reservation`  
  Failure condition: `reservationFrom` is after `reservationTo`.  
  Why it fails: `validateDates` throws `ReservationException`.  
  Violated prerequisite or constraint: valid interval order.

- Failing function: `create reservation`  
  Failure condition: `reservationFrom` is in the past.  
  Why it fails: service compares server `LocalDateTime.now()` to the requested start.  
  Violated prerequisite or constraint: future reservation start.

- Failing function: `create reservation`  
  Failure condition: requested start or end falls strictly inside an existing reservation interval.  
  Why it fails: `findAllBetween(from, to)` returns at least one reservation and the service throws.  
  Violated prerequisite or constraint: global interval availability.

- Failing function: `create reservation`  
  Failure condition: missing required fields, blank `reservationFor`, or date strings not matching `yyyy-MM-dd HH:mm:ss`.  
  Why it fails: bean validation or Jackson parsing rejects the request.  
  Violated prerequisite or constraint: request schema as implemented.

Implementation notes:
OpenAPI says the date fields are `date-time`, but implementation uses `yyyy-MM-dd HH:mm:ss`. `createdAt` is client-provided on create but not included in the response.

### Behavior 4: Inspect the Global Reservation Schedule

Business goal:  
View all reservations in the system.

Domain context:  
This behavior supports schedule overview and administrative-style inspection, although the implementation effectively permits ordinary authenticated users.

Starting point:  
No prior service state.

Required execution workflow:
1. Use function `register user and issue access token` (`POST /api/user/register`) with body `username={authUsername}`, `email={email}`, `password={base64Password}` to capture `accessToken`.
2. Use function `list all reservations` (`GET /api/reservation`) with header `Authorization: Bearer {accessToken}` to retrieve all reservations.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped if a valid bearer token already exists.
- Direct database setup can create reservation documents before the read if a non-empty list is needed.
- If no reservations exist, the list still succeeds and returns an empty array.

Parameter and value bindings:
- `accessToken` from registration or login is reused as `Authorization: Bearer {accessToken}`.
- No reservation id or `reservationFor` value is needed.

Business result:
No state changes occur. The caller receives all reservation records sorted by `reservationFrom` descending.

Constraints and invariants:
- Authentication is required.
- Source security configuration lists both a broad `GET /api/reservation/**` rule for `ADMIN` or `USER` and a later exact `GET /api/reservation` rule for `ADMIN`; the broad rule is evaluated first, so `USER` tokens can access the global list.
- Response dates use implementation formatting, not OpenAPI `date-time`.

Failure and exceptional cases:
- Failing function: `register user and issue access token`  
  Failure condition: setup account cannot be created.  
  Why it fails: duplicate or invalid registration request.  
  Violated prerequisite or constraint: authenticated access setup.

- Failing function: `list all reservations`  
  Failure condition: no valid bearer token.  
  Why it fails: security filter does not authenticate the request.  
  Violated prerequisite or constraint: protected endpoint authentication.

Implementation notes:
This is a global read. It does not enforce ownership, tenancy, or reservationFor filtering.

### Behavior 5: Inspect Reservations for a Reservation-For Value

Business goal:  
View reservations associated with a specific `reservationFor` label.

Domain context:  
The endpoint name suggests a user-specific reservation list, but the implementation filters by the reservation document’s `reservationFor` string and does not verify that the value is a registered username or the authenticated subject.

Starting point:  
No prior service state.

Required execution workflow:
1. Use function `register user and issue access token` (`POST /api/user/register`) with body `username={authUsername}`, `email={email}`, `password={base64Password}` to capture `accessToken`.
2. Use function `create reservation` (`POST /api/reservation`) with header `Authorization: Bearer {accessToken}` and body `reservationFor={reservationFor}`, `reservationFrom={from}`, `reservationTo={to}`, `createdAt={createdAt}` to create a matching reservation.
3. Use function `list reservations for user value` (`GET /api/reservation/{username}`) with path `username={reservationFor}` and header `Authorization: Bearer {accessToken}` to retrieve reservations with that exact stored value.

Optional verification workflow:
1. Use function `list all reservations` (`GET /api/reservation`) with header `Authorization: Bearer {accessToken}` to compare the filtered list against the global list.

Existing-state shortcuts:
- Step 1 can be skipped if a valid bearer token already exists.
- Step 2 can be skipped if a reservation already exists with `reservationFor={reservationFor}`.
- Direct database setup can insert matching reservation documents, but those rows may bypass API date and overlap rules.

Parameter and value bindings:
- The `reservationFor` body value from `create reservation` must be reused exactly as path `username` in `list reservations for user value`.
- `authUsername` does not need to match `reservationFor`; the implementation does not bind reservation ownership to the JWT subject.
- `accessToken` is reused across both reservation calls.

Business result:
No state changes occur during the list step. The response contains reservation records whose `reservationFor` exactly equals the path value.

Constraints and invariants:
- Path `username` must be nonblank.
- Authentication is required.
- Empty matches return `200 OK` with an empty array.
- No registered user relationship is required for `reservationFor`.

Failure and exceptional cases:
- Failing function: `register user and issue access token`  
  Failure condition: invalid or duplicate registration setup.  
  Why it fails: no token is produced.  
  Violated prerequisite or constraint: authenticated access setup.

- Failing function: `create reservation`  
  Failure condition: invalid interval, past start, overlapping start or end, bad dates, blank `reservationFor`, or missing token.  
  Why it fails: validation, date parsing, overlap check, or security rejects creation.  
  Violated prerequisite or constraint: matching reservation setup.

- Failing function: `list reservations for user value`  
  Failure condition: missing or invalid bearer token.  
  Why it fails: reservation endpoints are protected.  
  Violated prerequisite or constraint: authenticated access.

- Failing function: `list reservations for user value`  
  Failure condition: blank or whitespace path value.  
  Why it fails: `@NotBlank` path validation throws `ConstraintViolationException`.  
  Violated prerequisite or constraint: nonblank filter value.

Implementation notes:
The path variable is named `username`, but business behavior is actually filtering by `reservationFor`.

### Behavior 6: Update an Existing Reservation

Business goal:  
Change a reservation’s assignee label and scheduled interval.

Domain context:  
Updating supports schedule correction or reassignment after a reservation was created.

Starting point:  
No prior service state.

Required execution workflow:
1. Use function `register user and issue access token` (`POST /api/user/register`) with body `username={authUsername}`, `email={email}`, `password={base64Password}` to capture `accessToken`.
2. Use function `create reservation` (`POST /api/reservation`) with header `Authorization: Bearer {accessToken}` and body `reservationFor={oldReservationFor}`, `reservationFrom={oldFrom}`, `reservationTo={oldTo}`, `createdAt={createdAt}` to create the target reservation and capture response `uuid={reservationUuid}`.
3. Use function `update reservation` (`PUT /api/reservation`) with header `Authorization: Bearer {accessToken}` and body `uuid={reservationUuid}`, `reservationFor={newReservationFor}`, `reservationFrom={newFrom}`, `reservationTo={newTo}` to update the target reservation.

Optional verification workflow:
1. Use function `list reservations for user value` (`GET /api/reservation/{username}`) with path `username={newReservationFor}` and header `Authorization: Bearer {accessToken}` to inspect the new label’s reservations.
2. Use function `list all reservations` (`GET /api/reservation`) with header `Authorization: Bearer {accessToken}` to inspect the global updated record.

Existing-state shortcuts:
- Step 1 can be skipped if a valid bearer token already exists.
- Step 2 can be skipped if a reservation already exists and its generated `uuid` is known.
- Direct database setup can insert the reservation, but the `uuid` consumed by update must be the Mongo id mapped to the model `uuid`.

Parameter and value bindings:
- `accessToken` is reused for create and update.
- The generated `uuid` returned by `create reservation` must be reused as request-body `uuid` in `update reservation`.
- `newReservationFor` becomes the value used by later filtered reads.
- `createdAt` is not present in the update body and is ignored by the mapper, so the existing created timestamp is preserved.

Business result:
The existing reservation’s `reservationFor`, `reservationFrom`, and `reservationTo` are changed. The target id remains the same when the correct generated `uuid` is reused. `createdAt` remains from the original record.

Constraints and invariants:
- Authentication is required.
- Request body `uuid` and `reservationFor` must be nonblank.
- `reservationFrom` and `reservationTo` must be non-null and use `yyyy-MM-dd HH:mm:ss`.
- Updated start must not be in the past and must not be after end.
- Overlap validation is global and is run before loading the target reservation.
- Because the target reservation is not excluded from conflict detection, some updates inside the reservation’s own original interval may be rejected.

Failure and exceptional cases:
- Failing function: `register user and issue access token`  
  Failure condition: cannot create setup user.  
  Why it fails: duplicate or invalid registration.  
  Violated prerequisite or constraint: authenticated access setup.

- Failing function: `create reservation`  
  Failure condition: cannot create target reservation.  
  Why it fails: security, validation, date parsing, or overlap failure.  
  Violated prerequisite or constraint: existing reservation target.

- Failing function: `update reservation`  
  Failure condition: missing or invalid token.  
  Why it fails: Spring Security rejects protected endpoint access.  
  Violated prerequisite or constraint: authenticated access.

- Failing function: `update reservation`  
  Failure condition: `uuid={missingUuid}` does not identify an existing reservation.  
  Why it fails: `repository.findByUuid` returns no model and mapper/save logic has no explicit not-found handling.  
  Violated prerequisite or constraint: existing target id.

- Failing function: `update reservation`  
  Failure condition: invalid date order, past start, overlapping start/end, blank fields, missing fields, or bad date format.  
  Why it fails: validation, date parsing, or `validateDates` rejects the request.  
  Violated prerequisite or constraint: valid updated reservation representation.

Implementation notes:
There is no `404 Not Found` handling for missing `uuid`. The update is selected by request-body id, not a path id.

### Behavior 7: Delete an Existing Reservation

Business goal:  
Remove a reservation from the schedule.

Domain context:  
Deletion is the cancellation/removal capability for reservation records.

Starting point:  
No prior service state.

Required execution workflow:
1. Use function `register user and issue access token` (`POST /api/user/register`) with body `username={authUsername}`, `email={email}`, `password={base64Password}` to capture `accessToken`.
2. Use function `create reservation` (`POST /api/reservation`) with header `Authorization: Bearer {accessToken}` and body `reservationFor={reservationFor}`, `reservationFrom={from}`, `reservationTo={to}`, `createdAt={createdAt}` to create the target reservation and capture `uuid={reservationUuid}`.
3. Use function `delete reservation` (`DELETE /api/reservation/{reservationUuid}`) with path `reservationUuid={reservationUuid}` and header `Authorization: Bearer {accessToken}` to delete the target reservation.

Optional verification workflow:
1. Use function `list all reservations` (`GET /api/reservation`) with header `Authorization: Bearer {accessToken}` to verify the deleted id no longer appears.
2. Use function `list reservations for user value` (`GET /api/reservation/{username}`) with path `username={reservationFor}` and header `Authorization: Bearer {accessToken}` to verify it no longer appears under the old label.

Existing-state shortcuts:
- Step 1 can be skipped if a valid bearer token already exists.
- Step 2 can be skipped if an existing reservation’s generated `uuid` is already known.
- Direct database setup can insert the target reservation, but the path id must match the Mongo id used by `deleteById`.

Parameter and value bindings:
- The response `uuid` from `create reservation` must be reused as path `reservationUuid`.
- The same `accessToken` is reused for creation, deletion, and verification.
- `reservationFor` is only needed for filtered verification.

Business result:
The reservation identified by `reservationUuid` no longer exists. No reservation ownership or subject relationship is checked.

Constraints and invariants:
- Authentication is required.
- `reservationUuid` path value must be nonblank.
- The implementation deletes by id and does not verify existence before returning success.
- Deleting a non-existent nonblank id can return success while changing no state.

Failure and exceptional cases:
- Failing function: `register user and issue access token`  
  Failure condition: setup account creation fails.  
  Why it fails: registration validation or uniqueness conflict.  
  Violated prerequisite or constraint: authenticated access setup.

- Failing function: `create reservation`  
  Failure condition: target reservation cannot be created.  
  Why it fails: protected access, validation, date parsing, or overlap rules.  
  Violated prerequisite or constraint: existing deletion target.

- Failing function: `delete reservation`  
  Failure condition: missing or invalid token.  
  Why it fails: security rejects access.  
  Violated prerequisite or constraint: authenticated access.

- Failing function: `delete reservation`  
  Failure condition: blank path value.  
  Why it fails: `@NotBlank` path validation returns bad request.  
  Violated prerequisite or constraint: nonblank target id.

- Failing function: `delete reservation`  
  Failure condition: wrong but nonblank `reservationUuid`.  
  Why it fails: this does not fail at the API layer; `deleteById` is called and the intended reservation remains.  
  Violated prerequisite or constraint: correct generated id binding.

Implementation notes:
The API has delete but no restore, soft-cancel status, cancellation reason, or ownership check.

### Behavior 8: Complete Reservation Lifecycle

Business goal:  
Create, revise, inspect, and remove a reservation using only API functions.

Domain context:  
This composite workflow represents the practical lifecycle of a reservation record in the service.

Starting point:  
No prior service state.

Required execution workflow:
1. Use function `register user and issue access token` (`POST /api/user/register`) with body `username={authUsername}`, `email={email}`, `password={base64Password}` to capture `accessToken`.
2. Use function `create reservation` (`POST /api/reservation`) with header `Authorization: Bearer {accessToken}` and body `reservationFor={reservationFor}`, `reservationFrom={from1}`, `reservationTo={to1}`, `createdAt={createdAt}` to create the reservation and capture `uuid={reservationUuid}`.
3. Use function `update reservation` (`PUT /api/reservation`) with header `Authorization: Bearer {accessToken}` and body `uuid={reservationUuid}`, `reservationFor={updatedReservationFor}`, `reservationFrom={from2}`, `reservationTo={to2}` to revise the reservation.
4. Use function `delete reservation` (`DELETE /api/reservation/{reservationUuid}`) with path `reservationUuid={reservationUuid}` and header `Authorization: Bearer {accessToken}` to remove the revised reservation.

Optional verification workflow:
1. Use function `list reservations for user value` (`GET /api/reservation/{username}`) with path `username={updatedReservationFor}` and header `Authorization: Bearer {accessToken}` after the update to inspect the revised record.
2. Use function `list all reservations` (`GET /api/reservation`) with header `Authorization: Bearer {accessToken}` after deletion to verify the id no longer appears.

Existing-state shortcuts:
- Step 1 can be skipped if a valid token exists.
- Step 2 can be skipped only if an existing reservation id is known and the lifecycle is intentionally starting from an existing record.
- Direct database setup can establish user and reservation state, but generated id and token bindings must still be valid.

Parameter and value bindings:
- `accessToken` is reused across all protected calls.
- `uuid` from create is reused in both update body and delete path.
- `updatedReservationFor` replaces the original `reservationFor`; filtered reads after the update must use the new value.
- `createdAt` is supplied only on create and preserved through update.

Business result:
A reservation is created, modified, and then removed. After the final step, the reservation should no longer exist.

Constraints and invariants:
- All reservation date and overlap constraints apply to both create and update.
- Update can fail because the conflict query includes existing reservations and does not exclude the target reservation.
- Delete success does not prove the intended reservation existed or was removed unless the correct generated id was used.

Failure and exceptional cases:
- Failing function: `register user and issue access token`  
  Failure condition: account setup fails.  
  Why it fails: duplicate or invalid registration data.  
  Violated prerequisite or constraint: valid token setup.

- Failing function: `create reservation`  
  Failure condition: invalid reservation body, conflict, past start, bad token.  
  Why it fails: validation, security, or overlap logic.  
  Violated prerequisite or constraint: valid initial reservation.

- Failing function: `update reservation`  
  Failure condition: wrong `uuid`, invalid interval, conflict, past start, or bad token.  
  Why it fails: target lookup, validation, security, or overlap logic.  
  Violated prerequisite or constraint: valid update target and interval.

- Failing function: `delete reservation`  
  Failure condition: wrong id.  
  Why it fails: this may not fail; it can return success while leaving the intended reservation untouched.  
  Violated prerequisite or constraint: correct generated id reuse.

Implementation notes:
The lifecycle is API-realizable, but not strongly safe: there is no versioning, no ownership, no not-found response for delete, and no direct retrieve-by-id.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: User-Owned Reservation Access Control

Priority:  
Critical domain gap

Expected business goal:  
Users should only create, view, update, or delete reservations they own, or admins should have explicitly broader access.

Why it is unsupported:  
No function binds reservation ownership to the JWT subject. `reservationFor` is a request string, not a foreign key to the authenticated user.

Existing functions considered:
- `create reservation`: accepts arbitrary `reservationFor`; does not use the authenticated username.
- `list reservations for user value`: filters by path string; does not require it to match the JWT subject.
- `list all reservations`: exposes all reservations to authenticated users in practice.
- `update reservation`: updates by body `uuid`; no owner check.
- `delete reservation`: deletes by path id; no owner check.

Missing capability:  
Ownership field, owner assignment from JWT subject, owner-scoped queries, and authorization checks on update/delete/global list.

Proof that function composition is insufficient:  
Chaining login, create, list, update, and delete cannot create an enforced owner relationship because no function writes or checks such a relationship. Passing matching `reservationFor` values is only convention and can be bypassed by any caller.

Evidence from existing functions/source:
- `create reservation` maps request fields directly.
- `list reservations for user value` calls `findAllByReservationFor`.
- `update reservation` and `delete reservation` identify records only by generated id.
- Security rules authenticate roles but do not check resource ownership.

Business impact:  
Any authenticated user can potentially inspect, modify, or delete another user’s reservation if they know or can discover the id.

### Missing Behavior 2: Retrieve a Reservation by Identifier

Priority:  
Critical domain gap

Expected business goal:  
Fetch one reservation by generated `uuid` to view details before update, deletion, or display.

Why it is unsupported:  
There is no `GET /api/reservation/{reservationUuid}` retrieval behavior. The existing parameterized GET path is already used as a `reservationFor` filter.

Existing functions considered:
- `list all reservations`: can return all records but requires client-side scanning.
- `list reservations for user value`: filters by `reservationFor`, not by id.
- `update reservation`: consumes `uuid` but is mutating, not retrieval.
- `delete reservation`: consumes `uuid` but removes the record.

Missing capability:  
A non-mutating retrieve endpoint such as `GET /api/reservation/id/{reservationUuid}` or a different routing model.

Proof that function composition is insufficient:  
Listing all or by `reservationFor` cannot reliably retrieve one id if the caller does not know its label or if many records share the label. Mutating functions cannot be used as safe reads.

Evidence from existing functions/source:
- `ReservationRestController` has `GET /api/reservation/{username}` and `DELETE /api/reservation/{reservationUuid}` sharing the same path shape by method.
- `ReservationServiceImpl` exposes no read-by-id method.

Business impact:  
Clients cannot safely inspect one reservation, confirm exact current state, or distinguish missing id from inaccessible state without broad listing.

### Missing Behavior 3: Reliable Reservation Conflict Prevention

Priority:  
Critical domain gap

Expected business goal:  
Prevent all overlapping reservation intervals for the same reservable resource or schedule scope.

Why it is unsupported:  
The overlap check is global and incomplete. It checks whether the new start or end is strictly inside an existing interval, but not every overlap relation.

Existing functions considered:
- `create reservation`: performs partial global overlap validation.
- `update reservation`: performs the same partial global overlap validation.
- `list all reservations`: can inspect conflicts after the fact but cannot enforce prevention.
- `delete reservation`: can manually remove conflicts but cannot prevent them.

Missing capability:  
Complete interval-overlap predicate, resource/scope-aware availability, and database-level or transactional conflict enforcement.

Proof that function composition is insufficient:  
Clients can avoid some conflicts by listing first, but between list and create/update another write can occur. Composition cannot add atomic server-side validation or fix incomplete overlap logic.

Evidence from existing functions/source:
- `findAllBetween(from, to)` only checks existing interval contains the new start or new end.
- Conflict validation is not scoped by `reservationFor` or any reservable asset.
- No unique or exclusion constraint is visible.

Business impact:  
The schedule can reject legitimate separate-user reservations globally while still allowing some overlapping or duplicate interval cases.

### Missing Behavior 4: Safe Update with Not-Found and Self-Conflict Handling

Priority:  
Important robustness gap

Expected business goal:  
Update an existing reservation reliably, returning `404` for missing ids and allowing legitimate changes that do not conflict with other reservations.

Why it is unsupported:  
`update reservation` validates conflicts before loading the target and does not exclude the target reservation. Missing ids have no explicit not-found handling.

Existing functions considered:
- `update reservation`: mutates but lacks safe target handling.
- `create reservation`: can create a replacement, but cannot preserve identity.
- `delete reservation`: can remove old record, but delete-and-recreate is not equivalent.

Missing capability:  
Target lookup before validation, `404 Not Found`, exclusion of the target id during overlap checks, and possibly optimistic locking.

Proof that function composition is insufficient:  
Delete-and-recreate changes the generated id and loses original `createdAt`. Listing cannot make update atomic or exclude the target from server-side conflict checks.

Evidence from existing functions/source:
- `validateDates` runs before `repository.findByUuid`.
- `findAllBetween` has no target-id exclusion.
- Mapper ignores `createdAt` on update, so preserving the original record matters.

Business impact:  
Clients may be unable to make valid corrections and cannot reliably distinguish missing target errors from server faults.

### Missing Behavior 5: Validated Link Between Reservation and Registered User

Priority:  
Important robustness gap

Expected business goal:  
Create reservations for real registered users or clearly support guest names with explicit semantics.

Why it is unsupported:  
`reservationFor` is a free-form nonblank string. The service does not verify it against the user collection.

Existing functions considered:
- `register user and issue access token`: creates a user but does not link to reservations.
- `create reservation`: accepts any nonblank `reservationFor`.
- `list reservations for user value`: filters by the same free-form value.

Missing capability:  
Foreign-key-like user reference, user existence validation, or explicit guest reservation model.

Proof that function composition is insufficient:  
A client can voluntarily set `reservationFor` to a registered username, but another client can set any other value. No function prevents invalid labels or creates a durable user-reservation relation.

Evidence from existing functions/source:
- Reservation model stores only `String reservationFor`.
- Repository filters by `reservationFor`.
- No service calls `UserService` during reservation creation or update.

Business impact:  
Reservation lists by “user” are ambiguous, typo-prone, and not trustworthy for user account workflows.

### Missing Behavior 6: Search or Availability Query Before Booking

Priority:  
Important robustness gap

Expected business goal:  
Check whether a time range is available before attempting to create or update a reservation.

Why it is unsupported:  
No read-only availability or conflict query exists.

Existing functions considered:
- `list all reservations`: exposes all records but requires broad access and client-side logic.
- `list reservations for user value`: filters by label, not availability.
- `create reservation`: detects some conflicts but mutates on success.
- `update reservation`: detects some conflicts but mutates on success.

Missing capability:  
Read-only endpoint such as `GET /api/reservation/availability?from=...&to=...` with the same server-side rules used by create/update.

Proof that function composition is insufficient:  
Using create as a probe creates a reservation on success. Listing then checking client-side is stale immediately and cannot match server validation atomically.

Evidence from existing functions/source:
- Only repository conflict query is private to service validation.
- No controller method exposes availability.

Business impact:  
Clients cannot provide reliable pre-booking feedback and may force users into trial-and-error reservation attempts.

### Missing Behavior 7: User Account Lifecycle Management

Priority:  
API ergonomics gap

Expected business goal:  
Retrieve, update, disable, or delete user accounts; change email or password; inspect profile.

Why it is unsupported:  
The public user API only supports registration and login.

Existing functions considered:
- `register user and issue access token`: creates a user.
- `log in user and issue access token`: authenticates a user.
- Reservation functions: depend on tokens but do not manage user accounts.

Missing capability:  
User profile read/update/delete endpoints, password change/reset, email change, and account deactivation.

Proof that function composition is insufficient:  
Login and registration cannot change or remove existing account state. Direct database edits would bypass the API and are not business behavior.

Evidence from existing functions/source:
- `UserRestController` exposes only `/login` and `/register`.
- `UserService` has internal methods, but no public controller endpoints for lifecycle operations.

Business impact:  
Users cannot maintain account data or recover/change credentials through the API.

### Missing Behavior 8: Token Refresh, Revocation, and Logout

Priority:  
API ergonomics gap

Expected business goal:  
Refresh an expiring token, revoke compromised tokens, or log out.

Why it is unsupported:  
JWTs are issued but not persisted or revoked by any endpoint.

Existing functions considered:
- `register user and issue access token`: issues initial token.
- `log in user and issue access token`: issues another token.

Missing capability:  
Refresh token, logout endpoint, token denylist, revocation state, or session store.

Proof that function composition is insufficient:  
Calling login creates a new token but does not invalidate old tokens. No function records or changes token validity after issuance.

Evidence from existing functions/source:
- `TokenProvider` signs JWTs with expiration and claims only.
- `TokenAuthenticationFilter` validates signature and loads the subject; no revocation check exists.

Business impact:  
Session management is limited and compromised tokens remain usable until expiration.

## Cross-Behavior Observations

- Authentication exists, but authorization is coarse role checking; reservation ownership is not enforced.
- Password fields are implementation-required Base64 strings, despite OpenAPI documenting plain strings.
- Date fields are documented as OpenAPI `date-time`, but implementation requires and returns `yyyy-MM-dd HH:mm:ss`.
- `reservationFor` is not a user foreign key. It is a free-form label.
- Global reservation listing is effectively available to `USER` tokens because the broad security matcher appears before the admin-only matcher.
- Conflict detection is both overbroad in scope and incomplete in interval logic.
- Update lacks explicit not-found handling and can self-conflict.
- Delete by wrong nonblank id can return success with no state change.
- `createdAt` is client-supplied on create, preserved on update, and not returned in `ReservationResponse`.
- There is no direct read-by-id endpoint, no pagination, no search, no audit trail, no optimistic locking, and no transactional availability guarantee.

## Coverage Summary

Supported domain areas:
- User registration with immediate JWT issuance.
- User login with JWT issuance.
- Authenticated reservation creation.
- Authenticated global reservation listing.
- Authenticated listing by `reservationFor` value.
- Authenticated reservation update by generated id.
- Authenticated reservation deletion by generated id.
- Basic create-update-delete reservation lifecycle.

Partially supported domain areas:
- Schedule conflict prevention, because the validation is partial and globally scoped.
- User-specific reservation views, because filtering uses a free-form string rather than authenticated ownership.
- Administrative schedule visibility, because role rules are ambiguous in implementation order.

Unsupported domain areas:
- Enforced reservation ownership.
- Reservation retrieval by id.
- Reliable availability search.
- Complete overlap prevention.
- Safe update semantics with not-found and self-conflict handling.
- User profile and account lifecycle management.
- Token refresh, logout, and revocation.
- Auditing, pagination, search, and concurrency-safe booking.