### Function 1: return welcome message

Function name:
return welcome message

Core endpoint(s):
- `GET /`

Preconditions:
- No prerequisite resource state is required.

Successful execution:
- Result:
  This function returns the API root welcome JSON message.
- Invocation:
  Step 1: `GET /` with required path/query/body/form/header values
- Constraints:
  No prerequisite resource state is required.

Failure or exceptional branches:
- No implementation-specific failure branch is visible in `src`.

Endpoint coverage:
- Covers:
  `GET /`
- Distinct meaning:
  Root discovery/welcome endpoint.

### Function 2: answer CORS preflight

Function name:
answer CORS preflight

Core endpoint(s):
- `OPTIONS /*`

Preconditions:
- No prerequisite resource state is required.

Successful execution:
- Result:
  This function returns `204 No Content` for wildcard OPTIONS requests.
- Invocation:
  Step 1: `OPTIONS /*` with required path/query/body/form/header values
- Constraints:
  No prerequisite resource state is required.

Failure or exceptional branches:
- No implementation-specific failure branch is visible in `src`.

Endpoint coverage:
- Covers:
  `OPTIONS /*`
- Distinct meaning:
  Browser preflight support.

### Function 3: verify authenticated user access

Function name:
verify authenticated user access

Core endpoint(s):
- `GET /api/secured`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function returns a protected message to an authenticated consumer user.
- Invocation:
  Step 1: `GET /api/secured` with required path/query/body/form/header values
- Constraints:
  `POST /consumer/register` must create a user with `ROLE_USER`; `GET /api/secured` must authenticate as that same username and password.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: The request is not authenticated as a `ROLE_USER`. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /api/secured`
  - Why this fails:
    The endpoint is secured with `ROLE_USER`.
  - Intentionally violated constraints:
    The consumer registration/authentication prerequisite is omitted.

Endpoint coverage:
- Covers:
  `GET /api/secured`
- Distinct meaning:
  Confirms access to a secured user-only route.

### Function 4: log in user

Function name:
log in user

Core endpoint(s):
- `POST /login`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function validates submitted credentials and returns `success: true` plus the concrete user object.
- Invocation:
  Step 1: `POST /login` with form/query parameters `username` and `password`
- Constraints:
  `POST /login` form/query parameters `username` and `password` must match the consumer created by `POST /consumer/register`. The implementation may also include `externalURL` for consumers outside the `heroku` profile.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Required login fields are absent. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    The implementation returns `success: false` when `username` or `password` is missing.
  - Intentionally violated constraints:
    `username` and/or `password` are omitted.
- Branch 2:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - The failing request is made against state that violates this condition: Password does not match. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    The user exists, but `user.getPassword().equals(password)` is false.
  - Intentionally violated constraints:
    `POST /login` reuses the username from `POST /consumer/register` but sends a different password.
- Branch 3:
  - Preconditions:
    - The failing request is made against state that violates this condition: User does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    `createUser(username)` returns `null`.
  - Intentionally violated constraints:
    The account creation endpoint is intentionally omitted.

Endpoint coverage:
- Covers:
  `POST /login`
- Distinct meaning:
  Credential validation; it does not issue a token in this implementation.

### Function 5: register admin

Function name:
register admin

Core endpoint(s):
- `POST /admin/register`

Preconditions:
- No prerequisite resource state is required.

Successful execution:
- Result:
  This function creates an admin account.
- Invocation:
  Step 1: `POST /admin/register` with the required registration body or request parameters
- Constraints:
  The request body is an `Admin` object. The implementation saves it directly; a usable admin needs `username`, `password`, and `email`, although Swagger omits `password` from the visible schema.

Failure or exceptional branches:
- No explicit duplicate or validation branch is implemented; persistence errors may occur outside controller logic.

Endpoint coverage:
- Covers:
  `POST /admin/register`
- Distinct meaning:
  Creates an admin user with `ROLE_ADMIN`.

### Function 6: seed demo dataset

Function name:
seed demo dataset

Core endpoint(s):
- `POST /admin/seed`

Preconditions:
- No prerequisite resource state is required.

Successful execution:
- Result:
  This function fills the database with hardcoded demo admins, consumers, print shops, managers, registration requests, price tables, print requests, and reviews.
- Invocation:
  Step 1: `POST /admin/seed` with required path/query/body/form/header values
- Constraints:
  No documented input is required. This is a developer/demo data endpoint and creates broad state, not a minimal business object.

Failure or exceptional branches:
- No implementation-specific failure branch is visible in `src`.

Endpoint coverage:
- Covers:
  `POST /admin/seed`
- Distinct meaning:
  Bulk demo data creation.

### Function 7: seed many consumers

Function name:
seed many consumers

Core endpoint(s):
- `POST /admin/useed`

Preconditions:
- No prerequisite resource state is required.

Successful execution:
- Result:
  This function creates 1000 hardcoded consumer accounts with randomized coordinates and a preset balance.
- Invocation:
  Step 1: `POST /admin/useed` with required path/query/body/form/header values
- Constraints:
  No documented input is required.

Failure or exceptional branches:
- No implementation-specific failure branch is visible in `src`.

Endpoint coverage:
- Covers:
  `POST /admin/useed`
- Distinct meaning:
  Bulk consumer-account seeding.

### Function 8: list print shops as admin

Function name:
list print shops as admin

Core endpoint(s):
- `GET /admin/printshops`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.

Successful execution:
- Result:
  This function returns all registered print shops to an authenticated admin.
- Invocation:
  Step 1: `GET /admin/printshops` with required path/query/body/form/header values
- Constraints:
  `GET /admin/printshops` must authenticate as the admin created by `POST /admin/register`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Caller is not authenticated as an admin. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /admin/printshops`
  - Why this fails:
    The endpoint is secured with `ROLE_ADMIN`.
  - Intentionally violated constraints:
    The admin registration/authentication prerequisite is omitted.

Endpoint coverage:
- Covers:
  `GET /admin/printshops`
- Distinct meaning:
  Admin-scoped listing of all print-shop records.

### Function 9: register consumer

Function name:
register consumer

Core endpoint(s):
- `POST /consumer/register`

Preconditions:
- No prerequisite resource state is required.

Successful execution:
- Result:
  This function creates a consumer account and returns the created consumer with `success: true`.
- Invocation:
  Step 1: `POST /consumer/register` with the required registration body or request parameters
- Constraints:
  The implementation reads request parameters `username`, `password`, `email`, `name`, `latitude`, and `longitude`. `username` must not already exist.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - The failing request is made against state that violates this condition: Username already exists. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /consumer/register`
  - Why this fails:
    `POST /consumer/register` finds an existing consumer by the same `username` and returns `success: false`.
  - Intentionally violated constraints:
    `POST /consumer/register` repeats the `username` created by `POST /consumer/register`.

Endpoint coverage:
- Covers:
  `POST /consumer/register`
- Distinct meaning:
  Consumer self-registration.

### Function 10: retrieve consumer profile

Function name:
retrieve consumer profile

Core endpoint(s):
- `GET /consumer/info`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function returns the authenticated consumer’s stored profile.
- Invocation:
  Step 1: `GET /consumer/info` with required path/query/body/form/header values
- Constraints:
  `GET /consumer/info` must authenticate as the consumer created by `POST /consumer/register`. Swagger shows a `principal` body, but the implementation uses the authenticated `Principal`, not a client body.

Failure or exceptional branches:
- No reproducible controller-level failure branch is exposed through documented endpoints other than missing/invalid authentication.

Endpoint coverage:
- Covers:
  `GET /consumer/info`
- Distinct meaning:
  Reads the current consumer’s account data.

### Function 11: update consumer profile

Function name:
update consumer profile

Core endpoint(s):
- `PUT /consumer/info/update`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function updates the authenticated consumer’s name, username, password, and email.
- Invocation:
  Step 1: `PUT /consumer/info/update` with required path/query/body/form/header values
- Constraints:
  `PUT /consumer/info/update` must authenticate as the consumer created by `POST /consumer/register` and send a JSON `Consumer` body. This differs from Swagger, which only documents a `principal` body. The implementation compares and overwrites `name`, `username`, `password`, and `email`.

Failure or exceptional branches:
- No explicit controller-level validation branch is implemented beyond returning `success: false` if the authenticated principal cannot be resolved to a consumer.

Endpoint coverage:
- Covers:
  `PUT /consumer/info/update`
- Distinct meaning:
  Mutates current consumer profile fields.

### Function 12: retrieve consumer balance

Function name:
retrieve consumer balance

Core endpoint(s):
- `GET /consumer/balance`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function returns the authenticated consumer’s balance.
- Invocation:
  Step 1: `GET /consumer/balance` with required path/query/body/form/header values
- Constraints:
  `GET /consumer/balance` must authenticate as the consumer created by `POST /consumer/register`.

Failure or exceptional branches:
- No reproducible controller-level failure branch is exposed through documented endpoints other than missing/invalid authentication.

Endpoint coverage:
- Covers:
  `GET /consumer/balance`
- Distinct meaning:
  Reads current consumer balance.

### Function 13: request print-shop registration

Function name:
request print-shop registration

Core endpoint(s):
- `POST /request/register`

Preconditions:
- No prerequisite resource state is required.

Successful execution:
- Result:
  This function creates a pending registration request for a new print shop and manager.
- Invocation:
  Step 1: `POST /request/register` with the required registration body or request parameters
- Constraints:
  Body must contain `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`. The response contains the registration request `id`.

Failure or exceptional branches:
- No explicit controller-level validation branch is implemented.

Endpoint coverage:
- Covers:
  `POST /request/register`
- Distinct meaning:
  Creates a pending print-shop registration request.

### Function 14: list pending print-shop registration requests

Function name:
list pending print-shop registration requests

Core endpoint(s):
- `GET /requests/pending`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.

Successful execution:
- Result:
  This function returns registration requests whose `accepted` flag is false.
- Invocation:
  Step 1: `GET /requests/pending` with the required request id, authenticated principal, and request body values
- Constraints:
  `GET /requests/pending` must authenticate as the admin created by `POST /admin/register`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Caller is not an admin. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /requests/pending`
  - Why this fails:
    The endpoint is secured with `ROLE_ADMIN`.
  - Intentionally violated constraints:
    Admin registration/authentication is omitted.

Endpoint coverage:
- Covers:
  `GET /requests/pending`
- Distinct meaning:
  Admin review queue for print-shop registration.

### Function 15: accept print-shop registration request

Function name:
accept print-shop registration request

Core endpoint(s):
- `POST /request/accept/{id}`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.

Successful execution:
- Result:
  This function turns a pending registration request into a manager account and a print-shop record, links them, deletes the request, and sends an acceptance email.
- Invocation:
  Step 1: `POST /request/accept/{id}` with required path/query/body/form/header values
- Constraints:
  `POST /request/accept/{id}` must authenticate as the admin from `POST /admin/register`. `{id}` in `POST /request/accept/{id}` must be the registration request `id` returned by `POST /request/register`. The created manager credentials are copied from `POST /request/register`’s `managerUsername` and `managerPassword`; the print-shop fields are copied from `POST /request/register`’s `pShop...` fields.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - The failing request is made against state that violates this condition: Registration request does not exist. The endpoint `POST /request/register` is intentionally omitted, called with unrelated values, or its response value is not reused as required. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /request/accept/{id}`
  - Why this fails:
    `registerRequests.findOne(id)` returns `null`, so the response has `success: false`.
  - Intentionally violated constraints:
    `POST /request/register` is intentionally omitted, or `{id}` does not match its response.

Endpoint coverage:
- Covers:
  `POST /request/accept/{id}`
- Distinct meaning:
  Admin approval of print-shop onboarding.

### Function 16: reject print-shop registration request

Function name:
reject print-shop registration request

Core endpoint(s):
- `POST /request/reject/{printRequestID}`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.

Successful execution:
- Result:
  This function deletes a pending registration request and sends a rejection email with a motive.
- Invocation:
  Step 1: `POST /request/reject/{printRequestID}` with required path/query/body/form/header values
- Constraints:
  `POST /request/reject/{printRequestID}` must authenticate as the admin from `POST /admin/register`. `{printRequestID}` in `POST /request/reject/{printRequestID}` must be the registration request `id` returned by `POST /request/register`. The request body is the rejection motive string.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - The failing request is made against state that violates this condition: Registration request does not exist. The endpoint `POST /request/register` is intentionally omitted, called with unrelated values, or its response value is not reused as required. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /request/reject/{printRequestID}`
  - Why this fails:
    `registerRequests.findOne(prid)` returns `null`, so the response has `success: false`.
  - Intentionally violated constraints:
    `POST /request/register` is intentionally omitted, or `{printRequestID}` does not match its response.

Endpoint coverage:
- Covers:
  `POST /request/reject/{printRequestID}`
- Distinct meaning:
  Admin rejection of print-shop onboarding.

### Function 17: list public print shops

Function name:
list public print shops

Core endpoint(s):
- `GET /printshops`

Preconditions:
- No prerequisite resource state is required.

Successful execution:
- Result:
  This function returns all print shops visible through the public print-shop listing.
- Invocation:
  Step 1: `GET /printshops` with required path/query/body/form/header values
- Constraints:
  No specific print shop must exist; the endpoint can return an empty collection.

Failure or exceptional branches:
- No implementation-specific failure branch is visible in `src`.

Endpoint coverage:
- Covers:
  `GET /printshops`
- Distinct meaning:
  Public/global print-shop discovery.

### Function 18: retrieve print shop by id

Function name:
retrieve print shop by id

Core endpoint(s):
- `GET /printshops/{id}`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.

Successful execution:
- Result:
  This function retrieves a specific print shop by ID.
- Invocation:
  Step 1: `GET /printshops/{id}` with required path/query/body/form/header values
- Constraints:
  `POST /request/accept/{id}` uses the request `id` from `POST /request/register` and authenticates as the admin from `POST /admin/register`. `GET /printshop` authenticates as the manager credentials supplied in `POST /request/register` and returns the new print shop. `{id}` in `GET /printshops/{id}` must be the print-shop ID from `GET /printshop`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /printshops/{id}`
  - Why this fails:
    `printshops.findOne(id)` returns `null`, so the controller returns `404`.
  - Intentionally violated constraints:
    The registration and acceptance sequence that creates a print shop is omitted.

Endpoint coverage:
- Covers:
  `GET /printshops/{id}`
- Distinct meaning:
  Resource-specific print-shop read.

### Function 19: find nearest print shops

Function name:
find nearest print shops

Core endpoint(s):
- `GET /printshops/nearest`

Preconditions:
- No prerequisite resource state is required.

Successful execution:
- Result:
  This function returns print shops sorted by distance from the supplied coordinates.
- Invocation:
  Step 1: `GET /printshops/nearest` with query parameters `latitude` and `longitude`
- Constraints:
  Query parameters `latitude` and `longitude` must both be present and parseable as doubles. No specific print shop must exist.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Missing `latitude` or `longitude`. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /printshops/nearest`
  - Why this fails:
    The implementation returns `success: false` when either coordinate is absent.
  - Intentionally violated constraints:
    One or both coordinate query parameters are omitted.

Endpoint coverage:
- Covers:
  `GET /printshops/nearest`
- Distinct meaning:
  Location-based print-shop search.

### Function 20: retrieve print-shop price table

Function name:
retrieve print-shop price table

Core endpoint(s):
- `GET /printshops/{id}/pricetable`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.

Successful execution:
- Result:
  This function returns a print shop’s paper, ring, stapling, and cover prices.
- Invocation:
  Step 1: `GET /printshops/{id}/pricetable` with the required print-shop id and price-table body values
- Constraints:
  `GET /printshops/{id}/pricetable` authenticates as a `ROLE_MANAGER` or `ROLE_USER`. `{id}` in `GET /printshops/{id}/pricetable` must be the print-shop ID returned by `GET /printshop`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /printshops/{id}/pricetable`
  - Why this fails:
    The implementation returns `success: false` when `printshops.findOne(id)` is null.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `GET /printshops/{id}/pricetable`
- Distinct meaning:
  Reads normalized price-table data.

### Function 21: retrieve manager print shop

Function name:
retrieve manager print shop

Core endpoint(s):
- `GET /printshop`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.

Successful execution:
- Result:
  This function returns the print shop assigned to the authenticated manager.
- Invocation:
  Step 1: `GET /printshop` with required path/query/body/form/header values
- Constraints:
  `POST /request/accept/{id}` must use the registration request `id` from `POST /request/register`. `GET /printshop` must authenticate with the manager credentials submitted in `POST /request/register`.

Failure or exceptional branches:
- No documented endpoint creates a manager without a print shop; the visible failure branch only occurs if the authenticated manager has no linked print shop.

Endpoint coverage:
- Covers:
  `GET /printshop`
- Distinct meaning:
  Manager-scoped print-shop lookup.

### Function 22: retrieve print-shop statistics

Function name:
retrieve print-shop statistics

Core endpoint(s):
- `GET /printshops/stats`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.

Successful execution:
- Result:
  This function returns counts of pending, in-progress, and finished requests, employee count, and calculated print-shop profit for the authenticated manager’s print shop.
- Invocation:
  Step 1: `GET /printshops/stats` with required path/query/body/form/header values
- Constraints:
  `GET /printshops/stats` authenticates as the manager created by `POST /request/accept/{id}` using credentials from `POST /request/register`.

Failure or exceptional branches:
- No documented endpoint creates a manager without a print shop; otherwise the controller returns `success: false` if the manager or print shop cannot be resolved.

Endpoint coverage:
- Covers:
  `GET /printshops/stats`
- Distinct meaning:
  Manager dashboard statistics.

### Function 23: list print-shop employees

Function name:
list print-shop employees

Core endpoint(s):
- `GET /printshops/{printShopID}/employees`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.

Successful execution:
- Result:
  This function returns employees assigned to a print shop.
- Invocation:
  Step 1: `GET /printshops/{printShopID}/employees` with required path/query/body/form/header values
- Constraints:
  `GET /printshops/{printShopID}/employees` authenticates as the manager created by `POST /request/accept/{id}`. `{printShopID}` must be the ID returned by `GET /printshop`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /printshops/{printShopID}/employees`
  - Why this fails:
    `printshops.findOne(psid)` is null, so the response has `success: false`.
  - Intentionally violated constraints:
    The registration and acceptance sequence creating `{printShopID}` is omitted.

Endpoint coverage:
- Covers:
  `GET /printshops/{printShopID}/employees`
- Distinct meaning:
  Manager reads employee roster.

### Function 24: add print-shop employee

Function name:
add print-shop employee

Core endpoint(s):
- `POST /printshops/{printShopID}/employees`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.

Successful execution:
- Result:
  This function creates an employee account attached to a print shop.
- Invocation:
  Step 1: `POST /printshops/{printShopID}/employees` with required path/query/body/form/header values
- Constraints:
  `POST /printshops/{printShopID}/employees` authenticates as the manager created by `POST /request/accept/{id}`. `{printShopID}` must be from `GET /printshop`. The body must contain employee `name`, `username`, and `password`; the response returns the employee `id`. Swagger omits this request body, but the implementation requires it.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
    - The failing request is made against state that violates this condition: Employee username already exists. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /printshops/{printShopID}/employees`
  - Why this fails:
    `POST /printshops/{printShopID}/employees` finds the employee username created in `POST /printshops/{printShopID}/employees` and returns `success: false`.
  - Intentionally violated constraints:
    `POST /printshops/{printShopID}/employees` repeats `POST /printshops/{printShopID}/employees`’s `username`.

Endpoint coverage:
- Covers:
  `POST /printshops/{printShopID}/employees`
- Distinct meaning:
  Manager creates an employee account.

### Function 25: edit print-shop employee

Function name:
edit print-shop employee

Core endpoint(s):
- `PUT /printshops/{printShopID}/employees`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.

Successful execution:
- Result:
  This function updates an employee’s name, username, and password.
- Invocation:
  Step 1: `PUT /printshops/{printShopID}/employees` with required path/query/body/form/header values
- Constraints:
  `PUT /printshops/{printShopID}/employees` authenticates as the manager. `{printShopID}` comes from `GET /printshop`. The `PUT /printshops/{printShopID}/employees` body must include the employee `id` returned by `POST /printshops/{printShopID}/employees` plus new `name`, `username`, and `password`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - The failing request is made against state that violates this condition: Employee ID does not exist. The endpoint `POST /printshops/{printShopID}/employees` is intentionally omitted, called with unrelated values, or its response value is not reused as required. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `PUT /printshops/{printShopID}/employees`
  - Why this fails:
    `employees.findOne(editedEmp.getId())` returns null.
  - Intentionally violated constraints:
    The body uses an employee `id` not produced by `POST /printshops/{printShopID}/employees`.

Endpoint coverage:
- Covers:
  `PUT /printshops/{printShopID}/employees`
- Distinct meaning:
  Manager edits employee credentials/profile.

### Function 26: delete print-shop employee

Function name:
delete print-shop employee

Core endpoint(s):
- `DELETE /printshops/{printShopID}/employees/{employeeID}`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.

Successful execution:
- Result:
  This function deletes an employee account by ID.
- Invocation:
  Step 1: `DELETE /printshops/{printShopID}/employees/{employeeID}` with required path/query/body/form/header values
- Constraints:
  `{printShopID}` comes from `GET /printshop`. `{employeeID}` comes from `POST /printshops/{printShopID}/employees`. The implementation only checks whether the employee exists; it does not verify the employee belongs to `{printShopID}`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - The failing request is made against state that violates this condition: Employee ID does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `DELETE /printshops/{printShopID}/employees/{employeeID}`
  - Why this fails:
    `employees.findOne(eid)` returns null and the response has `success: false`.
  - Intentionally violated constraints:
    `{employeeID}` was not produced by the employee creation endpoint.

Endpoint coverage:
- Covers:
  `DELETE /printshops/{printShopID}/employees/{employeeID}`
- Distinct meaning:
  Manager deletes an employee account.

### Function 27: add paper price rows

Function name:
add paper price rows

Core endpoint(s):
- `POST /printshops/{id}/pricetable/papers`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.

Successful execution:
- Result:
  This function inserts paper-copy price rows into a print shop’s price table.
- Invocation:
  Step 1: `POST /printshops/{id}/pricetable/papers` with the required print-shop id and price-table body values
- Constraints:
  `{id}` in `POST /printshops/{id}/pricetable/papers` is the print-shop ID from `GET /printshop`. Body is `PaperTableItem` with `colors`, `infLim`, `supLim`, and any non-default `priceA4SIMPLEX`, `priceA4DUPLEX`, `priceA3SIMPLEX`, `priceA3DUPLEX`. The implementation writes map entries keyed by format, side, color, and page range.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /printshops/{id}/pricetable/papers`
  - Why this fails:
    `printshops.findOne(id)` returns null and the response has `success: false`.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `POST /printshops/{id}/pricetable/papers`
- Distinct meaning:
  Manager adds paper price rows.

### Function 28: edit paper price rows

Function name:
edit paper price rows

Core endpoint(s):
- `PUT /printshops/{id}/pricetable/papers`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- Paper price rows exist in the print shop price table. This can be satisfied by directly inserting price-table entries keyed by format, side, color, and page range, or by calling `POST /printshops/{id}/pricetable/papers` with `colors`, `infLim`, `supLim`, and the applicable A4/A3 simplex/duplex price fields.

Successful execution:
- Result:
  This function overwrites paper-copy price rows in a print shop’s price table.
- Invocation:
  Step 1: `PUT /printshops/{id}/pricetable/papers` with the required print-shop id and price-table body values
- Constraints:
  `{id}` in `POST /printshops/{id}/pricetable/papers` and `PUT /printshops/{id}/pricetable/papers` must be the same print-shop ID from `GET /printshop`. `PUT /printshops/{id}/pricetable/papers` uses a `PaperTableItem` with matching key fields for the rows being changed. Implementation logic is the same upsert operation as POST.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `PUT /printshops/{id}/pricetable/papers`
  - Why this fails:
    `printshops.findOne(id)` returns null.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `PUT /printshops/{id}/pricetable/papers`
- Distinct meaning:
  Manager edits paper price rows.

### Function 29: delete paper price rows

Function name:
delete paper price rows

Core endpoint(s):
- `POST /printshops/{id}/pricetable/deletepaper`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- Paper price rows exist in the print shop price table. This can be satisfied by directly inserting price-table entries keyed by format, side, color, and page range, or by calling `POST /printshops/{id}/pricetable/papers` with `colors`, `infLim`, `supLim`, and the applicable A4/A3 simplex/duplex price fields.

Successful execution:
- Result:
  This function removes paper-copy price rows from a print shop’s price table.
- Invocation:
  Step 1: `POST /printshops/{id}/pricetable/deletepaper` with the required print-shop id and price-table body values
- Constraints:
  `{id}` in `POST /printshops/{id}/pricetable/papers` and `POST /printshops/{id}/pricetable/deletepaper` must be the same print-shop ID. `POST /printshops/{id}/pricetable/deletepaper` body must describe the same paper item keys inserted in `POST /printshops/{id}/pricetable/papers` so the corresponding map entries are removed.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /printshops/{id}/pricetable/deletepaper`
  - Why this fails:
    `printshops.findOne(id)` returns null.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `POST /printshops/{id}/pricetable/deletepaper`
- Distinct meaning:
  Manager removes paper price rows.

### Function 30: add ring binding price

Function name:
add ring binding price

Core endpoint(s):
- `POST /printshops/{id}/pricetable/rings`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.

Successful execution:
- Result:
  This function inserts a ring-binding price into a print shop’s price table.
- Invocation:
  Step 1: `POST /printshops/{id}/pricetable/rings` with the required print-shop id and price-table body values
- Constraints:
  `{id}` comes from `GET /printshop`. Body is `RingTableItem` with `ringType`, `infLim`, `supLim`, and `price`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /printshops/{id}/pricetable/rings`
  - Why this fails:
    `printshops.findOne(id)` returns null.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `POST /printshops/{id}/pricetable/rings`
- Distinct meaning:
  Manager adds ring-binding price.

### Function 31: edit ring binding price

Function name:
edit ring binding price

Core endpoint(s):
- `PUT /printshops/{id}/pricetable/rings`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- A ring-binding price row exists in the print shop price table. This can be satisfied by directly inserting the binding price-table key, or by calling `POST /printshops/{id}/pricetable/rings` with `ringType`, `infLim`, `supLim`, and `price`.

Successful execution:
- Result:
  This function overwrites an existing ring-binding price.
- Invocation:
  Step 1: `PUT /printshops/{id}/pricetable/rings` with the required print-shop id and price-table body values
- Constraints:
  `PUT /printshops/{id}/pricetable/rings` must use the same `{id}` and ring key fields as `POST /printshops/{id}/pricetable/rings` to replace that price. Implementation logic is an upsert.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `PUT /printshops/{id}/pricetable/rings`
  - Why this fails:
    `printshops.findOne(id)` returns null.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `PUT /printshops/{id}/pricetable/rings`
- Distinct meaning:
  Manager edits ring-binding price.

### Function 32: delete ring binding price

Function name:
delete ring binding price

Core endpoint(s):
- `POST /printshops/{id}/pricetable/deletering`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- A ring-binding price row exists in the print shop price table. This can be satisfied by directly inserting the binding price-table key, or by calling `POST /printshops/{id}/pricetable/rings` with `ringType`, `infLim`, `supLim`, and `price`.

Successful execution:
- Result:
  This function removes a ring-binding price from the price table.
- Invocation:
  Step 1: `POST /printshops/{id}/pricetable/deletering` with the required print-shop id and price-table body values
- Constraints:
  `POST /printshops/{id}/pricetable/deletering` must use the same print-shop ID and ring key fields inserted in `POST /printshops/{id}/pricetable/rings`. The delete endpoint converts presentation ring type strings before removing the generated key.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /printshops/{id}/pricetable/deletering`
  - Why this fails:
    `printshops.findOne(id)` returns null.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `POST /printshops/{id}/pricetable/deletering`
- Distinct meaning:
  Manager removes ring-binding price.

### Function 33: add cover price

Function name:
add cover price

Core endpoint(s):
- `POST /printshops/{id}/pricetable/covers`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.

Successful execution:
- Result:
  This function inserts cover prices into the print shop’s price table.
- Invocation:
  Step 1: `POST /printshops/{id}/pricetable/covers` with the required print-shop id and price-table body values
- Constraints:
  `{id}` comes from `GET /printshop`. Body is `CoverTableItem` with `coverType`, `priceA4`, and `priceA3`. The implementation converts it into one or more `CoverItem` keys.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /printshops/{id}/pricetable/covers`
  - Why this fails:
    `printshops.findOne(id)` returns null.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `POST /printshops/{id}/pricetable/covers`
- Distinct meaning:
  Manager adds cover prices.

### Function 34: edit cover price

Function name:
edit cover price

Core endpoint(s):
- `PUT /printshops/{id}/pricetable/covers`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- Cover price rows exist in the print shop price table. This can be satisfied by directly inserting cover price-table keys, or by calling `POST /printshops/{id}/pricetable/covers` with `coverType`, `priceA4`, and `priceA3`.

Successful execution:
- Result:
  This function overwrites cover prices in the print shop’s price table.
- Invocation:
  Step 1: `PUT /printshops/{id}/pricetable/covers` with the required print-shop id and price-table body values
- Constraints:
  `PUT /printshops/{id}/pricetable/covers` uses the same print-shop ID and cover key fields as `POST /printshops/{id}/pricetable/covers`. Implementation logic is the same upsert operation as POST.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `PUT /printshops/{id}/pricetable/covers`
  - Why this fails:
    `printshops.findOne(id)` returns null.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `PUT /printshops/{id}/pricetable/covers`
- Distinct meaning:
  Manager edits cover prices.

### Function 35: delete cover price

Function name:
delete cover price

Core endpoint(s):
- `POST /printshops/{id}/pricetable/deletecover`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- Cover price rows exist in the print shop price table. This can be satisfied by directly inserting cover price-table keys, or by calling `POST /printshops/{id}/pricetable/covers` with `coverType`, `priceA4`, and `priceA3`.

Successful execution:
- Result:
  This function removes cover prices from the print shop’s price table.
- Invocation:
  Step 1: `POST /printshops/{id}/pricetable/deletecover` with the required print-shop id and price-table body values
- Constraints:
  `POST /printshops/{id}/pricetable/deletecover` must use the same print-shop ID and cover key fields inserted in `POST /printshops/{id}/pricetable/covers`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /printshops/{id}/pricetable/deletecover`
  - Why this fails:
    `printshops.findOne(id)` returns null.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `POST /printshops/{id}/pricetable/deletecover`
- Distinct meaning:
  Manager removes cover prices.

### Function 36: edit stapling price

Function name:
edit stapling price

Core endpoint(s):
- `PUT /printshops/{printShopID}/pricetable/editstapling`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.

Successful execution:
- Result:
  This function changes the price-table entry for stapling.
- Invocation:
  Step 1: `PUT /printshops/{printShopID}/pricetable/editstapling` with the required print-shop id and price-table body values
- Constraints:
  `{printShopID}` comes from `GET /printshop`. The request body is the new price string, parsed as a float, and stored under key `BINDING,STAPLING,0,0`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `PUT /printshops/{printShopID}/pricetable/editstapling`
  - Why this fails:
    `printshops.findOne(psid)` returns null.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `PUT /printshops/{printShopID}/pricetable/editstapling`
- Distinct meaning:
  Manager edits stapling price.

### Function 37: list print-shop reviews

Function name:
list print-shop reviews

Core endpoint(s):
- `GET /printshops/{id}/reviews`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.

Successful execution:
- Result:
  This function returns reviews for a specific print shop.
- Invocation:
  Step 1: `GET /printshops/{id}/reviews` with the required path values and review `review`/`rating` values when the method mutates a review
- Constraints:
  `{id}` in `GET /printshops/{id}/reviews` must be the print-shop ID from `GET /printshop`. Endpoint is secured for manager, employee, or user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /printshops/{id}/reviews`
  - Why this fails:
    The controller returns `404` when `printshops.findOne(id)` is null.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `GET /printshops/{id}/reviews`
- Distinct meaning:
  Reads public review data for one print shop.

### Function 38: add print-shop review

Function name:
add print-shop review

Core endpoint(s):
- `POST /printshops/{id}/reviews`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.

Successful execution:
- Result:
  This function creates a review for a print shop by the authenticated consumer and updates the shop’s average rating.
- Invocation:
  Step 1: `POST /printshops/{id}/reviews` with the required path values and review `review`/`rating` values when the method mutates a review
- Constraints:
  `POST /printshops/{id}/reviews` authenticates as the consumer from `POST /consumer/register`. `{id}` comes from `GET /printshop`. Body must contain `review` text and `rating` string. The response contains the created review `id`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - The failing request is made against state that violates this condition: Print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /printshops/{id}/reviews`
  - Why this fails:
    The controller returns `404` when `printshops.findOne(id)` is null.
  - Intentionally violated constraints:
    The print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `POST /printshops/{id}/reviews`
- Distinct meaning:
  Consumer creates a print-shop review.

### Function 39: edit print-shop review

Function name:
edit print-shop review

Core endpoint(s):
- `PUT /printshops/{printShopId}/reviews/{reviewId}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- The resource state required by `POST /printshops/{printShopId}/reviews` exists. This can be satisfied by direct database setup of the same resource relationships, or by calling `POST /printshops/{printShopId}/reviews` with the path, query, body, form, and header values documented for that endpoint and reusing any response identifiers it returns.

Successful execution:
- Result:
  This function changes the text and rating of an existing review owned by the authenticated consumer and recalculates the print-shop rating.
- Invocation:
  Step 1: `PUT /printshops/{printShopId}/reviews/{reviewId}` with the required path values and review `review`/`rating` values when the method mutates a review
- Constraints:
  `{printShopId}` comes from `GET /printshop`. `{reviewId}` comes from `POST /printshops/{printShopId}/reviews`. `PUT /printshops/{printShopId}/reviews/{reviewId}` must authenticate as the same consumer from `POST /consumer/register`. Implementation reads `review` and `rating` as request parameters, not from a JSON body as Swagger implies.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - The failing request is made against state that violates this condition: Review does not exist. The endpoint `POST /printshops/{printShopId}/reviews` is intentionally omitted, called with unrelated values, or its response value is not reused as required. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `PUT /printshops/{printShopId}/reviews/{reviewId}`
  - Why this fails:
    `reviews.findOne(reviewId)` returns null and the controller returns `404`.
  - Intentionally violated constraints:
    `{reviewId}` was not produced by `POST /printshops/{printShopId}/reviews`.
- Branch 2:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - A second consumer account exists with a distinct username and password for ownership or mismatch checks. This can be satisfied by directly inserting another `Consumer`/`User` row with `ROLE_USER`, or by calling `POST /consumer/register` again with request parameters `username`, `password`, `email`, `name`, `latitude`, and `longitude`; the response supplies that consumer's `id` when it must be reused.
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - The resource state required by `POST /printshops/{printShopId}/reviews` exists. This can be satisfied by direct database setup of the same resource relationships, or by calling `POST /printshops/{printShopId}/reviews` with the path, query, body, form, and header values documented for that endpoint and reusing any response identifiers it returns.
    - The failing request is made against state that violates this condition: Authenticated consumer does not own the review. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `PUT /printshops/{printShopId}/reviews/{reviewId}`
  - Why this fails:
    `PUT /printshops/{printShopId}/reviews/{reviewId}` authenticates as the second consumer, but the review was created by the first consumer, so the controller returns `401`.
  - Intentionally violated constraints:
    The principal username in `PUT /printshops/{printShopId}/reviews/{reviewId}` differs from the review owner created in `POST /printshops/{printShopId}/reviews`.

Endpoint coverage:
- Covers:
  `PUT /printshops/{printShopId}/reviews/{reviewId}`
- Distinct meaning:
  Consumer edits their own review.

### Function 40: delete print-shop review

Function name:
delete print-shop review

Core endpoint(s):
- `DELETE /printshops/{printShopId}/reviews/{reviewId}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- The resource state required by `POST /printshops/{printShopId}/reviews` exists. This can be satisfied by direct database setup of the same resource relationships, or by calling `POST /printshops/{printShopId}/reviews` with the path, query, body, form, and header values documented for that endpoint and reusing any response identifiers it returns.

Successful execution:
- Result:
  This function removes an existing review owned by the authenticated consumer from a print shop.
- Invocation:
  Step 1: `DELETE /printshops/{printShopId}/reviews/{reviewId}` with the required path values and review `review`/`rating` values when the method mutates a review
- Constraints:
  `{printShopId}` comes from `GET /printshop`. `{reviewId}` comes from `POST /printshops/{printShopId}/reviews`. `DELETE /printshops/{printShopId}/reviews/{reviewId}` authenticates as the same consumer that created the review.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - A second consumer account exists with a distinct username and password for ownership or mismatch checks. This can be satisfied by directly inserting another `Consumer`/`User` row with `ROLE_USER`, or by calling `POST /consumer/register` again with request parameters `username`, `password`, `email`, `name`, `latitude`, and `longitude`; the response supplies that consumer's `id` when it must be reused.
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - The resource state required by `POST /printshops/{printShopId}/reviews` exists. This can be satisfied by direct database setup of the same resource relationships, or by calling `POST /printshops/{printShopId}/reviews` with the path, query, body, form, and header values documented for that endpoint and reusing any response identifiers it returns.
    - The failing request is made against state that violates this condition: Authenticated consumer does not own the review. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `DELETE /printshops/{printShopId}/reviews/{reviewId}`
  - Why this fails:
    `DELETE /printshops/{printShopId}/reviews/{reviewId}` authenticates as a different consumer, so the controller returns `401`.
  - Intentionally violated constraints:
    The deleting principal differs from the review owner.

Endpoint coverage:
- Covers:
  `DELETE /printshops/{printShopId}/reviews/{reviewId}`
- Distinct meaning:
  Consumer deletes their own review.

### Function 41: list consumer printing schemas

Function name:
list consumer printing schemas

Core endpoint(s):
- `GET /consumer/{consumerID}/printingschemas`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function returns the printing schemas associated with a consumer.
- Invocation:
  Step 1: `GET /consumer/{consumerID}/printingschemas` with required path/query/body/form/header values
- Constraints:
  `{consumerID}` must be the consumer ID returned by `POST /consumer/register`. Endpoint is secured with `ROLE_USER`; implementation does not verify that the path ID matches the authenticated user.

Failure or exceptional branches:
- No explicit null handling exists for a missing consumer ID; using an ID not produced by the corresponding endpoint can result in a server exception rather than a structured failure response.

Endpoint coverage:
- Covers:
  `GET /consumer/{consumerID}/printingschemas`
- Distinct meaning:
  Reads a consumer’s saved print recipes/schemas.

### Function 42: add consumer printing schema

Function name:
add consumer printing schema

Core endpoint(s):
- `POST /consumer/{consumerID}/printingschemas`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function creates a printing schema and associates it with a consumer.
- Invocation:
  Step 1: `POST /consumer/{consumerID}/printingschemas` with required path/query/body/form/header values
- Constraints:
  `{consumerID}` must be from `POST /consumer/register`. Body is `PrintingSchema` with at least `name` and `paperSpecs`; `bindingSpecs` and `coverSpecs` are optional strings. Response returns the new schema `id`.

Failure or exceptional branches:
- No explicit controller-level validation branch is implemented; a missing consumer can cause a null dereference after the schema is saved.

Endpoint coverage:
- Covers:
  `POST /consumer/{consumerID}/printingschemas`
- Distinct meaning:
  Consumer saves a print configuration.

### Function 43: edit consumer printing schema

Function name:
edit consumer printing schema

Core endpoint(s):
- `PUT /consumer/{consumerID}/printingschemas/{printingSchemaID}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.

Successful execution:
- Result:
  This function updates a printing schema’s name, paper specs, binding specs, and cover specs.
- Invocation:
  Step 1: `PUT /consumer/{consumerID}/printingschemas/{printingSchemaID}` with required path/query/body/form/header values
- Constraints:
  `{consumerID}` comes from `POST /consumer/register`. `{printingSchemaID}` comes from `POST /consumer/{consumerID}/printingschemas`. `PUT /consumer/{consumerID}/printingschemas/{printingSchemaID}` body supplies replacement `name`, `paperSpecs`, `bindingSpecs`, and `coverSpecs`.

Failure or exceptional branches:
- No explicit null handling exists for a missing schema ID; using an ID not produced by the corresponding endpoint can result in a server exception.

Endpoint coverage:
- Covers:
  `PUT /consumer/{consumerID}/printingschemas/{printingSchemaID}`
- Distinct meaning:
  Consumer edits a saved print configuration.

### Function 44: soft-delete consumer printing schema

Function name:
soft-delete consumer printing schema

Core endpoint(s):
- `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.

Successful execution:
- Result:
  This function marks a printing schema as deleted.
- Invocation:
  Step 1: `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}` with required path/query/body/form/header values
- Constraints:
  `{consumerID}` comes from `POST /consumer/register`. `{printingSchemaID}` comes from `POST /consumer/{consumerID}/printingschemas`. The schema is not removed from storage; `deleted` is set true.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
    - The resource state required by `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}` exists. This can be satisfied by direct database setup of the same resource relationships, or by calling `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}` with the path, query, body, form, and header values documented for that endpoint and reusing any response identifiers it returns.
    - The failing request is made against state that violates this condition: Printing schema is already deleted. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}`
  - Why this fails:
    The second delete sees `ps.isDeleted()` true and returns a JSON property named `"false": true` instead of `success: false`.
  - Intentionally violated constraints:
    `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}` reuses a schema already deleted by `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}`.

Endpoint coverage:
- Covers:
  `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}`
- Distinct meaning:
  Consumer soft-deletes a print configuration.

### Function 45: calculate consumer print-request budgets

Function name:
calculate consumer print-request budgets

Core endpoint(s):
- `POST /consumer/budget`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.

Successful execution:
- Result:
  This function uploads PDFs, creates a `PrintRequest` with documents/specs for the authenticated consumer, calculates budgets for selected print shops, and returns `printRequestID`.
- Invocation:
  Step 1: `POST /consumer/budget` with required path/query/body/form/header values
- Constraints:
  `{consumerID}` comes from `POST /consumer/register`. `POST /request/accept/{id}` uses the registration request ID from `POST /request/register`. `GET /printshop` returns `{printShopID}`. `POST /consumer/{consumerID}/printingschemas` returns `{printingSchemaID}`. `POST /consumer/budget` must authenticate as the consumer from `POST /consumer/register`, send multipart PDF files, and include a request part named `printRequest` whose JSON `printshops` array contains `{printShopID}` and whose `files` entries match uploaded PDF filenames and refer to `{printingSchemaID}`. Swagger documents `requestJSON` form data, but the implementation expects `@RequestPart("printRequest")`.

Failure or exceptional branches:
- No structured controller-level failure branch is implemented for malformed multipart/JSON; errors would surface as exceptions.

Endpoint coverage:
- Covers:
  `POST /consumer/budget`
- Distinct meaning:
  Creates a draft print request and calculates print-shop budgets.

### Function 46: submit print request with ProxyPrint balance

Function name:
submit print request with ProxyPrint balance

Core endpoint(s):
- `POST /consumer/printrequest/{printRequestID}/submit`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
- A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
- The consumer balance is high enough for a ProxyPrint-balance payment. This can be satisfied by directly setting the consumer `Money` balance, or by calling `POST /paypal/ipn/consumer/{consumerID}` with IPN fields including `payment_status=Completed` and `mc_gross` at least equal to the submitted cost.

Successful execution:
- Result:
  This function submits a calculated print request to a selected print shop, immediately pays with ProxyPrint balance, deducts the consumer balance, credits the admin balance, and sets the request status to `PENDING`.
- Invocation:
  Step 1: `POST /consumer/printrequest/{printRequestID}/submit` with the required request id, authenticated principal, and request body values
- Constraints:
  `POST /consumer/budget` returns `{printRequestID}` and a budget for `{printShopID}` from `GET /printshop`. `POST /paypal/ipn/consumer/{consumerID}` must use PayPal IPN fields including `payment_status=Completed` and `mc_gross` high enough to cover the `POST /consumer/printrequest/{printRequestID}/submit` cost. `POST /consumer/printrequest/{printRequestID}/submit` body must include `printshopID` from `GET /printshop`, `budget`, and `paymentMethod` exactly `PROXYPRINT_PAYMENT`. The implementation trusts the submitted `budget`; it does not re-check it against `POST /consumer/budget`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
    - A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
    - The failing request is made against state that violates this condition: Consumer balance is lower than submitted cost. The endpoint `POST /paypal/ipn/consumer/{consumerID}` is intentionally omitted, called with unrelated values, or its response value is not reused as required. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /consumer/printrequest/{printRequestID}/submit`
  - Why this fails:
    With `paymentMethod=PROXYPRINT_PAYMENT`, the controller checks balance and returns `success: false` when it is insufficient.
  - Intentionally violated constraints:
    `POST /paypal/ipn/consumer/{consumerID}` is omitted or underfunded before submission.
- Branch 2:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - The failing request is made against state that violates this condition: Print request or print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /consumer/printrequest/{printRequestID}/submit`
  - Why this fails:
    `printRequests.findOne(prid)` or `printShops.findOne(pshopID)` returns null, producing `success: false`.
  - Intentionally violated constraints:
    The budget endpoint and/or print-shop creation endpoint is omitted.

Endpoint coverage:
- Covers:
  `POST /consumer/printrequest/{printRequestID}/submit`
- Distinct meaning:
  Finalizes a request using internal balance payment.

### Function 47: submit print request for PayPal payment

Function name:
submit print request for PayPal payment

Core endpoint(s):
- `POST /consumer/printrequest/{printRequestID}/submit`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
- A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.

Successful execution:
- Result:
  This function submits a calculated print request to a print shop, marks its payment type as PayPal, leaves it awaiting payment confirmation, and returns `success: true`.
- Invocation:
  Step 1: `POST /consumer/printrequest/{printRequestID}/submit` with the required request id, authenticated principal, and request body values
- Constraints:
  `POST /consumer/printrequest/{printRequestID}/submit` body must include `printshopID` from `GET /printshop`, `budget`, and a `paymentMethod` value other than `PROXYPRINT_PAYMENT`. The code treats any other value as PayPal and sets `paymentType` to `PAYPAL_PAYMENT`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - The failing request is made against state that violates this condition: Print request or print shop does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /consumer/printrequest/{printRequestID}/submit`
  - Why this fails:
    The controller cannot find the draft print request or selected print shop.
  - Intentionally violated constraints:
    The draft creation and/or print-shop creation sequence is omitted.

Endpoint coverage:
- Covers:
  `POST /consumer/printrequest/{printRequestID}/submit`
- Distinct meaning:
  Finalizes a request for later PayPal IPN confirmation.

### Function 48: confirm PayPal print-request payment

Function name:
confirm PayPal print-request payment

Core endpoint(s):
- `POST /paypal/ipn/{printRequestID}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
- A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
- The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.

Successful execution:
- Result:
  This function processes a PayPal IPN callback for a submitted PayPal print request, sets the request status to `PENDING`, stores the PayPal transaction ID, and notifies the consumer.
- Invocation:
  Step 1: `POST /paypal/ipn/{printRequestID}` with the PayPal IPN fields required by the callback
- Constraints:
  `POST /consumer/printrequest/{printRequestID}/submit` must submit with PayPal payment as described in Function 47. `POST /paypal/ipn/{printRequestID}` path ID must be the `printRequestID` from `POST /consumer/budget`. IPN body must include `payer_email`, `mc_gross` equal to the print request cost, `payment_status=Completed`, and `txn_id`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
    - A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
    - The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.
    - The failing request is made against state that violates this condition: IPN amount or status does not match. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /paypal/ipn/{printRequestID}`
  - Why this fails:
    The controller only updates the request when `payment_status` is PayPal completed and `mc_gross` equals the stored cost.
  - Intentionally violated constraints:
    `POST /paypal/ipn/{printRequestID}` sends a non-`Completed` status or an `mc_gross` different from the request cost.
- Branch 2:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print request does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /paypal/ipn/{printRequestID}`
  - Why this fails:
    `printRequests.findOne(prid)` returns null, so the callback logs and returns without updating anything.
  - Intentionally violated constraints:
    The print-request creation and submission sequence is omitted.

Endpoint coverage:
- Covers:
  `POST /paypal/ipn/{printRequestID}`
- Distinct meaning:
  PayPal callback that releases a PayPal-paid request to the print shop.

### Function 49: create external print request

Function name:
create external print request

Core endpoint(s):
- `POST /printdocument`

Preconditions:
- No prerequisite resource state is required.

Successful execution:
- Result:
  This function lets an external platform upload PDF documents and creates a print request with default print specs.
- Invocation:
  Step 1: `POST /printdocument` with the required document path values, multipart files, or budget body values
- Constraints:
  The implementation expects multipart PDF files, although Swagger documents no parameters. Non-PDF files are ignored. Response returns `printRequestID`.

Failure or exceptional branches:
- No structured controller-level failure branch is implemented for missing or malformed files.

Endpoint coverage:
- Covers:
  `POST /printdocument`
- Distinct meaning:
  External integration creates a print request with default schema.

### Function 50: retrieve external print-request documents

Function name:
retrieve external print-request documents

Core endpoint(s):
- `GET /printdocument/{id}`

Preconditions:
- An external print request exists with uploaded PDF documents and default document specs. This can be satisfied by directly inserting a `PrintRequest` with linked `Document` and default `DocumentSpec` rows, or by calling `POST /printdocument` with multipart PDF files; the response supplies `{printRequestID}`.

Successful execution:
- Result:
  This function returns document metadata for an external print request.
- Invocation:
  Step 1: `GET /printdocument/{id}` with the required document path values, multipart files, or budget body values
- Constraints:
  `{id}` in `GET /printdocument/{id}` must be the `printRequestID` returned by `POST /printdocument`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Print request does not exist. The endpoint `POST /printdocument` is intentionally omitted, called with unrelated values, or its response value is not reused as required. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /printdocument/{id}`
  - Why this fails:
    `printRequests.findOne(id)` returns null and the response has `success: false`.
  - Intentionally violated constraints:
    `POST /printdocument` is omitted, or `{id}` does not match its response.

Endpoint coverage:
- Covers:
  `GET /printdocument/{id}`
- Distinct meaning:
  External integration reads uploaded document metadata.

### Function 51: calculate external print-request budget

Function name:
calculate external print-request budget

Core endpoint(s):
- `POST /printdocument/{id}/budget`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- An external print request exists with uploaded PDF documents and default document specs. This can be satisfied by directly inserting a `PrintRequest` with linked `Document` and default `DocumentSpec` rows, or by calling `POST /printdocument` with multipart PDF files; the response supplies `{printRequestID}`.

Successful execution:
- Result:
  This function associates an external print request with the authenticated consumer and calculates budgets for selected print shops.
- Invocation:
  Step 1: `POST /printdocument/{id}/budget` with the required document path values, multipart files, or budget body values
- Constraints:
  `POST /printdocument/{id}/budget` authenticates as the consumer from `POST /consumer/register`. `{id}` is the `printRequestID` from `POST /printdocument`. `POST /printdocument/{id}/budget` body is a JSON list of print-shop IDs and must include the print-shop ID returned by `GET /printshop`.

Failure or exceptional branches:
- No explicit null check exists before `printRequest.setConsumer(consumer)`; using a missing print-request ID can cause a server exception rather than structured `success: false`.

Endpoint coverage:
- Covers:
  `POST /printdocument/{id}/budget`
- Distinct meaning:
  External integration budget calculation.

### Function 52: load consumer balance from PayPal IPN

Function name:
load consumer balance from PayPal IPN

Core endpoint(s):
- `POST /paypal/ipn/consumer/{consumerID}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function processes a PayPal IPN callback for a consumer top-up, increases the consumer balance, and sends a notification.
- Invocation:
  Step 1: `POST /paypal/ipn/consumer/{consumerID}` with the PayPal IPN fields required by the callback
- Constraints:
  `{consumerID}` must be the ID returned by `POST /consumer/register`. IPN body must include `mc_gross` and `payment_status=Completed`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - The failing request is made against state that violates this condition: Payment status is not completed. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /paypal/ipn/consumer/{consumerID}`
  - Why this fails:
    The controller does not increase balance and instead sends a failure notification.
  - Intentionally violated constraints:
    `POST /paypal/ipn/consumer/{consumerID}` sends `payment_status` other than `Completed`.

Endpoint coverage:
- Covers:
  `POST /paypal/ipn/consumer/{consumerID}`
- Distinct meaning:
  PayPal callback for consumer balance top-up.

### Function 53: list consumer pending print requests

Function name:
list consumer pending print requests

Core endpoint(s):
- `GET /consumer/requests`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function returns the authenticated consumer’s pending print requests.
- Invocation:
  Step 1: `GET /consumer/requests` with the required request id, authenticated principal, and request body values
- Constraints:
  `GET /consumer/requests` must authenticate as the consumer from `POST /consumer/register`. The list can be empty.

Failure or exceptional branches:
- No reproducible controller-level failure branch is exposed through documented endpoints other than missing/invalid authentication.

Endpoint coverage:
- Covers:
  `GET /consumer/requests`
- Distinct meaning:
  Consumer views active pending requests.

### Function 54: cancel consumer pending print request

Function name:
cancel consumer pending print request

Core endpoint(s):
- `DELETE /consumer/requests/cancel/{id}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
- A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
- The consumer balance is high enough for a ProxyPrint-balance payment. This can be satisfied by directly setting the consumer `Money` balance, or by calling `POST /paypal/ipn/consumer/{consumerID}` with IPN fields including `payment_status=Completed` and `mc_gross` at least equal to the submitted cost.
- The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.

Successful execution:
- Result:
  This function cancels a pending print request owned by the authenticated consumer, removes it from the print shop and consumer, and refunds the cost to the consumer balance from the admin balance.
- Invocation:
  Step 1: `DELETE /consumer/requests/cancel/{id}` with the required request id, authenticated principal, and request body values
- Constraints:
  `{consumerID}` comes from `POST /consumer/register`. `{printRequestID}` from `POST /consumer/budget` is used in `POST /consumer/printrequest/{printRequestID}/submit` and as `{id}` in `DELETE /consumer/requests/cancel/{id}`. `POST /consumer/printrequest/{printRequestID}/submit` must submit with `PROXYPRINT_PAYMENT` and enough balance so status becomes `PENDING`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - The failing request is made against state that violates this condition: Print request does not exist for the authenticated consumer. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `DELETE /consumer/requests/cancel/{id}`
  - Why this fails:
    `printrequests.findByIdInAndConsumer(id, consumer)` returns null.
  - Intentionally violated constraints:
    The budget and submit sequence producing `{id}` is omitted.
- Branch 2:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
    - A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
    - A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
    - The consumer balance is high enough for a ProxyPrint-balance payment. This can be satisfied by directly setting the consumer `Money` balance, or by calling `POST /paypal/ipn/consumer/{consumerID}` with IPN fields including `payment_status=Completed` and `mc_gross` at least equal to the submitted cost.
    - The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.
    - The assigned print request has already been advanced by `POST /printshops/requests/{id}`. This can also be satisfied by directly setting the corresponding `PrintRequest.status` and employee timestamp fields in the database.
    - The failing request is made against state that violates this condition: Print request is no longer pending. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `DELETE /consumer/requests/cancel/{id}`
  - Why this fails:
    `POST /printshops/requests/{id}` advances the request from `PENDING` to `IN_PROGRESS`, and consumer cancellation only succeeds for `PENDING`.
  - Intentionally violated constraints:
    `DELETE /consumer/requests/cancel/{id}` attempts cancellation after the print shop has started processing.

Endpoint coverage:
- Covers:
  `DELETE /consumer/requests/cancel/{id}`
- Distinct meaning:
  Consumer cancels their own pending request.

### Function 55: list consumer satisfied requests

Function name:
list consumer satisfied requests

Core endpoint(s):
- `GET /consumer/satisfied`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function returns the authenticated consumer’s finished or lifted requests with print-shop names.
- Invocation:
  Step 1: `GET /consumer/satisfied` with required path/query/body/form/header values
- Constraints:
  `GET /consumer/satisfied` must authenticate as the consumer from `POST /consumer/register`. The list can be empty.

Failure or exceptional branches:
- No reproducible controller-level failure branch is exposed through documented endpoints other than missing/invalid authentication.

Endpoint coverage:
- Covers:
  `GET /consumer/satisfied`
- Distinct meaning:
  Consumer views completed request history.

### Function 56: list print-shop active queue

Function name:
list print-shop active queue

Core endpoint(s):
- `GET /printshops/requests`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.

Successful execution:
- Result:
  This function returns pending and in-progress requests for the authenticated employee’s print shop.
- Invocation:
  Step 1: `GET /printshops/requests` with the required request id, authenticated principal, and request body values
- Constraints:
  `GET /printshops/requests` must authenticate as the employee created by `POST /printshops/{printShopID}/employees`. Although the annotation allows managers, the implementation looks up the principal in `EmployeeDAO`, so a manager principal can fail at runtime.

Failure or exceptional branches:
- No structured branch exists for a missing employee; it can cause a null dereference before `printshop == null` is checked.

Endpoint coverage:
- Covers:
  `GET /printshops/requests`
- Distinct meaning:
  Employee views active work queue.

### Function 57: retrieve print-shop request detail

Function name:
retrieve print-shop request detail

Core endpoint(s):
- `GET /printshops/requests/{id}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
- A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
- The consumer balance is high enough for a ProxyPrint-balance payment. This can be satisfied by directly setting the consumer `Money` balance, or by calling `POST /paypal/ipn/consumer/{consumerID}` with IPN fields including `payment_status=Completed` and `mc_gross` at least equal to the submitted cost.
- The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.

Successful execution:
- Result:
  This function returns a specific print request assigned to the authenticated employee’s print shop, including document specs formatted for presentation.
- Invocation:
  Step 1: `GET /printshops/requests/{id}` with the required request id, authenticated principal, and request body values
- Constraints:
  `{id}` in `GET /printshops/requests/{id}` must be the `printRequestID` from `POST /consumer/budget`. `GET /printshops/requests/{id}` authenticates as the employee from `POST /printshops/{printShopID}/employees`. The print request must be assigned to the employee’s print shop in `POST /consumer/printrequest/{printRequestID}/submit`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
    - The failing request is made against state that violates this condition: Request is not assigned to the employee’s print shop or does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /printshops/requests/{id}`
  - Why this fails:
    `printrequests.findByIdInAndPrintshop(id, printshop)` returns null.
  - Intentionally violated constraints:
    The print-request creation and submission sequence assigning `{id}` to the print shop is omitted.

Endpoint coverage:
- Covers:
  `GET /printshops/requests/{id}`
- Distinct meaning:
  Employee reads one assigned request.

### Function 58: start processing print request

Function name:
start processing print request

Core endpoint(s):
- `POST /printshops/requests/{id}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
- A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
- The consumer balance is high enough for a ProxyPrint-balance payment. This can be satisfied by directly setting the consumer `Money` balance, or by calling `POST /paypal/ipn/consumer/{consumerID}` with IPN fields including `payment_status=Completed` and `mc_gross` at least equal to the submitted cost.
- The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.

Successful execution:
- Result:
  This function changes a print request from `PENDING` to `IN_PROGRESS`, records the attending employee, and notifies the consumer.
- Invocation:
  Step 1: `POST /printshops/requests/{id}` with the required request id, authenticated principal, and request body values
- Constraints:
  `{id}` in `POST /printshops/requests/{id}` is the `printRequestID` from `POST /consumer/budget`. `POST /printshops/requests/{id}` authenticates as the employee from `POST /printshops/{printShopID}/employees`. `POST /consumer/printrequest/{printRequestID}/submit` must make the request `PENDING`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
    - The failing request is made against state that violates this condition: Request is not pending or not assigned to the employee’s print shop. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /printshops/requests/{id}`
  - Why this fails:
    No matching assigned request is found, so the response has `success: false`.
  - Intentionally violated constraints:
    The print request creation/submission sequence is omitted.

Endpoint coverage:
- Covers:
  `POST /printshops/requests/{id}`
- Distinct meaning:
  First status transition: `PENDING` to `IN_PROGRESS`.

### Function 59: finish print request

Function name:
finish print request

Core endpoint(s):
- `POST /printshops/requests/{id}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
- A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
- The consumer balance is high enough for a ProxyPrint-balance payment. This can be satisfied by directly setting the consumer `Money` balance, or by calling `POST /paypal/ipn/consumer/{consumerID}` with IPN fields including `payment_status=Completed` and `mc_gross` at least equal to the submitted cost.
- The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.
- The assigned print request is already `IN_PROGRESS`. This can be satisfied by directly setting `PrintRequest.status = IN_PROGRESS` and preserving its print-shop assignment, or by authenticating as the employee and calling `POST /printshops/requests/{id}` once while the request is `PENDING`.

Successful execution:
- Result:
  This function changes a print request from `IN_PROGRESS` to `FINISHED`, records the finish timestamp, and notifies the consumer that it is ready for pickup.
- Invocation:
  Step 1: `POST /printshops/requests/{id}` with the required request id, authenticated principal, and request body values
- Constraints:
  `{id}` is the `printRequestID` from `POST /consumer/budget`. `POST /printshops/requests/{id}` must execute first to move the request to `IN_PROGRESS`; `POST /printshops/requests/{id}` then moves it to `FINISHED`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
    - A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
    - A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
    - The consumer balance is high enough for a ProxyPrint-balance payment. This can be satisfied by directly setting the consumer `Money` balance, or by calling `POST /paypal/ipn/consumer/{consumerID}` with IPN fields including `payment_status=Completed` and `mc_gross` at least equal to the submitted cost.
    - The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.
    - The failing request is made against state that violates this condition: Request has not been started. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /printshops/requests/{id}`
  - Why this fails:
    With status still `PENDING`, the endpoint performs the start-processing function instead of finishing.
  - Intentionally violated constraints:
    The prior `PENDING` to `IN_PROGRESS` transition is omitted.

Endpoint coverage:
- Covers:
  `POST /printshops/requests/{id}`
- Distinct meaning:
  Second status transition: `IN_PROGRESS` to `FINISHED`.

### Function 60: mark print request lifted

Function name:
mark print request lifted

Core endpoint(s):
- `POST /printshops/requests/{id}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
- A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
- The consumer balance is high enough for a ProxyPrint-balance payment. This can be satisfied by directly setting the consumer `Money` balance, or by calling `POST /paypal/ipn/consumer/{consumerID}` with IPN fields including `payment_status=Completed` and `mc_gross` at least equal to the submitted cost.
- The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.
- The assigned print request has been started and is `IN_PROGRESS`. This can be satisfied by directly setting `PrintRequest.status = IN_PROGRESS`, or by calling `POST /printshops/requests/{id}` once while the request is `PENDING`.
- The assigned print request has been finished and is `FINISHED`. This can be satisfied by directly setting `PrintRequest.status = FINISHED`, or by calling `POST /printshops/requests/{id}` again while the request is `IN_PROGRESS`.

Successful execution:
- Result:
  This function changes a finished request to `LIFTED`, records delivery employee/timestamp, and transfers the print shop’s share for ProxyPrint balance payments.
- Invocation:
  Step 1: `POST /printshops/requests/{id}` with the required request id, authenticated principal, and request body values
- Constraints:
  `{id}` is the `printRequestID` from `POST /consumer/budget`. `POST /printshops/requests/{id}` and `POST /printshops/requests/{id}` must move the request to `FINISHED` before `POST /printshops/requests/{id}`. For PayPal-paid requests, `POST /printshops/requests/{id}` additionally depends on PayPal payout success; the sequence above uses ProxyPrint balance to avoid that external dependency.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
    - A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
    - A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
    - The consumer balance is high enough for a ProxyPrint-balance payment. This can be satisfied by directly setting the consumer `Money` balance, or by calling `POST /paypal/ipn/consumer/{consumerID}` with IPN fields including `payment_status=Completed` and `mc_gross` at least equal to the submitted cost.
    - The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.
    - The failing request is made against state that violates this condition: Request is not finished. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /printshops/requests/{id}`
  - Why this fails:
    With status `PENDING`, the endpoint performs a different status transition.
  - Intentionally violated constraints:
    The required transitions to `IN_PROGRESS` and `FINISHED` are omitted.

Endpoint coverage:
- Covers:
  `POST /printshops/requests/{id}`
- Distinct meaning:
  Third status transition: `FINISHED` to `LIFTED`.

### Function 61: cancel print-shop pending request

Function name:
cancel print-shop pending request

Core endpoint(s):
- `POST /printshops/requests/cancel/{id}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
- A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
- The consumer balance is high enough for a ProxyPrint-balance payment. This can be satisfied by directly setting the consumer `Money` balance, or by calling `POST /paypal/ipn/consumer/{consumerID}` with IPN fields including `payment_status=Completed` and `mc_gross` at least equal to the submitted cost.
- The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.

Successful execution:
- Result:
  This function lets an employee cancel a pending request assigned to their print shop, remove it from the print shop and consumer, and notify the consumer with a motive.
- Invocation:
  Step 1: `POST /printshops/requests/cancel/{id}` with the required request id, authenticated principal, and request body values
- Constraints:
  `{id}` in `POST /printshops/requests/cancel/{id}` is the `printRequestID` from `POST /consumer/budget`. The request body is the cancellation motive. `POST /printshops/requests/cancel/{id}` authenticates as the employee from `POST /printshops/{printShopID}/employees` and only succeeds while status is `PENDING`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
    - A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
    - A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
    - The consumer balance is high enough for a ProxyPrint-balance payment. This can be satisfied by directly setting the consumer `Money` balance, or by calling `POST /paypal/ipn/consumer/{consumerID}` with IPN fields including `payment_status=Completed` and `mc_gross` at least equal to the submitted cost.
    - The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.
    - The assigned print request has already been advanced by `POST /printshops/requests/{id}`. This can also be satisfied by directly setting the corresponding `PrintRequest.status` and employee timestamp fields in the database.
    - The failing request is made against state that violates this condition: Request is not pending. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `POST /printshops/requests/cancel/{id}`
  - Why this fails:
    `POST /printshops/requests/{id}` changes status to `IN_PROGRESS`; cancellation only succeeds for `PENDING`.
  - Intentionally violated constraints:
    `POST /printshops/requests/cancel/{id}` attempts cancellation after processing has started.

Endpoint coverage:
- Covers:
  `POST /printshops/requests/cancel/{id}`
- Distinct meaning:
  Employee cancels an assigned pending request.

### Function 62: list print-shop finished requests

Function name:
list print-shop finished requests

Core endpoint(s):
- `GET /printshops/satisfied`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.

Successful execution:
- Result:
  This function returns finished requests for the authenticated employee’s print shop.
- Invocation:
  Step 1: `GET /printshops/satisfied` with required path/query/body/form/header values
- Constraints:
  `GET /printshops/satisfied` authenticates as the employee from `POST /printshops/{printShopID}/employees`. The list can be empty. Implementation uses `EmployeeDAO` even though `ROLE_MANAGER` is also annotated.

Failure or exceptional branches:
- No structured branch exists for a non-employee principal; it can fail before returning JSON.

Endpoint coverage:
- Covers:
  `GET /printshops/satisfied`
- Distinct meaning:
  Print-shop view of requests ready for pickup.

### Function 63: list print-shop lifted request history

Function name:
list print-shop lifted request history

Core endpoint(s):
- `GET /printshops/history`

Preconditions:
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.

Successful execution:
- Result:
  This function returns lifted request history for the authenticated employee’s print shop.
- Invocation:
  Step 1: `GET /printshops/history` with required path/query/body/form/header values
- Constraints:
  `GET /printshops/history` authenticates as the employee from `POST /printshops/{printShopID}/employees`. The list can be empty.

Failure or exceptional branches:
- No structured branch exists for a non-employee principal; it can fail before returning JSON.

Endpoint coverage:
- Covers:
  `GET /printshops/history`
- Distinct meaning:
  Print-shop request history after delivery.

### Function 64: download assigned document PDF

Function name:
download assigned document PDF

Core endpoint(s):
- `GET /documents/{id}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
- A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
- The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
- The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
- An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
- A printing schema exists for consumer `{consumerID}`. This can be satisfied by directly inserting a `PrintingSchema` row and linking it to the consumer, or by calling `POST /consumer/{consumerID}/printingschemas` with a body containing `name`, `paperSpecs`, and optional `bindingSpecs`/`coverSpecs`; the response supplies `{printingSchemaID}`.
- A draft print request exists for the authenticated consumer with uploaded PDF documents, document specs, selected `{printShopID}`, and calculated budgets. This can be satisfied by directly inserting the linked `PrintRequest`, `Document`, and `DocumentSpec` rows, or by calling `POST /consumer/budget` with multipart PDF files and a `printRequest` JSON part whose `printshops` array contains `{printShopID}` and whose file specs reference `{printingSchemaID}`; the response supplies `{printRequestID}` and budget values.
- The consumer balance is high enough for a ProxyPrint-balance payment. This can be satisfied by directly setting the consumer `Money` balance, or by calling `POST /paypal/ipn/consumer/{consumerID}` with IPN fields including `payment_status=Completed` and `mc_gross` at least equal to the submitted cost.
- The draft print request has been submitted to `{printShopID}`. This can be satisfied by directly linking `{printRequestID}` to the print shop and setting its cost, payment type, and status, or by calling `POST /consumer/printrequest/{printRequestID}/submit` with body fields `printshopID`, `budget`, and `paymentMethod`; for ProxyPrint balance payment the status becomes `PENDING`, while other payment methods await PayPal confirmation.
- The document IDs for the assigned print request are known. This can be satisfied by directly reading the `Document` rows linked to `{printRequestID}`, or by authenticating as the employee and calling `GET /printshops/requests/{id}` with `{id} = {printRequestID}`; a returned document `id` must be reused as `{documentId}`.

Successful execution:
- Result:
  This function returns a PDF file for a document belonging to a request assigned to the authenticated employee’s print shop.
- Invocation:
  Step 1: `GET /documents/{id}` with the required document path values, multipart files, or budget body values
- Constraints:
  `GET /printshops/requests/{id}` uses the `printRequestID` from `POST /consumer/budget` and returns document IDs. `{id}` in `GET /documents/{id}` must be a document ID from `GET /printshops/requests/{id}`. `GET /documents/{id}` authenticates as the employee from `POST /printshops/{printShopID}/employees`. The document’s print request must belong to that employee’s print shop.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An admin account exists with `ROLE_ADMIN` credentials. This can be satisfied by directly inserting an `Admin`/`User` row with `username = {adminUsername}`, `password = {adminPassword}`, and `email`, or by calling `POST /admin/register` with an admin JSON body; the same credentials must authenticate admin-only endpoints.
    - A pending print-shop registration request exists. This can be satisfied by directly inserting a `RegisterRequest` row with `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`, or by calling `POST /request/register` with that JSON body; the response supplies `{registrationRequestId}`.
    - The pending registration request has been accepted, creating a manager account and print shop linked to that manager. This can be satisfied by directly inserting the linked `Manager` and `PrintShop` rows and deleting/marking the request consumed, or by authenticating as the admin and calling `POST /request/accept/{id}` with `{id} = {registrationRequestId}`; the manager credentials are copied from the registration request.
    - The target `{printShopID}` is known for the manager-created print shop. This can be satisfied by directly reading the `PrintShop` row linked to the manager, or by authenticating with the manager credentials and calling `GET /printshop`; the returned print-shop `id` must be reused in path or body values that reference the shop.
    - An employee account exists for `{printShopID}`. This can be satisfied by directly inserting an `Employee` row linked to the print shop, or by authenticating as the manager and calling `POST /printshops/{printShopID}/employees` with JSON fields `name`, `username`, and `password`; the response supplies `{employeeID}` and the employee credentials for employee-scoped endpoints.
    - The failing request is made against state that violates this condition: Document does not exist. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /documents/{id}`
  - Why this fails:
    `documents.findOne(id)` returns null and the controller returns `404`.
  - Intentionally violated constraints:
    The print-request upload sequence producing a document ID is omitted.

Endpoint coverage:
- Covers:
  `GET /documents/{id}`
- Distinct meaning:
  Employee retrieves the actual PDF file.

### Function 65: send notification to consumer

Function name:
send notification to consumer

Core endpoint(s):
- `POST /consumer/{id}/notify`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function creates a notification for a consumer and pushes it to active SSE subscribers.
- Invocation:
  Step 1: `POST /consumer/{id}/notify` with the required notification path values and message/read/delete parameters
- Constraints:
  `{id}` must be the consumer ID from `POST /consumer/register`. `POST /consumer/{id}/notify` uses request parameter `message`.

Failure or exceptional branches:
- No explicit null handling exists for a missing consumer ID; using an ID not produced by the corresponding endpoint can cause a null dereference.

Endpoint coverage:
- Covers:
  `POST /consumer/{id}/notify`
- Distinct meaning:
  Direct notification creation for a consumer.

### Function 66: subscribe to notification stream

Function name:
subscribe to notification stream

Core endpoint(s):
- `GET /consumer/subscribe`
- `HEAD /consumer/subscribe`
- `POST /consumer/subscribe`
- `PUT /consumer/subscribe`
- `DELETE /consumer/subscribe`
- `OPTIONS /consumer/subscribe`
- `PATCH /consumer/subscribe`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function opens a server-sent-events stream for a consumer.
- Invocation:
  Step 1: `GET /consumer/subscribe` with request parameters `username` and `password`
  Step 2: `HEAD /consumer/subscribe` only when using this documented HTTP method variant for the same core endpoint capability
  Step 3: `POST /consumer/subscribe` only when using this documented HTTP method variant for the same core endpoint capability
  Step 4: `PUT /consumer/subscribe` only when using this documented HTTP method variant for the same core endpoint capability
  Step 5: `DELETE /consumer/subscribe` only when using this documented HTTP method variant for the same core endpoint capability
  Step 6: `OPTIONS /consumer/subscribe` only when using this documented HTTP method variant for the same core endpoint capability
  Step 7: `PATCH /consumer/subscribe` only when using this documented HTTP method variant for the same core endpoint capability
- Constraints:
  `GET /consumer/subscribe` must provide `username` and `password` matching `POST /consumer/register`. Source code does not restrict HTTP method, so Swagger exposes GET, HEAD, POST, PUT, DELETE, OPTIONS, and PATCH for the same subscription logic.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failing request is made against state that violates this condition: Consumer username is unknown. The endpoint `POST /consumer/register` is intentionally omitted, called with unrelated values, or its response value is not reused as required. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /consumer/subscribe`
  - Why this fails:
    `consumers.findByUsername(username)` returns null and the endpoint returns `404`.
  - Intentionally violated constraints:
    `POST /consumer/register` is omitted.
- Branch 2:
  - Preconditions:
    - A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
    - The failing request is made against state that violates this condition: Password is incorrect. This can be satisfied by direct database setup or omission that produces the same missing, invalid, duplicate, mismatched, unauthorized, or cross-scoped state.
  - Failure endpoint:
    `GET /consumer/subscribe`
  - Why this fails:
    The username exists, but `consumer.getPassword().equals(password)` is false, so the endpoint returns `401`.
  - Intentionally violated constraints:
    `GET /consumer/subscribe` reuses the username from `POST /consumer/register` but sends a different password.

Endpoint coverage:
- Covers:
  `GET /consumer/subscribe`
  `HEAD /consumer/subscribe`
  `POST /consumer/subscribe`
  `PUT /consumer/subscribe`
  `DELETE /consumer/subscribe`
  `OPTIONS /consumer/subscribe`
  `PATCH /consumer/subscribe`
- Distinct meaning:
  All documented methods map to the same SSE subscription function because the implementation omits a method restriction.

### Function 67: list consumer notifications

Function name:
list consumer notifications

Core endpoint(s):
- `GET /consumer/notifications`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function returns notifications for the authenticated consumer.
- Invocation:
  Step 1: `GET /consumer/notifications` with the required notification path values and message/read/delete parameters
- Constraints:
  `GET /consumer/notifications` authenticates as the consumer from `POST /consumer/register`. The list can be empty.

Failure or exceptional branches:
- No reproducible controller-level failure branch is exposed through documented endpoints other than missing/invalid authentication.

Endpoint coverage:
- Covers:
  `GET /consumer/notifications`
- Distinct meaning:
  Consumer reads their notification list.

### Function 68: mark notification read

Function name:
mark notification read

Core endpoint(s):
- `PUT /notifications/{notificationId}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- A notification exists for consumer `{id}`. This can be satisfied by directly inserting a `Notification` linked to the consumer, or by calling `POST /consumer/{id}/notify` with request parameter `message`; the notification can later be found through the consumer notification list.
- The target `{notificationId}` is known. This can be satisfied by directly reading the consumer's `Notification` rows, or by authenticating as the consumer and calling `GET /consumer/notifications`; one returned notification `id` must be reused.

Successful execution:
- Result:
  This function marks a single notification as read.
- Invocation:
  Step 1: `PUT /notifications/{notificationId}` with the required notification path values and message/read/delete parameters
- Constraints:
  `{id}` in `POST /consumer/{id}/notify` is the consumer ID from `POST /consumer/register`. `GET /consumer/notifications` authenticates as that consumer and returns notification IDs. `{notificationId}` in `PUT /notifications/{notificationId}` must be from `GET /consumer/notifications`. The implementation does not verify ownership in `PUT /notifications/{notificationId}`.

Failure or exceptional branches:
- No explicit null handling exists for a missing notification ID; using an ID not produced by the corresponding endpoint can cause a server exception.

Endpoint coverage:
- Covers:
  `PUT /notifications/{notificationId}`
- Distinct meaning:
  Marks one notification read.

### Function 69: delete notification

Function name:
delete notification

Core endpoint(s):
- `DELETE /notifications/{notificationId}`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.
- A notification exists for consumer `{id}`. This can be satisfied by directly inserting a `Notification` linked to the consumer, or by calling `POST /consumer/{id}/notify` with request parameter `message`; the notification can later be found through the consumer notification list.
- The target `{notificationId}` is known. This can be satisfied by directly reading the consumer's `Notification` rows, or by authenticating as the consumer and calling `GET /consumer/notifications`; one returned notification `id` must be reused.

Successful execution:
- Result:
  This function deletes a single notification by ID.
- Invocation:
  Step 1: `DELETE /notifications/{notificationId}` with the required notification path values and message/read/delete parameters
- Constraints:
  `{notificationId}` in `DELETE /notifications/{notificationId}` must be obtained from `GET /consumer/notifications`. The implementation deletes by ID and does not verify ownership.

Failure or exceptional branches:
- No explicit controller-level failure branch is implemented.

Endpoint coverage:
- Covers:
  `DELETE /notifications/{notificationId}`
- Distinct meaning:
  Deletes one notification.

### Function 70: mark all consumer notifications read

Function name:
mark all consumer notifications read

Core endpoint(s):
- `PUT /consumer/{username}/notifications`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function marks all notifications for a username as read.
- Invocation:
  Step 1: `PUT /consumer/{username}/notifications` with the required notification path values and message/read/delete parameters
- Constraints:
  `{username}` should be the username created by `POST /consumer/register`. Endpoint is secured with `ROLE_USER`, but implementation does not check that the path username matches the authenticated principal.

Failure or exceptional branches:
- No explicit null handling exists for an unknown username; using a username not produced by the corresponding endpoint can cause a null dereference.

Endpoint coverage:
- Covers:
  `PUT /consumer/{username}/notifications`
- Distinct meaning:
  Bulk mark notifications read.

### Function 71: delete all consumer notifications

Function name:
delete all consumer notifications

Core endpoint(s):
- `DELETE /consumer/{username}/notifications`

Preconditions:
- A consumer account exists with `ROLE_USER` credentials. This can be satisfied by directly inserting a `Consumer`/`User` row with `username = {consumerUsername}`, `password = {consumerPassword}`, `email`, `name`, `latitude`, and `longitude`, or by calling `POST /consumer/register` with those request parameters; the response supplies `{consumerID}` when a consumer path value is needed.

Successful execution:
- Result:
  This function removes all notifications for a username.
- Invocation:
  Step 1: `DELETE /consumer/{username}/notifications` with the required notification path values and message/read/delete parameters
- Constraints:
  `{username}` should be the username created by `POST /consumer/register`. Endpoint is secured with `ROLE_USER`, but implementation does not check that the path username matches the authenticated principal.

Failure or exceptional branches:
- No explicit null handling exists for an unknown username; using a username not produced by the corresponding endpoint can cause a null dereference.

Endpoint coverage:
- Covers:
  `DELETE /consumer/{username}/notifications`
- Distinct meaning:
  Bulk delete notifications.

### Auxiliary endpoints

These endpoints are present in `proxyprint.json` but are not implemented by project application controllers in `src`; they appear to be generated Spring Boot Actuator or framework error endpoints, so they are auxiliary rather than domain functions:

- `GET /autoconfig`, `GET /autoconfig.json`
- `GET /beans`, `GET /beans.json`
- `GET /configprops`, `GET /configprops.json`
- `GET /dump`, `GET /dump.json`
- `GET /env`, `GET /env.json`, `GET /env/{name}`
- `GET /health`, `HEAD /health`, `POST /health`, `PUT /health`, `DELETE /health`, `OPTIONS /health`, `PATCH /health`
- `GET /health.json`, `HEAD /health.json`, `POST /health.json`, `PUT /health.json`, `DELETE /health.json`, `OPTIONS /health.json`, `PATCH /health.json`
- `GET /info`, `GET /info.json`
- `GET /mappings`, `GET /mappings.json`
- `GET /metrics`, `GET /metrics.json`, `GET /metrics/{name}`
- `GET /trace`, `GET /trace.json`
- `GET /error`, `HEAD /error`, `POST /error`, `PUT /error`, `DELETE /error`, `OPTIONS /error`, `PATCH /error`

Discrepancy note: Swagger also reports generic `401`, `403`, and `404` responses for many application endpoints. I only listed failure branches that are visible in controller/model logic, not every generic framework security response.
