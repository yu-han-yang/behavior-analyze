# Domain-Level Behavior Analysis

## Domain Summary
The service is a blog/content API with user accounts, roles, posts, categories, tags, comments, albums, photos, and personal todos. Core business concepts are authenticated ownership, admin override, categorized/tagged publishing, post discussions, album/photo organization, public profile/content browsing, and private todo tracking.

Implementation behavior is Spring/JPA-backed. Source code is more authoritative than the OpenAPI file where they disagree. Notable source-only endpoints exist under `/api/auth`, but they are not present in `full-behavior.md` or `blogapi.json`, so they are treated as implementation notes rather than available functions.

## Available Function Inventory

### Albums and Photos
- `list albums` - `GET /api/albums` - list all albums.
- `create album` - `POST /api/albums` - create an album owned by the authenticated user.
- `get album` - `GET /api/albums/{id}` - retrieve one album.
- `update album` - `PUT /api/albums/{id}` - change an owned/admin-accessible album title.
- `delete album` - `DELETE /api/albums/{id}` - delete an owned/admin-accessible album.
- `list album photos` - `GET /api/albums/{id}/photos` - list photos by album id.
- `list photos` - `GET /api/photos` - list all photos.
- `create photo` - `POST /api/photos` - create a photo in an owned album.
- `get photo` - `GET /api/photos/{id}` - retrieve one photo.
- `update photo` - `PUT /api/photos/{id}` - update photo title, thumbnail, and album reference.
- `delete photo` - `DELETE /api/photos/{id}` - delete an owned/admin-accessible photo.

### Categories, Tags, Posts, and Comments
- `list categories` - `GET /api/categories` - list categories.
- `create category` - `POST /api/categories` - create a category.
- `get category` - `GET /api/categories/{id}` - retrieve one category.
- `update category` - `PUT /api/categories/{id}` - rename a category.
- `delete category` - `DELETE /api/categories/{id}` - delete a category.
- `list tags` - `GET /api/tags` - list tags.
- `create tag` - `POST /api/tags` - create a tag.
- `get tag` - `GET /api/tags/{id}` - retrieve one tag.
- `update tag` - `PUT /api/tags/{id}` - rename a tag.
- `delete tag` - `DELETE /api/tags/{id}` - delete a tag.
- `list posts` - `GET /api/posts` - list all posts.
- `create post` - `POST /api/posts` - create a category-scoped post and link/create tags.
- `list posts by category` - `GET /api/posts/category/{id}` - list posts in a category.
- `list posts by tag` - `GET /api/posts/tag/{id}` - list posts by tag.
- `get post` - `GET /api/posts/{id}` - retrieve one post.
- `update post` - `PUT /api/posts/{id}` - update post title, body, and category.
- `delete post` - `DELETE /api/posts/{id}` - delete a post.
- `list post comments` - `GET /api/posts/{postId}/comments` - list comments for a post id.
- `add comment` - `POST /api/posts/{postId}/comments` - add authenticated-user comment.
- `get comment` - `GET /api/posts/{postId}/comments/{id}` - retrieve one comment within a post.
- `update comment` - `PUT /api/posts/{postId}/comments/{id}` - edit an owned/admin-accessible comment.
- `delete comment` - `DELETE /api/posts/{postId}/comments/{id}` - delete an owned/admin-accessible comment.

### Todos
- `list own todos` - `GET /api/todos` - list current user’s todos.
- `create todo` - `POST /api/todos` - create current user’s todo.
- `get own todo` - `GET /api/todos/{id}` - retrieve current user’s todo.
- `update own todo` - `PUT /api/todos/{id}` - update title/completed flag.
- `delete own todo` - `DELETE /api/todos/{id}` - delete current user’s todo.
- `complete todo` - `PUT /api/todos/{id}/complete` - mark owned todo completed.
- `uncomplete todo` - `PUT /api/todos/{id}/unComplete` - mark owned todo incomplete.

### Users and Identity
- `get current user summary` - `GET /api/users/me` - read authenticated user summary.
- `check username is available` - `GET /api/users/checkUsernameAvailability` - confirm free username.
- `check username is taken` - `GET /api/users/checkUsernameAvailability` - confirm used username.
- `check email is available` - `GET /api/users/checkEmailAvailability` - confirm free email.
- `check email is taken` - `GET /api/users/checkEmailAvailability` - confirm used email.
- `get user profile` - `GET /api/users/{username}/profile` - read public profile and post count.
- `list user posts` - `GET /api/users/{username}/posts` - list one user’s posts.
- `list user albums` - `GET /api/users/{username}/albums` - list one user’s albums.
- `create user` - `POST /api/users` - admin creates a user with `ROLE_USER`.
- `update user account` - `PUT /api/users/{username}` - self/admin updates account profile fields.
- `delete user account` - `DELETE /api/users/{username}` - implementation permits only admin self-delete.
- `grant admin role` - `PUT /api/users/{username}/giveAdmin` - assign admin + user roles.
- `revoke admin role` - `PUT /api/users/{username}/takeAdmin` - leave only user role.
- `set or update current user info` - `PUT /api/users/setOrUpdateInfo` - set current user address/company/contact info.

## Supported Business Behaviors

### Behavior 1: Browse Public Content Catalogs
Business goal:
Inspect public collections of albums, photos, categories, tags, posts, user profiles, user posts, and user albums.

Domain context:
This is the read side of the blog/gallery service. It lets clients discover content before acting on a specific resource.

Starting point:
No prior service state for global list calls. Existing user/category/tag state is needed for scoped reads.

Required execution workflow:
1. Use function `list albums` (`GET /api/albums`) with optional `page={page}`, `size={size}` to browse albums.
2. Use function `list photos` (`GET /api/photos`) with optional `page={page}`, `size={size}` to browse photos.
3. Use function `list categories` (`GET /api/categories`) with optional `page={page}`, `size={size}` to browse categories.
4. Use function `list tags` (`GET /api/tags`) with optional `page={page}`, `size={size}` to browse tags.
5. Use function `list posts` (`GET /api/posts`) with optional `page={page}`, `size={size}` to browse posts.
6. For user-scoped browsing, use function `create user` (`POST /api/users`) as admin with `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, `password={password}` if the user does not already exist.
7. Use function `get user profile` (`GET /api/users/{username}/profile`) with `username={username}` to inspect the user profile.
8. Use function `list user posts` (`GET /api/users/{username}/posts`) with `username={username}`, optional `page={page}`, `size={size}` to browse that user’s posts.
9. Use function `list user albums` (`GET /api/users/{username}/albums`) with `username={username}`, optional `page={page}`, `size={size}` to browse that user’s albums.

Optional verification workflow:
None.

Existing-state shortcuts:
- Skip `create user` if a user with `username={username}` already exists.
- Direct database setup can create the user and related posts/albums, but the path `username` must match the stored username.
- Empty lists are valid results when no matching resources exist.

Parameter and value bindings:
- `page` and `size` are reused only for pagination and must satisfy `page >= 0`, `size >= 0`, `size <= 30`.
- For user-scoped reads, the same `username` used to create or identify the user must be reused in profile, posts, and albums paths.

Business result:
The client receives paged public content or public user profile data. No domain state changes.

Constraints and invariants:
- Pagination validation is enforced for most list endpoints.
- `list user albums` resolves the user but does not validate page/size in `AlbumServiceImpl`, unlike most list functions.
- Public reads generally do not require authentication.

Failure and exceptional cases:
- Failing function: `list posts`
  - Failure condition: `page < 0`, `size < 0`, or `size > 30`.
  - Why it fails: service validation throws `BadRequestException`.
  - Violated prerequisite or constraint: pagination bounds.
- Failing function: `get user profile`
  - Failure condition: `username={username}` does not exist.
  - Why it fails: repository lookup by username throws `ResourceNotFoundException`.
  - Violated prerequisite or constraint: user identity must exist.
- Failing function: `list user posts`
  - Failure condition: nonexistent `username`.
  - Why it fails: user is resolved before querying posts.
  - Violated prerequisite or constraint: scoped user must exist.

Implementation notes:
`full-behavior.md` and source agree that most list functions are paged. The OpenAPI exposes some current-user-shaped query parameters on user/todo endpoints, but the implementation ignores them.

### Behavior 2: Admin-Provision User Accounts and Roles
Business goal:
Create users and manage admin authority.

Domain context:
The service distinguishes normal users from admins. Admins can create accounts and grant/revoke elevated authority.

Starting point:
An authenticated admin principal exists. A complete bootstrap path is source-only through `/api/auth/signup` for the first user and `/api/auth/signin` for JWT issuance, but those endpoints are not functions in `full-behavior.md`.

Required execution workflow:
1. Use function `create user` (`POST /api/users`) as admin with body `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, `password={password}` to create a `ROLE_USER` account.
2. Use function `grant admin role` (`PUT /api/users/{username}/giveAdmin`) as admin with path `username={username}` to give the same user `ROLE_ADMIN` and `ROLE_USER`.
3. Use function `revoke admin role` (`PUT /api/users/{username}/takeAdmin`) as admin with path `username={username}` to remove admin authority when needed.

Optional verification workflow:
1. Use function `get user profile` (`GET /api/users/{username}/profile`) with `username={username}` to verify the user exists.
2. Use function `get current user summary` (`GET /api/users/me`) as the affected authenticated user to verify current-principal summary; it does not expose roles.

Existing-state shortcuts:
- Skip `create user` if the target user already exists.
- Skip `grant admin role` if the target already has admin role.
- Direct database setup may create the user and `user_role` rows, but username/email uniqueness and role links must still hold.

Parameter and value bindings:
- The `username` returned/stored by `create user` is reused as the path value in role functions.
- `email` and `username` must remain unique across users.
- The admin caller’s authenticated authorities, not request body values, control authorization.

Business result:
A user account exists with either normal `ROLE_USER` or both `ROLE_USER` and `ROLE_ADMIN`.

Constraints and invariants:
- `create user` requires admin authority and assigns only `ROLE_USER`.
- `grant admin role` replaces roles with admin + user.
- `revoke admin role` replaces roles with user only.
- There is no role-inspection function in the inventory.

Failure and exceptional cases:
- Failing function: `create user`
  - Failure condition: duplicate `username` or `email`.
  - Why it fails: service checks `existsByUsername` and `existsByEmail`.
  - Violated prerequisite or constraint: account identity uniqueness.
- Failing function: `grant admin role`
  - Failure condition: `username={username}` does not resolve.
  - Why it fails: target user is loaded before role replacement.
  - Violated prerequisite or constraint: target account must exist.
- Failing function: `revoke admin role`
  - Failure condition: caller lacks `ROLE_ADMIN`.
  - Why it fails: controller method security blocks the call.
  - Violated prerequisite or constraint: admin-only role management.

Implementation notes:
Source has `/api/auth/signup`; first signup becomes admin if `userRepository.count() == 0`. That bootstrap capability is absent from `full-behavior.md` and `blogapi.json`.

### Behavior 3: Check Account Identity Availability
Business goal:
Determine whether a username or email can be used for an account.

Domain context:
This supports registration/admin provisioning workflows and prevents duplicate identity attempts.

Starting point:
No prior service state for checking availability; existing user state for checking taken values.

Required execution workflow:
1. Use function `check username is available` (`GET /api/users/checkUsernameAvailability`) with query `username={candidateUsername}` to confirm a free username.
2. Use function `check email is available` (`GET /api/users/checkEmailAvailability`) with query `email={candidateEmail}` to confirm a free email.
3. To check taken values from no prior state, use function `create user` (`POST /api/users`) as admin with body `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, `password={password}`.
4. Use function `check username is taken` (`GET /api/users/checkUsernameAvailability`) with query `username={username}`.
5. Use function `check email is taken` (`GET /api/users/checkEmailAvailability`) with query `email={email}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Skip `create user` if a user already exists with the username/email being checked.
- Direct database setup may insert a user row, but the checked query value must exactly match the stored username or email.

Parameter and value bindings:
- `username` created in `create user` must be reused as the query value for username availability.
- `email` created in `create user` must be reused as the query value for email availability.

Business result:
The API returns availability `true` for free values and `false` for stored values.

Constraints and invariants:
- Query parameters are required.
- Availability checks are read-only and do not reserve values.

Failure and exceptional cases:
- Failing function: `check username is available`
  - Failure condition: missing `username` query parameter.
  - Why it fails: Spring request binding requires it.
  - Violated prerequisite or constraint: required query value.
- Failing function: `create user`
  - Failure condition: duplicate `username` or `email` during setup.
  - Why it fails: service uniqueness checks reject duplicates.
  - Violated prerequisite or constraint: unique account identity.

Implementation notes:
Availability checks can race with later creation because there is no reservation/token mechanism.

### Behavior 4: Maintain Public User Profile Information
Business goal:
Allow users or admins to update profile/account details and extended contact information.

Domain context:
Profiles expose public user metadata and feed profile browsing.

Starting point:
An authenticated user exists.

Required execution workflow:
1. Use function `create user` (`POST /api/users`) as admin with `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, `password={password}` if the target user does not exist.
2. Use function `update user account` (`PUT /api/users/{username}`) as the same user or admin with path `username={username}` and body fields such as `firstName={newFirstName}`, `lastName={newLastName}`, `password={newPassword}`, `address={address}`, `phone={phone}`, `website={website}`, `company={company}`.
3. Use function `set or update current user info` (`PUT /api/users/setOrUpdateInfo`) as the authenticated target user with body `street={street}`, `suite={suite}`, `city={city}`, `zipcode={zipcode}`, optional `companyName={companyName}`, `catchPhrase={catchPhrase}`, `bs={bs}`, `website={website}`, `phone={phone}`, `lat={lat}`, `lng={lng}`.

Optional verification workflow:
1. Use function `get user profile` (`GET /api/users/{username}/profile`) with path `username={username}` to inspect persisted profile data.
2. Use function `get current user summary` (`GET /api/users/me`) as the authenticated user to inspect id, username, first name, and last name.

Existing-state shortcuts:
- Skip `create user` if the target account already exists.
- Direct database setup can create the account, but the authenticated principal must resolve to the same stored user for self updates.

Parameter and value bindings:
- The `username` path in `update user account` must match the stored user to update.
- `set or update current user info` uses authenticated principal username, not a path value.
- Address fields from `InfoRequest` are converted into `Address` and `Geo`; company fields are converted into `Company`.

Business result:
The account keeps the same username, email, and roles, while first/last name, password, address, company, phone, and website may change.

Constraints and invariants:
- `update user account` does not update username, email, or roles.
- `set or update current user info` requires nonblank `street`, `suite`, `city`, and `zipcode`.
- Password is always re-encoded on account update.

Failure and exceptional cases:
- Failing function: `update user account`
  - Failure condition: caller is neither the same user nor admin.
  - Why it fails: service checks target id against current user id or admin authority.
  - Violated prerequisite or constraint: self/admin update rule.
- Failing function: `set or update current user info`
  - Failure condition: blank `street`, `suite`, `city`, or `zipcode`.
  - Why it fails: bean validation rejects the request.
  - Violated prerequisite or constraint: required address fields.
- Failing function: `get user profile`
  - Failure condition: target username missing after setup.
  - Why it fails: repository lookup throws.
  - Violated prerequisite or constraint: profile owner must exist.

Implementation notes:
`set or update current user info` checks self/admin after resolving the current user, but because it updates only the current principal, the admin override is effectively redundant.

### Behavior 5: Delete a User Account
Business goal:
Remove a user account and its owned dependent data.

Domain context:
Account deletion is expected for lifecycle management, but implementation authorization is unusually strict.

Starting point:
An authenticated user exists and has admin authority.

Required execution workflow:
1. Use function `create user` (`POST /api/users`) as admin with `username={username}`, `email={email}`, `firstName={firstName}`, `lastName={lastName}`, `password={password}` if the target does not exist.
2. Use function `grant admin role` (`PUT /api/users/{username}/giveAdmin`) as admin with `username={username}` to ensure the target user also has `ROLE_ADMIN`.
3. Authenticate as that same `{username}` outside the available function inventory.
4. Use function `delete user account` (`DELETE /api/users/{username}`) with path `username={username}` as the same admin user.

Optional verification workflow:
1. Use function `get user profile` (`GET /api/users/{username}/profile`) with `username={username}` to verify deletion by expecting not found.

Existing-state shortcuts:
- Skip `create user` and `grant admin role` if the target account already exists and already has admin role.
- Direct database setup can create the target admin user.
- The current principal must be both the target user and an admin; a different admin is insufficient.

Parameter and value bindings:
- The same `username` must identify the created user, the role-granted user, the authenticated principal, and the delete path.

Business result:
The user row is deleted. JPA mappings imply cascade/orphan removal for that user’s todos, albums, posts, and comments.

Constraints and invariants:
- Implementation requires same-user-and-admin because it checks `if (!sameUser || !isAdmin)`.
- This is stricter than the controller annotation suggesting user or admin access.

Failure and exceptional cases:
- Failing function: `delete user account`
  - Failure condition: caller is an admin but not the target user.
  - Why it fails: implementation uses logical OR in the denial condition.
  - Violated prerequisite or constraint: implementation-specific same-user-and-admin rule.
- Failing function: `grant admin role`
  - Failure condition: target username does not exist.
  - Why it fails: service resolves target before role replacement.
  - Violated prerequisite or constraint: target account must exist.

Implementation notes:
This is a source/OpenAPI expectation mismatch: normal admin-managed deletion is blocked.

### Behavior 6: Maintain Category Taxonomy
Business goal:
Create, inspect, rename, and delete categories used to classify posts.

Domain context:
Categories are first-class organization resources for posts.

Starting point:
Authenticated user with `ROLE_USER` for creation; owner or admin for update/delete.

Required execution workflow:
1. Use function `create category` (`POST /api/categories`) with body `name={categoryName}` to create a category and capture generated `categoryId`.
2. Use function `update category` (`PUT /api/categories/{id}`) with path `id={categoryId}` and body `name={newCategoryName}` to rename it.
3. Use function `delete category` (`DELETE /api/categories/{id}`) with path `id={categoryId}` to remove it.

Optional verification workflow:
1. Use function `get category` (`GET /api/categories/{id}`) with `id={categoryId}` to inspect the category.
2. Use function `list categories` (`GET /api/categories`) with optional pagination to inspect the collection.
3. Use function `list posts by category` (`GET /api/posts/category/{id}`) with `id={categoryId}` to inspect posts in the category before deletion.

Existing-state shortcuts:
- Skip `create category` if the category already exists and its generated `categoryId` is known.
- Direct database setup may insert the category; `createdBy` must match the owner for non-admin updates/deletes.

Parameter and value bindings:
- The generated `categoryId` from `create category` is reused in get/update/delete and post creation/filtering.
- `createdBy` is the ownership field checked for update/delete.

Business result:
A category is created, optionally renamed, and optionally removed. If removed, JPA `Category.posts` has cascade/orphan removal, so deleting a category can delete its posts and their comments.

Constraints and invariants:
- Category names are not declared unique in the model.
- Creation does not explicitly use `currentUser`, but auditing likely sets `createdBy`.
- Owner or admin may update/delete.

Failure and exceptional cases:
- Failing function: `update category`
  - Failure condition: caller is not creator and not admin.
  - Why it fails: service compares `createdBy` with current user id or admin authority.
  - Violated prerequisite or constraint: creator/admin rule.
- Failing function: `get category`
  - Failure condition: wrong or missing `categoryId`.
  - Why it fails: repository lookup throws not found.
  - Violated prerequisite or constraint: category must exist.

Implementation notes:
No business validation prevents duplicate or blank-like category names beyond entity/persistence behavior.

### Behavior 7: Maintain Tag Taxonomy
Business goal:
Create, inspect, rename, and delete tags used to label posts.

Domain context:
Tags support cross-category discovery.

Starting point:
Authenticated user with `ROLE_USER` for creation; creator or admin for update/delete.

Required execution workflow:
1. Use function `create tag` (`POST /api/tags`) with body `name={tagName}` to create a tag and capture generated `tagId`.
2. Use function `update tag` (`PUT /api/tags/{id}`) with path `id={tagId}` and body `name={newTagName}` to rename it.
3. Use function `delete tag` (`DELETE /api/tags/{id}`) with path `id={tagId}` to remove it.

Optional verification workflow:
1. Use function `get tag` (`GET /api/tags/{id}`) with `id={tagId}`.
2. Use function `list tags` (`GET /api/tags`) with optional pagination.
3. Use function `list posts by tag` (`GET /api/posts/tag/{id}`) with `id={tagId}`.

Existing-state shortcuts:
- Skip `create tag` if the tag exists and `tagId` is known.
- Direct database setup may insert tag rows; `createdBy` must match for non-admin update/delete.

Parameter and value bindings:
- `tagId` generated by `create tag` is reused by get/update/delete and tag-filtered post listing.
- Tag name is reused by `create post` only when creating/linking tags by name, not by id.

Business result:
A tag exists, is renamed, or is removed. Existing post-tag relationships depend on join-table persistence behavior.

Constraints and invariants:
- Tag names are not unique at the table level.
- `create post` auto-creates tags by name if missing, which can bypass explicit tag lifecycle workflows.
- `update post` ignores tags, so renaming/deleting tags is the only available tag-maintenance path after post creation.

Failure and exceptional cases:
- Failing function: `delete tag`
  - Failure condition: caller is not creator and not admin.
  - Why it fails: service checks `createdBy` or admin authority.
  - Violated prerequisite or constraint: creator/admin rule.
- Failing function: `list posts by tag`
  - Failure condition: `tagId` does not exist.
  - Why it fails: service verifies tag before querying posts.
  - Violated prerequisite or constraint: tag must exist.

Implementation notes:
Because duplicate tag names can exist, `create post` lookup by name can be ambiguous at the domain level.

### Behavior 8: Publish a Categorized and Tagged Post
Business goal:
Create a blog post under a category and attach tags.

Domain context:
This is the central publishing workflow.

Starting point:
No prior content state, but an authenticated `ROLE_USER` principal exists.

Required execution workflow:
1. Use function `create category` (`POST /api/categories`) with body `name={categoryName}` to create the category and capture `categoryId`.
2. Optionally use function `create tag` (`POST /api/tags`) with body `name={tagName}` to pre-create tags and capture `tagId`; alternatively let post creation create tags by name.
3. Use function `create post` (`POST /api/posts`) with body `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, and `tags={tags}` to publish the post.
4. Use function `list posts` (`GET /api/posts`) with valid `page={page}`, `size={size}` and match `title={postTitle}` if the generated post id is needed later, because `create post` response does not include id.

Optional verification workflow:
1. Use function `list posts by category` (`GET /api/posts/category/{id}`) with `id={categoryId}`.
2. Use function `list posts by tag` (`GET /api/posts/tag/{id}`) with `id={tagId}` for pre-created or discovered tag ids.
3. Use function `get post` (`GET /api/posts/{id}`) with discovered `id={postId}`.

Existing-state shortcuts:
- Skip `create category` if `categoryId` already exists.
- Skip `create tag` because `create post` creates missing tags by name.
- Direct database setup can create category, tags, and post, but later functions need generated ids.

Parameter and value bindings:
- `categoryId` from `create category` is consumed by `create post` and category-filtered listing.
- Each string in `tags={tags}` is resolved by tag name; existing tag names are reused, missing names are created.
- `postTitle` may need to be unique for reliable id discovery through `list posts`.

Business result:
A post exists with title, body, owning user, category, and associated tags. Missing tags in the request are persisted automatically.

Constraints and invariants:
- Title length must be at least 10; body length at least 50.
- `categoryId` must exist.
- Post title is unique at the database table level, but service does not pre-check it.
- Response omits generated post id.

Failure and exceptional cases:
- Failing function: `create post`
  - Failure condition: nonexistent `categoryId`.
  - Why it fails: service resolves category before saving.
  - Violated prerequisite or constraint: post must belong to an existing category.
- Failing function: `create post`
  - Failure condition: duplicate title.
  - Why it fails: database unique constraint on `posts.title`, not explicit service validation.
  - Violated prerequisite or constraint: unique post title.
- Failing function: `create category`
  - Failure condition: caller lacks `ROLE_USER`.
  - Why it fails: method security blocks creation.
  - Violated prerequisite or constraint: authenticated user role.

Implementation notes:
`create post` returns `PostResponse` with title/body/category/tags but no id, forcing list-and-match or database lookup for later update/delete/comment workflows.

### Behavior 9: Edit, Reclassify, or Delete a Post
Business goal:
Maintain existing post content and category, or remove a post.

Domain context:
Post owners and admins need to correct or remove published content.

Starting point:
No prior content state for complete workflow; authenticated owner exists.

Required execution workflow:
1. Use function `create category` (`POST /api/categories`) with `name={categoryName}` and capture `categoryId`.
2. Use function `create post` (`POST /api/posts`) with `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, `tags={tags}`.
3. Use function `list posts` (`GET /api/posts`) with valid pagination and match `title={postTitle}` to obtain `postId`.
4. For edit/reclassification, use function `update post` (`PUT /api/posts/{id}`) with path `id={postId}` and body `title={newTitle}`, `body={newBody}`, `categoryId={categoryIdOrNewCategoryId}`, optional `tags={ignoredTags}`.
5. For removal, use function `delete post` (`DELETE /api/posts/{id}`) with path `id={postId}`.

Optional verification workflow:
1. Use function `get post` (`GET /api/posts/{id}`) with `id={postId}` to inspect updates.
2. Use function `list posts by category` (`GET /api/posts/category/{id}`) with the new category id.
3. After deletion, use function `get post` (`GET /api/posts/{id}`) and expect not found.

Existing-state shortcuts:
- Skip category/post creation if the post exists and `postId` is known.
- Direct database setup can provide exact `postId`.
- If moving to a new category, that replacement category must already exist or be created with `create category`.

Parameter and value bindings:
- `postId` discovered from `list posts` is reused in `update post`, `delete post`, and `get post`.
- `categoryId` in update must identify an existing category.
- `tags` in `update post` are intentionally ignored; they do not change post-tag relationships.

Business result:
The post’s title/body/category are changed, or the post is deleted. Deleting a post cascades comments through `Post.comments` orphan removal.

Constraints and invariants:
- Owner or admin can update/delete.
- Tags cannot be changed through post update.
- Update validates replacement category before authorization in source code.

Failure and exceptional cases:
- Failing function: `update post`
  - Failure condition: caller is not owner and not admin.
  - Why it fails: service checks post owner/admin before saving.
  - Violated prerequisite or constraint: post ownership/admin rule.
- Failing function: `update post`
  - Failure condition: body `categoryId` does not exist.
  - Why it fails: category is resolved before update.
  - Violated prerequisite or constraint: replacement category must exist.
- Failing function: `delete post`
  - Failure condition: wrong `postId`.
  - Why it fails: repository lookup throws not found.
  - Violated prerequisite or constraint: post must exist.

Implementation notes:
The OpenAPI/request model suggests tags may be supplied on update, but implementation ignores them.

### Behavior 10: Manage Post Discussion Comments
Business goal:
Add, read, edit, and delete comments under a post.

Domain context:
Comments are scoped child resources of posts and inherit domain meaning from the parent post.

Starting point:
No prior content state for complete workflow; authenticated commenter exists.

Required execution workflow:
1. Use function `create category` (`POST /api/categories`) with `name={categoryName}` and capture `categoryId`.
2. Use function `create post` (`POST /api/posts`) with `title={postTitle}`, `body={postBody}`, `categoryId={categoryId}`, `tags={tags}`.
3. Use function `list posts` (`GET /api/posts`) with valid pagination and match `title={postTitle}` to obtain `postId`.
4. Use function `add comment` (`POST /api/posts/{postId}/comments`) with path `postId={postId}` and body `body={commentBody}` to create the comment and capture `commentId`.
5. Use function `update comment` (`PUT /api/posts/{postId}/comments/{id}`) with path `postId={postId}`, `id={commentId}`, body `body={newCommentBody}` to edit it.
6. Use function `delete comment` (`DELETE /api/posts/{postId}/comments/{id}`) with path `postId={postId}`, `id={commentId}` to remove it.

Optional verification workflow:
1. Use function `list post comments` (`GET /api/posts/{postId}/comments`) with `postId={postId}`, optional pagination.
2. Use function `get comment` (`GET /api/posts/{postId}/comments/{id}`) with `postId={postId}`, `id={commentId}`.

Existing-state shortcuts:
- Skip category/post creation if an existing `postId` is known.
- Skip comment creation for read/update/delete if `commentId` exists and belongs to `postId`.
- Direct database setup can create comments, but `comment.post.id` must match the path `postId`.

Parameter and value bindings:
- `postId` discovered from post listing is reused in every comment function.
- `commentId` from `add comment` is reused in get/update/delete.
- Comment `name` and `email` are copied from the authenticated user, not request body.

Business result:
A comment exists under a post, can have its body changed, and can be removed. The comment remains scoped to the original post.

Constraints and invariants:
- Comment body minimum length is 10.
- Comment get/update/delete verify that the comment belongs to the path post.
- Owner or admin can update/delete.
- `list post comments` filters by `postId` but does not verify the post exists.

Failure and exceptional cases:
- Failing function: `add comment`
  - Failure condition: `postId` does not exist.
  - Why it fails: service loads post before saving.
  - Violated prerequisite or constraint: comment must attach to an existing post.
- Failing function: `get comment`
  - Failure condition: path `postId` belongs to a different post than `commentId`.
  - Why it fails: service compares stored comment post id to path post id and throws bad request.
  - Violated prerequisite or constraint: comment/post scope binding.
- Failing function: `update comment`
  - Failure condition: caller is not comment owner and not admin.
  - Why it fails: service checks comment user id/admin authority.
  - Violated prerequisite or constraint: comment ownership/admin rule.

Implementation notes:
`delete comment` returns an unsuccessful `ApiResponse` for post/comment mismatch instead of throwing like get/update.

### Behavior 11: Manage Albums
Business goal:
Create, view, rename, and delete photo albums.

Domain context:
Albums are owner-scoped containers for photos.

Starting point:
Authenticated `ROLE_USER` principal exists.

Required execution workflow:
1. Use function `create album` (`POST /api/albums`) with body `title={albumTitle}` and capture generated `albumId`.
2. Use function `update album` (`PUT /api/albums/{id}`) with path `id={albumId}` and body `title={newAlbumTitle}` to rename it.
3. Use function `delete album` (`DELETE /api/albums/{id}`) with path `id={albumId}` to remove it.

Optional verification workflow:
1. Use function `get album` (`GET /api/albums/{id}`) with `id={albumId}`.
2. Use function `list albums` (`GET /api/albums`) with optional pagination.
3. Use function `list album photos` (`GET /api/albums/{id}/photos`) with `id={albumId}` to inspect child photos.

Existing-state shortcuts:
- Skip `create album` if an album exists and `albumId` is known.
- Direct database setup can create the album, but `album.user.id` must match current user for non-admin update/delete.

Parameter and value bindings:
- Generated `albumId` is reused in get/update/delete and photo creation/listing.
- Album ownership is checked through `album.user.id`.

Business result:
An owned album is created, renamed, or deleted. Deleting an album cascades photos through `Album.photo` orphan removal.

Constraints and invariants:
- Album title is unique at database level, but not explicitly checked in service.
- Owner or admin may update/delete.
- Creation response uses HTTP 201.

Failure and exceptional cases:
- Failing function: `update album`
  - Failure condition: caller is not owner and not admin.
  - Why it fails: service checks album owner/admin.
  - Violated prerequisite or constraint: album ownership/admin rule.
- Failing function: `delete album`
  - Failure condition: wrong `albumId`.
  - Why it fails: repository lookup throws not found.
  - Violated prerequisite or constraint: album must exist.

Implementation notes:
`list album photos` does not verify album existence; it simply queries photos by album id.

### Behavior 12: Manage Photos Inside Albums
Business goal:
Create, inspect, update, move, and delete photos.

Domain context:
Photos belong to albums, and album ownership controls photo creation and maintenance.

Starting point:
Authenticated album owner exists.

Required execution workflow:
1. Use function `create album` (`POST /api/albums`) with body `title={albumTitle}` and capture `albumId`.
2. Use function `create photo` (`POST /api/photos`) with body `title={photoTitle}`, `url={url}`, `thumbnailUrl={thumbnailUrl}`, `albumId={albumId}` and capture `photoId`.
3. Use function `update photo` (`PUT /api/photos/{id}`) with path `id={photoId}` and body `title={newPhotoTitle}`, `url={ignoredUrl}`, `thumbnailUrl={newThumbnailUrl}`, `albumId={albumIdOrReplacementAlbumId}`.
4. Use function `delete photo` (`DELETE /api/photos/{id}`) with path `id={photoId}`.

Optional verification workflow:
1. Use function `get photo` (`GET /api/photos/{id}`) with `id={photoId}`.
2. Use function `list album photos` (`GET /api/albums/{id}/photos`) with `id={albumId}`.
3. Use function `list photos` (`GET /api/photos`) with optional pagination.

Existing-state shortcuts:
- Skip `create album` if an owned album exists and `albumId` is known.
- Skip `create photo` for update/delete if `photoId` already exists.
- Direct database setup can create album/photo rows; photo’s album owner must match current user for non-admin operations.

Parameter and value bindings:
- `albumId` from `create album` is consumed by `create photo`.
- `photoId` from `create photo` is reused by get/update/delete.
- Replacement `albumId` in `update photo` must identify an existing album.
- The request `url` in `update photo` is intentionally not bound to persisted state because implementation ignores it.

Business result:
A photo exists in an album, can have title/thumbnail and album changed, and can be deleted. Its original `url` remains unchanged on update.

Constraints and invariants:
- Create requires caller to own the target album.
- Update/delete require caller to own the photo’s current album or be admin.
- Update validates replacement album existence but does not require caller to own the replacement album.
- Photo title is unique at database level.

Failure and exceptional cases:
- Failing function: `create photo`
  - Failure condition: `albumId` does not exist.
  - Why it fails: service loads album before saving.
  - Violated prerequisite or constraint: photo must belong to an existing album.
- Failing function: `create photo`
  - Failure condition: caller does not own target album.
  - Why it fails: service compares album owner id to current user id.
  - Violated prerequisite or constraint: album ownership for photo creation.
- Failing function: `update photo`
  - Failure condition: replacement `albumId` does not exist.
  - Why it fails: replacement album is resolved before saving.
  - Violated prerequisite or constraint: target album must exist.

Implementation notes:
OpenAPI/request body implies `url` can be updated, but source only updates title, thumbnail, and album.

### Behavior 13: Manage Personal Todos
Business goal:
Create, list, read, update, complete, uncomplete, and delete private todos.

Domain context:
Todos are authenticated-user personal task items and are not global public content.

Starting point:
Authenticated `ROLE_USER` principal exists.

Required execution workflow:
1. Use function `create todo` (`POST /api/todos`) with body `title={todoTitle}`, optional `completed={completed}` and capture `todoId`.
2. Use function `update own todo` (`PUT /api/todos/{id}`) with path `id={todoId}` and body `title={newTodoTitle}`, `completed={completed}` to edit title/status.
3. Use function `complete todo` (`PUT /api/todos/{id}/complete`) with path `id={todoId}` to set `completed=true`.
4. Use function `uncomplete todo` (`PUT /api/todos/{id}/unComplete`) with path `id={todoId}` to set `completed=false`.
5. Use function `delete own todo` (`DELETE /api/todos/{id}`) with path `id={todoId}` to remove it.

Optional verification workflow:
1. Use function `list own todos` (`GET /api/todos`) with optional `page={page}`, `size={size}`.
2. Use function `get own todo` (`GET /api/todos/{id}`) with `id={todoId}`.

Existing-state shortcuts:
- Skip `create todo` if an owned todo exists and `todoId` is known.
- Direct database setup can create the todo, but `todo.user.id`/`createdBy` must match the current user.
- `uncomplete todo` does not actually require the todo to already be completed; it simply sets false.

Parameter and value bindings:
- `todoId` from `create todo` is reused in all subsequent todo path operations.
- Current user id is the scope for list/get/update/delete/complete/uncomplete.
- OpenAPI request bodies for complete/uncomplete are ignored.

Business result:
The current user owns a todo, can change its title and completion flag, and can delete it.

Constraints and invariants:
- Todo title must be nonblank.
- Todo title is unique at database level.
- No admin override exists for todo operations in service logic; only the owner can access.

Failure and exceptional cases:
- Failing function: `create todo`
  - Failure condition: blank or missing `title`.
  - Why it fails: bean validation on `Todo.title`.
  - Violated prerequisite or constraint: todo must have a title.
- Failing function: `get own todo`
  - Failure condition: another user requests the todo.
  - Why it fails: service compares todo owner id to current user id.
  - Violated prerequisite or constraint: todo ownership.
- Failing function: `complete todo`
  - Failure condition: wrong `todoId`.
  - Why it fails: repository lookup throws not found.
  - Violated prerequisite or constraint: todo must exist.

Implementation notes:
The list function filters by `createdBy`, while individual ownership checks use `todo.user.id`; these should align if auditing and explicit user assignment stay consistent.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Documented Authentication and Session Acquisition
Priority:
Critical domain gap

Expected business goal:
Create or obtain an authenticated session/JWT so protected workflows can be performed from no prior service state.

Why it is unsupported:
No authentication functions appear in `full-behavior.md` or `blogapi.json`.

Existing functions considered:
- `create user`: can create a user only when an admin is already authenticated.
- `get current user summary`: verifies an authenticated user but cannot create a session.
- `grant admin role`: requires an existing admin caller.

Missing capability:
Documented `signup` and `signin` functions in the function inventory/OpenAPI.

Proof that function composition is insufficient:
Protected functions require an authenticated principal, but no available function logs in, issues a token, or bootstraps the first admin. Direct database setup can create users but does not provide an API-realizable authentication workflow from the documented inventory.

Evidence from existing functions/source:
Source has `POST /api/auth/signup` and `POST /api/auth/signin`, but they are absent from the available function inventory and OpenAPI paths.

Business impact:
Complete API workflows cannot honestly start from no prior service state using only documented functions.

### Missing Behavior 2: Reliable Post Id Capture After Creation
Priority:
Important robustness gap

Expected business goal:
Create a post and use its generated id for immediate retrieve/update/delete/comment workflows.

Why it is unsupported:
`create post` returns `PostResponse` without the generated id.

Existing functions considered:
- `create post`: persists the post but omits id.
- `list posts`: can be used to search by title, but that is indirect and ambiguous if title uniqueness errors are not handled cleanly.
- `get post`, `update post`, `delete post`, `add comment`: all require `postId`.

Missing capability:
Return `id` or `Location` from `create post`, or provide a direct lookup by unique slug/title.

Proof that function composition is insufficient:
Listing and matching can fail under pagination, race conditions, duplicate-attempt errors, or non-unique client assumptions. It is not equivalent to returning the created id.

Evidence from existing functions/source:
`PostServiceImpl.addPost` builds a response with title, body, category, and tags only.

Business impact:
Post workflows are brittle and require extra reads.

### Missing Behavior 3: Update a Post’s Tags
Priority:
Critical domain gap

Expected business goal:
Change tags on an existing post.

Why it is unsupported:
`update post` accepts optional `tags` but ignores them.

Existing functions considered:
- `update post`: changes title, body, category only.
- `create tag`, `update tag`, `delete tag`: maintain tag resources but do not attach/detach them from an existing post.
- `delete post` plus `create post`: loses post id, timestamps, comments, and continuity.

Missing capability:
A post tag replacement/add/remove endpoint or implementation support in `update post`.

Proof that function composition is insufficient:
No function mutates the post-tag join table for an existing post. Delete-and-recreate is not equivalent because comments and identity are not preserved.

Evidence from existing functions/source:
`PostServiceImpl.updatePost` sets title, body, and category; it never reads `newPostRequest.getTags()`.

Business impact:
Published content cannot be re-tagged, weakening discovery and taxonomy maintenance.

### Missing Behavior 4: Update a Photo URL
Priority:
Important robustness gap

Expected business goal:
Correct the main URL of an existing photo.

Why it is unsupported:
`update photo` ignores the submitted `url`.

Existing functions considered:
- `update photo`: updates title, thumbnail URL, and album only.
- `delete photo` plus `create photo`: creates a new id and may disturb references/order.

Missing capability:
Persist `url` in `update photo`.

Proof that function composition is insufficient:
The only way to change URL is delete/recreate, which is not equivalent to preserving the photo identity.

Evidence from existing functions/source:
`PhotoServiceImpl.updatePhoto` never calls `photo.setUrl(...)`.

Business impact:
Broken photo URLs cannot be repaired in place.

### Missing Behavior 5: Safe Ownership Enforcement When Moving Photos
Priority:
Critical domain gap

Expected business goal:
Move a photo only into an album the caller owns or administers.

Why it is unsupported:
`update photo` checks ownership of the current album but not the replacement album.

Existing functions considered:
- `update photo`: can set `albumId` to any existing album after current-photo ownership passes.
- `create photo`: correctly requires ownership of the target album.
- `get album`: can inspect album but does not enforce move authorization.

Missing capability:
Replacement-album ownership/admin validation in `update photo`.

Proof that function composition is insufficient:
No existing function can prevent a valid update request from pointing to another user’s album once the caller owns the current photo.

Evidence from existing functions/source:
`PhotoServiceImpl.updatePhoto` resolves the new album, then checks only `photo.getAlbum().getUser().getId()` against current user.

Business impact:
Users can place their photos into albums they do not own, corrupting album ownership semantics.

### Missing Behavior 6: Normal Admin User Deletion
Priority:
Critical domain gap

Expected business goal:
Allow admins to delete other users, or allow normal users to delete themselves as the controller annotation suggests.

Why it is unsupported:
Implementation permits deletion only when the caller is both the same user and an admin.

Existing functions considered:
- `delete user account`: blocks different admin users and normal self-delete.
- `grant admin role`: can make target admin but still requires authenticating as target.
- `revoke admin role`: cannot help deletion authorization.

Missing capability:
Correct self-or-admin authorization logic.

Proof that function composition is insufficient:
Granting admin to a target does not let an administrator delete that target unless the administrator can authenticate as the target, which is not a legitimate admin workflow.

Evidence from existing functions/source:
`UserServiceImpl.deleteUser` denies when `!sameUser || !isAdmin`.

Business impact:
Account administration and account closure workflows are blocked or unsafe.

### Missing Behavior 7: Safe Delete/Impact Preview for Categories, Tags, Albums, and Users
Priority:
Important robustness gap

Expected business goal:
Know whether deleting a parent resource will cascade, fail, or orphan related resources.

Why it is unsupported:
Delete functions do not provide dependency previews or safe-delete checks.

Existing functions considered:
- `delete category`: may cascade posts/comments through JPA.
- `delete album`: cascades photos.
- `delete user account`: cascades user-owned resources.
- `delete tag`: does not expose join-table impact.
- List/filter functions can inspect some relationships but not complete cascade effects.

Missing capability:
Dependency count/impact endpoint or guarded delete mode.

Proof that function composition is insufficient:
Existing lists cannot fully predict JPA cascade/orphan behavior, join-table cleanup, or database constraint outcomes.

Evidence from existing functions/source:
Entity mappings define cascade/orphan removal for users, albums, categories, posts, and comments.

Business impact:
Clients can accidentally remove large portions of content.

### Missing Behavior 8: Search, Lookup, and Stable Filtering by Business Keys
Priority:
API ergonomics gap

Expected business goal:
Find resources by title, name, email, username, or other business keys without scanning pages.

Why it is unsupported:
Only limited availability checks and scoped list endpoints exist.

Existing functions considered:
- `list posts`, `list albums`, `list photos`, `list categories`, `list tags`: paged scans only.
- `check username is available`, `check email is available`: boolean only, not retrieval.
- `get user profile`: supports username only for users.

Missing capability:
Search/query endpoints for posts, albums, photos, categories, tags, and todos.

Proof that function composition is insufficient:
Paged scans can miss data, require client-side filtering, and are unstable under concurrent writes.

Evidence from existing functions/source:
Repositories have specific finders, but controllers expose only fixed list and id reads.

Business impact:
Clients cannot reliably locate resources created by workflows when ids are not returned.

## Cross-Behavior Observations
- Authentication bootstrap exists in source but not in `full-behavior.md` or OpenAPI.
- Ownership rules are inconsistent: posts/comments/albums/photos allow admin override, todos do not, and user deletion requires same-user-and-admin.
- Several database uniqueness constraints are not service-validated: album title, photo title, post title, todo title.
- Category and tag names are not unique, which weakens taxonomy semantics.
- `create post` auto-creates tags by name, but `update post` cannot change tags.
- `update photo` ignores `url` and can move a photo into another user’s album.
- Some scoped list functions do not verify parent existence: `list album photos` and `list post comments`.
- Parent deletes may cascade significant child state through JPA mappings.
- OpenAPI includes misleading request/body/query details for some endpoints that implementation ignores.

## Coverage Summary
Supported domain areas:
User profile reads/updates, admin user provisioning, role changes, public content browsing, category/tag lifecycle, post publishing/editing/deletion, comments, albums/photos, and personal todos.

Partially supported domain areas:
Authentication bootstrap, post id discovery, tag maintenance on existing posts, photo metadata repair, account deletion, and safe parent-resource deletion.

Unsupported domain areas:
Documented login/signup workflows, stable business-key search, post tag mutation, in-place photo URL update, dependency-aware deletes, complete role inspection, and consistent ownership validation.