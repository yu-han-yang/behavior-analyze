### Behavior 1: create new session

Behavior name:
create new session

Successful execution:
- Result:
  This behavior creates a new session for `{source}` and `{type}` and returns the generated session `{id}`.
- Endpoint sequence:
  Step 1: `POST /api/sessions/{source}/{type}` with a valid JSON request body.
- Constraints:
  `{type}` must be one of `main_session`, `virtual_study`, `group`, `comparison_session`, `settings`, `custom_data`, `genomic_chart`, or `custom_gene_list`. `{source}` must have at least 3 characters. The body is documented as a string in Swagger, but the implementation parses the raw body as a JSON/BSON document and stores it as `data`. A checksum is computed from the parsed data. For this creation behavior, no existing session may already have the same `{source}`, `{type}`, and checksum. Swagger lists `201`, but the controller implementation returns the created `Session` using the default `200 OK`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{type}` is not a valid session type.
  - Endpoint group:
    Step 1: `POST /api/sessions/{source}/{type}` with `{type}` set to an unsupported value and a valid JSON body.
  - Failure endpoint:
    `POST /api/sessions/{source}/{type}`
  - Why this fails:
    The path variable cannot be converted to the `SessionType` enum, so the controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    `{type}` is outside the documented enum.

- Branch 2:
  - Unsatisfied condition:
    The request body is missing or is not parseable as a JSON/BSON document.
  - Endpoint group:
    Step 1: `POST /api/sessions/{source}/{type}` with a missing or malformed body.
  - Failure endpoint:
    `POST /api/sessions/{source}/{type}`
  - Why this fails:
    The implementation parses the body before saving the session; unreadable or invalid JSON becomes a bad request.
  - Intentionally violated constraints:
    The body is not valid parsed session data.

- Branch 3:
  - Unsatisfied condition:
    `{source}` has fewer than 3 characters.
  - Endpoint group:
    Step 1: `POST /api/sessions/{source}/{type}` with `{source}` shorter than 3 characters and a valid JSON body.
  - Failure endpoint:
    `POST /api/sessions/{source}/{type}`
  - Why this fails:
    The `Session` model validates `source` with a minimum length of 3 during persistence.
  - Intentionally violated constraints:
    The source-length validation rule is violated.

Endpoint coverage:
- Covers:
  `POST /api/sessions/{source}/{type}`
- Distinct meaning:
  Creates and stores a new session when the `{source}`, `{type}`, and parsed data checksum combination does not already exist.

### Behavior 2: reuse existing duplicate session

Behavior name:
reuse existing duplicate session

Successful execution:
- Result:
  This behavior returns the existing session `{id}` instead of creating a second session when the same session data is posted again for the same `{source}` and `{type}`.
- Endpoint sequence:
  Step 1: `POST /api/sessions/{source}/{type}` with valid JSON body `{data}`.
  Step 2: `POST /api/sessions/{source}/{type}` with the same valid JSON body `{data}`.
- Constraints:
  Step 2 must use the same `{source}`, the same `{type}`, and body data that produces the same parsed-data checksum as Step 1. The `{id}` returned by Step 2 is the existing `{id}` produced by Step 1. This is implementation behavior: duplicate-key persistence errors are caught and resolved by looking up the existing session.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The second call does not use the same `{source}`, `{type}`, and data checksum.
  - Endpoint group:
    Step 1: `POST /api/sessions/{source}/{type}` with valid JSON body `{data}`.
    Step 2: `POST /api/sessions/{otherSource}/{type}` or `POST /api/sessions/{source}/{otherType}` with the same body `{data}`.
  - Failure endpoint:
    `POST /api/sessions/{source}/{type}`
  - Why this fails:
    It does not exercise duplicate reuse. The uniqueness rule is scoped to `{source}`, `{type}`, and checksum, so a different source or type creates or addresses a different uniqueness scope.
  - Intentionally violated constraints:
    The duplicate call changes one of the values required to match the original session.

Endpoint coverage:
- Covers:
  `POST /api/sessions/{source}/{type}`
- Distinct meaning:
  Returns an already-existing session id for duplicate session data rather than creating a new resource.

### Behavior 3: list sessions by source and type

Behavior name:
list sessions by source and type

Successful execution:
- Result:
  This behavior returns all sessions stored for `{source}` in the collection selected by `{type}`. The result can be an empty list.
- Endpoint sequence:
  Step 1: `GET /api/sessions/{source}/{type}`
- Constraints:
  `{type}` must be a valid documented session type. `{source}` is matched exactly and case-sensitively. No prerequisite creation endpoint is required because the implementation returns an empty list when no sessions match. Swagger lists `404`, but the implementation does not throw not-found for an empty list.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{type}` is not a valid session type.
  - Endpoint group:
    Step 1: `GET /api/sessions/{source}/{type}` with `{type}` set to an unsupported value.
  - Failure endpoint:
    `GET /api/sessions/{source}/{type}`
  - Why this fails:
    The controller cannot bind the path variable to the `SessionType` enum and returns `400 Bad Request`.
  - Intentionally violated constraints:
    `{type}` is outside the documented enum.

Endpoint coverage:
- Covers:
  `GET /api/sessions/{source}/{type}`
- Distinct meaning:
  Lists sessions in a source/type scope, including the successful empty-list case.

### Behavior 4: search sessions by query parameters

Behavior name:
search sessions by field and value

Successful execution:
- Result:
  This behavior searches sessions for `{source}` and `{type}` using a single equality predicate built from the query parameters `{field}` and `{value}`. The result can be empty.
- Endpoint sequence:
  Step 1: `GET /api/sessions/{source}/{type}/query?field={field}&value={value}`
- Constraints:
  `{type}` selects the session collection. `{source}` is added as a required criterion by the implementation. `{field}` and `{value}` are converted into a JSON equality query, so `{field}` must be a valid Mongo-style field path and `{value}` must not make the generated query invalid. No matching session has to exist for the request to succeed.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{field}` produces an invalid Mongo query, such as a field containing a null character or starting with `$`.
  - Endpoint group:
    Step 1: `GET /api/sessions/{source}/{type}/query?field={invalidField}&value={value}`
  - Failure endpoint:
    `GET /api/sessions/{source}/{type}/query`
  - Why this fails:
    The repository creates a Mongo `BasicQuery`; invalid query syntax is converted to `400 Bad Request`.
  - Intentionally violated constraints:
    `{field}` is not a valid query field.

- Branch 2:
  - Unsatisfied condition:
    `{type}` is not a valid session type.
  - Endpoint group:
    Step 1: `GET /api/sessions/{source}/{type}/query?field={field}&value={value}` with an unsupported `{type}`.
  - Failure endpoint:
    `GET /api/sessions/{source}/{type}/query`
  - Why this fails:
    The controller cannot bind `{type}` to the enum.
  - Intentionally violated constraints:
    `{type}` is outside the documented enum.

Endpoint coverage:
- Covers:
  `GET /api/sessions/{source}/{type}/query`
- Distinct meaning:
  Performs a simple field/value search where the server constructs the query from URL parameters.

### Behavior 5: search sessions by request-body filter

Behavior name:
search sessions by body filter

Successful execution:
- Result:
  This behavior searches sessions for `{source}` and `{type}` using a Mongo-style filter supplied in the request body. The result can be empty.
- Endpoint sequence:
  Step 1: `POST /api/sessions/{source}/{type}/query/fetch` with a valid JSON query body.
- Constraints:
  The body must be a valid Mongo-style JSON filter. `{source}` is always added as a required criterion by the implementation. `{type}` selects the collection. Swagger lists `201`, but the implementation returns a result list with `200 OK`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The body is not a valid query document.
  - Endpoint group:
    Step 1: `POST /api/sessions/{source}/{type}/query/fetch` with an invalid JSON query body, a field containing a null character, or a field starting with `$`.
  - Failure endpoint:
    `POST /api/sessions/{source}/{type}/query/fetch`
  - Why this fails:
    The repository rejects invalid `BasicQuery` input and the service maps it to `400 Bad Request`.
  - Intentionally violated constraints:
    The body is not a valid Mongo-style filter.

- Branch 2:
  - Unsatisfied condition:
    `{type}` is not a valid session type.
  - Endpoint group:
    Step 1: `POST /api/sessions/{source}/{type}/query/fetch` with an unsupported `{type}` and a valid query body.
  - Failure endpoint:
    `POST /api/sessions/{source}/{type}/query/fetch`
  - Why this fails:
    The controller cannot bind `{type}` to the enum.
  - Intentionally violated constraints:
    `{type}` is outside the documented enum.

Endpoint coverage:
- Covers:
  `POST /api/sessions/{source}/{type}/query/fetch`
- Distinct meaning:
  Performs an advanced/body-based session search rather than a single URL-parameter equality search.

### Behavior 6: retrieve session by id

Behavior name:
retrieve session by id

Successful execution:
- Result:
  This behavior retrieves the full stored session identified by `{id}` for the same `{source}` and `{type}` used when it was created.
- Endpoint sequence:
  Step 1: `POST /api/sessions/{source}/{type}` with a valid JSON body; the response produces `{id}`.
  Step 2: `GET /api/sessions/{source}/{type}/{id}` using the `{id}` from Step 1.
- Constraints:
  Step 2 must reuse the exact `{source}`, `{type}`, and `{id}` from the created session. The lookup requires all three values to match.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No session exists for the supplied `{source}`, `{type}`, and `{id}`.
  - Endpoint group:
    Step 1: `GET /api/sessions/{source}/{type}/{id}` while intentionally omitting the prerequisite `POST /api/sessions/{source}/{type}` that would create `{id}`.
  - Failure endpoint:
    `GET /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    The repository lookup returns no session and the service throws not-found.
  - Intentionally violated constraints:
    `{id}` was not obtained from a successful creation response.

- Branch 2:
  - Unsatisfied condition:
    `{id}` exists, but the request uses a different `{source}` or `{type}`.
  - Endpoint group:
    Step 1: `POST /api/sessions/{source}/{type}` with a valid JSON body; the response produces `{id}`.
    Step 2: `GET /api/sessions/{otherSource}/{type}/{id}` or `GET /api/sessions/{source}/{otherType}/{id}`.
  - Failure endpoint:
    `GET /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    The lookup is scoped by source, type, and id, so the existing id is not enough by itself.
  - Intentionally violated constraints:
    Step 2 does not reuse the same source/type scope as Step 1.

Endpoint coverage:
- Covers:
  `GET /api/sessions/{source}/{type}/{id}`
- Distinct meaning:
  Reads one specific existing session resource.

### Behavior 7: update session data

Behavior name:
update session data

Successful execution:
- Result:
  This behavior replaces the stored `data` for an existing session.
- Endpoint sequence:
  Step 1: `POST /api/sessions/{source}/{type}` with a valid JSON body; the response produces `{id}`.
  Step 2: `PUT /api/sessions/{source}/{type}/{id}` using the `{id}` from Step 1 and a new valid JSON body.
- Constraints:
  Step 2 must reuse the exact `{source}`, `{type}`, and `{id}` from Step 1. The body is documented as a string, but the implementation parses it as JSON/BSON session data. The implementation does not create missing sessions on `PUT`; Swagger lists `201`, but the source logic only updates existing sessions and otherwise returns not-found.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The target session does not exist.
  - Endpoint group:
    Step 1: `PUT /api/sessions/{source}/{type}/{id}` with a valid JSON body while intentionally omitting the prerequisite `POST /api/sessions/{source}/{type}`.
  - Failure endpoint:
    `PUT /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    The implementation looks up the session first and throws not-found if no matching source/type/id exists.
  - Intentionally violated constraints:
    `{id}` was not produced by a prior successful create call in the same source/type scope.

- Branch 2:
  - Unsatisfied condition:
    The target exists, but the update body is missing or invalid.
  - Endpoint group:
    Step 1: `POST /api/sessions/{source}/{type}` with a valid JSON body; the response produces `{id}`.
    Step 2: `PUT /api/sessions/{source}/{type}/{id}` with a missing or malformed body.
  - Failure endpoint:
    `PUT /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    The new data cannot be parsed or read, so the update is rejected with `400 Bad Request`.
  - Intentionally violated constraints:
    Step 2 does not provide valid parsed session data.

- Branch 3:
  - Unsatisfied condition:
    The update would duplicate another session’s `{source}`, `{type}`, and data checksum.
  - Endpoint group:
    Step 1: `POST /api/sessions/{source}/{type}` with body `{dataA}`; the response produces `{idA}`.
    Step 2: `POST /api/sessions/{source}/{type}` with different body `{dataB}`; the response produces `{idB}`.
    Step 3: `PUT /api/sessions/{source}/{type}/{idB}` with body `{dataA}`.
  - Failure endpoint:
    `PUT /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    The unique index on source, type, and checksum is violated. Unlike create, update does not catch the duplicate-key exception, so this is an implementation-level exceptional branch not documented in Swagger.
  - Intentionally violated constraints:
    Step 3 changes the second session to data that already belongs to another session in the same source/type scope.

Endpoint coverage:
- Covers:
  `PUT /api/sessions/{source}/{type}/{id}`
- Distinct meaning:
  Replaces data on an existing session; it is not an upsert.

### Behavior 8: delete session

Behavior name:
delete session

Successful execution:
- Result:
  This behavior deletes the existing session identified by `{id}` for `{source}` and `{type}`.
- Endpoint sequence:
  Step 1: `POST /api/sessions/{source}/{type}` with a valid JSON body; the response produces `{id}`.
  Step 2: `DELETE /api/sessions/{source}/{type}/{id}` using the `{id}` from Step 1.
- Constraints:
  Step 2 must reuse the exact `{source}`, `{type}`, and `{id}` from Step 1. The delete succeeds only when exactly one matching session is removed. Swagger lists `204`, but the implementation returns the default successful empty response, observed as `200 OK`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No matching session exists.
  - Endpoint group:
    Step 1: `DELETE /api/sessions/{source}/{type}/{id}` while intentionally omitting the prerequisite `POST /api/sessions/{source}/{type}`.
  - Failure endpoint:
    `DELETE /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    The delete count is not exactly one, so the service throws not-found.
  - Intentionally violated constraints:
    `{id}` was not obtained from a prior create call in the same source/type scope.

- Branch 2:
  - Unsatisfied condition:
    `{id}` exists, but the delete uses a different `{source}` or `{type}`.
  - Endpoint group:
    Step 1: `POST /api/sessions/{source}/{type}` with a valid JSON body; the response produces `{id}`.
    Step 2: `DELETE /api/sessions/{otherSource}/{type}/{id}` or `DELETE /api/sessions/{source}/{otherType}/{id}`.
  - Failure endpoint:
    `DELETE /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    The delete query is scoped by source, type, and id.
  - Intentionally violated constraints:
    Step 2 does not reuse the same source/type scope as Step 1.

- Branch 3:
  - Unsatisfied condition:
    The session was already deleted.
  - Endpoint group:
    Step 1: `POST /api/sessions/{source}/{type}` with a valid JSON body; the response produces `{id}`.
    Step 2: `DELETE /api/sessions/{source}/{type}/{id}` using the `{id}` from Step 1.
    Step 3: `DELETE /api/sessions/{source}/{type}/{id}` using the same `{id}` again.
  - Failure endpoint:
    `DELETE /api/sessions/{source}/{type}/{id}`
  - Why this fails:
    The second delete removes zero records and is treated as not-found.
  - Intentionally violated constraints:
    The required existing session state was removed before the failing call.

Endpoint coverage:
- Covers:
  `DELETE /api/sessions/{source}/{type}/{id}`
- Distinct meaning:
  Removes one existing session resource; repeated deletes are not treated as successful idempotent deletes.

### Behavior 9: retrieve service information

Behavior name:
retrieve service information

Successful execution:
- Result:
  This behavior returns the service implementation version string exposed by the application package metadata.
- Endpoint sequence:
  Step 1: `GET /info`
- Constraints:
  No session state is required. The security configuration explicitly permits `/info` even when basic authentication is enabled for session endpoints.

Failure or exceptional branches:
No implementation-specific failure branch is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /info`
- Distinct meaning:
  Provides service metadata/version information, separate from session storage workflows.