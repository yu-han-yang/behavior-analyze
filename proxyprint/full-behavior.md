Analyzed only `proxyprint.json` and `src`.

### Behavior 1: return welcome message

Behavior name:
return welcome message

Successful execution:
- Result:
  This behavior returns the API root welcome JSON message.
- Endpoint sequence:
  Step 1: `GET /`
- Constraints:
  No prerequisite resource state is required.

Failure or exceptional branches:
- No implementation-specific failure branch is visible in `src`.

Endpoint coverage:
- Covers:
  `GET /`
- Distinct meaning:
  Root discovery/welcome endpoint.

### Behavior 2: answer CORS preflight

Behavior name:
answer CORS preflight

Successful execution:
- Result:
  This behavior returns `204 No Content` for wildcard OPTIONS requests.
- Endpoint sequence:
  Step 1: `OPTIONS /*`
- Constraints:
  No prerequisite resource state is required.

Failure or exceptional branches:
- No implementation-specific failure branch is visible in `src`.

Endpoint coverage:
- Covers:
  `OPTIONS /*`
- Distinct meaning:
  Browser preflight support.

### Behavior 3: verify authenticated user access

Behavior name:
verify authenticated user access

Successful execution:
- Result:
  This behavior returns a protected message to an authenticated consumer user.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `GET /api/secured`
- Constraints:
  Step 1 must create a user with `ROLE_USER`; Step 2 must authenticate as that same username and password.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The request is not authenticated as a `ROLE_USER`.
  - Endpoint group:
    Step 1: `GET /api/secured`
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

### Behavior 4: log in user

Behavior name:
log in user

Successful execution:
- Result:
  This behavior validates submitted credentials and returns `success: true` plus the concrete user object.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /login`
- Constraints:
  Step 2 form/query parameters `username` and `password` must match the consumer created by Step 1. The implementation may also include `externalURL` for consumers outside the `heroku` profile.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Required login fields are absent.
  - Endpoint group:
    Step 1: `POST /login`
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    The implementation returns `success: false` when `username` or `password` is missing.
  - Intentionally violated constraints:
    `username` and/or `password` are omitted.
- Branch 2:
  - Unsatisfied condition:
    Password does not match.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /login`
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    The user exists, but `user.getPassword().equals(password)` is false.
  - Intentionally violated constraints:
    Step 2 reuses the username from Step 1 but sends a different password.
- Branch 3:
  - Unsatisfied condition:
    User does not exist.
  - Endpoint group:
    Step 1: `POST /login`
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

### Behavior 5: register admin

Behavior name:
register admin

Successful execution:
- Result:
  This behavior creates an admin account.
- Endpoint sequence:
  Step 1: `POST /admin/register`
- Constraints:
  The request body is an `Admin` object. The implementation saves it directly; a usable admin needs `username`, `password`, and `email`, although Swagger omits `password` from the visible schema.

Failure or exceptional branches:
- No explicit duplicate or validation branch is implemented; persistence errors may occur outside controller logic.

Endpoint coverage:
- Covers:
  `POST /admin/register`
- Distinct meaning:
  Creates an admin user with `ROLE_ADMIN`.

### Behavior 6: seed demo dataset

Behavior name:
seed demo dataset

Successful execution:
- Result:
  This behavior fills the database with hardcoded demo admins, consumers, print shops, managers, registration requests, price tables, print requests, and reviews.
- Endpoint sequence:
  Step 1: `POST /admin/seed`
- Constraints:
  No documented input is required. This is a developer/demo data endpoint and creates broad state, not a minimal business object.

Failure or exceptional branches:
- No implementation-specific failure branch is visible in `src`.

Endpoint coverage:
- Covers:
  `POST /admin/seed`
- Distinct meaning:
  Bulk demo data creation.

### Behavior 7: seed many consumers

Behavior name:
seed many consumers

Successful execution:
- Result:
  This behavior creates 1000 hardcoded consumer accounts with randomized coordinates and a preset balance.
- Endpoint sequence:
  Step 1: `POST /admin/useed`
- Constraints:
  No documented input is required.

Failure or exceptional branches:
- No implementation-specific failure branch is visible in `src`.

Endpoint coverage:
- Covers:
  `POST /admin/useed`
- Distinct meaning:
  Bulk consumer-account seeding.

### Behavior 8: list print shops as admin

Behavior name:
list print shops as admin

Successful execution:
- Result:
  This behavior returns all registered print shops to an authenticated admin.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `GET /admin/printshops`
- Constraints:
  Step 2 must authenticate as the admin created by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller is not authenticated as an admin.
  - Endpoint group:
    Step 1: `GET /admin/printshops`
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

### Behavior 9: register consumer

Behavior name:
register consumer

Successful execution:
- Result:
  This behavior creates a consumer account and returns the created consumer with `success: true`.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
- Constraints:
  The implementation reads request parameters `username`, `password`, `email`, `name`, `latitude`, and `longitude`. `username` must not already exist.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Username already exists.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /consumer/register`
  - Failure endpoint:
    `POST /consumer/register`
  - Why this fails:
    Step 2 finds an existing consumer by the same `username` and returns `success: false`.
  - Intentionally violated constraints:
    Step 2 repeats the `username` created by Step 1.

Endpoint coverage:
- Covers:
  `POST /consumer/register`
- Distinct meaning:
  Consumer self-registration.

### Behavior 10: retrieve consumer profile

Behavior name:
retrieve consumer profile

Successful execution:
- Result:
  This behavior returns the authenticated consumer’s stored profile.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `GET /consumer/info`
- Constraints:
  Step 2 must authenticate as the consumer created by Step 1. Swagger shows a `principal` body, but the implementation uses the authenticated `Principal`, not a client body.

Failure or exceptional branches:
- No reproducible controller-level failure branch is exposed through documented endpoints other than missing/invalid authentication.

Endpoint coverage:
- Covers:
  `GET /consumer/info`
- Distinct meaning:
  Reads the current consumer’s account data.

### Behavior 11: update consumer profile

Behavior name:
update consumer profile

Successful execution:
- Result:
  This behavior updates the authenticated consumer’s name, username, password, and email.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `PUT /consumer/info/update`
- Constraints:
  Step 2 must authenticate as the consumer created by Step 1 and send a JSON `Consumer` body. This differs from Swagger, which only documents a `principal` body. The implementation compares and overwrites `name`, `username`, `password`, and `email`.

Failure or exceptional branches:
- No explicit controller-level validation branch is implemented beyond returning `success: false` if the authenticated principal cannot be resolved to a consumer.

Endpoint coverage:
- Covers:
  `PUT /consumer/info/update`
- Distinct meaning:
  Mutates current consumer profile fields.

### Behavior 12: retrieve consumer balance

Behavior name:
retrieve consumer balance

Successful execution:
- Result:
  This behavior returns the authenticated consumer’s balance.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `GET /consumer/balance`
- Constraints:
  Step 2 must authenticate as the consumer created by Step 1.

Failure or exceptional branches:
- No reproducible controller-level failure branch is exposed through documented endpoints other than missing/invalid authentication.

Endpoint coverage:
- Covers:
  `GET /consumer/balance`
- Distinct meaning:
  Reads current consumer balance.

### Behavior 13: request print-shop registration

Behavior name:
request print-shop registration

Successful execution:
- Result:
  This behavior creates a pending registration request for a new print shop and manager.
- Endpoint sequence:
  Step 1: `POST /request/register`
- Constraints:
  Body must contain `managerName`, `managerUsername`, `managerEmail`, `managerPassword`, `pShopAddress`, `pShopLatitude`, `pShopLongitude`, `pShopNIF`, and `pShopName`. The response contains the registration request `id`.

Failure or exceptional branches:
- No explicit controller-level validation branch is implemented.

Endpoint coverage:
- Covers:
  `POST /request/register`
- Distinct meaning:
  Creates a pending print-shop registration request.

### Behavior 14: list pending print-shop registration requests

Behavior name:
list pending print-shop registration requests

Successful execution:
- Result:
  This behavior returns registration requests whose `accepted` flag is false.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `GET /requests/pending`
- Constraints:
  Step 2 must authenticate as the admin created by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Caller is not an admin.
  - Endpoint group:
    Step 1: `GET /requests/pending`
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

### Behavior 15: accept print-shop registration request

Behavior name:
accept print-shop registration request

Successful execution:
- Result:
  This behavior turns a pending registration request into a manager account and a print-shop record, links them, deletes the request, and sends an acceptance email.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
- Constraints:
  Step 3 must authenticate as the admin from Step 1. `{id}` in Step 3 must be the registration request `id` returned by Step 2. The created manager credentials are copied from Step 2’s `managerUsername` and `managerPassword`; the print-shop fields are copied from Step 2’s `pShop...` fields.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Registration request does not exist.
  - Endpoint group:
    Step 1: `POST /admin/register`
    Step 2: `POST /request/accept/{id}`
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

### Behavior 16: reject print-shop registration request

Behavior name:
reject print-shop registration request

Successful execution:
- Result:
  This behavior deletes a pending registration request and sends a rejection email with a motive.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/reject/{printRequestID}`
- Constraints:
  Step 3 must authenticate as the admin from Step 1. `{printRequestID}` in Step 3 must be the registration request `id` returned by Step 2. The request body is the rejection motive string.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Registration request does not exist.
  - Endpoint group:
    Step 1: `POST /admin/register`
    Step 2: `POST /request/reject/{printRequestID}`
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

### Behavior 17: list public print shops

Behavior name:
list public print shops

Successful execution:
- Result:
  This behavior returns all print shops visible through the public print-shop listing.
- Endpoint sequence:
  Step 1: `GET /printshops`
- Constraints:
  No specific print shop must exist; the endpoint can return an empty collection.

Failure or exceptional branches:
- No implementation-specific failure branch is visible in `src`.

Endpoint coverage:
- Covers:
  `GET /printshops`
- Distinct meaning:
  Public/global print-shop discovery.

### Behavior 18: retrieve print shop by id

Behavior name:
retrieve print shop by id

Successful execution:
- Result:
  This behavior retrieves a specific print shop by ID.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `GET /printshops/{id}`
- Constraints:
  Step 3 uses the request `id` from Step 2 and authenticates as the admin from Step 1. Step 4 authenticates as the manager credentials supplied in Step 2 and returns the new print shop. `{id}` in Step 5 must be the print-shop ID from Step 4.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `GET /printshops/{id}`
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

### Behavior 19: find nearest print shops

Behavior name:
find nearest print shops

Successful execution:
- Result:
  This behavior returns print shops sorted by distance from the supplied coordinates.
- Endpoint sequence:
  Step 1: `GET /printshops/nearest`
- Constraints:
  Query parameters `latitude` and `longitude` must both be present and parseable as doubles. No specific print shop must exist.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Missing `latitude` or `longitude`.
  - Endpoint group:
    Step 1: `GET /printshops/nearest`
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

### Behavior 20: retrieve print-shop price table

Behavior name:
retrieve print-shop price table

Successful execution:
- Result:
  This behavior returns a print shop’s paper, ring, stapling, and cover prices.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `GET /printshops/{id}/pricetable`
- Constraints:
  Step 5 authenticates as a `ROLE_MANAGER` or `ROLE_USER`. `{id}` in Step 5 must be the print-shop ID returned by Step 4.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `GET /printshops/{id}/pricetable`
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

### Behavior 21: retrieve manager print shop

Behavior name:
retrieve manager print shop

Successful execution:
- Result:
  This behavior returns the print shop assigned to the authenticated manager.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
- Constraints:
  Step 3 must use the registration request `id` from Step 2. Step 4 must authenticate with the manager credentials submitted in Step 2.

Failure or exceptional branches:
- No documented endpoint creates a manager without a print shop; the visible failure branch only occurs if the authenticated manager has no linked print shop.

Endpoint coverage:
- Covers:
  `GET /printshop`
- Distinct meaning:
  Manager-scoped print-shop lookup.

### Behavior 22: retrieve print-shop statistics

Behavior name:
retrieve print-shop statistics

Successful execution:
- Result:
  This behavior returns counts of pending, in-progress, and finished requests, employee count, and calculated print-shop profit for the authenticated manager’s print shop.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshops/stats`
- Constraints:
  Step 4 authenticates as the manager created by Step 3 using credentials from Step 2.

Failure or exceptional branches:
- No documented endpoint creates a manager without a print shop; otherwise the controller returns `success: false` if the manager or print shop cannot be resolved.

Endpoint coverage:
- Covers:
  `GET /printshops/stats`
- Distinct meaning:
  Manager dashboard statistics.

### Behavior 23: list print-shop employees

Behavior name:
list print-shop employees

Successful execution:
- Result:
  This behavior returns employees assigned to a print shop.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `GET /printshops/{printShopID}/employees`
- Constraints:
  Step 5 authenticates as the manager created by Step 3. `{printShopID}` must be the ID returned by Step 4.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `POST /admin/register`
    Step 2: `GET /printshops/{printShopID}/employees`
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

### Behavior 24: add print-shop employee

Behavior name:
add print-shop employee

Successful execution:
- Result:
  This behavior creates an employee account attached to a print shop.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{printShopID}/employees`
- Constraints:
  Step 5 authenticates as the manager created by Step 3. `{printShopID}` must be from Step 4. The body must contain employee `name`, `username`, and `password`; the response returns the employee `id`. Swagger omits this request body, but the implementation requires it.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Employee username already exists.
  - Endpoint group:
    Step 1: `POST /admin/register`
    Step 2: `POST /request/register`
    Step 3: `POST /request/accept/{id}`
    Step 4: `GET /printshop`
    Step 5: `POST /printshops/{printShopID}/employees`
    Step 6: `POST /printshops/{printShopID}/employees`
  - Failure endpoint:
    `POST /printshops/{printShopID}/employees`
  - Why this fails:
    Step 6 finds the employee username created in Step 5 and returns `success: false`.
  - Intentionally violated constraints:
    Step 6 repeats Step 5’s `username`.

Endpoint coverage:
- Covers:
  `POST /printshops/{printShopID}/employees`
- Distinct meaning:
  Manager creates an employee account.

### Behavior 25: edit print-shop employee

Behavior name:
edit print-shop employee

Successful execution:
- Result:
  This behavior updates an employee’s name, username, and password.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{printShopID}/employees`
  Step 6: `PUT /printshops/{printShopID}/employees`
- Constraints:
  Step 6 authenticates as the manager. `{printShopID}` comes from Step 4. The Step 6 body must include the employee `id` returned by Step 5 plus new `name`, `username`, and `password`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Employee ID does not exist.
  - Endpoint group:
    Step 1: `POST /admin/register`
    Step 2: `POST /request/register`
    Step 3: `POST /request/accept/{id}`
    Step 4: `GET /printshop`
    Step 5: `PUT /printshops/{printShopID}/employees`
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

### Behavior 26: delete print-shop employee

Behavior name:
delete print-shop employee

Successful execution:
- Result:
  This behavior deletes an employee account by ID.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{printShopID}/employees`
  Step 6: `DELETE /printshops/{printShopID}/employees/{employeeID}`
- Constraints:
  `{printShopID}` comes from Step 4. `{employeeID}` comes from Step 5. The implementation only checks whether the employee exists; it does not verify the employee belongs to `{printShopID}`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Employee ID does not exist.
  - Endpoint group:
    Step 1: `POST /admin/register`
    Step 2: `POST /request/register`
    Step 3: `POST /request/accept/{id}`
    Step 4: `GET /printshop`
    Step 5: `DELETE /printshops/{printShopID}/employees/{employeeID}`
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

### Behavior 27: add paper price rows

Behavior name:
add paper price rows

Successful execution:
- Result:
  This behavior inserts paper-copy price rows into a print shop’s price table.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{id}/pricetable/papers`
- Constraints:
  `{id}` in Step 5 is the print-shop ID from Step 4. Body is `PaperTableItem` with `colors`, `infLim`, `supLim`, and any non-default `priceA4SIMPLEX`, `priceA4DUPLEX`, `priceA3SIMPLEX`, `priceA3DUPLEX`. The implementation writes map entries keyed by format, side, color, and page range.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `POST /printshops/{id}/pricetable/papers`
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

### Behavior 28: edit paper price rows

Behavior name:
edit paper price rows

Successful execution:
- Result:
  This behavior overwrites paper-copy price rows in a print shop’s price table.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{id}/pricetable/papers`
  Step 6: `PUT /printshops/{id}/pricetable/papers`
- Constraints:
  `{id}` in Steps 5 and 6 must be the same print-shop ID from Step 4. Step 6 uses a `PaperTableItem` with matching key fields for the rows being changed. Implementation logic is the same upsert operation as POST.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `PUT /printshops/{id}/pricetable/papers`
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

### Behavior 29: delete paper price rows

Behavior name:
delete paper price rows

Successful execution:
- Result:
  This behavior removes paper-copy price rows from a print shop’s price table.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{id}/pricetable/papers`
  Step 6: `POST /printshops/{id}/pricetable/deletepaper`
- Constraints:
  `{id}` in Steps 5 and 6 must be the same print-shop ID. Step 6 body must describe the same paper item keys inserted in Step 5 so the corresponding map entries are removed.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `POST /printshops/{id}/pricetable/deletepaper`
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

### Behavior 30: add ring binding price

Behavior name:
add ring binding price

Successful execution:
- Result:
  This behavior inserts a ring-binding price into a print shop’s price table.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{id}/pricetable/rings`
- Constraints:
  `{id}` comes from Step 4. Body is `RingTableItem` with `ringType`, `infLim`, `supLim`, and `price`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `POST /printshops/{id}/pricetable/rings`
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

### Behavior 31: edit ring binding price

Behavior name:
edit ring binding price

Successful execution:
- Result:
  This behavior overwrites an existing ring-binding price.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{id}/pricetable/rings`
  Step 6: `PUT /printshops/{id}/pricetable/rings`
- Constraints:
  Step 6 must use the same `{id}` and ring key fields as Step 5 to replace that price. Implementation logic is an upsert.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `PUT /printshops/{id}/pricetable/rings`
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

### Behavior 32: delete ring binding price

Behavior name:
delete ring binding price

Successful execution:
- Result:
  This behavior removes a ring-binding price from the price table.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{id}/pricetable/rings`
  Step 6: `POST /printshops/{id}/pricetable/deletering`
- Constraints:
  Step 6 must use the same print-shop ID and ring key fields inserted in Step 5. The delete endpoint converts presentation ring type strings before removing the generated key.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `POST /printshops/{id}/pricetable/deletering`
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

### Behavior 33: add cover price

Behavior name:
add cover price

Successful execution:
- Result:
  This behavior inserts cover prices into the print shop’s price table.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{id}/pricetable/covers`
- Constraints:
  `{id}` comes from Step 4. Body is `CoverTableItem` with `coverType`, `priceA4`, and `priceA3`. The implementation converts it into one or more `CoverItem` keys.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `POST /printshops/{id}/pricetable/covers`
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

### Behavior 34: edit cover price

Behavior name:
edit cover price

Successful execution:
- Result:
  This behavior overwrites cover prices in the print shop’s price table.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{id}/pricetable/covers`
  Step 6: `PUT /printshops/{id}/pricetable/covers`
- Constraints:
  Step 6 uses the same print-shop ID and cover key fields as Step 5. Implementation logic is the same upsert operation as POST.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `PUT /printshops/{id}/pricetable/covers`
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

### Behavior 35: delete cover price

Behavior name:
delete cover price

Successful execution:
- Result:
  This behavior removes cover prices from the print shop’s price table.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{id}/pricetable/covers`
  Step 6: `POST /printshops/{id}/pricetable/deletecover`
- Constraints:
  Step 6 must use the same print-shop ID and cover key fields inserted in Step 5.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `POST /printshops/{id}/pricetable/deletecover`
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

### Behavior 36: edit stapling price

Behavior name:
edit stapling price

Successful execution:
- Result:
  This behavior changes the price-table entry for stapling.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `PUT /printshops/{printShopID}/pricetable/editstapling`
- Constraints:
  `{printShopID}` comes from Step 4. The request body is the new price string, parsed as a float, and stored under key `BINDING,STAPLING,0,0`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `PUT /printshops/{printShopID}/pricetable/editstapling`
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

### Behavior 37: list print-shop reviews

Behavior name:
list print-shop reviews

Successful execution:
- Result:
  This behavior returns reviews for a specific print shop.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `GET /printshops/{id}/reviews`
- Constraints:
  `{id}` in Step 5 must be the print-shop ID from Step 4. Endpoint is secured for manager, employee, or user.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `GET /printshops/{id}/reviews`
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

### Behavior 38: add print-shop review

Behavior name:
add print-shop review

Successful execution:
- Result:
  This behavior creates a review for a print shop by the authenticated consumer and updates the shop’s average rating.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /printshops/{id}/reviews`
- Constraints:
  Step 6 authenticates as the consumer from Step 1. `{id}` comes from Step 5. Body must contain `review` text and `rating` string. The response contains the created review `id`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print shop does not exist.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /printshops/{id}/reviews`
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

### Behavior 39: edit print-shop review

Behavior name:
edit print-shop review

Successful execution:
- Result:
  This behavior changes the text and rating of an existing review owned by the authenticated consumer and recalculates the print-shop rating.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /printshops/{printShopId}/reviews`
  Step 7: `PUT /printshops/{printShopId}/reviews/{reviewId}`
- Constraints:
  `{printShopId}` comes from Step 5. `{reviewId}` comes from Step 6. Step 7 must authenticate as the same consumer from Step 1. Implementation reads `review` and `rating` as request parameters, not from a JSON body as Swagger implies.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Review does not exist.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /admin/register`
    Step 3: `POST /request/register`
    Step 4: `POST /request/accept/{id}`
    Step 5: `GET /printshop`
    Step 6: `PUT /printshops/{printShopId}/reviews/{reviewId}`
  - Failure endpoint:
    `PUT /printshops/{printShopId}/reviews/{reviewId}`
  - Why this fails:
    `reviews.findOne(reviewId)` returns null and the controller returns `404`.
  - Intentionally violated constraints:
    `{reviewId}` was not produced by `POST /printshops/{printShopId}/reviews`.
- Branch 2:
  - Unsatisfied condition:
    Authenticated consumer does not own the review.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /consumer/register`
    Step 3: `POST /admin/register`
    Step 4: `POST /request/register`
    Step 5: `POST /request/accept/{id}`
    Step 6: `GET /printshop`
    Step 7: `POST /printshops/{printShopId}/reviews`
    Step 8: `PUT /printshops/{printShopId}/reviews/{reviewId}`
  - Failure endpoint:
    `PUT /printshops/{printShopId}/reviews/{reviewId}`
  - Why this fails:
    Step 8 authenticates as the second consumer, but the review was created by the first consumer, so the controller returns `401`.
  - Intentionally violated constraints:
    The principal username in Step 8 differs from the review owner created in Step 7.

Endpoint coverage:
- Covers:
  `PUT /printshops/{printShopId}/reviews/{reviewId}`
- Distinct meaning:
  Consumer edits their own review.

### Behavior 40: delete print-shop review

Behavior name:
delete print-shop review

Successful execution:
- Result:
  This behavior removes an existing review owned by the authenticated consumer from a print shop.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /printshops/{printShopId}/reviews`
  Step 7: `DELETE /printshops/{printShopId}/reviews/{reviewId}`
- Constraints:
  `{printShopId}` comes from Step 5. `{reviewId}` comes from Step 6. Step 7 authenticates as the same consumer that created the review.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Authenticated consumer does not own the review.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /consumer/register`
    Step 3: `POST /admin/register`
    Step 4: `POST /request/register`
    Step 5: `POST /request/accept/{id}`
    Step 6: `GET /printshop`
    Step 7: `POST /printshops/{printShopId}/reviews`
    Step 8: `DELETE /printshops/{printShopId}/reviews/{reviewId}`
  - Failure endpoint:
    `DELETE /printshops/{printShopId}/reviews/{reviewId}`
  - Why this fails:
    Step 8 authenticates as a different consumer, so the controller returns `401`.
  - Intentionally violated constraints:
    The deleting principal differs from the review owner.

Endpoint coverage:
- Covers:
  `DELETE /printshops/{printShopId}/reviews/{reviewId}`
- Distinct meaning:
  Consumer deletes their own review.

### Behavior 41: list consumer printing schemas

Behavior name:
list consumer printing schemas

Successful execution:
- Result:
  This behavior returns the printing schemas associated with a consumer.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `GET /consumer/{consumerID}/printingschemas`
- Constraints:
  `{consumerID}` must be the consumer ID returned by Step 1. Endpoint is secured with `ROLE_USER`; implementation does not verify that the path ID matches the authenticated user.

Failure or exceptional branches:
- No explicit null handling exists for a missing consumer ID; using an ID not produced by Step 1 can result in a server exception rather than a structured failure response.

Endpoint coverage:
- Covers:
  `GET /consumer/{consumerID}/printingschemas`
- Distinct meaning:
  Reads a consumer’s saved print recipes/schemas.

### Behavior 42: add consumer printing schema

Behavior name:
add consumer printing schema

Successful execution:
- Result:
  This behavior creates a printing schema and associates it with a consumer.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /consumer/{consumerID}/printingschemas`
- Constraints:
  `{consumerID}` must be from Step 1. Body is `PrintingSchema` with at least `name` and `paperSpecs`; `bindingSpecs` and `coverSpecs` are optional strings. Response returns the new schema `id`.

Failure or exceptional branches:
- No explicit controller-level validation branch is implemented; a missing consumer can cause a null dereference after the schema is saved.

Endpoint coverage:
- Covers:
  `POST /consumer/{consumerID}/printingschemas`
- Distinct meaning:
  Consumer saves a print configuration.

### Behavior 43: edit consumer printing schema

Behavior name:
edit consumer printing schema

Successful execution:
- Result:
  This behavior updates a printing schema’s name, paper specs, binding specs, and cover specs.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /consumer/{consumerID}/printingschemas`
  Step 3: `PUT /consumer/{consumerID}/printingschemas/{printingSchemaID}`
- Constraints:
  `{consumerID}` comes from Step 1. `{printingSchemaID}` comes from Step 2. Step 3 body supplies replacement `name`, `paperSpecs`, `bindingSpecs`, and `coverSpecs`.

Failure or exceptional branches:
- No explicit null handling exists for a missing schema ID; using an ID not produced by Step 2 can result in a server exception.

Endpoint coverage:
- Covers:
  `PUT /consumer/{consumerID}/printingschemas/{printingSchemaID}`
- Distinct meaning:
  Consumer edits a saved print configuration.

### Behavior 44: soft-delete consumer printing schema

Behavior name:
soft-delete consumer printing schema

Successful execution:
- Result:
  This behavior marks a printing schema as deleted.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /consumer/{consumerID}/printingschemas`
  Step 3: `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}`
- Constraints:
  `{consumerID}` comes from Step 1. `{printingSchemaID}` comes from Step 2. The schema is not removed from storage; `deleted` is set true.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Printing schema is already deleted.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /consumer/{consumerID}/printingschemas`
    Step 3: `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}`
    Step 4: `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}`
  - Failure endpoint:
    `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}`
  - Why this fails:
    The second delete sees `ps.isDeleted()` true and returns a JSON property named `"false": true` instead of `success: false`.
  - Intentionally violated constraints:
    Step 4 reuses a schema already deleted by Step 3.

Endpoint coverage:
- Covers:
  `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}`
- Distinct meaning:
  Consumer soft-deletes a print configuration.

### Behavior 45: calculate consumer print-request budgets

Behavior name:
calculate consumer print-request budgets

Successful execution:
- Result:
  This behavior uploads PDFs, creates a `PrintRequest` with documents/specs for the authenticated consumer, calculates budgets for selected print shops, and returns `printRequestID`.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /consumer/{consumerID}/printingschemas`
  Step 7: `POST /consumer/budget`
- Constraints:
  `{consumerID}` comes from Step 1. Step 4 uses the registration request ID from Step 3. Step 5 returns `{printShopID}`. Step 6 returns `{printingSchemaID}`. Step 7 must authenticate as the consumer from Step 1, send multipart PDF files, and include a request part named `printRequest` whose JSON `printshops` array contains `{printShopID}` and whose `files` entries match uploaded PDF filenames and refer to `{printingSchemaID}`. Swagger documents `requestJSON` form data, but the implementation expects `@RequestPart("printRequest")`.

Failure or exceptional branches:
- No structured controller-level failure branch is implemented for malformed multipart/JSON; errors would surface as exceptions.

Endpoint coverage:
- Covers:
  `POST /consumer/budget`
- Distinct meaning:
  Creates a draft print request and calculates print-shop budgets.

### Behavior 46: submit print request with ProxyPrint balance

Behavior name:
submit print request with ProxyPrint balance

Successful execution:
- Result:
  This behavior submits a calculated print request to a selected print shop, immediately pays with ProxyPrint balance, deducts the consumer balance, credits the admin balance, and sets the request status to `PENDING`.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /consumer/{consumerID}/printingschemas`
  Step 7: `POST /consumer/budget`
  Step 8: `POST /paypal/ipn/consumer/{consumerID}`
  Step 9: `POST /consumer/printrequest/{printRequestID}/submit`
- Constraints:
  Step 7 returns `{printRequestID}` and a budget for `{printShopID}` from Step 5. Step 8 must use PayPal IPN fields including `payment_status=Completed` and `mc_gross` high enough to cover the Step 9 cost. Step 9 body must include `printshopID` from Step 5, `budget`, and `paymentMethod` exactly `PROXYPRINT_PAYMENT`. The implementation trusts the submitted `budget`; it does not re-check it against Step 7.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Consumer balance is lower than submitted cost.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /admin/register`
    Step 3: `POST /request/register`
    Step 4: `POST /request/accept/{id}`
    Step 5: `GET /printshop`
    Step 6: `POST /consumer/{consumerID}/printingschemas`
    Step 7: `POST /consumer/budget`
    Step 8: `POST /consumer/printrequest/{printRequestID}/submit`
  - Failure endpoint:
    `POST /consumer/printrequest/{printRequestID}/submit`
  - Why this fails:
    With `paymentMethod=PROXYPRINT_PAYMENT`, the controller checks balance and returns `success: false` when it is insufficient.
  - Intentionally violated constraints:
    `POST /paypal/ipn/consumer/{consumerID}` is omitted or underfunded before submission.
- Branch 2:
  - Unsatisfied condition:
    Print request or print shop does not exist.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /consumer/printrequest/{printRequestID}/submit`
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

### Behavior 47: submit print request for PayPal payment

Behavior name:
submit print request for PayPal payment

Successful execution:
- Result:
  This behavior submits a calculated print request to a print shop, marks its payment type as PayPal, leaves it awaiting payment confirmation, and returns `success: true`.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /consumer/{consumerID}/printingschemas`
  Step 7: `POST /consumer/budget`
  Step 8: `POST /consumer/printrequest/{printRequestID}/submit`
- Constraints:
  Step 8 body must include `printshopID` from Step 5, `budget`, and a `paymentMethod` value other than `PROXYPRINT_PAYMENT`. The code treats any other value as PayPal and sets `paymentType` to `PAYPAL_PAYMENT`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print request or print shop does not exist.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /consumer/printrequest/{printRequestID}/submit`
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

### Behavior 48: confirm PayPal print-request payment

Behavior name:
confirm PayPal print-request payment

Successful execution:
- Result:
  This behavior processes a PayPal IPN callback for a submitted PayPal print request, sets the request status to `PENDING`, stores the PayPal transaction ID, and notifies the consumer.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /consumer/{consumerID}/printingschemas`
  Step 7: `POST /consumer/budget`
  Step 8: `POST /consumer/printrequest/{printRequestID}/submit`
  Step 9: `POST /paypal/ipn/{printRequestID}`
- Constraints:
  Step 8 must submit with PayPal payment as described in Behavior 47. Step 9 path ID must be the `printRequestID` from Step 7. IPN body must include `payer_email`, `mc_gross` equal to the print request cost, `payment_status=Completed`, and `txn_id`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    IPN amount or status does not match.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /admin/register`
    Step 3: `POST /request/register`
    Step 4: `POST /request/accept/{id}`
    Step 5: `GET /printshop`
    Step 6: `POST /consumer/{consumerID}/printingschemas`
    Step 7: `POST /consumer/budget`
    Step 8: `POST /consumer/printrequest/{printRequestID}/submit`
    Step 9: `POST /paypal/ipn/{printRequestID}`
  - Failure endpoint:
    `POST /paypal/ipn/{printRequestID}`
  - Why this fails:
    The controller only updates the request when `payment_status` is PayPal completed and `mc_gross` equals the stored cost.
  - Intentionally violated constraints:
    Step 9 sends a non-`Completed` status or an `mc_gross` different from the request cost.
- Branch 2:
  - Unsatisfied condition:
    Print request does not exist.
  - Endpoint group:
    Step 1: `POST /paypal/ipn/{printRequestID}`
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

### Behavior 49: create external print request

Behavior name:
create external print request

Successful execution:
- Result:
  This behavior lets an external platform upload PDF documents and creates a print request with default print specs.
- Endpoint sequence:
  Step 1: `POST /printdocument`
- Constraints:
  The implementation expects multipart PDF files, although Swagger documents no parameters. Non-PDF files are ignored. Response returns `printRequestID`.

Failure or exceptional branches:
- No structured controller-level failure branch is implemented for missing or malformed files.

Endpoint coverage:
- Covers:
  `POST /printdocument`
- Distinct meaning:
  External integration creates a print request with default schema.

### Behavior 50: retrieve external print-request documents

Behavior name:
retrieve external print-request documents

Successful execution:
- Result:
  This behavior returns document metadata for an external print request.
- Endpoint sequence:
  Step 1: `POST /printdocument`
  Step 2: `GET /printdocument/{id}`
- Constraints:
  `{id}` in Step 2 must be the `printRequestID` returned by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print request does not exist.
  - Endpoint group:
    Step 1: `GET /printdocument/{id}`
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

### Behavior 51: calculate external print-request budget

Behavior name:
calculate external print-request budget

Successful execution:
- Result:
  This behavior associates an external print request with the authenticated consumer and calculates budgets for selected print shops.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /printdocument`
  Step 7: `POST /printdocument/{id}/budget`
- Constraints:
  Step 7 authenticates as the consumer from Step 1. `{id}` is the `printRequestID` from Step 6. Step 7 body is a JSON list of print-shop IDs and must include the print-shop ID returned by Step 5.

Failure or exceptional branches:
- No explicit null check exists before `printRequest.setConsumer(consumer)`; using a missing print-request ID can cause a server exception rather than structured `success: false`.

Endpoint coverage:
- Covers:
  `POST /printdocument/{id}/budget`
- Distinct meaning:
  External integration budget calculation.

### Behavior 52: load consumer balance from PayPal IPN

Behavior name:
load consumer balance from PayPal IPN

Successful execution:
- Result:
  This behavior processes a PayPal IPN callback for a consumer top-up, increases the consumer balance, and sends a notification.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /paypal/ipn/consumer/{consumerID}`
- Constraints:
  `{consumerID}` must be the ID returned by Step 1. IPN body must include `mc_gross` and `payment_status=Completed`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Payment status is not completed.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /paypal/ipn/consumer/{consumerID}`
  - Failure endpoint:
    `POST /paypal/ipn/consumer/{consumerID}`
  - Why this fails:
    The controller does not increase balance and instead sends a failure notification.
  - Intentionally violated constraints:
    Step 2 sends `payment_status` other than `Completed`.

Endpoint coverage:
- Covers:
  `POST /paypal/ipn/consumer/{consumerID}`
- Distinct meaning:
  PayPal callback for consumer balance top-up.

### Behavior 53: list consumer pending print requests

Behavior name:
list consumer pending print requests

Successful execution:
- Result:
  This behavior returns the authenticated consumer’s pending print requests.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `GET /consumer/requests`
- Constraints:
  Step 2 must authenticate as the consumer from Step 1. The list can be empty.

Failure or exceptional branches:
- No reproducible controller-level failure branch is exposed through documented endpoints other than missing/invalid authentication.

Endpoint coverage:
- Covers:
  `GET /consumer/requests`
- Distinct meaning:
  Consumer views active pending requests.

### Behavior 54: cancel consumer pending print request

Behavior name:
cancel consumer pending print request

Successful execution:
- Result:
  This behavior cancels a pending print request owned by the authenticated consumer, removes it from the print shop and consumer, and refunds the cost to the consumer balance from the admin balance.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /consumer/{consumerID}/printingschemas`
  Step 7: `POST /consumer/budget`
  Step 8: `POST /paypal/ipn/consumer/{consumerID}`
  Step 9: `POST /consumer/printrequest/{printRequestID}/submit`
  Step 10: `DELETE /consumer/requests/cancel/{id}`
- Constraints:
  `{consumerID}` comes from Step 1. `{printRequestID}` from Step 7 is used in Step 9 and as `{id}` in Step 10. Step 9 must submit with `PROXYPRINT_PAYMENT` and enough balance so status becomes `PENDING`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Print request does not exist for the authenticated consumer.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `DELETE /consumer/requests/cancel/{id}`
  - Failure endpoint:
    `DELETE /consumer/requests/cancel/{id}`
  - Why this fails:
    `printrequests.findByIdInAndConsumer(id, consumer)` returns null.
  - Intentionally violated constraints:
    The budget and submit sequence producing `{id}` is omitted.
- Branch 2:
  - Unsatisfied condition:
    Print request is no longer pending.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /admin/register`
    Step 3: `POST /request/register`
    Step 4: `POST /request/accept/{id}`
    Step 5: `GET /printshop`
    Step 6: `POST /printshops/{printShopID}/employees`
    Step 7: `POST /consumer/{consumerID}/printingschemas`
    Step 8: `POST /consumer/budget`
    Step 9: `POST /paypal/ipn/consumer/{consumerID}`
    Step 10: `POST /consumer/printrequest/{printRequestID}/submit`
    Step 11: `POST /printshops/requests/{id}`
    Step 12: `DELETE /consumer/requests/cancel/{id}`
  - Failure endpoint:
    `DELETE /consumer/requests/cancel/{id}`
  - Why this fails:
    Step 11 advances the request from `PENDING` to `IN_PROGRESS`, and consumer cancellation only succeeds for `PENDING`.
  - Intentionally violated constraints:
    Step 12 attempts cancellation after the print shop has started processing.

Endpoint coverage:
- Covers:
  `DELETE /consumer/requests/cancel/{id}`
- Distinct meaning:
  Consumer cancels their own pending request.

### Behavior 55: list consumer satisfied requests

Behavior name:
list consumer satisfied requests

Successful execution:
- Result:
  This behavior returns the authenticated consumer’s finished or lifted requests with print-shop names.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `GET /consumer/satisfied`
- Constraints:
  Step 2 must authenticate as the consumer from Step 1. The list can be empty.

Failure or exceptional branches:
- No reproducible controller-level failure branch is exposed through documented endpoints other than missing/invalid authentication.

Endpoint coverage:
- Covers:
  `GET /consumer/satisfied`
- Distinct meaning:
  Consumer views completed request history.

### Behavior 56: list print-shop active queue

Behavior name:
list print-shop active queue

Successful execution:
- Result:
  This behavior returns pending and in-progress requests for the authenticated employee’s print shop.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{printShopID}/employees`
  Step 6: `GET /printshops/requests`
- Constraints:
  Step 6 must authenticate as the employee created by Step 5. Although the annotation allows managers, the implementation looks up the principal in `EmployeeDAO`, so a manager principal can fail at runtime.

Failure or exceptional branches:
- No structured branch exists for a missing employee; it can cause a null dereference before `printshop == null` is checked.

Endpoint coverage:
- Covers:
  `GET /printshops/requests`
- Distinct meaning:
  Employee views active work queue.

### Behavior 57: retrieve print-shop request detail

Behavior name:
retrieve print-shop request detail

Successful execution:
- Result:
  This behavior returns a specific print request assigned to the authenticated employee’s print shop, including document specs formatted for presentation.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /printshops/{printShopID}/employees`
  Step 7: `POST /consumer/{consumerID}/printingschemas`
  Step 8: `POST /consumer/budget`
  Step 9: `POST /paypal/ipn/consumer/{consumerID}`
  Step 10: `POST /consumer/printrequest/{printRequestID}/submit`
  Step 11: `GET /printshops/requests/{id}`
- Constraints:
  `{id}` in Step 11 must be the `printRequestID` from Step 8. Step 11 authenticates as the employee from Step 6. The print request must be assigned to the employee’s print shop in Step 10.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Request is not assigned to the employee’s print shop or does not exist.
  - Endpoint group:
    Step 1: `POST /admin/register`
    Step 2: `POST /request/register`
    Step 3: `POST /request/accept/{id}`
    Step 4: `GET /printshop`
    Step 5: `POST /printshops/{printShopID}/employees`
    Step 6: `GET /printshops/requests/{id}`
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

### Behavior 58: start processing print request

Behavior name:
start processing print request

Successful execution:
- Result:
  This behavior changes a print request from `PENDING` to `IN_PROGRESS`, records the attending employee, and notifies the consumer.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /printshops/{printShopID}/employees`
  Step 7: `POST /consumer/{consumerID}/printingschemas`
  Step 8: `POST /consumer/budget`
  Step 9: `POST /paypal/ipn/consumer/{consumerID}`
  Step 10: `POST /consumer/printrequest/{printRequestID}/submit`
  Step 11: `POST /printshops/requests/{id}`
- Constraints:
  `{id}` in Step 11 is the `printRequestID` from Step 8. Step 11 authenticates as the employee from Step 6. Step 10 must make the request `PENDING`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Request is not pending or not assigned to the employee’s print shop.
  - Endpoint group:
    Step 1: `POST /admin/register`
    Step 2: `POST /request/register`
    Step 3: `POST /request/accept/{id}`
    Step 4: `GET /printshop`
    Step 5: `POST /printshops/{printShopID}/employees`
    Step 6: `POST /printshops/requests/{id}`
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

### Behavior 59: finish print request

Behavior name:
finish print request

Successful execution:
- Result:
  This behavior changes a print request from `IN_PROGRESS` to `FINISHED`, records the finish timestamp, and notifies the consumer that it is ready for pickup.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /printshops/{printShopID}/employees`
  Step 7: `POST /consumer/{consumerID}/printingschemas`
  Step 8: `POST /consumer/budget`
  Step 9: `POST /paypal/ipn/consumer/{consumerID}`
  Step 10: `POST /consumer/printrequest/{printRequestID}/submit`
  Step 11: `POST /printshops/requests/{id}`
  Step 12: `POST /printshops/requests/{id}`
- Constraints:
  `{id}` is the `printRequestID` from Step 8. Step 11 must execute first to move the request to `IN_PROGRESS`; Step 12 then moves it to `FINISHED`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Request has not been started.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /admin/register`
    Step 3: `POST /request/register`
    Step 4: `POST /request/accept/{id}`
    Step 5: `GET /printshop`
    Step 6: `POST /printshops/{printShopID}/employees`
    Step 7: `POST /consumer/{consumerID}/printingschemas`
    Step 8: `POST /consumer/budget`
    Step 9: `POST /paypal/ipn/consumer/{consumerID}`
    Step 10: `POST /consumer/printrequest/{printRequestID}/submit`
    Step 11: `POST /printshops/requests/{id}`
  - Failure endpoint:
    `POST /printshops/requests/{id}`
  - Why this does not finish:
    With status still `PENDING`, the endpoint performs the start-processing behavior instead of finishing.
  - Intentionally violated constraints:
    The prior `PENDING` to `IN_PROGRESS` transition is omitted.

Endpoint coverage:
- Covers:
  `POST /printshops/requests/{id}`
- Distinct meaning:
  Second status transition: `IN_PROGRESS` to `FINISHED`.

### Behavior 60: mark print request lifted

Behavior name:
mark print request lifted

Successful execution:
- Result:
  This behavior changes a finished request to `LIFTED`, records delivery employee/timestamp, and transfers the print shop’s share for ProxyPrint balance payments.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /printshops/{printShopID}/employees`
  Step 7: `POST /consumer/{consumerID}/printingschemas`
  Step 8: `POST /consumer/budget`
  Step 9: `POST /paypal/ipn/consumer/{consumerID}`
  Step 10: `POST /consumer/printrequest/{printRequestID}/submit`
  Step 11: `POST /printshops/requests/{id}`
  Step 12: `POST /printshops/requests/{id}`
  Step 13: `POST /printshops/requests/{id}`
- Constraints:
  `{id}` is the `printRequestID` from Step 8. Steps 11 and 12 must move the request to `FINISHED` before Step 13. For PayPal-paid requests, Step 13 additionally depends on PayPal payout success; the sequence above uses ProxyPrint balance to avoid that external dependency.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Request is not finished.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /admin/register`
    Step 3: `POST /request/register`
    Step 4: `POST /request/accept/{id}`
    Step 5: `GET /printshop`
    Step 6: `POST /printshops/{printShopID}/employees`
    Step 7: `POST /consumer/{consumerID}/printingschemas`
    Step 8: `POST /consumer/budget`
    Step 9: `POST /paypal/ipn/consumer/{consumerID}`
    Step 10: `POST /consumer/printrequest/{printRequestID}/submit`
    Step 11: `POST /printshops/requests/{id}`
  - Failure endpoint:
    `POST /printshops/requests/{id}`
  - Why this does not mark lifted:
    With status `PENDING`, the endpoint performs a different status transition.
  - Intentionally violated constraints:
    The required transitions to `IN_PROGRESS` and `FINISHED` are omitted.

Endpoint coverage:
- Covers:
  `POST /printshops/requests/{id}`
- Distinct meaning:
  Third status transition: `FINISHED` to `LIFTED`.

### Behavior 61: cancel print-shop pending request

Behavior name:
cancel print-shop pending request

Successful execution:
- Result:
  This behavior lets an employee cancel a pending request assigned to their print shop, remove it from the print shop and consumer, and notify the consumer with a motive.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /printshops/{printShopID}/employees`
  Step 7: `POST /consumer/{consumerID}/printingschemas`
  Step 8: `POST /consumer/budget`
  Step 9: `POST /paypal/ipn/consumer/{consumerID}`
  Step 10: `POST /consumer/printrequest/{printRequestID}/submit`
  Step 11: `POST /printshops/requests/cancel/{id}`
- Constraints:
  `{id}` in Step 11 is the `printRequestID` from Step 8. The request body is the cancellation motive. Step 11 authenticates as the employee from Step 6 and only succeeds while status is `PENDING`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Request is not pending.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `POST /admin/register`
    Step 3: `POST /request/register`
    Step 4: `POST /request/accept/{id}`
    Step 5: `GET /printshop`
    Step 6: `POST /printshops/{printShopID}/employees`
    Step 7: `POST /consumer/{consumerID}/printingschemas`
    Step 8: `POST /consumer/budget`
    Step 9: `POST /paypal/ipn/consumer/{consumerID}`
    Step 10: `POST /consumer/printrequest/{printRequestID}/submit`
    Step 11: `POST /printshops/requests/{id}`
    Step 12: `POST /printshops/requests/cancel/{id}`
  - Failure endpoint:
    `POST /printshops/requests/cancel/{id}`
  - Why this fails:
    Step 11 changes status to `IN_PROGRESS`; cancellation only succeeds for `PENDING`.
  - Intentionally violated constraints:
    Step 12 attempts cancellation after processing has started.

Endpoint coverage:
- Covers:
  `POST /printshops/requests/cancel/{id}`
- Distinct meaning:
  Employee cancels an assigned pending request.

### Behavior 62: list print-shop finished requests

Behavior name:
list print-shop finished requests

Successful execution:
- Result:
  This behavior returns finished requests for the authenticated employee’s print shop.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{printShopID}/employees`
  Step 6: `GET /printshops/satisfied`
- Constraints:
  Step 6 authenticates as the employee from Step 5. The list can be empty. Implementation uses `EmployeeDAO` even though `ROLE_MANAGER` is also annotated.

Failure or exceptional branches:
- No structured branch exists for a non-employee principal; it can fail before returning JSON.

Endpoint coverage:
- Covers:
  `GET /printshops/satisfied`
- Distinct meaning:
  Print-shop view of requests ready for pickup.

### Behavior 63: list print-shop lifted request history

Behavior name:
list print-shop lifted request history

Successful execution:
- Result:
  This behavior returns lifted request history for the authenticated employee’s print shop.
- Endpoint sequence:
  Step 1: `POST /admin/register`
  Step 2: `POST /request/register`
  Step 3: `POST /request/accept/{id}`
  Step 4: `GET /printshop`
  Step 5: `POST /printshops/{printShopID}/employees`
  Step 6: `GET /printshops/history`
- Constraints:
  Step 6 authenticates as the employee from Step 5. The list can be empty.

Failure or exceptional branches:
- No structured branch exists for a non-employee principal; it can fail before returning JSON.

Endpoint coverage:
- Covers:
  `GET /printshops/history`
- Distinct meaning:
  Print-shop request history after delivery.

### Behavior 64: download assigned document PDF

Behavior name:
download assigned document PDF

Successful execution:
- Result:
  This behavior returns a PDF file for a document belonging to a request assigned to the authenticated employee’s print shop.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /admin/register`
  Step 3: `POST /request/register`
  Step 4: `POST /request/accept/{id}`
  Step 5: `GET /printshop`
  Step 6: `POST /printshops/{printShopID}/employees`
  Step 7: `POST /consumer/{consumerID}/printingschemas`
  Step 8: `POST /consumer/budget`
  Step 9: `POST /paypal/ipn/consumer/{consumerID}`
  Step 10: `POST /consumer/printrequest/{printRequestID}/submit`
  Step 11: `GET /printshops/requests/{id}`
  Step 12: `GET /documents/{id}`
- Constraints:
  Step 11 uses the `printRequestID` from Step 8 and returns document IDs. `{id}` in Step 12 must be a document ID from Step 11. Step 12 authenticates as the employee from Step 6. The document’s print request must belong to that employee’s print shop.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Document does not exist.
  - Endpoint group:
    Step 1: `POST /admin/register`
    Step 2: `POST /request/register`
    Step 3: `POST /request/accept/{id}`
    Step 4: `GET /printshop`
    Step 5: `POST /printshops/{printShopID}/employees`
    Step 6: `GET /documents/{id}`
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

### Behavior 65: send notification to consumer

Behavior name:
send notification to consumer

Successful execution:
- Result:
  This behavior creates a notification for a consumer and pushes it to active SSE subscribers.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /consumer/{id}/notify`
- Constraints:
  `{id}` must be the consumer ID from Step 1. Step 2 uses request parameter `message`.

Failure or exceptional branches:
- No explicit null handling exists for a missing consumer ID; using an ID not produced by Step 1 can cause a null dereference.

Endpoint coverage:
- Covers:
  `POST /consumer/{id}/notify`
- Distinct meaning:
  Direct notification creation for a consumer.

### Behavior 66: subscribe to notification stream

Behavior name:
subscribe to notification stream

Successful execution:
- Result:
  This behavior opens a server-sent-events stream for a consumer.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `GET /consumer/subscribe`
- Constraints:
  Step 2 must provide `username` and `password` matching Step 1. Source code does not restrict HTTP method, so Swagger exposes GET, HEAD, POST, PUT, DELETE, OPTIONS, and PATCH for the same subscription logic.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Consumer username is unknown.
  - Endpoint group:
    Step 1: `GET /consumer/subscribe`
  - Failure endpoint:
    `GET /consumer/subscribe`
  - Why this fails:
    `consumers.findByUsername(username)` returns null and the endpoint returns `404`.
  - Intentionally violated constraints:
    `POST /consumer/register` is omitted.
- Branch 2:
  - Unsatisfied condition:
    Password is incorrect.
  - Endpoint group:
    Step 1: `POST /consumer/register`
    Step 2: `GET /consumer/subscribe`
  - Failure endpoint:
    `GET /consumer/subscribe`
  - Why this fails:
    The username exists, but `consumer.getPassword().equals(password)` is false, so the endpoint returns `401`.
  - Intentionally violated constraints:
    Step 2 reuses the username from Step 1 but sends a different password.

Endpoint coverage:
- Covers:
  `GET /consumer/subscribe`, `HEAD /consumer/subscribe`, `POST /consumer/subscribe`, `PUT /consumer/subscribe`, `DELETE /consumer/subscribe`, `OPTIONS /consumer/subscribe`, `PATCH /consumer/subscribe`
- Distinct meaning:
  All documented methods map to the same SSE subscription behavior because the implementation omits a method restriction.

### Behavior 67: list consumer notifications

Behavior name:
list consumer notifications

Successful execution:
- Result:
  This behavior returns notifications for the authenticated consumer.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `GET /consumer/notifications`
- Constraints:
  Step 2 authenticates as the consumer from Step 1. The list can be empty.

Failure or exceptional branches:
- No reproducible controller-level failure branch is exposed through documented endpoints other than missing/invalid authentication.

Endpoint coverage:
- Covers:
  `GET /consumer/notifications`
- Distinct meaning:
  Consumer reads their notification list.

### Behavior 68: mark notification read

Behavior name:
mark notification read

Successful execution:
- Result:
  This behavior marks a single notification as read.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /consumer/{id}/notify`
  Step 3: `GET /consumer/notifications`
  Step 4: `PUT /notifications/{notificationId}`
- Constraints:
  `{id}` in Step 2 is the consumer ID from Step 1. Step 3 authenticates as that consumer and returns notification IDs. `{notificationId}` in Step 4 must be from Step 3. The implementation does not verify ownership in Step 4.

Failure or exceptional branches:
- No explicit null handling exists for a missing notification ID; using an ID not produced by Step 3 can cause a server exception.

Endpoint coverage:
- Covers:
  `PUT /notifications/{notificationId}`
- Distinct meaning:
  Marks one notification read.

### Behavior 69: delete notification

Behavior name:
delete notification

Successful execution:
- Result:
  This behavior deletes a single notification by ID.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `POST /consumer/{id}/notify`
  Step 3: `GET /consumer/notifications`
  Step 4: `DELETE /notifications/{notificationId}`
- Constraints:
  `{notificationId}` in Step 4 must be obtained from Step 3. The implementation deletes by ID and does not verify ownership.

Failure or exceptional branches:
- No explicit controller-level failure branch is implemented.

Endpoint coverage:
- Covers:
  `DELETE /notifications/{notificationId}`
- Distinct meaning:
  Deletes one notification.

### Behavior 70: mark all consumer notifications read

Behavior name:
mark all consumer notifications read

Successful execution:
- Result:
  This behavior marks all notifications for a username as read.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `PUT /consumer/{username}/notifications`
- Constraints:
  `{username}` should be the username created by Step 1. Endpoint is secured with `ROLE_USER`, but implementation does not check that the path username matches the authenticated principal.

Failure or exceptional branches:
- No explicit null handling exists for an unknown username; using a username not produced by Step 1 can cause a null dereference.

Endpoint coverage:
- Covers:
  `PUT /consumer/{username}/notifications`
- Distinct meaning:
  Bulk mark notifications read.

### Behavior 71: delete all consumer notifications

Behavior name:
delete all consumer notifications

Successful execution:
- Result:
  This behavior removes all notifications for a username.
- Endpoint sequence:
  Step 1: `POST /consumer/register`
  Step 2: `DELETE /consumer/{username}/notifications`
- Constraints:
  `{username}` should be the username created by Step 1. Endpoint is secured with `ROLE_USER`, but implementation does not check that the path username matches the authenticated principal.

Failure or exceptional branches:
- No explicit null handling exists for an unknown username; using a username not produced by Step 1 can cause a null dereference.

Endpoint coverage:
- Covers:
  `DELETE /consumer/{username}/notifications`
- Distinct meaning:
  Bulk delete notifications.

### Unclear or auxiliary endpoints

These endpoints are present in `proxyprint.json` but are not implemented by project application controllers in `src`; they appear to be generated Spring Boot Actuator or framework error endpoints, so they are auxiliary rather than domain behaviors:

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