### Function 1: search activities

Function name:
search activities

Core endpoint(s):
- `GET /v1/activities`
- `GET /v2/activities`

Preconditions:
- None; this collection search can run without pre-existing activity state and can validly return an empty result set.

Successful execution:
- Result:
  The function returns activities matching collection-level search filters.
- Invocation:
  Step 1: `GET /v1/activities` with optional query values such as `attrs`, `name`, `text`, `featured`, `categories`, `age_1`, `age_2`, `participants`, `time_1`, `time_2`, `ratings_count_min`, and `ratings_average_min`
  or
  Step 1: `GET /v2/activities` with optional query values such as `attrs`, `name`, `text`, `featured`, `categories`, `ages`, `participants`, `durations`, `ratings_count_min`, and `ratings_average_min`
- Constraints:
  `attrs` controls response attributes. `name` and `text` search activity fields and may use a leading minus to exclude words. Version-specific age/time filters differ: v1 uses `age_1`, `age_2`, `time_1`, and `time_2`, while v2 uses `ages` and `durations`.

Failure or exceptional branches:
- No concrete failure branch is specified by Swagger or by the visible `src` implementation for ordinary collection search.

Endpoint coverage:
- Covers:
  `GET /v1/activities`
- Distinct meaning:
  Search v1 activities using v1 query names.
- Covers:
  `GET /v2/activities`
- Distinct meaning:
  Search v2 activities using v2 query names.

### Function 2: retrieve activities by identifier list

Function name:
retrieve activities by identifier list

Core endpoint(s):
- `GET /v1/activities`
- `GET /v2/activities`

Preconditions:
- One or more activities exist in the database. This can be satisfied by directly inserting activity rows with known ids or by calling `POST /v1/activities` or `POST /v2/activities` with an `ActivityProperties` body for each activity.
- The `id` query value used by the collection request must contain the created activity id or comma-separated ids. If the API is used to establish the activities, those ids must be taken from the activity creation responses.

Successful execution:
- Result:
  The function returns activities whose internal ids match the comma-separated `id` query value.
- Invocation:
  Step 1: `GET /v1/activities` with query `id={id}` or `id={id1},{id2}`
  or
  Step 1: `GET /v2/activities` with query `id={id}` or `id={id1},{id2}`
- Constraints:
  The `id` query parameter is distinct from path-based single-activity lookup because the collection endpoint returns a filtered activity collection.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No activity exists for the requested `id` query value. This can be produced by starting from an empty database, deleting the activity beforehand, or intentionally not inserting it directly and not calling `POST /v1/activities` or `POST /v2/activities`.
  - Failure endpoint:
    `GET /v1/activities`
  - Why this fails:
    The query asks for a specific internal activity id, but no activity state exists for that id. Swagger does not expose the exact error/status handling, so the visible result may be an empty result rather than an error.
  - Intentionally violated constraints:
    The `id` query value was not obtained from a documented activity creation response or another existing activity row.

Endpoint coverage:
- Covers:
  `GET /v1/activities`
- Distinct meaning:
  Retrieve activities by explicit internal ids through the v1 collection endpoint.
- Covers:
  `GET /v2/activities`
- Distinct meaning:
  Retrieve activities by explicit internal ids through the v2 collection endpoint.

### Function 3: retrieve random matching activities

Function name:
retrieve random activities

Core endpoint(s):
- `GET /v1/activities`
- `GET /v2/activities`

Preconditions:
- None; the random collection query can run without setup and may return fewer activities than requested if the matched collection is smaller.

Successful execution:
- Result:
  The function returns a requested number of random activities matching any other allowed filters.
- Invocation:
  Step 1: `GET /v1/activities` with query `random={count}` and any compatible ordinary filters
  or
  Step 1: `GET /v2/activities` with query `random={count}` and any compatible ordinary filters
- Constraints:
  `random` is an integer limiting the number of random activities. It may be combined with other ordinary search filters according to the Swagger description.

Failure or exceptional branches:
- No concrete failure branch is specified beyond invalid query values, whose validation handling is not visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /v1/activities`
- Distinct meaning:
  Random v1 activity selection.
- Covers:
  `GET /v2/activities`
- Distinct meaning:
  Random v2 activity selection.

### Function 4: list overall favourite activities

Function name:
list overall favourite activities

Core endpoint(s):
- `GET /v1/activities`
- `GET /v2/activities`

Preconditions:
- None; overall favourite listing can run without setup and can validly return an empty result set.

Successful execution:
- Result:
  The function returns activities sorted by how many users have marked them as favourites.
- Invocation:
  Step 1: `GET /v1/activities` with query `favourites={count}`
  or
  Step 1: `GET /v2/activities` with query `favourites={count}`
- Constraints:
  Swagger explicitly says `favourites` cannot be used together with any other filtering parameter.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No special database state is required; the request itself combines `favourites={count}` with another filtering query value such as `name={text}`.
  - Failure endpoint:
    `GET /v1/activities`
  - Why this fails:
    Swagger describes `favourites` as exclusive with other filtering parameters.
  - Intentionally violated constraints:
    The documented exclusivity requirement for `favourites` was violated by combining it with another filter.

Endpoint coverage:
- Covers:
  `GET /v1/activities`
- Distinct meaning:
  List global favourite v1 activities.
- Covers:
  `GET /v2/activities`
- Distinct meaning:
  List global favourite v2 activities.

### Function 5: list current user's favourite activities

Function name:
list current user's favourite activities

Core endpoint(s):
- `GET /v1/activities`
- `GET /v2/activities`

Preconditions:
- An activity exists in the database. This can be satisfied by directly inserting an activity row or by calling `POST /v1/activities` or `POST /v2/activities` with an `ActivityProperties` body and recording the returned `id`.
- The authenticated user has a favourite relationship to that activity. This can be satisfied by directly inserting the user-activity favourite/rating state linked to the same user and activity, or by calling `POST /v1/favourites` with a `PutFavouritesEntity` body whose `id` array contains the activity id.
- The request must authenticate as the same user whose favourites are being listed. The visible source shows API-key authentication resolves a user by a `UserIdentity` of type `API`; Swagger does not document the credential transport.

Successful execution:
- Result:
  The function returns activity objects that the current authenticated user has marked as favourites.
- Invocation:
  Step 1: `GET /v1/activities` with query `my_favourites=true` and a valid current-user credential
  or
  Step 1: `GET /v2/activities` with query `my_favourites=true` and a valid current-user credential
- Constraints:
  The favourite state is scoped to the authenticated user. API-key credentials must match a stored `API` identity; Google authentication is also implemented in source but no Swagger endpoint documents an authentication exchange.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An activity exists in the database. This can be satisfied by direct insertion or by calling `POST /v1/activities` or `POST /v2/activities`.
    - The current user has favourite state for that activity. This can be satisfied by direct database setup or by calling `POST /v1/favourites` with body `id=[{activityId}]`.
    - The failing request omits credentials or uses credentials that do not match a stored `UserIdentity` of type `API` and are not a valid Google id token accepted by the configured verifier.
  - Failure endpoint:
    `GET /v1/activities`
  - Why this fails:
    `my_favourites` is determined by the current user, and `ApiKeyAuthenticator` returns no principal when no user has the supplied API identity. `GoogleAuthenticator` also returns empty when the id token is invalid.
  - Intentionally violated constraints:
    The current-user authentication requirement was violated.

Endpoint coverage:
- Covers:
  `GET /v1/activities`
- Distinct meaning:
  Return the current user's favourite v1 activity objects.
- Covers:
  `GET /v2/activities`
- Distinct meaning:
  Return the current user's favourite v2 activity objects.

### Function 6: create activity

Function name:
create activity

Core endpoint(s):
- `POST /v1/activities`
- `POST /v2/activities`

Preconditions:
- None; this endpoint creates the activity state. If authentication and authorization are enforced by hidden resource code, the source defines `activity_create` as available at user level `0`, but endpoint enforcement is not visible.

Successful execution:
- Result:
  The function creates a new activity and returns an `Activity` with an internal `id`.
- Invocation:
  Step 1: `POST /v1/activities` with an optional `ActivityProperties` body
  or
  Step 1: `POST /v2/activities` with an optional `ActivityProperties` body
- Constraints:
  Swagger declares the body optional and does not list required fields. The visible source defines the `activity_create` permission at user level `0`, but does not show endpoint-to-permission enforcement.

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

### Function 7: read activity

Function name:
read activity

Core endpoint(s):
- `GET /v1/activities/{id}`
- `GET /v2/activities/{id}`

Preconditions:
- An activity exists in the database. This can be satisfied by directly inserting an activity row or by calling `POST /v1/activities` or `POST /v2/activities` with an `ActivityProperties` body.
- The `{id}` path value must identify that activity. If the API is used to establish the activity, `{id}` must be taken from the activity creation response.

Successful execution:
- Result:
  The function retrieves one activity by path id.
- Invocation:
  Step 1: `GET /v1/activities/{id}` with required path `{id}` and optional query `attrs`
  or
  Step 1: `GET /v2/activities/{id}` with required path `{id}` and optional query `attrs`
- Constraints:
  `{id}` must identify an existing activity.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No activity exists for `{id}`. This can be produced by starting from an empty database, deleting the activity beforehand, or intentionally not inserting it directly and not calling `POST /v1/activities` or `POST /v2/activities`.
  - Failure endpoint:
    `GET /v1/activities/{id}`
  - Why this fails:
    The path id targets a specific activity, but no activity state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing activity.

Endpoint coverage:
- Covers:
  `GET /v1/activities/{id}`
- Distinct meaning:
  Read one v1 activity.
- Covers:
  `GET /v2/activities/{id}`
- Distinct meaning:
  Read one v2 activity.

### Function 8: replace activity

Function name:
replace activity

Core endpoint(s):
- `PUT /v1/activities/{id}`
- `PUT /v2/activities/{id}`

Preconditions:
- An activity exists in the database. This can be satisfied by directly inserting an activity row or by calling `POST /v1/activities` or `POST /v2/activities` with an `ActivityProperties` body.
- The `{id}` path value must identify that activity. If the API is used to establish the activity, `{id}` must be taken from the activity creation response.

Successful execution:
- Result:
  The function replaces an activity with new information and clears activity properties not specified in the request.
- Invocation:
  Step 1: `PUT /v1/activities/{id}` with required path `{id}` and an `ActivityProperties` body
  or
  Step 1: `PUT /v2/activities/{id}` with required path `{id}` and an `ActivityProperties` body
- Constraints:
  Swagger explicitly distinguishes PUT replacement from PATCH partial update. The visible source defines activity edit permissions, but endpoint enforcement is not visible.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No activity exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/activities` or `POST /v2/activities` before replacement.
  - Failure endpoint:
    `PUT /v1/activities/{id}`
  - Why this fails:
    Replacement targets a specific activity id, but no activity state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing activity.

Endpoint coverage:
- Covers:
  `PUT /v1/activities/{id}`
- Distinct meaning:
  Full replacement of v1 activity properties.
- Covers:
  `PUT /v2/activities/{id}`
- Distinct meaning:
  Full replacement of v2 activity properties.

### Function 9: partially update activity

Function name:
partially update activity

Core endpoint(s):
- `PATCH /v1/activities/{id}`
- `PATCH /v2/activities/{id}`

Preconditions:
- An activity exists in the database. This can be satisfied by directly inserting an activity row or by calling `POST /v1/activities` or `POST /v2/activities` with an `ActivityProperties` body.
- The `{id}` path value must identify that activity. If the API is used to establish the activity, `{id}` must be taken from the activity creation response.

Successful execution:
- Result:
  The function updates only the specified properties of an existing activity.
- Invocation:
  Step 1: `PATCH /v1/activities/{id}` with required path `{id}` and an `ActivityProperties` body containing the fields to update
  or
  Step 1: `PATCH /v2/activities/{id}` with required path `{id}` and an `ActivityProperties` body containing the fields to update
- Constraints:
  Only supplied properties are updated. The visible source defines activity edit permissions, but endpoint enforcement is not visible.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No activity exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/activities` or `POST /v2/activities` before patching.
  - Failure endpoint:
    `PATCH /v1/activities/{id}`
  - Why this fails:
    Patch targets a specific activity id, but no activity state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing activity.

Endpoint coverage:
- Covers:
  `PATCH /v1/activities/{id}`
- Distinct meaning:
  Partial update of v1 activity properties.
- Covers:
  `PATCH /v2/activities/{id}`
- Distinct meaning:
  Partial update of v2 activity properties.

### Function 10: delete activity

Function name:
delete activity

Core endpoint(s):
- `DELETE /v1/activities/{id}`
- `DELETE /v2/activities/{id}`

Preconditions:
- An activity exists in the database. This can be satisfied by directly inserting an activity row or by calling `POST /v1/activities` or `POST /v2/activities` with an `ActivityProperties` body.
- The `{id}` path value must identify that activity. If the API is used to establish the activity, `{id}` must be taken from the activity creation response.

Successful execution:
- Result:
  The function deletes an existing activity.
- Invocation:
  Step 1: `DELETE /v1/activities/{id}` with required path `{id}`
  or
  Step 1: `DELETE /v2/activities/{id}` with required path `{id}`
- Constraints:
  `{id}` must identify the activity to delete. The visible source defines activity edit permissions, but endpoint enforcement is not visible.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No activity exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/activities` or `POST /v2/activities` before deletion.
  - Failure endpoint:
    `DELETE /v1/activities/{id}`
  - Why this fails:
    Delete targets a specific activity id, but no activity state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing activity.

Endpoint coverage:
- Covers:
  `DELETE /v1/activities/{id}`
- Distinct meaning:
  Delete a v1 activity.
- Covers:
  `DELETE /v2/activities/{id}`
- Distinct meaning:
  Delete a v2 activity.

### Function 11: set own activity rating or favourite flag

Function name:
set own activity rating or favourite flag

Core endpoint(s):
- `POST /v1/activities/{id}/rating`
- `POST /v2/activities/{id}/rating`

Preconditions:
- An activity exists in the database. This can be satisfied by directly inserting an activity row or by calling `POST /v1/activities` or `POST /v2/activities` with an `ActivityProperties` body.
- The `{id}` path value must identify that activity. If the API is used to establish the activity, `{id}` must be taken from the activity creation response.
- The request must authenticate as the user whose rating or favourite flag is being set. This can be satisfied by directly inserting a `User` with a `UserIdentity` of type `API` and using that API identity value, or by using a valid Google id token accepted by the configured verifier.

Successful execution:
- Result:
  The function sets the current end user's rating and/or favourite flag for an activity.
- Invocation:
  Step 1: `POST /v1/activities/{id}/rating` with required path `{id}`, a valid current-user credential, and an `ActivityRatingAttrs` body containing `rating` and/or `favourite`
  or
  Step 1: `POST /v2/activities/{id}/rating` with required path `{id}`, a valid current-user credential, and an `ActivityRatingAttrs` body containing `rating` and/or `favourite`
- Constraints:
  The visible source defines `rating_set_own` permission at user level `0`. API-key authentication requires an existing `API` identity; Google authentication can create a user and API key for a valid id token.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No activity exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/activities` or `POST /v2/activities` before rating.
    - The request may otherwise use valid current-user credentials.
  - Failure endpoint:
    `POST /v1/activities/{id}/rating`
  - Why this fails:
    The rating is scoped to a specific activity id, but no activity state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing activity.
- Branch 2:
  - Preconditions:
    - An activity exists in the database. This can be satisfied by direct insertion or by calling `POST /v1/activities` or `POST /v2/activities`.
    - The failing request omits credentials or uses credentials that do not match a stored `API` identity and are not a valid Google id token accepted by source authentication.
  - Failure endpoint:
    `POST /v1/activities/{id}/rating`
  - Why this fails:
    The function is explicitly for the end user's rating. `ApiKeyAuthenticator` returns empty when credentials do not match a stored API identity, and `GoogleAuthenticator` returns empty for invalid id tokens.
  - Intentionally violated constraints:
    The current-user authentication requirement was violated.

Endpoint coverage:
- Covers:
  `POST /v1/activities/{id}/rating`
- Distinct meaning:
  Set the current user's v1 rating/favourite state.
- Covers:
  `POST /v2/activities/{id}/rating`
- Distinct meaning:
  Set the current user's v2 rating/favourite state.

### Function 12: read own activity rating

Function name:
read own activity rating

Core endpoint(s):
- `GET /v1/activities/{id}/rating`
- `GET /v2/activities/{id}/rating`

Preconditions:
- An activity exists in the database. This can be satisfied by directly inserting an activity row or by calling `POST /v1/activities` or `POST /v2/activities` with an `ActivityProperties` body.
- A rating or favourite state exists for the same authenticated user and activity. This can be satisfied by directly inserting the user-activity rating row or by calling `POST /v1/activities/{id}/rating` or `POST /v2/activities/{id}/rating` with an `ActivityRatingAttrs` body using the same activity id and current-user credential.
- The `{id}` path value must identify the activity with that user-specific rating state. If API calls are used to establish the state, reuse the activity id returned by activity creation and the same authenticated user for both setting and reading the rating.

Successful execution:
- Result:
  The function retrieves the current end user's rating/favourite state for an activity.
- Invocation:
  Step 1: `GET /v1/activities/{id}/rating` with required path `{id}`, a valid current-user credential, and optional query `attrs`
  or
  Step 1: `GET /v2/activities/{id}/rating` with required path `{id}`, a valid current-user credential, and optional query `attrs`
- Constraints:
  The activity id and authenticated user must match the stored rating state.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An activity exists in the database. This can be satisfied by direct insertion or by calling `POST /v1/activities` or `POST /v2/activities`.
    - No rating state exists for the current user and that activity. This can be produced by direct database absence or by intentionally not calling `POST /v1/activities/{id}/rating` or `POST /v2/activities/{id}/rating` for the same user.
  - Failure endpoint:
    `GET /v1/activities/{id}/rating`
  - Why this fails:
    The read targets the current user's rating state, but no rating exists for that user/activity pair.
  - Intentionally violated constraints:
    The required same-user rating state was omitted.
- Branch 2:
  - Preconditions:
    - An activity exists in the database. This can be satisfied by direct insertion or by calling `POST /v1/activities` or `POST /v2/activities`.
    - A rating state exists for a user and the activity. This can be satisfied by direct insertion or by calling `POST /v1/activities/{id}/rating` or `POST /v2/activities/{id}/rating`.
    - The failing request omits credentials or uses credentials that do not identify the user owning that rating state.
  - Failure endpoint:
    `GET /v1/activities/{id}/rating`
  - Why this fails:
    The rating is user-specific, and the source authenticators return no principal for invalid credentials.
  - Intentionally violated constraints:
    The current-user authentication and same-user rating ownership requirements were violated.

Endpoint coverage:
- Covers:
  `GET /v1/activities/{id}/rating`
- Distinct meaning:
  Read the current user's v1 activity rating.
- Covers:
  `GET /v2/activities/{id}/rating`
- Distinct meaning:
  Read the current user's v2 activity rating.

### Function 13: remove own activity rating

Function name:
remove own activity rating

Core endpoint(s):
- `DELETE /v1/activities/{id}/rating`
- `DELETE /v2/activities/{id}/rating`

Preconditions:
- An activity exists in the database. This can be satisfied by directly inserting an activity row or by calling `POST /v1/activities` or `POST /v2/activities` with an `ActivityProperties` body.
- A rating or favourite state exists for the same authenticated user and activity. This can be satisfied by directly inserting the user-activity rating row or by calling `POST /v1/activities/{id}/rating` or `POST /v2/activities/{id}/rating` with an `ActivityRatingAttrs` body using the same current-user credential.
- The `{id}` path value must identify that activity, and the delete request must authenticate as the same user that owns the rating state.

Successful execution:
- Result:
  The function removes the current end user's rating from an activity.
- Invocation:
  Step 1: `DELETE /v1/activities/{id}/rating` with required path `{id}` and a valid current-user credential
  or
  Step 1: `DELETE /v2/activities/{id}/rating` with required path `{id}` and a valid current-user credential
- Constraints:
  The stored rating state is scoped by both activity id and authenticated user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No activity exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/activities` or `POST /v2/activities`.
  - Failure endpoint:
    `DELETE /v1/activities/{id}/rating`
  - Why this fails:
    Rating deletion is scoped to a specific activity id, but no activity state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing activity.
- Branch 2:
  - Preconditions:
    - An activity exists in the database. This can be satisfied by direct insertion or by calling `POST /v1/activities` or `POST /v2/activities`.
    - A rating state exists for a user and the activity. This can be satisfied by direct insertion or by calling `POST /v1/activities/{id}/rating` or `POST /v2/activities/{id}/rating`.
    - The failing request omits credentials or uses credentials that do not identify the user owning that rating state.
  - Failure endpoint:
    `DELETE /v1/activities/{id}/rating`
  - Why this fails:
    The rating being removed is the current user's rating, and the source authenticator requires a valid user identity.
  - Intentionally violated constraints:
    The current-user authentication and same-user rating ownership requirements were violated.

Endpoint coverage:
- Covers:
  `DELETE /v1/activities/{id}/rating`
- Distinct meaning:
  Remove the current user's v1 activity rating.
- Covers:
  `DELETE /v2/activities/{id}/rating`
- Distinct meaning:
  Remove the current user's v2 activity rating.

### Function 14: list favourite activity ids

Function name:
list favourite activity ids

Core endpoint(s):
- `GET /v1/favourites`

Preconditions:
- An activity exists in the database. This can be satisfied by directly inserting an activity row or by calling `POST /v1/activities` with an `ActivityProperties` body and recording the returned `id`.
- The authenticated user has a favourite relationship to that activity. This can be satisfied by directly inserting favourite state linked to the same user and activity or by calling `POST /v1/favourites` with a `PutFavouritesEntity` body whose `id` array contains the activity id.
- The request must authenticate as the same user whose favourite ids are being listed.

Successful execution:
- Result:
  The function returns the current user's favourite activity ids.
- Invocation:
  Step 1: `GET /v1/favourites` with a valid current-user credential
- Constraints:
  Swagger incorrectly declares a required path parameter named `id` for `/v1/favourites`, but the path has no `{id}` segment.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An activity exists in the database. This can be satisfied by direct insertion or by calling `POST /v1/activities`.
    - The current user has favourite state for that activity. This can be satisfied by direct insertion or by calling `POST /v1/favourites` with body `id=[{activityId}]`.
    - The failing request omits credentials or uses credentials that do not match a stored `API` identity and are not a valid Google id token accepted by source authentication.
  - Failure endpoint:
    `GET /v1/favourites`
  - Why this fails:
    Favourites are user content, and the source authenticator returns no current user for invalid credentials.
  - Intentionally violated constraints:
    The current-user authentication requirement was violated.

Endpoint coverage:
- Covers:
  `GET /v1/favourites`
- Distinct meaning:
  Read the current user's favourite activity id list.

### Function 15: replace favourite activity id list

Function name:
replace favourite activity id list

Core endpoint(s):
- `POST /v1/favourites`

Preconditions:
- Each activity id that should become a favourite exists in the database. This can be satisfied by directly inserting activity rows or by calling `POST /v1/activities` with an `ActivityProperties` body for each activity and recording the returned ids.
- The request must authenticate as the user whose favourite list is being replaced. This can be satisfied by directly inserting a `User` with a `UserIdentity` of type `API` or by using a valid Google id token accepted by source authentication.

Successful execution:
- Result:
  The function replaces the current user's favourite activity id list.
- Invocation:
  Step 1: `POST /v1/favourites` with a valid current-user credential and a `PutFavouritesEntity` body whose `id` field is an array of activity ids
- Constraints:
  Swagger's required path parameter `id` is inconsistent with the path `/v1/favourites`, which has no path variable.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - One or more target activities exist in the database. This can be satisfied by direct insertion or by calling `POST /v1/activities`.
    - The failing request omits credentials or uses credentials that do not match a stored `API` identity and are not a valid Google id token accepted by source authentication.
  - Failure endpoint:
    `POST /v1/favourites`
  - Why this fails:
    The operation modifies user-specific content, and `ApiKeyAuthenticator` only authenticates users with a matching API identity.
  - Intentionally violated constraints:
    The current-user authentication requirement was violated.

Endpoint coverage:
- Covers:
  `POST /v1/favourites`
- Distinct meaning:
  Replace the current user's favourite activity ids.

### Function 16: list or search media files

Function name:
list or search media files

Core endpoint(s):
- `GET /v1/media_files`

Preconditions:
- None; this collection query can run without pre-existing media-file state and can validly return an empty result set.

Successful execution:
- Result:
  The function lists media files referenced in activities, optionally filtered by URI text.
- Invocation:
  Step 1: `GET /v1/media_files` with optional query values `uri` and `attrs`
- Constraints:
  Optional `uri` filters media files by case-sensitive substring match in their URI. Optional `attrs` controls response attributes.

Failure or exceptional branches:
- No concrete failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `GET /v1/media_files`
- Distinct meaning:
  List or search media-file metadata.

### Function 17: add media file

Function name:
add media file

Core endpoint(s):
- `POST /v1/media_files`

Preconditions:
- None; this endpoint creates the media-file state. If authentication and authorization are enforced by hidden resource code, the source defines media item permissions, but endpoint enforcement is not visible.

Successful execution:
- Result:
  The function adds a media file to the system and returns its metadata.
- Invocation:
  Step 1: `POST /v1/media_files` with a `MediaFile` body containing a `uri` or a data URI for base64-encoded content
- Constraints:
  Swagger says callers may specify a media file URL in `uri` or use a data URI to upload a base64-encoded file. The response returns a media-file `id`.

Failure or exceptional branches:
- No concrete creation failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `POST /v1/media_files`
- Distinct meaning:
  Create media-file metadata and optionally upload content via data URI.

### Function 18: read media file metadata

Function name:
read media file metadata

Core endpoint(s):
- `GET /v1/media_files/{id}`

Preconditions:
- A media file exists in the database. This can be satisfied by directly inserting a media-file row or by calling `POST /v1/media_files` with a `MediaFile` body containing a URL or data URI.
- The `{id}` path value must identify that media file. If the API is used to establish the media file, `{id}` must be taken from the media-file creation response.

Successful execution:
- Result:
  The function retrieves metadata for a specific media file.
- Invocation:
  Step 1: `GET /v1/media_files/{id}` with required path `{id}` and optional query `attrs`
- Constraints:
  `{id}` must identify an existing media file.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No media file exists for `{id}`. This can be produced by starting from an empty database, deleting the media file beforehand, or intentionally not inserting it directly and not calling `POST /v1/media_files`.
  - Failure endpoint:
    `GET /v1/media_files/{id}`
  - Why this fails:
    The read targets a specific media-file id, but no media-file state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing media file.

Endpoint coverage:
- Covers:
  `GET /v1/media_files/{id}`
- Distinct meaning:
  Read media-file metadata.

### Function 19: update media file metadata

Function name:
update media file metadata

Core endpoint(s):
- `PUT /v1/media_files/{id}`

Preconditions:
- A media file exists in the database. This can be satisfied by directly inserting a media-file row or by calling `POST /v1/media_files` with a `MediaFile` body containing a URL or data URI.
- The `{id}` path value must identify that media file. If the API is used to establish the media file, `{id}` must be taken from the media-file creation response.

Successful execution:
- Result:
  The function updates metadata for an existing media file.
- Invocation:
  Step 1: `PUT /v1/media_files/{id}` with required path `{id}` and a `MediaFile` body
- Constraints:
  `{id}` must identify an existing media file.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No media file exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/media_files` before update.
  - Failure endpoint:
    `PUT /v1/media_files/{id}`
  - Why this fails:
    The update targets a specific media-file id, but no media-file state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing media file.

Endpoint coverage:
- Covers:
  `PUT /v1/media_files/{id}`
- Distinct meaning:
  Update media-file metadata.

### Function 20: force-delete media file

Function name:
force-delete media file

Core endpoint(s):
- `DELETE /v1/media_files/{id}`

Preconditions:
- A media file exists in the database. This can be satisfied by directly inserting a media-file row or by calling `POST /v1/media_files` with a `MediaFile` body and recording the returned `id`.
- An activity references that media file. This can be satisfied by directly inserting an activity row with a relationship to the media-file id or by calling `POST /v1/activities` with an `ActivityProperties` body whose `media_files` value references that media file.
- The `{id}` path value used by the delete request must identify the media file. If the API is used to establish the media file, `{id}` must be taken from the media-file creation response.

Successful execution:
- Result:
  The function deletes a media file even when it is referenced by activities.
- Invocation:
  Step 1: `DELETE /v1/media_files/{id}` with required path `{id}` and query `verify_unused` absent or `false`
- Constraints:
  Swagger says deletion defaults to deleting even if referenced by activities, so `verify_unused` must be absent or false for this force-delete meaning.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No media file exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/media_files` before deletion.
  - Failure endpoint:
    `DELETE /v1/media_files/{id}`
  - Why this fails:
    The delete targets a specific media-file id, but no media-file state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing media file.

Endpoint coverage:
- Covers:
  `DELETE /v1/media_files/{id}`
- Distinct meaning:
  Delete media without checking whether activities reference it.

### Function 21: delete media file only when unused

Function name:
delete unused media file

Core endpoint(s):
- `DELETE /v1/media_files/{id}`

Preconditions:
- A media file exists in the database. This can be satisfied by directly inserting a media-file row or by calling `POST /v1/media_files` with a `MediaFile` body and recording the returned `id`.
- No activity references that media file. This can be satisfied by direct database setup with no activity-media relationship, or by not creating an activity with `media_files` referencing the media-file id.
- The `{id}` path value used by the delete request must identify the media file. If the API is used to establish the media file, `{id}` must be taken from the media-file creation response.

Successful execution:
- Result:
  The function deletes a media file only after verifying it is not referenced by activities.
- Invocation:
  Step 1: `DELETE /v1/media_files/{id}` with required path `{id}` and query `verify_unused=true`
- Constraints:
  The media file must not be referenced by any activity when `verify_unused=true`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A media file exists in the database. This can be satisfied by directly inserting a media-file row or by calling `POST /v1/media_files`.
    - An activity references that media file. This can be satisfied by directly inserting an activity-media relationship or by calling `POST /v1/activities` with an `ActivityProperties` body whose `media_files` includes the media-file id.
    - The delete request uses the media-file id from that state and query `verify_unused=true`.
  - Failure endpoint:
    `DELETE /v1/media_files/{id}`
  - Why this fails:
    Swagger says `verify_unused=true` verifies the media file is not referenced before deleting it.
  - Intentionally violated constraints:
    The unused-media requirement was violated by existing activity references.

Endpoint coverage:
- Covers:
  `DELETE /v1/media_files/{id}`
- Distinct meaning:
  Conditional delete with unused verification.

### Function 22: download media file content

Function name:
download media file content

Core endpoint(s):
- `GET /v1/media_files/{id}/file`

Preconditions:
- A media file exists in the database and has downloadable content or a resolvable URI. This can be satisfied by directly inserting a media-file row with content/URI metadata or by calling `POST /v1/media_files` with a `MediaFile` body containing a URL or data URI.
- The `{id}` path value must identify that media file. If the API is used to establish the media file, `{id}` must be taken from the media-file creation response.

Successful execution:
- Result:
  The function downloads the binary content of a media file.
- Invocation:
  Step 1: `GET /v1/media_files/{id}/file` with required path `{id}` and no `size` query
- Constraints:
  The response produces `application/octet-stream`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No media file exists for `{id}`. This can be produced by starting from an empty database, deleting the media file beforehand, or intentionally not inserting it directly and not calling `POST /v1/media_files`.
  - Failure endpoint:
    `GET /v1/media_files/{id}/file`
  - Why this fails:
    File download targets a specific media-file id, but no media-file state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing media file.

Endpoint coverage:
- Covers:
  `GET /v1/media_files/{id}/file`
- Distinct meaning:
  Download original media content.

### Function 23: download resized image media file

Function name:
download resized image media file

Core endpoint(s):
- `GET /v1/media_files/{id}/file`

Preconditions:
- An image media file exists in the database and has downloadable image content or a resolvable image URI. This can be satisfied by directly inserting a media-file row with image metadata/content or by calling `POST /v1/media_files` with a `MediaFile` body containing an image URL or image data URI.
- The `{id}` path value must identify that image media file. If the API is used to establish the media file, `{id}` must be taken from the media-file creation response.

Successful execution:
- Result:
  The function downloads an image media file resized so that width/height do not exceed the requested size; images are not enlarged.
- Invocation:
  Step 1: `GET /v1/media_files/{id}/file` with required path `{id}` and query `size={integer}`
- Constraints:
  Swagger says `size` is rounded up to the next power of two, such as 256, 512, or 1024.

Failure or exceptional branches:
- No concrete non-image or invalid-size branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `GET /v1/media_files/{id}/file`
- Distinct meaning:
  Download resized image content using the `size` query parameter.

### Function 24: list or search system messages

Function name:
list or search system messages

Core endpoint(s):
- `GET /v1/system_messages`

Preconditions:
- None; this collection query can run without pre-existing system-message state and can validly return an empty result set.

Successful execution:
- Result:
  The function returns system messages, optionally filtered by key or validity.
- Invocation:
  Step 1: `GET /v1/system_messages` with optional query values `key`, `valid`, and `attrs`
- Constraints:
  The visible source defines `system_message_read` permission at level `-100`, but endpoint security is not documented in Swagger or visible controller code.

Failure or exceptional branches:
- No concrete failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `GET /v1/system_messages`
- Distinct meaning:
  List or filter system messages.

### Function 25: create system message

Function name:
create system message

Core endpoint(s):
- `POST /v1/system_messages`

Preconditions:
- None; this endpoint creates the system-message state. If authentication and authorization are enforced by hidden resource code, the visible source defines `system_message_manage` at administrator level `20`, but endpoint enforcement is not visible.

Successful execution:
- Result:
  The function creates a system message.
- Invocation:
  Step 1: `POST /v1/system_messages` with a `SystemMessage` body containing fields such as `key`, `value`, `valid_from`, and `valid_to`
- Constraints:
  Swagger documents the body schema but no concrete required-field validation. The source defines the management permission constant but does not show endpoint enforcement.

Failure or exceptional branches:
- No concrete creation failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `POST /v1/system_messages`
- Distinct meaning:
  Create a system message.

### Function 26: read system message

Function name:
read system message

Core endpoint(s):
- `GET /v1/system_messages/{id}`

Preconditions:
- A system message exists in the database. This can be satisfied by directly inserting a system-message row or by calling `POST /v1/system_messages` with a `SystemMessage` body.
- The `{id}` path value must identify that system message. If the API is used to establish the system message, `{id}` must be taken from the system-message creation response.

Successful execution:
- Result:
  The function retrieves one system message by id.
- Invocation:
  Step 1: `GET /v1/system_messages/{id}` with required path `{id}` and optional query `attrs`
- Constraints:
  `{id}` must identify an existing system message.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No system message exists for `{id}`. This can be produced by starting from an empty database, deleting the system message beforehand, or intentionally not inserting it directly and not calling `POST /v1/system_messages`.
  - Failure endpoint:
    `GET /v1/system_messages/{id}`
  - Why this fails:
    The read targets a specific system-message id, but no system-message state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing system message.

Endpoint coverage:
- Covers:
  `GET /v1/system_messages/{id}`
- Distinct meaning:
  Read one system message.

### Function 27: update system message

Function name:
update system message

Core endpoint(s):
- `PUT /v1/system_messages/{id}`

Preconditions:
- A system message exists in the database. This can be satisfied by directly inserting a system-message row or by calling `POST /v1/system_messages` with a `SystemMessage` body.
- The `{id}` path value must identify that system message. If the API is used to establish the system message, `{id}` must be taken from the system-message creation response.

Successful execution:
- Result:
  The function updates an existing system message.
- Invocation:
  Step 1: `PUT /v1/system_messages/{id}` with required path `{id}` and a `SystemMessage` body
- Constraints:
  `{id}` must identify an existing system message. If authorization is enforced by hidden resource code, the visible source defines `system_message_manage` at administrator level `20`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No system message exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/system_messages` before update.
  - Failure endpoint:
    `PUT /v1/system_messages/{id}`
  - Why this fails:
    The update targets a specific system-message id, but no system-message state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing system message.

Endpoint coverage:
- Covers:
  `PUT /v1/system_messages/{id}`
- Distinct meaning:
  Update a system message.

### Function 28: delete system message

Function name:
delete system message

Core endpoint(s):
- `DELETE /v1/system_messages/{id}`

Preconditions:
- A system message exists in the database. This can be satisfied by directly inserting a system-message row or by calling `POST /v1/system_messages` with a `SystemMessage` body.
- The `{id}` path value must identify that system message. If the API is used to establish the system message, `{id}` must be taken from the system-message creation response.

Successful execution:
- Result:
  The function deletes an existing system message.
- Invocation:
  Step 1: `DELETE /v1/system_messages/{id}` with required path `{id}`
- Constraints:
  `{id}` must identify the system message to delete. If authorization is enforced by hidden resource code, the visible source defines `system_message_manage` at administrator level `20`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No system message exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/system_messages` before deletion.
  - Failure endpoint:
    `DELETE /v1/system_messages/{id}`
  - Why this fails:
    The delete targets a specific system-message id, but no system-message state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing system message.

Endpoint coverage:
- Covers:
  `DELETE /v1/system_messages/{id}`
- Distinct meaning:
  Delete a system message.

### Function 29: check system health

Function name:
check system health

Core endpoint(s):
- `GET /v1/system/ping`

Preconditions:
- None; no request parameters or resource state are documented.

Successful execution:
- Result:
  The function checks whether the API system responds to a ping/status request.
- Invocation:
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

### Function 30: list roles and permission levels

Function name:
list roles and permission levels

Core endpoint(s):
- `GET /v1/system/roles`

Preconditions:
- None; no setup endpoint or resource state is required by Swagger for this metadata request.

Successful execution:
- Result:
  The function returns role and permission level metadata.
- Invocation:
  Step 1: `GET /v1/system/roles`
- Constraints:
  The response schema is `RolesView`, containing `permission_levels` and `role_levels`. The visible source defines role levels `limited_user=-1`, `user=0`, `moderator=10`, and `administrator=20`, and permissions such as `activity_create`, `rating_set_own`, `category_create`, `system_message_manage`, `auth_role_list`, and `auth_user_create`.

Failure or exceptional branches:
- No endpoint-specific failure branch is visible. The source defines `auth_role_list` at administrator level `20`, but the allowed files do not show that `/v1/system/roles` enforces it.

Endpoint coverage:
- Covers:
  `GET /v1/system/roles`
- Distinct meaning:
  Retrieve system role and permission metadata.

### Function 31: list or search tags/categories

Function name:
list or search tags/categories

Core endpoint(s):
- `GET /v1/categories`
- `GET /v2/tags`

Preconditions:
- None; this collection query can run without pre-existing tag/category state and can validly return an empty result set.

Successful execution:
- Result:
  The function returns tags or categories, optionally filtered by group, name, or minimum activity count.
- Invocation:
  Step 1: `GET /v1/categories` with optional query values `group`, `name`, `min_activities_count`, and `attrs`
  or
  Step 1: `GET /v2/tags` with optional query values `group`, `name`, `min_activities_count`, and `attrs`
- Constraints:
  Both versions use the `Tag` schema.

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

### Function 32: create tag/category

Function name:
create tag/category

Core endpoint(s):
- `POST /v1/categories`
- `POST /v2/tags`

Preconditions:
- None; this endpoint creates the tag/category state. If authentication and authorization are enforced by hidden resource code, the visible source defines `category_create` at moderator level `10`, but endpoint enforcement is not visible.

Successful execution:
- Result:
  The function creates a tag/category.
- Invocation:
  Step 1: `POST /v1/categories` with a `Tag` body
  or
  Step 1: `POST /v2/tags` with a `Tag` body
- Constraints:
  The `Tag` schema includes fields such as `group`, `name`, optional `media_file`, and `activities_count`.

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

### Function 33: read tag/category

Function name:
read tag/category

Core endpoint(s):
- `GET /v1/categories/{id}`
- `GET /v2/tags/{id}`

Preconditions:
- A tag/category exists in the database. This can be satisfied by directly inserting a tag/category row or by calling `POST /v1/categories` or `POST /v2/tags` with a `Tag` body.
- The `{id}` path value must identify that tag/category. If the API is used to establish the tag/category, `{id}` must be taken from the creation response.

Successful execution:
- Result:
  The function retrieves one tag/category by id.
- Invocation:
  Step 1: `GET /v1/categories/{id}` with required path `{id}` and optional query `attrs`
  or
  Step 1: `GET /v2/tags/{id}` with required path `{id}` and optional query `attrs`
- Constraints:
  `{id}` must identify an existing tag/category.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No tag/category exists for `{id}`. This can be produced by starting from an empty database, deleting the tag/category beforehand, or intentionally not inserting it directly and not calling `POST /v1/categories` or `POST /v2/tags`.
  - Failure endpoint:
    `GET /v1/categories/{id}`
  - Why this fails:
    The read targets a specific category id, but no category state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing tag/category.

Endpoint coverage:
- Covers:
  `GET /v1/categories/{id}`
- Distinct meaning:
  Read one v1 category.
- Covers:
  `GET /v2/tags/{id}`
- Distinct meaning:
  Read one v2 tag.

### Function 34: update tag/category

Function name:
update tag/category

Core endpoint(s):
- `PUT /v1/categories/{id}`
- `PUT /v2/tags/{id}`

Preconditions:
- A tag/category exists in the database. This can be satisfied by directly inserting a tag/category row or by calling `POST /v1/categories` or `POST /v2/tags` with a `Tag` body.
- The `{id}` path value must identify that tag/category. If the API is used to establish the tag/category, `{id}` must be taken from the creation response.

Successful execution:
- Result:
  The function updates an existing tag/category.
- Invocation:
  Step 1: `PUT /v1/categories/{id}` with required path `{id}` and a `Tag` body
  or
  Step 1: `PUT /v2/tags/{id}` with required path `{id}` and a `Tag` body
- Constraints:
  `{id}` must identify an existing tag/category. If authorization is enforced by hidden resource code, the visible source defines `category_edit` at moderator level `10`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No tag/category exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/categories` or `POST /v2/tags` before update.
  - Failure endpoint:
    `PUT /v1/categories/{id}`
  - Why this fails:
    The update targets a specific category id, but no category state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing tag/category.

Endpoint coverage:
- Covers:
  `PUT /v1/categories/{id}`
- Distinct meaning:
  Update a v1 category.
- Covers:
  `PUT /v2/tags/{id}`
- Distinct meaning:
  Update a v2 tag.

### Function 35: delete tag/category

Function name:
delete tag/category

Core endpoint(s):
- `DELETE /v1/categories/{id}`
- `DELETE /v2/tags/{id}`

Preconditions:
- A tag/category exists in the database. This can be satisfied by directly inserting a tag/category row or by calling `POST /v1/categories` or `POST /v2/tags` with a `Tag` body.
- The `{id}` path value must identify that tag/category. If the API is used to establish the tag/category, `{id}` must be taken from the creation response.

Successful execution:
- Result:
  The function deletes an existing tag/category.
- Invocation:
  Step 1: `DELETE /v1/categories/{id}` with required path `{id}`
  or
  Step 1: `DELETE /v2/tags/{id}` with required path `{id}`
- Constraints:
  `{id}` must identify the tag/category to delete. If authorization is enforced by hidden resource code, the visible source defines `category_edit` at moderator level `10`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No tag/category exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/categories` or `POST /v2/tags` before deletion.
  - Failure endpoint:
    `DELETE /v1/categories/{id}`
  - Why this fails:
    The delete targets a specific category id, but no category state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing tag/category.

Endpoint coverage:
- Covers:
  `DELETE /v1/categories/{id}`
- Distinct meaning:
  Delete a v1 category.
- Covers:
  `DELETE /v2/tags/{id}`
- Distinct meaning:
  Delete a v2 tag.

### Function 36: list or search users

Function name:
list or search users

Core endpoint(s):
- `GET /v1/users`

Preconditions:
- None; this collection query can run without pre-existing user state and can validly return an empty result set, subject to any hidden authorization enforcement.

Successful execution:
- Result:
  The function returns users, optionally filtered by name.
- Invocation:
  Step 1: `GET /v1/users` with optional query values `name` and `attrs`
- Constraints:
  Optional `name` filters users. Optional `attrs` controls response attributes. The visible source defines user-related administrator permissions, but endpoint enforcement is not visible.

Failure or exceptional branches:
- No concrete failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `GET /v1/users`
- Distinct meaning:
  List or search users.

### Function 37: create user

Function name:
create user

Core endpoint(s):
- `POST /v1/users`

Preconditions:
- None; this endpoint creates the user state. If authorization is enforced by hidden resource code, the visible source defines `auth_user_create` at administrator level `20`, but endpoint enforcement is not visible.

Successful execution:
- Result:
  The function creates a user.
- Invocation:
  Step 1: `POST /v1/users` with a `User` body containing fields such as `name`, `email_address`, `authorization_level`, and `identities`
- Constraints:
  `UserIdentity.type` may be `API` or `GOOGLE`.

Failure or exceptional branches:
- No concrete creation failure branch is specified by Swagger or visible implementation.

Endpoint coverage:
- Covers:
  `POST /v1/users`
- Distinct meaning:
  Create a user.

### Function 38: read user

Function name:
read user

Core endpoint(s):
- `GET /v1/users/{id}`

Preconditions:
- A user exists in the database. This can be satisfied by directly inserting a user row or by calling `POST /v1/users` with a `User` body.
- The `{id}` path value must identify that user. If the API is used to establish the user, `{id}` must be taken from the user creation response.

Successful execution:
- Result:
  The function retrieves one user by id.
- Invocation:
  Step 1: `GET /v1/users/{id}` with required path `{id}` and optional query `attrs`
- Constraints:
  `{id}` must identify an existing user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user exists for `{id}`. This can be produced by starting from an empty database, deleting the user beforehand, or intentionally not inserting it directly and not calling `POST /v1/users`.
  - Failure endpoint:
    `GET /v1/users/{id}`
  - Why this fails:
    The read targets a specific user id, but no user state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing user.

Endpoint coverage:
- Covers:
  `GET /v1/users/{id}`
- Distinct meaning:
  Read one user.

### Function 39: update user

Function name:
update user

Core endpoint(s):
- `PUT /v1/users/{id}`

Preconditions:
- A user exists in the database. This can be satisfied by directly inserting a user row or by calling `POST /v1/users` with a `User` body.
- The `{id}` path value must identify that user. If the API is used to establish the user, `{id}` must be taken from the user creation response.

Successful execution:
- Result:
  The function updates an existing user.
- Invocation:
  Step 1: `PUT /v1/users/{id}` with required path `{id}` and a `User` body
- Constraints:
  `{id}` must identify an existing user. If authorization is enforced by hidden resource code, the visible source defines `auth_user_edit` and role-assignment permissions.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/users` before update.
  - Failure endpoint:
    `PUT /v1/users/{id}`
  - Why this fails:
    The update targets a specific user id, but no user state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing user.

Endpoint coverage:
- Covers:
  `PUT /v1/users/{id}`
- Distinct meaning:
  Update a user.

### Function 40: delete user

Function name:
delete user

Core endpoint(s):
- `DELETE /v1/users/{id}`

Preconditions:
- A user exists in the database. This can be satisfied by directly inserting a user row or by calling `POST /v1/users` with a `User` body.
- The `{id}` path value must identify that user. If the API is used to establish the user, `{id}` must be taken from the user creation response.

Successful execution:
- Result:
  The function deletes an existing user.
- Invocation:
  Step 1: `DELETE /v1/users/{id}` with required path `{id}`
- Constraints:
  `{id}` must identify the user to delete. If authorization is enforced by hidden resource code, the visible source defines `auth_user_edit` at administrator level `20`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user exists for `{id}`. This can be produced by direct database absence or by intentionally not calling `POST /v1/users` before deletion.
  - Failure endpoint:
    `DELETE /v1/users/{id}`
  - Why this fails:
    The delete targets a specific user id, but no user state exists for that id.
  - Intentionally violated constraints:
    `{id}` does not identify an existing user.

Endpoint coverage:
- Covers:
  `DELETE /v1/users/{id}`
- Distinct meaning:
  Delete a user.

### Function 41: retrieve authenticated user profile

Function name:
retrieve authenticated user profile

Core endpoint(s):
- `GET /v1/users/profile`

Preconditions:
- A user exists with an identity usable for authentication. This can be satisfied by directly inserting a `User` row and a linked `UserIdentity` of type `API`, or by calling `POST /v1/users` with a `User` body whose `identities` includes an `API` identity value.
- The request credential must match that user identity. For API-key authentication, the credential must equal the stored `API` identity value. For Google authentication, the id token must be valid, have the configured audience, and pass any configured accepted-application check.
- If Google authentication is used and the user does not already exist, the visible source can create a new user from the token subject, name, and email, and add an API key if missing; Swagger does not document this authentication exchange.

Successful execution:
- Result:
  The function returns the current authenticated user's profile, including role and role permissions.
- Invocation:
  Step 1: `GET /v1/users/profile` with a valid current-user credential
- Constraints:
  Swagger incorrectly declares a required path parameter `id` for `/v1/users/profile`, but the path has no `{id}` segment. `ApiKeyAuthenticator` reads users by `IdentityType.API`; `GoogleAuthenticator` verifies the token and may create missing user/API-key state.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A user may exist in the database, but the failing request omits credentials or uses an API key that does not match any stored `API` identity.
    - If Google authentication is attempted, the id token is invalid, has the wrong audience, has an unaccepted authorized party, or cannot be verified by the configured verifier.
  - Failure endpoint:
    `GET /v1/users/profile`
  - Why this fails:
    `ApiKeyAuthenticator` returns empty when the supplied credential does not match a user `API` identity. `GoogleAuthenticator` returns empty when the Google id token is invalid or rejected by audience/application checks.
  - Intentionally violated constraints:
    The request does not establish a valid current authenticated user.

Endpoint coverage:
- Covers:
  `GET /v1/users/profile`
- Distinct meaning:
  Read the current authenticated user's profile and permissions.
