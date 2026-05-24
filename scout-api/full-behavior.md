Paths below are the Swagger paths under basePath `/api`. I only used `src` and `scout-api.json`. The Swagger file documents the REST surface, but the allowed `src` directory contains only auth/permission code, not resource controllers; therefore exact status codes and many persistence failures are not visible. Where Swagger and source disagree, I call it out.

### Behavior 1: search activities

Behavior name:
search activities

Successful execution:
- Result:
  This behavior returns activities matching collection-level search filters.
- Endpoint sequence:
  Step 1: `GET /v1/activities`
  or
  Step 1: `GET /v2/activities`
- Constraints:
  `attrs` controls response attributes. `name` and `text` search activity fields and may use a leading minus to exclude words. `featured`, `participants`, `ratings_count_min`, `ratings_average_min`, and version-specific age/time filters narrow the collection. In v1, age/time filters are `age_1`, `age_2`, `time_1`, and `time_2`; in v2, they are `ages` and `durations`. This general search can succeed with an empty result set and does not require setup.

Failure or exceptional branches:
- No concrete failure branch is specified by the Swagger file or by the allowed `src` implementation for ordinary collection search.

Endpoint coverage:
- Covers:
  `GET /v1/activities`
- Distinct meaning:
  Search v1 activities using v1 query names.
- Covers:
  `GET /v2/activities`
- Distinct meaning:
  Search v2 activities using v2 query names.

### Behavior 2: retrieve activities by identifier list

Behavior name:
retrieve activities by identifier list

Successful execution:
- Result:
  This behavior returns activities whose internal ids match the comma-separated `id` query value.
- Endpoint sequence:
  Step 1: `POST /v1/activities`
  Step 2: `GET /v1/activities`
  or
  Step 1: `POST /v2/activities`
  Step 2: `GET /v2/activities`
- Constraints:
  The `id` returned by Step 1 must be reused in Step 2 as the `id` query value. Multiple ids are comma-separated. This is distinct from path-based single-activity lookup because the collection endpoint returns a filtered activity collection.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The requested id does not identify an existing activity.
  - Endpoint group:
    Step 1: `GET /v1/activities`
  - Failure endpoint:
    `GET /v1/activities`
  - Why this fails:
    The query asks for a specific internal activity id, but the prerequisite `POST /v1/activities` that would create that id was intentionally omitted. The Swagger does not expose the exact error/status behavior, so the visible failure may be an empty result rather than an error.
  - Intentionally violated constraints:
    The `id` query value was not obtained from a documented activity creation response.

Endpoint coverage:
- Covers:
  `GET /v1/activities`
- Distinct meaning:
  Retrieve activities by explicit internal ids through the v1 collection endpoint.
- Covers:
  `GET /v2/activities`
- Distinct meaning:
  Retrieve activities by explicit internal ids through the v2 collection endpoint.

### Behavior 3: retrieve random matching activities

Behavior name:
retrieve random activities

Successful execution:
- Result:
  This behavior returns a requested number of random activities matching any other allowed filters.
- Endpoint sequence:
  Step 1: `GET /v1/activities`
  or
  Step 1: `GET /v2/activities`
- Constraints:
  The `random` query parameter is an integer limiting the number of random activities. It may be combined with other ordinary filters according to the Swagger description.

Failure or exceptional branches:
- No concrete failure branch is specified beyond invalid query values, whose validation behavior is not visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /v1/activities`
- Distinct meaning:
  Random v1 activity selection.
- Covers:
  `GET /v2/activities`
- Distinct meaning:
  Random v2 activity selection.

### Behavior 4: list overall favourite activities

Behavior name:
list overall favourite activities

Successful execution:
- Result:
  This behavior returns activities sorted by how many users have marked them as favourites.
- Endpoint sequence:
  Step 1: `GET /v1/activities`
  or
  Step 1: `GET /v2/activities`
- Constraints:
  The `favourites` query parameter limits the number of overall favourite activities. Swagger explicitly says this parameter cannot be used together with any other filtering parameter.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `favourites` is used together with another filtering parameter.
  - Endpoint group:
    Step 1: `GET /v1/activities`
  - Failure endpoint:
    `GET /v1/activities`
  - Why this fails:
    The Swagger description says `favourites` cannot be combined with other filtering parameters.
  - Intentionally violated constraints:
    A request such as `favourites={count}` plus `name={text}` violates the documented exclusivity rule.

Endpoint coverage:
- Covers:
  `GET /v1/activities`
- Distinct meaning:
  List global favourite v1 activities.
- Covers:
  `GET /v2/activities`
- Distinct meaning:
  List global favourite v2 activities.

### Behavior 5: list current user's favourite activities

Behavior name:
list current user's favourite activities

Successful execution:
- Result:
  This behavior returns activity objects that the current API-key-authenticated user has marked as favourites.
- Endpoint sequence:
  Step 1: `POST /v1/activities`
  Step 2: `POST /v1/favourites`
  Step 3: `GET /v1/activities`
  or
  Step 1: `POST /v2/activities`
  Step 2: `POST /v1/favourites`
  Step 3: `GET /v2/activities`
- Constraints:
  Step 1 returns an activity `id`. Step 2 body `id` array must include that activity id. Step 3 must use `my_favourites=true`. The source shows API-key authentication resolves the current user by an `API` identity value; Swagger has no security definition, so the credential transport is not documented.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The current user cannot be authenticated.
  - Endpoint group:
    Step 1: `POST /v1/activities`
    Step 2: `POST /v1/favourites`
    Step 3: `GET /v1/activities`
  - Failure endpoint:
    `GET /v1/activities`
  - Why this fails:
    The Swagger says `my_favourites` is determined by the current user's API key, and `ApiKeyAuthenticator` returns no principal when no user has the supplied API identity.
  - Intentionally violated constraints:
    Step 3 omits a valid API key or uses one not associated with a `UserIdentity` of type `API`.

Endpoint coverage:
- Covers:
  `GET /v1/activities`
- Distinct meaning:
  Return the current user's favourite v1 activity objects.
- Covers:
  `GET /v2/activities`
- Distinct meaning:
  Return the current user's favourite v2 activity objects.

### Behavior 6: create activity

Behavior name:
create activity

Successful execution:
- Result:
  This behavior creates a new activity and returns an `Activity` with an internal `id`.
- Endpoint sequence:
  Step 1: `POST /v1/activities`
  or
  Step 1: `POST /v2/activities`
- Constraints:
  The request body is an `ActivityProperties` object. Swagger declares the body optional and does not list required fields. The source defines `activity_create` permission at user level `0`, but the allowed source does not show endpoint-to-permission enforcement.

Failure or exceptional branches:
- No concrete creation failure branch is specified by Swagger or visible controller code.

Endpoint coverage:
- Covers:
  `POST /v1/activities`
- Distinct meaning:
  Create a v1 activity.
- Covers:
  `POST /v2/activities`
- Distinct meaning:
  Create a v2 activity.

### Behavior 7: read activity

Behavior name:
read activity

Successful execution:
- Result:
  This behavior retrieves one activity by path id.
- Endpoint sequence:
  Step 1: `POST /v1/activities`
  Step 2: `GET /v1/activities/{id}`
  or
  Step 1: `POST /v2/activities`
  Step 2: `GET /v2/activities/{id}`
- Constraints:
  The `id` returned by Step 1 must be reused as path `{id}` in Step 2. Optional `attrs` controls response attributes.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No activity exists for `{id}`.
  - Endpoint group:
    Step 1: `GET /v1/activities/{id}`
  - Failure endpoint:
    `GET /v1/activities/{id}`
  - Why this fails:
    The path id targets a specific activity, but the prerequisite documented creation endpoint was intentionally omitted.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/activities` or another existing activity.

Endpoint coverage:
- Covers:
  `GET /v1/activities/{id}`
- Distinct meaning:
  Read one v1 activity.
- Covers:
  `GET /v2/activities/{id}`
- Distinct meaning:
  Read one v2 activity.

### Behavior 8: replace activity

Behavior name:
replace activity

Successful execution:
- Result:
  This behavior updates an activity with new information and clears activity properties not specified in the request.
- Endpoint sequence:
  Step 1: `POST /v1/activities`
  Step 2: `PUT /v1/activities/{id}`
  or
  Step 1: `POST /v2/activities`
  Step 2: `PUT /v2/activities/{id}`
- Constraints:
  Step 1 returns the activity `id`; Step 2 path `{id}` must reuse it. Step 2 body is `ActivityProperties`. Swagger explicitly distinguishes PUT replacement from PATCH partial update.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The target activity does not exist.
  - Endpoint group:
    Step 1: `PUT /v1/activities/{id}`
  - Failure endpoint:
    `PUT /v1/activities/{id}`
  - Why this fails:
    Replacement targets a specific activity id, but no documented endpoint was used to create that activity first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/activities`.

Endpoint coverage:
- Covers:
  `PUT /v1/activities/{id}`
- Distinct meaning:
  Full replacement of v1 activity properties.
- Covers:
  `PUT /v2/activities/{id}`
- Distinct meaning:
  Full replacement of v2 activity properties.

### Behavior 9: partially update activity

Behavior name:
partially update activity

Successful execution:
- Result:
  This behavior updates only the specified properties of an existing activity.
- Endpoint sequence:
  Step 1: `POST /v1/activities`
  Step 2: `PATCH /v1/activities/{id}`
  or
  Step 1: `POST /v2/activities`
  Step 2: `PATCH /v2/activities/{id}`
- Constraints:
  The `id` returned by Step 1 must be reused as path `{id}` in Step 2. Step 2 body is `ActivityProperties`; only supplied properties are updated.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The target activity does not exist.
  - Endpoint group:
    Step 1: `PATCH /v1/activities/{id}`
  - Failure endpoint:
    `PATCH /v1/activities/{id}`
  - Why this fails:
    Patch targets a specific activity id, but no documented creation endpoint established that id.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/activities`.

Endpoint coverage:
- Covers:
  `PATCH /v1/activities/{id}`
- Distinct meaning:
  Partial update of v1 activity properties.
- Covers:
  `PATCH /v2/activities/{id}`
- Distinct meaning:
  Partial update of v2 activity properties.

### Behavior 10: delete activity

Behavior name:
delete activity

Successful execution:
- Result:
  This behavior deletes an existing activity.
- Endpoint sequence:
  Step 1: `POST /v1/activities`
  Step 2: `DELETE /v1/activities/{id}`
  or
  Step 1: `POST /v2/activities`
  Step 2: `DELETE /v2/activities/{id}`
- Constraints:
  The activity `id` returned by Step 1 must be reused as path `{id}` in Step 2.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The activity does not exist.
  - Endpoint group:
    Step 1: `DELETE /v1/activities/{id}`
  - Failure endpoint:
    `DELETE /v1/activities/{id}`
  - Why this fails:
    Delete targets a specific activity id, but the prerequisite creation endpoint was intentionally omitted.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/activities`.

Endpoint coverage:
- Covers:
  `DELETE /v1/activities/{id}`
- Distinct meaning:
  Delete a v1 activity.
- Covers:
  `DELETE /v2/activities/{id}`
- Distinct meaning:
  Delete a v2 activity.

### Behavior 11: set own activity rating or favourite flag

Behavior name:
set own activity rating or favourite flag

Successful execution:
- Result:
  This behavior sets the current end user's rating and/or favourite flag for an activity.
- Endpoint sequence:
  Step 1: `POST /v1/activities`
  Step 2: `POST /v1/activities/{id}/rating`
  or
  Step 1: `POST /v2/activities`
  Step 2: `POST /v2/activities/{id}/rating`
- Constraints:
  Step 1 returns activity `id`; Step 2 path `{id}` must reuse it. Step 2 body is `ActivityRatingAttrs` with `rating` and/or `favourite`. The source defines `rating_set_own` permission at user level `0`, and API-key authentication requires an existing `API` identity.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The activity does not exist.
  - Endpoint group:
    Step 1: `POST /v1/activities/{id}/rating`
  - Failure endpoint:
    `POST /v1/activities/{id}/rating`
  - Why this fails:
    The rating is scoped to a specific activity id, but the activity was not created first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/activities`.
- Branch 2:
  - Unsatisfied condition:
    The current user cannot be authenticated.
  - Endpoint group:
    Step 1: `POST /v1/activities`
    Step 2: `POST /v1/activities/{id}/rating`
  - Failure endpoint:
    `POST /v1/activities/{id}/rating`
  - Why this fails:
    The behavior is explicitly for the end user's rating. `ApiKeyAuthenticator` returns empty when credentials do not match a stored API identity.
  - Intentionally violated constraints:
    Step 2 omits or uses an invalid API key/Google credential.

Endpoint coverage:
- Covers:
  `POST /v1/activities/{id}/rating`
- Distinct meaning:
  Set the current user's v1 rating/favourite state.
- Covers:
  `POST /v2/activities/{id}/rating`
- Distinct meaning:
  Set the current user's v2 rating/favourite state.

### Behavior 12: read own activity rating

Behavior name:
read own activity rating

Successful execution:
- Result:
  This behavior retrieves the current end user's rating/favourite state for an activity.
- Endpoint sequence:
  Step 1: `POST /v1/activities`
  Step 2: `POST /v1/activities/{id}/rating`
  Step 3: `GET /v1/activities/{id}/rating`
  or
  Step 1: `POST /v2/activities`
  Step 2: `POST /v2/activities/{id}/rating`
  Step 3: `GET /v2/activities/{id}/rating`
- Constraints:
  The activity `id` returned by Step 1 must be reused in Steps 2 and 3. The same authenticated end user must be used in Steps 2 and 3. Optional `attrs` controls response attributes.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No rating exists for the current user and activity.
  - Endpoint group:
    Step 1: `POST /v1/activities`
    Step 2: `GET /v1/activities/{id}/rating`
  - Failure endpoint:
    `GET /v1/activities/{id}/rating`
  - Why this fails:
    The read targets the current user's rating state, but the documented rating creation/update endpoint was intentionally omitted.
  - Intentionally violated constraints:
    `POST /v1/activities/{id}/rating` was not executed for the same authenticated user.
- Branch 2:
  - Unsatisfied condition:
    The current user cannot be authenticated.
  - Endpoint group:
    Step 1: `POST /v1/activities`
    Step 2: `POST /v1/activities/{id}/rating`
    Step 3: `GET /v1/activities/{id}/rating`
  - Failure endpoint:
    `GET /v1/activities/{id}/rating`
  - Why this fails:
    The rating is user-specific, and the source authenticator returns no principal for invalid credentials.
  - Intentionally violated constraints:
    Step 3 uses no valid current-user credential.

Endpoint coverage:
- Covers:
  `GET /v1/activities/{id}/rating`
- Distinct meaning:
  Read the current user's v1 activity rating.
- Covers:
  `GET /v2/activities/{id}/rating`
- Distinct meaning:
  Read the current user's v2 activity rating.

### Behavior 13: remove own activity rating

Behavior name:
remove own activity rating

Successful execution:
- Result:
  This behavior removes the current end user's rating from an activity.
- Endpoint sequence:
  Step 1: `POST /v1/activities`
  Step 2: `POST /v1/activities/{id}/rating`
  Step 3: `DELETE /v1/activities/{id}/rating`
  or
  Step 1: `POST /v2/activities`
  Step 2: `POST /v2/activities/{id}/rating`
  Step 3: `DELETE /v2/activities/{id}/rating`
- Constraints:
  The activity `id` from Step 1 must be reused in Steps 2 and 3. The same authenticated user must be used for setting and deleting the rating.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The activity does not exist.
  - Endpoint group:
    Step 1: `DELETE /v1/activities/{id}/rating`
  - Failure endpoint:
    `DELETE /v1/activities/{id}/rating`
  - Why this fails:
    The rating deletion is scoped to a specific activity id, but the activity was not created first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/activities`.
- Branch 2:
  - Unsatisfied condition:
    The current user cannot be authenticated.
  - Endpoint group:
    Step 1: `POST /v1/activities`
    Step 2: `POST /v1/activities/{id}/rating`
    Step 3: `DELETE /v1/activities/{id}/rating`
  - Failure endpoint:
    `DELETE /v1/activities/{id}/rating`
  - Why this fails:
    The rating being removed is the current user's rating, and the source authenticator requires a valid user identity.
  - Intentionally violated constraints:
    Step 3 uses no valid current-user credential.

Endpoint coverage:
- Covers:
  `DELETE /v1/activities/{id}/rating`
- Distinct meaning:
  Remove the current user's v1 activity rating.
- Covers:
  `DELETE /v2/activities/{id}/rating`
- Distinct meaning:
  Remove the current user's v2 activity rating.

### Behavior 14: list favourite activity ids

Behavior name:
list favourite activity ids

Successful execution:
- Result:
  This behavior returns the current user's favourite activity ids.
- Endpoint sequence:
  Step 1: `POST /v1/activities`
  Step 2: `POST /v1/favourites`
  Step 3: `GET /v1/favourites`
- Constraints:
  Step 1 returns an activity `id`. Step 2 body `id` array must contain that id. Step 3 returns an array of integer ids. Swagger incorrectly declares a required path parameter named `id` for `/v1/favourites`, but the path has no `{id}` segment.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The current user cannot be authenticated.
  - Endpoint group:
    Step 1: `POST /v1/activities`
    Step 2: `POST /v1/favourites`
    Step 3: `GET /v1/favourites`
  - Failure endpoint:
    `GET /v1/favourites`
  - Why this fails:
    Favourites are user content, and the source authenticator returns no current user for invalid credentials.
  - Intentionally violated constraints:
    Step 3 omits or uses an invalid current-user credential.

Endpoint coverage:
- Covers:
  `GET /v1/favourites`
- Distinct meaning:
  Read the current user's favourite activity id list.

### Behavior 15: replace favourite activity id list

Behavior name:
replace favourite activity id list

Successful execution:
- Result:
  This behavior replaces the current user's favourite activity id list.
- Endpoint sequence:
  Step 1: `POST /v1/activities`
  Step 2: `POST /v1/favourites`
- Constraints:
  Step 1 returns an activity `id`. Step 2 body is `PutFavouritesEntity`; its `id` field is an array of activity ids and must include ids intended to become favourites. Swagger's required path parameter `id` is inconsistent with the path `/v1/favourites`, which has no path variable.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The current user cannot be authenticated.
  - Endpoint group:
    Step 1: `POST /v1/activities`
    Step 2: `POST /v1/favourites`
  - Failure endpoint:
    `POST /v1/favourites`
  - Why this fails:
    The operation modifies user-specific content, and `ApiKeyAuthenticator` only authenticates users with a matching API identity.
  - Intentionally violated constraints:
    Step 2 uses no valid current-user credential.

Endpoint coverage:
- Covers:
  `POST /v1/favourites`
- Distinct meaning:
  Replace the current user's favourite activity ids.

### Behavior 16: list or search media files

Behavior name:
list or search media files

Successful execution:
- Result:
  This behavior lists media files referenced in activities, optionally filtered by URI text.
- Endpoint sequence:
  Step 1: `GET /v1/media_files`
- Constraints:
  Optional `uri` filters media files by case-sensitive substring match in their URI. Optional `attrs` controls response attributes. This collection query can succeed with an empty result set.

Failure or exceptional branches:
- No concrete failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `GET /v1/media_files`
- Distinct meaning:
  List or search media-file metadata.

### Behavior 17: add media file

Behavior name:
add media file

Successful execution:
- Result:
  This behavior adds a media file to the system and returns its metadata.
- Endpoint sequence:
  Step 1: `POST /v1/media_files`
- Constraints:
  The body is `MediaFile`. Swagger says callers may specify a media file URL in `uri` or use a data URI to upload a base64-encoded file. The response returns a media-file `id`.

Failure or exceptional branches:
- No concrete creation failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `POST /v1/media_files`
- Distinct meaning:
  Create media-file metadata and optionally upload content via data URI.

### Behavior 18: read media file metadata

Behavior name:
read media file metadata

Successful execution:
- Result:
  This behavior retrieves metadata for a specific media file.
- Endpoint sequence:
  Step 1: `POST /v1/media_files`
  Step 2: `GET /v1/media_files/{id}`
- Constraints:
  Step 1 returns media-file `id`; Step 2 path `{id}` must reuse it. Optional `attrs` controls response attributes.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No media file exists for `{id}`.
  - Endpoint group:
    Step 1: `GET /v1/media_files/{id}`
  - Failure endpoint:
    `GET /v1/media_files/{id}`
  - Why this fails:
    The read targets a specific media-file id, but the creation endpoint was intentionally omitted.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/media_files`.

Endpoint coverage:
- Covers:
  `GET /v1/media_files/{id}`
- Distinct meaning:
  Read media-file metadata.

### Behavior 19: update media file metadata

Behavior name:
update media file metadata

Successful execution:
- Result:
  This behavior updates metadata for an existing media file.
- Endpoint sequence:
  Step 1: `POST /v1/media_files`
  Step 2: `PUT /v1/media_files/{id}`
- Constraints:
  Step 1 returns media-file `id`; Step 2 path `{id}` must reuse it. Step 2 body is `MediaFile`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The media file does not exist.
  - Endpoint group:
    Step 1: `PUT /v1/media_files/{id}`
  - Failure endpoint:
    `PUT /v1/media_files/{id}`
  - Why this fails:
    The update targets a specific media-file id, but no media file was created first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/media_files`.

Endpoint coverage:
- Covers:
  `PUT /v1/media_files/{id}`
- Distinct meaning:
  Update media-file metadata.

### Behavior 20: force-delete media file

Behavior name:
force-delete media file

Successful execution:
- Result:
  This behavior deletes a media file even when it is referenced by activities.
- Endpoint sequence:
  Step 1: `POST /v1/media_files`
  Step 2: `POST /v1/activities`
  Step 3: `DELETE /v1/media_files/{id}`
- Constraints:
  Step 1 returns media-file `id`. Step 2 body `media_files` should reference that media file to create the referenced state. Step 3 path `{id}` must reuse the media-file id. The Swagger says deletion defaults to deleting even if referenced, so `verify_unused` must be absent or false.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The media file does not exist.
  - Endpoint group:
    Step 1: `DELETE /v1/media_files/{id}`
  - Failure endpoint:
    `DELETE /v1/media_files/{id}`
  - Why this fails:
    The delete targets a specific media-file id, but no media file was created first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/media_files`.

Endpoint coverage:
- Covers:
  `DELETE /v1/media_files/{id}`
- Distinct meaning:
  Delete media without checking whether activities reference it.

### Behavior 21: delete media file only when unused

Behavior name:
delete unused media file

Successful execution:
- Result:
  This behavior deletes a media file only after verifying it is not referenced by activities.
- Endpoint sequence:
  Step 1: `POST /v1/media_files`
  Step 2: `DELETE /v1/media_files/{id}`
- Constraints:
  Step 1 returns media-file `id`; Step 2 path `{id}` must reuse it and use `verify_unused=true`. The media file must not be referenced by any activity.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The media file is referenced by an activity.
  - Endpoint group:
    Step 1: `POST /v1/media_files`
    Step 2: `POST /v1/activities`
    Step 3: `DELETE /v1/media_files/{id}`
  - Failure endpoint:
    `DELETE /v1/media_files/{id}`
  - Why this fails:
    Swagger says `verify_unused=true` verifies the media file is not referenced before deleting it.
  - Intentionally violated constraints:
    Step 2 creates an activity whose `media_files` includes the media-file `id`; Step 3 uses `verify_unused=true`.

Endpoint coverage:
- Covers:
  `DELETE /v1/media_files/{id}`
- Distinct meaning:
  Conditional delete with unused verification.

### Behavior 22: download media file content

Behavior name:
download media file content

Successful execution:
- Result:
  This behavior downloads the binary content of a media file.
- Endpoint sequence:
  Step 1: `POST /v1/media_files`
  Step 2: `GET /v1/media_files/{id}/file`
- Constraints:
  Step 1 returns media-file `id`; Step 2 path `{id}` must reuse it. The response produces `application/octet-stream`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No media file exists for `{id}`.
  - Endpoint group:
    Step 1: `GET /v1/media_files/{id}/file`
  - Failure endpoint:
    `GET /v1/media_files/{id}/file`
  - Why this fails:
    File download targets a specific media-file id, but the media file was not created first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/media_files`.

Endpoint coverage:
- Covers:
  `GET /v1/media_files/{id}/file`
- Distinct meaning:
  Download original media content.

### Behavior 23: download resized image media file

Behavior name:
download resized image media file

Successful execution:
- Result:
  This behavior downloads an image media file resized so that width/height do not exceed the requested size; images are not enlarged.
- Endpoint sequence:
  Step 1: `POST /v1/media_files`
  Step 2: `GET /v1/media_files/{id}/file`
- Constraints:
  Step 1 must create or reference an image media file and returns media-file `id`. Step 2 path `{id}` reuses it and supplies `size={integer}`. Swagger says `size` is rounded up to the next power of two, such as 256, 512, or 1024.

Failure or exceptional branches:
- No concrete non-image or invalid-size branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `GET /v1/media_files/{id}/file`
- Distinct meaning:
  Download resized image content using the `size` query parameter.

### Behavior 24: list or search system messages

Behavior name:
list or search system messages

Successful execution:
- Result:
  This behavior returns system messages, optionally filtered by key or validity.
- Endpoint sequence:
  Step 1: `GET /v1/system_messages`
- Constraints:
  Optional query parameters are `key`, `valid`, and `attrs`. The source defines `system_message_read` permission at level `-100`, but endpoint security is not documented in Swagger.

Failure or exceptional branches:
- No concrete failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `GET /v1/system_messages`
- Distinct meaning:
  List or filter system messages.

### Behavior 25: create system message

Behavior name:
create system message

Successful execution:
- Result:
  This behavior creates a system message.
- Endpoint sequence:
  Step 1: `POST /v1/system_messages`
- Constraints:
  The body is `SystemMessage` with fields such as `key`, `value`, `valid_from`, and `valid_to`. The source defines `system_message_manage` at administrator level `20`, but endpoint enforcement is not visible.

Failure or exceptional branches:
- No concrete creation failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `POST /v1/system_messages`
- Distinct meaning:
  Create a system message.

### Behavior 26: read system message

Behavior name:
read system message

Successful execution:
- Result:
  This behavior retrieves one system message by id.
- Endpoint sequence:
  Step 1: `POST /v1/system_messages`
  Step 2: `GET /v1/system_messages/{id}`
- Constraints:
  Step 1 returns system-message `id`; Step 2 path `{id}` must reuse it. Optional `attrs` controls response attributes.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No system message exists for `{id}`.
  - Endpoint group:
    Step 1: `GET /v1/system_messages/{id}`
  - Failure endpoint:
    `GET /v1/system_messages/{id}`
  - Why this fails:
    The read targets a specific system-message id, but the documented creation endpoint was omitted.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/system_messages`.

Endpoint coverage:
- Covers:
  `GET /v1/system_messages/{id}`
- Distinct meaning:
  Read one system message.

### Behavior 27: update system message

Behavior name:
update system message

Successful execution:
- Result:
  This behavior updates an existing system message.
- Endpoint sequence:
  Step 1: `POST /v1/system_messages`
  Step 2: `PUT /v1/system_messages/{id}`
- Constraints:
  Step 1 returns system-message `id`; Step 2 path `{id}` must reuse it. Step 2 body is `SystemMessage`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The system message does not exist.
  - Endpoint group:
    Step 1: `PUT /v1/system_messages/{id}`
  - Failure endpoint:
    `PUT /v1/system_messages/{id}`
  - Why this fails:
    The update targets a specific system-message id, but no system message was created first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/system_messages`.

Endpoint coverage:
- Covers:
  `PUT /v1/system_messages/{id}`
- Distinct meaning:
  Update a system message.

### Behavior 28: delete system message

Behavior name:
delete system message

Successful execution:
- Result:
  This behavior deletes an existing system message.
- Endpoint sequence:
  Step 1: `POST /v1/system_messages`
  Step 2: `DELETE /v1/system_messages/{id}`
- Constraints:
  Step 1 returns system-message `id`; Step 2 path `{id}` must reuse it.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The system message does not exist.
  - Endpoint group:
    Step 1: `DELETE /v1/system_messages/{id}`
  - Failure endpoint:
    `DELETE /v1/system_messages/{id}`
  - Why this fails:
    The delete targets a specific system-message id, but the creation endpoint was intentionally omitted.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/system_messages`.

Endpoint coverage:
- Covers:
  `DELETE /v1/system_messages/{id}`
- Distinct meaning:
  Delete a system message.

### Behavior 29: check system health

Behavior name:
check system health

Successful execution:
- Result:
  This behavior checks whether the API system responds to a ping/status request.
- Endpoint sequence:
  Step 1: `GET /v1/system/ping`
- Constraints:
  No request parameters are documented.

Failure or exceptional branches:
- No concrete failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `GET /v1/system/ping`
- Distinct meaning:
  Health/status ping.

### Behavior 30: list roles and permission levels

Behavior name:
list roles and permission levels

Successful execution:
- Result:
  This behavior returns role and permission level metadata.
- Endpoint sequence:
  Step 1: `GET /v1/system/roles`
- Constraints:
  The response schema is `RolesView`, containing `permission_levels` and `role_levels`. The source defines role levels `limited_user=-1`, `user=0`, `moderator=10`, and `administrator=20`; it also defines permissions such as `activity_create`, `rating_set_own`, `category_create`, `system_message_manage`, `auth_role_list`, and `auth_user_create`.

Failure or exceptional branches:
- No endpoint-specific failure branch is visible. The source defines `auth_role_list` at administrator level `20`, but the allowed files do not show that `/v1/system/roles` enforces it.

Endpoint coverage:
- Covers:
  `GET /v1/system/roles`
- Distinct meaning:
  Retrieve system role and permission metadata.

### Behavior 31: list or search tags/categories

Behavior name:
list or search tags/categories

Successful execution:
- Result:
  This behavior returns tags or categories, optionally filtered by group, name, or minimum activity count.
- Endpoint sequence:
  Step 1: `GET /v1/categories`
  or
  Step 1: `GET /v2/tags`
- Constraints:
  Both versions use the `Tag` schema. Optional filters are `group`, `name`, `min_activities_count`, and `attrs`.

Failure or exceptional branches:
- No concrete failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `GET /v1/categories`
- Distinct meaning:
  List/search v1 categories.
- Covers:
  `GET /v2/tags`
- Distinct meaning:
  List/search v2 tags.

### Behavior 32: create tag/category

Behavior name:
create tag/category

Successful execution:
- Result:
  This behavior creates a tag/category.
- Endpoint sequence:
  Step 1: `POST /v1/categories`
  or
  Step 1: `POST /v2/tags`
- Constraints:
  The body is `Tag`, with fields such as `group`, `name`, optional `media_file`, and `activities_count`. The source defines `category_create` at moderator level `10`, but endpoint enforcement is not visible.

Failure or exceptional branches:
- No concrete creation failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `POST /v1/categories`
- Distinct meaning:
  Create a v1 category.
- Covers:
  `POST /v2/tags`
- Distinct meaning:
  Create a v2 tag.

### Behavior 33: read tag/category

Behavior name:
read tag/category

Successful execution:
- Result:
  This behavior retrieves one tag/category by id.
- Endpoint sequence:
  Step 1: `POST /v1/categories`
  Step 2: `GET /v1/categories/{id}`
  or
  Step 1: `POST /v2/tags`
  Step 2: `GET /v2/tags/{id}`
- Constraints:
  Step 1 returns tag/category `id`; Step 2 path `{id}` must reuse it. Optional `attrs` controls response attributes.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No tag/category exists for `{id}`.
  - Endpoint group:
    Step 1: `GET /v1/categories/{id}`
  - Failure endpoint:
    `GET /v1/categories/{id}`
  - Why this fails:
    The read targets a specific category id, but no category was created first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/categories`.

Endpoint coverage:
- Covers:
  `GET /v1/categories/{id}`
- Distinct meaning:
  Read one v1 category.
- Covers:
  `GET /v2/tags/{id}`
- Distinct meaning:
  Read one v2 tag.

### Behavior 34: update tag/category

Behavior name:
update tag/category

Successful execution:
- Result:
  This behavior updates an existing tag/category.
- Endpoint sequence:
  Step 1: `POST /v1/categories`
  Step 2: `PUT /v1/categories/{id}`
  or
  Step 1: `POST /v2/tags`
  Step 2: `PUT /v2/tags/{id}`
- Constraints:
  Step 1 returns tag/category `id`; Step 2 path `{id}` must reuse it. Step 2 body is `Tag`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The tag/category does not exist.
  - Endpoint group:
    Step 1: `PUT /v1/categories/{id}`
  - Failure endpoint:
    `PUT /v1/categories/{id}`
  - Why this fails:
    The update targets a specific category id, but no category was created first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/categories`.

Endpoint coverage:
- Covers:
  `PUT /v1/categories/{id}`
- Distinct meaning:
  Update a v1 category.
- Covers:
  `PUT /v2/tags/{id}`
- Distinct meaning:
  Update a v2 tag.

### Behavior 35: delete tag/category

Behavior name:
delete tag/category

Successful execution:
- Result:
  This behavior deletes an existing tag/category.
- Endpoint sequence:
  Step 1: `POST /v1/categories`
  Step 2: `DELETE /v1/categories/{id}`
  or
  Step 1: `POST /v2/tags`
  Step 2: `DELETE /v2/tags/{id}`
- Constraints:
  Step 1 returns tag/category `id`; Step 2 path `{id}` must reuse it.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The tag/category does not exist.
  - Endpoint group:
    Step 1: `DELETE /v1/categories/{id}`
  - Failure endpoint:
    `DELETE /v1/categories/{id}`
  - Why this fails:
    The delete targets a specific category id, but no category was created first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/categories`.

Endpoint coverage:
- Covers:
  `DELETE /v1/categories/{id}`
- Distinct meaning:
  Delete a v1 category.
- Covers:
  `DELETE /v2/tags/{id}`
- Distinct meaning:
  Delete a v2 tag.

### Behavior 36: list or search users

Behavior name:
list or search users

Successful execution:
- Result:
  This behavior returns users, optionally filtered by name.
- Endpoint sequence:
  Step 1: `GET /v1/users`
- Constraints:
  Optional `name` filters users. Optional `attrs` controls response attributes. The source defines user-related administrator permissions, but endpoint enforcement is not visible.

Failure or exceptional branches:
- No concrete failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `GET /v1/users`
- Distinct meaning:
  List or search users.

### Behavior 37: create user

Behavior name:
create user

Successful execution:
- Result:
  This behavior creates a user.
- Endpoint sequence:
  Step 1: `POST /v1/users`
- Constraints:
  The body is `User`, with fields such as `name`, `email_address`, `authorization_level`, and `identities`. `UserIdentity.type` may be `API` or `GOOGLE`. The source defines `auth_user_create` at administrator level `20`, but endpoint enforcement is not visible.

Failure or exceptional branches:
- No concrete creation failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `POST /v1/users`
- Distinct meaning:
  Create a user.

### Behavior 38: read user

Behavior name:
read user

Successful execution:
- Result:
  This behavior retrieves one user by id.
- Endpoint sequence:
  Step 1: `POST /v1/users`
  Step 2: `GET /v1/users/{id}`
- Constraints:
  Step 1 returns user `id`; Step 2 path `{id}` must reuse it. Optional `attrs` controls response attributes.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No user exists for `{id}`.
  - Endpoint group:
    Step 1: `GET /v1/users/{id}`
  - Failure endpoint:
    `GET /v1/users/{id}`
  - Why this fails:
    The read targets a specific user id, but no user was created first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/users`.

Endpoint coverage:
- Covers:
  `GET /v1/users/{id}`
- Distinct meaning:
  Read one user.

### Behavior 39: update user

Behavior name:
update user

Successful execution:
- Result:
  This behavior updates an existing user.
- Endpoint sequence:
  Step 1: `POST /v1/users`
  Step 2: `PUT /v1/users/{id}`
- Constraints:
  Step 1 returns user `id`; Step 2 path `{id}` must reuse it. Step 2 body is `User`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The user does not exist.
  - Endpoint group:
    Step 1: `PUT /v1/users/{id}`
  - Failure endpoint:
    `PUT /v1/users/{id}`
  - Why this fails:
    The update targets a specific user id, but no user was created first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/users`.

Endpoint coverage:
- Covers:
  `PUT /v1/users/{id}`
- Distinct meaning:
  Update a user.

### Behavior 40: delete user

Behavior name:
delete user

Successful execution:
- Result:
  This behavior deletes an existing user.
- Endpoint sequence:
  Step 1: `POST /v1/users`
  Step 2: `DELETE /v1/users/{id}`
- Constraints:
  Step 1 returns user `id`; Step 2 path `{id}` must reuse it.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The user does not exist.
  - Endpoint group:
    Step 1: `DELETE /v1/users/{id}`
  - Failure endpoint:
    `DELETE /v1/users/{id}`
  - Why this fails:
    The delete targets a specific user id, but no user was created first.
  - Intentionally violated constraints:
    `{id}` was not obtained from `POST /v1/users`.

Endpoint coverage:
- Covers:
  `DELETE /v1/users/{id}`
- Distinct meaning:
  Delete a user.

### Behavior 41: retrieve authenticated user profile

Behavior name:
retrieve authenticated user profile

Successful execution:
- Result:
  This behavior returns the current authenticated user's profile, including role and role permissions.
- Endpoint sequence:
  Step 1: `POST /v1/users`
  Step 2: `GET /v1/users/profile`
- Constraints:
  One documented way to establish the state is for Step 1 to create a user whose `identities` includes an `API` identity value. Step 2 must authenticate with that same API identity value, because `ApiKeyAuthenticator` reads users by `IdentityType.API`. Google authentication is also implemented: a valid Google id token can create the user automatically and add an API key if missing, but no Swagger endpoint documents that authentication exchange. Swagger incorrectly declares a required path parameter `id` for `/v1/users/profile`, but the path has no `{id}` segment.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The request has no valid current user.
  - Endpoint group:
    Step 1: `POST /v1/users`
    Step 2: `GET /v1/users/profile`
  - Failure endpoint:
    `GET /v1/users/profile`
  - Why this fails:
    `ApiKeyAuthenticator` returns empty when the supplied credential does not match a user `API` identity; `GoogleAuthenticator` returns empty when the Google id token is invalid.
  - Intentionally violated constraints:
    Step 2 omits credentials or uses an API key/Google token not associated with a valid authenticated user.

Endpoint coverage:
- Covers:
  `GET /v1/users/profile`
- Distinct meaning:
  Read the current authenticated user's profile and permissions.