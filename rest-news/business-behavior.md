# Domain-Level Behavior Analysis

## Domain Summary

The service is a small REST news publication service. Its main business resource is a news item with a generated numeric id, an author id, body text, a country label, and a creation timestamp. The service also exposes a country catalog used as the source of valid country names for persisted news.

The main business concepts are:

- Country catalog: a static list loaded from `country_list.txt` and exposed to clients.
- News item: a persisted article-like record with server-generated id and creation time on create.
- Author scope: a free-form `authorId` string stored on news items and used for filtering.
- Country scope: a free-form country string that must pass country validation when persisted and can be used for filtering.
- Editorial mutation: full replacement of an existing news item, text-only replacement, and deletion.

The implementation is the authoritative source where it differs from Swagger. There is no authentication or authorization in the source, even though Swagger lists generic `401` and `403` responses. Successful create returns `201` with the generated id body; successful full replacement and text update return `204`, even though Swagger lists `200` and `201` for those PUT operations.

## Available Function Inventory

### Country Catalog

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `retrieve country names` | `GET /countries` | Returns the configured country names accepted by the news country validator. |

### News Publication and Search

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `create news` | `POST /news` | Creates a news item with generated id and server-generated creation time. |
| `list all news` | `GET /news` | Returns all stored news items when no filters are supplied. |
| `list news by country` | `GET /news?country={country}` | Returns stored news items whose country field exactly matches the supplied query value. |
| `list news by author` | `GET /news?authorId={authorId}` | Returns stored news items whose author id exactly matches the supplied query value. |
| `list news by country and author` | `GET /news?country={country}&authorId={authorId}` | Returns stored news items matching both country and author id. |
| `retrieve news by id` | `GET /news/{id}` | Retrieves one stored news item by generated numeric id. |

### News Editorial Mutation

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `replace existing news` | `PUT /news/{id}` | Fully replaces mutable fields of an existing news item while preserving its id. |
| `update news text` | `PUT /news/{id}/text` | Replaces only the text field of an existing news item. |
| `delete news` | `DELETE /news/{id}` | Deletes an existing news item by generated numeric id. |

## Supported Business Behaviors

### Behavior 1: Discover Publishable Countries

Business goal:
Obtain the list of country names that clients can use when publishing or replacing news.

Domain context:
Country is a first-class classification field on news. The country catalog is the only API-visible source for values that should satisfy the persistence validator.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve country names` (`GET /countries`) with no path, query, body, form, or header values to retrieve the static country catalog.

Optional verification workflow:
None.

Existing-state shortcuts:
- No workflow step can be skipped for this behavior because the behavior itself is the catalog read.
- Direct resource-file knowledge of `country_list.txt` can substitute outside the API, but API clients still need `retrieve country names` to discover the currently exposed catalog.

Parameter and value bindings:
- No request value is required.
- A returned country name, for example `Norway`, can be reused later as the `country` body value in `create news` or `replace existing news`.

Business result:
The caller receives the configured country list. No news state is created, changed, or deleted.

Constraints and invariants:
- The source contains no authentication, authorization, or not-found branch for this endpoint.
- The list is loaded from a bundled resource file, not from the news database.
- The country validator accepts countries case-insensitively, but the catalog returns the configured spelling.

Failure and exceptional cases:
- Failing function: `retrieve country names`
- Failure condition: the service cannot load or serve the bundled country resource.
- Why it fails: the controller directly returns `CountryList.countries`; a resource-loading failure would occur outside normal controller branching.
- Violated prerequisite or constraint: the deployed service must include a readable `country_list.txt` resource.

Implementation notes:
Swagger documents generic `401`, `403`, and `404` responses, but the implementation has no authentication, authorization, or explicit not-found handling for this endpoint.

### Behavior 2: Publish a News Item

Business goal:
Create a new news item authored by a specific author and associated with a valid country.

Domain context:
Publication is the core write behavior. The service assigns the durable news id and creation time; clients provide only author, text, and country.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve country names` (`GET /countries`) with no request values to obtain a valid country name such as `Norway`.
2. Use function `create news` (`POST /news`) with JSON body `authorId=author-1`, `text=Initial story text`, `country=Norway`, and with `id`, `newsId`, and `creationTime` omitted to create the news item and capture the generated id from the `201` response body.

Optional verification workflow:
1. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` to inspect the created item.
2. Use function `list all news` (`GET /news`) with `country` omitted or blank and `authorId` omitted or blank to inspect the collection containing the created item.

Existing-state shortcuts:
- Step 1 can be skipped if the caller already has an equivalent country name from the current catalog.
- Direct database setup can create a `NewsEntity`, but that is an alternative to the core publication action and is not equivalent for validating API create behavior.
- The core `create news` step cannot be skipped for API publication.

Parameter and value bindings:
- The `country` value returned by `retrieve country names` is reused as the JSON body `country` in `create news`.
- The generated id returned by `create news` is reused as path `id` by `retrieve news by id` or later mutation functions.
- The submitted `authorId`, `text`, and `country` values become fields of the persisted news item.

Business result:
A new news item exists with a generated numeric id, the submitted author id, text, and country, plus a server-generated `creationTime`. The create response body contains the generated id.

Constraints and invariants:
- `authorId` must be non-blank and at most 32 characters.
- `text` must be non-blank and at most 1024 characters.
- `country` must match the country list case-insensitively.
- Clients cannot supply `id`, `newsId`, or `creationTime` on create.
- No uniqueness rule prevents duplicate text, duplicate author-country combinations, or repeated stories.

Failure and exceptional cases:
- Failing function: `retrieve country names`
- Failure condition: the country resource cannot be loaded.
- Why it fails: the catalog is loaded from the classpath resource.
- Violated prerequisite or constraint: a valid country source must be deployed.

- Failing function: `create news`
- Failure condition: request body includes `id` or `newsId`.
- Why it fails: the implementation rejects client-supplied ids because ids are generated by persistence.
- Violated prerequisite or constraint: generated ids must be assigned by the service.

- Failing function: `create news`
- Failure condition: request body includes `creationTime`.
- Why it fails: the implementation rejects client-supplied creation time on create.
- Violated prerequisite or constraint: creation time must be assigned during persistence.

- Failing function: `create news`
- Failure condition: `authorId`, `text`, or `country` is missing, null, blank where prohibited, too long, or uses an invalid country.
- Why it fails: the controller checks nulls and repository validation raises `ConstraintViolationException` for entity constraints.
- Violated prerequisite or constraint: required publication fields and validation limits must hold.

Implementation notes:
Swagger lists a `200` response with integer schema and `201`, while the source returns `201` with the generated id body for success.

### Behavior 3: Browse the Full News Collection

Business goal:
View the complete set of stored news items without filtering.

Domain context:
This is the broadest reader-facing news feed. It can be used before or after publication and may legitimately return an empty list.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `list all news` (`GET /news`) with query `country` omitted or blank and query `authorId` omitted or blank to retrieve the full collection.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup is required. The read works against an empty or populated database.
- Direct database setup or prior `create news` calls can be used to make the list non-empty, but they are not required for the collection read itself.

Parameter and value bindings:
- `country` and `authorId` must both be omitted or blank to bind this request to the unfiltered repository path.
- Returned `newsId` or deprecated `id` values can be reused later as path `id` for `retrieve news by id`, `replace existing news`, `update news text`, or `delete news`.

Business result:
The caller receives a list of all currently stored news items. No state changes occur.

Constraints and invariants:
- The implementation does not paginate, sort, or limit the collection.
- Swagger documents generic `401`, `403`, and `404`, but the source has no such branches for this listing.

Failure and exceptional cases:
- Failing function: `list all news`
- Failure condition: unexpected persistence or service failure while reading all rows.
- Why it fails: the controller delegates directly to `crud.findAll()` and transforms the result.
- Violated prerequisite or constraint: the repository must be available and able to read persisted news.

Implementation notes:
Blank query values are treated like omitted values because the controller uses Kotlin `isNullOrBlank()`.

### Behavior 4: Build a Country-Specific News Feed

Business goal:
Publish or identify news for a country and retrieve the country-specific feed.

Domain context:
Country is the main classification field. A country feed lets clients show stories related to one country.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve country names` (`GET /countries`) with no request values to obtain a valid country name such as `Norway`.
2. Use function `create news` (`POST /news`) with JSON body `authorId=author-1`, `text=Norway story`, `country=Norway`, and with `id`, `newsId`, and `creationTime` omitted to create a country-scoped news item and capture `{generatedNewsId}`.
3. Use function `list news by country` (`GET /news`) with query `country=Norway` and query `authorId` omitted or blank to retrieve news stored with that country value.

Optional verification workflow:
1. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` to inspect the created item directly.

Existing-state shortcuts:
- Steps 1 and 2 can be skipped if an equivalent news item already exists with a country field exactly equal to the query country.
- Direct database setup can insert a `NewsEntity` with `country=Norway`; the same exact country string must still be used in the query.
- The `list news by country` step cannot be skipped because it is the country-feed behavior.

Parameter and value bindings:
- The country selected from `retrieve country names` is reused as the body `country` in `create news` and as query `country` in `list news by country`.
- The same exact stored country string must be used for filtering. Persistence validation is case-insensitive, but repository filtering is exact.
- The generated id is only needed for optional verification or later item-specific operations.

Business result:
At least one news item exists with the selected country, and the country-filtered feed returns items whose persisted `country` value matches the supplied query value.

Constraints and invariants:
- Create and full replacement validate country values; list queries do not validate the query country.
- Invalid or unmatched query country values return an empty list rather than an error.
- Country matching for filtering is implementation-level exact matching against the stored value.

Failure and exceptional cases:
- Failing function: `retrieve country names`
- Failure condition: the country catalog cannot be read.
- Why it fails: the catalog endpoint depends on the bundled resource.
- Violated prerequisite or constraint: a valid country list must be available for API-level discovery.

- Failing function: `create news`
- Failure condition: the body country is not in the catalog, or author/text validation fails.
- Why it fails: repository validation rejects invalid persisted values.
- Violated prerequisite or constraint: country-scoped feed entries must be persistently valid news items.

- Failing function: `list news by country`
- Failure condition: the query country differs in case or spelling from the stored country value.
- Why it fails: the repository method `findAllByCountry` performs an exact persisted-field lookup.
- Violated prerequisite or constraint: query value must match the stored country string to return the created item.

Implementation notes:
The country validator accepts `norway` as valid, but if stored as `norway`, querying `country=Norway` will not necessarily return it because the filter is not normalized by the controller.

### Behavior 5: Build an Author-Specific News Feed

Business goal:
Publish or identify news by an author and retrieve the author-specific feed.

Domain context:
`authorId` is the service's only author concept. It is not a separate resource, but it scopes news search and attribution.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve country names` (`GET /countries`) with no request values to obtain a valid country name such as `Norway`.
2. Use function `create news` (`POST /news`) with JSON body `authorId=author-1`, `text=Author story`, `country=Norway`, and with `id`, `newsId`, and `creationTime` omitted to create an author-attributed news item and capture `{generatedNewsId}`.
3. Use function `list news by author` (`GET /news`) with query `authorId=author-1` and query `country` omitted or blank to retrieve news stored for that author id.

Optional verification workflow:
1. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` to inspect the created item.

Existing-state shortcuts:
- Steps 1 and 2 can be skipped if an equivalent news item already exists with `authorId=author-1`.
- Direct database setup can insert a valid `NewsEntity` with the required author id.
- The `list news by author` step cannot be skipped for the author-feed behavior.

Parameter and value bindings:
- The `authorId` body value in `create news` is reused as query `authorId` in `list news by author`.
- The country value is needed only to create a valid news item.
- The generated id is optional unless the caller later performs item-specific operations.

Business result:
At least one news item exists with the selected author id, and the author-filtered feed returns items whose persisted `authorId` equals the query value.

Constraints and invariants:
- `authorId` is a plain string; there is no author resource, ownership record, or authentication binding.
- Blank or overlong author ids cannot be persisted.
- Unmatched author ids return an empty list.

Failure and exceptional cases:
- Failing function: `create news`
- Failure condition: `authorId` is blank, longer than 32 characters, omitted, or null.
- Why it fails: the controller checks nulls and entity validation enforces non-blank and size constraints.
- Violated prerequisite or constraint: author-attributed news must have a valid author id.

- Failing function: `list news by author`
- Failure condition: the query author id does not exactly match any stored author id.
- Why it fails: the repository method `findAllByAuthorId` searches by exact stored value.
- Violated prerequisite or constraint: the feed query must use the same author id value that was persisted.

Implementation notes:
The service does not verify that an author exists outside the news item.

### Behavior 6: Build a Country-and-Author News Feed

Business goal:
Retrieve news for a specific author within a specific country.

Domain context:
This combines the two supported business dimensions, enabling a scoped feed such as all Norway-related stories by `author-1`.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve country names` (`GET /countries`) with no request values to obtain a valid country name such as `Norway`.
2. Use function `create news` (`POST /news`) with JSON body `authorId=author-1`, `text=Scoped story`, `country=Norway`, and with `id`, `newsId`, and `creationTime` omitted to create a matching news item and capture `{generatedNewsId}`.
3. Use function `list news by country and author` (`GET /news`) with query `country=Norway` and query `authorId=author-1` to retrieve news matching both dimensions.

Optional verification workflow:
1. Use function `list news by country` (`GET /news`) with query `country=Norway` and `authorId` omitted or blank to compare the broader country feed.
2. Use function `list news by author` (`GET /news`) with query `authorId=author-1` and `country` omitted or blank to compare the broader author feed.
3. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` to inspect the created item.

Existing-state shortcuts:
- Steps 1 and 2 can be skipped if a valid news item already exists with exactly the same persisted `country` and `authorId` values.
- Direct database setup can insert a valid `NewsEntity` with `country=Norway` and `authorId=author-1`.
- The combined-filter listing cannot be skipped for this behavior.

Parameter and value bindings:
- `country=Norway` is used in the create body and then reused as the listing query country.
- `authorId=author-1` is used in the create body and then reused as the listing query author id.
- Both query parameters must be non-blank to select the combined repository path.

Business result:
The caller receives the subset of news items whose persisted country and author id both match the query values.

Constraints and invariants:
- Both filters must be supplied as non-blank query parameters.
- Country query values are not validated.
- Matching is constrained by exact persisted values for both country and author id.

Failure and exceptional cases:
- Failing function: `create news`
- Failure condition: the setup news item cannot be created because country, author id, or text validation fails.
- Why it fails: valid persisted state is required before the feed can contain the intended item.
- Violated prerequisite or constraint: the scoped feed requires valid news state.

- Failing function: `list news by country and author`
- Failure condition: one query parameter is blank or omitted.
- Why it fails: the controller routes blank or omitted values to a different listing function path, so the result is not the combined feed.
- Violated prerequisite or constraint: both country and author id must be supplied for combined scoping.

Implementation notes:
There is no distinction between "no matching rows" and "invalid query country"; both yield an empty list.

### Behavior 7: Retrieve a Specific Published News Item

Business goal:
Look up one news item by its generated id.

Domain context:
Item retrieval is needed after create, list, search, or external id sharing.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve country names` (`GET /countries`) with no request values to obtain a valid country name such as `Norway`.
2. Use function `create news` (`POST /news`) with JSON body `authorId=author-1`, `text=Lookup story`, `country=Norway`, and with `id`, `newsId`, and `creationTime` omitted to create the item and capture `{generatedNewsId}` from the `201` response body.
3. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` to retrieve the created item.

Optional verification workflow:
1. Use function `list all news` (`GET /news`) with `country` omitted or blank and `authorId` omitted or blank to verify the item is present in the collection.

Existing-state shortcuts:
- Steps 1 and 2 can be skipped if an equivalent news item already exists and its numeric id is known.
- Direct database setup can insert a valid `NewsEntity`; the database-assigned id must still be known and used as path `id`.
- The `retrieve news by id` step cannot be skipped for this behavior.

Parameter and value bindings:
- The id returned by `create news` is reused exactly as path `id` in `retrieve news by id`.
- The returned DTO contains both `newsId` and deprecated `id`, both representing the same generated id.

Business result:
The caller receives the current representation of the identified news item. No state changes occur during retrieval.

Constraints and invariants:
- Path `id` must parse as a numeric `Long`.
- The numeric id must exist in the database at read time.
- There is no ownership or visibility check.

Failure and exceptional cases:
- Failing function: `create news`
- Failure condition: setup creation uses invalid author id, text, or country.
- Why it fails: the item cannot exist without satisfying create constraints.
- Violated prerequisite or constraint: retrieval requires an existing valid row.

- Failing function: `retrieve news by id`
- Failure condition: path `id` is non-numeric.
- Why it fails: the implementation parses the string path id as `Long` and returns `404` on parsing failure.
- Violated prerequisite or constraint: item ids are numeric.

- Failing function: `retrieve news by id`
- Failure condition: no row exists with the numeric path id.
- Why it fails: repository lookup returns empty and the controller returns `404`.
- Violated prerequisite or constraint: the requested news item must exist.

Implementation notes:
The source returns `404` for non-numeric ids on GET because the controller accepts the path as `String` and handles parse failure itself.

### Behavior 8: Fully Replace a Published News Item

Business goal:
Replace the editable content, attribution, country, and creation timestamp of an existing news item while keeping the same generated id.

Domain context:
This is the service's broad editorial correction workflow. It can change more than text, including author id, country, and creation time.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve country names` (`GET /countries`) with no request values to obtain valid country names such as `Norway` and `Sweden`.
2. Use function `create news` (`POST /news`) with JSON body `authorId=author-1`, `text=Original story`, `country=Norway`, and with `id`, `newsId`, and `creationTime` omitted to create the item and capture `{generatedNewsId}`.
3. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` to obtain the current `creationTime` if the replacement should preserve it.
4. Use function `replace existing news` (`PUT /news/{id}`) with path `id={generatedNewsId}` and JSON body `newsId={generatedNewsId}`, `authorId=author-2`, `text=Replacement story`, `country=Sweden`, `creationTime={creationTimeFromStep3}` to replace the news item.

Optional verification workflow:
1. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` to verify the replacement values.
2. Use function `list news by country and author` (`GET /news`) with query `country=Sweden` and query `authorId=author-2` to verify the item appears under the new classification.

Existing-state shortcuts:
- Steps 1 and 2 can be skipped if an existing news item id is known and a valid replacement country is already known.
- Step 3 can be skipped if the caller intentionally wants to set a different `creationTime` and already has a non-null date-time value.
- Direct database setup can create the original item, but the replacement path id and body `newsId` must still match an existing row.
- The `replace existing news` step cannot be skipped for this replacement behavior.

Parameter and value bindings:
- The generated id from `create news` is reused as path `id` and JSON body `newsId` in `replace existing news`.
- If preserving publication time, the `creationTime` returned by `retrieve news by id` is reused as body `creationTime` in `replace existing news`.
- Replacement `country=Sweden` must be a valid country value for persistence.
- Replacement values intentionally differ from the original values to represent editorial reclassification or correction.

Business result:
The existing news item still has the same generated id, but its text, author id, country, and creation time are set to the replacement body values. No new item is created.

Constraints and invariants:
- Body `newsId` is preferred over deprecated body `id`; if both are present, `newsId` controls the id check.
- Body id must match path id exactly as a string.
- The target row must already exist; PUT does not create missing resources.
- `text`, `authorId`, `country`, and `creationTime` must be non-null.
- Country, author id, and text must satisfy the same validation constraints as create.

Failure and exceptional cases:
- Failing function: `create news`
- Failure condition: original setup item cannot be created.
- Why it fails: invalid setup values prevent creation of the row to be replaced.
- Violated prerequisite or constraint: replacement requires an existing news item.

- Failing function: `retrieve news by id`
- Failure condition: the captured id no longer exists before step 3.
- Why it fails: repository lookup returns no entity.
- Violated prerequisite or constraint: the row must remain available if its current creation time is reused.

- Failing function: `replace existing news`
- Failure condition: body omits both `newsId` and deprecated `id`, or the selected body id is non-numeric.
- Why it fails: the implementation parses the selected body id and returns `404` on failure.
- Violated prerequisite or constraint: the replacement body must identify the same numeric resource.

- Failing function: `replace existing news`
- Failure condition: body id differs from path id.
- Why it fails: the controller rejects attempts to change the resource id with `409`.
- Violated prerequisite or constraint: generated ids are immutable.

- Failing function: `replace existing news`
- Failure condition: no row exists with the id.
- Why it fails: the implementation checks `existsById` and returns `404`.
- Violated prerequisite or constraint: full replacement is only for existing news.

- Failing function: `replace existing news`
- Failure condition: required replacement fields are null, author/text constraints fail, country is invalid, or `creationTime` is missing.
- Why it fails: the controller performs null checks and persistence validation rejects invalid entity state.
- Violated prerequisite or constraint: a full replacement must provide a complete valid item representation.

Implementation notes:
Successful replacement returns `204 No Content`; Swagger lists `200` and `201`. The implementation allows the caller to change `creationTime`, even though create assigns it server-side.

### Behavior 9: Revise Only the Text of a Published News Item

Business goal:
Correct or revise the body text of a news item without changing author, country, creation time, or id.

Domain context:
This is a narrower editorial workflow than full replacement and avoids resubmitting the whole news representation.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve country names` (`GET /countries`) with no request values to obtain a valid country name such as `Norway`.
2. Use function `create news` (`POST /news`) with JSON body `authorId=author-1`, `text=Original text`, `country=Norway`, and with `id`, `newsId`, and `creationTime` omitted to create the item and capture `{generatedNewsId}`.
3. Use function `update news text` (`PUT /news/{id}/text`) with path `id={generatedNewsId}`, content type `text/plain`, and body `Corrected text` to replace only the text field.

Optional verification workflow:
1. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` to verify `text=Corrected text` and unchanged author id, country, and id.

Existing-state shortcuts:
- Steps 1 and 2 can be skipped if an existing news item id is known.
- Direct database setup can create a valid `NewsEntity`; its generated id must be reused as path `id`.
- The `update news text` step cannot be skipped for the text revision behavior.

Parameter and value bindings:
- The generated id from `create news` is reused as path `id` in `update news text`.
- The text/plain request body becomes the persisted `text` field.
- No body id is supplied to the text update endpoint.

Business result:
The identified news item remains stored with the same id, author id, country, and creation time, but its text is replaced by the supplied plain-text body.

Constraints and invariants:
- Path `id` must bind as a numeric `Long`.
- The news item must exist.
- Replacement text must be non-blank and at most 1024 characters.
- The endpoint cannot change author, country, or creation time.

Failure and exceptional cases:
- Failing function: `create news`
- Failure condition: setup item cannot be created because create validation fails.
- Why it fails: text revision requires an existing news item.
- Violated prerequisite or constraint: a valid existing item must be available.

- Failing function: `update news text`
- Failure condition: path id is non-numeric.
- Why it fails: the path variable is typed as `Long`; binding rejects non-numeric values before the method can update.
- Violated prerequisite or constraint: news ids are numeric.

- Failing function: `update news text`
- Failure condition: no row exists with the path id.
- Why it fails: the controller checks `existsById` and returns `404`.
- Violated prerequisite or constraint: text can only be updated for an existing news item.

- Failing function: `update news text`
- Failure condition: replacement text is blank or longer than 1024 characters.
- Why it fails: repository update violates the entity text constraints and the endpoint returns `400`.
- Violated prerequisite or constraint: persisted news text must be non-blank and within the size limit.

Implementation notes:
Successful text update returns `204 No Content`; Swagger lists `200` and `201`.

### Behavior 10: Remove a Published News Item

Business goal:
Delete an existing news item from the service.

Domain context:
Deletion is the lifecycle endpoint for removing obsolete, erroneous, or unwanted news.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve country names` (`GET /countries`) with no request values to obtain a valid country name such as `Norway`.
2. Use function `create news` (`POST /news`) with JSON body `authorId=author-1`, `text=Story to delete`, `country=Norway`, and with `id`, `newsId`, and `creationTime` omitted to create the item and capture `{generatedNewsId}`.
3. Use function `delete news` (`DELETE /news/{id}`) with path `id={generatedNewsId}` to delete the item.

Optional verification workflow:
1. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` to verify the item is no longer retrievable and should return `404`.
2. Use function `list all news` (`GET /news`) with `country` omitted or blank and `authorId` omitted or blank to inspect the remaining collection.

Existing-state shortcuts:
- Steps 1 and 2 can be skipped if an existing news item id is known.
- Direct database setup can create the target `NewsEntity`; its id must be reused as path `id`.
- The `delete news` step cannot be skipped for deletion.

Parameter and value bindings:
- The generated id from `create news` is reused as path `id` in `delete news`.
- The same id can be reused after deletion only for verification, where retrieval or a second delete should fail.

Business result:
The news item identified by the generated id no longer exists in the news repository.

Constraints and invariants:
- Path `id` must parse as `Long`.
- The row must exist at deletion time.
- Deletion has no child resources to cascade through in this service.
- A second delete of the same id returns `404`, so deletion is not idempotent at the response level.

Failure and exceptional cases:
- Failing function: `create news`
- Failure condition: setup item cannot be created.
- Why it fails: deletion requires a target row.
- Violated prerequisite or constraint: a valid existing item must be available.

- Failing function: `delete news`
- Failure condition: path id is non-numeric.
- Why it fails: the implementation parses the path id as `Long` and returns `400` on parsing failure.
- Violated prerequisite or constraint: news ids are numeric.

- Failing function: `delete news`
- Failure condition: the row no longer exists.
- Why it fails: the controller checks `existsById` before deleting and returns `404` when absent.
- Violated prerequisite or constraint: delete requires an existing row at request time.

Implementation notes:
Swagger documents `204` and a generic `200` for delete, but the source returns `204` on success and also returns `404` for an absent id, which Swagger does not document.

### Behavior 11: Publish and Confirm Multi-Feed Visibility

Business goal:
Publish a news item and confirm that it is visible through the global, country, author, and combined feeds.

Domain context:
This is the complete publication-to-discovery workflow a client would use to ensure a newly published item can be found through every supported read path.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve country names` (`GET /countries`) with no request values to obtain `Norway`.
2. Use function `create news` (`POST /news`) with JSON body `authorId=author-1`, `text=Discoverable story`, `country=Norway`, and with `id`, `newsId`, and `creationTime` omitted to publish the item and capture `{generatedNewsId}`.

Optional verification workflow:
1. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` to verify the specific item.
2. Use function `list all news` (`GET /news`) with `country` omitted or blank and `authorId` omitted or blank to verify global visibility.
3. Use function `list news by country` (`GET /news`) with query `country=Norway` and `authorId` omitted or blank to verify country-feed visibility.
4. Use function `list news by author` (`GET /news`) with query `authorId=author-1` and `country` omitted or blank to verify author-feed visibility.
5. Use function `list news by country and author` (`GET /news`) with query `country=Norway` and query `authorId=author-1` to verify combined-feed visibility.

Existing-state shortcuts:
- Step 1 can be skipped if a current valid country value is already known.
- Direct database setup can create equivalent state for verification exercises, but it does not exercise the API publication action.
- The create step is the core state-changing action for publication and cannot be skipped for this behavior.

Parameter and value bindings:
- `country=Norway` is reused from country discovery into create and both country-scoped list queries.
- `authorId=author-1` is reused from create into author-scoped list queries.
- `{generatedNewsId}` from create is reused by the item retrieval verification.

Business result:
A news item exists and is discoverable through every supported read surface that matches its id, country, and author id.

Constraints and invariants:
- Visibility depends on exact query matching for country and author id.
- Optional reads do not mutate state.
- No indexing delay or asynchronous publication state exists; reads observe persisted repository state directly.

Failure and exceptional cases:
- Failing function: `retrieve country names`
- Failure condition: the country catalog cannot be served.
- Why it fails: valid country selection depends on the bundled resource.
- Violated prerequisite or constraint: publication needs a country value that passes validation.

- Failing function: `create news`
- Failure condition: body values fail create validation.
- Why it fails: invalid news state cannot be persisted.
- Violated prerequisite or constraint: feed visibility requires a successfully created row.

- Failing function: `list news by country`
- Failure condition: verification query uses a different country spelling or case than the persisted value.
- Why it fails: filtering uses exact repository lookup.
- Violated prerequisite or constraint: verification must reuse the stored country value exactly.

- Failing function: `list news by author`
- Failure condition: verification query uses a different author id than the persisted value.
- Why it fails: filtering uses exact repository lookup.
- Violated prerequisite or constraint: verification must reuse the stored author id exactly.

Implementation notes:
The workflow is synchronous. There is no separate publishing status, activation state, or cache invalidation step.

### Behavior 12: Reclassify an Existing News Item and Move It Between Feeds

Business goal:
Move an existing news item from one country/author classification to another using full replacement, then inspect the old and new feeds.

Domain context:
Because country and author are mutable only through full replacement, reclassification is a composite editorial workflow.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve country names` (`GET /countries`) with no request values to obtain `Norway` and `Sweden`.
2. Use function `create news` (`POST /news`) with JSON body `authorId=author-1`, `text=Original classification`, `country=Norway`, and with `id`, `newsId`, and `creationTime` omitted to create the original item and capture `{generatedNewsId}`.
3. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` to capture `{originalCreationTime}`.
4. Use function `replace existing news` (`PUT /news/{id}`) with path `id={generatedNewsId}` and JSON body `newsId={generatedNewsId}`, `authorId=author-2`, `text=Reclassified story`, `country=Sweden`, `creationTime={originalCreationTime}` to move the item to the new classification while preserving creation time.

Optional verification workflow:
1. Use function `list news by country and author` (`GET /news`) with query `country=Norway` and query `authorId=author-1` to inspect the old classification, which should no longer include the updated item.
2. Use function `list news by country and author` (`GET /news`) with query `country=Sweden` and query `authorId=author-2` to inspect the new classification, which should include the updated item.
3. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` to verify the same id now has new classification values.

Existing-state shortcuts:
- Steps 1 and 2 can be skipped if an existing item id and valid old/new country values are already known.
- Step 3 can be skipped if the business intends to overwrite `creationTime` rather than preserve it.
- Direct database setup can create the original item, but replacement still requires matching path id and body id.
- The `replace existing news` step cannot be skipped for reclassification.

Parameter and value bindings:
- `{generatedNewsId}` is reused across retrieve, path `id`, and body `newsId`.
- `{originalCreationTime}` from retrieval is reused in the replacement body to preserve the original timestamp.
- The country and author values intentionally differ between create and replacement; this difference represents movement from the old feed to the new feed.

Business result:
One existing news item remains with the same id but now belongs to the new author/country feed. The old classification no longer matches that item.

Constraints and invariants:
- Reclassification is not partial; all required replacement fields must be supplied.
- The service does not preserve the original creation time automatically; clients must read and resubmit it if they want it preserved.
- Existing feeds are not separately maintained; listing results are derived from current persisted fields.

Failure and exceptional cases:
- Failing function: `create news`
- Failure condition: original item setup fails validation.
- Why it fails: there is no item to reclassify.
- Violated prerequisite or constraint: reclassification requires an existing valid news item.

- Failing function: `retrieve news by id`
- Failure condition: the item is deleted or the id is wrong before timestamp capture.
- Why it fails: the repository returns no entity.
- Violated prerequisite or constraint: preserving creation time requires reading the current item.

- Failing function: `replace existing news`
- Failure condition: replacement country is invalid, id mismatch occurs, required fields are missing, or the item no longer exists.
- Why it fails: the controller enforces identity consistency and required fields, while persistence validation enforces valid stored values.
- Violated prerequisite or constraint: reclassification must target an existing item and produce a complete valid representation.

Implementation notes:
The service has no audit trail, so the previous classification is not preserved after replacement except in external logs or client history.

### Behavior 13: Editorial Cleanup After Publication

Business goal:
Publish a news item, optionally correct its text, and then remove it from the service.

Domain context:
This is a full content lifecycle supported by the available API: create, revise, and delete.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve country names` (`GET /countries`) with no request values to obtain a valid country name such as `Norway`.
2. Use function `create news` (`POST /news`) with JSON body `authorId=author-1`, `text=Temporary story`, `country=Norway`, and with `id`, `newsId`, and `creationTime` omitted to create the item and capture `{generatedNewsId}`.
3. Use function `update news text` (`PUT /news/{id}/text`) with path `id={generatedNewsId}`, content type `text/plain`, and body `Corrected temporary story` to revise the text.
4. Use function `delete news` (`DELETE /news/{id}`) with path `id={generatedNewsId}` to remove the item.

Optional verification workflow:
1. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` after step 3 to inspect the corrected text before deletion.
2. Use function `retrieve news by id` (`GET /news/{id}`) with path `id={generatedNewsId}` after step 4 to verify deletion by receiving `404`.
3. Use function `list all news` (`GET /news`) with `country` omitted or blank and `authorId` omitted or blank to inspect the remaining collection.

Existing-state shortcuts:
- Step 1 can be skipped if a valid country is known.
- Steps 1 and 2 can be skipped if the workflow starts from an existing item id that should be revised and deleted.
- Step 3 can be skipped only if no text correction is needed; then the behavior becomes direct deletion rather than cleanup after correction.
- Direct database setup can establish the target row, but the API delete still requires the correct generated id.

Parameter and value bindings:
- `{generatedNewsId}` from create is reused for text update, deletion, and optional retrieval.
- The text update body intentionally differs from the create text; the later delete consumes the same item id after revision.

Business result:
The news item is first persisted, then its text is updated, and finally the item no longer exists. No replacement item is created.

Constraints and invariants:
- Each mutation requires the item to exist at the time of the request.
- The delete step removes the row regardless of prior text values.
- There is no soft-delete status; deleted items are not retrievable through the API.

Failure and exceptional cases:
- Failing function: `create news`
- Failure condition: initial publication fails validation.
- Why it fails: cleanup cannot proceed without a target item.
- Violated prerequisite or constraint: lifecycle cleanup requires a valid created item.

- Failing function: `update news text`
- Failure condition: replacement text is invalid or the item no longer exists.
- Why it fails: the text update endpoint validates existence and entity text constraints.
- Violated prerequisite or constraint: text correction requires an existing item and valid text.

- Failing function: `delete news`
- Failure condition: the id is non-numeric or the item has already been deleted.
- Why it fails: delete parses the id and checks existence before removal.
- Violated prerequisite or constraint: cleanup deletion requires the target row to still exist.

Implementation notes:
There is no transaction across the multi-step lifecycle. If text update succeeds and delete later fails, the corrected item remains stored.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Authenticated Author Ownership and Protected Editorial Actions

Priority:
Critical domain gap

Expected business goal:
Only the owning author, editor, or authorized principal should be able to update or delete a news item.

Why it is unsupported:
The service has no authentication, authorization, session, token, role, or ownership-checking function. `authorId` is only a string field in the news body and query parameters.

Existing functions considered:
- `create news`: stores `authorId`, but does not authenticate the caller or bind the value to an account.
- `replace existing news`: allows changing `authorId` when the path id and body id match.
- `update news text`: updates text by id without checking author ownership.
- `delete news`: deletes by id without checking author ownership.
- `retrieve news by id`: exposes any existing item by id without authorization.

Missing capability:
An authentication model, authorization checks on mutation and read endpoints where needed, and a persistent ownership link that cannot be freely rewritten by any caller.

Proof that function composition is insufficient:
Chaining existing functions can create an `authorId` string and later query it, but no function can prove caller identity, create an authenticated session, issue a token, or enforce that the caller owns the target id. Because `replace existing news` can change `authorId`, even a manually established author value is not a durable ownership control.

Evidence from existing functions/source:
- The controllers do not read authorization headers or security context.
- Swagger lists generic `401` and `403`, but implementation source has no branches that produce those outcomes.

Business impact:
Any caller who knows or guesses a news id can modify or delete content, weakening editorial integrity and making author attribution untrustworthy.

### Missing Behavior 2: Canonical Country Normalization and Case-Insensitive Country Search

Priority:
Critical domain gap

Expected business goal:
A country accepted by validation should be searchable using the canonical country spelling returned by the country catalog, regardless of input case.

Why it is unsupported:
Country validation is case-insensitive, but persisted country values are stored exactly as submitted, and filtering uses exact repository matching.

Existing functions considered:
- `retrieve country names`: returns canonical configured names, but does not normalize existing news rows.
- `create news`: accepts any case-insensitive country match and stores the submitted string.
- `replace existing news`: accepts any case-insensitive country match and stores the submitted string.
- `list news by country`: does not validate or normalize the query country.
- `list news by country and author`: also uses the supplied country query directly.

Missing capability:
Canonicalization on write, case-insensitive country queries, or a generated canonical country key stored separately from display text.

Proof that function composition is insufficient:
If a client creates a row with `country=norway`, the row can pass validation. Later querying `country=Norway` through `list news by country` cannot force a match because the repository searches exact stored values. Delete-and-recreate can repair one item, but it changes the generated id and creation time unless those values are manually handled through replacement; it is not a general query-time consistency behavior.

Evidence from existing functions/source:
- `CountryValidator` delegates to `CountryList.isValidCountry`, which uses `equals(..., ignoreCase = true)`.
- `NewsRepository.findAllByCountry(country)` and `findAllByCountryAndAuthorId(country, authorId)` use the query value directly.

Business impact:
Country feeds can silently miss valid news items, making country-based discovery unreliable.

### Missing Behavior 3: Stable Creation-Time Integrity

Priority:
Important robustness gap

Expected business goal:
Creation time should represent when the item was first published and should not be arbitrarily overwritten during editorial replacement.

Why it is unsupported:
Create assigns `creationTime` server-side, but full replacement requires a body `creationTime` and writes it back to the entity.

Existing functions considered:
- `create news`: correctly prevents client-supplied `creationTime`.
- `retrieve news by id`: can read the current `creationTime`.
- `replace existing news`: requires non-null `creationTime` and persists the supplied value.
- `update news text`: avoids touching `creationTime`, but only supports text changes.

Missing capability:
Server-side preservation of original creation time during full replacement, a separate update timestamp, or validation that replacement `creationTime` equals the current persisted creation time.

Proof that function composition is insufficient:
A client can read the original timestamp and resubmit it, but the service does not enforce that this happens. No existing function can prevent another client from submitting a different timestamp in `replace existing news`. Text-only update preserves creation time but cannot change country or author.

Evidence from existing functions/source:
- `create news` rejects `creationTime`.
- `NewsRepositoryImpl.update` sets `news.creationTime = creationTime`.

Business impact:
Publication chronology can be falsified or accidentally corrupted during normal full replacement.

### Missing Behavior 4: Partial Updates for Author, Country, or Metadata Without Full Replacement

Priority:
Important robustness gap

Expected business goal:
Editors should be able to correct the author id or country classification without resubmitting text and creation time.

Why it is unsupported:
Only text has a dedicated partial update endpoint. Author, country, and creation time can only be changed through full replacement.

Existing functions considered:
- `update news text`: only changes text.
- `replace existing news`: can change author and country, but requires complete `text`, `authorId`, `country`, and `creationTime`.
- `retrieve news by id`: can be used to read current values before replacement, but cannot update state.

Missing capability:
PATCH semantics or dedicated endpoints such as author update and country update that preserve omitted fields server-side.

Proof that function composition is insufficient:
Read-then-replace can emulate a partial update only if the client accurately copies all fields. It cannot guarantee that omitted fields are preserved under concurrent changes, and it unnecessarily exposes `creationTime` to client rewriting.

Evidence from existing functions/source:
- `replace existing news` returns `400` when any of `text`, `authorId`, `country`, or `creationTime` is null.
- Only `PUT /news/{id}/text` exists as a partial mutation endpoint.

Business impact:
Simple classification corrections are more error-prone than necessary and can accidentally overwrite unrelated fields.

### Missing Behavior 5: Pagination, Sorting, and Bounded Feed Retrieval

Priority:
Important robustness gap

Expected business goal:
Clients should retrieve news feeds in pages ordered by a meaningful field, typically creation time.

Why it is unsupported:
The listing functions accept only `country` and `authorId` filters. There are no limit, offset, cursor, page, size, sort, or order query parameters.

Existing functions considered:
- `list all news`: returns all stored rows.
- `list news by country`: returns all rows for a country.
- `list news by author`: returns all rows for an author.
- `list news by country and author`: returns all rows for both filters.

Missing capability:
Pagination and sorting query parameters, cursor response fields, or repository methods that bound and order results.

Proof that function composition is insufficient:
Repeated list calls return the same unbounded collection and provide no cursor or stable order contract. Client-side slicing after fetching everything is not equivalent because it does not reduce server load, response size, or consistency ambiguity.

Evidence from existing functions/source:
- The controller only reads `country` and `authorId`.
- Repository methods return `Iterable<NewsEntity>` without ordering parameters.

Business impact:
Feeds can become inefficient, unstable, and hard to consume as the dataset grows.

### Missing Behavior 6: Text Search or News Discovery by Content

Priority:
API ergonomics gap

Expected business goal:
Clients should find news items by words or phrases in the news text.

Why it is unsupported:
No endpoint accepts text search, keyword, title, or full-text query parameters.

Existing functions considered:
- `list all news`: can return all items, but leaves all search work to the client.
- `list news by country`: narrows by country only.
- `list news by author`: narrows by author only.
- `list news by country and author`: narrows by both supported metadata fields only.

Missing capability:
A search query parameter or dedicated search endpoint with server-side text matching.

Proof that function composition is insufficient:
Combining existing filters can only match metadata fields. Fetching all news and filtering client-side is not equivalent for large datasets, access control, pagination, or server-side relevance.

Evidence from existing functions/source:
- `NewsRepository` defines finders only for country and author id.
- `NewsRestApi.get` branches only on `country` and `authorId`.

Business impact:
Users cannot discover relevant stories by content through the API.

### Missing Behavior 7: Duplicate Detection and Idempotent Publication

Priority:
API ergonomics gap

Expected business goal:
Avoid accidental duplicate news creation when clients retry a publish request or submit the same story twice.

Why it is unsupported:
Create always persists a new row when validation passes. There is no idempotency key, uniqueness rule, duplicate check, or client-supplied stable external id.

Existing functions considered:
- `create news`: creates a new generated id for each valid request.
- `list news by author`: can find existing items by author but cannot prove duplicate text or prevent creation.
- `list news by country and author`: can narrow candidates but cannot enforce uniqueness.
- `delete news`: can remove duplicates after the fact if ids are known.

Missing capability:
Idempotency-key handling, uniqueness constraints, duplicate-detection endpoint, or conflict response for duplicate content.

Proof that function composition is insufficient:
A client can search after or before creation, but there is no atomic check-and-create behavior. Concurrent retries can still create duplicates, and cleanup by delete changes state only after duplicate rows have already existed.

Evidence from existing functions/source:
- `createNews` delegates to `crud.createNews` without checking existing rows.
- `NewsEntity` has no uniqueness annotations.

Business impact:
Network retries or user mistakes can create duplicate stories, producing noisy feeds and requiring manual cleanup.

### Missing Behavior 8: Soft Delete, Restore, and Deletion Audit

Priority:
API ergonomics gap

Expected business goal:
Deleted news should be recoverable or auditable when removal was accidental or requires traceability.

Why it is unsupported:
Deletion physically removes the row through repository delete. There is no status field, deleted flag, restore endpoint, tombstone, audit log, or deletion reason.

Existing functions considered:
- `delete news`: removes the row by id.
- `retrieve news by id`: returns `404` after deletion and cannot distinguish deleted from never-existing.
- `list all news`: excludes deleted rows because they no longer exist.
- `create news`: can create a replacement item, but it receives a new generated id and creation time.

Missing capability:
Soft-delete state, restore endpoint, deletion metadata, and a way to query deleted items or tombstones.

Proof that function composition is insufficient:
After `delete news`, existing functions cannot recover the old id, original creation time, or previous values unless an external client saved them. Recreating the item is not equivalent because it creates a new identity and publication timestamp.

Evidence from existing functions/source:
- `delete news` calls `crud.deleteById(id)`.
- `retrieve news by id` returns `404` when repository lookup is empty.

Business impact:
Accidental deletion is irreversible through the API, and consumers cannot distinguish removed content from an id that never existed.

## Cross-Behavior Observations

- The service has a simple news lifecycle: create, read, list, full replace, text update, and delete.
- Country validation is applied on persisted entity writes, but country query values are not validated.
- Country validation is case-insensitive, while country filtering is exact against stored values.
- `authorId` is an attribution and filter string, not an authenticated owner.
- Full replacement can mutate `creationTime`, creating a mismatch with the create rule that creation time is server-generated.
- Successful PUT operations return `204`, not the `200` or `201` documented in Swagger.
- Swagger contains generic `401` and `403` responses, but the source contains no security implementation.
- Delete is not response-idempotent: deleting an existing item returns `204`, while deleting the same id again returns `404`.
- There is no pagination, sorting, text search, duplicate prevention, soft delete, or audit trail.
- The service is synchronously consistent from the controller perspective; listing reads current repository state and there is no separate activation or publishing status.

## Coverage Summary

Supported domain areas:
- Static country catalog discovery.
- News publication with generated ids and server-generated creation time.
- News retrieval by id.
- Global, country, author, and combined country-author feeds.
- Full editorial replacement of an existing item.
- Text-only editorial correction.
- Hard deletion of existing news.

Partially supported domain areas:
- Country-based classification is supported, but normalization and query validation are incomplete.
- Author-based attribution is supported, but author identity and ownership are not modeled.
- Editorial correction is supported, but only text has a partial update endpoint; other corrections require full replacement.
- Publication lifecycle is supported, but without idempotency, duplicate detection, soft delete, or audit.

Unsupported domain areas:
- Authentication, authorization, and protected ownership workflows.
- Canonical country search consistency.
- Stable creation-time integrity under full replacement.
- Paginated, sorted, or cursor-based feed consumption.
- Content search.
- Duplicate-safe publication.
- Restore, tombstone, or deletion audit behavior.
