### Function 1: create pet

Function name:
create pet

Core endpoint(s):
- `POST /pet`

Preconditions:
- No existing pet with `id={petId}` is present in the in-memory pet store if the call is intended to create a new pet. This can be satisfied by directly seeding the `PetData` collection without that id or by not calling `POST /pet` previously with the same `id`.

Successful execution:
- Result:
  Stores the supplied Pet object as a pet resource.
- Invocation:
  Step 1: `POST /pet` with a non-null Pet body containing `id={petId}` and any supplied `name`, `photoUrls`, `category`, `tags`, and `status` values.
- Constraints:
  The Pet body must be non-null. Swagger requires `name` and `photoUrls`, but the implementation only checks that the body is non-null. `PetData.addPet` removes any existing pet with the same id before storing the submitted body.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No Pet request body is supplied, or the request body deserializes to null.
  - Failure endpoint:
    `POST /pet`
  - Why this fails:
    `PetController.addPet` returns bad request when `pet == null`.
  - Intentionally violated constraints:
    The implementation-required Pet request body is omitted.
- Branch 2:
  - Preconditions:
    - A pet with `id={petId}` already exists in the in-memory pet store. This can be satisfied by direct fixture insertion into `PetData` or by calling `POST /pet` once with a Pet body whose `id` is `{petId}`.
  - Failure endpoint:
    `POST /pet`
  - Why this fails:
    The request succeeds, but it does not create an additional independent pet; `PetData.addPet` removes the existing pet with the same id and stores the new body.
  - Intentionally violated constraints:
    The submitted id is reused for a create-only interpretation of the endpoint.

Endpoint coverage:
- Covers:
  `POST /pet`
- Distinct meaning:
  Adds a pet when the submitted id is not already present.

### Function 2: replace pet through create endpoint

Function name:
replace pet through create endpoint

Core endpoint(s):
- `POST /pet`

Preconditions:
- A pet with `id={petId}` already exists in the in-memory pet store. This can be satisfied by directly inserting a `Pet` fixture with `id={petId}` into `PetData` or by calling `POST /pet` with a non-null Pet body containing `id={petId}`.

Successful execution:
- Result:
  Replaces the stored pet that has the same id as the submitted Pet body.
- Invocation:
  Step 1: `POST /pet` with a non-null replacement Pet body whose `id` is `{petId}`.
- Constraints:
  The replacement body must be non-null and must reuse the id of an existing pet. `PetData.addPet` deletes any current pet with that id and stores the submitted body.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No pet with `id={petId}` exists in the in-memory pet store. This can be produced by direct fixture setup without that id, by deleting it beforehand, or by not calling `POST /pet` before the replacement request.
  - Failure endpoint:
    `POST /pet`
  - Why this fails:
    The request succeeds as a new pet creation instead of replacing an existing pet.
  - Intentionally violated constraints:
    The existing pet state required for replacement is omitted.
- Branch 2:
  - Preconditions:
    - A pet with `id={petId}` already exists in the in-memory pet store. This can be satisfied by directly inserting a `Pet` fixture with that id or by calling `POST /pet` with `id={petId}`.
    - No replacement Pet request body is supplied, or the body deserializes to null.
  - Failure endpoint:
    `POST /pet`
  - Why this fails:
    `PetController.addPet` rejects a null Pet body.
  - Intentionally violated constraints:
    The replacement body is omitted.

Endpoint coverage:
- Covers:
  `POST /pet`
- Distinct meaning:
  Overwrites an existing pet when the submitted id already exists.

### Function 3: update existing pet by body

Function name:
update existing pet by body

Core endpoint(s):
- `PUT /pet`

Preconditions:
- A pet with `id={petId}` exists in the in-memory pet store. This can be satisfied by directly inserting a `Pet` fixture with `id={petId}` into `PetData` or by calling `POST /pet` with a non-null Pet body containing `id={petId}`.
- The `id` in the `PUT /pet` body must be `{petId}`, matching the existing pet to be replaced.

Successful execution:
- Result:
  Replaces the existing pet identified by the id in the submitted Pet body.
- Invocation:
  Step 1: `PUT /pet` with a non-null Pet body whose `id` is `{petId}`.
- Constraints:
  The body must be non-null. The implementation looks up the body id before replacing the pet. Swagger requires `name` and `photoUrls`, but the implementation does not validate those fields.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No Pet update body is supplied, or the body deserializes to null.
  - Failure endpoint:
    `PUT /pet`
  - Why this fails:
    `PetController.updatePet` returns bad request when `pet == null`.
  - Intentionally violated constraints:
    The implementation-required Pet update body is omitted.
- Branch 2:
  - Preconditions:
    - No pet with `id={missingPetId}` exists in the in-memory pet store. This can be produced by direct fixture setup without that id, by deleting it beforehand, or by not calling `POST /pet` with `id={missingPetId}`.
    - The `PUT /pet` body uses `id={missingPetId}`.
  - Failure endpoint:
    `PUT /pet`
  - Why this fails:
    The controller looks up the body id, finds no existing pet, and returns not found.
  - Intentionally violated constraints:
    The required existing pet state for the body id is omitted.

Endpoint coverage:
- Covers:
  `PUT /pet`
- Distinct meaning:
  Replaces an existing pet by the id carried in the request body.

### Function 4: find pets by status

Function name:
find pets by status

Core endpoint(s):
- `GET /pet/findByStatus`

Preconditions:
- None.

Successful execution:
- Result:
  Returns pets whose `status` equals one of the supplied comma-separated status values.
- Invocation:
  Step 1: `GET /pet/findByStatus` with query `status={status}`.
- Constraints:
  The `status` query value must be non-null. Swagger marks `status` optional with default `available` and documents enum values `available`, `pending`, and `sold`, but the implementation requires a non-null string and does not enforce the enum. Unmatched non-null values return an empty list.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request omits the `status` query value.
  - Failure endpoint:
    `GET /pet/findByStatus`
  - Why this fails:
    `PetController.findPetsByStatus` returns bad request when `status == null`.
  - Intentionally violated constraints:
    The implementation-required `status` query value is omitted, despite Swagger documenting it as optional.

Endpoint coverage:
- Covers:
  `GET /pet/findByStatus`
- Distinct meaning:
  Searches the pet collection by status.

### Function 5: find pets by tags

Function name:
find pets by tags

Core endpoint(s):
- `GET /pet/findByTags`

Preconditions:
- None.

Successful execution:
- Result:
  Returns pets that have at least one tag whose `name` matches one of the supplied tag values.
- Invocation:
  Step 1: `GET /pet/findByTags` with query `tags={tagName}` or multiple `tags` values.
- Constraints:
  The `tags` query list must be non-null and non-empty. Swagger marks the query parameter optional, but the implementation rejects null or empty tag lists. Non-empty values with no matches return an empty list.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request omits `tags` or supplies an empty tag list.
  - Failure endpoint:
    `GET /pet/findByTags`
  - Why this fails:
    `PetController.findPetsByTags` returns bad request when the tag list is null or empty.
  - Intentionally violated constraints:
    The implementation-required tag list is omitted or empty.

Endpoint coverage:
- Covers:
  `GET /pet/findByTags`
- Distinct meaning:
  Searches the pet collection by tag name.

### Function 6: get pet by id

Function name:
get pet by id

Core endpoint(s):
- `GET /pet/{petId}`

Preconditions:
- A pet with `id={petId}` exists in the in-memory pet store. This can be satisfied by directly inserting a `Pet` fixture with `id={petId}` into `PetData` or by calling `POST /pet` with a non-null Pet body containing `id={petId}`.
- The `{petId}` path value used by `GET /pet/{petId}` must equal the id of the stored pet.

Successful execution:
- Result:
  Retrieves the existing pet identified by the path id.
- Invocation:
  Step 1: `GET /pet/{petId}` with path `{petId}`.
- Constraints:
  The path id must be non-null and must identify an existing pet. Swagger documents `api_key` or OAuth security, but the implementation does not enforce authentication or authorization.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No pet with `id={missingPetId}` exists in the in-memory pet store. This can be produced by direct fixture setup without that id, by deleting it beforehand, or by not calling `POST /pet` with `id={missingPetId}`.
  - Failure endpoint:
    `GET /pet/{petId}`
  - Why this fails:
    `PetController.getPetById` cannot find the pet and returns not found.
  - Intentionally violated constraints:
    The required pet state for the requested path id is omitted.

Endpoint coverage:
- Covers:
  `GET /pet/{petId}`
- Distinct meaning:
  Reads one pet by id.

### Function 7: update pet name and status by path id

Function name:
update pet name and status by path id

Core endpoint(s):
- `POST /pet/{petId}`

Preconditions:
- A pet with `id={petId}` exists in the in-memory pet store. This can be satisfied by directly inserting a `Pet` fixture with `id={petId}` into `PetData` or by calling `POST /pet` with a non-null Pet body containing `id={petId}`.
- The `{petId}` path value used by `POST /pet/{petId}` must equal the id of the stored pet.

Successful execution:
- Result:
  Updates the existing pet's `name` and sets its `status` to the supplied value, which may be null.
- Invocation:
  Step 1: `POST /pet/{petId}` with path `{petId}`, query `name={newName}`, and optional query `status={newStatus}`.
- Constraints:
  The path id must be non-null and identify an existing pet. The `name` query value is required by the implementation even though Swagger marks it optional. `status` is not required and is not validated against Swagger's enum.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A pet with `id={petId}` exists in the in-memory pet store. This can be satisfied by direct fixture insertion into `PetData` or by calling `POST /pet` with `id={petId}`.
    - The update request omits the `name` query value.
  - Failure endpoint:
    `POST /pet/{petId}`
  - Why this fails:
    `PetController.updatePetWithForm` returns bad request when `name == null`.
  - Intentionally violated constraints:
    The implementation-required `name` query value is omitted.
- Branch 2:
  - Preconditions:
    - No pet with `id={missingPetId}` exists in the in-memory pet store. This can be produced by direct fixture setup without that id, by deleting it beforehand, or by not calling `POST /pet` with `id={missingPetId}`.
    - The request supplies query `name={newName}`.
  - Failure endpoint:
    `POST /pet/{petId}`
  - Why this fails:
    The controller cannot find the existing pet and returns not found. Swagger does not document this 404 branch for this endpoint.
  - Intentionally violated constraints:
    The required pet state for the requested path id is omitted.

Endpoint coverage:
- Covers:
  `POST /pet/{petId}`
- Distinct meaning:
  Updates mutable fields of an existing pet selected by path id.

### Function 8: delete pet

Function name:
delete pet

Core endpoint(s):
- `DELETE /pet/{petId}`

Preconditions:
- A pet with `id={petId}` exists in the in-memory pet store. This can be satisfied by directly inserting a `Pet` fixture with `id={petId}` into `PetData` or by calling `POST /pet` with a non-null Pet body containing `id={petId}`.
- The `{petId}` path value used by `DELETE /pet/{petId}` must equal the id of the stored pet.

Successful execution:
- Result:
  Deletes the pet identified by the path id and returns the success message `Pet deleted`.
- Invocation:
  Step 1: `DELETE /pet/{petId}` with path `{petId}` and optional header `api_key={apiKey}`.
- Constraints:
  The path id must be non-null. The `api_key` header is documented by Swagger but ignored by the implementation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No pet with `id={missingPetId}` exists in the in-memory pet store. This can be produced by direct fixture setup without that id, by deleting it beforehand, or by not calling `POST /pet` with `id={missingPetId}`.
  - Failure endpoint:
    `DELETE /pet/{petId}`
  - Why this fails:
    It does not fail at HTTP level. `PetData.deletePetById` removes nothing, the follow-up lookup returns null, and the controller still returns the success message `Pet deleted`.
  - Intentionally violated constraints:
    The required pet state for an actual deletion is omitted.

Endpoint coverage:
- Covers:
  `DELETE /pet/{petId}`
- Distinct meaning:
  Deletes a pet when it exists; absent ids are treated as successful no-op deletes.

### Function 9: upload image for pet

Function name:
upload image for pet

Core endpoint(s):
- `POST /pet/{petId}/uploadImage`

Preconditions:
- A pet with `id={petId}` exists in the in-memory pet store and has a non-null `photoUrls` list. This can be satisfied by directly inserting a `Pet` fixture with `id={petId}` and initialized `photoUrls` into `PetData` or by calling `POST /pet` with a non-null Pet body containing `id={petId}` and `photoUrls=[...]`.
- The `{petId}` path value used by `POST /pet/{petId}/uploadImage` must equal the id of the stored pet.

Successful execution:
- Result:
  Appends the uploaded file's absolute path to the existing pet's `photoUrls` list and returns the updated Pet.
- Invocation:
  Step 1: `POST /pet/{petId}/uploadImage` with path `{petId}` and a binary file body.
- Constraints:
  The path id and file body must be non-null, and the pet must exist. Swagger documents query `additionalMetadata` and an `ApiResponse` response body, but the implementation ignores `additionalMetadata` and returns the updated Pet. The implementation assumes `photoUrls` is non-null before appending the file path.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A pet with `id={petId}` exists in the in-memory pet store. This can be satisfied by direct fixture insertion into `PetData` or by calling `POST /pet` with `id={petId}`.
    - No file body is supplied.
  - Failure endpoint:
    `POST /pet/{petId}/uploadImage`
  - Why this fails:
    `PetController.uploadFile` returns bad request when `file == null`.
  - Intentionally violated constraints:
    The implementation-required binary upload body is omitted.
- Branch 2:
  - Preconditions:
    - No pet with `id={missingPetId}` exists in the in-memory pet store. This can be produced by direct fixture setup without that id, by deleting it beforehand, or by not calling `POST /pet` with `id={missingPetId}`.
    - A file body is supplied.
  - Failure endpoint:
    `POST /pet/{petId}/uploadImage`
  - Why this fails:
    The controller cannot find the pet and returns not found.
  - Intentionally violated constraints:
    The required pet state for the upload target is omitted.

Endpoint coverage:
- Covers:
  `POST /pet/{petId}/uploadImage`
- Distinct meaning:
  Attaches an uploaded file path to an existing pet.

### Function 10: get order inventory

Function name:
get order inventory

Core endpoint(s):
- `GET /store/inventory`

Preconditions:
- None.

Successful execution:
- Result:
  Returns a map from order status to the sum of order quantities for that status.
- Invocation:
  Step 1: `GET /store/inventory`.
- Constraints:
  No order needs to be created first. The implementation aggregates the current in-memory `OrderData` collection by each order's `status`.

Failure or exceptional branches:
None identified in implementation logic.

Endpoint coverage:
- Covers:
  `GET /store/inventory`
- Distinct meaning:
  Reads aggregate store inventory by order status.

### Function 11: place order

Function name:
place order

Core endpoint(s):
- `POST /store/order`

Preconditions:
- No existing order with `id={orderId}` is present in the in-memory order store if the call is intended to create a new order. This can be satisfied by directly seeding the `OrderData` collection without that id or by not calling `POST /store/order` previously with the same `id`.

Successful execution:
- Result:
  Stores the supplied Order object as an order resource.
- Invocation:
  Step 1: `POST /store/order` with a non-null Order body containing `id={orderId}` and any supplied `petId`, `quantity`, `shipDate`, `status`, and `complete` values.
- Constraints:
  The Order body must be non-null. Swagger describes the order as being for a pet, but the implementation does not validate that `petId` identifies an existing pet. `OrderData.addOrder` removes any existing order with the same id before storing the submitted body.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No Order request body is supplied, or the request body deserializes to null.
  - Failure endpoint:
    `POST /store/order`
  - Why this fails:
    `OrderController.placeOrder` returns bad request when `order == null`.
  - Intentionally violated constraints:
    The implementation-required Order request body is omitted.

Endpoint coverage:
- Covers:
  `POST /store/order`
- Distinct meaning:
  Adds an order when the submitted id is not already present.

### Function 12: replace order through place-order endpoint

Function name:
replace order through place-order endpoint

Core endpoint(s):
- `POST /store/order`

Preconditions:
- An order with `id={orderId}` already exists in the in-memory order store. This can be satisfied by directly inserting an `Order` fixture with `id={orderId}` into `OrderData` or by calling `POST /store/order` with a non-null Order body containing `id={orderId}`.

Successful execution:
- Result:
  Replaces the stored order that has the same id as the submitted Order body.
- Invocation:
  Step 1: `POST /store/order` with a non-null replacement Order body whose `id` is `{orderId}`.
- Constraints:
  The replacement body must be non-null and must reuse the id of an existing order. `OrderData.addOrder` deletes any current order with that id and stores the submitted body.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No order with `id={orderId}` exists in the in-memory order store. This can be produced by direct fixture setup without that id, by deleting it beforehand, or by not calling `POST /store/order` before the replacement request.
  - Failure endpoint:
    `POST /store/order`
  - Why this fails:
    The request succeeds as a new order creation instead of replacing an existing order.
  - Intentionally violated constraints:
    The existing order state required for replacement is omitted.

Endpoint coverage:
- Covers:
  `POST /store/order`
- Distinct meaning:
  Overwrites an existing order when the submitted id already exists.

### Function 13: get order by id

Function name:
get order by id

Core endpoint(s):
- `GET /store/order/{orderId}`

Preconditions:
- An order with `id={orderId}` exists in the in-memory order store. This can be satisfied by directly inserting an `Order` fixture with `id={orderId}` into `OrderData` or by calling `POST /store/order` with a non-null Order body containing `id={orderId}`.
- The `{orderId}` path value used by `GET /store/order/{orderId}` must equal the id of the stored order.

Successful execution:
- Result:
  Retrieves the existing order identified by the path id.
- Invocation:
  Step 1: `GET /store/order/{orderId}` with path `{orderId}`.
- Constraints:
  The path id must be non-null and must identify an existing order. Swagger mentions special valid id ranges, but the implementation only checks whether the id exists.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No order with `id={missingOrderId}` exists in the in-memory order store. This can be produced by direct fixture setup without that id, by deleting it beforehand, or by not calling `POST /store/order` with `id={missingOrderId}`.
  - Failure endpoint:
    `GET /store/order/{orderId}`
  - Why this fails:
    `OrderController.getOrderById` cannot find the order and returns not found.
  - Intentionally violated constraints:
    The required order state for the requested path id is omitted.

Endpoint coverage:
- Covers:
  `GET /store/order/{orderId}`
- Distinct meaning:
  Reads one order by id.

### Function 14: delete order

Function name:
delete order

Core endpoint(s):
- `DELETE /store/order/{orderId}`

Preconditions:
- An order with `id={orderId}` exists in the in-memory order store. This can be satisfied by directly inserting an `Order` fixture with `id={orderId}` into `OrderData` or by calling `POST /store/order` with a non-null Order body containing `id={orderId}`.
- The `{orderId}` path value used by `DELETE /store/order/{orderId}` must equal the id of the stored order.

Successful execution:
- Result:
  Deletes the order identified by the path id and returns a successful response with a null entity.
- Invocation:
  Step 1: `DELETE /store/order/{orderId}` with path `{orderId}`.
- Constraints:
  The path id must be non-null. Swagger documents 404 for missing orders, but the implementation treats missing ids as successful no-op deletes.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No order with `id={missingOrderId}` exists in the in-memory order store. This can be produced by direct fixture setup without that id, by deleting it beforehand, or by not calling `POST /store/order` with `id={missingOrderId}`.
  - Failure endpoint:
    `DELETE /store/order/{orderId}`
  - Why this fails:
    It does not fail at HTTP level. `OrderData.deleteOrderById` removes nothing, the follow-up lookup returns null, and the controller returns success with a null entity.
  - Intentionally violated constraints:
    The required order state for an actual deletion is omitted.

Endpoint coverage:
- Covers:
  `DELETE /store/order/{orderId}`
- Distinct meaning:
  Deletes an order when it exists; absent ids are treated as successful no-op deletes.

### Function 15: create user

Function name:
create user

Core endpoint(s):
- `POST /user`

Preconditions:
- No existing user with `username={username}` is present in the in-memory user store if the call is intended to create a new user. This can be satisfied by directly seeding the `UserData` collection without that username or by not calling `POST /user` previously with the same `username`.

Successful execution:
- Result:
  Stores the supplied User object as a user resource.
- Invocation:
  Step 1: `POST /user` with a non-null User body containing `username={username}` and any supplied profile fields.
- Constraints:
  The User body must be non-null. The implementation does not require authentication despite Swagger's description that creation can only be done by the logged-in user. `UserData.addUser` removes any existing user with the same username before storing the submitted body.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No User request body is supplied, or the request body deserializes to null.
  - Failure endpoint:
    `POST /user`
  - Why this fails:
    `UserController.createUser` returns bad request when `user == null`.
  - Intentionally violated constraints:
    The implementation-required User request body is omitted.

Endpoint coverage:
- Covers:
  `POST /user`
- Distinct meaning:
  Adds a user when the submitted username is not already present.

### Function 16: replace user through create endpoint

Function name:
replace user through create endpoint

Core endpoint(s):
- `POST /user`

Preconditions:
- A user with `username={username}` already exists in the in-memory user store. This can be satisfied by directly inserting a `User` fixture with `username={username}` into `UserData` or by calling `POST /user` with a non-null User body containing `username={username}`.

Successful execution:
- Result:
  Replaces the stored user that has the same username as the submitted User body.
- Invocation:
  Step 1: `POST /user` with a non-null replacement User body whose `username` is `{username}`.
- Constraints:
  The replacement body must be non-null and must reuse an existing username. `UserData.addUser` deletes any current user with that username and stores the submitted body.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user with `username={username}` exists in the in-memory user store. This can be produced by direct fixture setup without that username, by deleting it beforehand, or by not calling `POST /user` before the replacement request.
  - Failure endpoint:
    `POST /user`
  - Why this fails:
    The request succeeds as a new user creation instead of replacing an existing user.
  - Intentionally violated constraints:
    The existing user state required for replacement is omitted.

Endpoint coverage:
- Covers:
  `POST /user`
- Distinct meaning:
  Overwrites an existing user when the submitted username already exists.

### Function 17: create users from list

Function name:
create users from list

Core endpoint(s):
- `POST /user/createWithList`

Preconditions:
- No existing users with the submitted usernames are present in the in-memory user store if the call is intended to create only new users. This can be satisfied by directly seeding the `UserData` collection without those usernames or by not calling `POST /user` or `POST /user/createWithList` previously with those usernames.

Successful execution:
- Result:
  Stores every User object from the submitted array and returns the submitted array.
- Invocation:
  Step 1: `POST /user/createWithList` with a non-empty JSON array of User objects.
- Constraints:
  The user array must be non-null and non-empty. Swagger's response schema is a single User, but the implementation returns the submitted array. Each submitted username replaces any existing user with the same username.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user array is supplied, or the submitted array is empty.
  - Failure endpoint:
    `POST /user/createWithList`
  - Why this fails:
    `UserController.createUsersWithListInput` returns bad request when the array is null or empty.
  - Intentionally violated constraints:
    The implementation-required non-empty user array is omitted or empty.

Endpoint coverage:
- Covers:
  `POST /user/createWithList`
- Distinct meaning:
  Adds multiple users when the submitted usernames are new.

### Function 18: replace users from list

Function name:
replace users from list

Core endpoint(s):
- `POST /user/createWithList`

Preconditions:
- At least one user with `username={username}` already exists in the in-memory user store. This can be satisfied by directly inserting a `User` fixture with `username={username}` into `UserData` or by calling `POST /user/createWithList` with a non-empty array containing that username.

Successful execution:
- Result:
  Replaces existing users whose usernames appear in the submitted array.
- Invocation:
  Step 1: `POST /user/createWithList` with a non-empty JSON array containing a replacement User whose `username` is `{username}`.
- Constraints:
  The submitted array must be non-null and non-empty. A reused username is removed from `UserData` and then re-added with the submitted values. Usernames in the same array that do not already exist are created rather than replaced.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user with `username={username}` exists in the in-memory user store. This can be produced by direct fixture setup without that username, by deleting it beforehand, or by not calling `POST /user/createWithList` before the replacement request.
    - The submitted array contains a User whose `username` is `{username}`.
  - Failure endpoint:
    `POST /user/createWithList`
  - Why this fails:
    The request succeeds as user creation for that username instead of replacement.
  - Intentionally violated constraints:
    The existing user state required for replacement is omitted.
- Branch 2:
  - Preconditions:
    - A user with `username={username}` already exists in the in-memory user store. This can be satisfied by direct fixture insertion into `UserData` or by calling `POST /user/createWithList` with an array containing that username.
    - The replacement request supplies an empty user array.
  - Failure endpoint:
    `POST /user/createWithList`
  - Why this fails:
    `UserController.createUsersWithListInput` rejects an empty array.
  - Intentionally violated constraints:
    The implementation-required replacement user entries are omitted.

Endpoint coverage:
- Covers:
  `POST /user/createWithList`
- Distinct meaning:
  Overwrites users in bulk when submitted usernames already exist.

### Function 19: log in user

Function name:
log in user

Core endpoint(s):
- `GET /user/login`

Preconditions:
- None.

Successful execution:
- Result:
  Returns a login session string with `X-Rate-Limit` and `X-Expires-After` headers.
- Invocation:
  Step 1: `GET /user/login` with optional query `username={username}` and optional query `password={password}`.
- Constraints:
  No existing user is required. The implementation does not validate the username or password and always returns a generated session string. Swagger documents invalid-credential failure, but the controller does not implement it.

Failure or exceptional branches:
None identified in implementation logic.

Endpoint coverage:
- Covers:
  `GET /user/login`
- Distinct meaning:
  Produces a login-style response without credential validation.

### Function 20: log out user

Function name:
log out user

Core endpoint(s):
- `GET /user/logout`

Preconditions:
- None.

Successful execution:
- Result:
  Returns a logout confirmation message.
- Invocation:
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

### Function 21: get user by username

Function name:
get user by username

Core endpoint(s):
- `GET /user/{username}`

Preconditions:
- A user with `username={username}` exists in the in-memory user store. This can be satisfied by directly inserting a `User` fixture with `username={username}` into `UserData` or by calling `POST /user` with a non-null User body containing `username={username}`.
- The `{username}` path value used by `GET /user/{username}` must equal the stored user's username.

Successful execution:
- Result:
  Retrieves the existing user identified by the path username.
- Invocation:
  Step 1: `GET /user/{username}` with path `{username}`.
- Constraints:
  The path username must be non-null and must identify an existing user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user with `username=missing-user` exists in the in-memory user store. This can be produced by direct fixture setup without that username, by deleting it beforehand, or by not calling `POST /user` with `username=missing-user`.
  - Failure endpoint:
    `GET /user/{username}`
  - Why this fails:
    `UserController.getUserByName` cannot find the user and returns not found.
  - Intentionally violated constraints:
    The required user state for the requested path username is omitted.

Endpoint coverage:
- Covers:
  `GET /user/{username}`
- Distinct meaning:
  Reads one user by username.

### Function 22: update user without changing username

Function name:
update user without changing username

Core endpoint(s):
- `PUT /user/{username}`

Preconditions:
- A user with `username={username}` exists in the in-memory user store. This can be satisfied by directly inserting a `User` fixture with `username={username}` into `UserData` or by calling `POST /user` with a non-null User body containing `username={username}`.
- The `{username}` path value and the update body `username` must both be `{username}`.

Successful execution:
- Result:
  Updates the existing user while keeping the same username.
- Invocation:
  Step 1: `PUT /user/{username}` with path `{username}` and a non-null User body whose `username` is also `{username}`.
- Constraints:
  The path username and body must be non-null. The path username must identify an existing user. Swagger's description implies a logged-in user requirement, but the implementation does not enforce authentication or authorization.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A user with `username={username}` exists in the in-memory user store. This can be satisfied by direct fixture insertion into `UserData` or by calling `POST /user` with `username={username}`.
    - No User update body is supplied, or the body deserializes to null.
  - Failure endpoint:
    `PUT /user/{username}`
  - Why this fails:
    `UserController.updateUser` returns bad request when `user == null`.
  - Intentionally violated constraints:
    The implementation-required User update body is omitted.
- Branch 2:
  - Preconditions:
    - No user with `username=missing-user` exists in the in-memory user store. This can be produced by direct fixture setup without that username, by deleting it beforehand, or by not calling `POST /user` with `username=missing-user`.
    - A non-null User update body is supplied.
  - Failure endpoint:
    `PUT /user/{username}`
  - Why this fails:
    The controller cannot find the existing user selected by the path username and returns not found.
  - Intentionally violated constraints:
    The required user state for the path username is omitted.

Endpoint coverage:
- Covers:
  `PUT /user/{username}`
- Distinct meaning:
  Updates a user while retaining the same username.

### Function 23: rename user through update endpoint

Function name:
rename user through update endpoint

Core endpoint(s):
- `PUT /user/{username}`

Preconditions:
- A user with `username={oldUsername}` exists in the in-memory user store. This can be satisfied by directly inserting a `User` fixture with `username={oldUsername}` into `UserData` or by calling `POST /user` with a non-null User body containing `username={oldUsername}`.
- The `{username}` path value must be `{oldUsername}`, and the update body must contain `username={newUsername}`.

Successful execution:
- Result:
  Deletes the user selected by the path username and stores the submitted body user under the new username.
- Invocation:
  Step 1: `PUT /user/{username}` with path `{username}={oldUsername}` and a non-null User body whose `username` is `{newUsername}`.
- Constraints:
  The path username and body must be non-null. The path username must identify an existing user. `{newUsername}` must differ from `{oldUsername}` for this to be a rename. If `{newUsername}` already exists, `UserData.addUser` removes that existing user and stores the submitted body.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user with `username=missing-user` exists in the in-memory user store. This can be produced by direct fixture setup without that username, by deleting it beforehand, or by not calling `POST /user` with `username=missing-user`.
    - A non-null User body with `username={newUsername}` is supplied.
  - Failure endpoint:
    `PUT /user/{username}`
  - Why this fails:
    The controller cannot find the old user selected by the path username and returns not found.
  - Intentionally violated constraints:
    The required old-user state for the path username is omitted.
- Branch 2:
  - Preconditions:
    - A user with `username={username}` exists in the in-memory user store. This can be satisfied by direct fixture insertion into `UserData` or by calling `POST /user` with `username={username}`.
    - The request body also uses `username={username}`.
  - Failure endpoint:
    `PUT /user/{username}`
  - Why this fails:
    The request succeeds, but it performs an ordinary same-username update instead of a rename.
  - Intentionally violated constraints:
    The body username intentionally matches the path username.

Endpoint coverage:
- Covers:
  `PUT /user/{username}`
- Distinct meaning:
  Renames a user by deleting the path username and storing the body username.

### Function 24: delete user

Function name:
delete user

Core endpoint(s):
- `DELETE /user/{username}`

Preconditions:
- A user with `username={username}` exists in the in-memory user store. This can be satisfied by directly inserting a `User` fixture with `username={username}` into `UserData` or by calling `POST /user` with a non-null User body containing `username={username}`.
- The `{username}` path value used by `DELETE /user/{username}` must equal the stored user's username.

Successful execution:
- Result:
  Deletes the user identified by the path username and returns a successful response with a null entity.
- Invocation:
  Step 1: `DELETE /user/{username}` with path `{username}`.
- Constraints:
  The path username must be non-null. Swagger documents 404 for missing users, but the implementation treats missing usernames as successful no-op deletes.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user with `username=missing-user` exists in the in-memory user store. This can be produced by direct fixture setup without that username, by deleting it beforehand, or by not calling `POST /user` with `username=missing-user`.
  - Failure endpoint:
    `DELETE /user/{username}`
  - Why this fails:
    It does not fail at HTTP level. `UserData.deleteUser` removes nothing, the follow-up lookup returns null, and the controller returns success with a null entity.
  - Intentionally violated constraints:
    The required user state for an actual deletion is omitted.

Endpoint coverage:
- Covers:
  `DELETE /user/{username}`
- Distinct meaning:
  Deletes a user when it exists; absent usernames are treated as successful no-op deletes.
