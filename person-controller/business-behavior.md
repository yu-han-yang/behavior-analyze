# Domain-Level Behavior Analysis

## Domain Summary

This service is a small MongoDB-backed person registry. Its main resource is a `Person`, identified by a server-assigned MongoDB ObjectId string and carrying profile attributes: `firstName`, `lastName`, `age`, embedded `address`, `createdAt`, `insurance`, and embedded `cars`.

The business model is document-oriented. Address and car details are not first-class resources; they are embedded inside a person and can only be created, read, replaced, or deleted as part of the containing person document. The service supports individual and batch person creation, full-document replacement, selected retrieval and deletion by ObjectId, full collection listing and clearing, total count, and collection-wide average age.

Implementation behavior takes priority over the OpenAPI contract. The OpenAPI file omits path parameter definitions for `/api/person/{id}` and `/api/persons/{ids}`, but the controller requires those path values. The controller appears to intend `404 Not Found` for a missing person lookup, but the service constructs `new PersonDTO(null)`, so the implemented result is a runtime exception handled as `500 Internal Server Error`.

## Available Function Inventory

### Person Profile Lifecycle

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `create person` | `POST /api/person` | Register one person profile with embedded address and car data; the service assigns a new MongoDB ObjectId. |
| `create multiple persons` | `POST /api/persons` | Register multiple person profiles from a JSON array; each accepted person receives a new MongoDB ObjectId. |
| `replace person` | `PUT /api/person` | Fully replace one existing person profile selected by the `id` inside the request body. |
| `replace multiple persons` | `PUT /api/persons` | Fully replace multiple existing person profiles selected by each request body's `id`; returns MongoDB's modified count. |
| `delete person by id` | `DELETE /api/person/{id}` | Delete one person whose `_id` matches the `{id}` path value. |
| `delete multiple persons by ids` | `DELETE /api/persons/{ids}` | Delete selected persons whose `_id` values are included in the comma-separated `{ids}` path value. |
| `delete all persons` | `DELETE /api/persons` | Delete every person document in the registry. |

### Person Profile Read and Selection

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list persons` | `GET /api/persons` | Read every stored person profile. |
| `retrieve person by id` | `GET /api/person/{id}` | Read one person profile by MongoDB ObjectId. |
| `retrieve multiple persons by ids` | `GET /api/persons/{ids}` | Read the subset of person profiles whose ObjectIds are included in a comma-separated path value. |

### Registry Metrics

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `count persons` | `GET /api/persons/count` | Return the number of person documents in the registry. |
| `compute average age` | `GET /api/persons/averageAge` | Return the average `age` value across the whole person collection. |

## Supported Business Behaviors

### Behavior 1: Register One Person Profile

Business goal:
Create a person profile containing identity attributes, age, insurance flag, address, and cars.

Domain context:
This is the basic onboarding action for the person registry. Address and cars are captured as embedded attributes of the person rather than independently managed records.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create person` (`POST /api/person`) with JSON body `id=null` or omitted, `firstName="Maxime"`, `lastName="Beugnet"`, `age=31`, `address={number=1, street="The Best Street", postcode="12345", city="Paris", country="France"}`, `createdAt=<optional date-time>`, `insurance=true`, and `cars=[{brand="Ferrari", model="SF90 Stradale", maxSpeedKmH=339.0}]` to insert one person document.

Optional verification workflow:
1. Use function `retrieve person by id` (`GET /api/person/{id}`) with `id=<id returned by create person>` to inspect the created profile.
2. Use function `count persons` (`GET /api/persons/count`) with no request values to inspect the registry size after creation.

Existing-state shortcuts:
- No setup function is needed for the main workflow.
- Direct database insertion is an alternative only for creating equivalent persisted state for later workflows; it is not equivalent to this behavior because it bypasses the API's generated-id response.

Parameter and value bindings:
- The response field `id` from `create person` is the generated MongoDB ObjectId string for the new profile and must be reused by later read, replace, or delete functions that target this person.
- If the request body includes an `id`, it must be a valid ObjectId string, but the repository overwrites it with a new ObjectId before insertion. The submitted `id` is not the persisted identity.
- The submitted `address` object and `cars` array become embedded fields inside the created person document.

Business result:
One person document exists in the `persons` collection with a server-assigned `_id`, the submitted profile values, embedded address, embedded cars, and the submitted `insurance` flag.

Constraints and invariants:
- `address` must be non-null.
- `cars` must be non-null and must not contain null items.
- A submitted non-null `id` must be syntactically valid as a MongoDB ObjectId, even though it is overwritten.
- There is no uniqueness rule for names, addresses, cars, or insurance values.
- Age is an `int`; the implementation does not enforce a positive or realistic age.

Failure and exceptional cases:
- Failing function: `create person`
  - Failure condition: Body `id` is a non-ObjectId string.
  - Why it fails: `PersonDTO.toPersonEntity()` constructs `new ObjectId(id)` before repository insertion.
  - Violated prerequisite or constraint: Submitted `id` must be absent or a valid ObjectId string.
- Failing function: `create person`
  - Failure condition: Body `address=null`, `cars=null`, or `cars` contains a null item.
  - Why it fails: DTO conversion dereferences `address`, streams `cars`, and maps each car through `CarDTO.toCarEntity()`.
  - Violated prerequisite or constraint: Embedded address and car list must be convertible.

Implementation notes:
The OpenAPI schema does not mark nested fields as required, but the conversion code effectively requires `address` and `cars`. The repository always calls `personEntity.setId(new ObjectId())` before `insertOne`, so client-supplied IDs are not preserved.

### Behavior 2: Register a Batch of Person Profiles

Business goal:
Create several person profiles in one API request.

Domain context:
This supports bulk onboarding of people while preserving each profile as an independent person document.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create multiple persons` (`POST /api/persons`) with JSON array body containing at least two valid `PersonDTO` objects, for example item 1 with `firstName="Maxime"`, `lastName="Beugnet"`, `age=31`, non-null `address`, `insurance=true`, `cars=[...]`, and item 2 with `firstName="Alex"`, `lastName="Beugnet"`, `age=27`, non-null `address`, `insurance=false`, `cars=[...]`, to insert the batch.

Optional verification workflow:
1. Use function `retrieve multiple persons by ids` (`GET /api/persons/{ids}`) with `ids=<id1>,<id2>` captured from the batch creation response to inspect the created cohort.
2. Use function `count persons` (`GET /api/persons/count`) with no request values to inspect total registry size.

Existing-state shortcuts:
- No setup function is needed for the main workflow.
- Direct database insertion can create equivalent profile documents for later read, replace, delete, or metric workflows, but it bypasses the API response that returns generated IDs.

Parameter and value bindings:
- Each response item from `create multiple persons` contains a generated `id`; those IDs must be reused as comma-separated `{ids}` for selected retrieval or deletion, or as body `id` values for replacement.
- The array order should not be treated as a stable business identity. The durable binding is each returned `id`.
- If any submitted item includes `id`, it must be a valid ObjectId string, but the persisted ID is newly generated.

Business result:
Multiple person documents exist, each with its own generated `_id` and embedded address and car details.

Constraints and invariants:
- Every batch item must satisfy the same conversion constraints as a single person.
- The service implementation uses a stream with `peek(personRepository::save)`, so it saves items one by one through `save`, not through the repository's transactional `saveAll`.
- There is no API-level uniqueness or duplicate detection.

Failure and exceptional cases:
- Failing function: `create multiple persons`
  - Failure condition: One item has an invalid `id`, null `address`, null `cars`, or a null car item.
  - Why it fails: The service converts and saves each stream item sequentially; conversion or ObjectId parsing throws for the invalid item.
  - Violated prerequisite or constraint: Every submitted person must be convertible.
- Failing function: `create multiple persons`
  - Failure condition: A later batch item is invalid after earlier items were valid.
  - Why it fails: Earlier items may already have been inserted because the service saves sequentially and does not use a transaction for this endpoint path.
  - Violated prerequisite or constraint: Batch creation is not atomic in the controller-service implementation.

Implementation notes:
`MongoDBPersonRepository.saveAll()` is transactional, but `PersonServiceImpl.saveAll()` does not call it. This creates a meaningful implementation discrepancy inside the codebase: the repository has an atomic-looking helper that the API does not use.

### Behavior 3: List the Person Registry

Business goal:
Inspect all person profiles currently stored in the registry.

Domain context:
This is the broad registry view. It is useful for administrative inspection and for obtaining IDs when the caller does not already know them.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `list persons` (`GET /api/persons`) with no path, query, body, form, or header values to return the full collection.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup step is needed. If a non-empty list is desired, previous `create person` or `create multiple persons` steps, or direct database insertion, can establish records before listing.
- Direct database setup must still create documents with non-null address and cars if the API response needs to convert them successfully.

Parameter and value bindings:
- This behavior does not require inbound value reuse.
- IDs returned by `list persons` can be captured and reused as `{id}` for `retrieve person by id` or `delete person by id`, as comma-separated `{ids}` for selected retrieval/deletion, or as body `id` values for replacement.

Business result:
No state changes. The response is a JSON array of every convertible person document, or an empty array when the collection has no persons.

Constraints and invariants:
- The listing is collection-wide and has no pagination, filtering, sorting, or ownership scope.
- Stored documents must be convertible into `PersonDTO`; malformed database records can break listing.

Failure and exceptional cases:
- Failing function: `list persons`
  - Failure condition: A persisted person has null `address`, null `cars`, or a null item inside `cars`.
  - Why it fails: `PersonServiceImpl.findAll()` maps each entity to `PersonDTO`, whose constructor dereferences those nested fields.
  - Violated prerequisite or constraint: Stored person documents must have convertible embedded fields.
- Failing function: `list persons`
  - Failure condition: Database or runtime failure while reading.
  - Why it fails: The controller catches `RuntimeException` and returns `500 Internal Server Error`.
  - Violated prerequisite or constraint: Database availability.

Implementation notes:
The API exposes no query parameters. Any caller-side filtering must happen after retrieving the full collection.

### Behavior 4: Retrieve One Known Person Profile

Business goal:
Read the full profile for a specific person.

Domain context:
The registry identity is the generated MongoDB ObjectId. A caller must know or first obtain that ID before a precise single-person lookup.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create person` (`POST /api/person`) with a valid `PersonDTO` body containing non-null `address` and non-null `cars` to establish a person and capture response field `id=<personId>`.
2. Use function `retrieve person by id` (`GET /api/person/{id}`) with path `id=<personId>` to read that person.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped if an equivalent person already exists and its ObjectId string is known.
- Direct database setup can replace step 1 if it creates a valid `PersonEntity` document and the caller obtains the generated or inserted `_id`.
- The `id` used in step 2 must still be a valid ObjectId string and must identify a convertible stored person.

Parameter and value bindings:
- The `id` response field from `create person` is reused exactly as the `{id}` path value in `retrieve person by id`.
- The lookup does not use names, address values, or car values; only `_id` controls selection.

Business result:
No additional state changes after setup. The caller receives the person profile whose `_id` matches the reused generated ID.

Constraints and invariants:
- `{id}` must be syntactically valid as a MongoDB ObjectId.
- `{id}` must match an existing person for successful retrieval.
- The OpenAPI file omits the path parameter definition, but the controller requires it.

Failure and exceptional cases:
- Failing function: `create person`
  - Failure condition: Setup body has null `address` or null `cars`.
  - Why it fails: DTO conversion throws before insertion.
  - Violated prerequisite or constraint: A retrievable profile must first be created with convertible embedded fields.
- Failing function: `retrieve person by id`
  - Failure condition: `{id}` is malformed.
  - Why it fails: Repository lookup constructs `new ObjectId(id)`.
  - Violated prerequisite or constraint: Path `id` must be a valid ObjectId string.
- Failing function: `retrieve person by id`
  - Failure condition: `{id}` is valid but no person exists with that `_id`.
  - Why it fails: Repository returns `null`; service constructs `new PersonDTO(null)`, causing a runtime exception before the controller's intended null check can return `404`.
  - Violated prerequisite or constraint: Path `id` must identify an existing person.

Implementation notes:
The intended controller behavior for missing persons is `404 Not Found`, but the implemented result is `500 Internal Server Error`.

### Behavior 5: Retrieve a Selected Cohort by IDs

Business goal:
Read a selected group of person profiles by known registry IDs.

Domain context:
This supports batch inspection when the caller already has a set of person IDs from creation, listing, or an external database process.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create multiple persons` (`POST /api/persons`) with a valid JSON array of two or more person bodies to establish the cohort and capture response IDs `id1`, `id2`, and any further IDs.
2. Use function `retrieve multiple persons by ids` (`GET /api/persons/{ids}`) with path `ids=<id1>,<id2>` to read the selected persons.

Optional verification workflow:
1. Use function `count persons` (`GET /api/persons/count`) with no request values to compare total registry size with the selected cohort size.

Existing-state shortcuts:
- Step 1 can be skipped if equivalent persons already exist and their ObjectId strings are known.
- Direct database setup can replace step 1 if it creates valid person documents and exposes their `_id` values.
- Every segment in `{ids}` must still be a valid ObjectId string. Missing valid IDs are silently omitted rather than reported.

Parameter and value bindings:
- Each generated response `id` from `create multiple persons` is reused as one comma-separated segment in `{ids}`.
- The path value must use the exact ID strings, separated by commas, for example `ids=<id1>,<id2>`.

Business result:
No state changes after setup. The response contains the stored persons whose `_id` values match the supplied IDs; valid IDs without matching documents are absent from the response.

Constraints and invariants:
- All `{ids}` segments must be valid ObjectId strings.
- Selection is by ID only; it is not filtered by name, address, age, insurance, car, or caller ownership.
- Response order is not guaranteed by the domain contract.

Failure and exceptional cases:
- Failing function: `create multiple persons`
  - Failure condition: A setup item is invalid.
  - Why it fails: DTO conversion or ObjectId parsing throws; earlier valid stream items may already be saved.
  - Violated prerequisite or constraint: A valid cohort must be established before selected retrieval.
- Failing function: `retrieve multiple persons by ids`
  - Failure condition: Any `{ids}` segment is malformed.
  - Why it fails: The repository maps every segment through `new ObjectId(...)` before querying.
  - Violated prerequisite or constraint: Every path segment must be a valid ObjectId.
- Failing function: `retrieve multiple persons by ids`
  - Failure condition: A valid ID does not match a stored person.
  - Why it fails: It does not fail at HTTP level; MongoDB returns only matching documents.
  - Violated prerequisite or constraint: Every expected cohort member must still exist.

Implementation notes:
The OpenAPI file lists `/api/persons/{ids}` but omits the path parameter definition. The controller parses the path by `ids.split(",")`.

### Behavior 6: Count Registered Persons

Business goal:
Measure how many person profiles are stored.

Domain context:
Count is a collection-level metric useful after onboarding, deletion, or cleanup workflows.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `count persons` (`GET /api/persons/count`) with no path, query, body, form, or header values to retrieve the current document count.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup step is needed. The behavior works on an empty collection and returns `0`.
- If the business question is "how many after onboarding", use `create person` or `create multiple persons` first, then run this behavior.

Parameter and value bindings:
- This behavior has no request value reuse.
- The returned count can be compared with the number of successful create or delete operations, but it is not a cursor or stable snapshot token.

Business result:
No state changes. The response is the total number of documents in the `persons` collection.

Constraints and invariants:
- Count is collection-wide and unfiltered.
- It includes all person documents regardless of age, insurance, address, or car values.

Failure and exceptional cases:
- Failing function: `count persons`
  - Failure condition: Database or runtime failure while counting.
  - Why it fails: `personCollection.countDocuments()` throws and the controller returns `500 Internal Server Error`.
  - Violated prerequisite or constraint: Database availability.

Implementation notes:
The endpoint is safe for an empty collection and returns `0`.

### Behavior 7: Compute Average Age for the Registry

Business goal:
Calculate the average age across all registered persons.

Domain context:
This is the only analytical behavior exposed by the service. It operates over the entire person registry, not a subset.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create person` (`POST /api/person`) with a valid `PersonDTO` body such as `age=31`, non-null `address`, and non-null `cars` to ensure at least one person exists.
2. Use function `compute average age` (`GET /api/persons/averageAge`) with no request values to compute the collection-wide average.

Optional verification workflow:
1. Use function `list persons` (`GET /api/persons`) with no request values to inspect the ages contributing to the average.
2. Use function `count persons` (`GET /api/persons/count`) with no request values to inspect the denominator.

Existing-state shortcuts:
- Step 1 can be skipped if at least one person already exists.
- Direct database setup can replace step 1 if it creates at least one document with an `age` field.
- The calculation is always collection-wide, so any existing extra persons also affect the result.

Parameter and value bindings:
- The `age` body value submitted to `create person` is later consumed indirectly by the aggregation in `compute average age`.
- No ID is needed by the average endpoint, although the created ID can be used for cleanup after the metric is computed.

Business result:
At least one person exists, and the response returns the numeric average of all stored `age` values.

Constraints and invariants:
- The collection must not be empty.
- Age values are not validated for domain realism, so negative or implausible ages would still contribute to the average if persisted.
- There is no query parameter to compute average age for selected IDs or filtered cohorts.

Failure and exceptional cases:
- Failing function: `create person`
  - Failure condition: Setup body has invalid nested fields.
  - Why it fails: DTO conversion throws before insertion.
  - Violated prerequisite or constraint: At least one convertible person must exist.
- Failing function: `compute average age`
  - Failure condition: The collection is empty.
  - Why it fails: The aggregation returns no first result, and `MongoDBPersonRepository.getAverageAge()` calls `.averageAge()` on `null`.
  - Violated prerequisite or constraint: At least one person document must exist.

Implementation notes:
The aggregation pipeline groups all documents and projects only `averageAge`. The tests request the value as `Long`, but the controller returns `Double`.

### Behavior 8: Replace One Person Profile

Business goal:
Fully update an existing person profile while keeping the same registry identity.

Domain context:
The service does not support partial patching. Updating age, insurance, address, cars, or names requires submitting a full replacement document with the existing `id`.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create person` (`POST /api/person`) with valid original body values such as `firstName="Maxime"`, `age=31`, `insurance=true`, non-null `address`, and non-null `cars`; capture response `id=<personId>`.
2. Use function `replace person` (`PUT /api/person`) with JSON body `id=<personId>`, updated fields such as `age=32`, `insurance=false`, and complete non-null replacement values for `firstName`, `lastName`, `address`, `createdAt`, and `cars` to replace the stored profile.

Optional verification workflow:
1. Use function `retrieve person by id` (`GET /api/person/{id}`) with `id=<personId>` to verify the replacement.
2. Use function `count persons` (`GET /api/persons/count`) with no request values to verify replacement did not create an additional profile.

Existing-state shortcuts:
- Step 1 can be skipped if the person already exists and its ObjectId string is known.
- Direct database setup can replace step 1 if it creates a valid person and the replacement body reuses that exact `_id`.
- The core replacement step cannot be skipped for this behavior.

Parameter and value bindings:
- The `id` returned by `create person` must be reused exactly as the body `id` in `replace person`.
- The body `id` selects the target; there is no path ID.
- Fields not intended to change must still be included in the replacement body, otherwise they will be replaced by null or default values if conversion permits them.

Business result:
One existing person document with `_id=<personId>` is replaced. The identity remains the same, while submitted profile fields such as `age`, `insurance`, address, and cars reflect the replacement body.

Constraints and invariants:
- Replacement is full-document replacement, not merge or patch.
- Body `id` must be a valid ObjectId string and must identify an existing document.
- `address` and `cars` must remain non-null in the replacement body.
- There is no optimistic locking, ETag, or version check.

Failure and exceptional cases:
- Failing function: `create person`
  - Failure condition: Setup profile is invalid.
  - Why it fails: DTO conversion rejects null required embedded values or invalid ObjectId syntax.
  - Violated prerequisite or constraint: A valid target profile must exist before replacement.
- Failing function: `replace person`
  - Failure condition: Body `id` is omitted or uses a valid ObjectId that does not match any document.
  - Why it fails: Omitted `id` generates a new ObjectId during DTO conversion; `findOneAndReplace` finds no match and returns `null`; the service then constructs `new PersonDTO(null)`.
  - Violated prerequisite or constraint: Replacement body `id` must reuse an existing person ID.
- Failing function: `replace person`
  - Failure condition: Replacement body has null `address`, null `cars`, or a null car item.
  - Why it fails: DTO conversion dereferences nested fields before repository replacement.
  - Violated prerequisite or constraint: Full replacement body must be convertible.

Implementation notes:
The service returns the replaced document after `findOneAndReplace` with `ReturnDocument.AFTER`. Missing targets produce a `500`, not a domain-specific not-found response.

### Behavior 9: Replace Multiple Person Profiles

Business goal:
Apply full-document replacements to several existing person profiles in one request.

Domain context:
This supports batch maintenance of person profiles, for example changing age and insurance flags for a known cohort.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create multiple persons` (`POST /api/persons`) with valid person array body to establish target profiles and capture `id1`, `id2`, and any further IDs.
2. Use function `replace multiple persons` (`PUT /api/persons`) with JSON array body where item 1 has `id=<id1>` and complete replacement fields, item 2 has `id=<id2>` and complete replacement fields, and every item includes non-null `address` and non-null `cars`.

Optional verification workflow:
1. Use function `retrieve multiple persons by ids` (`GET /api/persons/{ids}`) with `ids=<id1>,<id2>` to inspect the replaced profiles.
2. Use function `count persons` (`GET /api/persons/count`) with no request values to verify the operation did not insert new persons.

Existing-state shortcuts:
- Step 1 can be skipped if all target persons already exist and their ObjectId strings are known.
- Direct database setup can replace step 1 if it creates valid documents and exposes the exact IDs.
- All replacement body `id` values must still identify existing target documents to get a modified count matching the intended batch size.

Parameter and value bindings:
- Each generated ID from `create multiple persons` is reused as the body `id` for the corresponding replacement item.
- The replacement item does not have to preserve the original name, age, address, insurance, or car values, but it must preserve the intended target `id` to update the correct person.

Business result:
All matched person documents are replaced with the submitted full representations. The response is the number of documents MongoDB reports as modified.

Constraints and invariants:
- This is not an upsert. Valid IDs that do not match existing documents are not inserted.
- If a replacement document is identical to the stored document, MongoDB's modified count may be lower than the number of matched documents.
- Conversion of all request items occurs before the repository bulk write, so malformed item IDs or nested nulls prevent the write.

Failure and exceptional cases:
- Failing function: `create multiple persons`
  - Failure condition: Batch setup contains an invalid item.
  - Why it fails: Sequential create path may throw after inserting earlier valid items.
  - Violated prerequisite or constraint: All targets should be established before batch replacement.
- Failing function: `replace multiple persons`
  - Failure condition: Any replacement body `id` is malformed.
  - Why it fails: The service converts the whole request array to `PersonEntity` before calling the repository.
  - Violated prerequisite or constraint: Every replacement body `id` must be a valid ObjectId.
- Failing function: `replace multiple persons`
  - Failure condition: A valid body `id` does not match an existing person.
  - Why it fails: It does not fail at HTTP level; `replaceOne` has no upsert, so the modified count is lower than the intended count or zero.
  - Violated prerequisite or constraint: Every intended target ID must exist.

Implementation notes:
The repository uses `bulkWrite` inside a transaction for replacements, but the response only reports modified count. It does not report which IDs were missing or unchanged.

### Behavior 10: Delete One Person Profile

Business goal:
Remove one person profile from the registry.

Domain context:
This supports individual offboarding or cleanup of a known person document.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create person` (`POST /api/person`) with a valid `PersonDTO` body to establish a profile and capture response `id=<personId>`.
2. Use function `delete person by id` (`DELETE /api/person/{id}`) with path `id=<personId>` to delete that profile.

Optional verification workflow:
1. Use function `count persons` (`GET /api/persons/count`) with no request values to inspect the remaining registry size.
2. Use function `retrieve person by id` (`GET /api/person/{id}`) with `id=<personId>` only to confirm absence, recognizing that the implemented absence result is `500`, not a clean `404`.

Existing-state shortcuts:
- Step 1 can be skipped if the target person already exists and its ObjectId string is known.
- Direct database setup can replace step 1 if it creates the target document and exposes its `_id`.
- The delete step cannot be skipped for this behavior.

Parameter and value bindings:
- The generated `id` from `create person` is reused exactly as the `{id}` path value in `delete person by id`.
- Delete selection is by `_id` only; profile fields are not checked before removal.

Business result:
The person document with `_id=<personId>` no longer exists. The endpoint normally returns deleted count `1`.

Constraints and invariants:
- `{id}` must be a valid ObjectId string.
- Deleting a missing but valid ID is treated as a successful zero-count outcome, not as an error.
- There are no child records to cascade because address and cars are embedded in the deleted document.

Failure and exceptional cases:
- Failing function: `create person`
  - Failure condition: Setup body is invalid.
  - Why it fails: DTO conversion rejects invalid ObjectId syntax or null required nested values.
  - Violated prerequisite or constraint: A target person must exist for a one-person deletion with count `1`.
- Failing function: `delete person by id`
  - Failure condition: `{id}` is malformed.
  - Why it fails: Repository deletion constructs `new ObjectId(id)`.
  - Violated prerequisite or constraint: Path `id` must be a valid ObjectId.
- Failing function: `delete person by id`
  - Failure condition: `{id}` is valid but no document matches it.
  - Why it fails: It does not fail at HTTP level; MongoDB deletes zero documents and the response is `0`.
  - Violated prerequisite or constraint: The ID must identify the intended person to actually remove anything.

Implementation notes:
The OpenAPI file omits the path parameter definition for `/api/person/{id}`. The controller still requires the path variable.

### Behavior 11: Delete a Selected Cohort by IDs

Business goal:
Remove several selected person profiles while leaving other registry entries untouched.

Domain context:
This supports batch offboarding or cleanup when the caller has the exact IDs of the persons to remove.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create multiple persons` (`POST /api/persons`) with a valid JSON array to establish at least two target profiles and capture `id1` and `id2`.
2. Use function `delete multiple persons by ids` (`DELETE /api/persons/{ids}`) with path `ids=<id1>,<id2>` to delete those selected profiles.

Optional verification workflow:
1. Use function `count persons` (`GET /api/persons/count`) with no request values to inspect remaining registry size.
2. Use function `retrieve multiple persons by ids` (`GET /api/persons/{ids}`) with `ids=<id1>,<id2>` to inspect whether any selected profiles remain; an empty array indicates no matching selected persons.

Existing-state shortcuts:
- Step 1 can be skipped if all intended target persons already exist and their ObjectId strings are known.
- Direct database setup can replace step 1 if it creates the target documents and exposes their IDs.
- Every comma-separated ID must still be valid; missing valid IDs are silently ignored.

Parameter and value bindings:
- IDs returned by `create multiple persons` are joined with commas and reused as the `{ids}` path value for `delete multiple persons by ids`.
- The same comma-separated ID string can be reused for optional selected retrieval after deletion.

Business result:
All matching selected person documents are removed. The response is the number of deleted documents.

Constraints and invariants:
- Every `{ids}` segment must be a valid ObjectId string.
- The delete is selected only by `_id`; it does not verify names, ownership, or other profile fields.
- The repository performs selected deletion inside a transaction.

Failure and exceptional cases:
- Failing function: `create multiple persons`
  - Failure condition: Setup batch contains an invalid item.
  - Why it fails: Sequential creation may persist earlier valid items before failing on a later invalid item.
  - Violated prerequisite or constraint: All intended target IDs should exist before selected deletion.
- Failing function: `delete multiple persons by ids`
  - Failure condition: Any `{ids}` segment is malformed.
  - Why it fails: The repository converts every segment to `ObjectId` before `deleteMany`.
  - Violated prerequisite or constraint: Every path segment must be a valid ObjectId.
- Failing function: `delete multiple persons by ids`
  - Failure condition: One or more valid IDs do not match any person.
  - Why it fails: It does not fail at HTTP level; only matching documents are deleted and the returned deleted count is lower than requested.
  - Violated prerequisite or constraint: Every intended deletion ID must still exist.

Implementation notes:
The endpoint cannot report per-ID success or failure. The only outcome detail is a count.

### Behavior 12: Clear the Person Registry

Business goal:
Remove all person profiles from the registry.

Domain context:
This is a collection-level cleanup action, useful for resetting the registry in tests or administrative workflows.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create multiple persons` (`POST /api/persons`) with a valid person array body to establish registry contents for the clear operation.
2. Use function `delete all persons` (`DELETE /api/persons`) with no path, query, body, form, or header values to remove all person documents.

Optional verification workflow:
1. Use function `count persons` (`GET /api/persons/count`) with no request values to verify the registry count is `0`.
2. Use function `list persons` (`GET /api/persons`) with no request values to verify the registry returns an empty list.

Existing-state shortcuts:
- Step 1 can be skipped if the registry already contains the data to be cleared.
- Direct database setup can replace step 1 when the goal is to clear pre-existing data.
- If the collection is already empty, the core `delete all persons` function still succeeds with deleted count `0`.

Parameter and value bindings:
- This behavior does not require ID reuse because `delete all persons` operates on the full collection.
- Any IDs returned by setup creation become invalid for retrieval after the clear operation because their documents are removed.

Business result:
The `persons` collection contains no person documents. The response is the number of documents removed.

Constraints and invariants:
- The clear operation is unscoped and unfiltered.
- There is no ownership, tenant, or confirmation parameter.
- The repository performs the delete-all operation inside a transaction.

Failure and exceptional cases:
- Failing function: `create multiple persons`
  - Failure condition: Setup batch contains an invalid item.
  - Why it fails: Sequential creation may partially populate the registry before failing.
  - Violated prerequisite or constraint: Setup did not cleanly establish the intended initial state.
- Failing function: `delete all persons`
  - Failure condition: The collection is already empty.
  - Why it fails: It does not fail at HTTP level; the response is deleted count `0`.
  - Violated prerequisite or constraint: None; this is an idempotent zero-count outcome.
- Failing function: `delete all persons`
  - Failure condition: Database or runtime failure during deletion.
  - Why it fails: The controller maps runtime exceptions to `500 Internal Server Error`.
  - Violated prerequisite or constraint: Database availability.

Implementation notes:
Because address and cars are embedded, clearing persons also removes all embedded address and car data. There are no separate cleanup functions for those embedded values.

### Behavior 13: Onboard a Cohort and Report Registry Metrics

Business goal:
Create a group of people and immediately obtain registry-level metrics for total size and average age.

Domain context:
This composite workflow combines bulk onboarding with the service's two aggregate views. It is meaningful for a small administrative registry where the caller wants to know the resulting population size and age statistic after import.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create multiple persons` (`POST /api/persons`) with JSON array body containing valid profiles, for example one person with `age=31` and another with `age=27`, each with non-null `address` and `cars`, to insert the cohort and capture generated IDs.
2. Use function `count persons` (`GET /api/persons/count`) with no request values to retrieve the collection-wide person count after onboarding.
3. Use function `compute average age` (`GET /api/persons/averageAge`) with no request values to retrieve the collection-wide average age after onboarding.

Optional verification workflow:
1. Use function `retrieve multiple persons by ids` (`GET /api/persons/{ids}`) with `ids=<id1>,<id2>` from the create response to inspect the specific onboarded cohort.
2. Use function `list persons` (`GET /api/persons`) with no request values to inspect all persons contributing to the metrics.

Existing-state shortcuts:
- Step 1 can be skipped if the cohort already exists and at least one person is present for average age calculation.
- Direct database setup can replace step 1 if it creates valid person documents with `age` values.
- Existing extra persons cannot be ignored; both count and average age are collection-wide.

Parameter and value bindings:
- The `age` values submitted in `create multiple persons` are consumed indirectly by `compute average age`.
- The generated IDs are not needed for count or average age, but they bind the optional selected verification step to the newly onboarded cohort.

Business result:
The cohort exists in the registry. The caller receives total registry count and collection-wide average age reflecting all persisted persons at the time of the metric requests.

Constraints and invariants:
- This workflow is not isolated from pre-existing data; metrics include the entire collection.
- If batch creation partially persists before failing, subsequent metrics may reflect a partial cohort if the caller still invokes them.
- There is no transaction spanning create, count, and average-age requests.

Failure and exceptional cases:
- Failing function: `create multiple persons`
  - Failure condition: Any cohort item has invalid ID syntax, null address, null cars, or a null car item.
  - Why it fails: DTO conversion throws; earlier valid stream items may already have been inserted.
  - Violated prerequisite or constraint: Cohort creation requires every profile to be convertible.
- Failing function: `compute average age`
  - Failure condition: No person exists when average age is requested.
  - Why it fails: Aggregation returns no result and the repository dereferences `null`.
  - Violated prerequisite or constraint: At least one person must exist.
- Failing function: `count persons`
  - Failure condition: Database failure during count.
  - Why it fails: Runtime exception is handled as `500`.
  - Violated prerequisite or constraint: Database availability.

Implementation notes:
This workflow demonstrates that the service is event-driven only by explicit API calls. Metrics are recomputed on read from the current collection rather than stored with the cohort.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Search or Filter Person Profiles by Business Attributes

Priority:
Critical domain gap

Expected business goal:
Find persons by natural business attributes such as name, age range, city, country, insurance status, car brand, or combinations of these fields.

Why it is unsupported:
The API only supports full listing or ID-based retrieval. There is no query endpoint or query parameter for business attributes.

Existing functions considered:
- `list persons`: Can return the whole registry, but cannot filter server-side or avoid exposing unrelated records.
- `retrieve person by id`: Can fetch one known ID, but cannot discover that ID from business attributes.
- `retrieve multiple persons by ids`: Can fetch known IDs only; it cannot search for IDs.
- `count persons`: Counts the whole collection, not a filtered subset.
- `compute average age`: Aggregates the whole collection, not a filtered subset.

Missing capability:
A search/list endpoint with query parameters or request body filters, plus optional count/aggregate filtering over the same criteria.

Proof that function composition is insufficient:
Chaining `list persons` with client-side filtering can produce a local filtered view only after retrieving every person. It cannot enforce server-side filtering, cannot protect unrelated records from disclosure, cannot page or count filtered results efficiently, and cannot compute filtered average age in the service.

Evidence from existing functions/source:
- `PersonController.getPersons()` accepts no query parameters.
- `MongoDBPersonRepository.findAll()` calls `personCollection.find()` without filters.
- `MongoDBPersonRepository.getAverageAge()` aggregates over the entire collection.

Business impact:
Any workflow that starts from real-world identity or profile attributes is blocked or forced into broad collection reads.

### Missing Behavior 2: Domain-Level Person Identity and Duplicate Prevention

Priority:
Critical domain gap

Expected business goal:
Prevent accidental duplicate person registration or identify an existing person by stable business identity.

Why it is unsupported:
Creation always inserts a new document with a new ObjectId. There is no uniqueness check on names, address, date, insurance, cars, or any external identifier.

Existing functions considered:
- `create person`: Always assigns a new ID and inserts.
- `create multiple persons`: Inserts each valid item independently.
- `list persons`: Can reveal duplicates after the fact, but cannot prevent them.
- `replace person`: Requires an existing ObjectId and cannot merge duplicates.
- `delete person by id`: Can manually remove one duplicate only after the duplicate is known.

Missing capability:
A business key, uniqueness index, duplicate check endpoint, idempotency key, or create-or-return-existing operation.

Proof that function composition is insufficient:
A caller can list all persons and compare fields before creating, but this is race-prone and not enforced by the service. Delete-and-recreate does not preserve identity and cannot prove duplicates were prevented during concurrent calls.

Evidence from existing functions/source:
- `MongoDBPersonRepository.save()` overwrites any supplied ID with a new `ObjectId` and calls `insertOne`.
- The DTO and entity models have no unique business identifier.
- No controller method validates duplicate names or profile attributes.

Business impact:
The registry cannot reliably represent one real-world person as one durable service record.

### Missing Behavior 3: Atomic Bulk Person Import Through the Public API

Priority:
Important robustness gap

Expected business goal:
Import a batch of person profiles so either all valid intended records are created or none are created.

Why it is unsupported:
The public batch creation path saves each item sequentially through `personRepository.save` in a stream. Invalid later items can fail after earlier items have already been inserted.

Existing functions considered:
- `create multiple persons`: Provides batch input but not API-level atomicity.
- `delete multiple persons by ids`: Can clean up known inserted IDs only if the caller received them before failure, which may not happen.
- `delete all persons`: Can remove unrelated records and is not equivalent to rollback.
- `list persons`: Can inspect partial state but cannot distinguish newly inserted partial records from pre-existing similar records reliably.

Missing capability:
The service should use the repository's transactional `saveAll` for the public endpoint or expose transactional import semantics with a clear all-or-nothing response.

Proof that function composition is insufficient:
If batch creation fails after partial insertion, the failed response may not return the IDs already inserted. Without those IDs, the caller cannot safely delete only the partial records. Deleting all records would remove unrelated existing data and is not equivalent to rollback.

Evidence from existing functions/source:
- `PersonServiceImpl.saveAll()` maps, saves with `peek(personRepository::save)`, and returns a list.
- `MongoDBPersonRepository.saveAll()` has transaction logic but is not used by the controller-service path.

Business impact:
Bulk import can leave partial, ambiguous registry state after a single failed request.

### Missing Behavior 4: Clean Not-Found Handling for Person Retrieval and Replacement

Priority:
Important robustness gap

Expected business goal:
Distinguish "person does not exist" from internal service failure when reading or replacing by ID.

Why it is unsupported:
Missing person retrieval and missing single replacement targets produce runtime exceptions and `500 Internal Server Error`.

Existing functions considered:
- `retrieve person by id`: Should be able to report absence, but the service constructs `PersonDTO` from `null`.
- `replace person`: Should report missing target, but replacement returns `null` and the service constructs `PersonDTO` from `null`.
- `delete person by id`: Returns `0` for missing target, but only for delete.
- `retrieve multiple persons by ids`: Silently omits missing IDs, which does not identify which requested persons were absent.

Missing capability:
Consistent `404 Not Found` or structured not-found responses for single-resource read and replacement; per-ID miss reporting for batch operations.

Proof that function composition is insufficient:
Calling `count persons` or `list persons` before retrieval cannot prove a specific ID exists at the moment of lookup. Calling delete to test existence mutates state. Batch retrieval omits missing IDs but cannot provide a clean single-resource not-found response.

Evidence from existing functions/source:
- `PersonController.getPerson()` checks for `null`, but `PersonServiceImpl.findOne()` returns `new PersonDTO(personRepository.findOne(id))` before the controller can see `null`.
- `PersonServiceImpl.update()` similarly constructs `new PersonDTO(...)` from the repository replacement result.

Business impact:
Clients cannot reliably distinguish user-correctable missing IDs from server defects, weakening error handling and support workflows.

### Missing Behavior 5: Partial Updates to Person, Address, Insurance, Age, or Cars

Priority:
API ergonomics gap

Expected business goal:
Change one profile field, address field, insurance flag, age, or car list without resubmitting the entire person document.

Why it is unsupported:
Only full replacement endpoints exist. Address and cars are embedded and have no independent lifecycle endpoints.

Existing functions considered:
- `replace person`: Can change fields only by submitting a full replacement body with all required nested values.
- `replace multiple persons`: Same limitation for batches.
- `retrieve person by id`: Can fetch the current document to help construct a replacement, but cannot perform a partial update.
- `create person`: Creates a new person and does not update an existing one.

Missing capability:
PATCH-style endpoints or field-specific commands for age, insurance, address, and car collection updates.

Proof that function composition is insufficient:
Read-modify-replace is not equivalent to server-side partial update because it has no concurrency token and can overwrite concurrent changes to fields the caller did not intend to modify.

Evidence from existing functions/source:
- `MongoDBPersonRepository.update()` uses `findOneAndReplace`.
- `MongoDBPersonRepository.update(List<PersonEntity>)` uses `ReplaceOneModel`, not `$set` updates.

Business impact:
Small edits require larger payloads and can accidentally erase or stale-overwrite embedded data.

### Missing Behavior 6: Paginated or Stable Registry Browsing

Priority:
API ergonomics gap

Expected business goal:
Browse a large registry page by page with stable ordering and continuation.

Why it is unsupported:
The list endpoint returns the full collection with no `limit`, `offset`, cursor, sort, or filter values.

Existing functions considered:
- `list persons`: Returns all documents.
- `retrieve multiple persons by ids`: Requires IDs already known and does not provide discovery or pagination.
- `count persons`: Gives total size only.

Missing capability:
Pagination and sorting parameters, cursor tokens, or stable page endpoints.

Proof that function composition is insufficient:
The caller cannot ask the server for "next page" or a deterministic sorted slice. Client-side slicing after `list persons` still requires downloading the full registry and is unstable under concurrent changes.

Evidence from existing functions/source:
- `PersonController.getPersons()` has no request parameters.
- `MongoDBPersonRepository.findAll()` has no sort or limit.

Business impact:
The API does not scale cleanly for administrative browsing as the registry grows.

### Missing Behavior 7: Scoped Ownership, Tenant Isolation, or Authorization

Priority:
Important robustness gap

Expected business goal:
Restrict person operations to an owner, tenant, user, or authorized actor.

Why it is unsupported:
No endpoint accepts authentication headers or owner/tenant scope, and the data model has no ownership field.

Existing functions considered:
- `list persons`: Returns all persons.
- `retrieve person by id`: Reads any existing ID.
- `replace person`: Replaces any existing ID if known.
- `delete person by id`, `delete multiple persons by ids`, and `delete all persons`: Delete by ID or entire collection without ownership checks.

Missing capability:
Authentication, authorization, tenant/owner fields, scoped queries, and scoped update/delete filters.

Proof that function composition is insufficient:
Adding caller-side checks cannot protect the service because any caller with an ID can invoke read, replace, or delete functions directly. There is no server-side state that binds a person to an owner.

Evidence from existing functions/source:
- Controller methods accept only path variables or request bodies; no principal or authorization logic appears.
- Repository filters use only `_id` or no filter.

Business impact:
The API is unsuitable for multi-user or tenant-sensitive person data without an external protective layer.

## Cross-Behavior Observations

- Validation is mostly accidental through DTO conversion. The OpenAPI schema allows optional fields, but implementation requires non-null `address` and `cars` for creation, replacement, and response conversion.
- MongoDB ObjectId syntax is a hard constraint for all ID path values and body IDs used for replacement.
- Client-supplied IDs on create are parsed but then overwritten, so creation identity always comes from the server.
- Batch creation is not atomic in the public service path, even though the repository contains an unused transactional `saveAll`.
- Batch replacement, selected deletion, and delete-all use repository transactions.
- Missing single-resource retrieval and replacement produce `500` due to null DTO construction, despite controller code suggesting an intended `404` for retrieval.
- ID-based batch retrieval and deletion silently omit or ignore valid IDs that do not match existing records.
- Address and car data are embedded. There are no independent address or car behaviors, no cascade rules beyond deleting the containing person, and no direct way to query or update embedded values.
- Metrics are collection-wide and computed at read time. There is no filtered average, no cohort-specific average, and no stored aggregate state.
- OpenAPI and implementation disagree for path parameters on `/api/person/{id}` and `/api/persons/{ids}`: the spec omits parameter definitions, while the controller requires path variables.

## Coverage Summary

Supported domain areas:
- Person profile creation, individual and batch.
- Person profile full replacement, individual and batch.
- Person profile retrieval by known ObjectId, individual and selected batch.
- Full registry listing.
- Individual, selected batch, and full registry deletion.
- Collection-wide count and average age.

Partially supported domain areas:
- Bulk import is available but not atomic through the public service implementation.
- Error handling exists through generic `500` handling, but not through clean domain-specific not-found and validation responses.
- Embedded address and car maintenance is possible only through full person replacement.
- Registry metrics exist only for the full collection.

Unsupported domain areas:
- Server-side search, filtering, pagination, sorting, and filtered metrics.
- Duplicate prevention and stable real-world person identity.
- Partial updates for profile fields, addresses, insurance, age, or cars.
- Ownership, authorization, tenant scoping, and audit behavior.
- Per-ID outcome reporting for batch retrieval, replacement, and deletion.
