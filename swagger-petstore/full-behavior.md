Analyzed `swagger-petstore.json`, `src/main/webapp/openapi.yaml`, and the implementation under `src/main/java/io/swagger/petstore`. Where Swagger and source disagree, I prioritized the controller/data-store behavior.

### Behavior 1: create pet

Behavior name:
create pet

Successful execution:
- Result:
  This behavior stores a new pet.
- Endpoint sequence:
  Step 1: `POST /pet` with a non-null Pet body containing `id={petId}`.
- Constraints:
  For this to be creation, `{petId}` must not already identify an existing pet. Swagger requires `name` and `photoUrls`, but the implementation only checks that the Pet body is non-null.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    A Pet body must be supplied.
  - Endpoint group:
    Step 1: `POST /pet` with no Pet body or a body that deserializes to null.
  - Failure endpoint:
    `POST /pet`
  - Why this fails:
    `PetController.addPet` returns bad request when `pet == null`.
  - Intentionally violated constraints:
    The required Pet request body is omitted.
- Branch 2:
  - Unsatisfied condition:
    `{petId}` must be new for this behavior to be creation.
  - Endpoint group:
    Step 1: `POST /pet` with body `id={petId}`.
    Step 2: `POST /pet` again with body `id={petId}`.
  - Failure endpoint:
    `POST /pet`
  - Why this fails:
    The second call does not create a second independent pet; `PetData.addPet` removes the existing pet with the same id and stores the new one.
  - Intentionally violated constraints:
    The second request reuses an already-existing pet id.

Endpoint coverage:
- Covers:
  `POST /pet`
- Distinct meaning:
  Creates a pet when the submitted `id` is not already present.

### Behavior 2: replace pet through create endpoint

Behavior name:
replace pet through create endpoint

Successful execution:
- Result:
  This behavior replaces the stored pet with the same `id`.
- Endpoint sequence:
  Step 1: `POST /pet` with body `id={petId}`.
  Step 2: `POST /pet` with another non-null Pet body whose `id` is the same `{petId}`.
- Constraints:
  Step 2 must reuse the same `id` as Step 1. The implementation removes the previous pet with that id and adds the new body.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    A pet with `{petId}` must already exist for this to be replacement.
  - Endpoint group:
    Step 1: `POST /pet` with body `id={petId}` while no existing pet with `{petId}` was created first.
  - Failure endpoint:
    `POST /pet`
  - Why this fails:
    The call succeeds, but it performs Behavior 1, creation, rather than replacement.
  - Intentionally violated constraints:
    The prerequisite existing pet state was intentionally omitted.
- Branch 2:
  - Unsatisfied condition:
    The replacement Pet body must be supplied.
  - Endpoint group:
    Step 1: `POST /pet` with body `id={petId}`.
    Step 2: `POST /pet` with no Pet body.
  - Failure endpoint:
    `POST /pet`
  - Why this fails:
    `PetController.addPet` rejects a null Pet body.
  - Intentionally violated constraints:
    Step 2 omits the required replacement body.

Endpoint coverage:
- Covers:
  `POST /pet`
- Distinct meaning:
  Replaces an existing pet when the submitted `id` already exists.

### Behavior 3: update existing pet by body

Behavior name:
update existing pet by body

Successful execution:
- Result:
  This behavior updates an existing pet using the Pet body.
- Endpoint sequence:
  Step 1: `POST /pet` with body `id={petId}`.
  Step 2: `PUT /pet` with a non-null Pet body whose `id` is `{petId}`.
- Constraints:
  Step 2 body `id` must match the pet created by Step 1. The implementation requires the existing pet to be found before replacing it.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The update body must be supplied.
  - Endpoint group:
    Step 1: `PUT /pet` with no Pet body.
  - Failure endpoint:
    `PUT /pet`
  - Why this fails:
    `PetController.updatePet` returns bad request for `pet == null`.
  - Intentionally violated constraints:
    The required Pet body is omitted.
- Branch 2:
  - Unsatisfied condition:
    A pet with the body `id` must already exist.
  - Endpoint group:
    Step 1: `PUT /pet` with body `id={missingPetId}`, where `{missingPetId}` was not created and is not one of the seeded pet ids.
  - Failure endpoint:
    `PUT /pet`
  - Why this fails:
    The controller looks up the body `id`, finds no existing pet, and returns not found.
  - Intentionally violated constraints:
    The prerequisite `POST /pet` for `{missingPetId}` is intentionally omitted.

Endpoint coverage:
- Covers:
  `POST /pet`
  `PUT /pet`
- Distinct meaning:
  `POST /pet` establishes the pet; `PUT /pet` replaces an existing pet by body id.

### Behavior 4: find pets by status

Behavior name:
find pets by status

Successful execution:
- Result:
  This behavior returns pets whose `status` equals one of the supplied comma-separated status values.
- Endpoint sequence:
  Step 1: `GET /pet/findByStatus` with query `status={status}`.
- Constraints:
  `{status}` must be non-null. Swagger lists `available`, `pending`, and `sold`, but the implementation does not enforce that enum; an unmatched non-null status returns an empty list.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The `status` query value must be provided.
  - Endpoint group:
    Step 1: `GET /pet/findByStatus` without `status`.
  - Failure endpoint:
    `GET /pet/findByStatus`
  - Why this fails:
    The implementation returns bad request when `status == null`.
  - Intentionally violated constraints:
    The query value is omitted. This differs from Swagger, which marks `status` optional with default `available`.

Endpoint coverage:
- Covers:
  `GET /pet/findByStatus`
- Distinct meaning:
  Searches the pet collection by status.

### Behavior 5: find pets by tags

Behavior name:
find pets by tags

Successful execution:
- Result:
  This behavior returns pets that have at least one tag whose name matches the supplied tag values.
- Endpoint sequence:
  Step 1: `GET /pet/findByTags` with query `tags={tagName}`.
- Constraints:
  The `tags` query list must be non-null and non-empty. The implementation returns an empty list when no pet tag matches.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    At least one tag value must be supplied.
  - Endpoint group:
    Step 1: `GET /pet/findByTags` without `tags`, or with an empty tag list.
  - Failure endpoint:
    `GET /pet/findByTags`
  - Why this fails:
    `PetController.findPetsByTags` returns bad request when the tag list is null or empty.
  - Intentionally violated constraints:
    The required implementation-level tag list is omitted, although Swagger marks the query parameter optional.

Endpoint coverage:
- Covers:
  `GET /pet/findByTags`
- Distinct meaning:
  Searches the pet collection by tag name.

### Behavior 6: get pet by id

Behavior name:
get pet by id

Successful execution:
- Result:
  This behavior retrieves one existing pet by id.
- Endpoint sequence:
  Step 1: `POST /pet` with body `id={petId}`.
  Step 2: `GET /pet/{petId}` using `{petId}` from the Step 1 response body.
- Constraints:
  The path `{petId}` in Step 2 must equal the `id` stored by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The requested pet must exist.
  - Endpoint group:
    Step 1: `GET /pet/{petId}` with `{petId}={missingPetId}`, where `{missingPetId}` was not created and is not seeded.
  - Failure endpoint:
    `GET /pet/{petId}`
  - Why this fails:
    The controller cannot find the pet and returns not found.
  - Intentionally violated constraints:
    The prerequisite `POST /pet` for `{missingPetId}` is intentionally omitted.

Endpoint coverage:
- Covers:
  `POST /pet`
  `GET /pet/{petId}`
- Distinct meaning:
  `POST /pet` creates the readable pet; `GET /pet/{petId}` reads it.

### Behavior 7: update pet name and status by path id

Behavior name:
update pet name and status by path id

Successful execution:
- Result:
  This behavior updates an existing pet’s `name` and `status` using the path id and query values.
- Endpoint sequence:
  Step 1: `POST /pet` with body `id={petId}`.
  Step 2: `POST /pet/{petId}` with path `{petId}` from Step 1 and query `name={newName}`; query `status={newStatus}` may also be supplied.
- Constraints:
  Step 2 path `{petId}` must identify the pet created by Step 1. `name` is required by the implementation even though Swagger marks it optional. `status` may be null.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The `name` query value must be supplied.
  - Endpoint group:
    Step 1: `POST /pet` with body `id={petId}`.
    Step 2: `POST /pet/{petId}` without `name`.
  - Failure endpoint:
    `POST /pet/{petId}`
  - Why this fails:
    The controller returns bad request when `name == null`.
  - Intentionally violated constraints:
    The implementation-required `name` query value is omitted.
- Branch 2:
  - Unsatisfied condition:
    The pet must already exist.
  - Endpoint group:
    Step 1: `POST /pet/{petId}` with `{petId}={missingPetId}` and query `name={newName}`.
  - Failure endpoint:
    `POST /pet/{petId}`
  - Why this fails:
    The controller cannot find the existing pet and returns not found.
  - Intentionally violated constraints:
    The prerequisite `POST /pet` for `{missingPetId}` is intentionally omitted. Swagger does not document this 404 branch for this endpoint.

Endpoint coverage:
- Covers:
  `POST /pet`
  `POST /pet/{petId}`
- Distinct meaning:
  `POST /pet` creates the target pet; `POST /pet/{petId}` changes its name/status fields.

### Behavior 8: delete pet

Behavior name:
delete pet

Successful execution:
- Result:
  This behavior deletes an existing pet.
- Endpoint sequence:
  Step 1: `POST /pet` with body `id={petId}`.
  Step 2: `DELETE /pet/{petId}` using `{petId}` from the Step 1 response body.
- Constraints:
  The path `{petId}` in Step 2 must equal the created pet id. Header `api_key` is documented but not used by the implementation.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The pet should exist for this to be an actual deletion.
  - Endpoint group:
    Step 1: `DELETE /pet/{petId}` with `{petId}={missingPetId}`, where `{missingPetId}` was not created and is not seeded.
  - Failure endpoint:
    `DELETE /pet/{petId}`
  - Why this fails:
    It does not fail at HTTP level; the implementation deletes nothing and still returns the success message `Pet deleted`.
  - Intentionally violated constraints:
    The prerequisite `POST /pet` for `{missingPetId}` is intentionally omitted.

Endpoint coverage:
- Covers:
  `POST /pet`
  `DELETE /pet/{petId}`
- Distinct meaning:
  Deletes a pet when it exists; absent ids are treated as successful no-op deletes.

### Behavior 9: upload image for pet

Behavior name:
upload image for pet

Successful execution:
- Result:
  This behavior appends the uploaded file path to an existing pet’s `photoUrls`.
- Endpoint sequence:
  Step 1: `POST /pet` with body `id={petId}` and a non-null `photoUrls` list.
  Step 2: `POST /pet/{petId}/uploadImage` using `{petId}` from Step 1 and a binary file body.
- Constraints:
  Step 2 path `{petId}` must identify the pet created by Step 1. A file body must be supplied. Swagger documents `additionalMetadata`, but the implementation ignores it. Swagger says the response is `ApiResponse`; the implementation returns the updated Pet.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    A file must be supplied.
  - Endpoint group:
    Step 1: `POST /pet` with body `id={petId}`.
    Step 2: `POST /pet/{petId}/uploadImage` without a file body.
  - Failure endpoint:
    `POST /pet/{petId}/uploadImage`
  - Why this fails:
    The controller returns bad request when `file == null`.
  - Intentionally violated constraints:
    The binary upload body is omitted.
- Branch 2:
  - Unsatisfied condition:
    The pet must exist before upload.
  - Endpoint group:
    Step 1: `POST /pet/{petId}/uploadImage` with `{petId}={missingPetId}` and a file body.
  - Failure endpoint:
    `POST /pet/{petId}/uploadImage`
  - Why this fails:
    The controller cannot find the pet and returns not found.
  - Intentionally violated constraints:
    The prerequisite `POST /pet` for `{missingPetId}` is intentionally omitted.

Endpoint coverage:
- Covers:
  `POST /pet`
  `POST /pet/{petId}/uploadImage`
- Distinct meaning:
  `POST /pet` creates the pet; `POST /pet/{petId}/uploadImage` attaches an uploaded file path to it.

### Behavior 10: get order inventory

Behavior name:
get order inventory

Successful execution:
- Result:
  This behavior returns a map from order status to the sum of order quantities for that status.
- Endpoint sequence:
  Step 1: `GET /store/inventory`.
- Constraints:
  No specific order needs to be created first. The implementation aggregates the current in-memory orders by `status`.

Failure or exceptional branches:
None identified in implementation logic.

Endpoint coverage:
- Covers:
  `GET /store/inventory`
- Distinct meaning:
  Reads aggregate store inventory by order status.

### Behavior 11: place order

Behavior name:
place order

Successful execution:
- Result:
  This behavior stores a new order.
- Endpoint sequence:
  Step 1: `POST /store/order` with a non-null Order body containing `id={orderId}`.
- Constraints:
  For this to be creation, `{orderId}` must not already identify an existing order. Swagger describes this as an order for a pet, but the implementation does not validate that `petId` refers to an existing pet.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    An Order body must be supplied.
  - Endpoint group:
    Step 1: `POST /store/order` with no Order body.
  - Failure endpoint:
    `POST /store/order`
  - Why this fails:
    `OrderController.placeOrder` returns bad request when `order == null`.
  - Intentionally violated constraints:
    The Order body is omitted.

Endpoint coverage:
- Covers:
  `POST /store/order`
- Distinct meaning:
  Creates an order when the submitted `id` is not already present.

### Behavior 12: replace order through place-order endpoint

Behavior name:
replace order through place-order endpoint

Successful execution:
- Result:
  This behavior replaces an existing order with a new Order body using the same id.
- Endpoint sequence:
  Step 1: `POST /store/order` with body `id={orderId}`.
  Step 2: `POST /store/order` with another Order body whose `id` is the same `{orderId}`.
- Constraints:
  Step 2 must reuse the same order `id` as Step 1. `OrderData.addOrder` removes an existing order with the same id before adding the new one.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    An existing order with `{orderId}` is required for replacement.
  - Endpoint group:
    Step 1: `POST /store/order` with body `id={orderId}` while no order with `{orderId}` was created first.
  - Failure endpoint:
    `POST /store/order`
  - Why this fails:
    It succeeds as Behavior 11, creation, rather than replacement.
  - Intentionally violated constraints:
    The prerequisite existing order state is omitted.

Endpoint coverage:
- Covers:
  `POST /store/order`
- Distinct meaning:
  Replaces an order when the submitted `id` already exists.

### Behavior 13: get order by id

Behavior name:
get order by id

Successful execution:
- Result:
  This behavior retrieves one existing order by id.
- Endpoint sequence:
  Step 1: `POST /store/order` with body `id={orderId}`.
  Step 2: `GET /store/order/{orderId}` using `{orderId}` from the Step 1 response body.
- Constraints:
  The path `{orderId}` in Step 2 must equal the stored order id. Swagger mentions special id ranges, but the implementation only checks whether the id exists.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The requested order must exist.
  - Endpoint group:
    Step 1: `GET /store/order/{orderId}` with `{orderId}={missingOrderId}`, where `{missingOrderId}` was not created and is not seeded.
  - Failure endpoint:
    `GET /store/order/{orderId}`
  - Why this fails:
    The controller cannot find the order and returns not found.
  - Intentionally violated constraints:
    The prerequisite `POST /store/order` for `{missingOrderId}` is intentionally omitted.

Endpoint coverage:
- Covers:
  `POST /store/order`
  `GET /store/order/{orderId}`
- Distinct meaning:
  `POST /store/order` creates the readable order; `GET /store/order/{orderId}` reads it.

### Behavior 14: delete order

Behavior name:
delete order

Successful execution:
- Result:
  This behavior deletes an existing order.
- Endpoint sequence:
  Step 1: `POST /store/order` with body `id={orderId}`.
  Step 2: `DELETE /store/order/{orderId}` using `{orderId}` from the Step 1 response body.
- Constraints:
  The path `{orderId}` in Step 2 must equal the created order id.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The order should exist for this to be an actual deletion.
  - Endpoint group:
    Step 1: `DELETE /store/order/{orderId}` with `{orderId}={missingOrderId}`, where `{missingOrderId}` was not created and is not seeded.
  - Failure endpoint:
    `DELETE /store/order/{orderId}`
  - Why this fails:
    It does not fail at HTTP level; the implementation deletes nothing and returns success with a null entity. This differs from Swagger, which documents a 404 branch.
  - Intentionally violated constraints:
    The prerequisite `POST /store/order` for `{missingOrderId}` is intentionally omitted.

Endpoint coverage:
- Covers:
  `POST /store/order`
  `DELETE /store/order/{orderId}`
- Distinct meaning:
  Deletes an order when it exists; absent ids are treated as successful no-op deletes.

### Behavior 15: create user

Behavior name:
create user

Successful execution:
- Result:
  This behavior stores a new user.
- Endpoint sequence:
  Step 1: `POST /user` with a non-null User body containing `username={username}`.
- Constraints:
  For this to be creation, `{username}` must not already identify an existing user. The implementation checks only that the User body is non-null.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    A User body must be supplied.
  - Endpoint group:
    Step 1: `POST /user` with no User body.
  - Failure endpoint:
    `POST /user`
  - Why this fails:
    `UserController.createUser` returns bad request when `user == null`.
  - Intentionally violated constraints:
    The User body is omitted.

Endpoint coverage:
- Covers:
  `POST /user`
- Distinct meaning:
  Creates a user when the submitted `username` is not already present.

### Behavior 16: replace user through create endpoint

Behavior name:
replace user through create endpoint

Successful execution:
- Result:
  This behavior replaces an existing user with a new User body using the same username.
- Endpoint sequence:
  Step 1: `POST /user` with body `username={username}`.
  Step 2: `POST /user` with another User body whose `username` is the same `{username}`.
- Constraints:
  Step 2 must reuse the same `username` as Step 1. `UserData.addUser` removes an existing user with that username before adding the new one.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    An existing user with `{username}` is required for replacement.
  - Endpoint group:
    Step 1: `POST /user` with body `username={username}` while no user with `{username}` was created first.
  - Failure endpoint:
    `POST /user`
  - Why this fails:
    It succeeds as Behavior 15, creation, rather than replacement.
  - Intentionally violated constraints:
    The prerequisite existing user state is omitted.

Endpoint coverage:
- Covers:
  `POST /user`
- Distinct meaning:
  Replaces a user when the submitted `username` already exists.

### Behavior 17: create users from list

Behavior name:
create users from list

Successful execution:
- Result:
  This behavior stores multiple users from an input array.
- Endpoint sequence:
  Step 1: `POST /user/createWithList` with a non-empty JSON array of User objects.
- Constraints:
  For pure creation, each submitted `username` must not already identify an existing user. Swagger’s response schema is a single User, but the implementation returns the submitted array.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The user array must be non-null and non-empty.
  - Endpoint group:
    Step 1: `POST /user/createWithList` with no array or an empty array.
  - Failure endpoint:
    `POST /user/createWithList`
  - Why this fails:
    `UserController.createUsersWithListInput` returns bad request when the array is null or empty.
  - Intentionally violated constraints:
    The required non-empty user list is omitted.

Endpoint coverage:
- Covers:
  `POST /user/createWithList`
- Distinct meaning:
  Creates multiple users when submitted usernames are new.

### Behavior 18: replace users from list

Behavior name:
replace users from list

Successful execution:
- Result:
  This behavior replaces existing users whose usernames appear in the submitted array.
- Endpoint sequence:
  Step 1: `POST /user/createWithList` with an array containing a user `username={username}`.
  Step 2: `POST /user/createWithList` with another array containing a user whose `username` is the same `{username}`.
- Constraints:
  Step 2 must reuse at least one username created by Step 1. Each reused username is removed and re-added with the new body values.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Existing usernames are required for replacement.
  - Endpoint group:
    Step 1: `POST /user/createWithList` with an array of users whose usernames were not created first.
  - Failure endpoint:
    `POST /user/createWithList`
  - Why this fails:
    It succeeds as Behavior 17, creation, rather than replacement.
  - Intentionally violated constraints:
    The prerequisite existing user state is omitted.
- Branch 2:
  - Unsatisfied condition:
    The replacement array must be non-null and non-empty.
  - Endpoint group:
    Step 1: `POST /user/createWithList` with an array containing `username={username}`.
    Step 2: `POST /user/createWithList` with an empty array.
  - Failure endpoint:
    `POST /user/createWithList`
  - Why this fails:
    The controller rejects an empty array.
  - Intentionally violated constraints:
    Step 2 omits replacement user entries.

Endpoint coverage:
- Covers:
  `POST /user/createWithList`
- Distinct meaning:
  Replaces users in bulk when submitted usernames already exist.

### Behavior 19: log in user

Behavior name:
log in user

Successful execution:
- Result:
  This behavior returns a login session message plus `X-Rate-Limit` and `X-Expires-After` headers.
- Endpoint sequence:
  Step 1: `GET /user/login` with optional query `username={username}` and optional query `password={password}`.
- Constraints:
  No existing user is required. The implementation does not validate the username or password and always returns a generated session string.

Failure or exceptional branches:
None identified in implementation logic. Swagger documents invalid-credential failure, but the controller does not implement it.

Endpoint coverage:
- Covers:
  `GET /user/login`
- Distinct meaning:
  Produces a login-style response without credential validation.

### Behavior 20: log out user

Behavior name:
log out user

Successful execution:
- Result:
  This behavior returns a logout confirmation message.
- Endpoint sequence:
  Step 1: `GET /user/logout`.
- Constraints:
  No prior `GET /user/login` call is required. The implementation does not track or invalidate session state.

Failure or exceptional branches:
None identified in implementation logic.

Endpoint coverage:
- Covers:
  `GET /user/logout`
- Distinct meaning:
  Returns logout confirmation.

### Behavior 21: get user by username

Behavior name:
get user by username

Successful execution:
- Result:
  This behavior retrieves one existing user by username.
- Endpoint sequence:
  Step 1: `POST /user` with body `username={username}`.
  Step 2: `GET /user/{username}` using `{username}` from the Step 1 response body.
- Constraints:
  The path `{username}` in Step 2 must equal the stored username.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The requested user must exist.
  - Endpoint group:
    Step 1: `GET /user/{username}` with `{username}=missing-user`, where that username was not created and is not seeded.
  - Failure endpoint:
    `GET /user/{username}`
  - Why this fails:
    The controller cannot find the user and returns not found.
  - Intentionally violated constraints:
    The prerequisite `POST /user` for `missing-user` is intentionally omitted.

Endpoint coverage:
- Covers:
  `POST /user`
  `GET /user/{username}`
- Distinct meaning:
  `POST /user` creates the readable user; `GET /user/{username}` reads it.

### Behavior 22: update user without changing username

Behavior name:
update user without changing username

Successful execution:
- Result:
  This behavior updates an existing user while keeping the same username.
- Endpoint sequence:
  Step 1: `POST /user` with body `username={username}`.
  Step 2: `PUT /user/{username}` using path `{username}` from Step 1 and a non-null User body whose `username` is also `{username}`.
- Constraints:
  The path username must identify an existing user. To keep the username unchanged, the Step 2 body `username` must equal the path `{username}`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The update body must be supplied.
  - Endpoint group:
    Step 1: `POST /user` with body `username={username}`.
    Step 2: `PUT /user/{username}` with no User body.
  - Failure endpoint:
    `PUT /user/{username}`
  - Why this fails:
    `UserController.updateUser` returns bad request when `user == null`.
  - Intentionally violated constraints:
    The User update body is omitted.
- Branch 2:
  - Unsatisfied condition:
    The path username must already exist.
  - Endpoint group:
    Step 1: `PUT /user/{username}` with `{username}=missing-user` and a non-null User body.
  - Failure endpoint:
    `PUT /user/{username}`
  - Why this fails:
    The controller cannot find the existing user and returns not found.
  - Intentionally violated constraints:
    The prerequisite `POST /user` for `missing-user` is intentionally omitted.

Endpoint coverage:
- Covers:
  `POST /user`
  `PUT /user/{username}`
- Distinct meaning:
  `POST /user` creates the target user; `PUT /user/{username}` updates that same username.

### Behavior 23: rename user through update endpoint

Behavior name:
rename user through update endpoint

Successful execution:
- Result:
  This behavior deletes the user at the path username and stores the body user under a different username.
- Endpoint sequence:
  Step 1: `POST /user` with body `username={oldUsername}`.
  Step 2: `PUT /user/{username}` with path `{username}={oldUsername}` and body `username={newUsername}`.
- Constraints:
  Step 2 path `{oldUsername}` must exist. `{newUsername}` must differ from `{oldUsername}` for this to be a rename. The implementation deletes the old username and adds the body user.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The old path username must exist.
  - Endpoint group:
    Step 1: `PUT /user/{username}` with `{username}=missing-user` and body `username={newUsername}`.
  - Failure endpoint:
    `PUT /user/{username}`
  - Why this fails:
    The controller cannot find the old user and returns not found.
  - Intentionally violated constraints:
    The prerequisite `POST /user` for `missing-user` is intentionally omitted.
- Branch 2:
  - Unsatisfied condition:
    The body username must differ from the path username for this to be a rename.
  - Endpoint group:
    Step 1: `POST /user` with body `username={username}`.
    Step 2: `PUT /user/{username}` with path `{username}` and body `username={username}`.
  - Failure endpoint:
    `PUT /user/{username}`
  - Why this fails:
    It succeeds, but it performs Behavior 22, ordinary update, rather than rename.
  - Intentionally violated constraints:
    The body username intentionally matches the path username.

Endpoint coverage:
- Covers:
  `POST /user`
  `PUT /user/{username}`
- Distinct meaning:
  `POST /user` creates the old user; `PUT /user/{username}` removes the old username and stores the body username.

### Behavior 24: delete user

Behavior name:
delete user

Successful execution:
- Result:
  This behavior deletes an existing user.
- Endpoint sequence:
  Step 1: `POST /user` with body `username={username}`.
  Step 2: `DELETE /user/{username}` using `{username}` from the Step 1 response body.
- Constraints:
  The path `{username}` in Step 2 must equal the created username.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The user should exist for this to be an actual deletion.
  - Endpoint group:
    Step 1: `DELETE /user/{username}` with `{username}=missing-user`, where that username was not created and is not seeded.
  - Failure endpoint:
    `DELETE /user/{username}`
  - Why this fails:
    It does not fail at HTTP level; the implementation deletes nothing and returns success with a null entity. This differs from Swagger, which documents a 404 branch.
  - Intentionally violated constraints:
    The prerequisite `POST /user` for `missing-user` is intentionally omitted.

Endpoint coverage:
- Covers:
  `POST /user`
  `DELETE /user/{username}`
- Distinct meaning:
  Deletes a user when it exists; absent usernames are treated as successful no-op deletes.

Unclear or auxiliary endpoints:
None. Every Swagger path operation is covered above.