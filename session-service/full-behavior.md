### Function 1: create new session

Function name:
create new session

Core endpoint(s):
- `POST /api/sessions/{source}/{type}`

Preconditions:
- No prior session state is required. The request can create the Mongo collection named by `{type}` if it does not already exist.

Successful execution:
- Result:
  Creates a session document for `{source}` and `{type}`, parses the request body into stored `data`, computes a checksum from the parsed data, persists the document, and returns the generated session `{id}`.
- Invocation:
  Step 1: `POST /api/sessions/{source}/{type}` with `{source}` in the path, `{type}` in the path, and a required `application/json` body containing a valid JSON/BSON document.
- Constraints:
  `{type}` must be one of `main_session`, `virtual_study`, `group`, `comparison_session`, `settings`, `custom_data`, `genomic_chart`, or `custom_gene_list`. `{source}` must have at least 3 characters because the `Session` model validates it during persistence. The OpenAPI body schema is `string`, but the implementation parses the raw body with `Document.parse` and stores the parsed document as `data`. The implementation computes `checksum` from the parsed data and enforces uniqueness on `{source}`, `{type}`, and `checksum` through a unique Mongo index. OpenAPI lists `201 Created`, but the controller returns the created `Session` through the default successful response, so the implementation result is `200 OK` with an id-only JSON view.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No session state is required.
    - `{type}` is not one of the supported `SessionType` enum values.
  - Failure endpoint:
    `POST /api/sessions/{source}/{type}`
  - Why this fails:
    Spring cannot bind the path variable to the `SessionType` enum, and the controller's `MethodArgumentTypeMismatchException` handler sends `400 Bad Request` with the valid type list.
  - Intentionally violated constraints:
    The `{type}` path value is outside the documented and implemented enum.

- Branch 2:
  - Preconditions:
    - No session state is required.
    - The request body is missing or is not parseable as a JSON/BSON document.
  - Failure endpoint:
    `POST /api/sessions/{source}/{type}`
  - Why this fails:
    `Session.setData` parses string bodies with `Document.parse`. Invalid JSON raises a parse error that the service maps to `SessionInvalidException`, and the controller sends `400 Bad Request`.
  - Intentionally violated constraints:
    The required body does not contain valid parsed session data.

- Branch 3:
  - Preconditions:
    - No session state is required.
    - `{source}` has fewer than 3 characters.
  - Failure endpoint:
    `POST /api/sessions/{source}/{type}`
  - Why this fails:
    The `Session` object is validated during repository save. A short source violates `@Size(min=3)` and is mapped to `400 Bad Request`.
  - Intentionally violated constraints:
    The source-length validation rule is violated.

Endpoint coverage:
- Covers:
  `POST /api/sessions/{source}/{type}`
- Distinct meaning:
  Creates and stores a new session document when no existing document has the same `{source}`, `{type}`, and parsed-data checksum.

### Function 2: reuse existing duplicate session

Function name:
reuse existing duplicate session

Core endpoint(s):
- `POST /api/sessions/{source}/{type}`

Preconditions:
- A session already exists in the Mongo collection named by `{type}` with `source = {source}`, `type = {type}`, parsed `data = {data}`, and a checksum equal to the checksum that will be computed from `{data}`. This can be satisfied by directly inserting a `Session` document with those fields and a unique `{id}`, or by calling `POST /api/sessions/{source}/{type}` once with body `{data}`.
- The `{id}` returned by the duplicate request must identify the existing session. If the API is used to establish the existing session, this `{id}` is obtained from the earlier creation response.

Successful execution:
- Result:
  Returns the existing session `{id}` instead of creating a second document when the same parsed data is submitted again for the same `{source}` and `{type}`.
- Invocation:
  Step 1: `POST /api/sessions/{source}/{type}` with the same `{source}`, the same `{type}`, and a valid JSON body `{data}` that computes to the existing session checksum.
- Constraints:
  The request must use the same uniqueness scope as the existing document: `{source}`, `{type}`, and parsed-data checksum. The implementation catches `DuplicateKeyException` from Mongo persistence and resolves it by looking up the existing session through `{source}`, `{type}`, and `checksum`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A session exists for `{source}`, `{type}`, and body `{data}`. This can be satisfied by directly inserting a `Session` document in the collection named by `{type}` with the matching source, type, parsed data, checksum, and id, or by calling `POST /api/sessions/{source}/{type}` with body `{data}`.
    - The failing request changes at least one uniqueness-scope value, such as using `{otherSource}` or `{otherType}`, while reusing body `{data}`.
  - Failure endpoint:
    `POST /api/sessions/{source}/{type}`
  - Why this fails:
    This does not exercise duplicate reuse because the unique index is scoped to `{source}`, `{type}`, and `checksum`. A different source or type addresses a different uniqueness scope and can create or target a different session.
  - Intentionally violated constraints:
    The duplicate request does not preserve the same source/type scope as the existing session.

Endpoint coverage:
- Covers:
  `POST /api/sessions/{source}/{type}`
- Distinct meaning:
  Exposes the implementation's duplicate-submission semantics for the create endpoint: duplicate data in the same source/type scope returns the existing id.

### Function 3: list sessions by source and type

Function name:
list sessions by source and type

Core endpoint(s):
- `GET /api/sessions/{source}/{type}`

Preconditions:
- No session state is required. Matching sessions can exist or the collection can be empty or absent; the implementation returns a list either way.

Successful execution:
- Result:
  Returns all sessions whose stored `source` and `type` match the path values. The result can be an empty list.
- Invocation:
  Step 1: `GET /api/sessions/{source}/{type}` with `{source}` and `{type}` in the path.
- Constraints:
  `{type}` must be a valid `SessionType` enum value. `{source}` is matched exactly and case-sensitively in Mongo. OpenAPI lists `404 Not Found`, but the implementation does not throw not-found for an empty list.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No session state is required.
    - `{type}` is not one of the supported `SessionType` enum values.
  - Failure endpoint:
    `GET /api/sessions/{source}/{type}`
  - Why this fails:
    Spring cannot bind the path variable to `SessionType`, and the controller returns `400 Bad Request` through its type-mismatch handler.
  - Intentionally violated constraints:
    The `{type}` path value is outside the documented and implemented enum.

Endpoint coverage:
- Covers:
  `GET /api/sessions/{source}/{type}`
- Distinct meaning:
  Lists the sessions in a source/type scope, including the successful empty-list case.

### Function 4: search sessions by field and value

Function name:
search sessions by field and value

Core endpoint(s):
- `GET /api/sessions/{source}/{type}/query`

Preconditions:
- No matching session state is required. Sessions can be prepared by directly inserting `Session` documents in the collection named by `{type}` or by calling `POST /api/sessions/{source}/{type}` with valid JSON bodies, but an empty search result is still successful.

Successful execution:
- Result:
  Searches sessions for `{source}` and `{type}` using a single equality predicate built from query parameters `{field}` and `{value}`.
- Invocation:
  Step 1: `GET /api/sessions/{source}/{type}/query?field={field}&value={value}` with `{source}` and `{type}` in the path and required `field` and `value` query parameters.
- Constraints:
  `{type}` must be a valid `SessionType` enum value. The controller builds a JSON query string in the form `{"{field}":"{value}"}` and the repository adds `source = {source}` as an additional criterion. `{field}` must be usable as a Mongo-style field path, and `{value}` must not make the generated query invalid.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No matching session state is required.
    - `{field}` produces an invalid Mongo query, such as a field containing a null character or using syntax rejected by `BasicQuery`.
  - Failure endpoint:
    `GET /api/sessions/{source}/{type}/query`
  - Why this fails:
    The repository constructs a `BasicQuery` from the generated query string. Invalid query syntax raises `IllegalArgumentException`, `JsonParseException`, `BSONException`, or `UncategorizedMongoDbException`, and the service maps it to `SessionQueryInvalidException`; the controller sends `400 Bad Request`.
  - Intentionally violated constraints:
    `{field}` is not a valid query field for the generated Mongo query.

- Branch 2:
  - Preconditions:
    - No session state is required.
    - `{type}` is not one of the supported `SessionType` enum values.
  - Failure endpoint:
    `GET /api/sessions/{source}/{type}/query`
  - Why this fails:
    Spring cannot bind `{type}` to the enum, so the request is rejected before repository query execution.
  - Intentionally violated constraints:
    The `{type}` path value is outside the documented and implemented enum.

Endpoint coverage:
- Covers:
  `GET /api/sessions/{source}/{type}/query`
- Distinct meaning:
  Performs a server-built field/value equality search using URL query parameters.

### Function 5: search sessions by request-body filter

Function name:
search sessions by body filter

Core endpoint(s):
- `POST /api/sessions/{source}/{type}/query/fetch`

Preconditions:
- No matching session state is required. Sessions can be prepared by directly inserting `Session` documents in the collection named by `{type}` or by calling `POST /api/sessions/{source}/{type}` with valid JSON bodies, but an empty search result is still successful.

Successful execution:
- Result:
  Searches sessions for `{source}` and `{type}` using a Mongo-style filter supplied in the request body.
- Invocation:
  Step 1: `POST /api/sessions/{source}/{type}/query/fetch` with `{source}` and `{type}` in the path and a valid JSON query body.
- Constraints:
  `{type}` must be a valid `SessionType` enum value. The body must be a valid Mongo-style JSON filter accepted by `BasicQuery`. The repository always adds `source = {source}` as an additional criterion. OpenAPI lists `201 Created`, but the implementation returns a result list through the default successful response, so the implementation result is `200 OK`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No matching session state is required.
    - The request body is not a valid Mongo-style query document, contains invalid BSON syntax, or uses a field form rejected by `BasicQuery`.
  - Failure endpoint:
    `POST /api/sessions/{source}/{type}/query/fetch`
  - Why this fails:
    The repository rejects invalid `BasicQuery` input, the service maps the exception to `SessionQueryInvalidException`, and the controller sends `400 Bad Request`.
  - Intentionally violated constraints:
    The body is not a valid Mongo-style filter.

- Branch 2:
  - Preconditions:
    - No session state is required.
    - `{type}` is not one of the supported `SessionType` enum values.
  - Failure endpoint:
    `POST /api/sessions/{source}/{type}/query/fetch`
  - Why this fails:
    Spring cannot bind `{type}` to the enum, so the request is rejected before repository query execution.
  - Intentionally violated constraints:
    The `{type}` path value is outside the documented and implemented enum.

Endpoint coverage:
- Covers:
  `POST /api/sessions/{source}/{type}/query/fetch`
- Distinct meaning:
  Performs body-based session search with an arbitrary Mongo-style filter rather than a single URL-parameter equality predicate.

### Function 6: retrieve session by id

Function name:
retrieve session by id

Core endpoint(s):
- `GET /api/sessions/{source}/{type}/{id}`

Preconditions:
- A session exists in the Mongo collection named by `{type}` with `id = {id}`, `source = {source}`, and `type = {type}`. This can be satisfied by directly inserting a `Session` document with those values, valid parsed `data`, and a checksum, or by calling `POST /api/sessions/{source}/{type}` with a valid JSON body.
- The `{id}` used by `GET /api/sessions/{source}/{type}/{id}` must identify the session described above. If the API is used to establish the session, `{id}` must be obtained from the creation response.

Successful execution:
- Result:
  Returns the full stored session identified by `{id}` in the `{source}` and `{type}` scope.
- Invocation:
  Step 1: `GET /api/sessions/{source}/{type}/{id}` with the same `{source}`, `{type}`, and `{id}` as the existing session.
- Constraints:
  Lookup requires all three values to match: `source`, `type`, and `id`. `{type}` must be a valid `SessionType` enum value.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No session exists with `id = {id}`, `source = {source}`, and `type = {type}`. This can be produced by starting from an empty database, deleting the session beforehand, directly omitting that document, or intentionally not calling `POST /api/sessions/{source}/{type}` to obtain `{id}`.
  - Failure endpoint:
    `GET /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    `findOneBySourceAndTypeAndId` returns no session, so the service throws `SessionNotFoundException` and the controller returns `404 Not Found`.
  - Intentionally violated constraints:
    `{id}` does not identify an existing session in the requested source/type scope.

- Branch 2:
  - Preconditions:
    - A session exists with `id = {id}`, `source = {source}`, and `type = {type}`. This can be satisfied by directly inserting that `Session` document or by calling `POST /api/sessions/{source}/{type}` with a valid JSON body and using the returned `{id}`.
    - The failing request uses `{otherSource}` or `{otherType}` instead of the existing session's source/type scope.
  - Failure endpoint:
    `GET /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    The repository lookup is scoped by `source`, `type`, and `id`; an existing id is not sufficient when the source or type does not match.
  - Intentionally violated constraints:
    The request does not reuse the source/type scope associated with `{id}`.

- Branch 3:
  - Preconditions:
    - No session state is required.
    - `{type}` is not one of the supported `SessionType` enum values.
  - Failure endpoint:
    `GET /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    Spring cannot bind `{type}` to the enum, and the controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The `{type}` path value is outside the documented and implemented enum.

Endpoint coverage:
- Covers:
  `GET /api/sessions/{source}/{type}/{id}`
- Distinct meaning:
  Reads one specific session resource by id within a source/type scope.

### Function 7: update session data

Function name:
update session data

Core endpoint(s):
- `PUT /api/sessions/{source}/{type}/{id}`

Preconditions:
- A session exists in the Mongo collection named by `{type}` with `id = {id}`, `source = {source}`, and `type = {type}`. This can be satisfied by directly inserting a `Session` document with those values, valid parsed `data`, and a checksum, or by calling `POST /api/sessions/{source}/{type}` with a valid JSON body.
- The `{id}` used by `PUT /api/sessions/{source}/{type}/{id}` must identify the session described above. If the API is used to establish the session, `{id}` must be obtained from the creation response.

Successful execution:
- Result:
  Replaces the stored `data` for an existing session and recomputes the session checksum.
- Invocation:
  Step 1: `PUT /api/sessions/{source}/{type}/{id}` with the same `{source}`, `{type}`, and `{id}` as the existing session and a new valid JSON body.
- Constraints:
  The endpoint is not an upsert; a matching session must already exist. The body is documented as a string in OpenAPI, but the implementation parses it as JSON/BSON session data. OpenAPI lists `201 Created`, but the controller method returns `void`; successful implementation result is the default `200 OK` empty response. The unique index on `{source}`, `{type}`, and `checksum` still applies after the update.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No session exists with `id = {id}`, `source = {source}`, and `type = {type}`. This can be produced by starting from an empty database, deleting the session beforehand, directly omitting that document, or intentionally not calling `POST /api/sessions/{source}/{type}` to obtain `{id}`.
  - Failure endpoint:
    `PUT /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    The implementation looks up the session first with `findOneBySourceAndTypeAndId`. If no matching document exists, it throws `SessionNotFoundException` and the controller returns `404 Not Found`.
  - Intentionally violated constraints:
    `{id}` does not identify an existing session in the requested source/type scope.

- Branch 2:
  - Preconditions:
    - A session exists with `id = {id}`, `source = {source}`, and `type = {type}`. This can be satisfied by directly inserting that `Session` document or by calling `POST /api/sessions/{source}/{type}` with a valid JSON body and using the returned `{id}`.
    - The update body is missing or malformed and cannot be parsed as JSON/BSON session data.
  - Failure endpoint:
    `PUT /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    `Session.setData` parses string data with `Document.parse`. Invalid JSON raises a parse error or validation error that the service maps to `SessionInvalidException`, and the controller sends `400 Bad Request`.
  - Intentionally violated constraints:
    The update request does not provide valid parsed session data.

- Branch 3:
  - Preconditions:
    - A first session exists in the collection named by `{type}` with `id = {idA}`, `source = {source}`, `type = {type}`, and body data `{dataA}`. This can be satisfied by directly inserting that `Session` document with its checksum or by calling `POST /api/sessions/{source}/{type}` with body `{dataA}` and saving the returned `{idA}`.
    - A second session exists in the same collection with `id = {idB}`, `source = {source}`, `type = {type}`, and different body data `{dataB}`. This can be satisfied by directly inserting that second `Session` document with a different checksum or by calling `POST /api/sessions/{source}/{type}` with body `{dataB}` and saving the returned `{idB}`.
    - The failing update targets `{idB}` and sends body `{dataA}`, which would make the second session's checksum duplicate the first session's checksum in the same source/type scope.
  - Failure endpoint:
    `PUT /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    Mongo's unique index on `source`, `type`, and `checksum` is violated during `saveSession`. Unlike create, update does not catch `DuplicateKeyException`, so this is an implementation-level exceptional branch not described by a specific OpenAPI response.
  - Intentionally violated constraints:
    The update changes one session to data that already belongs to another session in the same source/type uniqueness scope.

- Branch 4:
  - Preconditions:
    - No session state is required.
    - `{type}` is not one of the supported `SessionType` enum values.
  - Failure endpoint:
    `PUT /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    Spring cannot bind `{type}` to the enum, and the controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The `{type}` path value is outside the documented and implemented enum.

Endpoint coverage:
- Covers:
  `PUT /api/sessions/{source}/{type}/{id}`
- Distinct meaning:
  Replaces data on an existing session; it never creates a missing session.

### Function 8: delete session

Function name:
delete session

Core endpoint(s):
- `DELETE /api/sessions/{source}/{type}/{id}`

Preconditions:
- A session exists in the Mongo collection named by `{type}` with `id = {id}`, `source = {source}`, and `type = {type}`. This can be satisfied by directly inserting a `Session` document with those values, valid parsed `data`, and a checksum, or by calling `POST /api/sessions/{source}/{type}` with a valid JSON body.
- The `{id}` used by `DELETE /api/sessions/{source}/{type}/{id}` must identify the session described above. If the API is used to establish the session, `{id}` must be obtained from the creation response.

Successful execution:
- Result:
  Deletes the existing session identified by `{id}` in the `{source}` and `{type}` scope.
- Invocation:
  Step 1: `DELETE /api/sessions/{source}/{type}/{id}` with the same `{source}`, `{type}`, and `{id}` as the existing session.
- Constraints:
  The delete succeeds only when exactly one matching document is removed. OpenAPI lists `204 No Content`, but the controller method returns `void`; successful implementation result is the default `200 OK` empty response.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No session exists with `id = {id}`, `source = {source}`, and `type = {type}`. This can be produced by starting from an empty database, deleting the session beforehand, directly omitting that document, or intentionally not calling `POST /api/sessions/{source}/{type}` to obtain `{id}`.
  - Failure endpoint:
    `DELETE /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    The repository delete query removes zero documents. Because the service requires a delete count of exactly one, it throws `SessionNotFoundException` and the controller returns `404 Not Found`. OpenAPI does not list `404` for this delete endpoint, but the implementation can return it.
  - Intentionally violated constraints:
    `{id}` does not identify an existing session in the requested source/type scope.

- Branch 2:
  - Preconditions:
    - A session exists with `id = {id}`, `source = {source}`, and `type = {type}`. This can be satisfied by directly inserting that `Session` document or by calling `POST /api/sessions/{source}/{type}` with a valid JSON body and using the returned `{id}`.
    - The failing request uses `{otherSource}` or `{otherType}` instead of the existing session's source/type scope.
  - Failure endpoint:
    `DELETE /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    The delete query is scoped by `source`, `type`, and `id`; it removes zero documents when the source or type does not match the existing session.
  - Intentionally violated constraints:
    The request does not reuse the source/type scope associated with `{id}`.

- Branch 3:
  - Preconditions:
    - A session exists with `id = {id}`, `source = {source}`, and `type = {type}`. This can be satisfied by directly inserting that `Session` document or by calling `POST /api/sessions/{source}/{type}` with a valid JSON body and using the returned `{id}`.
    - That same session has already been removed by a prior successful `DELETE /api/sessions/{source}/{type}/{id}` request or by direct database deletion.
  - Failure endpoint:
    `DELETE /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    A repeated delete removes zero documents, and the service treats any delete count other than one as not-found.
  - Intentionally violated constraints:
    The required existing session state was removed before the failing request.

- Branch 4:
  - Preconditions:
    - No session state is required.
    - `{type}` is not one of the supported `SessionType` enum values.
  - Failure endpoint:
    `DELETE /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    Spring cannot bind `{type}` to the enum, and the controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The `{type}` path value is outside the documented and implemented enum.

Endpoint coverage:
- Covers:
  `DELETE /api/sessions/{source}/{type}/{id}`
- Distinct meaning:
  Removes one existing session resource; repeated deletes are not treated as successful idempotent deletes.

### Function 9: retrieve service information

Function name:
retrieve service information

Core endpoint(s):
- `GET /info`

Preconditions:
- No session state is required.

Successful execution:
- Result:
  Returns the service implementation version string from application package metadata.
- Invocation:
  Step 1: `GET /info` with no path, query, body, form, or header values required by the implementation.
- Constraints:
  The endpoint is outside `/api/sessions/`. When `security.basic.enabled` is true, the security configuration explicitly permits `/info`; when it is false, all endpoints are permitted. OpenAPI lists generic `401`, `403`, and `404` responses, but the controller implementation exposes a simple successful string response and no implementation-specific failure branch is visible.

Failure or exceptional branches:
No implementation-specific failure branch is visible in the inspected source code.

Endpoint coverage:
- Covers:
  `GET /info`
- Distinct meaning:
  Provides service metadata/version information, separate from session storage workflows.
