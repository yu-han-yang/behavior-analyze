# Domain-Level Behavior Analysis

## Domain Summary

`scout-api` is an activity catalog service for scouting and guiding activities. Its main domain resources are activities, activity properties/revisions, tags/categories, media files, users, user identities, user-specific ratings/favourites, system messages, and system role/permission metadata.

The service supports activity discovery, activity authoring, taxonomy and media management, user profile/identity administration, personal rating/favourite state, and operational metadata. The OpenAPI contract exposes both v1 and v2 activity endpoints; v1 categories and v2 tags represent the same `Tag` domain model. Implementation evidence shows role-based authorization, API-key and Google authentication, generated IDs, derived counters, and several discrepancies where Swagger advertises behavior that the implementation rejects or only partially supports.

## Available Function Inventory

### Activity discovery and lifecycle

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search activities` | `GET /v1/activities`, `GET /v2/activities` | Search the activity catalog by text, category, age/time/participant ranges, feature flag, ratings, and response attributes. |
| `retrieve activities by identifier list` | `GET /v1/activities`, `GET /v2/activities` | Retrieve a collection of activities by comma-separated internal activity IDs. |
| `retrieve random activities` | `GET /v1/activities`, `GET /v2/activities` | Return a random subset of matching activities. |
| `list overall favourite activities` | `GET /v1/activities`, `GET /v2/activities` | Return globally favourite activities ranked by favourite count. |
| `list current user's favourite activities` | `GET /v1/activities`, `GET /v2/activities` | Intended to return activity objects favourited by the current user, but implementation tests show `my_favourites` currently fails with `500`. |
| `create activity` | `POST /v1/activities`, `POST /v2/activities` | Create an activity and return an activity with a generated internal `id`. |
| `read activity` | `GET /v1/activities/{id}`, `GET /v2/activities/{id}` | Retrieve one activity by internal ID. |
| `replace activity` | `PUT /v1/activities/{id}`, `PUT /v2/activities/{id}` | Replace the activity properties for an existing activity; unspecified properties are cleared. |
| `partially update activity` | `PATCH /v1/activities/{id}`, `PATCH /v2/activities/{id}` | Update only supplied activity properties. |
| `delete activity` | `DELETE /v1/activities/{id}`, `DELETE /v2/activities/{id}` | Delete an existing activity. |

### Activity ratings and favourites

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `set own activity rating or favourite flag` | `POST /v1/activities/{id}/rating`, `POST /v2/activities/{id}/rating` | Set the authenticated user’s rating and/or favourite flag for one activity. |
| `read own activity rating` | `GET /v1/activities/{id}/rating`, `GET /v2/activities/{id}/rating` | Read the authenticated user’s rating/favourite state for one activity. |
| `remove own activity rating` | `DELETE /v1/activities/{id}/rating`, `DELETE /v2/activities/{id}/rating` | Remove the authenticated user’s rating/favourite state for one activity. |
| `list favourite activity ids` | `GET /v1/favourites` | Return the current user’s favourite activity IDs. |
| `replace favourite activity id list` | `POST /v1/favourites` | Replace the current user’s favourite activity ID list. |

### Media files

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list or search media files` | `GET /v1/media_files` | List/search media-file metadata, optionally filtered by URI. |
| `add media file` | `POST /v1/media_files` | Add media-file metadata or upload base64 content through a data URI. |
| `read media file metadata` | `GET /v1/media_files/{id}` | Retrieve media-file metadata by ID. |
| `update media file metadata` | `PUT /v1/media_files/{id}` | Update media-file metadata. |
| `force-delete media file` | `DELETE /v1/media_files/{id}` | Delete a media file without checking whether activities reference it. |
| `delete unused media file` | `DELETE /v1/media_files/{id}` | Delete a media file only if it is unused, with `verify_unused=true`. |
| `download media file content` | `GET /v1/media_files/{id}/file` | Download original binary media content. |
| `download resized image media file` | `GET /v1/media_files/{id}/file` | Download image media resized to a requested maximum size. |

### System messages and system metadata

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list or search system messages` | `GET /v1/system_messages` | List/filter system messages by key and validity. |
| `create system message` | `POST /v1/system_messages` | Create an operational/system message. |
| `read system message` | `GET /v1/system_messages/{id}` | Retrieve one system message. |
| `update system message` | `PUT /v1/system_messages/{id}` | Update one system message. |
| `delete system message` | `DELETE /v1/system_messages/{id}` | Delete one system message. |
| `check system health` | `GET /v1/system/ping` | Check that the API responds. |
| `list roles and permission levels` | `GET /v1/system/roles` | Return role and permission-level metadata. |

### Tags/categories

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list or search tags/categories` | `GET /v1/categories`, `GET /v2/tags` | List/search tags/categories by group, name, and minimum activity count. |
| `create tag/category` | `POST /v1/categories`, `POST /v2/tags` | Create a tag/category. |
| `read tag/category` | `GET /v1/categories/{id}`, `GET /v2/tags/{id}` | Retrieve one tag/category. |
| `update tag/category` | `PUT /v1/categories/{id}`, `PUT /v2/tags/{id}` | Update a tag/category. |
| `delete tag/category` | `DELETE /v1/categories/{id}`, `DELETE /v2/tags/{id}` | Delete a tag/category. |

### Users and profile

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list or search users` | `GET /v1/users` | List/search users by name. |
| `create user` | `POST /v1/users` | Create a user with identities and authorization level. |
| `read user` | `GET /v1/users/{id}` | Retrieve one user. |
| `update user` | `PUT /v1/users/{id}` | Update a user. |
| `delete user` | `DELETE /v1/users/{id}` | Delete a user. |
| `retrieve authenticated user profile` | `GET /v1/users/profile` | Return the current authenticated user’s profile, role, and permissions. |

## Supported Business Behaviors

### Behavior 1: Discover Activities In The Catalog

Business goal:
Find activities that match planning needs, retrieve known activity IDs, obtain random suggestions, or inspect globally popular activities.

Domain context:
Activity discovery is the core catalog behavior. It lets clients search by name/text/category, age, participants, duration, featured status, rating thresholds, explicit IDs, random sampling, and global favourite ranking.

Starting point:
No prior service state. The workflow can return an empty list if no matching activities exist.

Required execution workflow:
1. Use function `search activities` (`GET /v1/activities`) with query `name={activityName}`, `text={wordsOrMinusExcludedWords}`, `featured={true|false}`, `categories={categoryNamesOrIds}`, `age_1={minAge}`, `age_2={maxAge}`, `participants={range}`, `time_1={minMinutes}`, `time_2={maxMinutes}`, `ratings_count_min={count}`, `ratings_average_min={average}`, and optional `attrs={fields}` to search v1 activities.
2. Alternatively, use function `search activities` (`GET /v2/activities`) with query `ages={ageRange}`, `durations={durationRange}`, and the other v2-compatible filters to search with v2 query names.
3. Use function `retrieve activities by identifier list` (`GET /v1/activities` or `GET /v2/activities`) with query `id={activityId}` or `id={activityId1},{activityId2}` when exact IDs are already known.
4. Use function `retrieve random activities` (`GET /v1/activities` or `GET /v2/activities`) with query `random={count}` plus compatible search filters to obtain random matching suggestions.
5. Use function `list overall favourite activities` (`GET /v1/activities` or `GET /v2/activities`) with query `favourites={count}` to retrieve globally favourite activities.

Optional verification workflow:
None. These functions are themselves read/inspection operations.

Existing-state shortcuts:
- If activities already exist through seed data or direct database setup, only the desired read/search step is needed.
- If exact activity IDs are already known from a previous `create activity` response or database fixture, the ID-list retrieval step can be used directly.
- If no non-empty result is required, all setup can be skipped and empty results are valid.

Parameter and value bindings:
- The same generated `activityId` returned by `create activity` can be reused as the `id` query value in `retrieve activities by identifier list`.
- v1 age/time filters use `age_1`, `age_2`, `time_1`, and `time_2`; v2 uses `ages` and `durations`.
- `favourites={count}` is documented as exclusive with other filters; combining it with ordinary filters violates the advertised query contract.
- `my_favourites={true|false}` is declared in Swagger and `full-behavior.md`, but implementation tests show it raises `500` with “API currently does not support filtering on your own favourites.”

Business result:
The service returns an activity collection, a random subset, an explicit ID-filtered collection, or a globally favourite-ranked collection. No catalog state is changed.

Constraints and invariants:
- Search filters must be parseable in the version-specific format; generated tests show malformed numeric/range values can produce `400`.
- `favourites` should not be combined with other filters according to Swagger.
- `my_favourites` is not a reliable supported implementation behavior despite being documented.
- Search results depend on derived rating/favourite/tag counters that may be stale if underlying rows are changed outside normal workflows.

Failure and exceptional cases:
- Failing function: `search activities`
  - Failure condition: malformed numeric or range query such as non-integer participant/time values.
  - Why it fails: implementation returns a `400` “Could not understand the request” style error for unparseable query values.
  - Violated prerequisite or constraint: query values must match expected numeric/range formats.
- Failing function: `list overall favourite activities`
  - Failure condition: `favourites={count}` is combined with other filters.
  - Why it fails: Swagger documents `favourites` as mutually exclusive with other filters.
  - Violated prerequisite or constraint: favourite ranking must be requested as its own mode.
- Failing function: `list current user's favourite activities`
  - Failure condition: request includes `my_favourites=true` or `my_favourites=false`.
  - Why it fails: implementation tests repeatedly show `500` with “API currently does not support filtering on your own favourites.”
  - Violated prerequisite or constraint: no valid request composition can make this documented filter work in the tested implementation.

Implementation notes:
The implementation priority differs from Swagger for `my_favourites`: it is exposed as a query parameter and described as a function, but behaves as unsupported. Global favourite ranking and explicit ID retrieval remain separate catalog read modes.

### Behavior 2: Create And Maintain Activity Content

Business goal:
Create an activity, retrieve it, replace all properties, patch selected properties, and delete it when it should no longer be available.

Domain context:
Activities are the primary business object. Their `ActivityProperties` include name, publication dates, descriptions, age/participant/time ranges, featured status, source, tags, media files, author, and revision history.

Starting point:
No prior activity state.

Required execution workflow:
1. Use function `create activity` (`POST /v1/activities`) with body `ActivityProperties{name="Fire building", description_main="...", age_min=10, age_max=15, participants_min=4, participants_max=12, time_min=30, time_max=60, featured=false}` to create the activity and capture generated response field `id={activityId}`.
2. To fully replace the activity, use function `replace activity` (`PUT /v1/activities/{id}`) with path `id={activityId}` and a complete `ActivityProperties` body containing every property that should remain.
3. To partially change only selected fields, use function `partially update activity` (`PATCH /v1/activities/{id}`) with path `id={activityId}` and body `ActivityProperties{description_notes="Updated note"}`.
4. To remove the activity, use function `delete activity` (`DELETE /v1/activities/{id}`) with path `id={activityId}`.

Optional verification workflow:
1. Use function `read activity` (`GET /v1/activities/{id}`) with path `id={activityId}` and optional query `attrs={fields}` after create, replace, or patch.
2. Use function `retrieve activities by identifier list` (`GET /v1/activities`) with query `id={activityId}` to inspect collection-level visibility.
3. After delete, use function `read activity` (`GET /v1/activities/{id}`) with path `id={activityId}` to confirm it no longer resolves.

Existing-state shortcuts:
- If an activity already exists, skip `create activity` and reuse its existing `id` for read, replace, patch, or delete.
- Direct database setup may create `ACTIVITY` and `ACTIVITY_PROPERTIES` rows, but the same generated or known `activityId` must still be used in all path/query values.
- The core action being analyzed cannot be skipped: replacement, patch, or deletion must still call the corresponding function.

Parameter and value bindings:
- The generated `id` from `create activity` is reused as `{id}` in `read activity`, `replace activity`, `partially update activity`, and `delete activity`.
- `PUT` consumes a complete replacement body; omitted properties are cleared by domain design.
- `PATCH` consumes only changed fields and preserves unspecified properties.
- v1 and v2 endpoints share the same activity identity model; the same `activityId` may be used through either version if the backing activity exists.

Business result:
A new activity exists after creation; replacement changes the current activity properties and clears omitted fields; patch changes only supplied fields; deletion removes the activity from direct lookup and catalog search.

Constraints and invariants:
- `{id}` must identify an existing activity for read, replace, patch, and delete.
- Swagger marks request bodies optional, but malformed JSON produces `400`, and invalid nested object graphs can produce implementation `500`s.
- Permission constants indicate `activity_create`, `activity_edit_own`, and `activity_edit`, but Swagger does not document the security contract.
- Generated tests show role authorization exists in practice and unauthenticated or under-authorized requests can return `401` or `403`.

Failure and exceptional cases:
- Failing function: `create activity`
  - Failure condition: body contains malformed JSON or invalid nested activity/media/tag structures.
  - Why it fails: tests show `400` for unprocessable JSON and `500` in model/DAO paths such as activity publication or nested reference initialization.
  - Violated prerequisite or constraint: request body must be parseable and nested references must be valid for persistence.
- Failing function: `read activity`
  - Failure condition: path `id={missingActivityId}`.
  - Why it fails: the lookup targets a non-existing activity.
  - Violated prerequisite or constraint: `{id}` must identify an existing activity.
- Failing function: `replace activity`
  - Failure condition: path `id={missingActivityId}` or invalid replacement body.
  - Why it fails: update targets a missing row or cannot persist invalid properties.
  - Violated prerequisite or constraint: existing activity and valid replacement body.
- Failing function: `partially update activity`
  - Failure condition: path `id={missingActivityId}`.
  - Why it fails: patch has no target activity.
  - Violated prerequisite or constraint: existing activity.
- Failing function: `delete activity`
  - Failure condition: path `id={missingActivityId}`.
  - Why it fails: delete targets a non-existing activity.
  - Violated prerequisite or constraint: existing activity.

Implementation notes:
The activity model stores property revisions. OpenAPI advertises optional bodies, but actual persistence is stricter than the schema implies. Some invalid states lead to server errors rather than clean validation errors.

### Behavior 3: Publish A Media-Rich, Categorized Activity

Business goal:
Create reusable media and taxonomy records, then publish an activity that references those domain objects.

Domain context:
A complete activity often needs categorization and media assets. The service exposes media and tag/category resources separately, and activity properties can embed `tags` and `media_files`.

Starting point:
No prior catalog, media, or tag state.

Required execution workflow:
1. Use function `add media file` (`POST /v1/media_files`) with body `MediaFile{uri="https://example.org/photo.jpg", mime_type="image/jpeg", name="Camp photo", author="Uploader"}` and capture response `id={mediaFileId}`.
2. Use function `create tag/category` (`POST /v1/categories`) with body `Tag{group="skills", name="fire", media_file={id={mediaFileId}}}` and capture response `id={tagId}`.
3. Use function `create activity` (`POST /v1/activities`) with body `ActivityProperties{name="Fire craft", tags=[{id={tagId}}], media_files=[{id={mediaFileId}}], description_main="...", age_min=10, time_min=30}` and capture response `id={activityId}`.

Optional verification workflow:
1. Use function `read media file metadata` (`GET /v1/media_files/{id}`) with path `id={mediaFileId}`.
2. Use function `read tag/category` (`GET /v1/categories/{id}`) with path `id={tagId}`.
3. Use function `read activity` (`GET /v1/activities/{id}`) with path `id={activityId}` and `attrs={fields}`.
4. Use function `search activities` (`GET /v1/activities`) with query `categories=fire` to inspect category-based discovery.

Existing-state shortcuts:
- If equivalent media already exists, skip `add media file` and reuse its `mediaFileId`.
- If equivalent tag/category already exists, skip `create tag/category` and reuse its `tagId`.
- Direct database setup can create `MEDIA_FILE`, `TAG`, and activity relation rows, but referenced IDs must exist before activity creation.
- The final `create activity` step cannot be skipped for this behavior.

Parameter and value bindings:
- `mediaFileId` from `add media file` is reused in `Tag.media_file.id` and `ActivityProperties.media_files[].id`.
- `tagId` from `create tag/category` is reused in `ActivityProperties.tags[].id`.
- `activityId` from `create activity` binds all later read, update, rating, favourite, or delete operations.
- If v2 is used, `create tag/category` maps to `POST /v2/tags`, but the `Tag` schema and generated ID are the same domain concept.

Business result:
A media record exists, a tag/category exists, and an activity exists with references to both. The activity can be retrieved and may be discoverable by category/tag filters.

Constraints and invariants:
- Referenced media/tag IDs must exist and be valid for the activity persistence path.
- The service does not expose a dedicated attach/detach endpoint; relationships are established through activity create/replace/patch bodies.
- Activity creation with malformed nested media/tag values can fail with implementation `500` rather than a clean domain validation response.
- Deleting referenced media is possible with `force-delete media file`, so activity-media consistency is not fully protected unless `verify_unused=true` is used on media deletion.

Failure and exceptional cases:
- Failing function: `add media file`
  - Failure condition: malformed `MediaFile` body or invalid data URI/content handling.
  - Why it fails: tests show media create paths can return server errors for invalid payloads.
  - Violated prerequisite or constraint: valid media metadata or upload representation.
- Failing function: `create tag/category`
  - Failure condition: null/malformed body or invalid embedded `media_file`.
  - Why it fails: tests show `TagDao.create` and resource create paths can produce `500`.
  - Violated prerequisite or constraint: valid tag body and referenced media state.
- Failing function: `create activity`
  - Failure condition: `tags[].id` or `media_files[].id` points to missing or invalid referenced records.
  - Why it fails: DAO initialization of tags/media expects resolvable records.
  - Violated prerequisite or constraint: activity child references must bind to existing tag/media rows.

Implementation notes:
The service uses embedded object representations for relationships but does not provide explicit relationship-management endpoints. This makes delete-and-replace workflows possible but weakens validation clarity.

### Behavior 4: Manage A User’s Rating And Favourite Flag For An Activity

Business goal:
Let an authenticated user rate an activity, mark it as favourite, inspect that personal state, and remove it.

Domain context:
Ratings and favourites are user-specific engagement signals. They feed activity rating/favourite counters and support personal favourite ID listing.

Starting point:
No prior activity/rating state. A user identity must be available; it can be created through the user API if permitted, supplied by seed data, or established by valid Google authentication.

Required execution workflow:
1. Use function `create user` (`POST /v1/users`) with body `User{name="User One", email_address="u1@example.org", authorization_level=0, identities=[{type="API", value="api-key-u1"}]}` to create the authenticated user and capture `id={userId}`. If user creation authorization is enforced, perform this with an existing administrator credential.
2. Use function `create activity` (`POST /v1/activities`) with body `ActivityProperties{name="Rated activity"}` and capture `id={activityId}`.
3. Use function `set own activity rating or favourite flag` (`POST /v1/activities/{id}/rating`) with path `id={activityId}`, header `Authorization=ApiKey api-key-u1`, and body `ActivityRatingAttrs{rating=4, favourite=true}`.
4. To remove the personal rating/favourite state, use function `remove own activity rating` (`DELETE /v1/activities/{id}/rating`) with path `id={activityId}` and header `Authorization=ApiKey api-key-u1`.

Optional verification workflow:
1. Use function `read own activity rating` (`GET /v1/activities/{id}/rating`) with path `id={activityId}`, header `Authorization=ApiKey api-key-u1`, and optional `attrs={fields}` after setting.
2. Use function `list favourite activity ids` (`GET /v1/favourites`) with header `Authorization=ApiKey api-key-u1` to inspect favourite IDs.
3. Use function `read activity` (`GET /v1/activities/{id}`) with path `id={activityId}` to inspect aggregate rating/favourite counters if those attributes are included.

Existing-state shortcuts:
- If a user with `UserIdentity{type=API,value="api-key-u1"}` already exists, skip `create user`.
- If an activity already exists, skip `create activity` and reuse its `activityId`.
- Direct database setup may create `USERS`, `USER_IDENTITY`, `ACTIVITY`, and `ACTIVITY_RATING`, but the authenticated credential must still resolve to the same user that owns the rating row.
- The set/read/remove rating action itself cannot be skipped.

Parameter and value bindings:
- `User.identities[].value="api-key-u1"` is reused as the credential in `Authorization=ApiKey api-key-u1`.
- `activityId` from `create activity` is reused as path `{id}` in rating functions.
- The same authenticated user must be used for `set own activity rating or favourite flag`, `read own activity rating`, `remove own activity rating`, and `list favourite activity ids`.
- The rating/favourite row is scoped by both `userId` and `activityId`.

Business result:
After setting, the authenticated user has a personal rating/favourite state for the activity. After removal, that user-specific rating/favourite state no longer exists. Aggregate counters may change according to persistence logic.

Constraints and invariants:
- A valid authenticated principal is required.
- `{id}` must identify an existing activity.
- The rating belongs only to the current user; another user cannot read or delete it as their own.
- Swagger does not document credential transport, but tests and source show `Authorization: ApiKey {value}` and authenticators for API keys and Google tokens.

Failure and exceptional cases:
- Failing function: `create user`
  - Failure condition: caller lacks permission or tries to assign an authorization level above its own.
  - Why it fails: implementation enforces authorization level checks and returns `403` for excessive role assignment.
  - Violated prerequisite or constraint: caller must be allowed to create/assign the requested user role.
- Failing function: `set own activity rating or favourite flag`
  - Failure condition: missing/invalid credentials.
  - Why it fails: `ApiKeyAuthenticator` returns empty unless an `API` identity value matches; protected endpoints can return `401`.
  - Violated prerequisite or constraint: current-user authentication.
- Failing function: `set own activity rating or favourite flag`
  - Failure condition: path `id={missingActivityId}`.
  - Why it fails: rating key construction and persistence require an existing activity.
  - Violated prerequisite or constraint: rating target activity must exist.
- Failing function: `read own activity rating`
  - Failure condition: no rating row exists for the authenticated user/activity pair.
  - Why it fails: read targets user-specific state that has not been created or was deleted.
  - Violated prerequisite or constraint: same-user rating state must exist.
- Failing function: `remove own activity rating`
  - Failure condition: credentials identify a different user than the rating owner.
  - Why it fails: delete is scoped to current user.
  - Violated prerequisite or constraint: same-user ownership.

Implementation notes:
Generated tests show invalid or missing rating key inputs can result in server errors in `ActivityRating.Key`. This is stricter and less graceful than the OpenAPI schema suggests.

### Behavior 5: Replace And Inspect A User’s Favourite Activity ID List

Business goal:
Replace the authenticated user’s favourites as a list of activity IDs, then retrieve the resulting ID list.

Domain context:
The `/v1/favourites` resource is a compact user-content API for favourites. It manages favourite IDs rather than full activity objects.

Starting point:
No prior favourite state. A user identity and target activities must be established.

Required execution workflow:
1. Use function `create user` (`POST /v1/users`) with body `User{name="Favourite User", authorization_level=0, identities=[{type="API", value="fav-key"}]}` and capture `userId`. If authorization is enforced, use an existing administrator credential for this setup.
2. Use function `create activity` (`POST /v1/activities`) with body `ActivityProperties{name="Favourite A"}` and capture `id={activityId1}`.
3. Use function `create activity` (`POST /v1/activities`) with body `ActivityProperties{name="Favourite B"}` and capture `id={activityId2}`.
4. Use function `replace favourite activity id list` (`POST /v1/favourites`) with header `Authorization=ApiKey fav-key` and body `PutFavouritesEntity{id=[{activityId1},{activityId2}]}`.

Optional verification workflow:
1. Use function `list favourite activity ids` (`GET /v1/favourites`) with header `Authorization=ApiKey fav-key` to verify the stored favourite IDs.
2. Use function `read activity` (`GET /v1/activities/{id}`) with path `id={activityId1}` or `id={activityId2}` to inspect target activity existence.

Existing-state shortcuts:
- If an authenticated user already exists, skip `create user` and reuse its API key.
- If target activities already exist, skip the activity creation steps and reuse their IDs.
- Direct database setup may create user, identity, activities, and favourite/rating rows, but the same user/activity bindings must hold.
- Replacing the favourite list cannot be skipped for this behavior.

Parameter and value bindings:
- `fav-key` from `User.identities[].value` is reused in the `Authorization` header.
- `activityId1` and `activityId2` from activity creation are reused in `PutFavouritesEntity.id[]`.
- `GET /v1/favourites` must authenticate as the same user whose list was replaced.
- Swagger incorrectly declares a required path parameter `id` for `/v1/favourites`; there is no `{id}` path segment, so no path value can be supplied.

Business result:
The current user’s favourite list is replaced by the supplied activity IDs that resolve to valid favourite relationships. Existing favourite state not in the replacement body is removed.

Constraints and invariants:
- The operation is user-scoped.
- Target IDs should identify existing activities; tests suggest nonexistent IDs may be accepted with `204` but not produce returned favourite IDs.
- There is no endpoint to append/remove a single favourite ID; replacement is the only list-level write.
- Full favourite activity objects through `my_favourites` are not reliably supported.

Failure and exceptional cases:
- Failing function: `replace favourite activity id list`
  - Failure condition: missing/invalid credentials.
  - Why it fails: favourite list mutation requires a current user.
  - Violated prerequisite or constraint: authenticated user.
- Failing function: `replace favourite activity id list`
  - Failure condition: body is malformed or contains invalid structures.
  - Why it fails: JSON binding/persistence cannot process the payload; tests show `400` for invalid JSON and some `500` paths in the favourites resource.
  - Violated prerequisite or constraint: valid `PutFavouritesEntity{id=[...]}` body.
- Failing function: `list favourite activity ids`
  - Failure condition: request omits valid credentials.
  - Why it fails: the favourite list is current-user content.
  - Violated prerequisite or constraint: authenticated user.

Implementation notes:
`POST /v1/favourites` is a replacement operation despite the operationId being `put` in Swagger. The OpenAPI path-parameter declaration for `/v1/favourites` is wrong.

### Behavior 6: Manage Media File Metadata And Content Delivery

Business goal:
Add media metadata or uploaded content, search media records, update metadata, and download original or resized content.

Domain context:
Media files support activity illustrations and tag/category images. They can be URL-backed or uploaded as data URIs.

Starting point:
No prior media state.

Required execution workflow:
1. Use function `add media file` (`POST /v1/media_files`) with body `MediaFile{uri="data:image/png;base64,{base64Content}", mime_type="image/png", name="Knot image"}` or `MediaFile{uri="https://example.org/knot.png", mime_type="image/png", name="Knot image"}` and capture generated `id={mediaFileId}`.
2. Use function `update media file metadata` (`PUT /v1/media_files/{id}`) with path `id={mediaFileId}` and body `MediaFile{uri="https://example.org/knot-v2.png", mime_type="image/png", name="Updated knot image"}` when metadata must change.
3. Use function `download media file content` (`GET /v1/media_files/{id}/file`) with path `id={mediaFileId}` to download original content.
4. For images, use function `download resized image media file` (`GET /v1/media_files/{id}/file`) with path `id={mediaFileId}` and query `size=512` to download a resized image.

Optional verification workflow:
1. Use function `read media file metadata` (`GET /v1/media_files/{id}`) with path `id={mediaFileId}` and optional `attrs={fields}`.
2. Use function `list or search media files` (`GET /v1/media_files`) with query `uri={substring}` and optional `attrs={fields}`.

Existing-state shortcuts:
- If a media record already exists, skip `add media file` and reuse `mediaFileId`.
- Direct database setup may create `MEDIA_FILE` rows, but downloadable content still depends on a valid stored or resolvable `uri`.
- The download or update step being analyzed cannot be skipped.

Parameter and value bindings:
- `mediaFileId` from `add media file` is reused as `{id}` for read, update, delete, and download functions.
- `uri` supplied during creation is later searchable through `list or search media files` with query `uri={substring}`.
- `size={integer}` is consumed only by resized image download and is rounded up to a power-of-two size by the documented behavior.

Business result:
A media record exists with metadata and optional content backing. Metadata may be changed. Original or resized binary content is returned for download without changing persisted state.

Constraints and invariants:
- `{id}` must identify an existing media file for metadata read/update/download.
- Resizing applies to images and should not enlarge images.
- `GET /v1/media_files/{id}/file` produces `application/octet-stream`.
- Download may fail if the URI/content is missing, invalid, or not resolvable.

Failure and exceptional cases:
- Failing function: `read media file metadata`
  - Failure condition: path `id={missingMediaFileId}`.
  - Why it fails: metadata lookup has no target row.
  - Violated prerequisite or constraint: existing media file.
- Failing function: `update media file metadata`
  - Failure condition: path `id={missingMediaFileId}` or malformed body.
  - Why it fails: update targets missing media or cannot bind/persist invalid payload.
  - Violated prerequisite or constraint: existing media and valid body.
- Failing function: `download media file content`
  - Failure condition: path `id={missingMediaFileId}` or media has no usable downloadable content/URI.
  - Why it fails: file delivery requires both metadata and retrievable content.
  - Violated prerequisite or constraint: existing downloadable media.
- Failing function: `download resized image media file`
  - Failure condition: media is not an image or `size` is invalid.
  - Why it fails: resizing assumes image content and a valid integer size; Swagger does not fully specify non-image behavior.
  - Violated prerequisite or constraint: image media and valid `size`.

Implementation notes:
Tests show `MediaFileResource.downloadFile` can return `500` for invalid or unavailable content, so media download errors are not always cleanly mapped to domain-specific responses.

### Behavior 7: Delete Media According To Reference Policy

Business goal:
Either force-delete media regardless of references or safely delete only media that no activity uses.

Domain context:
Media can be referenced by activities. The API exposes two deletion modes through the `verify_unused` query parameter.

Starting point:
No prior media state for unused deletion; for force-delete with a meaningful referenced case, a media file and referencing activity must be established.

Required execution workflow:
1. Use function `add media file` (`POST /v1/media_files`) with body `MediaFile{uri="https://example.org/remove.jpg"}` and capture `id={mediaFileId}`.
2. For safe unused deletion, use function `delete unused media file` (`DELETE /v1/media_files/{id}`) with path `id={mediaFileId}` and query `verify_unused=true`.
3. For force deletion, if a referenced-media case is needed, first use function `create activity` (`POST /v1/activities`) with body `ActivityProperties{name="Media owner", media_files=[{id={mediaFileId}}]}` and capture `activityId`.
4. Use function `force-delete media file` (`DELETE /v1/media_files/{id}`) with path `id={mediaFileId}` and query `verify_unused=false` or no `verify_unused` query.

Optional verification workflow:
1. Use function `read media file metadata` (`GET /v1/media_files/{id}`) with path `id={mediaFileId}` to confirm deletion.
2. Use function `read activity` (`GET /v1/activities/{id}`) with path `id={activityId}` after force-delete to inspect whether the activity still exists and how media references are represented.

Existing-state shortcuts:
- If an unused media file already exists, skip `add media file` for safe deletion.
- If referenced media already exists, skip media/activity setup for force-delete.
- Direct database setup may create `MEDIA_FILE` and activity-media relationship rows.
- The delete call itself cannot be skipped.

Parameter and value bindings:
- `mediaFileId` from `add media file` is reused as path `{id}` in both delete modes.
- For referenced-media setup, the same `mediaFileId` is reused inside `ActivityProperties.media_files[].id`.
- `verify_unused=true` selects safe deletion; absent or `false` selects force deletion.

Business result:
Safe deletion removes the media only when no activity references it. Force deletion removes the media even if activities reference it, potentially leaving activity content without the media record.

Constraints and invariants:
- `{id}` must identify an existing media file.
- Safe deletion requires no activity-media references.
- Force deletion intentionally bypasses reference protection.
- The API does not expose a separate “detach media from activity” endpoint before safe deletion.

Failure and exceptional cases:
- Failing function: `delete unused media file`
  - Failure condition: an activity references `mediaFileId` and request uses `verify_unused=true`.
  - Why it fails: documented safe deletion verifies the media is unused.
  - Violated prerequisite or constraint: unused-media requirement.
- Failing function: `force-delete media file`
  - Failure condition: path `id={missingMediaFileId}`.
  - Why it fails: delete target does not exist.
  - Violated prerequisite or constraint: existing media file.
- Failing function: `create activity`
  - Failure condition: setup activity references a missing or invalid media ID.
  - Why it fails: activity media initialization requires resolvable media.
  - Violated prerequisite or constraint: referenced media must exist.

Implementation notes:
The default delete behavior is destructive from a referential perspective: it deletes even when activities reference the media unless `verify_unused=true` is provided.

### Behavior 8: Manage System Messages

Business goal:
Create, filter, retrieve, update, and delete system messages used for operational announcements or configuration-like text.

Domain context:
System messages have keys, values, and validity windows. They are separate from activity content and likely intended for client-facing notices.

Starting point:
No prior system-message state.

Required execution workflow:
1. Use function `create system message` (`POST /v1/system_messages`) with body `SystemMessage{key="maintenance", value="Maintenance tonight", valid_from="2026-05-27T00:00:00Z", valid_to="2026-05-28T00:00:00Z"}` and capture `id={systemMessageId}`.
2. Use function `update system message` (`PUT /v1/system_messages/{id}`) with path `id={systemMessageId}` and body `SystemMessage{key="maintenance", value="Updated message", valid_from="2026-05-27T00:00:00Z", valid_to="2026-05-29T00:00:00Z"}` when the message changes.
3. Use function `delete system message` (`DELETE /v1/system_messages/{id}`) with path `id={systemMessageId}` when the message should be removed.

Optional verification workflow:
1. Use function `read system message` (`GET /v1/system_messages/{id}`) with path `id={systemMessageId}` and optional `attrs={fields}`.
2. Use function `list or search system messages` (`GET /v1/system_messages`) with query `key=maintenance`, `valid={timestampOrFlagAsAcceptedByImplementation}`, and optional `attrs={fields}`.

Existing-state shortcuts:
- If a system message already exists, skip `create system message` and reuse `systemMessageId`.
- Direct database setup can create a `SYSTEM_MESSAGE` row; the same ID must be reused for read/update/delete.
- The update or delete action cannot be skipped for that behavior.

Parameter and value bindings:
- `systemMessageId` returned by creation is reused as path `{id}` for read, update, and delete.
- `key` supplied in creation/update can be reused as the `key` query in `list or search system messages`.
- `valid_from` and `valid_to` define validity filtering semantics; exact `valid` query format is not fully documented by the schema.

Business result:
A system message is created, can be changed, can be filtered by key/validity, and can be deleted. After deletion, the message should no longer be returned by read or list calls.

Constraints and invariants:
- `{id}` must identify an existing system message for read/update/delete.
- Permission constants indicate `system_message_read` for broad reading and `system_message_manage` for administrator-level management.
- Swagger does not document required fields or authorization, but implementation tests show protected management calls can return `403`.

Failure and exceptional cases:
- Failing function: `create system message`
  - Failure condition: malformed body or missing fields that the DAO expects.
  - Why it fails: tests show `SystemMessageDao.create` can return `500` for invalid payloads.
  - Violated prerequisite or constraint: valid system message body.
- Failing function: `read system message`
  - Failure condition: path `id={missingSystemMessageId}`.
  - Why it fails: lookup has no target row.
  - Violated prerequisite or constraint: existing message.
- Failing function: `update system message`
  - Failure condition: path `id={missingSystemMessageId}` or invalid body.
  - Why it fails: update target is missing or cannot be persisted.
  - Violated prerequisite or constraint: existing message and valid update body.
- Failing function: `delete system message`
  - Failure condition: path `id={missingSystemMessageId}`.
  - Why it fails: delete target is missing.
  - Violated prerequisite or constraint: existing message.

Implementation notes:
System message management is more protected in implementation than Swagger shows. Invalid management payloads sometimes produce server errors rather than validation responses.

### Behavior 9: Manage Tags And Categories

Business goal:
Create, search, retrieve, update, and delete taxonomy labels used to classify activities.

Domain context:
v1 calls them categories and v2 calls them tags, but both use the `Tag` schema with `group`, `name`, optional `media_file`, and `activities_count`.

Starting point:
No prior tag/category state.

Required execution workflow:
1. Use function `create tag/category` (`POST /v1/categories`) with body `Tag{group="activity_type", name="outdoor"}` and capture `id={tagId}`.
2. Use function `update tag/category` (`PUT /v1/categories/{id}`) with path `id={tagId}` and body `Tag{group="activity_type", name="outdoor-updated"}` when the taxonomy value changes.
3. Use function `delete tag/category` (`DELETE /v1/categories/{id}`) with path `id={tagId}` when the taxonomy value should be removed.

Optional verification workflow:
1. Use function `read tag/category` (`GET /v1/categories/{id}` or `GET /v2/tags/{id}`) with path `id={tagId}` and optional `attrs={fields}`.
2. Use function `list or search tags/categories` (`GET /v1/categories` or `GET /v2/tags`) with query `group=activity_type`, `name=outdoor`, `min_activities_count={count}`, and optional `attrs={fields}`.

Existing-state shortcuts:
- If a tag/category already exists, skip `create tag/category` and reuse `tagId`.
- Direct database setup can create a `TAG` row and optional `TAG_DERIVED` row.
- The update or delete action cannot be skipped for that behavior.

Parameter and value bindings:
- `tagId` from creation is reused as path `{id}` for read/update/delete through either `/v1/categories/{id}` or `/v2/tags/{id}`.
- `group` and `name` supplied in the body are reused as search query filters.
- If `media_file.id` is supplied in the tag body, it must bind to a media file created by `add media file` or equivalent setup.
- `min_activities_count` filters by derived `activities_count`, not by manually supplied body value.

Business result:
A taxonomy entry exists, can be listed/searched, can be changed, and can be removed. Activity classification can later reference the tag/category from activity properties.

Constraints and invariants:
- `{id}` must identify an existing tag/category for read/update/delete.
- Permission constants indicate moderator-level `category_create` and `category_edit`.
- The API does not expose uniqueness guarantees for `(group, name)`.
- Derived `activities_count` is read-oriented and may not automatically recompute after all external database changes.

Failure and exceptional cases:
- Failing function: `create tag/category`
  - Failure condition: null body, malformed body, or invalid embedded media file.
  - Why it fails: generated tests show `TagDao.create`, `CategoryResource.create`, and `TagResource.create` can produce `500`.
  - Violated prerequisite or constraint: valid tag/category body and valid media reference if supplied.
- Failing function: `read tag/category`
  - Failure condition: path `id={missingTagId}`.
  - Why it fails: lookup has no tag/category row.
  - Violated prerequisite or constraint: existing tag/category.
- Failing function: `update tag/category`
  - Failure condition: path `id={missingTagId}` or invalid body.
  - Why it fails: update target is missing or persistence fails.
  - Violated prerequisite or constraint: existing tag/category and valid body.
- Failing function: `delete tag/category`
  - Failure condition: path `id={missingTagId}` or referenced/dependency state blocks DAO deletion.
  - Why it fails: delete target is missing or cannot be removed cleanly.
  - Violated prerequisite or constraint: existing removable tag/category.

Implementation notes:
v1 category and v2 tag endpoints are aliases for the same domain model. Implementation errors around null bodies and DAO operations are observable in tests.

### Behavior 10: Manage Users And Retrieve Current Profile

Business goal:
Create users with identities, search/read/update/delete user records, and let a signed-in user inspect their role and permissions.

Domain context:
Users own authentication identities and authorization levels. API-key identities are used by the API authenticator; Google identities can create users and API keys when token verification succeeds.

Starting point:
No prior user state for basic creation, but administrative credentials may be required by the implementation for user management.

Required execution workflow:
1. Use function `create user` (`POST /v1/users`) with body `User{name="Admin-created User", email_address="person@example.org", authorization_level=0, identities=[{type="API", value="person-api-key"}]}` and capture `id={userId}`.
2. Use function `update user` (`PUT /v1/users/{id}`) with path `id={userId}` and body `User{name="Updated User", email_address="updated@example.org", authorization_level=0, identities=[{type="API", value="person-api-key"}]}` when profile/admin fields change.
3. Use function `retrieve authenticated user profile` (`GET /v1/users/profile`) with header `Authorization=ApiKey person-api-key` to inspect the authenticated user’s role and permissions.
4. Use function `delete user` (`DELETE /v1/users/{id}`) with path `id={userId}` when the user should be removed.

Optional verification workflow:
1. Use function `read user` (`GET /v1/users/{id}`) with path `id={userId}` and optional `attrs={fields}`.
2. Use function `list or search users` (`GET /v1/users`) with query `name=Updated User` and optional `attrs={fields}`.
3. Use function `list roles and permission levels` (`GET /v1/system/roles`) to inspect available role and permission levels.

Existing-state shortcuts:
- If a user already exists, skip `create user` and reuse `userId`.
- If an API identity already exists, reuse its `value` as the API-key credential.
- Direct database setup can create `USERS` and `USER_IDENTITY`, but the credential must match the stored identity value.
- For Google authentication, direct API setup can be skipped if a valid Google token is accepted and the authenticator creates the missing user/API key.

Parameter and value bindings:
- `userId` from `create user` is reused as `{id}` for read/update/delete.
- `User.identities[].value="person-api-key"` is reused as `Authorization=ApiKey person-api-key` for profile and other current-user functions.
- `authorization_level` maps to roles: limited user `-1`, user `0`, moderator `10`, administrator `20`.
- Swagger incorrectly declares a required path parameter `id` for `/v1/users/profile`; no path value exists.

Business result:
A user exists with identities and authorization level; the user can authenticate via API key; profile returns role/permission information; update changes user metadata; delete removes the user.

Constraints and invariants:
- `UserIdentity.type` is `API` or `GOOGLE`.
- API-key authentication resolves a user by `IdentityType.API` and exact credential value.
- Google authentication validates the token, may create a user, and adds an API key if missing.
- Implementation enforces role assignment limits: callers cannot set authorization level higher than their own.
- User management endpoints may require administrator privileges despite incomplete Swagger security documentation.

Failure and exceptional cases:
- Failing function: `create user`
  - Failure condition: caller tries to set `authorization_level` higher than allowed.
  - Why it fails: implementation authorization-level assertion rejects elevation above the caller’s role, often with `403`.
  - Violated prerequisite or constraint: caller must have sufficient authorization.
- Failing function: `retrieve authenticated user profile`
  - Failure condition: missing or invalid API key/Google token.
  - Why it fails: authenticators return no principal and protected endpoint returns `401`.
  - Violated prerequisite or constraint: valid current-user credential.
- Failing function: `read user`
  - Failure condition: path `id={missingUserId}`.
  - Why it fails: user lookup has no target row.
  - Violated prerequisite or constraint: existing user.
- Failing function: `update user`
  - Failure condition: path `id={missingUserId}` or forbidden role change.
  - Why it fails: target is missing or authorization-level rule is violated.
  - Violated prerequisite or constraint: existing user and allowed authorization level.
- Failing function: `delete user`
  - Failure condition: path `id={missingUserId}` or insufficient authorization.
  - Why it fails: target is missing or caller lacks user-edit permission.
  - Violated prerequisite or constraint: existing user and authorized caller.

Implementation notes:
Swagger omits security details, but tests show `401` for missing credentials and `403` for forbidden operations. The profile path has an erroneous path-parameter declaration.

### Behavior 11: Inspect System Health And Authorization Metadata

Business goal:
Check whether the service is alive and inspect configured roles and permission thresholds.

Domain context:
Operational clients and admin interfaces need status and authorization metadata.

Starting point:
No prior service resource state.

Required execution workflow:
1. Use function `check system health` (`GET /v1/system/ping`) to verify the API responds.
2. Use function `list roles and permission levels` (`GET /v1/system/roles`) to retrieve `permission_levels` and `role_levels`.

Optional verification workflow:
None. These functions are read/status operations.

Existing-state shortcuts:
- No setup is needed.
- If role metadata is statically known from source, the roles call may be skipped for client-side display, but the API behavior itself requires calling the function.

Parameter and value bindings:
- No path, query, body, form, or generated values are required.
- Returned role levels bind role names to authorization levels used by user-management rules.

Business result:
The caller receives a health response and metadata for roles and permissions, including levels such as limited user, user, moderator, and administrator.

Constraints and invariants:
- Source defines role levels `limited_user=-1`, `user=0`, `moderator=10`, and `administrator=20`.
- Source defines permissions such as `activity_create`, `rating_set_own`, `category_create`, `system_message_manage`, `auth_role_list`, and `auth_user_create`.
- `auth_role_list` is administrator-level in source, but Swagger does not document security for `GET /v1/system/roles`.

Failure and exceptional cases:
- Failing function: `check system health`
  - Failure condition: service is unavailable.
  - Why it fails: the API cannot answer the ping.
  - Violated prerequisite or constraint: running service.
- Failing function: `list roles and permission levels`
  - Failure condition: authorization enforcement rejects the caller.
  - Why it fails: source permission `auth_role_list` is administrator-level, although endpoint security is not visible in Swagger.
  - Violated prerequisite or constraint: caller may need administrator-level access.

Implementation notes:
This behavior exposes system metadata, not business resources. The role list is useful for interpreting `authorization_level` values on users.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Retrieve Full Current User Favourite Activity Objects

Priority:
Critical domain gap

Expected business goal:
A user should be able to retrieve full activity objects for their own favourites, not only raw favourite IDs.

Why it is unsupported:
`full-behavior.md` and Swagger expose `list current user's favourite activities` through `my_favourites`, but implementation tests show both v1 and v2 `GET /activities?my_favourites=...` return `500` with “API currently does not support filtering on your own favourites.”

Existing functions considered:
- `list current user's favourite activities`: documented closest match, but implementation rejects it.
- `list favourite activity ids`: returns IDs only, not activity objects.
- `retrieve activities by identifier list`: can retrieve objects only after the client already has IDs, but it does not perform the current-user favourite filter.
- `replace favourite activity id list`: writes favourite state but does not return full activity objects.

Missing capability:
A working `GET /v1/activities?my_favourites=true` or equivalent endpoint that joins current-user favourite state to activity retrieval.

Proof that function composition is insufficient:
A client can call `list favourite activity ids` and then `retrieve activities by identifier list`, but that is not equivalent to a supported server-side current-user favourite activity query: it requires client-side composition, loses atomicity, cannot guarantee a consistent snapshot, and fails if the `id` query cannot represent the complete list.

Evidence from existing functions/source:
- Function `list current user's favourite activities` is documented.
- Generated tests repeatedly observe implementation failure for `my_favourites`.
- Function `list favourite activity ids` works at ID level only.

Business impact:
Personalized activity screens are blocked or require brittle two-step client orchestration.

### Missing Behavior 2: Incrementally Add Or Remove One Favourite From The Favourite List

Priority:
Important robustness gap

Expected business goal:
A user should be able to favourite or unfavourite one activity without replacing the entire favourite list.

Why it is unsupported:
The list-level favourite API only replaces all favourite IDs. Rating endpoints can set `favourite` for one activity, but `/v1/favourites` has no append/remove-one operation.

Existing functions considered:
- `replace favourite activity id list`: replaces the whole list, so concurrent changes can be lost.
- `set own activity rating or favourite flag`: can set a single favourite flag but is coupled to rating state and requires an activity-specific endpoint.
- `remove own activity rating`: removes rating state, not just a favourite flag.
- `list favourite activity ids`: can read the list but cannot mutate it.

Missing capability:
Dedicated `POST /v1/favourites/{activityId}` and `DELETE /v1/favourites/{activityId}`, or a patch semantics for favourites.

Proof that function composition is insufficient:
Read-modify-write with `list favourite activity ids` plus `replace favourite activity id list` is not atomic. Another client update between the read and replacement can be overwritten. `remove own activity rating` is not equivalent because it removes the whole rating row, not only the favourite flag.

Evidence from existing functions/source:
The only `/v1/favourites` write function is `replace favourite activity id list`.

Business impact:
Favourite management is prone to lost updates and cannot cleanly express “toggle favourite” without affecting rating-related state.

### Missing Behavior 3: First-Class Activity Relationship Management

Priority:
Critical domain gap

Expected business goal:
Administrators or importers should be able to create, update, delete, and inspect related-activity relationships.

Why it is unsupported:
The `Activity` schema includes a read-only `related` field and tests show an `ACTIVITY_RELATION` table, but no OpenAPI function manages relationships.

Existing functions considered:
- `read activity`: may expose `related`.
- `create activity`, `replace activity`, `partially update activity`: activity bodies include nested `activity`, but no documented relationship field write exists.
- `search activities`: cannot create or modify relations.

Missing capability:
Endpoints such as `POST /v1/activities/{id}/related/{relatedId}`, `DELETE /v1/activities/{id}/related/{relatedId}`, and a relationship list/read endpoint.

Proof that function composition is insufficient:
No existing function accepts a relationship edge as a supported request value. Direct database insertion can create `ACTIVITY_RELATION`, but API clients cannot create, prevent duplicates, delete, or ownership-check relationships through the REST API.

Evidence from existing functions/source:
`Activity.related` exists in Swagger as read-only. Database reset lists `ACTIVITY_RELATION`. No path in Swagger exposes relationship mutation.

Business impact:
Related activity recommendations cannot be curated or repaired through the API.

### Missing Behavior 4: Safe Deletion Impact Analysis For Activities, Tags, Users, And System Messages

Priority:
Important robustness gap

Expected business goal:
Before deleting important resources, clients should be able to know what dependent state will be removed, orphaned, or invalidated.

Why it is unsupported:
Only media deletion has a `verify_unused` option. Activity, tag/category, user, and system-message delete functions have no dry-run, dependency check, or safe-delete mode.

Existing functions considered:
- `delete activity`: deletes by ID without a documented dependency check.
- `delete tag/category`: deletes by ID without a documented activity-reference check.
- `delete user`: deletes by ID without documented handling of ratings, identities, or authored activities.
- `delete unused media file`: provides this behavior only for media.

Missing capability:
Safe-delete or dependency-inspection endpoints for non-media resources.

Proof that function composition is insufficient:
Read/list functions can inspect some resources, but they cannot enumerate all dependent ratings, favourites, authorship, activity-tag links, or derived counters. Delete-and-recreate is not equivalent because original IDs, revisions, ownership, and derived state may be lost.

Evidence from existing functions/source:
Only `DELETE /v1/media_files/{id}` has query `verify_unused`.

Business impact:
Administrative cleanup is risky and may create orphaned or stale domain state.

### Missing Behavior 5: Recompute Or Validate Derived Counts And Ratings

Priority:
Important robustness gap

Expected business goal:
The system should recalculate and validate derived values such as `ratings_count`, `ratings_sum`, `ratings_average`, `favourites_count`, and tag `activities_count`.

Why it is unsupported:
Derived fields are visible in schemas and filters, but no endpoint recomputes or validates them.

Existing functions considered:
- `set own activity rating or favourite flag`: may update rating/favourite state for one user/activity.
- `read activity`: displays aggregate fields.
- `list or search tags/categories`: filters by `min_activities_count`.
- `replace favourite activity id list`: changes favourite relationships but does not expose recalculation controls.

Missing capability:
A derived-state rebuild endpoint or consistency validation endpoint.

Proof that function composition is insufficient:
Reading aggregates cannot correct stale values. Updating one rating cannot recompute all affected historical or externally modified rows. Direct database repair is not an API behavior and cannot be performed by normal clients.

Evidence from existing functions/source:
Swagger exposes derived fields but no recomputation path. Tests reset `ACTIVITY_DERIVED` and `TAG_DERIVED`, confirming derived tables exist.

Business impact:
Search ranking and filters can become stale or inconsistent after imports, direct database changes, failed writes, or partial failures.

### Missing Behavior 6: Public Authentication/Login And API-Key Rotation

Priority:
Critical domain gap

Expected business goal:
A user should be able to sign in, obtain an API key, rotate it, and revoke old credentials through documented API behavior.

Why it is unsupported:
Authentication is implemented in source but no Swagger endpoint documents login, token exchange, API-key issuance, rotation, or revocation.

Existing functions considered:
- `create user`: can create a user with identities if caller is authorized, but it is an admin/user-management operation, not login.
- `retrieve authenticated user profile`: verifies a credential but does not issue one.
- `update user`: could alter identities if authorized, but this is not a safe credential-rotation workflow.
- Google authentication source can create a user/API key internally, but no REST authentication exchange endpoint is documented.

Missing capability:
Documented login/token exchange and API-key lifecycle endpoints.

Proof that function composition is insufficient:
A user cannot obtain a valid API key from no credential state through any documented endpoint. Admin-created identities are not self-service login. Updating the whole user record is not equivalent to atomic key rotation with revocation and audit semantics.

Evidence from existing functions/source:
`ApiKeyAuthenticator` and `GoogleAuthenticator` exist in source; Swagger has no auth endpoint.

Business impact:
Client onboarding and credential management depend on out-of-band setup or undocumented authentication behavior.

### Missing Behavior 7: Server-Side Uniqueness And Duplicate Prevention For Tags, Media, Users, And Activities

Priority:
Important robustness gap

Expected business goal:
The API should prevent or detect duplicate business records such as duplicate tag `(group,name)`, duplicate media URI, duplicate API identity, or duplicate activity source IDs.

Why it is unsupported:
No function documents uniqueness checks or lookup-by-natural-key behavior beyond general search.

Existing functions considered:
- `list or search tags/categories`: can search by group/name but does not reserve uniqueness.
- `list or search media files`: can search by URI substring but not exact unique URI.
- `list or search users`: can search by name but not identity uniqueness.
- `create tag/category`, `add media file`, `create user`, `create activity`: create new records without documented idempotency or conflict responses.

Missing capability:
Unique constraints, conflict responses, exact lookup endpoints, or idempotent create semantics.

Proof that function composition is insufficient:
Searching before creating is race-prone and not equivalent to database-backed uniqueness. Delete-and-recreate changes IDs and breaks existing references.

Evidence from existing functions/source:
Swagger create operations return success without documented `409 Conflict` or natural-key constraints.

Business impact:
Catalog quality degrades through duplicates, and clients cannot safely synchronize imported data.

### Missing Behavior 8: Activity Import Or Source Synchronization Workflow

Priority:
API ergonomics gap

Expected business goal:
Because the service domain is mirrored from `www.aktivitetsbanken.se`, clients would expect import/sync behavior for external activities and sources.

Why it is unsupported:
Importer classes exist in coverage reports, and `ActivityProperties.source` exists, but no REST endpoint starts imports or reconciles external source records.

Existing functions considered:
- `create activity`: can manually create an activity with `source`.
- `replace activity` and `partially update activity`: can manually change activity properties.
- `search activities`: can search existing imported/manual data.
- `retrieve activities by identifier list`: only works with internal IDs, not external source IDs.

Missing capability:
Import/sync endpoint, source lookup, external ID mapping, conflict handling, and import status reporting.

Proof that function composition is insufficient:
Manual create/update cannot crawl, map, deduplicate, or reconcile external source data. Internal ID retrieval cannot locate a source record unless the client already knows the internal ID.

Evidence from existing functions/source:
Coverage includes activity importer classes; OpenAPI exposes no importer paths.

Business impact:
Mirroring must be run out-of-band, and API clients cannot trigger or monitor synchronization.

## Cross-Behavior Observations

- Swagger omits or under-documents security, while implementation tests show `401`, `403`, API-key authentication, Google authentication, role levels, and authorization-level constraints.
- `my_favourites` is documented but implementation returns `500`, making favourite activity-object retrieval unsupported.
- Swagger incorrectly declares required path parameter `id` on `/v1/favourites` and `/v1/users/profile`, both of which have no `{id}` path segment.
- Several schemas mark bodies optional, but malformed or null bodies often produce `400` or `500`; validation is weaker than the domain model requires.
- Media has a safe-delete option; most other resources do not.
- Activity, tag, and rating aggregate fields rely on derived state, but no recomputation endpoint exists.
- v1 categories and v2 tags are the same domain concept under different endpoint names.
- Relationship and import concepts appear in models/coverage but are not first-class REST behaviors.
- Direct database setup can establish many states, but API-realizable workflows require generated IDs and current-user credentials to be reused exactly across calls.

## Coverage Summary

Supported domain areas:
Activity search, activity CRUD, activity rating/favourite flag management, favourite ID replacement/listing, media CRUD/download, media safe/force deletion, system message CRUD, tag/category CRUD/search, user CRUD/profile, health, and role metadata.

Partially supported domain areas:
Personal favourites are only fully reliable as ID lists or per-activity rating flags; full favourite activity-object filtering is broken. Authorization is implemented but poorly represented in Swagger. Derived counters are readable but not explicitly maintainable.

Unsupported domain areas:
Documented current-user favourite activity search, incremental favourite list mutation, relationship management, broad safe-delete impact analysis, derived-state recomputation, documented login/API-key lifecycle, uniqueness/idempotent synchronization, and external activity import workflows.