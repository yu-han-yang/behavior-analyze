### Function 1: list albums

Function name:
list albums

Core endpoint(s):
- `GET /api/albums`

Preconditions:
- No persisted resource state is required before invoking the core endpoint.

Successful execution:
- Result:
  This function returns a paged list of albums.
- Invocation:
  Step 1: `GET /api/albums` with optional query values `page={page}` and `size={size}`
- Constraints:
  Optional query values must satisfy `page >= 0`, `size >= 0`, and `size <= 30`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request uses invalid pagination, such as `page < 0`, `size < 0`, or `size > 30`.
  - Failure endpoint:
    `GET /api/albums`
  - Why this fails:
    The controller and service validate page and size and throw a bad request error when the values are outside the allowed range.
  - Intentionally violated constraints:
    Pagination constraints were violated.

Endpoint coverage:
- Covers:
  `GET /api/albums`
- Distinct meaning:
  Reads the global album collection.

### Function 2: create album

Function name:
create album

Core endpoint(s):
- `POST /api/albums`

Preconditions:
- No persisted resource state is required before invoking the core endpoint.

Successful execution:
- Result:
  This function creates an album owned by the authenticated user.
- Invocation:
  Step 1: `POST /api/albums` with JSON body field `title={albumTitle}`
- Constraints:
  Caller must have `ROLE_USER`. The request body supplies the album title, and the creation response provides the generated album id for later album operations.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No authenticated principal with `ROLE_USER` is supplied for the request. This can be produced by omitting authentication or using a principal whose authorities do not include `ROLE_USER`.
  - Failure endpoint:
    `POST /api/albums`
  - Why this fails:
    `@PreAuthorize("hasRole('USER')")` blocks the call before the service creates an album.
  - Intentionally violated constraints:
    The required user role was omitted.

Endpoint coverage:
- Covers:
  `POST /api/albums`
- Distinct meaning:
  Creates an owned album resource.

### Function 3: get album

Function name:
get album

Core endpoint(s):
- `GET /api/albums/{id}`

Preconditions:
- An album identified by `{id}` exists with `title={albumTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={albumTitle}`. The `{id}` value used by the core endpoint must be the generated album id from the inserted row or creation response.

Successful execution:
- Result:
  This function retrieves the album identified by `{id}`.
- Invocation:
  Step 1: `GET /api/albums/{id}` with path value `id={id}`
- Constraints:
  The path id must identify an existing album.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No album with id `{id}` exists in the database. This can be produced by starting from an empty database, deleting the album beforehand, or intentionally not inserting it directly and not calling `POST /api/albums` for that id.
  - Failure endpoint:
    `GET /api/albums/{id}`
  - Why this fails:
    The service looks up the album by id and throws `ResourceNotFoundException` when it is absent.
  - Intentionally violated constraints:
    The required album state was omitted or the wrong generated id was used.

Endpoint coverage:
- Covers:
  `GET /api/albums/{id}`
- Distinct meaning:
  Reads one album by id.

### Function 4: update album

Function name:
update album

Core endpoint(s):
- `PUT /api/albums/{id}`

Preconditions:
- An album identified by `{id}` exists with `title={oldTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={oldTitle}`. The `{id}` value used by the core endpoint must be the generated album id from the inserted row or creation response.

Successful execution:
- Result:
  This function updates the title of album `{id}`.
- Invocation:
  Step 1: `PUT /api/albums/{id}` with path value `id={id}` and JSON body field `title={newTitle}`
- Constraints:
  The path id must identify an existing album. Caller must be the album owner or have `ROLE_ADMIN`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An album identified by `{id}` exists with `title={oldTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={oldTitle}`. The `{id}` value used by the core endpoint must be the generated album id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who is not the album owner and does not have `ROLE_ADMIN`.
  - Failure endpoint:
    `PUT /api/albums/{id}`
  - Why this fails:
    The service checks album ownership or admin authority before saving and throws an unauthorized error for a different non-admin user.
  - Intentionally violated constraints:
    The ownership/admin requirement was violated.

Endpoint coverage:
- Covers:
  `PUT /api/albums/{id}`
- Distinct meaning:
  Changes an existing album title.

### Function 5: delete album

Function name:
delete album

Core endpoint(s):
- `DELETE /api/albums/{id}`

Preconditions:
- An album identified by `{id}` exists with `title={albumTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={albumTitle}`. The `{id}` value used by the core endpoint must be the generated album id from the inserted row or creation response.

Successful execution:
- Result:
  This function deletes the album identified by `{id}`.
- Invocation:
  Step 1: `DELETE /api/albums/{id}` with path value `id={id}`
- Constraints:
  The path id must identify an existing album. Caller must be the album owner or have `ROLE_ADMIN`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An album identified by `{id}` exists with `title={albumTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={albumTitle}`. The `{id}` value used by the core endpoint must be the generated album id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who is not the album owner and does not have `ROLE_ADMIN`.
  - Failure endpoint:
    `DELETE /api/albums/{id}`
  - Why this fails:
    The service checks ownership or admin authority before deleting and throws an unauthorized error for a different non-admin user.
  - Intentionally violated constraints:
    The ownership/admin requirement was violated.

Endpoint coverage:
- Covers:
  `DELETE /api/albums/{id}`
- Distinct meaning:
  Removes an owned album.

### Function 6: list album photos

Function name:
list album photos

Core endpoint(s):
- `GET /api/albums/{id}/photos`

Preconditions:
- An album identified by `{id}` exists with `title={albumTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={albumTitle}`. The `{id}` value used by the core endpoint must be the generated album id from the inserted row or creation response.

Successful execution:
- Result:
  This function returns a paged list of photos whose album id matches `{id}`.
- Invocation:
  Step 1: `GET /api/albums/{id}/photos` with path value `id={id}` and optional query values `page={page}` and `size={size}`
- Constraints:
  Optional query values must satisfy `page >= 0`, `size >= 0`, and `size <= 30`. The implementation queries photos by album id but does not verify that the album exists.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An album identified by `{id}` exists with `title={albumTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={albumTitle}`. The `{id}` value used by the core endpoint must be the generated album id from the inserted row or creation response.
    - The core request uses invalid pagination, such as `page < 0`, `size < 0`, or `size > 30`.
  - Failure endpoint:
    `GET /api/albums/{id}/photos`
  - Why this fails:
    The photo service validates page and size before returning the album-filtered photo page.
  - Intentionally violated constraints:
    Pagination constraints were violated.

Endpoint coverage:
- Covers:
  `GET /api/albums/{id}/photos`
- Distinct meaning:
  Reads photos filtered by album id.

### Function 7: list categories

Function name:
list categories

Core endpoint(s):
- `GET /api/categories`

Preconditions:
- No persisted resource state is required before invoking the core endpoint.

Successful execution:
- Result:
  This function returns a paged list of categories.
- Invocation:
  Step 1: `GET /api/categories` with optional query values `page={page}` and `size={size}`
- Constraints:
  Optional query values must satisfy `page >= 0`, `size >= 0`, and `size <= 30`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request uses invalid pagination, such as `page < 0`, `size < 0`, or `size > 30`.
  - Failure endpoint:
    `GET /api/categories`
  - Why this fails:
    The category service validates page and size and rejects invalid values.
  - Intentionally violated constraints:
    Pagination constraints were violated.

Endpoint coverage:
- Covers:
  `GET /api/categories`
- Distinct meaning:
  Reads the category collection.

### Function 8: create category

Function name:
create category

Core endpoint(s):
- `POST /api/categories`

Preconditions:
- No persisted resource state is required before invoking the core endpoint.

Successful execution:
- Result:
  This function creates a category.
- Invocation:
  Step 1: `POST /api/categories` with JSON body field `name={categoryName}`
- Constraints:
  Caller must have `ROLE_USER`. The response provides the generated category id for later category and post operations.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No authenticated principal with `ROLE_USER` is supplied for the request. This can be produced by omitting authentication or using a principal whose authorities do not include `ROLE_USER`.
  - Failure endpoint:
    `POST /api/categories`
  - Why this fails:
    Method security blocks the call before the service persists the category.
  - Intentionally violated constraints:
    The required user role was omitted.

Endpoint coverage:
- Covers:
  `POST /api/categories`
- Distinct meaning:
  Creates a category resource.

### Function 9: get category

Function name:
get category

Core endpoint(s):
- `GET /api/categories/{id}`

Preconditions:
- A category identified by `{id}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{id}` value used by later endpoints must be the generated category id from the inserted row or creation response.

Successful execution:
- Result:
  This function retrieves the category identified by `{id}`.
- Invocation:
  Step 1: `GET /api/categories/{id}` with path value `id={id}`
- Constraints:
  The path id must identify an existing category.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No category with id `{id}` exists in the database. This can be produced by starting from an empty database, deleting the category beforehand, or intentionally not inserting it directly and not calling `POST /api/categories` for that id.
  - Failure endpoint:
    `GET /api/categories/{id}`
  - Why this fails:
    The service looks up the category by id and throws `ResourceNotFoundException` when it is absent.
  - Intentionally violated constraints:
    The required category state was omitted or the wrong generated id was used.

Endpoint coverage:
- Covers:
  `GET /api/categories/{id}`
- Distinct meaning:
  Reads one category by id.

### Function 10: update category

Function name:
update category

Core endpoint(s):
- `PUT /api/categories/{id}`

Preconditions:
- A category identified by `{id}` exists with `name={oldCategoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={oldCategoryName}`. The `{id}` value used by later endpoints must be the generated category id from the inserted row or creation response.

Successful execution:
- Result:
  This function updates the name of category `{id}`.
- Invocation:
  Step 1: `PUT /api/categories/{id}` with path value `id={id}` and JSON body field `name={newCategoryName}`
- Constraints:
  The path id must identify an existing category. Caller must be the category creator or have `ROLE_ADMIN`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A category identified by `{id}` exists with `name={oldCategoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={oldCategoryName}`. The `{id}` value used by later endpoints must be the generated category id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who is not the category creator and does not have `ROLE_ADMIN`.
  - Failure endpoint:
    `PUT /api/categories/{id}`
  - Why this fails:
    The service compares `createdBy` with the current user id or requires admin authority before saving.
  - Intentionally violated constraints:
    The creator/admin requirement was violated.

Endpoint coverage:
- Covers:
  `PUT /api/categories/{id}`
- Distinct meaning:
  Changes an existing category name.

### Function 11: delete category

Function name:
delete category

Core endpoint(s):
- `DELETE /api/categories/{id}`

Preconditions:
- A category identified by `{id}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{id}` value used by later endpoints must be the generated category id from the inserted row or creation response.

Successful execution:
- Result:
  This function deletes the category identified by `{id}`.
- Invocation:
  Step 1: `DELETE /api/categories/{id}` with path value `id={id}`
- Constraints:
  The path id must identify an existing category. Caller must be the category creator or have `ROLE_ADMIN`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A category identified by `{id}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{id}` value used by later endpoints must be the generated category id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who is not the category creator and does not have `ROLE_ADMIN`.
  - Failure endpoint:
    `DELETE /api/categories/{id}`
  - Why this fails:
    The service checks creator ownership or admin authority before deleting.
  - Intentionally violated constraints:
    The creator/admin requirement was violated.

Endpoint coverage:
- Covers:
  `DELETE /api/categories/{id}`
- Distinct meaning:
  Removes a category.

### Function 12: list photos

Function name:
list photos

Core endpoint(s):
- `GET /api/photos`

Preconditions:
- No persisted resource state is required before invoking the core endpoint.

Successful execution:
- Result:
  This function returns a paged list of photos.
- Invocation:
  Step 1: `GET /api/photos` with optional query values `page={page}` and `size={size}`
- Constraints:
  Optional query values must satisfy `page >= 0`, `size >= 0`, and `size <= 30`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request uses invalid pagination, such as `page < 0`, `size < 0`, or `size > 30`.
  - Failure endpoint:
    `GET /api/photos`
  - Why this fails:
    The photo service validates page and size and rejects invalid values.
  - Intentionally violated constraints:
    Pagination constraints were violated.

Endpoint coverage:
- Covers:
  `GET /api/photos`
- Distinct meaning:
  Reads the global photo collection.

### Function 13: create photo

Function name:
create photo

Core endpoint(s):
- `POST /api/photos`

Preconditions:
- An album identified by `{albumId}` exists with `title={albumTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={albumTitle}`. The `{albumId}` value used by the core endpoint must be the generated album id from the inserted row or creation response.

Successful execution:
- Result:
  This function creates a photo in album `{albumId}`.
- Invocation:
  Step 1: `POST /api/photos` with JSON body fields `title={photoTitle}`, `url={url}`, `thumbnailUrl={thumbnailUrl}`, and `albumId={albumId}`
- Constraints:
  Caller must have `ROLE_USER` and must own the referenced album. Body fields must satisfy the source validation: title length at least 3, url length at least 10, thumbnailUrl length at least 10, and non-null albumId. The implementation returns HTTP 200 for this creation even though the OpenAPI response is documented as a creation response.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No album with id `{albumId}` exists in the database. This can be produced by starting from an empty database, deleting the album beforehand, or intentionally not inserting it directly and not calling `POST /api/albums` for that id.
  - Failure endpoint:
    `POST /api/photos`
  - Why this fails:
    The service looks up the submitted `albumId` before saving the photo and throws `ResourceNotFoundException` when no album exists.
  - Intentionally violated constraints:
    The required album state was omitted.

Endpoint coverage:
- Covers:
  `POST /api/photos`
- Distinct meaning:
  Creates a photo inside an owned album.

### Function 14: get photo

Function name:
get photo

Core endpoint(s):
- `GET /api/photos/{id}`

Preconditions:
- An album identified by `{albumId}` exists with `title={albumTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={albumTitle}`. The `{albumId}` value used by the core endpoint must be the generated album id from the inserted row or creation response.
- A photo identified by `{id}` exists in album `{albumId}` with `title={photoTitle}`, `url={url}`, and `thumbnailUrl={thumbnailUrl}`. This can be satisfied by directly inserting a `Photo` row linked to album `{albumId}` or by calling `POST /api/photos` as `{albumOwner}` with JSON body fields `title={photoTitle}`, `url={url}`, `thumbnailUrl={thumbnailUrl}`, and `albumId={albumId}`. The `{id}` value used by the core endpoint must be the generated photo id from the inserted row or creation response.

Successful execution:
- Result:
  This function retrieves the photo identified by `{id}`.
- Invocation:
  Step 1: `GET /api/photos/{id}` with path value `id={id}`
- Constraints:
  The path id must identify an existing photo.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No photo with id `{id}` exists in the database. This can be produced by starting from an empty database, deleting the photo beforehand, or intentionally not inserting it directly and not calling `POST /api/photos` for that id.
  - Failure endpoint:
    `GET /api/photos/{id}`
  - Why this fails:
    The service looks up the photo by id and throws `ResourceNotFoundException` when it is absent.
  - Intentionally violated constraints:
    The required photo state was omitted or the wrong generated id was used.

Endpoint coverage:
- Covers:
  `GET /api/photos/{id}`
- Distinct meaning:
  Reads one photo by id.

### Function 15: update photo

Function name:
update photo

Core endpoint(s):
- `PUT /api/photos/{id}`

Preconditions:
- An album identified by `{albumId}` exists with `title={albumTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={albumTitle}`. The `{albumId}` value used by the core endpoint must be the generated album id from the inserted row or creation response.
- A photo identified by `{id}` exists in album `{albumId}` with `title={photoTitle}`, `url={url}`, and `thumbnailUrl={thumbnailUrl}`. This can be satisfied by directly inserting a `Photo` row linked to album `{albumId}` or by calling `POST /api/photos` as `{albumOwner}` with JSON body fields `title={photoTitle}`, `url={url}`, `thumbnailUrl={thumbnailUrl}`, and `albumId={albumId}`. The `{id}` value used by the core endpoint must be the generated photo id from the inserted row or creation response.

Successful execution:
- Result:
  This function updates a photo title, thumbnail URL, and album reference.
- Invocation:
  Step 1: `PUT /api/photos/{id}` with path value `id={id}` and JSON body fields `title={newPhotoTitle}`, `url={ignoredUrl}`, `thumbnailUrl={newThumbnailUrl}`, and `albumId={albumId}`
- Constraints:
  The path id must identify an existing photo. The body albumId must identify an existing album. Caller must own the photo current album or have `ROLE_ADMIN`. Source code updates title, thumbnailUrl, and album, but ignores the submitted url value.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An album identified by `{albumId}` exists with `title={albumTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={albumTitle}`. The `{albumId}` value used by the core endpoint must be the generated album id from the inserted row or creation response.
    - A photo identified by `{id}` exists in album `{albumId}` with `title={photoTitle}`, `url={url}`, and `thumbnailUrl={thumbnailUrl}`. This can be satisfied by directly inserting a `Photo` row linked to album `{albumId}` or by calling `POST /api/photos` as `{albumOwner}` with JSON body fields `title={photoTitle}`, `url={url}`, `thumbnailUrl={thumbnailUrl}`, and `albumId={albumId}`. The `{id}` value used by the core endpoint must be the generated photo id from the inserted row or creation response.
    - No album with id `{newAlbumId}` exists. This can be produced by not inserting an album directly and not calling `POST /api/albums` for `{newAlbumId}`.
  - Failure endpoint:
    `PUT /api/photos/{id}`
  - Why this fails:
    The service validates the requested body `albumId` before loading and saving the photo, and throws `ResourceNotFoundException` when that album is absent.
  - Intentionally violated constraints:
    The replacement album id in the request body did not identify an existing album.

Endpoint coverage:
- Covers:
  `PUT /api/photos/{id}`
- Distinct meaning:
  Edits photo metadata and may move it to another existing album.

### Function 16: delete photo

Function name:
delete photo

Core endpoint(s):
- `DELETE /api/photos/{id}`

Preconditions:
- An album identified by `{albumId}` exists with `title={albumTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={albumTitle}`. The `{albumId}` value used by the core endpoint must be the generated album id from the inserted row or creation response.
- A photo identified by `{id}` exists in album `{albumId}` with `title={photoTitle}`, `url={url}`, and `thumbnailUrl={thumbnailUrl}`. This can be satisfied by directly inserting a `Photo` row linked to album `{albumId}` or by calling `POST /api/photos` as `{albumOwner}` with JSON body fields `title={photoTitle}`, `url={url}`, `thumbnailUrl={thumbnailUrl}`, and `albumId={albumId}`. The `{id}` value used by the core endpoint must be the generated photo id from the inserted row or creation response.

Successful execution:
- Result:
  This function deletes the photo identified by `{id}`.
- Invocation:
  Step 1: `DELETE /api/photos/{id}` with path value `id={id}`
- Constraints:
  The path id must identify an existing photo. Caller must own the photo album or have `ROLE_ADMIN`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An album identified by `{albumId}` exists with `title={albumTitle}` and is owned by `{albumOwner}`. This can be satisfied by directly inserting an `Album` row linked to that user or by calling `POST /api/albums` as `{albumOwner}` with JSON body field `title={albumTitle}`. The `{albumId}` value used by the core endpoint must be the generated album id from the inserted row or creation response.
    - A photo identified by `{id}` exists in album `{albumId}` with `title={photoTitle}`, `url={url}`, and `thumbnailUrl={thumbnailUrl}`. This can be satisfied by directly inserting a `Photo` row linked to album `{albumId}` or by calling `POST /api/photos` as `{albumOwner}` with JSON body fields `title={photoTitle}`, `url={url}`, `thumbnailUrl={thumbnailUrl}`, and `albumId={albumId}`. The `{id}` value used by the core endpoint must be the generated photo id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who does not own the album and does not have `ROLE_ADMIN`.
  - Failure endpoint:
    `DELETE /api/photos/{id}`
  - Why this fails:
    The service checks ownership through the photo album before deleting.
  - Intentionally violated constraints:
    The album ownership/admin requirement was violated.

Endpoint coverage:
- Covers:
  `DELETE /api/photos/{id}`
- Distinct meaning:
  Removes a photo.

### Function 17: list posts

Function name:
list posts

Core endpoint(s):
- `GET /api/posts`

Preconditions:
- No persisted resource state is required before invoking the core endpoint.

Successful execution:
- Result:
  This function returns a paged list of posts.
- Invocation:
  Step 1: `GET /api/posts` with optional query values `page={page}` and `size={size}`
- Constraints:
  Optional query values must satisfy `page >= 0`, `size >= 0`, and `size <= 30`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request uses invalid pagination, such as `page < 0`, `size < 0`, or `size > 30`.
  - Failure endpoint:
    `GET /api/posts`
  - Why this fails:
    The post service validates page and size and throws `BadRequestException` for invalid values.
  - Intentionally violated constraints:
    Pagination constraints were violated.

Endpoint coverage:
- Covers:
  `GET /api/posts`
- Distinct meaning:
  Reads the global post collection.

### Function 18: create post

Function name:
create post

Core endpoint(s):
- `POST /api/posts`

Preconditions:
- A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.

Successful execution:
- Result:
  This function creates a post under an existing category and links or creates requested tag names.
- Invocation:
  Step 1: `POST /api/posts` with JSON body fields `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`
- Constraints:
  Caller must have `ROLE_USER`. Title must be at least 10 characters, body at least 50 characters, and categoryId must identify an existing category. For each tag name in tags, the implementation reuses an existing tag by name or creates one automatically.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No category with id `{categoryId}` exists in the database. This can be produced by starting from an empty database, deleting the category beforehand, or intentionally not inserting it directly and not calling `POST /api/categories` for that id.
  - Failure endpoint:
    `POST /api/posts`
  - Why this fails:
    The service resolves the category id before saving the post and throws `ResourceNotFoundException` when the category is absent.
  - Intentionally violated constraints:
    The required category state was omitted.

Endpoint coverage:
- Covers:
  `POST /api/posts`
- Distinct meaning:
  Creates a post and links or implicitly creates tags by name.

### Function 19: list posts by category

Function name:
list posts by category

Core endpoint(s):
- `GET /api/posts/category/{id}`

Preconditions:
- A category identified by `{id}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{id}` value used by later endpoints must be the generated category id from the inserted row or creation response.

Successful execution:
- Result:
  This function returns a paged list of posts in category `{id}`.
- Invocation:
  Step 1: `GET /api/posts/category/{id}` with path value `id={id}` and optional query values `page={page}` and `size={size}`
- Constraints:
  The category id must exist, and pagination must be valid.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No category with id `{id}` exists in the database. This can be produced by starting from an empty database, deleting the category beforehand, or intentionally not inserting it directly and not calling `POST /api/categories` for that id.
  - Failure endpoint:
    `GET /api/posts/category/{id}`
  - Why this fails:
    The service verifies the category exists before querying posts and throws `ResourceNotFoundException` if it is absent.
  - Intentionally violated constraints:
    The required category state was omitted.

Endpoint coverage:
- Covers:
  `GET /api/posts/category/{id}`
- Distinct meaning:
  Reads posts filtered by category id.

### Function 20: list posts by tag

Function name:
list posts by tag

Core endpoint(s):
- `GET /api/posts/tag/{id}`

Preconditions:
- A tag identified by `{id}` exists with `name={tagName}` and creator `{tagOwner}`. This can be satisfied by directly inserting a `Tag` row with that creator or by calling `POST /api/tags` as `{tagOwner}` with JSON body field `name={tagName}`. The `{id}` value used by later endpoints must be the generated tag id from the inserted row or creation response.

Successful execution:
- Result:
  This function returns a paged list of posts associated with tag `{id}`.
- Invocation:
  Step 1: `GET /api/posts/tag/{id}` with path value `id={id}` and optional query values `page={page}` and `size={size}`
- Constraints:
  The tag id must exist, and pagination must be valid.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No tag with id `{id}` exists in the database. This can be produced by starting from an empty database, deleting the tag beforehand, or intentionally not inserting it directly and not calling `POST /api/tags` for that id.
  - Failure endpoint:
    `GET /api/posts/tag/{id}`
  - Why this fails:
    The service verifies the tag exists before querying posts and throws `ResourceNotFoundException` if it is absent.
  - Intentionally violated constraints:
    The required tag state was omitted.

Endpoint coverage:
- Covers:
  `GET /api/posts/tag/{id}`
- Distinct meaning:
  Reads posts filtered by tag id.

### Function 21: get post

Function name:
get post

Core endpoint(s):
- `GET /api/posts/{id}`

Preconditions:
- A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
- A post with `title={postTitle}` exists under category `{categoryId}` and is owned by `{postOwner}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwner}` with JSON body fields `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
- The `{id}` value used by the core endpoint must identify the post described above. If the API is used to create the post, the implementation response does not include the post id, so `{id}` must be obtained by calling `GET /api/posts` with valid pagination and selecting the created post, for example by matching unique `title={postTitle}`. Direct database setup can satisfy this by using the inserted post id.

Successful execution:
- Result:
  This function retrieves the post identified by `{id}`.
- Invocation:
  Step 1: `GET /api/posts/{id}` with path value `id={id}`
- Constraints:
  The path id must identify an existing post. If the post was created through the API, the id must be discovered separately because the creation response does not include it.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No post with id `{id}` exists in the database. This can be produced by starting from an empty database, deleting the post beforehand, or intentionally not inserting it directly and not calling `POST /api/posts` plus `GET /api/posts` to discover that id.
  - Failure endpoint:
    `GET /api/posts/{id}`
  - Why this fails:
    The service looks up the post by id and throws `ResourceNotFoundException` when it is absent.
  - Intentionally violated constraints:
    The required post state or id-discovery value was omitted.

Endpoint coverage:
- Covers:
  `GET /api/posts/{id}`
- Distinct meaning:
  Reads one post by id.

### Function 22: update post

Function name:
update post

Core endpoint(s):
- `PUT /api/posts/{id}`

Preconditions:
- A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
- A post with `title={postTitle}` exists under category `{categoryId}` and is owned by `{postOwner}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwner}` with JSON body fields `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
- The `{id}` value used by the core endpoint must identify the post described above. If the API is used to create the post, the implementation response does not include the post id, so `{id}` must be obtained by calling `GET /api/posts` with valid pagination and selecting the created post, for example by matching unique `title={postTitle}`. Direct database setup can satisfy this by using the inserted post id.

Successful execution:
- Result:
  This function updates an existing post title, body, and category.
- Invocation:
  Step 1: `PUT /api/posts/{id}` with path value `id={id}` and JSON body fields `title={newTitle}`, `body={newBody}`, `categoryId={categoryId}`, and optional `tags={ignoredTags}`
- Constraints:
  The path id must identify an existing post, and the body categoryId must identify an existing category. Caller must be the post owner or have `ROLE_ADMIN`. The implementation ignores tags on update.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
    - A post with `title={postTitle}` exists under category `{categoryId}` and is owned by `{postOwner}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwner}` with JSON body fields `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
    - The `{id}` value used by the core endpoint must identify the post described above. If the API is used to create the post, the implementation response does not include the post id, so `{id}` must be obtained by calling `GET /api/posts` with valid pagination and selecting the created post, for example by matching unique `title={postTitle}`. Direct database setup can satisfy this by using the inserted post id.
    - The failing request is authenticated as `{otherUser}`, who is not the post owner and does not have `ROLE_ADMIN`.
  - Failure endpoint:
    `PUT /api/posts/{id}`
  - Why this fails:
    The service checks post owner or admin authority before saving after resolving the post and category.
  - Intentionally violated constraints:
    The post ownership/admin requirement was violated.

Endpoint coverage:
- Covers:
  `PUT /api/posts/{id}`
- Distinct meaning:
  Edits post content and category.

### Function 23: delete post

Function name:
delete post

Core endpoint(s):
- `DELETE /api/posts/{id}`

Preconditions:
- A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
- A post with `title={postTitle}` exists under category `{categoryId}` and is owned by `{postOwner}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwner}` with JSON body fields `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
- The `{id}` value used by the core endpoint must identify the post described above. If the API is used to create the post, the implementation response does not include the post id, so `{id}` must be obtained by calling `GET /api/posts` with valid pagination and selecting the created post, for example by matching unique `title={postTitle}`. Direct database setup can satisfy this by using the inserted post id.

Successful execution:
- Result:
  This function deletes the post identified by `{id}`.
- Invocation:
  Step 1: `DELETE /api/posts/{id}` with path value `id={id}`
- Constraints:
  The path id must identify an existing post. Caller must be the post owner or have `ROLE_ADMIN`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
    - A post with `title={postTitle}` exists under category `{categoryId}` and is owned by `{postOwner}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwner}` with JSON body fields `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
    - The `{id}` value used by the core endpoint must identify the post described above. If the API is used to create the post, the implementation response does not include the post id, so `{id}` must be obtained by calling `GET /api/posts` with valid pagination and selecting the created post, for example by matching unique `title={postTitle}`. Direct database setup can satisfy this by using the inserted post id.
    - The failing request is authenticated as `{otherUser}`, who is not the post owner and does not have `ROLE_ADMIN`.
  - Failure endpoint:
    `DELETE /api/posts/{id}`
  - Why this fails:
    The service checks post owner or admin authority before deleting.
  - Intentionally violated constraints:
    The post ownership/admin requirement was violated.

Endpoint coverage:
- Covers:
  `DELETE /api/posts/{id}`
- Distinct meaning:
  Removes a post.

### Function 24: list post comments

Function name:
list post comments

Core endpoint(s):
- `GET /api/posts/{postId}/comments`

Preconditions:
- A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
- A post with `title={postTitle}` exists under category `{categoryId}` and is owned by `{postOwner}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwner}` with JSON body fields `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
- The `{postId}` value used by the core endpoint must identify the post described above. If the API is used to create the post, the implementation response does not include the post id, so `{postId}` must be obtained by calling `GET /api/posts` with valid pagination and selecting the created post, for example by matching unique `title={postTitle}`. Direct database setup can satisfy this by using the inserted post id.

Successful execution:
- Result:
  This function returns a paged list of comments whose post id matches `{postId}`.
- Invocation:
  Step 1: `GET /api/posts/{postId}/comments` with path value `postId={postId}` and optional query values `page={page}` and `size={size}`
- Constraints:
  Optional query values must satisfy `page >= 0`, `size >= 0`, and `size <= 30`. The implementation filters comments by post id but does not verify that the post exists for this listing.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request uses invalid pagination, such as `page < 0`, `size < 0`, or `size > 30`.
  - Failure endpoint:
    `GET /api/posts/{postId}/comments`
  - Why this fails:
    The comment service validates page and size before querying comments.
  - Intentionally violated constraints:
    Pagination constraints were violated.

Endpoint coverage:
- Covers:
  `GET /api/posts/{postId}/comments`
- Distinct meaning:
  Reads comments filtered by post id.

### Function 25: add comment

Function name:
add comment

Core endpoint(s):
- `POST /api/posts/{postId}/comments`

Preconditions:
- A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
- A post with `title={postTitle}` exists under category `{categoryId}` and is owned by `{postOwner}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwner}` with JSON body fields `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
- The `{postId}` value used by the core endpoint must identify the post described above. If the API is used to create the post, the implementation response does not include the post id, so `{postId}` must be obtained by calling `GET /api/posts` with valid pagination and selecting the created post, for example by matching unique `title={postTitle}`. Direct database setup can satisfy this by using the inserted post id.

Successful execution:
- Result:
  This function adds a comment to an existing post.
- Invocation:
  Step 1: `POST /api/posts/{postId}/comments` with path value `postId={postId}` and JSON body field `body={commentBody}`
- Constraints:
  Caller must have `ROLE_USER`. Comment body must be at least 10 characters. The implementation copies comment name and email from the authenticated user, not from request body.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No post with id `{postId}` exists in the database. This can be produced by starting from an empty database, deleting the post beforehand, or intentionally not inserting it directly and not calling `POST /api/posts` plus `GET /api/posts` to discover that id.
  - Failure endpoint:
    `POST /api/posts/{postId}/comments`
  - Why this fails:
    The service verifies the post exists before saving the comment and throws `ResourceNotFoundException` when it is absent.
  - Intentionally violated constraints:
    The required post state and id-discovery value were omitted.

Endpoint coverage:
- Covers:
  `POST /api/posts/{postId}/comments`
- Distinct meaning:
  Creates a comment on a post.

### Function 26: get comment

Function name:
get comment

Core endpoint(s):
- `GET /api/posts/{postId}/comments/{id}`

Preconditions:
- A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
- A post with `title={postTitle}` exists under category `{categoryId}` and is owned by `{postOwner}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwner}` with JSON body fields `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
- The `{postId}` value used by the core endpoint must identify the post described above. If the API is used to create the post, the implementation response does not include the post id, so `{postId}` must be obtained by calling `GET /api/posts` with valid pagination and selecting the created post, for example by matching unique `title={postTitle}`. Direct database setup can satisfy this by using the inserted post id.
- A comment identified by `{id}` exists on post `{postId}` and is owned by `{commentOwner}` with body `body={commentBody}`. This can be satisfied by directly inserting a `Comment` row linked to that post and user, or by calling `POST /api/posts/{postId}/comments` as `{commentOwner}` with path `postId={postId}` and JSON body field `body={commentBody}`. The `{id}` value used by the core endpoint must be the generated comment id from the inserted row or creation response.

Successful execution:
- Result:
  This function retrieves a comment that belongs to the specified post.
- Invocation:
  Step 1: `GET /api/posts/{postId}/comments/{id}` with path values `postId={postId}` and `id={id}`
- Constraints:
  Both the post and comment must exist, and the comment must belong to the path post.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
    - A post with `title={postTitleA}` exists under category `{categoryId}` and is owned by `{postOwnerA}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwnerA}` with JSON body fields `title={postTitleA}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
    - A post with `title={postTitleB}` exists under category `{categoryId}` and is owned by `{postOwnerB}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwnerB}` with JSON body fields `title={postTitleB}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
    - The values `{postIdA}` and `{postIdB}` identify two different posts. If the API is used, they must be discovered by calling `GET /api/posts` after both post creations and matching their unique titles; direct database setup can use inserted ids.
    - A comment identified by `{id}` exists on post `{postIdA}` and is owned by `{commentOwner}` with body `body={commentBody}`. This can be satisfied by directly inserting a `Comment` row linked to that post and user, or by calling `POST /api/posts/{postId}/comments` as `{commentOwner}` with path `postId={postIdA}` and JSON body field `body={commentBody}`. The `{id}` value used by the core endpoint must be the generated comment id from the inserted row or creation response.
  - Failure endpoint:
    `GET /api/posts/{postId}/comments/{id}`
  - Why this fails:
    The service loads the path post and comment, then compares the comment stored post id with the path post id and throws a bad request error when they differ.
  - Intentionally violated constraints:
    The path post/comment ownership relationship was mismatched by using `postId={postIdB}` with a comment created under `postId={postIdA}`.

Endpoint coverage:
- Covers:
  `GET /api/posts/{postId}/comments/{id}`
- Distinct meaning:
  Reads one comment within a post scope.

### Function 27: update comment

Function name:
update comment

Core endpoint(s):
- `PUT /api/posts/{postId}/comments/{id}`

Preconditions:
- A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
- A post with `title={postTitle}` exists under category `{categoryId}` and is owned by `{postOwner}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwner}` with JSON body fields `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
- The `{postId}` value used by the core endpoint must identify the post described above. If the API is used to create the post, the implementation response does not include the post id, so `{postId}` must be obtained by calling `GET /api/posts` with valid pagination and selecting the created post, for example by matching unique `title={postTitle}`. Direct database setup can satisfy this by using the inserted post id.
- A comment identified by `{id}` exists on post `{postId}` and is owned by `{commentOwner}` with body `body={commentBody}`. This can be satisfied by directly inserting a `Comment` row linked to that post and user, or by calling `POST /api/posts/{postId}/comments` as `{commentOwner}` with path `postId={postId}` and JSON body field `body={commentBody}`. The `{id}` value used by the core endpoint must be the generated comment id from the inserted row or creation response.

Successful execution:
- Result:
  This function updates the body of a comment that belongs to the specified post.
- Invocation:
  Step 1: `PUT /api/posts/{postId}/comments/{id}` with path values `postId={postId}` and `id={id}`, plus JSON body field `body={newCommentBody}`
- Constraints:
  The post and comment must exist, the comment must belong to the path post, caller must be the comment owner or have `ROLE_ADMIN`, and the new body must be valid.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
    - A post with `title={postTitle}` exists under category `{categoryId}` and is owned by `{postOwner}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwner}` with JSON body fields `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
    - The `{postId}` value used by the core endpoint must identify the post described above. If the API is used to create the post, the implementation response does not include the post id, so `{postId}` must be obtained by calling `GET /api/posts` with valid pagination and selecting the created post, for example by matching unique `title={postTitle}`. Direct database setup can satisfy this by using the inserted post id.
    - A comment identified by `{id}` exists on post `{postId}` and is owned by `{commentOwner}` with body `body={commentBody}`. This can be satisfied by directly inserting a `Comment` row linked to that post and user, or by calling `POST /api/posts/{postId}/comments` as `{commentOwner}` with path `postId={postId}` and JSON body field `body={commentBody}`. The `{id}` value used by the core endpoint must be the generated comment id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who is not the comment owner and does not have `ROLE_ADMIN`.
  - Failure endpoint:
    `PUT /api/posts/{postId}/comments/{id}`
  - Why this fails:
    The service verifies post/comment scope, then checks comment owner or admin authority before saving.
  - Intentionally violated constraints:
    The comment ownership/admin requirement was violated.

Endpoint coverage:
- Covers:
  `PUT /api/posts/{postId}/comments/{id}`
- Distinct meaning:
  Edits one comment.

### Function 28: delete comment

Function name:
delete comment

Core endpoint(s):
- `DELETE /api/posts/{postId}/comments/{id}`

Preconditions:
- A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
- A post with `title={postTitle}` exists under category `{categoryId}` and is owned by `{postOwner}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwner}` with JSON body fields `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
- The `{postId}` value used by the core endpoint must identify the post described above. If the API is used to create the post, the implementation response does not include the post id, so `{postId}` must be obtained by calling `GET /api/posts` with valid pagination and selecting the created post, for example by matching unique `title={postTitle}`. Direct database setup can satisfy this by using the inserted post id.
- A comment identified by `{id}` exists on post `{postId}` and is owned by `{commentOwner}` with body `body={commentBody}`. This can be satisfied by directly inserting a `Comment` row linked to that post and user, or by calling `POST /api/posts/{postId}/comments` as `{commentOwner}` with path `postId={postId}` and JSON body field `body={commentBody}`. The `{id}` value used by the core endpoint must be the generated comment id from the inserted row or creation response.

Successful execution:
- Result:
  This function deletes a comment that belongs to the specified post.
- Invocation:
  Step 1: `DELETE /api/posts/{postId}/comments/{id}` with path values `postId={postId}` and `id={id}`
- Constraints:
  The post and comment must exist, the comment must belong to the path post, and caller must be the comment owner or have `ROLE_ADMIN`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A category identified by `{categoryId}` exists with `name={categoryName}` and creator `{categoryOwner}`. This can be satisfied by directly inserting a `Category` row with that creator or by calling `POST /api/categories` as `{categoryOwner}` with JSON body field `name={categoryName}`. The `{categoryId}` value used by later endpoints must be the generated category id from the inserted row or creation response.
    - A post with `title={postTitleA}` exists under category `{categoryId}` and is owned by `{postOwnerA}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwnerA}` with JSON body fields `title={postTitleA}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
    - A post with `title={postTitleB}` exists under category `{categoryId}` and is owned by `{postOwnerB}`. This can be satisfied by directly inserting a `Post` row linked to that category and user, or by calling `POST /api/posts` as `{postOwnerB}` with JSON body fields `title={postTitleB}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}`.
    - The values `{postIdA}` and `{postIdB}` identify two different posts. If the API is used, they must be discovered by calling `GET /api/posts` after both post creations and matching their unique titles; direct database setup can use inserted ids.
    - A comment identified by `{id}` exists on post `{postIdA}` and is owned by `{commentOwner}` with body `body={commentBody}`. This can be satisfied by directly inserting a `Comment` row linked to that post and user, or by calling `POST /api/posts/{postId}/comments` as `{commentOwner}` with path `postId={postIdA}` and JSON body field `body={commentBody}`. The `{id}` value used by the core endpoint must be the generated comment id from the inserted row or creation response.
  - Failure endpoint:
    `DELETE /api/posts/{postId}/comments/{id}`
  - Why this fails:
    Delete returns an unsuccessful API response and HTTP 400 when the path post id does not match the comment stored post id.
  - Intentionally violated constraints:
    The path post/comment ownership relationship was mismatched by using `postId={postIdB}` with a comment created under `postId={postIdA}`.

Endpoint coverage:
- Covers:
  `DELETE /api/posts/{postId}/comments/{id}`
- Distinct meaning:
  Removes one comment from a post.

### Function 29: list tags

Function name:
list tags

Core endpoint(s):
- `GET /api/tags`

Preconditions:
- No persisted resource state is required before invoking the core endpoint.

Successful execution:
- Result:
  This function returns a paged list of tags.
- Invocation:
  Step 1: `GET /api/tags` with optional query values `page={page}` and `size={size}`
- Constraints:
  Optional query values must satisfy `page >= 0`, `size >= 0`, and `size <= 30`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request uses invalid pagination, such as `page < 0`, `size < 0`, or `size > 30`.
  - Failure endpoint:
    `GET /api/tags`
  - Why this fails:
    The tag service validates page and size and rejects invalid values.
  - Intentionally violated constraints:
    Pagination constraints were violated.

Endpoint coverage:
- Covers:
  `GET /api/tags`
- Distinct meaning:
  Reads the tag collection.

### Function 30: create tag

Function name:
create tag

Core endpoint(s):
- `POST /api/tags`

Preconditions:
- No persisted resource state is required before invoking the core endpoint.

Successful execution:
- Result:
  This function creates a tag.
- Invocation:
  Step 1: `POST /api/tags` with JSON body field `name={tagName}`
- Constraints:
  Caller must have `ROLE_USER`. The response provides the generated tag id.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No authenticated principal with `ROLE_USER` is supplied for the request. This can be produced by omitting authentication or using a principal whose authorities do not include `ROLE_USER`.
  - Failure endpoint:
    `POST /api/tags`
  - Why this fails:
    Method security blocks the call before the service persists the tag.
  - Intentionally violated constraints:
    The required user role was omitted.

Endpoint coverage:
- Covers:
  `POST /api/tags`
- Distinct meaning:
  Creates a tag resource.

### Function 31: get tag

Function name:
get tag

Core endpoint(s):
- `GET /api/tags/{id}`

Preconditions:
- A tag identified by `{id}` exists with `name={tagName}` and creator `{tagOwner}`. This can be satisfied by directly inserting a `Tag` row with that creator or by calling `POST /api/tags` as `{tagOwner}` with JSON body field `name={tagName}`. The `{id}` value used by later endpoints must be the generated tag id from the inserted row or creation response.

Successful execution:
- Result:
  This function retrieves the tag identified by `{id}`.
- Invocation:
  Step 1: `GET /api/tags/{id}` with path value `id={id}`
- Constraints:
  The path id must identify an existing tag.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No tag with id `{id}` exists in the database. This can be produced by starting from an empty database, deleting the tag beforehand, or intentionally not inserting it directly and not calling `POST /api/tags` for that id.
  - Failure endpoint:
    `GET /api/tags/{id}`
  - Why this fails:
    The service looks up the tag by id and throws `ResourceNotFoundException` when it is absent.
  - Intentionally violated constraints:
    The required tag state was omitted or the wrong generated id was used.

Endpoint coverage:
- Covers:
  `GET /api/tags/{id}`
- Distinct meaning:
  Reads one tag by id.

### Function 32: update tag

Function name:
update tag

Core endpoint(s):
- `PUT /api/tags/{id}`

Preconditions:
- A tag identified by `{id}` exists with `name={oldTagName}` and creator `{tagOwner}`. This can be satisfied by directly inserting a `Tag` row with that creator or by calling `POST /api/tags` as `{tagOwner}` with JSON body field `name={oldTagName}`. The `{id}` value used by later endpoints must be the generated tag id from the inserted row or creation response.

Successful execution:
- Result:
  This function updates a tag name.
- Invocation:
  Step 1: `PUT /api/tags/{id}` with path value `id={id}` and JSON body field `name={newTagName}`
- Constraints:
  The path id must identify an existing tag. Caller must be the tag creator or have `ROLE_ADMIN`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A tag identified by `{id}` exists with `name={oldTagName}` and creator `{tagOwner}`. This can be satisfied by directly inserting a `Tag` row with that creator or by calling `POST /api/tags` as `{tagOwner}` with JSON body field `name={oldTagName}`. The `{id}` value used by later endpoints must be the generated tag id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who is not the tag creator and does not have `ROLE_ADMIN`.
  - Failure endpoint:
    `PUT /api/tags/{id}`
  - Why this fails:
    The service checks `createdBy` or admin authority before saving.
  - Intentionally violated constraints:
    The creator/admin requirement was violated.

Endpoint coverage:
- Covers:
  `PUT /api/tags/{id}`
- Distinct meaning:
  Changes a tag name.

### Function 33: delete tag

Function name:
delete tag

Core endpoint(s):
- `DELETE /api/tags/{id}`

Preconditions:
- A tag identified by `{id}` exists with `name={tagName}` and creator `{tagOwner}`. This can be satisfied by directly inserting a `Tag` row with that creator or by calling `POST /api/tags` as `{tagOwner}` with JSON body field `name={tagName}`. The `{id}` value used by later endpoints must be the generated tag id from the inserted row or creation response.

Successful execution:
- Result:
  This function deletes the tag identified by `{id}`.
- Invocation:
  Step 1: `DELETE /api/tags/{id}` with path value `id={id}`
- Constraints:
  The path id must identify an existing tag. Caller must be the tag creator or have `ROLE_ADMIN`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A tag identified by `{id}` exists with `name={tagName}` and creator `{tagOwner}`. This can be satisfied by directly inserting a `Tag` row with that creator or by calling `POST /api/tags` as `{tagOwner}` with JSON body field `name={tagName}`. The `{id}` value used by later endpoints must be the generated tag id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who is not the tag creator and does not have `ROLE_ADMIN`.
  - Failure endpoint:
    `DELETE /api/tags/{id}`
  - Why this fails:
    The service checks creator ownership or admin authority before deleting.
  - Intentionally violated constraints:
    The creator/admin requirement was violated.

Endpoint coverage:
- Covers:
  `DELETE /api/tags/{id}`
- Distinct meaning:
  Removes a tag.

### Function 34: list own todos

Function name:
list own todos

Core endpoint(s):
- `GET /api/todos`

Preconditions:
- No persisted resource state is required before invoking the core endpoint.

Successful execution:
- Result:
  This function returns a paged list of todos created by the authenticated user.
- Invocation:
  Step 1: `GET /api/todos` with optional query values `page={page}` and `size={size}`
- Constraints:
  Caller must have `ROLE_USER`. Pagination must be valid. The implementation filters by the current user id, not by request parameters; OpenAPI exposes unrelated current-user query parameters that the controller ignores.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No authenticated principal with `ROLE_USER` is supplied for the request. This can be produced by omitting authentication or using a principal whose authorities do not include `ROLE_USER`.
  - Failure endpoint:
    `GET /api/todos`
  - Why this fails:
    `@PreAuthorize("hasRole('USER')")` blocks the call.
  - Intentionally violated constraints:
    The required user role was omitted.

Endpoint coverage:
- Covers:
  `GET /api/todos`
- Distinct meaning:
  Reads the authenticated user todos.

### Function 35: create todo

Function name:
create todo

Core endpoint(s):
- `POST /api/todos`

Preconditions:
- No persisted resource state is required before invoking the core endpoint.

Successful execution:
- Result:
  This function creates a todo owned by the authenticated user.
- Invocation:
  Step 1: `POST /api/todos` with JSON body fields `title={todoTitle}` and optional `completed={completed}`
- Constraints:
  Caller must have `ROLE_USER`. Body must include a nonblank title. The response provides the generated todo id.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request body omits `title` or sends it blank. No setup endpoint is needed to produce this invalid body state.
  - Failure endpoint:
    `POST /api/todos`
  - Why this fails:
    Bean validation requires a nonblank title before the service can persist the todo.
  - Intentionally violated constraints:
    The todo title validation requirement was violated.

Endpoint coverage:
- Covers:
  `POST /api/todos`
- Distinct meaning:
  Creates an owned todo.

### Function 36: get own todo

Function name:
get own todo

Core endpoint(s):
- `GET /api/todos/{id}`

Preconditions:
- A todo identified by `{id}` exists with `title={todoTitle}` and is owned by `{todoOwner}`. This can be satisfied by directly inserting a `Todo` row linked to that user or by calling `POST /api/todos` as `{todoOwner}` with JSON body fields `title={todoTitle}` and optional `completed={completed}`. The `{id}` value used by the core endpoint must be the generated todo id from the inserted row or creation response.

Successful execution:
- Result:
  This function retrieves one todo owned by the authenticated user.
- Invocation:
  Step 1: `GET /api/todos/{id}` with path value `id={id}`
- Constraints:
  The path id must identify an existing todo owned by the current user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A todo identified by `{id}` exists with `title={todoTitle}` and is owned by `{todoOwner}`. This can be satisfied by directly inserting a `Todo` row linked to that user or by calling `POST /api/todos` as `{todoOwner}` with JSON body fields `title={todoTitle}` and optional `completed={completed}`. The `{id}` value used by the core endpoint must be the generated todo id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who is not the todo owner.
  - Failure endpoint:
    `GET /api/todos/{id}`
  - Why this fails:
    The service compares the todo owner id with the current user id and rejects non-owners.
  - Intentionally violated constraints:
    The todo ownership requirement was violated.

Endpoint coverage:
- Covers:
  `GET /api/todos/{id}`
- Distinct meaning:
  Reads one owned todo.

### Function 37: update own todo

Function name:
update own todo

Core endpoint(s):
- `PUT /api/todos/{id}`

Preconditions:
- A todo identified by `{id}` exists with `title={todoTitle}` and is owned by `{todoOwner}`. This can be satisfied by directly inserting a `Todo` row linked to that user or by calling `POST /api/todos` as `{todoOwner}` with JSON body fields `title={todoTitle}` and optional `completed={completed}`. The `{id}` value used by the core endpoint must be the generated todo id from the inserted row or creation response.

Successful execution:
- Result:
  This function updates the title and completed flag of an owned todo.
- Invocation:
  Step 1: `PUT /api/todos/{id}` with path value `id={id}` and JSON body fields `title={newTodoTitle}` and `completed={completed}`
- Constraints:
  The path id must identify an existing todo owned by the current user. The new title must be nonblank.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A todo identified by `{id}` exists with `title={todoTitle}` and is owned by `{todoOwner}`. This can be satisfied by directly inserting a `Todo` row linked to that user or by calling `POST /api/todos` as `{todoOwner}` with JSON body fields `title={todoTitle}` and optional `completed={completed}`. The `{id}` value used by the core endpoint must be the generated todo id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who is not the todo owner.
  - Failure endpoint:
    `PUT /api/todos/{id}`
  - Why this fails:
    The service rejects updates from non-owners after loading the todo.
  - Intentionally violated constraints:
    The todo ownership requirement was violated.

Endpoint coverage:
- Covers:
  `PUT /api/todos/{id}`
- Distinct meaning:
  Edits an owned todo.

### Function 38: delete own todo

Function name:
delete own todo

Core endpoint(s):
- `DELETE /api/todos/{id}`

Preconditions:
- A todo identified by `{id}` exists with `title={todoTitle}` and is owned by `{todoOwner}`. This can be satisfied by directly inserting a `Todo` row linked to that user or by calling `POST /api/todos` as `{todoOwner}` with JSON body fields `title={todoTitle}` and optional `completed={completed}`. The `{id}` value used by the core endpoint must be the generated todo id from the inserted row or creation response.

Successful execution:
- Result:
  This function deletes an owned todo.
- Invocation:
  Step 1: `DELETE /api/todos/{id}` with path value `id={id}`
- Constraints:
  The path id must identify an existing todo owned by the current user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A todo identified by `{id}` exists with `title={todoTitle}` and is owned by `{todoOwner}`. This can be satisfied by directly inserting a `Todo` row linked to that user or by calling `POST /api/todos` as `{todoOwner}` with JSON body fields `title={todoTitle}` and optional `completed={completed}`. The `{id}` value used by the core endpoint must be the generated todo id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who is not the todo owner.
  - Failure endpoint:
    `DELETE /api/todos/{id}`
  - Why this fails:
    The service rejects deletion from non-owners after loading the todo.
  - Intentionally violated constraints:
    The todo ownership requirement was violated.

Endpoint coverage:
- Covers:
  `DELETE /api/todos/{id}`
- Distinct meaning:
  Removes an owned todo.

### Function 39: complete todo

Function name:
complete todo

Core endpoint(s):
- `PUT /api/todos/{id}/complete`

Preconditions:
- A todo identified by `{id}` exists with `title={todoTitle}` and is owned by `{todoOwner}`. This can be satisfied by directly inserting a `Todo` row linked to that user or by calling `POST /api/todos` as `{todoOwner}` with JSON body fields `title={todoTitle}` and optional `completed={completed}`. The `{id}` value used by the core endpoint must be the generated todo id from the inserted row or creation response.

Successful execution:
- Result:
  This function marks an owned todo as completed.
- Invocation:
  Step 1: `PUT /api/todos/{id}/complete` with path value `id={id}`
- Constraints:
  The path id must identify an existing todo owned by the current user. OpenAPI shows a request body for this endpoint, but the implementation ignores the body and sets `completed` to `true`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A todo identified by `{id}` exists with `title={todoTitle}` and is owned by `{todoOwner}`. This can be satisfied by directly inserting a `Todo` row linked to that user or by calling `POST /api/todos` as `{todoOwner}` with JSON body fields `title={todoTitle}` and optional `completed={completed}`. The `{id}` value used by the core endpoint must be the generated todo id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who is not the todo owner.
  - Failure endpoint:
    `PUT /api/todos/{id}/complete`
  - Why this fails:
    The service checks todo ownership before setting completed to true.
  - Intentionally violated constraints:
    The todo ownership requirement was violated.

Endpoint coverage:
- Covers:
  `PUT /api/todos/{id}/complete`
- Distinct meaning:
  Sets an owned todo to completed.

### Function 40: uncomplete todo

Function name:
uncomplete todo

Core endpoint(s):
- `PUT /api/todos/{id}/unComplete`

Preconditions:
- A todo identified by `{id}` exists with `title={todoTitle}` and is owned by `{todoOwner}`. This can be satisfied by directly inserting a `Todo` row linked to that user or by calling `POST /api/todos` as `{todoOwner}` with JSON body fields `title={todoTitle}` and optional `completed={completed}`. The `{id}` value used by the core endpoint must be the generated todo id from the inserted row or creation response.
- Todo `{id}` is currently completed. This can be satisfied by directly setting the stored `completed` flag to `true` or by calling `PUT /api/todos/{id}/complete` as the todo owner after creating the todo. The same generated `{id}` must be reused.

Successful execution:
- Result:
  This function marks an owned completed todo as incomplete.
- Invocation:
  Step 1: `PUT /api/todos/{id}/unComplete` with path value `id={id}`
- Constraints:
  The path id must identify an existing todo owned by the current user. OpenAPI shows a request body for this endpoint, but the implementation ignores the body and sets `completed` to `false`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A todo identified by `{id}` exists with `title={todoTitle}` and is owned by `{todoOwner}`. This can be satisfied by directly inserting a `Todo` row linked to that user or by calling `POST /api/todos` as `{todoOwner}` with JSON body fields `title={todoTitle}` and optional `completed={completed}`. The `{id}` value used by the core endpoint must be the generated todo id from the inserted row or creation response.
    - The failing request is authenticated as `{otherUser}`, who is not the todo owner.
  - Failure endpoint:
    `PUT /api/todos/{id}/unComplete`
  - Why this fails:
    The service checks todo ownership before setting completed to false.
  - Intentionally violated constraints:
    The todo ownership requirement was violated.

Endpoint coverage:
- Covers:
  `PUT /api/todos/{id}/unComplete`
- Distinct meaning:
  Sets an owned todo to incomplete.

### Function 41: get current user summary

Function name:
get current user summary

Core endpoint(s):
- `GET /api/users/me`

Preconditions:
- No persisted resource state is required before invoking the core endpoint.

Successful execution:
- Result:
  This function returns id, username, first name, and last name for the authenticated user.
- Invocation:
  Step 1: `GET /api/users/me`
- Constraints:
  Caller must have `ROLE_USER`. OpenAPI lists current-user-shaped query parameters for this endpoint, but the implementation ignores them and reads the authenticated principal.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No authenticated principal with `ROLE_USER` is supplied for the request. This can be produced by omitting authentication or using a principal whose authorities do not include `ROLE_USER`.
  - Failure endpoint:
    `GET /api/users/me`
  - Why this fails:
    Method security blocks the call before the controller reads the current principal.
  - Intentionally violated constraints:
    The required user role was omitted.

Endpoint coverage:
- Covers:
  `GET /api/users/me`
- Distinct meaning:
  Reads the authenticated user summary.

### Function 42: check username is available

Function name:
check username is available

Core endpoint(s):
- `GET /api/users/checkUsernameAvailability`

Preconditions:
- No user with `username={username}` exists in the database. This can be satisfied by starting from an empty database, deleting such a user directly, or intentionally not inserting one directly and not calling `POST /api/users` with that username.

Successful execution:
- Result:
  This function returns availability `true` for a username that is not in use.
- Invocation:
  Step 1: `GET /api/users/checkUsernameAvailability` with query value `username={username}`
- Constraints:
  The query parameter `username` is required.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request omits the required query parameter `username`.
  - Failure endpoint:
    `GET /api/users/checkUsernameAvailability`
  - Why this fails:
    Spring request binding requires the query parameter before the service can check availability.
  - Intentionally violated constraints:
    The required query parameter was omitted.

Endpoint coverage:
- Covers:
  `GET /api/users/checkUsernameAvailability`
- Distinct meaning:
  Confirms a username is free.

### Function 43: check username is taken

Function name:
check username is taken

Core endpoint(s):
- `GET /api/users/checkUsernameAvailability`

Preconditions:
- A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.

Successful execution:
- Result:
  This function returns availability `false` for a username already assigned to a user.
- Invocation:
  Step 1: `GET /api/users/checkUsernameAvailability` with query value `username={username}`
- Constraints:
  The query username must equal an existing stored username.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.
    - A second user-creation request reuses `username={username}`. This failing duplicate state can be produced by calling `POST /api/users` again as an admin with the same username, or by attempting to insert a second user row with the same username directly.
  - Failure endpoint:
    `POST /api/users`
  - Why this fails:
    The user service explicitly checks `existsByUsername` and throws `BadRequestException` for a duplicate username.
  - Intentionally violated constraints:
    The username uniqueness requirement was violated while establishing the taken-username state.

Endpoint coverage:
- Covers:
  `GET /api/users/checkUsernameAvailability`
- Distinct meaning:
  Confirms a username is already taken.

### Function 44: check email is available

Function name:
check email is available

Core endpoint(s):
- `GET /api/users/checkEmailAvailability`

Preconditions:
- No user with `email={email}` exists in the database. This can be satisfied by starting from an empty database, deleting such a user directly, or intentionally not inserting one directly and not calling `POST /api/users` with that email.

Successful execution:
- Result:
  This function returns availability `true` for an email address that is not in use.
- Invocation:
  Step 1: `GET /api/users/checkEmailAvailability` with query value `email={email}`
- Constraints:
  The query parameter `email` is required.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request omits the required query parameter `email`.
  - Failure endpoint:
    `GET /api/users/checkEmailAvailability`
  - Why this fails:
    Spring request binding requires the query parameter before the service can check availability.
  - Intentionally violated constraints:
    The required query parameter was omitted.

Endpoint coverage:
- Covers:
  `GET /api/users/checkEmailAvailability`
- Distinct meaning:
  Confirms an email is free.

### Function 45: check email is taken

Function name:
check email is taken

Core endpoint(s):
- `GET /api/users/checkEmailAvailability`

Preconditions:
- A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.

Successful execution:
- Result:
  This function returns availability `false` for an email already assigned to a user.
- Invocation:
  Step 1: `GET /api/users/checkEmailAvailability` with query value `email={email}`
- Constraints:
  The query email must equal an existing stored email.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.
    - A second user-creation request reuses `email={email}`. This failing duplicate state can be produced by calling `POST /api/users` again as an admin with the same email, or by attempting to insert a second user row with the same email directly.
  - Failure endpoint:
    `POST /api/users`
  - Why this fails:
    The user service explicitly checks `existsByEmail` and throws `BadRequestException` for a duplicate email.
  - Intentionally violated constraints:
    The email uniqueness requirement was violated while establishing the taken-email state.

Endpoint coverage:
- Covers:
  `GET /api/users/checkEmailAvailability`
- Distinct meaning:
  Confirms an email is already taken.

### Function 46: get user profile

Function name:
get user profile

Core endpoint(s):
- `GET /api/users/{username}/profile`

Preconditions:
- A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.

Successful execution:
- Result:
  This function retrieves public profile data and post count for `{username}`.
- Invocation:
  Step 1: `GET /api/users/{username}/profile` with path value `username={username}`
- Constraints:
  The path username must identify an existing user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user with `username={username}` exists in the database. This can be produced by starting from an empty database, deleting the user beforehand, or intentionally not inserting it directly and not calling `POST /api/users` for that username.
  - Failure endpoint:
    `GET /api/users/{username}/profile`
  - Why this fails:
    The repository lookup by username throws `ResourceNotFoundException` when no such user exists.
  - Intentionally violated constraints:
    The required user state was omitted.

Endpoint coverage:
- Covers:
  `GET /api/users/{username}/profile`
- Distinct meaning:
  Reads one user profile.

### Function 47: list user posts

Function name:
list user posts

Core endpoint(s):
- `GET /api/users/{username}/posts`

Preconditions:
- A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.

Successful execution:
- Result:
  This function returns a paged list of posts created by `{username}`.
- Invocation:
  Step 1: `GET /api/users/{username}/posts` with path value `username={username}` and optional query values `page={page}` and `size={size}`
- Constraints:
  The path username must identify an existing user, and pagination must be valid.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user with `username={username}` exists in the database. This can be produced by starting from an empty database, deleting the user beforehand, or intentionally not inserting it directly and not calling `POST /api/users` for that username.
  - Failure endpoint:
    `GET /api/users/{username}/posts`
  - Why this fails:
    The service resolves the username before querying posts and throws when the user is absent.
  - Intentionally violated constraints:
    The required user state was omitted.

Endpoint coverage:
- Covers:
  `GET /api/users/{username}/posts`
- Distinct meaning:
  Reads posts by user.

### Function 48: list user albums

Function name:
list user albums

Core endpoint(s):
- `GET /api/users/{username}/albums`

Preconditions:
- A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.

Successful execution:
- Result:
  This function returns a paged list of albums created by `{username}`.
- Invocation:
  Step 1: `GET /api/users/{username}/albums` with path value `username={username}` and optional query values `page={page}` and `size={size}`
- Constraints:
  The path username must identify an existing user. The service resolves the username before querying albums.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user with `username={username}` exists in the database. This can be produced by starting from an empty database, deleting the user beforehand, or intentionally not inserting it directly and not calling `POST /api/users` for that username.
  - Failure endpoint:
    `GET /api/users/{username}/albums`
  - Why this fails:
    The service resolves the username before querying albums and throws when the user is absent.
  - Intentionally violated constraints:
    The required user state was omitted.

Endpoint coverage:
- Covers:
  `GET /api/users/{username}/albums`
- Distinct meaning:
  Reads albums by user.

### Function 49: create user

Function name:
create user

Core endpoint(s):
- `POST /api/users`

Preconditions:
- No existing user has `username={username}` or `email={email}`. This can be satisfied by starting from a database without those values or by deleting conflicting rows directly; it is also the state produced by not calling `POST /api/users` previously with the same username or email.

Successful execution:
- Result:
  This function creates a user with the default `ROLE_USER` role.
- Invocation:
  Step 1: `POST /api/users` with JSON body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`
- Constraints:
  Caller must have `ROLE_ADMIN`. Username and email must be unique. The service encodes the password and assigns `ROLE_USER`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.
    - The failing creation request reuses `username={username}` or `email={email}`. This can be produced by calling `POST /api/users` once before the failing request or by inserting the conflicting user directly.
  - Failure endpoint:
    `POST /api/users`
  - Why this fails:
    The user service checks duplicate username and email and throws `BadRequestException` when either already exists.
  - Intentionally violated constraints:
    The username or email uniqueness requirement was violated.

Endpoint coverage:
- Covers:
  `POST /api/users`
- Distinct meaning:
  Admin creates a user account.

### Function 50: update user account

Function name:
update user account

Core endpoint(s):
- `PUT /api/users/{username}`

Preconditions:
- A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.

Successful execution:
- Result:
  This function updates first name, last name, password, address, phone, website, and company for the user named `{username}`.
- Invocation:
  Step 1: `PUT /api/users/{username}` with path value `username={username}` and JSON body fields for the updated account profile
- Constraints:
  Caller must be the same user or have `ROLE_ADMIN`. The implementation does not update username, email, or roles through this endpoint.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.
    - The failing request is authenticated as `{otherUser}`, who is neither the target user nor an admin.
  - Failure endpoint:
    `PUT /api/users/{username}`
  - Why this fails:
    The service checks target user id against current user id or admin authority before saving.
  - Intentionally violated constraints:
    The self/admin requirement was violated.

Endpoint coverage:
- Covers:
  `PUT /api/users/{username}`
- Distinct meaning:
  Updates account/profile fields for an existing user.

### Function 51: delete user account

Function name:
delete user account

Core endpoint(s):
- `DELETE /api/users/{username}`

Preconditions:
- A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.
- The target user `{username}` has `ROLE_ADMIN` in addition to `ROLE_USER`. This can be satisfied by directly inserting the role link or by calling `PUT /api/users/{username}/giveAdmin` as an admin after the user exists. The same username from user creation must be reused.

Successful execution:
- Result:
  This function deletes the user account named `{username}`.
- Invocation:
  Step 1: `DELETE /api/users/{username}` with path value `username={username}`
- Constraints:
  Due to implementation logic, the request succeeds only when the authenticated caller is both the same user and has admin authority. This is stricter than the OpenAPI/controller annotation `@PreAuthorize("hasRole('USER') or hasRole('ADMIN')")` suggests.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.
    - The failing request is authenticated as admin principal `{adminUser}` whose username differs from `{username}`.
  - Failure endpoint:
    `DELETE /api/users/{username}`
  - Why this fails:
    The service uses `if (!sameUser || !isAdmin)`, so an admin who is not the target user is denied.
  - Intentionally violated constraints:
    The implementation-specific same-user-and-admin requirement was violated.

Endpoint coverage:
- Covers:
  `DELETE /api/users/{username}`
- Distinct meaning:
  Deletes a user account, but implementation only permits self-delete by an admin user.

### Function 52: grant admin role

Function name:
grant admin role

Core endpoint(s):
- `PUT /api/users/{username}/giveAdmin`

Preconditions:
- A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.

Successful execution:
- Result:
  This function gives `{username}` both `ROLE_ADMIN` and `ROLE_USER`.
- Invocation:
  Step 1: `PUT /api/users/{username}/giveAdmin` with path value `username={username}`
- Constraints:
  Caller must have `ROLE_ADMIN`, and the path username must identify an existing user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user with `username={username}` exists in the database. This can be produced by starting from an empty database, deleting the user beforehand, or intentionally not inserting it directly and not calling `POST /api/users` for that username.
  - Failure endpoint:
    `PUT /api/users/{username}/giveAdmin`
  - Why this fails:
    The service resolves the target user by username before replacing roles.
  - Intentionally violated constraints:
    The required target user state was omitted.

Endpoint coverage:
- Covers:
  `PUT /api/users/{username}/giveAdmin`
- Distinct meaning:
  Adds admin authority to a user.

### Function 53: revoke admin role

Function name:
revoke admin role

Core endpoint(s):
- `PUT /api/users/{username}/takeAdmin`

Preconditions:
- A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.
- The target user `{username}` currently has `ROLE_ADMIN`. This can be satisfied by directly inserting the role link or by calling `PUT /api/users/{username}/giveAdmin` as an admin after the user exists.

Successful execution:
- Result:
  This function removes admin authority from `{username}` and leaves only `ROLE_USER`.
- Invocation:
  Step 1: `PUT /api/users/{username}/takeAdmin` with path value `username={username}`
- Constraints:
  Caller must have `ROLE_ADMIN`, and the path username must identify an existing user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user with `username={username}` exists in the database. This can be produced by starting from an empty database, deleting the user beforehand, or intentionally not inserting it directly and not calling `POST /api/users` for that username.
  - Failure endpoint:
    `PUT /api/users/{username}/takeAdmin`
  - Why this fails:
    The service resolves the target user by username before replacing roles.
  - Intentionally violated constraints:
    The required target user state was omitted.

Endpoint coverage:
- Covers:
  `PUT /api/users/{username}/takeAdmin`
- Distinct meaning:
  Removes admin authority from a user.

### Function 54: set or update current user info

Function name:
set or update current user info

Core endpoint(s):
- `PUT /api/users/setOrUpdateInfo`

Preconditions:
- A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.

Successful execution:
- Result:
  This function sets or replaces address, company, website, and phone profile fields for the authenticated user.
- Invocation:
  Step 1: `PUT /api/users/setOrUpdateInfo` with JSON body fields `street={street}`, `suite={suite}`, `city={city}`, `zipcode={zipcode}`, and optional company, website, phone, lat, and lng fields
- Constraints:
  Caller must have `ROLE_USER` or `ROLE_ADMIN`. The authenticated username must resolve to an existing user. Bean validation requires nonblank street, suite, city, and zipcode.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A user with `username={username}` and `email={email}` exists and has at least `ROLE_USER`. This can be satisfied by directly inserting a `User` row plus role link or by calling `POST /api/users` as an admin with body fields `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, and `password={password}`. The username path or query value must reuse the stored username.
    - The request body omits or blanks one of `street`, `suite`, `city`, or `zipcode`.
  - Failure endpoint:
    `PUT /api/users/setOrUpdateInfo`
  - Why this fails:
    Bean validation requires those address fields before the service can update the profile.
  - Intentionally violated constraints:
    The required info field validation was violated.

Endpoint coverage:
- Covers:
  `PUT /api/users/setOrUpdateInfo`
- Distinct meaning:
  Updates extended profile info for the authenticated user.
