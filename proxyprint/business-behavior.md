# Domain-Level Behavior Analysis

## Domain Summary

ProxyPrint is a print-service marketplace. Consumers register accounts, maintain saved printing schemas, upload PDF print requests, calculate budgets against registered print shops, pay through ProxyPrint balance or PayPal, and track request status. Print shops are onboarded through admin-reviewed registration requests. Managers maintain their shop, employees, price table, and operational queue. Employees process requests and download assigned documents. The service also supports print-shop reviews, notifications, demo data seeding, and external print-document submission.

The main resources are users (`Consumer`, `Admin`, `Manager`, `Employee`), `PrintShop`, `RegisterRequest`, `PrintingSchema`, `PrintRequest`, `Document`, `DocumentSpec`, `Review`, `Notification`, and price-table items for paper, binding/ring, stapling, and covers.

Implementation behavior is more authoritative than Swagger. Several Swagger entries omit required bodies or document generic parameters, while controllers read form parameters, raw JSON bodies, query parameters, multipart `printRequest` parts, or PayPal IPN fields.

## Available Function Inventory

### Platform and Authentication

- `return welcome message` - `GET /` - returns the API root welcome message.
- `answer CORS preflight` - `OPTIONS /*` - returns wildcard preflight response.
- `verify authenticated user access` - `GET /api/secured` - confirms a `ROLE_USER` authenticated route.
- `log in user` - `POST /login` - validates username/password and returns the user object; no token is issued.
- `register admin` - `POST /admin/register` - creates an admin user.

### Admin and Demo Data

- `seed demo dataset` - `POST /admin/seed` - creates broad hardcoded demo state.
- `seed many consumers` - `POST /admin/useed` - creates 1000 consumer accounts.
- `list print shops as admin` - `GET /admin/printshops` - admin-only list of print shops.

### Consumer Account

- `register consumer` - `POST /consumer/register` - creates a consumer account from request parameters.
- `retrieve consumer profile` - `GET /consumer/info` - reads the authenticated consumer profile.
- `update consumer profile` - `PUT /consumer/info/update` - updates the authenticated consumer profile.
- `retrieve consumer balance` - `GET /consumer/balance` - reads the authenticated consumer balance.

### Print-Shop Onboarding and Discovery

- `request print-shop registration` - `POST /request/register` - creates a pending print-shop registration request.
- `list pending print-shop registration requests` - `GET /requests/pending` - admin review queue for registration requests.
- `accept print-shop registration request` - `POST /request/accept/{id}` - creates manager and print shop from a request, then deletes the request.
- `reject print-shop registration request` - `POST /request/reject/{printRequestID}` - deletes a pending registration request with a motive.
- `list public print shops` - `GET /printshops` - public list of print shops.
- `retrieve print shop by id` - `GET /printshops/{id}` - public read of one print shop.
- `find nearest print shops` - `GET /printshops/nearest` - location-based sorting by supplied coordinates.
- `retrieve print-shop price table` - `GET /printshops/{id}/pricetable` - reads normalized price-table data.
- `retrieve manager print shop` - `GET /printshop` - manager-scoped lookup of the managed print shop.
- `retrieve print-shop statistics` - `GET /printshops/stats` - manager dashboard counts and profit.

### Employees

- `list print-shop employees` - `GET /printshops/{printShopID}/employees` - manager reads employee roster.
- `add print-shop employee` - `POST /printshops/{printShopID}/employees` - manager creates employee credentials.
- `edit print-shop employee` - `PUT /printshops/{printShopID}/employees` - manager updates employee profile fields.
- `delete print-shop employee` - `DELETE /printshops/{printShopID}/employees/{employeeID}` - manager deletes an employee account.
- `download assigned document PDF` - `GET /documents/{id}` - employee downloads a PDF assigned to the employee's print shop.

### Price Tables

- `add paper price rows` - `POST /printshops/{id}/pricetable/papers` - adds or upserts paper price rows.
- `edit paper price rows` - `PUT /printshops/{id}/pricetable/papers` - upserts paper price rows.
- `delete paper price rows` - `POST /printshops/{id}/pricetable/deletepaper` - removes paper price rows by generated item keys.
- `add ring binding price` - `POST /printshops/{id}/pricetable/rings` - adds or upserts ring binding price.
- `edit ring binding price` - `PUT /printshops/{id}/pricetable/rings` - upserts ring binding price.
- `delete ring binding price` - `POST /printshops/{id}/pricetable/deletering` - removes ring binding price by generated item key.
- `add cover price` - `POST /printshops/{id}/pricetable/covers` - adds or upserts cover prices.
- `edit cover price` - `PUT /printshops/{id}/pricetable/covers` - upserts cover prices.
- `delete cover price` - `POST /printshops/{id}/pricetable/deletecover` - removes cover prices by generated item keys.
- `edit stapling price` - `PUT /printshops/{printShopID}/pricetable/editstapling` - updates stapling price.

### Reviews

- `list print-shop reviews` - `GET /printshops/{id}/reviews` - lists reviews for a shop.
- `add print-shop review` - `POST /printshops/{id}/reviews` - consumer creates a review and updates average rating.
- `edit print-shop review` - `PUT /printshops/{printShopId}/reviews/{reviewId}` - author edits review content and rating.
- `delete print-shop review` - `DELETE /printshops/{printShopId}/reviews/{reviewId}` - author removes review from shop.

### Printing Schemas

- `list consumer printing schemas` - `GET /consumer/{consumerID}/printingschemas` - lists saved schemas for a consumer id.
- `add consumer printing schema` - `POST /consumer/{consumerID}/printingschemas` - saves a print configuration.
- `edit consumer printing schema` - `PUT /consumer/{consumerID}/printingschemas/{printingSchemaID}` - updates schema fields.
- `soft-delete consumer printing schema` - `DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}` - marks a schema deleted.

### Print Requests, Payment, and Fulfillment

- `calculate consumer print-request budgets` - `POST /consumer/budget` - uploads PDFs, creates a draft request, stores specs, and calculates budgets.
- `submit print request with ProxyPrint balance` - `POST /consumer/printrequest/{printRequestID}/submit` - selects a print shop and pays from ProxyPrint balance.
- `submit print request for PayPal payment` - `POST /consumer/printrequest/{printRequestID}/submit` - selects a print shop and marks the request as PayPal-paid later.
- `confirm PayPal print-request payment` - `POST /paypal/ipn/{printRequestID}` - PayPal IPN confirms a submitted request and moves it to pending.
- `create external print request` - `POST /printdocument` - uploads PDFs from an external platform and creates a print request with default specs.
- `retrieve external print-request documents` - `GET /printdocument/{id}` - lists documents for an external print request.
- `calculate external print-request budget` - `POST /printdocument/{id}/budget` - attaches a consumer and calculates budgets for an external request.
- `load consumer balance from PayPal IPN` - `POST /paypal/ipn/consumer/{consumerID}` - PayPal IPN credits consumer balance.
- `list consumer pending print requests` - `GET /consumer/requests` - lists authenticated consumer pending requests.
- `cancel consumer pending print request` - `DELETE /consumer/requests/cancel/{id}` - consumer cancels own pending request and receives balance refund.
- `list consumer satisfied requests` - `GET /consumer/satisfied` - lists consumer finished and lifted requests.
- `list print-shop active queue` - `GET /printshops/requests` - manager/employee lists pending and in-progress requests for their shop.
- `retrieve print-shop request detail` - `GET /printshops/requests/{id}` - manager/employee reads one scoped request.
- `start processing print request` - `POST /printshops/requests/{id}` - advances pending request to in-progress.
- `finish print request` - `POST /printshops/requests/{id}` - advances in-progress request to finished.
- `mark print request lifted` - `POST /printshops/requests/{id}` - advances finished request to lifted and settles shop share.
- `cancel print-shop pending request` - `POST /printshops/requests/cancel/{id}` - manager/employee cancels a pending shop request with motive.
- `list print-shop finished requests` - `GET /printshops/satisfied` - lists finished requests for a shop.
- `list print-shop lifted request history` - `GET /printshops/history` - lists lifted request history for a shop.

### Notifications

- `send notification to consumer` - `POST /consumer/{id}/notify` - sends a notification to a consumer by id.
- `subscribe to notification stream` - `GET|HEAD|POST|PUT|DELETE|OPTIONS|PATCH /consumer/subscribe` - opens SSE stream after username/password check.
- `list consumer notifications` - `GET /consumer/notifications` - lists authenticated consumer notifications.
- `mark notification read` - `PUT /notifications/{notificationId}` - marks one notification as read.
- `delete notification` - `DELETE /notifications/{notificationId}` - removes one notification.
- `mark all consumer notifications read` - `PUT /consumer/{username}/notifications` - marks all notifications for a username as read.
- `delete all consumer notifications` - `DELETE /consumer/{username}/notifications` - removes all notifications for a username.

## Supported Business Behaviors

### Behavior 1: Establish Accounts and Authenticate Users

Business goal:
Create platform users and validate credentials for later role-scoped workflows.

Domain context:
Consumers, admins, managers, and employees drive different parts of ProxyPrint. Consumer and admin accounts can be created directly by API. Manager accounts are created through print-shop onboarding. Employee accounts are created by managers.

Starting point:
No prior service state for consumer/admin registration. Manager and employee credentials require onboarding or employee creation.

Required execution workflow:
1. Use function `register consumer` (`POST /consumer/register`) with form/query values `username={consumerUsername}`, `password={consumerPassword}`, `email={consumerEmail}`, `name={consumerName}`, `latitude={consumerLatitude}`, and `longitude={consumerLongitude}` to create a consumer.
2. Use function `register admin` (`POST /admin/register`) with JSON body containing `username={adminUsername}`, `password={adminPassword}`, and `email={adminEmail}` to create an admin.
3. Use function `log in user` (`POST /login`) with form/query values `username={consumerUsername}` and `password={consumerPassword}` or `username={adminUsername}` and `password={adminPassword}` to validate credentials.
4. Use function `verify authenticated user access` (`GET /api/secured`) authenticated as `{consumerUsername}:{consumerPassword}` to confirm `ROLE_USER` access.

Optional verification workflow:
1. Use function `retrieve consumer profile` (`GET /consumer/info`) authenticated as `{consumerUsername}:{consumerPassword}` to inspect the created consumer.
2. Use function `retrieve consumer balance` (`GET /consumer/balance`) authenticated as `{consumerUsername}:{consumerPassword}` to inspect the initial balance.

Existing-state shortcuts:
- Skip `register consumer` if an equivalent `Consumer` user with `ROLE_USER`, matching username/password, and profile fields already exists.
- Skip `register admin` if an equivalent `Admin` user with `ROLE_ADMIN` already exists.
- Direct database setup can create users, but role, username, password, and consumer/admin subclass must still match the later authentication step.

Parameter and value bindings:
- The same `{consumerUsername}` and `{consumerPassword}` created by `register consumer` must be reused by `log in user`, `verify authenticated user access`, `retrieve consumer profile`, and `retrieve consumer balance`.
- The same `{adminUsername}` and `{adminPassword}` created by `register admin` must be reused by admin-only workflows.
- `register consumer` returns a consumer object; its `id` is reused as `{consumerID}` in printing-schema, balance-load, and notification workflows.

Business result:
Consumer and admin accounts exist. Login returns `success: true` and the concrete user object for matching credentials. Consumer-only secured access is available for the consumer account.

Constraints and invariants:
- Consumer usernames must be unique at controller level.
- Admin registration has no explicit duplicate validation.
- Login checks stored plaintext password equality and does not issue a token.
- Swagger omits some concrete registration fields; the implementation reads consumer form/query parameters and admin JSON body.

Failure and exceptional cases:
- Failing function: `register consumer`
  Failure condition: `{consumerUsername}` already exists.
  Why it fails: `consumers.findByUsername(username)` returns an existing consumer and the controller returns `success: false`.
  Violated prerequisite or constraint: consumer username uniqueness.
- Failing function: `log in user`
  Failure condition: missing username/password, unknown username, or mismatched password.
  Why it fails: the controller returns `success: false` when parameters are absent, user lookup fails, or password equality fails.
  Violated prerequisite or constraint: supplied credentials must match an existing user.
- Failing function: `verify authenticated user access`
  Failure condition: caller is not authenticated as `ROLE_USER`.
  Why it fails: Spring security protects the endpoint.
  Violated prerequisite or constraint: consumer-role authentication.

Implementation notes:
- `POST /login` is credential validation, not session/token issuance in controller code.
- Consumer profile update can later change username/password, which changes values required by authentication-dependent functions.

### Behavior 2: Seed Demonstration Operating State

Business goal:
Populate the service with broad demo data for development and testing.

Domain context:
The seed endpoints create non-minimal state spanning users, print shops, price tables, requests, and reviews. This is not a normal customer workflow but is domain-relevant for demonstration.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `seed demo dataset` (`POST /admin/seed`) with no body to create demo admins, consumers, print shops, managers, registration requests, print requests, price tables, and reviews.
2. Optionally, use function `seed many consumers` (`POST /admin/useed`) with no body to create 1000 consumer accounts with randomized coordinates and preset balance.

Optional verification workflow:
1. Use function `list public print shops` (`GET /printshops`) to inspect created print shops.
2. Use function `list print shops as admin` (`GET /admin/printshops`) authenticated as a seeded or separately created admin to inspect all print shops from admin scope.

Existing-state shortcuts:
- Skip either seed function if equivalent demo records already exist.
- Direct database fixtures are equivalent only if they establish linked users, shops, price tables, requests, balances, and reviews consistently.

Parameter and value bindings:
- The seed functions do not take user-provided identifiers. Later workflows must discover generated ids through listing/read functions such as `list public print shops`, `retrieve print shop by id`, `retrieve manager print shop`, or direct database reads.

Business result:
The database contains a prebuilt operating marketplace dataset. `seed many consumers` additionally creates a large consumer population.

Constraints and invariants:
- No authorization is enforced on these seed endpoints in the controller.
- Repeated calls may create duplicate or conflicting state, depending on persistence constraints outside the controller.

Failure and exceptional cases:
- Failing function: `seed demo dataset`
  Failure condition: persistence-level duplicate or constraint violation.
  Why it fails: the controller has no explicit duplicate checks; failures would come from repository/database behavior.
  Violated prerequisite or constraint: database uniqueness or integrity constraints, if present.
- Failing function: `seed many consumers`
  Failure condition: persistence-level duplicate or constraint violation.
  Why it fails: the controller loops through generated users without visible controller-level recovery.
  Violated prerequisite or constraint: database uniqueness or integrity constraints, if present.

Implementation notes:
- This behavior is broad setup, not a minimal business creation path.

### Behavior 3: Manage Consumer Profile and Balance

Business goal:
Allow a consumer to inspect and update account data and monitor ProxyPrint balance.

Domain context:
Profile information and balance are used during print ordering, location-based search, payment, notification, and account continuity.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `register consumer` (`POST /consumer/register`) with `username={consumerUsername}`, `password={consumerPassword}`, `email={consumerEmail}`, `name={consumerName}`, `latitude={consumerLatitude}`, and `longitude={consumerLongitude}` to create the consumer.
2. Use function `update consumer profile` (`PUT /consumer/info/update`) authenticated as `{consumerUsername}:{consumerPassword}` with JSON `Consumer` body containing replacement `name={newName}`, `username={newUsername}`, `password={newPassword}`, and `email={newEmail}` to update mutable profile fields.
3. Use function `load consumer balance from PayPal IPN` (`POST /paypal/ipn/consumer/{consumerID}`) with `{consumerID}` from `register consumer` and IPN fields including `payment_status=Completed`, `mc_gross={amount}`, and `payer_email={payerEmail}` to increase balance.

Optional verification workflow:
1. Use function `retrieve consumer profile` (`GET /consumer/info`) authenticated with the current username/password to inspect profile values.
2. Use function `retrieve consumer balance` (`GET /consumer/balance`) authenticated with the current username/password to inspect balance.

Existing-state shortcuts:
- Skip `register consumer` if the consumer exists and the current credentials are known.
- Skip `update consumer profile` if the stored profile already has the desired values.
- Skip `load consumer balance from PayPal IPN` if direct database setup or earlier payments already provide the required balance.

Parameter and value bindings:
- `{consumerID}` returned by `register consumer` is reused in `load consumer balance from PayPal IPN`.
- If `update consumer profile` changes username or password, later authenticated calls must use `{newUsername}:{newPassword}`, not the original credentials.
- `mc_gross={amount}` becomes the added balance quantity when `payment_status=Completed`.

Business result:
The consumer exists with updated profile fields and a balance increased by the completed IPN amount.

Constraints and invariants:
- Profile update is authenticated by current principal and updates only name, username, password, and email.
- Profile update does not validate new username uniqueness.
- PayPal IPN validation is instantiated but the balance-load implementation does not require `isIpnVerified` before crediting; it checks `payment_status`.

Failure and exceptional cases:
- Failing function: `update consumer profile`
  Failure condition: authenticated principal cannot be resolved to a consumer, or the body cannot be parsed into a usable `Consumer`.
  Why it fails: the controller only sets `success: true` after finding the current consumer and applying fields.
  Violated prerequisite or constraint: valid authenticated consumer and valid JSON body.
- Failing function: `load consumer balance from PayPal IPN`
  Failure condition: `{consumerID}` does not identify a consumer.
  Why it fails: `consumers.findOne(cid)` returns null and no balance is credited.
  Violated prerequisite or constraint: existing consumer id.
- Failing function: `load consumer balance from PayPal IPN`
  Failure condition: `payment_status` is not `Completed`.
  Why it fails: the controller sends a failure notification instead of adding money.
  Violated prerequisite or constraint: completed PayPal payment status.

Implementation notes:
- This endpoint is a callback-style payment handler and returns no structured success body.

### Behavior 4: Onboard or Reject a Print Shop

Business goal:
Allow a prospective print shop to request onboarding and allow an admin to approve or reject it.

Domain context:
Print shops are not created directly by a public create-shop endpoint. They enter the marketplace through `RegisterRequest`, admin acceptance, manager account creation, and print-shop creation.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `register admin` (`POST /admin/register`) with JSON body `username={adminUsername}`, `password={adminPassword}`, and `email={adminEmail}` to create an approving admin.
2. Use function `request print-shop registration` (`POST /request/register`) with JSON body containing `managerName={managerName}`, `managerUsername={managerUsername}`, `managerEmail={managerEmail}`, `managerPassword={managerPassword}`, `pShopAddress={shopAddress}`, `pShopLatitude={shopLatitude}`, `pShopLongitude={shopLongitude}`, `pShopNIF={shopNIF}`, and `pShopName={shopName}` to create `{registrationRequestId}`.
3. For approval, use function `accept print-shop registration request` (`POST /request/accept/{id}`) authenticated as `{adminUsername}:{adminPassword}` with `{id}={registrationRequestId}` to create a manager account and linked print shop.
4. For rejection instead of approval, use function `reject print-shop registration request` (`POST /request/reject/{printRequestID}`) authenticated as `{adminUsername}:{adminPassword}` with `{printRequestID}={registrationRequestId}` and body `{motive}` to delete the request and send a rejection email.

Optional verification workflow:
1. Use function `list pending print-shop registration requests` (`GET /requests/pending`) authenticated as admin before the decision to inspect pending requests.
2. After approval, use function `retrieve manager print shop` (`GET /printshop`) authenticated as `{managerUsername}:{managerPassword}` to obtain the created `{printShopID}`.
3. Use function `list public print shops` (`GET /printshops`) or `retrieve print shop by id` (`GET /printshops/{id}`) with `{id}={printShopID}` to inspect public availability.

Existing-state shortcuts:
- Skip `register admin` if an admin already exists.
- Skip `request print-shop registration` only if an equivalent pending `RegisterRequest` exists and its id is known.
- Direct database setup can create linked `Manager` and `PrintShop` rows, but then approval is no longer the behavior being executed; later workflows still need the manager credentials and print-shop id.

Parameter and value bindings:
- `{registrationRequestId}` returned by `request print-shop registration` must be reused as `{id}` for acceptance or `{printRequestID}` for rejection.
- The manager credentials used later are copied from `managerUsername` and `managerPassword` in the registration request body.
- The print-shop fields are copied from `pShop...` request fields into the created `PrintShop`.

Business result:
Approval creates a manager user and a print shop linked to that manager, deletes the registration request, and attempts an acceptance email. Rejection deletes the registration request and attempts a rejection email with the motive.

Constraints and invariants:
- Acceptance and rejection require `ROLE_ADMIN`.
- The controller does not enforce uniqueness for manager username, shop NIF, or shop name.
- `accepted=false` requests are listed as pending, but acceptance deletes the request rather than marking it accepted.

Failure and exceptional cases:
- Failing function: `list pending print-shop registration requests`
  Failure condition: caller is not an admin.
  Why it fails: Spring security protects the endpoint with `ROLE_ADMIN`.
  Violated prerequisite or constraint: admin authentication.
- Failing function: `accept print-shop registration request`
  Failure condition: `{registrationRequestId}` does not exist.
  Why it fails: `registerRequests.findOne(id)` returns null and the controller returns `success: false`.
  Violated prerequisite or constraint: existing pending registration request id.
- Failing function: `reject print-shop registration request`
  Failure condition: `{registrationRequestId}` does not exist.
  Why it fails: `registerRequests.findOne(prid)` returns null and the controller returns `success: false`.
  Violated prerequisite or constraint: existing pending registration request id.

Implementation notes:
- The failure tail of `accept print-shop registration request` returns `success: true` even if manager or print-shop saving fails after the request exists, which is an implementation defect.

### Behavior 5: Discover Print Shops and Their Offerings

Business goal:
Allow users to find print shops, inspect one shop, sort shops by distance, and view price offerings.

Domain context:
Consumers need shop discovery before budgeting and submission. Managers also use their scoped shop identity for employee and price-table operations.

Starting point:
No prior service state for empty public listing. For inspecting a concrete shop, use an onboarded shop.

Required execution workflow:
1. Use function `register admin` (`POST /admin/register`) with admin JSON body to create an admin.
2. Use function `request print-shop registration` (`POST /request/register`) with manager and `pShop...` fields to create `{registrationRequestId}`.
3. Use function `accept print-shop registration request` (`POST /request/accept/{id}`) authenticated as admin with `{id}={registrationRequestId}` to create the shop.
4. Use function `retrieve manager print shop` (`GET /printshop`) authenticated as `{managerUsername}:{managerPassword}` to capture `{printShopID}`.
5. Use function `list public print shops` (`GET /printshops`) to discover public shops.
6. Use function `retrieve print shop by id` (`GET /printshops/{id}`) with `{id}={printShopID}` to inspect the specific shop.
7. Use function `find nearest print shops` (`GET /printshops/nearest`) with query `latitude={latitude}` and `longitude={longitude}` to sort shops by distance.
8. Use function `retrieve print-shop price table` (`GET /printshops/{id}/pricetable`) authenticated as a manager or consumer with `{id}={printShopID}` to inspect normalized paper, ring, stapling, and cover prices.

Optional verification workflow:
1. Use function `list print shops as admin` (`GET /admin/printshops`) authenticated as admin to inspect all shops through admin scope.

Existing-state shortcuts:
- Skip onboarding steps if `{printShopID}` already identifies an existing print shop.
- Direct database setup can create `PrintShop` and `Manager` rows, but price-table reads still require `ROLE_MANAGER` or `ROLE_USER` authentication.

Parameter and value bindings:
- `{registrationRequestId}` links request creation to acceptance.
- `{managerUsername}:{managerPassword}` from registration are reused to call `retrieve manager print shop`.
- `{printShopID}` from `retrieve manager print shop` is reused in `retrieve print shop by id` and `retrieve print-shop price table`.
- `latitude` and `longitude` query parameters for `find nearest print shops` intentionally differ from shop coordinates; they represent the search origin.

Business result:
The caller can list shops, read one shop, calculate distance ordering, and inspect current price-table data for the chosen shop.

Constraints and invariants:
- `find nearest print shops` requires both coordinates and parses them as doubles.
- `retrieve print-shop price table` is secured for managers and consumers, while public shop listing and shop-by-id are not secured.
- The nearest-shop implementation uses a `TreeMap<Double, PrintShop>`; equal distances can overwrite earlier shops with the same distance key.

Failure and exceptional cases:
- Failing function: `find nearest print shops`
  Failure condition: missing `latitude` or `longitude`.
  Why it fails: the controller returns `success: false` unless both query parameters are present.
  Violated prerequisite or constraint: complete coordinate input.
- Failing function: `retrieve print shop by id`
  Failure condition: `{printShopID}` does not exist.
  Why it fails: `printshops.findOne(id)` returns null and the controller returns 404.
  Violated prerequisite or constraint: existing print shop.
- Failing function: `retrieve print-shop price table`
  Failure condition: `{printShopID}` does not exist.
  Why it fails: the controller returns `success: false`.
  Violated prerequisite or constraint: existing print shop and role authentication.

Implementation notes:
- Admin listing serializes the list into a JSON string property named `prinshops`, not a normal JSON array property.

### Behavior 6: Manage Print-Shop Staff and Manager Dashboard

Business goal:
Allow a manager to operate the shop's employee roster and inspect operational statistics.

Domain context:
Employees process print requests and download documents. Managers create those employee accounts and monitor queue counts and profit.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `register admin` (`POST /admin/register`) with admin JSON body.
2. Use function `request print-shop registration` (`POST /request/register`) with manager and shop fields.
3. Use function `accept print-shop registration request` (`POST /request/accept/{id}`) authenticated as admin with `{id}={registrationRequestId}`.
4. Use function `retrieve manager print shop` (`GET /printshop`) authenticated as `{managerUsername}:{managerPassword}` to capture `{printShopID}`.
5. Use function `add print-shop employee` (`POST /printshops/{printShopID}/employees`) authenticated as manager with `{printShopID}` and JSON body `name={employeeName}`, `username={employeeUsername}`, and `password={employeePassword}` to create `{employeeID}`.
6. Use function `edit print-shop employee` (`PUT /printshops/{printShopID}/employees`) authenticated as manager with `{printShopID}` and JSON body `id={employeeID}`, `name={editedEmployeeName}`, `username={editedEmployeeUsername}`, and `password={editedEmployeePassword}` to update the employee.
7. Use function `delete print-shop employee` (`DELETE /printshops/{printShopID}/employees/{employeeID}`) authenticated as manager with `{employeeID}` to delete the employee when no longer needed.

Optional verification workflow:
1. Use function `list print-shop employees` (`GET /printshops/{printShopID}/employees`) authenticated as manager to inspect the roster after create/edit/delete.
2. Use function `retrieve print-shop statistics` (`GET /printshops/stats`) authenticated as manager to inspect pending, in-progress, finished count, employee count, and profit.

Existing-state shortcuts:
- Skip onboarding if manager and linked print shop already exist.
- Skip `add print-shop employee` for edit/delete if an equivalent employee already exists and `{employeeID}` is known.
- Direct database setup must preserve the employee-to-print-shop relationship and employee credentials for later employee-scoped functions.

Parameter and value bindings:
- `{printShopID}` from `retrieve manager print shop` scopes all employee endpoints.
- `{employeeID}` returned by `add print-shop employee` is reused in edit and delete.
- The edited employee username/password replace the credentials needed for employee-authenticated queue/document workflows.

Business result:
An employee account is created, can be updated, and can be deleted. Manager statistics reflect the current employee count and shop request state.

Constraints and invariants:
- Add requires non-null employee `name`, `username`, and `password`.
- Add rejects duplicate employee username.
- Edit and delete check employee existence, but do not verify that the employee belongs to `{printShopID}`.
- `retrieve print-shop statistics` is scoped by authenticated manager's linked shop, not by a path id.

Failure and exceptional cases:
- Failing function: `add print-shop employee`
  Failure condition: missing shop, missing required employee fields, or duplicate username.
  Why it fails: the controller checks print shop, body fields, and `employees.findByUsername`.
  Violated prerequisite or constraint: existing shop, complete employee data, unique employee username.
- Failing function: `edit print-shop employee`
  Failure condition: `{employeeID}` does not exist.
  Why it fails: `employees.findOne(editedEmp.getId())` returns null and the controller returns `success: false`.
  Violated prerequisite or constraint: existing employee id.
- Failing function: `delete print-shop employee`
  Failure condition: `{employeeID}` does not exist.
  Why it fails: `employees.findOne(eid)` returns null and the controller returns `success: false`.
  Violated prerequisite or constraint: existing employee id.

Implementation notes:
- The lack of `{printShopID}` ownership validation on edit/delete is a cross-shop safety issue.

### Behavior 7: Maintain a Print-Shop Price Table

Business goal:
Allow a manager to define and adjust prices used for budget calculation.

Domain context:
Budgeting depends on paper copy ranges, ring binding, cover, and stapling prices stored in each print shop's price table.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `register admin` (`POST /admin/register`) with admin JSON body.
2. Use function `request print-shop registration` (`POST /request/register`) with manager and shop fields.
3. Use function `accept print-shop registration request` (`POST /request/accept/{id}`) authenticated as admin with `{id}={registrationRequestId}`.
4. Use function `retrieve manager print shop` (`GET /printshop`) authenticated as `{managerUsername}:{managerPassword}` to capture `{printShopID}`.
5. Use function `add paper price rows` (`POST /printshops/{id}/pricetable/papers`) authenticated as manager with `{id}={printShopID}` and `PaperTableItem` body `colors={colors}`, `infLim={infLim}`, `supLim={supLim}`, and relevant `priceA4SIMPLEX`, `priceA4DUPLEX`, `priceA3SIMPLEX`, `priceA3DUPLEX` values.
6. Use function `add ring binding price` (`POST /printshops/{id}/pricetable/rings`) authenticated as manager with `{id}={printShopID}` and `RingTableItem` body `ringType={ringType}`, `infLim={infLim}`, `supLim={supLim}`, `price={ringPrice}`.
7. Use function `add cover price` (`POST /printshops/{id}/pricetable/covers`) authenticated as manager with `{id}={printShopID}` and `CoverTableItem` body `coverType={coverType}`, `priceA4={coverA4Price}`, `priceA3={coverA3Price}`.
8. Use function `edit stapling price` (`PUT /printshops/{printShopID}/pricetable/editstapling`) authenticated as manager with `{printShopID}` and body `{staplingPrice}`.
9. To change existing entries, use `edit paper price rows` (`PUT /printshops/{id}/pricetable/papers`), `edit ring binding price` (`PUT /printshops/{id}/pricetable/rings`), or `edit cover price` (`PUT /printshops/{id}/pricetable/covers`) with the same key fields and replacement price values.
10. To remove entries, use `delete paper price rows` (`POST /printshops/{id}/pricetable/deletepaper`), `delete ring binding price` (`POST /printshops/{id}/pricetable/deletering`), or `delete cover price` (`POST /printshops/{id}/pricetable/deletecover`) with the same key fields.

Optional verification workflow:
1. Use function `retrieve print-shop price table` (`GET /printshops/{id}/pricetable`) authenticated as manager or consumer with `{id}={printShopID}` to inspect normalized price table.
2. Use function `calculate consumer print-request budgets` (`POST /consumer/budget`) with a matching schema and documents to observe price impact in a budget.

Existing-state shortcuts:
- Skip onboarding if `{printShopID}` already exists and manager credentials are available.
- Skip add steps before edit/delete if equivalent price-table entries already exist, either through prior API calls or direct database setup.
- Direct database setup must use the same generated price-table item keys expected by `RangePaperItem`, `BindingItem`, and `CoverItem`.

Parameter and value bindings:
- `{printShopID}` from `retrieve manager print shop` is reused in all price-table paths.
- Edit and delete functions consume the same item-defining fields used by add functions: paper `colors/infLim/supLim/format/sides`, ring `ringType/infLim/supLim`, and cover `coverType/format`.
- Stapling uses fixed key `BINDING,STAPLING,0,0`; the body price string is parsed as a float.

Business result:
The shop's price table contains or no longer contains the selected paper, ring, cover, and stapling price entries. Future budget calculations use the current price table; existing calculated request costs are not automatically recomputed.

Constraints and invariants:
- Add and edit are upserts for paper, ring, and cover entries.
- Delete of a non-existing key still returns success when the print shop exists.
- No controller-level validation prevents negative, overlapping, or inconsistent ranges/prices.
- Budget calculation treats missing required price support as unsatisfied for a shop.

Failure and exceptional cases:
- Failing function: any price-table add/edit/delete function.
  Failure condition: `{printShopID}` does not exist.
  Why it fails: `printshops.findOne(id)` returns null and the controller returns `success: false`.
  Violated prerequisite or constraint: existing print shop.
- Failing function: `edit stapling price`
  Failure condition: body is not parseable as a float.
  Why it fails: `Float.parseFloat(newStaplingPrice)` throws outside structured response handling.
  Violated prerequisite or constraint: numeric stapling price.

Implementation notes:
- Swagger does not fully describe the required request bodies. The implementation-specific table DTOs are authoritative.

### Behavior 8: Manage Saved Consumer Printing Schemas

Business goal:
Allow consumers to save, update, list, and soft-delete reusable print configurations.

Domain context:
Printing schemas are referenced by print-request document specs and determine paper, binding, and cover choices used in budget calculation.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `register consumer` (`POST /consumer/register`) with consumer fields to create `{consumerID}`.
2. Use function `add consumer printing schema` (`POST /consumer/{consumerID}/printingschemas`) authenticated as the consumer with `{consumerID}` and JSON body `name={schemaName}`, `paperSpecs={paperSpecs}`, optional `bindingSpecs={bindingSpecs}`, and optional `coverSpecs={coverSpecs}` to create `{printingSchemaID}`.
3. Use function `edit consumer printing schema` (`PUT /consumer/{consumerID}/printingschemas/{printingSchemaID}`) authenticated as the consumer with replacement schema body to update fields.
4. Use function `soft-delete consumer printing schema` (`DELETE /consumer/{consumerID}/printingschemas/{printingSchemaID}`) authenticated as the consumer to mark it deleted.

Optional verification workflow:
1. Use function `list consumer printing schemas` (`GET /consumer/{consumerID}/printingschemas`) authenticated as a consumer to inspect saved schemas.

Existing-state shortcuts:
- Skip `register consumer` if `{consumerID}` exists.
- Skip `add consumer printing schema` before edit/delete if `{printingSchemaID}` already exists.
- Direct database setup must link the schema to the consumer if list semantics matter.

Parameter and value bindings:
- `{consumerID}` returned by `register consumer` is reused in all printing-schema paths.
- `{printingSchemaID}` returned by `add consumer printing schema` is reused by edit, delete, and print-budget document spec references.
- `paperSpecs`, `bindingSpecs`, and `coverSpecs` later drive budget compatibility with shop price-table entries.

Business result:
A reusable printing schema is saved, can be changed, and is soft-deleted by setting its deleted flag rather than removing the database row.

Constraints and invariants:
- Endpoints require `ROLE_USER`, but the implementation does not verify that path `{consumerID}` matches the authenticated principal.
- Delete does not remove the row.
- Listing can return soft-deleted schemas unless model-level filtering exists; the controller directly returns the consumer's set.

Failure and exceptional cases:
- Failing function: `add consumer printing schema`
  Failure condition: `{consumerID}` does not exist.
  Why it fails: the controller saves the schema before dereferencing `c.addPrintingSchema`, so a null consumer can cause an exception after orphan-like persistence.
  Violated prerequisite or constraint: existing consumer id.
- Failing function: `edit consumer printing schema`
  Failure condition: `{printingSchemaID}` does not exist.
  Why it fails: `printingSchemas.findOne(psid)` returns null and subsequent setter calls dereference null.
  Violated prerequisite or constraint: existing schema id.
- Failing function: `soft-delete consumer printing schema`
  Failure condition: schema is already deleted.
  Why it fails: the controller returns a malformed success-like JSON property `"false": true` instead of `success: false`.
  Violated prerequisite or constraint: schema must not already be deleted.

Implementation notes:
- The path consumer id is mostly used as a lookup key, not as an authorization boundary.

### Behavior 9: Create a Consumer Print Request and Calculate Budgets

Business goal:
Create a draft print request from uploaded PDFs and obtain per-shop budget options.

Domain context:
Consumers choose saved schemas and candidate print shops before submitting a paid request.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `register consumer` (`POST /consumer/register`) with consumer fields to create `{consumerID}`.
2. Use function `register admin` (`POST /admin/register`) with admin JSON body.
3. Use function `request print-shop registration` (`POST /request/register`) with manager and shop fields.
4. Use function `accept print-shop registration request` (`POST /request/accept/{id}`) authenticated as admin with `{id}={registrationRequestId}`.
5. Use function `retrieve manager print shop` (`GET /printshop`) authenticated as manager to capture `{printShopID}`.
6. Use function `add consumer printing schema` (`POST /consumer/{consumerID}/printingschemas`) authenticated as the consumer with schema body to create `{printingSchemaID}`.
7. Use function `calculate consumer print-request budgets` (`POST /consumer/budget`) authenticated as the consumer with multipart PDF files and request part `printRequest={...}` where `printshops=[{printShopID}]` and `files` maps each uploaded filename to `specs` entries containing `id={printingSchemaID}` and optional `from={firstPage}`, `to={lastPage}`.

Optional verification workflow:
1. Use function `retrieve print-shop price table` (`GET /printshops/{id}/pricetable`) with `{id}={printShopID}` to inspect prices used for calculation.
2. Use function `list consumer pending print requests` (`GET /consumer/requests`) after later submission to inspect pending requests; before submission the draft may not appear as pending.

Existing-state shortcuts:
- Skip setup functions when an authenticated consumer, candidate `{printShopID}`, and `{printingSchemaID}` already exist.
- Direct database setup can create `PrintRequest`, `Document`, and `DocumentSpec` rows, but to calculate budgets through API the multipart files and `printRequest` part must still be sent.

Parameter and value bindings:
- `{consumerID}` scopes the saved schema creation.
- `{printingSchemaID}` is referenced in each document spec inside the multipart `printRequest` JSON.
- Uploaded PDF filenames must match keys in `printRequest.files`; the implementation maps saved document ids by original filename.
- `{printShopID}` in `printRequest.printshops` determines which shops receive budget calculations.
- The returned `{printRequestID}` is reused by submit functions.

Business result:
A draft `PrintRequest` exists with uploaded PDF `Document` rows, `DocumentSpec` rows linked to printing schemas, and a returned budget map by print-shop id.

Constraints and invariants:
- Only files with `.pdf` extension are handled.
- Swagger documents form `requestJSON`, but the implementation requires `@RequestPart("printRequest")`.
- Malformed JSON, missing files, missing schemas, or missing shops can produce exceptions or unusable budgets rather than structured failures.
- Budget values reflect the current price table at calculation time and are not automatically recomputed at submission.

Failure and exceptional cases:
- Failing function: `calculate consumer print-request budgets`
  Failure condition: missing or malformed multipart `printRequest` part.
  Why it fails: the controller directly parses the part into a map and casts fields.
  Violated prerequisite or constraint: valid multipart request shape.
- Failing function: `calculate consumer print-request budgets`
  Failure condition: referenced `{printingSchemaID}` or `{printShopID}` does not exist.
  Why it fails: repository lookups return null and downstream budget/spec logic cannot satisfy the request reliably.
  Violated prerequisite or constraint: existing schema and print shop ids.

Implementation notes:
- The endpoint persists the draft before all parsing and calculation work completes, so malformed later input can leave partial state.

### Behavior 10: Submit, Pay, Process, and Complete a Print Request

Business goal:
Move a print request from budgeted draft into a shop queue, process it, finish it, deliver it, and settle money.

Domain context:
This is the core operational workflow: consumer orders printing, shop staff execute it, and request status moves through `PENDING`, `IN_PROGRESS`, `FINISHED`, and `LIFTED`.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `register consumer` (`POST /consumer/register`) with consumer fields to create `{consumerID}`.
2. Use function `register admin` (`POST /admin/register`) with admin JSON body.
3. Use function `request print-shop registration` (`POST /request/register`) with manager and shop fields.
4. Use function `accept print-shop registration request` (`POST /request/accept/{id}`) authenticated as admin with `{id}={registrationRequestId}`.
5. Use function `retrieve manager print shop` (`GET /printshop`) authenticated as manager to capture `{printShopID}`.
6. Use function `add print-shop employee` (`POST /printshops/{printShopID}/employees`) authenticated as manager with employee JSON body to create employee credentials.
7. Use function `add consumer printing schema` (`POST /consumer/{consumerID}/printingschemas`) authenticated as consumer to create `{printingSchemaID}`.
8. Use function `calculate consumer print-request budgets` (`POST /consumer/budget`) authenticated as consumer with multipart PDFs and `printRequest` JSON referencing `{printShopID}` and `{printingSchemaID}` to create `{printRequestID}` and budget `{budget}`.
9. For ProxyPrint-balance payment, use function `load consumer balance from PayPal IPN` (`POST /paypal/ipn/consumer/{consumerID}`) with `payment_status=Completed` and `mc_gross` at least `{budget}`.
10. For ProxyPrint-balance payment, use function `submit print request with ProxyPrint balance` (`POST /consumer/printrequest/{printRequestID}/submit`) authenticated as consumer with JSON body `printshopID={printShopID}`, `budget={budget}`, and `paymentMethod=PROXYPRINT_PAYMENT`.
11. For PayPal request payment instead, use function `submit print request for PayPal payment` (`POST /consumer/printrequest/{printRequestID}/submit`) authenticated as consumer with JSON body `printshopID={printShopID}`, `budget={budget}`, and `paymentMethod={non-PROXYPRINT payment value, typically PAYPAL_PAYMENT}`, then use function `confirm PayPal print-request payment` (`POST /paypal/ipn/{printRequestID}`) with IPN fields `payment_status=Completed`, `mc_gross={budget}`, `payer_email={payerEmail}`, and `txn_id={transactionID}`.
12. Use function `start processing print request` (`POST /printshops/requests/{id}`) authenticated as employee or manager with `{id}={printRequestID}` while status is `PENDING` to move it to `IN_PROGRESS`.
13. Use function `finish print request` (`POST /printshops/requests/{id}`) authenticated as employee or manager with `{id}={printRequestID}` while status is `IN_PROGRESS` to move it to `FINISHED`.
14. Use function `mark print request lifted` (`POST /printshops/requests/{id}`) authenticated as employee or manager with `{id}={printRequestID}` while status is `FINISHED` to move it to `LIFTED` and settle shop share.

Optional verification workflow:
1. Use function `list print-shop active queue` (`GET /printshops/requests`) authenticated as employee/manager after submission to inspect pending or in-progress requests.
2. Use function `retrieve print-shop request detail` (`GET /printshops/requests/{id}`) authenticated as employee/manager with `{id}={printRequestID}` to inspect documents and specs.
3. Use function `download assigned document PDF` (`GET /documents/{id}`) authenticated as employee with a document id from request detail to download the PDF.
4. Use function `list print-shop finished requests` (`GET /printshops/satisfied`) after finishing to inspect finished requests.
5. Use function `list print-shop lifted request history` (`GET /printshops/history`) after lifting to inspect history.
6. Use function `list consumer pending print requests` (`GET /consumer/requests`) and `list consumer satisfied requests` (`GET /consumer/satisfied`) authenticated as consumer to inspect consumer-side queues.

Existing-state shortcuts:
- Skip account, shop, employee, schema, and budget setup when equivalent state already exists and ids are known.
- If a draft request with documents/specs/budget already exists, skip `calculate consumer print-request budgets` and reuse `{printRequestID}`, `{printShopID}`, and `{budget}`.
- Direct database setup can create a `PrintRequest` in a specific status for testing individual transitions, but the status and shop/consumer/document links must satisfy the transition function being called.

Parameter and value bindings:
- `{printRequestID}` returned by budget creation is reused by submit, payment confirmation, queue detail, status transitions, and cancellation.
- `{budget}` returned for `{printShopID}` should be reused in submit and PayPal IPN `mc_gross`; the implementation trusts submitted `budget` on submit.
- `{printShopID}` used in budget calculation must match `printshopID` in submit and the employee's/manager's scoped shop for later queue operations.
- For PayPal confirmation, `mc_gross` must equal `PrintRequest.cost`, `payment_status` must be `Completed`, and the print request must belong to the consumer.

Business result:
The request becomes queued, processed, finished, and lifted. For ProxyPrint balance, consumer balance is deducted at submit, admin balance is credited, and on lifting 90% of cost is transferred from admin to print shop. For PayPal request payment, IPN confirmation moves the request to pending and lifting attempts PayPal share transfer to the shop.

Constraints and invariants:
- Status transitions are sequential and implemented by repeated `POST /printshops/requests/{id}` calls.
- Shop queue functions scope requests by the authenticated employee's print shop.
- `changeStatusPrintShopRequests` looks up `Employee e = employees.findByUsername(principal.getName())`; manager principals may fail if no corresponding `Employee` row exists despite `ROLE_MANAGER` being allowed.
- ProxyPrint submit checks consumer balance but does not recompute or verify budget against price table.
- Existing calculated costs are not updated if the price table changes.

Failure and exceptional cases:
- Failing function: `submit print request with ProxyPrint balance`
  Failure condition: consumer balance is lower than submitted `budget`.
  Why it fails: the controller checks `consumer.getBalance().getMoneyAsDouble() < cost` and returns `success: false`.
  Violated prerequisite or constraint: sufficient ProxyPrint balance.
- Failing function: `submit print request with ProxyPrint balance`
  Failure condition: `{printRequestID}` or `{printShopID}` does not exist.
  Why it fails: `printRequests.findOne(prid)` or `printShops.findOne(pshopID)` returns null.
  Violated prerequisite or constraint: existing draft request and print shop.
- Failing function: `confirm PayPal print-request payment`
  Failure condition: IPN amount does not equal request cost, status is not completed, consumer link is missing, or request is not in the consumer's request set.
  Why it fails: the controller only sets `PENDING` when all those conditions pass.
  Violated prerequisite or constraint: valid completed PayPal payment for the exact request cost.
- Failing function: `start processing print request`
  Failure condition: request is not `PENDING` or does not belong to authenticated staff's shop.
  Why it fails: scoped lookup or status branch fails and returns `success: false`.
  Violated prerequisite or constraint: pending request in staff's shop.
- Failing function: `finish print request`
  Failure condition: request is not `IN_PROGRESS`.
  Why it fails: only the `IN_PROGRESS` branch sets `FINISHED`.
  Violated prerequisite or constraint: in-progress request.
- Failing function: `mark print request lifted`
  Failure condition: request is not `FINISHED` or PayPal shop-share transfer fails.
  Why it fails: only finished requests can be lifted; PayPal branch returns `success: false` on share-transfer failure.
  Violated prerequisite or constraint: finished request and successful settlement path.

Implementation notes:
- Notifications are sent on processing start, finish, and PayPal confirmation.
- `submit print request for PayPal payment` sets payment type but leaves status `NOT_PAYED` until IPN confirmation.

### Behavior 11: Submit an External Print Document

Business goal:
Allow an external platform to upload PDFs, receive a print request id, and later calculate budgets through a ProxyPrint consumer.

Domain context:
This integration path creates documents first with default specs, then attaches a consumer and candidate shops during budget calculation.

Starting point:
No prior service state for external request creation. Budget calculation needs a consumer and print shop.

Required execution workflow:
1. Use function `create external print request` (`POST /printdocument`) with multipart PDF files to create `{printRequestID}` and default document specs.
2. Use function `register consumer` (`POST /consumer/register`) with consumer fields to create the consumer who will own/price the request.
3. Use function `register admin` (`POST /admin/register`) with admin JSON body.
4. Use function `request print-shop registration` (`POST /request/register`) with manager and shop fields.
5. Use function `accept print-shop registration request` (`POST /request/accept/{id}`) authenticated as admin with `{id}={registrationRequestId}`.
6. Use function `retrieve manager print shop` (`GET /printshop`) authenticated as manager to capture `{printShopID}`.
7. Use function `calculate external print-request budget` (`POST /printdocument/{id}/budget`) authenticated as consumer with `{id}={printRequestID}` and body JSON array `[{printShopID}]` to attach the consumer and calculate budgets.

Optional verification workflow:
1. Use function `retrieve external print-request documents` (`GET /printdocument/{id}`) with `{id}={printRequestID}` to inspect uploaded documents.
2. Use function `submit print request with ProxyPrint balance` or `submit print request for PayPal payment` after budget calculation to continue into the normal fulfillment behavior.

Existing-state shortcuts:
- Skip `create external print request` only if an external `PrintRequest` with documents and default specs already exists.
- Skip consumer/shop setup when ids already exist.
- Direct database setup must preserve document-file records and default specs if equivalent to `create external print request`.

Parameter and value bindings:
- `{printRequestID}` returned by `create external print request` is reused in retrieval, budget calculation, and later submission.
- `{printShopID}` from onboarding is included in the JSON array body for external budget calculation.
- The consumer authenticated for `calculate external print-request budget` becomes the request's consumer.

Business result:
An externally created print request has PDFs, default print specs, a consumer owner, and budget options for selected print shops.

Constraints and invariants:
- External creation is not authenticated.
- Default specs are hardcoded to an A4 duplex black-and-white paper item with stapling.
- Budget calculation is secured with `ROLE_USER`.

Failure and exceptional cases:
- Failing function: `retrieve external print-request documents`
  Failure condition: `{printRequestID}` does not exist.
  Why it fails: `printRequests.findOne(id)` returns null and the controller returns `success: false`.
  Violated prerequisite or constraint: existing external request id.
- Failing function: `calculate external print-request budget`
  Failure condition: `{printRequestID}` does not exist.
  Why it fails: the controller dereferences `printRequest` after lookup and can throw rather than return a structured failure.
  Violated prerequisite or constraint: existing print request.

Implementation notes:
- This flow bypasses consumer schema selection by creating a new default `PrintingSchema`.

### Behavior 12: Cancel Print Requests and Inspect Request Histories

Business goal:
Allow consumers or shops to cancel pending work and allow both sides to inspect current and historical request lists.

Domain context:
Cancellation is limited to pending requests. Consumer cancellation refunds ProxyPrint balance through the admin account; shop cancellation removes relationships and notifies the consumer with a motive.

Starting point:
No prior service state.

Required execution workflow:
1. Establish a submitted pending print request by following Behavior 10 through `submit print request with ProxyPrint balance` or through PayPal submit plus `confirm PayPal print-request payment`, producing `{printRequestID}` with status `PENDING`.
2. For consumer cancellation, use function `cancel consumer pending print request` (`DELETE /consumer/requests/cancel/{id}`) authenticated as the consumer with `{id}={printRequestID}`.
3. For shop cancellation instead, use function `cancel print-shop pending request` (`POST /printshops/requests/cancel/{id}`) authenticated as employee/manager with `{id}={printRequestID}` and body `{motive}`.

Optional verification workflow:
1. Use function `list consumer pending print requests` (`GET /consumer/requests`) authenticated as consumer before and after cancellation.
2. Use function `list print-shop active queue` (`GET /printshops/requests`) authenticated as employee/manager before and after cancellation.
3. Use function `list consumer satisfied requests` (`GET /consumer/satisfied`) authenticated as consumer for finished/lifted requests.
4. Use function `list print-shop finished requests` (`GET /printshops/satisfied`) and `list print-shop lifted request history` (`GET /printshops/history`) authenticated as employee/manager for shop-side history.

Existing-state shortcuts:
- Skip setup if an existing pending request is already linked to the authenticated consumer and print shop.
- Direct database setup must ensure status `PENDING`, consumer relation, print-shop relation, and cost/balance state for refund behavior.

Parameter and value bindings:
- `{printRequestID}` created by budget/submit/payment workflow is reused in cancellation and list/detail workflows.
- Consumer cancellation requires the authenticated consumer to own the request.
- Shop cancellation requires the authenticated staff's print shop to own the request.
- Shop cancellation body `{motive}` is used in the consumer notification.

Business result:
The pending request is removed from the shop and consumer request collections. Consumer cancellation refunds the request cost from admin balance to consumer balance. Shop cancellation notifies the consumer with the supplied motive.

Constraints and invariants:
- Only `PENDING` requests can be canceled by either side.
- Cancellation removes relationships but does not explicitly delete the `PrintRequest` row in the shown controller code.
- Consumer cancellation assumes an admin record exists for refund accounting.

Failure and exceptional cases:
- Failing function: `cancel consumer pending print request`
  Failure condition: request does not belong to consumer or is not pending.
  Why it fails: scoped lookup fails or status check is not `PENDING`.
  Violated prerequisite or constraint: authenticated consumer owns a pending request.
- Failing function: `cancel print-shop pending request`
  Failure condition: request does not belong to staff's shop or is not pending.
  Why it fails: scoped lookup fails or status check is not `PENDING`.
  Violated prerequisite or constraint: authenticated staff's shop owns a pending request.
- Failing function: `list consumer pending print requests`
  Failure condition: authenticated principal cannot be resolved as a consumer.
  Why it fails: controller returns `success: false`.
  Violated prerequisite or constraint: valid consumer authentication.

Implementation notes:
- Shop cancellation sends notification but does not perform the same refund logic visible in consumer cancellation.

### Behavior 13: Review a Print Shop

Business goal:
Allow consumers to create, update, delete, and read reviews for print shops.

Domain context:
Reviews contribute to a print shop's rating and support consumer trust/discovery.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `register consumer` (`POST /consumer/register`) with consumer fields.
2. Use function `register admin` (`POST /admin/register`) with admin JSON body.
3. Use function `request print-shop registration` (`POST /request/register`) with manager and shop fields.
4. Use function `accept print-shop registration request` (`POST /request/accept/{id}`) authenticated as admin with `{id}={registrationRequestId}`.
5. Use function `retrieve manager print shop` (`GET /printshop`) authenticated as manager to capture `{printShopID}`.
6. Use function `add print-shop review` (`POST /printshops/{id}/reviews`) authenticated as consumer with `{id}={printShopID}` and JSON body `review={reviewText}`, `rating={rating}` to create `{reviewId}`.
7. Use function `edit print-shop review` (`PUT /printshops/{printShopId}/reviews/{reviewId}`) authenticated as the same consumer with `{printShopId}={printShopID}`, `{reviewId}`, query/form values `review={editedReviewText}` and `rating={editedRating}`.
8. Use function `delete print-shop review` (`DELETE /printshops/{printShopId}/reviews/{reviewId}`) authenticated as the same consumer with `{printShopId}={printShopID}` and `{reviewId}`.

Optional verification workflow:
1. Use function `list print-shop reviews` (`GET /printshops/{id}/reviews`) authenticated as manager, employee, or consumer with `{id}={printShopID}` to inspect reviews.
2. Use function `retrieve print shop by id` (`GET /printshops/{id}`) with `{id}={printShopID}` to inspect rating after add/edit.

Existing-state shortcuts:
- Skip account/shop setup when a consumer and `{printShopID}` already exist.
- Skip add before edit/delete if `{reviewId}` exists and belongs to the authenticated consumer.
- Direct database setup must link review to the target shop and consumer for ownership checks and rating semantics.

Parameter and value bindings:
- `{printShopID}` is reused across add, edit, delete, and list.
- `{reviewId}` returned by add is reused by edit and delete.
- The authenticated username for edit/delete must equal `review.consumer.username`.
- `rating` is parsed as an integer and used to update shop average rating on add/edit.

Business result:
A consumer review is created, can be changed, and can be removed from the shop. Add/edit recalculates shop rating; delete removes the review from the shop but does not visibly recalculate rating in the controller.

Constraints and invariants:
- Edit/delete are author-only.
- Add does not require that the consumer has completed an order with the shop.
- Rating range is not validated in controller code.
- Review edit reads parameters from `WebRequest`, not a JSON body.

Failure and exceptional cases:
- Failing function: `add print-shop review`
  Failure condition: `{printShopID}` does not exist.
  Why it fails: controller returns 404.
  Violated prerequisite or constraint: existing print shop.
- Failing function: `edit print-shop review`
  Failure condition: review does not exist, shop does not exist, or authenticated consumer is not the author.
  Why it fails: controller returns 404 for missing resources and 401 for author mismatch.
  Violated prerequisite or constraint: existing review in shop and author ownership.
- Failing function: `delete print-shop review`
  Failure condition: authenticated consumer is not the author.
  Why it fails: controller returns 401.
  Violated prerequisite or constraint: review ownership.

Implementation notes:
- Deleting a review does not call `updatePrintShopRating`, so rating can become stale.

### Behavior 14: Deliver and Manage Consumer Notifications

Business goal:
Allow the system or clients to send notifications, consumers to stream/list them, and consumers to mark or remove them.

Domain context:
Notifications communicate payment confirmation, processing progress, finished requests, cancellation motives, and balance-load outcomes.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `register consumer` (`POST /consumer/register`) with consumer fields to create `{consumerID}` and `{consumerUsername}`.
2. Use function `send notification to consumer` (`POST /consumer/{id}/notify`) with `{id}={consumerID}` and query/form `message={message}` to create a notification.
3. Use function `mark notification read` (`PUT /notifications/{notificationId}`) authenticated as a consumer with `{notificationId}` from notification listing or direct state to mark one notification read.
4. Use function `delete notification` (`DELETE /notifications/{notificationId}`) authenticated as a consumer with `{notificationId}` to remove one notification.
5. Use function `mark all consumer notifications read` (`PUT /consumer/{username}/notifications`) authenticated as a consumer with `{username}={consumerUsername}` to mark all notifications read.
6. Use function `delete all consumer notifications` (`DELETE /consumer/{username}/notifications`) authenticated as a consumer with `{username}={consumerUsername}` to remove all notifications.

Optional verification workflow:
1. Use function `subscribe to notification stream` (`GET /consumer/subscribe`) with query `username={consumerUsername}` and `password={consumerPassword}` to establish an SSE stream.
2. Use function `list consumer notifications` (`GET /consumer/notifications`) authenticated as the consumer to inspect notifications and capture `{notificationId}`.

Existing-state shortcuts:
- Skip `register consumer` if the consumer exists.
- Skip `send notification to consumer` if an equivalent notification already exists and `{notificationId}` is known.
- Direct database setup can insert notifications, but consumer ownership must match the listing/mark/delete action.

Parameter and value bindings:
- `{consumerID}` from registration is reused in `send notification to consumer`.
- `{consumerUsername}` and `{consumerPassword}` are reused by `subscribe to notification stream`.
- `{notificationId}` from listing or direct state is reused in single-notification mark/delete functions.
- `{username}` path value in bulk mark/delete must identify the target consumer; the implementation does not require it to match the authenticated principal.

Business result:
Notifications can be delivered, streamed, listed, marked as read, and deleted individually or in bulk.

Constraints and invariants:
- `send notification to consumer` is not secured in controller code.
- Single notification mark/delete do not verify ownership in the controller.
- Bulk notification mark/delete look up by path username and do not verify authenticated principal matches the path.
- Subscribe uses explicit username/password query parameters rather than Spring principal authentication.

Failure and exceptional cases:
- Failing function: `subscribe to notification stream`
  Failure condition: unknown username or wrong password.
  Why it fails: controller returns 404 for missing consumer and 401 for password mismatch.
  Violated prerequisite or constraint: valid consumer credentials.
- Failing function: `send notification to consumer`
  Failure condition: `{consumerID}` does not exist.
  Why it fails: controller dereferences `consumers.findOne(id).getUsername()`.
  Violated prerequisite or constraint: existing consumer id.
- Failing function: `mark all consumer notifications read`
  Failure condition: `{username}` does not identify a consumer.
  Why it fails: controller dereferences null consumer.
  Violated prerequisite or constraint: existing consumer username.

Implementation notes:
- Many print-request and payment functions create notifications as side effects, so manual notification sending is not the only creation path.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Directly Create, Update, or Delete a Print Shop

Priority:
Critical domain gap

Expected business goal:
Admins or managers would reasonably expect to edit shop identity, address, coordinates, NIF, logo, or remove a shop from the marketplace.

Why it is unsupported:
Print shops can be created only by accepting a registration request or through seed data. There is no function to update or delete an existing shop.

Existing functions considered:
- `request print-shop registration`: creates a pending request, not an existing shop update.
- `accept print-shop registration request`: creates a new manager and print shop from a request, not update/delete.
- `retrieve manager print shop`: reads a manager's shop only.
- `retrieve print shop by id`: reads one shop only.
- `list public print shops`: lists shops only.

Missing capability:
Missing `PUT/PATCH /printshops/{id}` and `DELETE /printshops/{id}` or equivalent state-transition endpoints with ownership/admin checks.

Proof that function composition is insufficient:
Creating a new registration request and accepting it creates a second shop and manager; it cannot preserve the same `{printShopID}`, existing price table, reviews, employees, requests, history, or balances. Direct database editing is not an API behavior.

Evidence from existing functions/source:
- `PrintShopController` exposes only list/read/nearest/price-table/request queue functions.
- `RegisterRequestController.acceptRequest` always constructs a new `PrintShop`.

Business impact:
Incorrect shop data cannot be repaired through API, and shops cannot be safely retired from the marketplace.

### Missing Behavior 2: Secure Ownership Enforcement for Consumer-Scoped Resources

Priority:
Critical domain gap

Expected business goal:
A consumer should only manage their own printing schemas and notifications.

Why it is unsupported:
Several endpoints use path `consumerID` or `username` but do not compare it to the authenticated principal.

Existing functions considered:
- `list consumer printing schemas`: returns schemas for any path consumer id under any `ROLE_USER`.
- `add consumer printing schema`: attaches a schema to any path consumer id.
- `edit consumer printing schema`: updates any schema id without checking ownership.
- `soft-delete consumer printing schema`: deletes any schema id without checking ownership.
- `mark all consumer notifications read`: modifies notifications for path username.
- `delete all consumer notifications`: removes notifications for path username.

Missing capability:
Missing ownership checks binding authenticated principal to `{consumerID}`, `{username}`, `{printingSchemaID}`, and `{notificationId}`.

Proof that function composition is insufficient:
No existing function can assert or enforce that the caller owns the path id before mutation. Listing current profile does not constrain later path-scoped calls.

Evidence from existing functions/source:
- `PrintingSchemaController` uses `consumers.findOne(id)` and `printingSchemas.findOne(psid)` directly.
- `ConsumerController.readAllNotifications` and `deleteAllNotifications` use path username directly.

Business impact:
Users can read or mutate other users' saved schemas and notifications if they know ids or usernames.

### Missing Behavior 3: Transactionally Safe Print Request Creation

Priority:
Critical domain gap

Expected business goal:
Budget creation should either fully create a valid draft with documents/specs/budgets or create nothing.

Why it is unsupported:
The budget endpoint saves the `PrintRequest`, documents, and specs progressively before all parsing and validation completes.

Existing functions considered:
- `calculate consumer print-request budgets`: creates partial state while parsing multipart request.
- `submit print request with ProxyPrint balance`: can only submit an existing draft; it cannot repair missing documents/specs.
- `cancel consumer pending print request`: applies only pending submitted requests, not invalid drafts.

Missing capability:
Missing validation-before-persistence or transactional rollback for malformed multipart JSON, missing files, unknown schemas, and unknown shops.

Proof that function composition is insufficient:
Once partial draft state is persisted, no delete-draft or repair endpoint exists. Re-running budget creates another request rather than atomically replacing the partial one.

Evidence from existing functions/source:
- `PrintRequestController.calcBudgetForPrintRequest` saves `new PrintRequest()` before parsing `printRequest`, processing files, storing specs, and calculating budgets.

Business impact:
Bad requests can leave orphaned drafts, documents, or specs, weakening request integrity and later operations.

### Missing Behavior 4: Recalculate or Lock Budgets at Submission

Priority:
Important robustness gap

Expected business goal:
The submitted cost should match the selected shop's current price table and uploaded document specs.

Why it is unsupported:
Submission trusts the request body `budget` and does not recompute or compare it to the budget returned by calculation.

Existing functions considered:
- `calculate consumer print-request budgets`: calculates budgets but does not lock them.
- `submit print request with ProxyPrint balance`: accepts caller-supplied `budget`.
- `submit print request for PayPal payment`: also accepts caller-supplied `budget`.
- Price-table edit functions can change prices after budget calculation.

Missing capability:
Missing server-side budget revalidation, persisted quote id, quote expiry, or immutable quote binding.

Proof that function composition is insufficient:
Calling budget before submit does not force the submitted body to reuse the returned value. Price edits between budget and submit are not detected, and callers can submit arbitrary `budget`.

Evidence from existing functions/source:
- `PrintRequestController.finishAndSubmitPrintRequest` reads `budget` from request JSON and sets `printRequest.setCost(cost)`.

Business impact:
Consumers can underpay, shops can receive stale pricing, and accounting can become inconsistent.

### Missing Behavior 5: Safe Review Eligibility and Rating Consistency

Priority:
Important robustness gap

Expected business goal:
Only consumers who completed an order with a shop should review it, and rating should remain correct after edits and deletes.

Why it is unsupported:
Review creation only requires an authenticated consumer and existing shop. Delete removes a review but does not recalculate shop rating.

Existing functions considered:
- `add print-shop review`: does not check completed request history.
- `edit print-shop review`: author-only and recalculates rating.
- `delete print-shop review`: author-only but does not update rating.
- `list consumer satisfied requests`: can show completed work but is not used for review eligibility.

Missing capability:
Missing order-completion eligibility check and rating recalculation on delete.

Proof that function composition is insufficient:
Manually checking satisfied requests before adding a review is advisory only; `add print-shop review` will still accept ineligible callers. No existing function recomputes rating after delete.

Evidence from existing functions/source:
- `ReviewController.addPrintShopReview` saves review and updates rating without request-history checks.
- `ReviewController.deletePrintShopReview` removes review and saves shop without `updatePrintShopRating`.

Business impact:
Shop ratings can be gamed and can become stale after review deletion.

### Missing Behavior 6: End-to-End PayPal Verification Enforcement

Priority:
Important robustness gap

Expected business goal:
PayPal IPN callbacks should mutate money/request state only when the IPN is verified and semantically valid.

Why it is unsupported:
The controllers instantiate `IPNMessage` and call `validate()`, but balance-load mutation does not require `isIpnVerified`. Print-request confirmation logs verification but also does not include it in the core condition.

Existing functions considered:
- `load consumer balance from PayPal IPN`: credits balance on `payment_status=Completed`.
- `confirm PayPal print-request payment`: requires cost/status/request checks but not verified IPN status in the condition.

Missing capability:
Missing enforced verified-IPN gate and robust transaction id/idempotency checks.

Proof that function composition is insufficient:
No other function can retrospectively distinguish verified from unverified IPN mutations or prevent duplicate callback effects.

Evidence from existing functions/source:
- `PayPalController` stores `boolean isIpnVerified = ipnlistener.validate()` but does not gate balance crediting on it.

Business impact:
Payment and balance state can be changed by unverified or duplicate callback-like requests.

### Missing Behavior 7: Retrieve Draft Print Requests or Delete Invalid Drafts

Priority:
API ergonomics gap

Expected business goal:
Consumers should be able to inspect or delete draft print requests created during budgeting before submission.

Why it is unsupported:
Consumer listing only returns pending requests; external document retrieval lists documents but not general consumer drafts.

Existing functions considered:
- `calculate consumer print-request budgets`: creates draft and returns id.
- `list consumer pending print requests`: filters `PENDING`, excluding unsent drafts and `NOT_PAYED` requests.
- `retrieve print-shop request detail`: is shop-scoped and requires submitted queue membership.
- `cancel consumer pending print request`: only cancels `PENDING`.

Missing capability:
Missing `GET /consumer/printrequest/{id}` and `DELETE /consumer/printrequest/{id}` for draft/not-paid states.

Proof that function composition is insufficient:
Without a read/delete draft endpoint, clients cannot manage a draft after the budget response except by submitting it. Pending cancellation cannot target drafts.

Evidence from existing functions/source:
- `ConsumerController.getRequests` queries only `PrintRequest.Status.PENDING`.

Business impact:
Users can accumulate invisible drafts and cannot recover from mistaken budget creation.

### Missing Behavior 8: Public Search and Filtering Beyond Nearest

Priority:
API ergonomics gap

Expected business goal:
Users should search print shops by name, city/address, capabilities, rating, or price support.

Why it is unsupported:
The only search-like function sorts all shops by coordinates.

Existing functions considered:
- `list public print shops`: returns all shops without filters.
- `find nearest print shops`: requires coordinates and sorts by distance only.
- `retrieve print-shop price table`: reads one shop's price table but does not search across shops by capability.

Missing capability:
Missing query filters and capability/rating/price indexed search.

Proof that function composition is insufficient:
Clients can fetch all shops and filter locally only if the dataset is small and all needed details are exposed. Server-side capability matching across price tables is not available.

Evidence from existing functions/source:
- `PrintShopController` has only `/printshops`, `/printshops/{id}`, and `/printshops/nearest` for public discovery.

Business impact:
Discovery is weak for real marketplace use and expensive/ambiguous for clients.

## Cross-Behavior Observations

- Validation is weak in many controllers. Missing ids often cause null dereferences rather than structured errors.
- Ownership checks are incomplete for printing schemas, notifications, and some employee operations.
- Price-table edits are upserts and do not validate overlapping ranges, negative prices, or semantic consistency.
- Budget calculation is event-driven. Existing requests are not recomputed when price tables or schemas change.
- Payment submission trusts client-supplied budget values.
- PayPal IPN verification is logged or computed but not consistently enforced before mutation.
- Some endpoints mutate state and return no body, especially PayPal callbacks and notification send.
- Some error or edge responses are malformed or misleading, such as printing-schema double-delete returning `"false": true`.
- Swagger frequently disagrees with implementation: consumer registration uses request parameters, consumer profile update uses a JSON body, budget creation requires multipart `printRequest`, review edit uses request parameters, and employee/price-table bodies are not fully represented.

## Coverage Summary

Supported domain areas:
consumer/admin registration, credential validation, print-shop onboarding, public shop discovery, manager shop lookup, employee lifecycle, price-table maintenance, printing-schema lifecycle, consumer budget creation, print-request submission/payment/fulfillment, cancellation, reviews, notifications, demo seeding, and external print-document upload.

Partially supported domain areas:
payment verification, accounting settlement, request history, review reputation, notification management, employee authorization, and draft request lifecycle.

Unsupported domain areas:
direct shop update/delete, strong ownership enforcement for consumer-scoped resources, transactional draft creation, immutable/revalidated quotes, review eligibility, robust PayPal idempotency and verification, draft cleanup, and rich shop search/filtering.
