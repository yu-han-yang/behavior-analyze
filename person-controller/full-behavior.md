Source/OpenAPI discrepancies:
- The OpenAPI file lists `/api/person/{id}` and `/api/persons/{ids}` but omits their path parameter definitions. The controller requires `{id}` and `{ids}` as `@PathVariable` values.
- For `GET /api/person/{id}`, the controller appears to intend a `404 Not Found` when no person is found, but the service constructs `new PersonDTO(null)`, so the implemented result for a missing person is a runtime exception handled as `500 Internal Server Error`.

### Function 1: create person

Function name:
create person

Core endpoint(s):
- `POST /api/person`

Preconditions:
- None. The endpoint creates a new `persons` collection document from the submitted JSON body.

Successful execution:
- Result:
  One person document is inserted and the response returns the created `PersonDTO` with a server-assigned MongoDB ObjectId string in `id`.
- Invocation:
  Step 1: `POST /api/person` with a JSON `PersonDTO` body containing person fields such as `firstName`, `lastName`, `age`, `address`, `createdAt`, `insurance`, and `cars`.
- Constraints:
  The JSON body must be convertible to `PersonDTO`. `address` must be non-null because `PersonDTO.toPersonEntity()` calls `address.toAddressEntity()`. `cars` must be non-null and must not contain null items because conversion calls `cars.stream()` and maps each car through `CarDTO.toCarEntity()`. If `id` is supplied, it must be a valid MongoDB ObjectId string, but `MongoDBPersonRepository.save()` overwrites it with a newly generated ObjectId before insertion.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The submitted `PersonDTO` body contains `id` set to a non-ObjectId string.
  - Failure endpoint:
    `POST /api/person`
  - Why this fails:
    `PersonDTO.toPersonEntity()` calls `new ObjectId(id)` before the repository can overwrite the id, and the ObjectId constructor throws a runtime exception.
  - Intentionally violated constraints:
    Body `id` must be absent or a valid MongoDB ObjectId string.
- Branch 2:
  - Preconditions:
    - The submitted `PersonDTO` body has `address: null`, `cars: null`, or a null item inside `cars`.
  - Failure endpoint:
    `POST /api/person`
  - Why this fails:
    DTO conversion dereferences `address` and `cars`, and a null car item is dereferenced when the stream maps it through `CarDTO.toCarEntity()`.
  - Intentionally violated constraints:
    The body must include non-null `address`, non-null `cars`, and only non-null car items.

Endpoint coverage:
- Covers:
  `POST /api/person`
- Distinct meaning:
  Creates one person document.

### Function 2: create multiple persons

Function name:
create multiple persons

Core endpoint(s):
- `POST /api/persons`

Preconditions:
- None. The endpoint creates new `persons` collection documents from the submitted JSON array.

Successful execution:
- Result:
  All submitted persons are inserted and the response returns a list of created `PersonDTO` objects with server-assigned MongoDB ObjectId strings in `id`.
- Invocation:
  Step 1: `POST /api/persons` with a JSON array of `PersonDTO` bodies.
- Constraints:
  Every array item must satisfy the same conversion constraints as `POST /api/person`: absent or valid ObjectId `id`, non-null `address`, non-null `cars`, and no null car items. The service saves items sequentially through `personRepository.save(...)`, so each request id is overwritten with a newly generated ObjectId.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - At least one submitted array item has `id` set to a non-ObjectId string. Any valid items processed before that invalid item may already have been inserted because the service stream saves each converted item sequentially.
  - Failure endpoint:
    `POST /api/persons`
  - Why this fails:
    The invalid item fails in `PersonDTO.toPersonEntity()` when it calls `new ObjectId(id)`.
  - Intentionally violated constraints:
    Every submitted item `id` must be absent or a valid MongoDB ObjectId string.
- Branch 2:
  - Preconditions:
    - At least one submitted array item has `address: null`, `cars: null`, or a null item inside `cars`. Any valid items processed before that invalid item may already have been inserted.
  - Failure endpoint:
    `POST /api/persons`
  - Why this fails:
    DTO conversion dereferences the nested `address` and `cars` fields before or during repository insertion.
  - Intentionally violated constraints:
    Every submitted item must include non-null `address`, non-null `cars`, and only non-null car items.

Endpoint coverage:
- Covers:
  `POST /api/persons`
- Distinct meaning:
  Creates a batch of person documents.

### Function 3: list persons

Function name:
list persons

Core endpoint(s):
- `GET /api/persons`

Preconditions:
- None. The collection may be empty or populated.

Successful execution:
- Result:
  The endpoint returns every stored person as a JSON array. If no persons exist, it returns an empty list.
- Invocation:
  Step 1: `GET /api/persons` with no path, query, body, form, or header values required by the controller.
- Constraints:
  No prerequisite person resource is required. Each stored document must be convertible to `PersonDTO`; malformed persisted documents with null nested `address` or `cars` can cause response conversion failures.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The database contains a persisted person document whose `address` is null, whose `cars` list is null, or whose `cars` list contains a null car. This can be produced by direct database setup; the public creation endpoints normally fail before inserting such a document.
  - Failure endpoint:
    `GET /api/persons`
  - Why this fails:
    `PersonServiceImpl.findAll()` maps each `PersonEntity` to `new PersonDTO(...)`, and the DTO constructor dereferences nested `address` and `cars` values.
  - Intentionally violated constraints:
    Stored person documents must contain nested data that can be converted to `PersonDTO`.
- Branch 2:
  - Preconditions:
    - A generic database or runtime failure occurs while reading the collection.
  - Failure endpoint:
    `GET /api/persons`
  - Why this fails:
    The controller catches `RuntimeException` through its exception handler and returns `500 Internal Server Error`.
  - Intentionally violated constraints:
    No endpoint-reproducible API constraint is visible beyond database/runtime availability.

Endpoint coverage:
- Covers:
  `GET /api/persons`
- Distinct meaning:
  Lists the full person collection.

### Function 4: retrieve person by id

Function name:
retrieve person by id

Core endpoint(s):
- `GET /api/person/{id}`

Preconditions:
- A person exists in the `persons` collection with the desired `firstName`, `lastName`, `age`, `address`, `createdAt`, `insurance`, and `cars` values. This can be satisfied by directly inserting a valid `PersonEntity` document with non-null `address` and `cars`, or by calling `POST /api/person` with those values in a convertible JSON `PersonDTO` body.
- The `{id}` path value used by `GET /api/person/{id}` must identify that existing person. If the API is used to establish the person, `{id}` must be obtained from the `id` field returned by `POST /api/person`.

Successful execution:
- Result:
  The endpoint returns the `PersonDTO` for the existing person whose MongoDB `_id` equals `{id}`.
- Invocation:
  Step 1: `GET /api/person/{id}` with `{id}` set to the existing person id.
- Constraints:
  `{id}` must be a valid MongoDB ObjectId string and must identify an existing person. The OpenAPI file omits the path parameter definition, but the controller requires `{id}`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No person exists with `_id = {id}`. This can be satisfied by starting from an empty collection, deleting the person beforehand, directly omitting the document, or using a valid ObjectId that was not returned by `POST /api/person`.
  - Failure endpoint:
    `GET /api/person/{id}`
  - Why this fails:
    The repository returns `null`; `PersonServiceImpl.findOne()` immediately constructs `new PersonDTO(null)`, which throws before the controller can return its intended `404 Not Found`.
  - Intentionally violated constraints:
    `{id}` must identify an existing person.
- Branch 2:
  - Preconditions:
    - The `{id}` path value is not a valid MongoDB ObjectId string.
  - Failure endpoint:
    `GET /api/person/{id}`
  - Why this fails:
    `MongoDBPersonRepository.findOne()` constructs `new ObjectId(id)`, which throws for malformed ids.
  - Intentionally violated constraints:
    `{id}` must be a valid MongoDB ObjectId string.
- Branch 3:
  - Preconditions:
    - A person exists with `_id = {id}`, but the persisted document has null nested `address`, null `cars`, or a null car item. This can be produced by direct database setup; normal API creation rejects those shapes before insertion.
  - Failure endpoint:
    `GET /api/person/{id}`
  - Why this fails:
    The found `PersonEntity` cannot be converted to `PersonDTO` because the DTO constructor dereferences nested fields.
  - Intentionally violated constraints:
    The stored person selected by `{id}` must be convertible to `PersonDTO`.

Endpoint coverage:
- Covers:
  `GET /api/person/{id}`
- Distinct meaning:
  Reads one existing person by id.

### Function 5: retrieve multiple persons by ids

Function name:
retrieve multiple persons by ids

Core endpoint(s):
- `GET /api/persons/{ids}`

Preconditions:
- One or more persons exist in the `persons` collection with the desired `firstName`, `lastName`, `age`, `address`, `createdAt`, `insurance`, and `cars` values. This can be satisfied by directly inserting valid `PersonEntity` documents with non-null `address` and `cars`, or by calling `POST /api/persons` with those values in a JSON array of convertible `PersonDTO` bodies.
- The `{ids}` path value must be a comma-separated list of ids. If the API is used to establish the persons, each intended id in `{ids}` must be obtained from the corresponding `id` field returned by `POST /api/persons`.

Successful execution:
- Result:
  The endpoint returns the stored persons whose MongoDB `_id` values are included in `{ids}`. Valid ids that do not match any document are silently omitted.
- Invocation:
  Step 1: `GET /api/persons/{ids}` with `{ids}` set to comma-separated MongoDB ObjectId strings.
- Constraints:
  Every `{ids}` segment must be a valid MongoDB ObjectId string. The OpenAPI file omits the path parameter definition, but the controller requires `{ids}`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - At least one segment in the comma-separated `{ids}` path value is not a valid MongoDB ObjectId string. Other segments may be valid ids returned by `POST /api/persons` or inserted directly.
  - Failure endpoint:
    `GET /api/persons/{ids}`
  - Why this fails:
    The repository maps every segment through `new ObjectId(...)` before querying.
  - Intentionally violated constraints:
    Every `{ids}` segment must be a valid MongoDB ObjectId string.
- Branch 2:
  - Preconditions:
    - At least one id in `{ids}` identifies an existing person, and at least one other id is a valid ObjectId that does not identify any person. The existing person can be created by direct database insertion or by `POST /api/persons`; the missing id can be produced by omitting that document or deleting it before the request.
  - Failure endpoint:
    `GET /api/persons/{ids}`
  - Why this fails:
    It does not fail at HTTP level. MongoDB returns only matching documents, so non-existing ids are silently omitted from the response.
  - Intentionally violated constraints:
    Every id that the client expects in the response must identify an existing person.
- Branch 3:
  - Preconditions:
    - At least one matched person document has null nested `address`, null `cars`, or a null car item. This can be produced by direct database setup; normal API creation rejects those shapes before insertion.
  - Failure endpoint:
    `GET /api/persons/{ids}`
  - Why this fails:
    The service maps found entities to `PersonDTO`, and response conversion dereferences the malformed nested fields.
  - Intentionally violated constraints:
    Every matched stored person must be convertible to `PersonDTO`.

Endpoint coverage:
- Covers:
  `GET /api/persons/{ids}`
- Distinct meaning:
  Reads a selected subset of persons by comma-separated ids.

### Function 6: count persons

Function name:
count persons

Core endpoint(s):
- `GET /api/persons/count`

Preconditions:
- None. The collection may be empty or populated.

Successful execution:
- Result:
  The endpoint returns the number of documents in the `persons` collection.
- Invocation:
  Step 1: `GET /api/persons/count` with no path, query, body, form, or header values required by the controller.
- Constraints:
  No prerequisite person is required; an empty collection returns `0`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A generic database or runtime failure occurs while counting the collection.
  - Failure endpoint:
    `GET /api/persons/count`
  - Why this fails:
    The controller catches `RuntimeException` through its exception handler and returns `500 Internal Server Error`.
  - Intentionally violated constraints:
    No endpoint-reproducible API constraint is visible beyond database/runtime availability.

Endpoint coverage:
- Covers:
  `GET /api/persons/count`
- Distinct meaning:
  Counts all stored persons.

### Function 7: compute average age

Function name:
compute average age

Core endpoint(s):
- `GET /api/persons/averageAge`

Preconditions:
- At least one person exists in the `persons` collection with an `age` value. This can be satisfied by directly inserting a valid `PersonEntity` document with the desired `age`, or by calling `POST /api/person` with a convertible JSON `PersonDTO` body containing that `age` plus non-null `address` and `cars`; the created `id` is not used by the average endpoint.

Successful execution:
- Result:
  The endpoint returns the average of the `age` field across all stored persons.
- Invocation:
  Step 1: `GET /api/persons/averageAge` with no path, query, body, form, or header values required by the controller.
- Constraints:
  At least one person must exist. The average is computed over the entire collection, not over a caller-supplied subset.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No person documents exist in the `persons` collection. This can be produced by starting from an empty database, directly clearing the collection, or calling `DELETE /api/persons` before the failing request.
  - Failure endpoint:
    `GET /api/persons/averageAge`
  - Why this fails:
    The aggregation returns no first result, and `MongoDBPersonRepository.getAverageAge()` calls `.averageAge()` on `null`.
  - Intentionally violated constraints:
    At least one person must exist before computing the average.

Endpoint coverage:
- Covers:
  `GET /api/persons/averageAge`
- Distinct meaning:
  Computes aggregate average age across the person collection.

### Function 8: replace person

Function name:
replace person

Core endpoint(s):
- `PUT /api/person`

Preconditions:
- A person exists in the `persons` collection with the original values to be replaced. This can be satisfied by directly inserting a valid `PersonEntity` document with non-null `address` and `cars`, or by calling `POST /api/person` with those values in a convertible JSON `PersonDTO` body.
- The replacement body `id` must identify that existing person. If the API is used to establish the person, the replacement body `id` must reuse the `id` value returned by `POST /api/person`.

Successful execution:
- Result:
  The endpoint replaces the existing person selected by the body `id` and returns the replaced `PersonDTO`.
- Invocation:
  Step 1: `PUT /api/person` with a JSON `PersonDTO` body whose `id` equals an existing person id and whose other fields contain the replacement document.
- Constraints:
  The update target is selected by body `id`, not by path. The body is a full replacement document. Body `id` must be a valid MongoDB ObjectId string, must match an existing document, and required nested fields such as `address` and `cars` must be present and non-null.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A person exists in the collection. This can be satisfied by direct database insertion or by calling `POST /api/person`.
    - The `PUT /api/person` body omits `id` or uses a different valid ObjectId that does not identify an existing person.
  - Failure endpoint:
    `PUT /api/person`
  - Why this fails:
    If `id` is omitted, DTO conversion generates a new ObjectId. In both cases, `findOneAndReplace` finds no matching document, returns `null`, and the service constructs `new PersonDTO(null)`.
  - Intentionally violated constraints:
    The replacement body `id` must reuse the existing person id.
- Branch 2:
  - Preconditions:
    - The `PUT /api/person` body has `id` set to a non-ObjectId string.
  - Failure endpoint:
    `PUT /api/person`
  - Why this fails:
    `PersonDTO.toPersonEntity()` constructs `new ObjectId(id)` before the repository update.
  - Intentionally violated constraints:
    Body `id` must be a valid MongoDB ObjectId string.
- Branch 3:
  - Preconditions:
    - A person exists in the collection. This can be satisfied by direct database insertion or by calling `POST /api/person`.
    - The replacement body reuses the existing person id but has `address: null`, `cars: null`, or a null item inside `cars`.
  - Failure endpoint:
    `PUT /api/person`
  - Why this fails:
    DTO conversion dereferences the nested fields before `findOneAndReplace` is called.
  - Intentionally violated constraints:
    The replacement body must contain non-null `address`, non-null `cars`, and only non-null car items.

Endpoint coverage:
- Covers:
  `PUT /api/person`
- Distinct meaning:
  Replaces one existing person selected by body id.

### Function 9: replace multiple persons

Function name:
replace multiple persons

Core endpoint(s):
- `PUT /api/persons`

Preconditions:
- Multiple persons exist in the `persons` collection with the original values to be replaced. This can be satisfied by directly inserting valid `PersonEntity` documents with non-null `address` and `cars`, or by calling `POST /api/persons` with those values in a JSON array of convertible `PersonDTO` bodies.
- Each replacement body item `id` must identify one existing person. If the API is used to establish the persons, each item `id` must reuse an `id` value returned by `POST /api/persons`.

Successful execution:
- Result:
  The endpoint replaces all matched persons and returns MongoDB's modified document count.
- Invocation:
  Step 1: `PUT /api/persons` with a JSON array of `PersonDTO` bodies whose `id` values identify existing persons and whose other fields contain the replacement documents.
- Constraints:
  Each body item selects its target by `id`. Every item must be convertible to `PersonEntity`. `replaceOne` is used without upsert, so valid ids that do not match existing documents are not inserted. To receive a modified count equal to the number of submitted persons, each id must match an existing document and each replacement must modify stored content.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Multiple persons exist in the collection. This can be satisfied by direct database insertion or by calling `POST /api/persons`.
    - At least one replacement body item has `id` set to a non-ObjectId string.
  - Failure endpoint:
    `PUT /api/persons`
  - Why this fails:
    The service converts the whole request array with `personEntities.stream().map(PersonDTO::toPersonEntity).toList()` before calling the repository, so malformed ids fail before any bulk write is executed.
  - Intentionally violated constraints:
    Every replacement body item `id` must be a valid MongoDB ObjectId string.
- Branch 2:
  - Preconditions:
    - At least one replacement body item uses an id for an existing person. This can be satisfied by direct database insertion or by calling `POST /api/persons`.
    - At least one other replacement body item uses a valid ObjectId that does not identify any existing person. This can be produced by omitting that document, deleting it before the request, or using an unrelated valid ObjectId.
  - Failure endpoint:
    `PUT /api/persons`
  - Why this fails:
    It does not fail at HTTP level. `replaceOne` has no upsert, so unmatched ids are not updated and the modified count is lower than the number of submitted items or zero.
  - Intentionally violated constraints:
    Every intended update body `id` must identify an existing person.
- Branch 3:
  - Preconditions:
    - Multiple persons exist in the collection. This can be satisfied by direct database insertion or by calling `POST /api/persons`.
    - At least one replacement body item has `address: null`, `cars: null`, or a null item inside `cars`.
  - Failure endpoint:
    `PUT /api/persons`
  - Why this fails:
    DTO conversion dereferences nested fields before the bulk write is executed.
  - Intentionally violated constraints:
    Every replacement body item must contain non-null `address`, non-null `cars`, and only non-null car items.

Endpoint coverage:
- Covers:
  `PUT /api/persons`
- Distinct meaning:
  Bulk replacement of existing persons by body ids.

### Function 10: delete person by id

Function name:
delete person by id

Core endpoint(s):
- `DELETE /api/person/{id}`

Preconditions:
- A person exists in the `persons` collection. This can be satisfied by directly inserting a `PersonEntity` document, or by calling `POST /api/person` with a convertible JSON `PersonDTO` body containing the person values and non-null `address` and `cars`.
- The `{id}` path value used by `DELETE /api/person/{id}` must identify that existing person. If the API is used to establish the person, `{id}` must be obtained from the `id` field returned by `POST /api/person`.

Successful execution:
- Result:
  The endpoint deletes the person whose MongoDB `_id` equals `{id}` and returns the deleted count, normally `1`.
- Invocation:
  Step 1: `DELETE /api/person/{id}` with `{id}` set to the existing person id.
- Constraints:
  `{id}` must be a valid MongoDB ObjectId string. The OpenAPI file omits the path parameter definition, but the controller requires `{id}`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No person exists with `_id = {id}`. This can be satisfied by starting from an empty collection, deleting the person beforehand, directly omitting the document, or using a valid ObjectId unrelated to any `POST /api/person` response.
  - Failure endpoint:
    `DELETE /api/person/{id}`
  - Why this fails:
    It does not fail at HTTP level. MongoDB deletes zero documents and the endpoint returns `0`.
  - Intentionally violated constraints:
    `{id}` must identify the person intended to be deleted.
- Branch 2:
  - Preconditions:
    - The `{id}` path value is not a valid MongoDB ObjectId string.
  - Failure endpoint:
    `DELETE /api/person/{id}`
  - Why this fails:
    `MongoDBPersonRepository.delete(String id)` constructs `new ObjectId(id)`.
  - Intentionally violated constraints:
    `{id}` must be a valid MongoDB ObjectId string.

Endpoint coverage:
- Covers:
  `DELETE /api/person/{id}`
- Distinct meaning:
  Deletes one person by id.

### Function 11: delete multiple persons by ids

Function name:
delete multiple persons by ids

Core endpoint(s):
- `DELETE /api/persons/{ids}`

Preconditions:
- One or more persons exist in the `persons` collection. This can be satisfied by directly inserting `PersonEntity` documents, or by calling `POST /api/persons` with a JSON array of convertible `PersonDTO` bodies containing the person values and non-null `address` and `cars`.
- The `{ids}` path value must be a comma-separated list of ids. If the API is used to establish the persons, each intended deletion id in `{ids}` must be obtained from an `id` value returned by `POST /api/persons`.

Successful execution:
- Result:
  The endpoint deletes all persons whose MongoDB `_id` values are included in `{ids}` and returns the deleted count.
- Invocation:
  Step 1: `DELETE /api/persons/{ids}` with `{ids}` set to comma-separated MongoDB ObjectId strings.
- Constraints:
  Every `{ids}` segment must be a valid MongoDB ObjectId string. The OpenAPI file omits the path parameter definition, but the controller requires `{ids}`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - At least one segment in the comma-separated `{ids}` path value is not a valid MongoDB ObjectId string. Other segments may be valid ids returned by `POST /api/persons` or inserted directly.
  - Failure endpoint:
    `DELETE /api/persons/{ids}`
  - Why this fails:
    The repository converts all segments to ObjectIds before executing `deleteMany`.
  - Intentionally violated constraints:
    Every `{ids}` segment must be a valid MongoDB ObjectId string.
- Branch 2:
  - Preconditions:
    - At least one id in `{ids}` identifies an existing person, and at least one other id is a valid ObjectId that does not identify any person. The existing person can be created by direct database insertion or by `POST /api/persons`; the missing id can be produced by omitting that document or deleting it before the request.
  - Failure endpoint:
    `DELETE /api/persons/{ids}`
  - Why this fails:
    It does not fail at HTTP level. MongoDB deletes only matching documents, so the returned deleted count is lower than the number of requested ids or zero.
  - Intentionally violated constraints:
    Every id intended for deletion must identify an existing person.

Endpoint coverage:
- Covers:
  `DELETE /api/persons/{ids}`
- Distinct meaning:
  Deletes a selected subset of persons by comma-separated ids.

### Function 12: delete all persons

Function name:
delete all persons

Core endpoint(s):
- `DELETE /api/persons`

Preconditions:
- None. The collection may be empty or populated.

Successful execution:
- Result:
  The endpoint deletes every document in the `persons` collection and returns the deleted count. If the collection is empty, it returns `0`.
- Invocation:
  Step 1: `DELETE /api/persons` with no path, query, body, form, or header values required by the controller.
- Constraints:
  No prerequisite person is required. The endpoint removes all stored persons in one `deleteMany` operation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The `persons` collection is already empty. This can be produced by starting from an empty database, directly clearing the collection, or calling `DELETE /api/persons` before the request.
  - Failure endpoint:
    `DELETE /api/persons`
  - Why this fails:
    It does not fail at HTTP level. The endpoint deletes zero documents and returns `0`.
  - Intentionally violated constraints:
    None; empty collection deletion is an idempotent zero-count outcome.
- Branch 2:
  - Preconditions:
    - A generic database or runtime failure occurs while deleting the collection contents.
  - Failure endpoint:
    `DELETE /api/persons`
  - Why this fails:
    The controller catches `RuntimeException` through its exception handler and returns `500 Internal Server Error`.
  - Intentionally violated constraints:
    No endpoint-reproducible API constraint is visible beyond database/runtime availability.

Endpoint coverage:
- Covers:
  `DELETE /api/persons`
- Distinct meaning:
  Deletes the full person collection.

Unclear or auxiliary endpoints: none. Every OpenAPI endpoint maps to a `PersonController` method.
