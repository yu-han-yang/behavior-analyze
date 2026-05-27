# Domain-Level Behavior Analysis

## Domain Summary

The service is a cBioPortal session document store backed by MongoDB. Its main domain resource is a `Session`: a saved JSON/BSON document with a generated `id`, caller-provided `source`, caller-selected `type`, parsed `data`, and an implementation-derived `checksum`.

The service uses `source` as a client or portal scope and `type` as the session category. Supported session types are `main_session`, `virtual_study`, `group`, `comparison_session`, `settings`, `custom_data`, `genomic_chart`, and `custom_gene_list`. Each type is stored in a Mongo collection named after the type. Most session operations require the same `source`, `type`, and sometimes `id` values to be reused, so the resource identity is scoped rather than globally addressable by id alone.

The service also exposes a small metadata endpoint that returns the implementation version string.

## Available Function Inventory

### Session Storage

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `create new session` | `POST /api/sessions/{source}/{type}` | Store a new parsed session document under a source/type scope and return its generated id. |
| `reuse existing duplicate session` | `POST /api/sessions/{source}/{type}` | Return the existing id when the same parsed data is submitted again in the same source/type scope. |
| `list sessions by source and type` | `GET /api/sessions/{source}/{type}` | List all sessions stored for a source and session type. |
| `search sessions by field and value` | `GET /api/sessions/{source}/{type}/query` | Search sessions in a source/type collection using one field/value equality predicate built from query parameters. |
| `search sessions by body filter` | `POST /api/sessions/{source}/{type}/query/fetch` | Search sessions in a source/type collection using a request-body Mongo-style filter. |
| `retrieve session by id` | `GET /api/sessions/{source}/{type}/{id}` | Retrieve one stored session by id within a source/type scope. |
| `update session data` | `PUT /api/sessions/{source}/{type}/{id}` | Replace the stored data for an existing session and recompute its checksum. |
| `delete session` | `DELETE /api/sessions/{source}/{type}/{id}` | Delete one existing session by id within a source/type scope. |

### Service Metadata

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `retrieve service information` | `GET /info` | Return the service implementation version string. |

## Supported Business Behaviors

### Behavior 1: Store A New Scoped Session

Business goal:
Persist a client session document so a cBioPortal caller can later retrieve, search, update, or delete it.

Domain context:
Session data represents saved cBioPortal state such as a main session, virtual study, group, comparison session, settings, custom data, genomic chart, or custom gene list. The service does not interpret the business schema inside `data`; it stores the parsed document and scopes it by `source` and `type`.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create new session` (`POST /api/sessions/{source}/{type}`) with `source=msk_portal`, `type=main_session`, and body `{"portal-session":{"title":"Case review"}}` to create a `main_session` document for the `msk_portal` source and capture the returned generated `id`.

Optional verification workflow:
1. Use function `retrieve session by id` (`GET /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, and `id={id returned by create new session}` to inspect the persisted `data`, `source`, `type`, and `id`.
2. Use function `list sessions by source and type` (`GET /api/sessions/{source}/{type}`) with `source=msk_portal` and `type=main_session` to verify that the new session appears in the source/type collection.

Existing-state shortcuts:
- No setup function is needed for the main workflow because creation can start from an empty service state.
- A direct database insert can create an equivalent session document, but it must include a valid generated or assigned `id`, `source=msk_portal`, `type=main_session`, parsed `data`, and a checksum consistent with the implementation's `data.toString()` MD5 derivation if duplicate behavior will later be tested.
- The core creation action cannot be skipped when the business goal is to create a new session through the API.

Parameter and value bindings:
- The path value `source=msk_portal` is stored as the session's `source`.
- The path value `type=main_session` is stored as the session's `type` and selects the Mongo collection named `main_session`.
- The request body is parsed into the stored `data`; the implementation computes `checksum` from the parsed data and uses it with `source` and `type` for duplicate detection.
- The generated response `id` is the value later consumed as the `{id}` path value by retrieve, update, and delete functions.

Business result:
A session document exists in the `main_session` collection with `source=msk_portal`, `type=main_session`, parsed `data={"portal-session":{"title":"Case review"}}`, an implementation-computed `checksum`, and a generated `id`. The create response returns only the id view.

Constraints and invariants:
- `type` must be one of the implemented `SessionType` enum values.
- `source` must have at least 3 characters when persisted.
- The body must be parseable as a JSON/BSON document. OpenAPI describes it as a string, but the implementation parses it with `Document.parse`.
- The combination of `source`, `type`, and `checksum` is unique in the collection once the collection and index exist.
- The implementation creates the collection and unique index lazily when saving the first session for a type.
- OpenAPI lists `201 Created`, but the controller returns the saved `Session` with the default successful response, which is `200 OK` in the implementation.

Failure and exceptional cases:
- Failing function: `create new session`
  - Failure condition: `type=invalid_type`.
  - Why it fails: Spring cannot bind the path value to the `SessionType` enum, and the controller's type-mismatch handler returns `400 Bad Request` with valid type names.
  - Violated prerequisite or constraint: The type path value must be one of the supported session categories.
- Failing function: `create new session`
  - Failure condition: The body is missing or malformed, such as `{"portal-session":blah}`.
  - Why it fails: `Session.setData` parses string input through `Document.parse`; parse errors are mapped to `SessionInvalidException` and returned as `400 Bad Request`.
  - Violated prerequisite or constraint: Persisted session data must be a valid parsed JSON/BSON document.
- Failing function: `create new session`
  - Failure condition: `source=ab`.
  - Why it fails: The `Session` model enforces `@Size(min=3)` on `source` during repository save, and the service maps the validation failure to `SessionInvalidException`.
  - Violated prerequisite or constraint: Persisted source names must have at least 3 characters.

Implementation notes:
The implementation prioritizes parsed data over raw JSON text. Two textual bodies that parse to equivalent documents and produce the same `data.toString()` checksum are treated as duplicates in the same source/type scope.

### Behavior 2: Reuse An Existing Duplicate Session Submission

Business goal:
Avoid creating another session record when a caller resubmits the same session data for the same source and type.

Domain context:
This is an id reuse behavior for clients that repeatedly save identical session state. It lets callers receive the canonical id for already stored data without manually searching first.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create new session` (`POST /api/sessions/{source}/{type}`) with `source=msk_portal`, `type=main_session`, and body `{"portal-session":{"title":"Case review"}}` to create the initial session and capture `id={initialId}`.
2. Use function `reuse existing duplicate session` (`POST /api/sessions/{source}/{type}`) with the same `source=msk_portal`, same `type=main_session`, and the same body `{"portal-session":{"title":"Case review"}}` to resubmit duplicate data and receive `id={initialId}` again.

Optional verification workflow:
1. Use function `list sessions by source and type` (`GET /api/sessions/{source}/{type}`) with `source=msk_portal` and `type=main_session` to verify that only one matching session exists for the duplicate data in that source/type scope.
2. Use function `retrieve session by id` (`GET /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, and `id={initialId}` to inspect the reused session.

Existing-state shortcuts:
- If an equivalent session already exists, step 1 can be skipped and step 2 can be executed directly as `reuse existing duplicate session`.
- Direct database setup can replace step 1 by inserting a document in the `main_session` collection with `source=msk_portal`, `type=main_session`, the same parsed data, a matching checksum, and a known `id`.
- The duplicate request must still reuse the exact same source/type uniqueness scope and checksum-producing data.

Parameter and value bindings:
- Step 2 must reuse `source=msk_portal` and `type=main_session` from step 1.
- Step 2 must submit body data that produces the same parsed `data` checksum as step 1.
- The `id` returned by step 1 is expected to be returned again by step 2.
- If `source` or `type` intentionally differs, the request is no longer a duplicate reuse; it represents a separate scoped session save.

Business result:
The service stores one session document for the source/type/data combination and returns the same existing id on duplicate submission. No second document is created in the same uniqueness scope.

Constraints and invariants:
- Duplicate reuse is scoped by `source`, `type`, and computed `checksum`, not by the generated id.
- The implementation catches `DuplicateKeyException` only during create and then looks up the existing session by `source`, `type`, and `checksum`.
- Duplicate reuse is case-sensitive for `source`; `msk_portal` and `MSK_portal` are different scopes.

Failure and exceptional cases:
- Failing function: `create new session`
  - Failure condition: The initial setup body is invalid JSON/BSON.
  - Why it fails: The first session cannot be persisted because the body cannot be parsed into stored `data`.
  - Violated prerequisite or constraint: Duplicate reuse requires an existing valid session in the same scope.
- Failing function: `reuse existing duplicate session`
  - Failure condition: The second request uses `source=other_portal`, `type=main_session`, and the same body.
  - Why it fails: It does not trigger duplicate reuse for the original record because uniqueness includes `source`; the implementation can create or resolve a different scoped session.
  - Violated prerequisite or constraint: Duplicate reuse requires the same source/type/checksum scope.
- Failing function: `reuse existing duplicate session`
  - Failure condition: The second request uses the same source/type but a malformed body.
  - Why it fails: The duplicate checksum cannot be computed because parsing fails before persistence and duplicate-key handling.
  - Violated prerequisite or constraint: The duplicate request must still contain valid parsed session data.

Implementation notes:
This behavior is implemented through a database uniqueness violation on save, not by a pre-save existence check. It is idempotent for duplicate create requests, but the update endpoint does not catch duplicate-key violations in the same way.

### Behavior 3: List Sessions In A Source/Type Scope

Business goal:
Inspect all sessions saved by a source for one session category.

Domain context:
Clients need a collection view to find stored sessions for a portal source and type. The list is scoped; it does not return sessions across all sources or all types.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `list sessions by source and type` (`GET /api/sessions/{source}/{type}`) with `source=msk_portal` and `type=main_session` to retrieve all `main_session` records for `msk_portal`.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup is required because the list operation succeeds even when no sessions exist; it returns an empty list.
- To verify a non-empty list, first use function `create new session` (`POST /api/sessions/{source}/{type}`) with `source=msk_portal`, `type=main_session`, and a valid body such as `{"portal-session":{"title":"Case review"}}`, or directly insert equivalent session documents in the `main_session` collection.
- Direct database setup must preserve the `source` and `type` fields because list filtering uses both fields.

Parameter and value bindings:
- The same `source` and `type` values define the list scope.
- Created sessions appear only when their stored `source` and stored `type` match the list path values.
- The generated `id` values returned inside list results can be reused by retrieve, update, or delete functions.

Business result:
The response is a list of full session views for the requested source/type scope. If no matching sessions exist, the business result is an empty collection, not a missing resource.

Constraints and invariants:
- `type` must be a valid enum value.
- `source` is matched exactly and case-sensitively.
- The implementation applies both `source` and `type` criteria to the query.
- OpenAPI lists `404 Not Found`, but the implementation does not return not-found for an empty list.

Failure and exceptional cases:
- Failing function: `list sessions by source and type`
  - Failure condition: `type=invalid_type`.
  - Why it fails: Spring cannot bind the type path value to `SessionType`, and the controller returns `400 Bad Request`.
  - Violated prerequisite or constraint: The list scope must use a supported session type.
- Failing function: `create new session`
  - Failure condition: A setup request intended to create a non-empty list uses `source=ab`.
  - Why it fails: The short source violates persistence validation and no setup session is created.
  - Violated prerequisite or constraint: Non-empty list setup through the API requires valid persisted sessions.

Implementation notes:
The source minimum length validation is enforced on save, not on read. A read with a short source is accepted and usually returns an empty list unless matching database state was inserted outside normal API validation.

### Behavior 4: Retrieve A Specific Scoped Session

Business goal:
Read the complete stored session document for a known session id in its source/type scope.

Domain context:
The generated id from creation is not enough by itself. Retrieval is a scoped lookup using source, type, and id, which preserves tenant/category boundaries at the API level.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create new session` (`POST /api/sessions/{source}/{type}`) with `source=msk_portal`, `type=main_session`, and body `{"portal-session":{"title":"Case review"}}` to create a session and capture `id={sessionId}`.
2. Use function `retrieve session by id` (`GET /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, and `id={sessionId}` to retrieve the full stored session.

Optional verification workflow:
None.

Existing-state shortcuts:
- If an equivalent session already exists and its id is known, step 1 can be skipped.
- Direct database setup can replace step 1 by inserting a document with `id={sessionId}`, `source=msk_portal`, `type=main_session`, valid parsed `data`, and a checksum in the `main_session` collection.
- The known id must still be used together with the matching source and type; an id from another scope is not sufficient.

Parameter and value bindings:
- The `id` returned by `create new session` becomes the `{id}` path value for `retrieve session by id`.
- The retrieval path must reuse the same `source=msk_portal` and `type=main_session` used at creation.
- The response binds the stored `data`, `source`, and `type` to the same record identified by `id={sessionId}`.

Business result:
The caller receives the full stored session: id, parsed data, source, and type. The checksum exists internally and is present on the model, though JSON views in the controller expose only fields included in the full view.

Constraints and invariants:
- Retrieval requires a document matching all three values: `source`, `type`, and `id`.
- `type` must be valid.
- A wrong source or wrong type for an existing id returns not-found because the lookup is scoped.

Failure and exceptional cases:
- Failing function: `create new session`
  - Failure condition: The setup create request uses malformed session data.
  - Why it fails: No session id is generated because data parsing fails.
  - Violated prerequisite or constraint: Retrieval through a complete API workflow requires a previously created valid session id.
- Failing function: `retrieve session by id`
  - Failure condition: `id=missing-id` does not identify a session with `source=msk_portal` and `type=main_session`.
  - Why it fails: `findOneBySourceAndTypeAndId` returns no document, the service throws `SessionNotFoundException`, and the controller returns `404 Not Found`.
  - Violated prerequisite or constraint: The id must exist within the requested source/type scope.
- Failing function: `retrieve session by id`
  - Failure condition: The session was created under `source=msk_portal`, `type=main_session`, but retrieval uses `source=other_portal` or `type=virtual_study`.
  - Why it fails: The repository lookup includes source and type criteria, so an otherwise valid id is not found outside its scope.
  - Violated prerequisite or constraint: Retrieval must reuse the original scope.
- Failing function: `retrieve session by id`
  - Failure condition: `type=invalid_type`.
  - Why it fails: Spring rejects the enum binding before service lookup.
  - Violated prerequisite or constraint: The requested session category must be supported.

Implementation notes:
There is no endpoint for global retrieval by id alone. This is intentional in the current API shape, but it means clients must retain source and type alongside id.

### Behavior 5: Search Sessions By A Single Data Field

Business goal:
Find saved sessions in a source/type scope where one stored field equals a requested value.

Domain context:
Clients often know a property inside the saved session data, such as a title. This behavior supports simple equality lookup without sending a request-body filter.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create new session` (`POST /api/sessions/{source}/{type}`) with `source=msk_portal`, `type=main_session`, and body `{"portal-session":{"title":"Case review"}}` to create a searchable session.
2. Use function `search sessions by field and value` (`GET /api/sessions/{source}/{type}/query`) with `source=msk_portal`, `type=main_session`, query `field=data.portal-session.title`, and query `value=Case review` to find sessions whose stored title matches the requested value.

Optional verification workflow:
1. Use function `retrieve session by id` (`GET /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, and `id={id from a search result}` to inspect one matching session.

Existing-state shortcuts:
- If a matching session already exists, step 1 can be skipped.
- Direct database setup can replace step 1 by inserting a session in the `main_session` collection whose stored data contains `portal-session.title = "Case review"` and whose stored `source` is `msk_portal`.
- The searchable field path and value must correspond to the stored parsed data shape; using the raw request body text is not equivalent.

Parameter and value bindings:
- The search path reuses `source=msk_portal` and `type=main_session`.
- The query parameter `field=data.portal-session.title` is bound to the nested stored data path.
- The query parameter `value=Case review` must equal the stored value as represented in the generated Mongo query.
- Search result ids can be reused in `retrieve session by id`, `update session data`, or `delete session`.

Business result:
The response lists sessions in the selected type collection and source scope whose generated equality filter matches the requested field/value pair. The operation does not mutate state.

Constraints and invariants:
- `type` must be valid.
- The controller constructs a query string in the form `{"{field}":"{value}"}`.
- The repository adds `source=msk_portal` as an additional criterion.
- Because the collection is selected by `type`, normal API-created data is scoped by type, but the query implementation itself does not add a `type` field criterion for body or field/value searches.
- The field name must be acceptable to Mongo/Spring `BasicQuery`; invalid field syntax fails.

Failure and exceptional cases:
- Failing function: `create new session`
  - Failure condition: The setup body does not contain valid JSON/BSON.
  - Why it fails: The searchable session is not created because parsing fails.
  - Violated prerequisite or constraint: A non-empty search workflow needs valid persisted data.
- Failing function: `search sessions by field and value`
  - Failure condition: `field=$data.portal-session.title` or a field containing a null character.
  - Why it fails: `BasicQuery` rejects invalid query syntax or invalid BSON field names; the service maps the error to `SessionQueryInvalidException` and the controller returns `400 Bad Request`.
  - Violated prerequisite or constraint: The field must produce a valid Mongo-style query document.
- Failing function: `search sessions by field and value`
  - Failure condition: `type=invalid_type`.
  - Why it fails: Spring rejects the enum binding.
  - Violated prerequisite or constraint: Search must target a supported session type.

Implementation notes:
The URL-query search is not a general typed query language. It builds a string query from raw `field` and `value` parameters, so all searched values are inserted as strings in the generated query.

### Behavior 6: Search Sessions With A Body Filter

Business goal:
Find saved sessions using a caller-supplied Mongo-style filter document.

Domain context:
The body-filter search supports more expressive stored-data lookup than the single field/value endpoint, because the caller supplies the query document directly.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create new session` (`POST /api/sessions/{source}/{type}`) with `source=msk_portal`, `type=main_session`, and body `{"portal-session":{"title":"Case review","owner":"analyst-1"}}` to create a searchable session.
2. Use function `search sessions by body filter` (`POST /api/sessions/{source}/{type}/query/fetch`) with `source=msk_portal`, `type=main_session`, and body `{"data.portal-session.owner":"analyst-1"}` to search for sessions matching the owner field.

Optional verification workflow:
1. Use function `retrieve session by id` (`GET /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, and `id={id from a search result}` to inspect a matching session.

Existing-state shortcuts:
- If a matching session already exists, step 1 can be skipped.
- Direct database setup can replace step 1 by inserting a session in the `main_session` collection with `source=msk_portal` and stored data matching the filter.
- The request-body filter must match stored parsed data fields, and source scoping is still applied by the repository.

Parameter and value bindings:
- The body filter field `data.portal-session.owner` is bound to the nested stored data field created in step 1.
- The body filter value `analyst-1` must equal the stored field value for the session to match.
- The path `source=msk_portal` is added by the repository as an extra criterion.
- The path `type=main_session` selects the Mongo collection; normal API-created sessions in that collection also store `type=main_session`.

Business result:
The response lists sessions in the chosen type collection and source scope that match the supplied Mongo-style filter. The operation does not mutate state.

Constraints and invariants:
- `type` must be valid.
- The body must be a valid Mongo-style JSON filter accepted by `BasicQuery`.
- The implementation always adds `source` as an additional criterion.
- OpenAPI lists `201 Created`, but the implementation returns a read result list with the default `200 OK`.

Failure and exceptional cases:
- Failing function: `create new session`
  - Failure condition: The setup body is invalid session data.
  - Why it fails: The searchable session is not persisted.
  - Violated prerequisite or constraint: The workflow needs a valid stored document to produce a matching result.
- Failing function: `search sessions by body filter`
  - Failure condition: The filter body is malformed or contains an invalid field such as `{"$data.portal-session.owner":"analyst-1"}`.
  - Why it fails: `BasicQuery` or Mongo rejects the query; the service maps the exception to `SessionQueryInvalidException` and returns `400 Bad Request`.
  - Violated prerequisite or constraint: The body must be a valid Mongo-style query document.
- Failing function: `search sessions by body filter`
  - Failure condition: `type=invalid_type`.
  - Why it fails: Spring cannot bind the type path value to `SessionType`.
  - Violated prerequisite or constraint: Body-filter search must target a supported type collection.

Implementation notes:
This function is a read/search operation even though it uses `POST`. It does not create a session. The OpenAPI `201 Created` response is therefore misleading.

### Behavior 7: Replace The Data In An Existing Session

Business goal:
Change the saved state of a specific session while keeping the same scoped session identity.

Domain context:
A client may need to overwrite a saved session after user changes. The service supports full data replacement, not patching individual fields.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create new session` (`POST /api/sessions/{source}/{type}`) with `source=msk_portal`, `type=main_session`, and body `{"portal-session":{"title":"Draft review"}}` to create the session and capture `id={sessionId}`.
2. Use function `update session data` (`PUT /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, `id={sessionId}`, and body `{"portal-session":{"title":"Final review"}}` to replace the stored data.

Optional verification workflow:
1. Use function `retrieve session by id` (`GET /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, and `id={sessionId}` to verify that `data.portal-session.title` is now `Final review`.
2. Use function `search sessions by field and value` (`GET /api/sessions/{source}/{type}/query`) with `source=msk_portal`, `type=main_session`, `field=data.portal-session.title`, and `value=Final review` to find the updated session by the new value.

Existing-state shortcuts:
- If the target session already exists and its id, source, and type are known, step 1 can be skipped.
- Direct database setup can replace step 1 by inserting a valid session with `id={sessionId}`, `source=msk_portal`, `type=main_session`, parsed data, and checksum.
- The update action itself cannot be skipped because it is the core behavior.

Parameter and value bindings:
- The `id` returned by creation is reused as the update `{id}` path value.
- The update path must reuse the original `source=msk_portal` and `type=main_session`.
- The update body replaces the prior `data` and causes the implementation to recompute `checksum`.
- The `source`, `type`, and `id` identity values remain unchanged by the update.

Business result:
The existing session still exists under the same source/type/id, but its stored `data` is replaced by the new parsed body and its checksum is recomputed. A successful implementation response has an empty body.

Constraints and invariants:
- Update is not an upsert; a matching session must already exist.
- The body must be parseable as JSON/BSON.
- The unique `source`/`type`/`checksum` index still applies after update.
- OpenAPI lists `201 Created`, but the controller method returns `void`; successful implementation behavior is `200 OK` with no response body.

Failure and exceptional cases:
- Failing function: `create new session`
  - Failure condition: The setup create request uses an invalid type or invalid data.
  - Why it fails: No target session is created, so there is no id to update.
  - Violated prerequisite or constraint: Update requires an existing valid session.
- Failing function: `update session data`
  - Failure condition: `id=missing-id` or the request uses a different source/type from the created session.
  - Why it fails: The service first calls `findOneBySourceAndTypeAndId`; no matching document results in `SessionNotFoundException` and `404 Not Found`.
  - Violated prerequisite or constraint: The update target must exist in the requested scope.
- Failing function: `update session data`
  - Failure condition: The update body is malformed, such as `{"portal-session":blah}`.
  - Why it fails: `Session.setData` fails to parse the new data, and the service maps the parse error to `SessionInvalidException`.
  - Violated prerequisite or constraint: Replacement data must be valid parsed session data.
- Failing function: `update session data`
  - Failure condition: A second session already exists in the same source/type scope with the same target data checksum.
  - Why it fails: Mongo's unique index on `source`, `type`, and `checksum` is violated during save. Unlike create, update does not catch `DuplicateKeyException`.
  - Violated prerequisite or constraint: Updated data must not duplicate another session's checksum in the same source/type scope.

Implementation notes:
The service replaces the whole `data` object. There is no merge or partial update behavior, and there is no duplicate-id reuse fallback for update.

### Behavior 8: Delete A Scoped Session

Business goal:
Remove a saved session from its source/type scope.

Domain context:
Clients need to retire stored session state. Deletion is scoped so a session can only be removed when the caller supplies the same source, type, and id used to address it.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create new session` (`POST /api/sessions/{source}/{type}`) with `source=msk_portal`, `type=main_session`, and body `{"portal-session":{"title":"Temporary review"}}` to create the session and capture `id={sessionId}`.
2. Use function `delete session` (`DELETE /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, and `id={sessionId}` to delete the session.

Optional verification workflow:
1. Use function `retrieve session by id` (`GET /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, and `id={sessionId}` to verify that retrieval now returns not-found.
2. Use function `list sessions by source and type` (`GET /api/sessions/{source}/{type}`) with `source=msk_portal` and `type=main_session` to inspect the remaining sessions in the scope.

Existing-state shortcuts:
- If the target session already exists and its id, source, and type are known, step 1 can be skipped.
- Direct database setup can replace step 1 by inserting a valid session with matching `id`, `source`, and `type`.
- The delete action itself cannot be skipped because it is the core behavior.

Parameter and value bindings:
- The `id` returned by creation is reused as the delete `{id}` path value.
- The delete path must reuse the original `source` and `type`.
- The same `id` cannot be successfully deleted again after the first delete removes it.

Business result:
Exactly one matching session document is removed from the selected type collection. No cascading delete behavior is visible because sessions are independent documents in this service.

Constraints and invariants:
- Delete succeeds only when exactly one document matches `source`, `type`, and `id`.
- Repeated delete is not treated as idempotent success; once the document is gone, another delete returns not-found.
- OpenAPI lists `204 No Content`, but the implementation returns `200 OK` with an empty body on success.
- OpenAPI omits `404` for delete, but implementation can return `404 Not Found`.

Failure and exceptional cases:
- Failing function: `create new session`
  - Failure condition: The setup request cannot persist valid session data.
  - Why it fails: There is no generated id to delete.
  - Violated prerequisite or constraint: Delete requires an existing target session.
- Failing function: `delete session`
  - Failure condition: `id=missing-id`.
  - Why it fails: The repository removes zero documents, and the service treats any delete count other than one as `SessionNotFoundException`.
  - Violated prerequisite or constraint: The delete target must exist in the requested source/type scope.
- Failing function: `delete session`
  - Failure condition: The session exists under `type=virtual_study`, but the delete request uses `type=main_session`.
  - Why it fails: The delete query is scoped by source, type, and id, so it removes zero documents.
  - Violated prerequisite or constraint: Deletion must reuse the session's actual type scope.
- Failing function: `delete session`
  - Failure condition: A second delete is issued after the first successful delete.
  - Why it fails: The target state was removed, so the second delete removes zero documents.
  - Violated prerequisite or constraint: The target session must still exist.

Implementation notes:
Deletion is a hard delete from MongoDB. The service does not expose soft-delete status, audit history, or recovery.

### Behavior 9: Run A Complete Session Lifecycle

Business goal:
Create a session, revise its data, and remove it when it is no longer needed.

Domain context:
This composite behavior represents a normal lifecycle for saved cBioPortal state. It combines the core mutating operations into one API-realizable workflow.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create new session` (`POST /api/sessions/{source}/{type}`) with `source=msk_portal`, `type=main_session`, and body `{"portal-session":{"title":"Initial lifecycle state"}}` to create the session and capture `id={sessionId}`.
2. Use function `update session data` (`PUT /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, `id={sessionId}`, and body `{"portal-session":{"title":"Updated lifecycle state"}}` to replace the stored state.
3. Use function `delete session` (`DELETE /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, and `id={sessionId}` to remove the updated session.

Optional verification workflow:
1. Use function `retrieve session by id` (`GET /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, and `id={sessionId}` after step 1 or step 2 to inspect the current state.
2. Use function `search sessions by field and value` (`GET /api/sessions/{source}/{type}/query`) with `source=msk_portal`, `type=main_session`, `field=data.portal-session.title`, and `value=Updated lifecycle state` after step 2 to verify the updated value is searchable.
3. Use function `retrieve session by id` (`GET /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, and `id={sessionId}` after step 3 to verify that the deleted session is no longer retrievable.

Existing-state shortcuts:
- If the lifecycle is intended to start from an already existing session, step 1 can be skipped, but the caller must already know `id={sessionId}`, `source=msk_portal`, and `type=main_session`.
- Direct database setup can replace step 1 only if it creates a valid document with matching source/type/id and checksum.
- The update and delete steps cannot be skipped without changing the lifecycle result.

Parameter and value bindings:
- The generated `id` from creation is reused by both update and delete.
- The same `source` and `type` scope must be reused across all mutating functions.
- The update body intentionally differs from the create body; this represents a domain state change from initial to updated saved session data.
- The delete consumes the same scoped identity after update and removes that final session state.

Business result:
The service first creates a session, then changes its stored data, then removes the session. After the workflow completes, the final domain state is that the session no longer exists in the source/type scope.

Constraints and invariants:
- Each mutating step uses the same scoped identity.
- The update step is not transactional with the later delete; if delete fails, the updated session remains.
- There is no version check or ETag, so concurrent updates are last-write-wins unless a duplicate checksum constraint is violated.
- No audit trail records the previous data or deletion event.

Failure and exceptional cases:
- Failing function: `create new session`
  - Failure condition: Invalid type, short source, or invalid body.
  - Why it fails: The service cannot persist the initial session, so no generated id exists for later lifecycle steps.
  - Violated prerequisite or constraint: Lifecycle creation must satisfy create validation.
- Failing function: `update session data`
  - Failure condition: The session is deleted or the wrong source/type is used before update.
  - Why it fails: The scoped lookup returns no session and update returns not-found.
  - Violated prerequisite or constraint: The update target must exist under the same scope created in step 1.
- Failing function: `delete session`
  - Failure condition: The wrong source/type/id is used after a successful update.
  - Why it fails: The delete query removes zero documents, and the service returns not-found.
  - Violated prerequisite or constraint: Delete must address the same scoped session identity.

Implementation notes:
The service has no transaction boundary spanning create, update, and delete. Each function commits independently.

### Behavior 10: Maintain Separate Sessions Across Sources Or Types

Business goal:
Store the same session data independently for different portal sources or different session categories.

Domain context:
The service is multi-scope. Identical session data may be meaningful in separate portals or categories, and uniqueness should not collapse those across source/type boundaries.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create new session` (`POST /api/sessions/{source}/{type}`) with `source=msk_portal`, `type=main_session`, and body `{"portal-session":{"title":"Shared content"}}` to create the first scoped session and capture `id={mskMainId}`.
2. Use function `create new session` (`POST /api/sessions/{source}/{type}`) with `source=other_portal`, `type=main_session`, and the same body `{"portal-session":{"title":"Shared content"}}` to create a separate session for another source and capture `id={otherMainId}`.
3. Use function `create new session` (`POST /api/sessions/{source}/{type}`) with `source=msk_portal`, `type=settings`, and the same body `{"portal-session":{"title":"Shared content"}}` to create a separate session in another type collection and capture `id={mskSettingsId}`.

Optional verification workflow:
1. Use function `retrieve session by id` (`GET /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=main_session`, and `id={mskMainId}` to inspect the first session.
2. Use function `retrieve session by id` (`GET /api/sessions/{source}/{type}/{id}`) with `source=other_portal`, `type=main_session`, and `id={otherMainId}` to inspect the second source-scoped session.
3. Use function `retrieve session by id` (`GET /api/sessions/{source}/{type}/{id}`) with `source=msk_portal`, `type=settings`, and `id={mskSettingsId}` to inspect the type-scoped session.

Existing-state shortcuts:
- Any of the create steps can be skipped if the corresponding scoped session already exists and the workflow only needs to demonstrate or use the independent scopes.
- Direct database setup can create equivalent documents, but each document must be placed in the collection named by its type and have matching stored `source` and `type` fields.
- If the same source/type/data combination already exists, the create step for that exact scope will reuse the existing id rather than create a new record.

Parameter and value bindings:
- Steps 1 and 2 intentionally reuse the same `type` and body but change `source`; this creates different source-scoped sessions.
- Steps 1 and 3 intentionally reuse the same `source` and body but change `type`; this creates different type-scoped sessions in different collections.
- The returned ids should differ across different uniqueness scopes, unless direct database manipulation has created unusual state.

Business result:
The same data can exist as separate sessions when source or type differs. Duplicate suppression is limited to the same source/type/checksum scope.

Constraints and invariants:
- Source values are case-sensitive.
- Type values must be one of the enum values.
- The unique index does not enforce global uniqueness across sources or types.
- The implementation stores each type in a separate collection and also stores the `type` field in the document.

Failure and exceptional cases:
- Failing function: `create new session`
  - Failure condition: Step 2 uses `source=ab`.
  - Why it fails: Source validation rejects the short source during persistence.
  - Violated prerequisite or constraint: Each independently scoped session must still satisfy create validation.
- Failing function: `create new session`
  - Failure condition: Step 3 uses `type=unknown`.
  - Why it fails: Type enum binding fails before persistence.
  - Violated prerequisite or constraint: Independent type scoping is limited to supported session types.
- Failing function: `retrieve session by id`
  - Failure condition: Verification uses `id={mskMainId}` with `source=other_portal`.
  - Why it fails: The retrieval query is scoped by source/type/id, so the first session id is not valid in the second source scope.
  - Violated prerequisite or constraint: Verification must bind each generated id to the source/type scope that produced it.

Implementation notes:
This behavior is backed by the create tests for uniqueness: submitting the same data under a different source creates a different id, while the same source/type/data reuses the existing id.

### Behavior 11: Retrieve Service Metadata

Business goal:
Inspect the deployed service version independently of session data.

Domain context:
Operators and clients may need a lightweight metadata endpoint to identify the running session-service implementation.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `retrieve service information` (`GET /info`) with no path, query, body, form, or header values to retrieve the implementation version string.

Optional verification workflow:
None.

Existing-state shortcuts:
- No session setup is relevant.
- Direct database setup is irrelevant because the endpoint reads application package metadata, not Mongo session data.

Parameter and value bindings:
- There are no session ids, source values, type values, request bodies, generated tokens, cursors, or ownership values to bind.

Business result:
The response contains the implementation version string returned by `getClass().getPackage().getImplementationVersion()`. Depending on packaging, that value may be null or a concrete version string.

Constraints and invariants:
- The endpoint is outside `/api/sessions/`.
- Security configuration explicitly permits `/info` when basic security is enabled; when security is disabled, all endpoints are permitted.
- OpenAPI lists generic `401`, `403`, and `404` responses, but no implementation-specific failure branch is visible for this endpoint.

Failure and exceptional cases:
- Failing function: `retrieve service information`
  - Failure condition: No implementation-specific failure condition is visible in the inspected source.
  - Why it fails: Not applicable from the service code.
  - Violated prerequisite or constraint: Not applicable.

Implementation notes:
The endpoint returns a plain string and does not expose health, database connectivity, supported type metadata, or build details beyond the package implementation version.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Enforce User Ownership Or Authorization On Session Data

Priority:
Critical domain gap

Expected business goal:
Ensure that only the user or client that owns a session can read, update, delete, or search it.

Why it is unsupported:
No function accepts an owner id, user id, bearer token, session token, ownership header, or permission scope. The optional basic security configuration only authenticates requests globally; it does not bind session documents to owners or enforce per-session authorization.

Existing functions considered:
- `create new session`: stores `source`, `type`, `data`, and checksum, but no owner or access-control field is enforced.
- `retrieve session by id`: requires source/type/id, but not ownership.
- `list sessions by source and type`: exposes all sessions in a source/type scope to any authorized caller.
- `search sessions by field and value`: searches by arbitrary stored field and source, not caller ownership.
- `search sessions by body filter`: accepts caller-provided filters, but ownership is not added by the service.
- `update session data`: updates by source/type/id without owner verification.
- `delete session`: deletes by source/type/id without owner verification.

Missing capability:
A persisted ownership model, authenticated principal binding, and authorization checks on read, search, update, and delete.

Proof that function composition is insufficient:
Chaining create with retrieve, search, update, or delete can only reuse `source`, `type`, and `id`. It cannot create an enforced owner relationship because no function records the authenticated principal as immutable owner state and no later function checks it. Storing an owner field inside `data` is not equivalent because the service treats `data` as opaque caller-controlled content and does not enforce or preserve it.

Evidence from existing functions/source:
- `Session` has fields for `id`, `checksum`, `data`, `source`, and `type`, but no owner.
- `SessionRepositoryImpl` queries use source/type/id or source plus caller filter; no principal criterion is added.
- `SessionServiceController.configure` can require basic authentication, but controller methods do not inspect the authenticated user.

Business impact:
Any caller that knows or can guess a source/type/id or searchable field can access or mutate sessions within that scope, which is unsafe for user-specific saved state.

### Missing Behavior 2: Partially Update Or Patch Session Data

Priority:
Important robustness gap

Expected business goal:
Modify one field inside a saved session document without replacing the entire stored `data` object.

Why it is unsupported:
The only update function replaces the full data body. No function accepts a patch operation, merge request, JSON Patch, field path update, or targeted Mongo update.

Existing functions considered:
- `update session data`: can replace all stored data for an existing session but cannot patch one field.
- `retrieve session by id`: can read the current document, but does not update it.
- `search sessions by field and value`: can find sessions by a field but cannot modify them.
- `search sessions by body filter`: can find sessions with a filter but cannot modify them.

Missing capability:
A partial update endpoint or update semantics that merge a requested field-level change into existing data while preserving unrelated fields.

Proof that function composition is insufficient:
A client can retrieve the full session, modify it locally, and call `update session data`, but that is not equivalent to server-side patching. It can overwrite concurrent changes, lacks field-level conflict detection, and depends on the client preserving every unrelated field exactly.

Evidence from existing functions/source:
- `SessionServiceImpl.updateSession` calls `savedSession.setData(data)` and saves the session; it does not merge with existing data.
- No controller route exposes `PATCH`.

Business impact:
Clients must handle full-document read-modify-write cycles, which increases accidental data loss risk and makes concurrent edits unsafe.

### Missing Behavior 3: Safely Rename Or Move A Session Across Source Or Type

Priority:
Important robustness gap

Expected business goal:
Move a saved session from one source or type to another, or correct a source/type classification while preserving identity or history.

Why it is unsupported:
No function updates `source` or `type`. The update function only replaces `data`. Create can make a new session in another scope, and delete can remove the old one, but that is not an atomic move and does not preserve the same id.

Existing functions considered:
- `create new session`: can create a new document in the target source/type scope.
- `retrieve session by id`: can read the old data for client-side copying.
- `update session data`: cannot change source or type.
- `delete session`: can remove the old scoped session after a replacement is created.

Missing capability:
A move or rename endpoint that changes source/type atomically, handles uniqueness conflicts, and defines whether the id is preserved.

Proof that function composition is insufficient:
Retrieve-create-delete can copy data to a new scope, but it creates a new id, can leave duplicate state if delete fails, can lose the original if deletion succeeds after an incorrect create, and cannot be made atomic through existing functions.

Evidence from existing functions/source:
- `Session` has setters for source and type, but `SessionServiceImpl.updateSession` only calls `setData`.
- Delete and create are independent operations with no transaction boundary.

Business impact:
Administrative correction or migration of saved session scopes is unsafe and cannot preserve stable references.

### Missing Behavior 4: Validate Type-Specific Session Schemas

Priority:
Important robustness gap

Expected business goal:
Ensure that `main_session`, `virtual_study`, `settings`, and other session types contain data structures appropriate for that business category.

Why it is unsupported:
The implementation validates only that the body is parseable JSON/BSON and that source/type/checksum are present. It does not validate the shape or required fields of `data` for any `SessionType`.

Existing functions considered:
- `create new session`: accepts any valid JSON/BSON document for any type.
- `update session data`: accepts any valid JSON/BSON document for an existing session.
- `search sessions by field and value`: can query fields but does not validate schema.
- `search sessions by body filter`: can query fields but does not validate schema.

Missing capability:
Type-specific schema validation rules at create and update time.

Proof that function composition is insufficient:
Search and retrieve can inspect stored data after the fact, but no existing function prevents invalid business data from being saved. A client-side validator is not equivalent because other clients can bypass it.

Evidence from existing functions/source:
- `Session.setData` only parses the body and computes checksum.
- `SessionType` enumerates categories but no schema rules are attached to those categories.
- The tests show generic `"portal-session"` data stored under multiple types.

Business impact:
Invalid or semantically meaningless session data can be persisted and later returned as if it were a valid cBioPortal session.

### Missing Behavior 5: Prevent Duplicate Checksum Failures During Update With A Domain Response

Priority:
Important robustness gap

Expected business goal:
Handle updates that would duplicate another session's data in the same source/type scope by either returning the existing id or producing a clear business-level conflict response.

Why it is unsupported:
Duplicate handling is implemented only for create. Update can violate the unique source/type/checksum index, but the service does not catch `DuplicateKeyException` in `update session data`.

Existing functions considered:
- `reuse existing duplicate session`: returns the existing id for duplicate create requests only.
- `update session data`: can replace data, but does not handle duplicate checksum conflicts gracefully.
- `list sessions by source and type`: can help a client discover sessions, but cannot make update conflict handling atomic.
- `search sessions by body filter`: may find equivalent data, but cannot prevent a later race.

Missing capability:
Conflict handling for update, such as `409 Conflict`, duplicate id reuse semantics, or an atomic precondition check.

Proof that function composition is insufficient:
A client can search before update, but another write can occur between search and update. Existing functions cannot atomically test duplicate absence and save replacement data.

Evidence from existing functions/source:
- `SessionServiceImpl.addSession` catches `DuplicateKeyException` and resolves the existing session.
- `SessionServiceImpl.updateSession` catches validation and parse errors but not `DuplicateKeyException`.

Business impact:
Clients may receive low-level server errors or inconsistent behavior when updating one session to match another, weakening idempotent save workflows.

### Missing Behavior 6: Paginate, Sort, Or Limit Session Listings And Searches

Priority:
API ergonomics gap

Expected business goal:
Browse large session collections predictably with pagination, sorting, and result limits.

Why it is unsupported:
List and search endpoints return all matching sessions. No function accepts page, size, sort, cursor, or limit parameters.

Existing functions considered:
- `list sessions by source and type`: returns all sessions in a scope.
- `search sessions by field and value`: filters by one equality predicate but has no pagination.
- `search sessions by body filter`: accepts a Mongo-style filter but no explicit API-level paging or sort contract.

Missing capability:
Pagination and ordering parameters, response cursors, total counts, or bounded result semantics.

Proof that function composition is insufficient:
Repeated list or search calls with the same inputs return the same unbounded result set. The API does not expose a cursor or stable ordering value that can be captured and reused to retrieve the next page.

Evidence from existing functions/source:
- Controller method signatures for list and search include only source/type, field/value, or query body.
- Repository methods call `mongoTemplate.find` without `limit`, `skip`, or `sort`.

Business impact:
Large source/type scopes can produce expensive or unwieldy responses, and clients cannot reliably build scalable browsing workflows.

### Missing Behavior 7: Retrieve A Session By Id Without Prior Source And Type Knowledge

Priority:
API ergonomics gap

Expected business goal:
Resolve a saved session from a shared id when the caller does not know its source or type.

Why it is unsupported:
Every single-session read, update, and delete endpoint requires `source`, `type`, and `id`. There is no global id lookup endpoint.

Existing functions considered:
- `retrieve session by id`: retrieves only when the caller supplies the correct source and type.
- `list sessions by source and type`: requires the caller to know the source and type in advance.
- `search sessions by body filter`: requires type and source, and searches by data rather than id globally.

Missing capability:
A global lookup such as `GET /api/sessions/{id}` or an index/search endpoint that can resolve id to source/type.

Proof that function composition is insufficient:
Without knowing type, the caller would have to guess among all supported types. Without knowing source, there is no API to enumerate all sources. Chaining existing functions cannot discover the missing scope values from the id alone.

Evidence from existing functions/source:
- `SessionServiceController.getSession` maps only `/api/sessions/{source}/{type}/{id}`.
- `SessionRepositoryImpl.findOneBySourceAndTypeAndId` requires all three values.

Business impact:
External links or persisted references must store source and type alongside id, otherwise the session cannot be resolved through the API.

### Missing Behavior 8: Enumerate Available Sources Or Session Types From Runtime Data

Priority:
API ergonomics gap

Expected business goal:
Discover which sources or session categories currently have stored sessions.

Why it is unsupported:
The type enum is documented in OpenAPI, but no runtime endpoint lists source values, populated type collections, or counts.

Existing functions considered:
- `list sessions by source and type`: can list sessions only after the caller already knows both source and type.
- `search sessions by field and value`: still requires source and type.
- `search sessions by body filter`: still requires source and type.
- `retrieve service information`: returns version metadata only, not domain inventory.

Missing capability:
Endpoints for source discovery, type inventory, collection counts, or session summary metadata.

Proof that function composition is insufficient:
Existing reads are scoped by caller-provided source/type. Because no function returns all sources or all populated types, a client cannot discover unknown scopes by chaining available functions.

Evidence from existing functions/source:
- Controller routes all session collection reads through `/{source}/{type}`.
- The metadata controller exposes only `/info`.

Business impact:
Administrative UIs and cleanup tools cannot discover stored session domains without direct database access or out-of-band knowledge.

### Missing Behavior 9: Provide Safe Structured Query Parameters For Complex Search

Priority:
API ergonomics gap

Expected business goal:
Search stored sessions with explicit, validated query operators without exposing raw Mongo query syntax or fragile string-built filters.

Why it is unsupported:
One search endpoint builds a raw JSON query string from `field` and `value`; the other accepts a raw Mongo-style filter body. Neither provides a structured, domain-safe query DSL.

Existing functions considered:
- `search sessions by field and value`: supports one equality predicate but builds query JSON through string concatenation.
- `search sessions by body filter`: supports more flexible filters but delegates raw query validation to `BasicQuery` and Mongo.

Missing capability:
A validated query contract with allowed fields, allowed operators, typed values, escaping, and clear error semantics.

Proof that function composition is insufficient:
Chaining raw search calls does not add validation or operator control. Clients cannot express safe typed filters beyond what raw Mongo syntax accepts, and the service cannot distinguish intended business searches from unsafe or malformed query documents.

Evidence from existing functions/source:
- `SessionServiceController.getSessionsByQuery` builds `"{\""+field+"\":\""+value+"\"}"`.
- `SessionRepositoryImpl.findBySourceAndTypeAndQuery` constructs `new BasicQuery(query)`.

Business impact:
Search behavior is brittle, hard to validate, and potentially exposes implementation-specific Mongo query semantics to API consumers.

## Cross-Behavior Observations

- The dominant identity rule is scoped identity: callers must preserve `source`, `type`, and usually `id` across workflows.
- The service validates session type and JSON parseability, but it does not validate type-specific business schemas inside `data`.
- Duplicate create behavior is based on the checksum of parsed data plus source/type. It is helpful for idempotent creates but is not mirrored by update.
- Source values are case-sensitive and are only length-validated during persistence.
- Search endpoints apply source scoping. For query searches, type scoping is primarily enforced by collection selection rather than an added `type` field criterion.
- There is no per-session ownership or authorization model in the domain logic.
- Mutating workflows are not transactional across multiple API calls.
- Deletes are hard deletes and repeated deletes return not-found rather than idempotent success.
- OpenAPI and implementation disagree in several places: create, update, delete, and body-filter search list `201` or `204` responses that the implementation does not use; list documents `404` even though empty lists are successful; delete can return `404` though OpenAPI omits it; body schemas are documented as strings but parsed as JSON/BSON documents.
- The service does not execute domain events, background reevaluation, cascade cleanup, or derived business validation after data changes.

## Coverage Summary

Supported domain areas:
Session creation, duplicate create reuse, source/type scoped listing, scoped retrieval, simple field/value search, Mongo-style body-filter search, full data replacement, scoped deletion, cross-source/type separation, and service version metadata.

Partially supported domain areas:
Idempotency is supported for duplicate creates but not duplicate updates. Search is supported but exposes low-level Mongo query behavior and lacks pagination. Security can require basic authentication, but domain ownership is not modeled.

Unsupported domain areas:
Per-user ownership, type-specific schema validation, partial updates, atomic moves between source/type scopes, global id lookup, source/type discovery, scalable pagination/sorting, safe structured query semantics, audit/history, soft delete, and transactional multi-step workflows.
