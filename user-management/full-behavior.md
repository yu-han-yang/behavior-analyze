Source/OpenAPI discrepancies observed: the implementation returns `201 Created` for several create, update, and association endpoints where Swagger also lists `200 OK`; `GET /users/rbac/salt` returns `201 Created` in the implementation while Swagger documents `200 OK`. Swagger does not mark most model fields as required, but the implementation validates passwords, email addresses, phone numbers, genders, unique usernames, unique emails, unique role names, and unique permission keys; the database schema also requires user `username`, `password`, `name`, `surname`, and contact `email`.

### Function 1: authenticate user

Function name:
authenticate user

Core endpoint(s):
- `POST /login`

Preconditions:
- An enabled user exists with a unique `username`, encrypted password, valid email contact, `gender` equal to `MALE` or `FEMALE`, and a default role. This can be satisfied by directly inserting rows into `users`, `contacts`, `addresses`, and `users_roles`, or by calling `POST /users/register` with `username`, plaintext `password`, valid `email`, `name`, `surname`, and `gender`.
- The plaintext `password` used by `POST /login` must match the password originally supplied to `POST /users/register` or the value encrypted into the database with the configured application salt.
- If the API establishes the user, the `username` returned by or supplied to `POST /users/register` must be reused in the login body.

Successful execution:
- Result:
  The core endpoint logs in an enabled user, updates the user's `loginDt`, and returns user presentation data with roles and enabled permissions.
- Invocation:
  Step 1: `POST /login` with JSON body fields `username` and plaintext `password`
- Constraints:
  `username` and `password` must be non-empty. The user must exist, must be enabled, and the provided plaintext password must validate against the stored encrypted password using the configured salt.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The login request has a missing, null, or empty `username` or `password`; no database setup is required because validation fails before user lookup.
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    The login service rejects empty credentials before looking up a user.
  - Intentionally violated constraints:
    Required login credentials are omitted or empty.
- Branch 2:
  - Preconditions:
    - No user exists for `{username}` in the `users` table. This can be produced by starting from an empty database, deleting the user beforehand, or intentionally not inserting the user directly and not calling `POST /users/register` or `POST /users`.
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    The user lookup by username returns null and the service raises an invalid-login error.
  - Intentionally violated constraints:
    The required existing user state is omitted.
- Branch 3:
  - Preconditions:
    - A user exists for `{username}` with an encrypted password derived from `{originalPassword}`. This can be satisfied by direct database insertion using the application salt or by calling `POST /users/register` with `{username}` and `{originalPassword}`.
    - The login body reuses `{username}` but supplies `{differentPassword}` instead of `{originalPassword}`.
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    The stored encrypted password comparison fails.
  - Intentionally violated constraints:
    The login request does not reuse the plaintext password that matches the stored credential.
- Branch 4:
  - Preconditions:
    - A user exists for `{username}` with a valid encrypted password and `enabled=false`. This can be satisfied by direct database insertion or by calling `POST /users/register` and then `PUT /users/{id}` with the returned `id`, a valid full user body, and `enabled=false`.
    - If the API creates and disables the user, the `{id}` returned by registration must be reused in `PUT /users/{id}`, and `{username}` plus the matching plaintext password must be reused in the login request.
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    Password validation succeeds, but the service rejects users whose `enabled` flag is false.
  - Intentionally violated constraints:
    The user is intentionally placed in a disabled state before authentication.

Endpoint coverage:
- Covers:
  `POST /login`
- Distinct meaning:
  Authenticates an enabled user and updates login metadata.

### Function 2: list users

Function name:
list users

Core endpoint(s):
- `GET /users`

Preconditions:
- No prerequisite user state is required. The database may contain zero or more rows in `users`.

Successful execution:
- Result:
  The core endpoint returns a `UserListDTO` containing presentation data for all stored users.
- Invocation:
  Step 1: `GET /users`
- Constraints:
  The endpoint performs a global read and does not require a user id, request body, or prior setup endpoint.

Failure or exceptional branches:
- None identified from the OpenAPI definition or implementation logic.

Endpoint coverage:
- Covers:
  `GET /users`
- Distinct meaning:
  Reads the global user collection.

### Function 3: create full user

Function name:
create full user

Core endpoint(s):
- `POST /users`

Preconditions:
- The default role with `id = 1` and `role = USER` exists because user creation always assigns `Role.USER`. This can be satisfied by `src/main/resources/data.sql` or by directly inserting the row into `roles`; an ordinary `POST /users/rbac/roles` call cannot reliably satisfy the exact `id = 1` requirement unless the database sequence produces that id.

Successful execution:
- Result:
  The core endpoint creates a complete user with encrypted credentials, identity fields, contact data, address data, `enabled=true`, the requested `secured` value, and the default `USER` role.
- Invocation:
  Step 1: `POST /users` with JSON body fields including unique `username`, valid `password`, unique valid `email`, valid non-empty `phone`, `name`, `surname`, `gender`, optional profile/contact/address fields, and optional `secured`
- Constraints:
  `password` must be 8 to 60 characters with a digit, uppercase letter, lowercase letter, special character, and no spaces. `email` must be non-empty, valid, and at most 255 characters. `phone` must match the international phone pattern and be at most 50 characters. `gender` must be `MALE` or `FEMALE`. `username` and `email` must be unused. The implementation sets `enabled=true` regardless of the request body's `enabled` value.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request body is missing or contains invalid required values, such as null data, invalid `password`, invalid `email`, missing or invalid `phone`, missing `name` or `surname`, or unsupported `gender`; no database setup is required for this validation failure.
  - Failure endpoint:
    `POST /users`
  - Why this fails:
    The service validates the DTO, password, email, phone, and gender before saving; database constraints also require core user and contact fields.
  - Intentionally violated constraints:
    Required body values or format rules are violated.
- Branch 2:
  - Preconditions:
    - A user with `{username}` already exists. This can be satisfied by directly inserting a `users` row with `username = {username}` and matching contact/address state, or by calling `POST /users` once with that `username`.
  - Failure endpoint:
    `POST /users`
  - Why this fails:
    The service looks up the username and rejects duplicate usernames before saving.
  - Intentionally violated constraints:
    The unique username requirement is violated.
- Branch 3:
  - Preconditions:
    - A contact with `{email}` already exists for another user. This can be satisfied by directly inserting rows into `users` and `contacts`, or by calling `POST /users` once with that `email`.
  - Failure endpoint:
    `POST /users`
  - Why this fails:
    The service looks up the contact email and rejects duplicate emails before saving.
  - Intentionally violated constraints:
    The unique email requirement is violated.

Endpoint coverage:
- Covers:
  `POST /users`
- Distinct meaning:
  Creates a complete user record with contact, address, credentials, default role, and secured flag.

### Function 4: register minimal user account

Function name:
register minimal user account

Core endpoint(s):
- `POST /users/register`

Preconditions:
- The default role with `id = 1` and `role = USER` exists because registration always assigns `Role.USER`. This can be satisfied by `src/main/resources/data.sql` or by directly inserting the row into `roles`; an ordinary `POST /users/rbac/roles` call cannot reliably satisfy the exact `id = 1` requirement unless the database sequence produces that id.

Successful execution:
- Result:
  The core endpoint creates a minimal user account with encrypted password, identity fields, email contact, empty address, `enabled=true`, `secured=false`, and the default `USER` role.
- Invocation:
  Step 1: `POST /users/register` with JSON body fields `username`, `password`, `email`, `name`, `surname`, and `gender`
- Constraints:
  `username` and `email` must be unused. `password` must satisfy the password validator. `email` must satisfy the email validator. `gender` must be `MALE` or `FEMALE`. `name` and `surname` must satisfy non-null user columns.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The registration body is missing or contains invalid required values, such as invalid `password`, invalid `email`, missing `name` or `surname`, or unsupported `gender`; no database setup is required for this validation failure.
  - Failure endpoint:
    `POST /users/register`
  - Why this fails:
    The service validates registration data before creating the account.
  - Intentionally violated constraints:
    Required registration values or format rules are violated.
- Branch 2:
  - Preconditions:
    - A user with `{username}` already exists. This can be satisfied by directly inserting a `users` row with `username = {username}` and required related rows, or by calling `POST /users/register` once with that `username`.
  - Failure endpoint:
    `POST /users/register`
  - Why this fails:
    The service rejects duplicate usernames.
  - Intentionally violated constraints:
    The unique username requirement is violated.
- Branch 3:
  - Preconditions:
    - A contact with `{email}` already exists for another user. This can be satisfied by directly inserting rows into `users` and `contacts`, or by calling `POST /users/register` once with that `email`.
  - Failure endpoint:
    `POST /users/register`
  - Why this fails:
    The service rejects duplicate contact emails.
  - Intentionally violated constraints:
    The unique email requirement is violated.

Endpoint coverage:
- Covers:
  `POST /users/register`
- Distinct meaning:
  Creates a reduced account for self-registration.

### Function 5: retrieve user by id

Function name:
retrieve user by id

Core endpoint(s):
- `GET /users/{id}`

Preconditions:
- A user exists with `{id}`. This can be satisfied by directly inserting rows into `users`, `contacts`, optional `addresses`, and role association tables, or by calling `POST /users/register` and using the returned user `id`.
- If the API establishes the user, the generated `id` from the creation response must be reused as `{id}`.

Successful execution:
- Result:
  The core endpoint returns presentation data for the selected user.
- Invocation:
  Step 1: `GET /users/{id}` with `{id}` set to an existing user id
- Constraints:
  `{id}` must be non-null and must identify a stored user.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user exists with `{id}`. This can be produced by starting from an empty database, deleting the user beforehand, or intentionally not inserting the user directly and not calling `POST /users/register` or `POST /users`.
  - Failure endpoint:
    `GET /users/{id}`
  - Why this fails:
    The repository lookup by id returns empty.
  - Intentionally violated constraints:
    The required existing user state is omitted or the wrong generated id is used.

Endpoint coverage:
- Covers:
  `GET /users/{id}`
- Distinct meaning:
  Reads one specific user resource.

### Function 6: update user

Function name:
update user

Core endpoint(s):
- `PUT /users/{id}`

Preconditions:
- A user exists with `{id}` and has contact state available for update. This can be satisfied by directly inserting rows into `users`, `contacts`, optional `addresses`, and role association tables, or by calling `POST /users/register` and using the returned user `id`.
- If the API establishes the user, the generated `id` from the creation response must be reused as `{id}`.

Successful execution:
- Result:
  The core endpoint updates editable user profile fields, encrypted password, gender, birth date, enabled flag, note, contact fields, address fields, and `updatedDt`.
- Invocation:
  Step 1: `PUT /users/{id}` with `{id}` set to an existing user id and a valid full `CreateOrUpdateUserDTO` JSON body
- Constraints:
  The body must contain a valid password, valid email, valid non-empty phone, and `gender` equal to `MALE` or `FEMALE`. The updated `username` and `email` may match the same user but must not belong to another user. The endpoint does not update the `secured` flag.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user exists with `{id}`. This can be produced by starting from an empty database, deleting the user beforehand, or intentionally not inserting the user directly and not calling `POST /users/register` or `POST /users`.
  - Failure endpoint:
    `PUT /users/{id}`
  - Why this fails:
    The service looks up the user id before applying updates and cannot find it.
  - Intentionally violated constraints:
    The required target user state is omitted or the wrong generated id is used.
- Branch 2:
  - Preconditions:
    - A user exists with `{id}`. This can be satisfied by direct database insertion or by calling `POST /users/register` and reusing the returned `id`.
    - The update body is null or contains invalid data, such as invalid `password`, invalid `email`, missing or invalid `phone`, or unsupported `gender`.
  - Failure endpoint:
    `PUT /users/{id}`
  - Why this fails:
    The service validates the update DTO before saving.
  - Intentionally violated constraints:
    The request targets an existing user but violates update body validation rules.
- Branch 3:
  - Preconditions:
    - A target user exists with `{id}`. This can be satisfied by direct database insertion or by calling `POST /users/register` and reusing the returned `id`.
    - A different user already owns `{otherUsername}`. This can be satisfied by direct database insertion or by calling `POST /users/register` for another account and taking its `username`.
  - Failure endpoint:
    `PUT /users/{id}`
  - Why this fails:
    The service permits the current user's own username but rejects a username owned by a different user id.
  - Intentionally violated constraints:
    The update body reuses another user's unique `username`.
- Branch 4:
  - Preconditions:
    - A target user exists with `{id}`. This can be satisfied by direct database insertion or by calling `POST /users/register` and reusing the returned `id`.
    - A different user's contact already owns `{otherEmail}`. This can be satisfied by direct database insertion or by calling `POST /users/register` for another account and taking its `email`.
  - Failure endpoint:
    `PUT /users/{id}`
  - Why this fails:
    The service permits the current user's own email but rejects an email owned by a different user id.
  - Intentionally violated constraints:
    The update body reuses another user's unique `email`.

Endpoint coverage:
- Covers:
  `PUT /users/{id}`
- Distinct meaning:
  Replaces editable user profile, contact, address, credential, and enabled-state fields.

### Function 7: delete non-secured user

Function name:
delete non-secured user

Core endpoint(s):
- `DELETE /users/{id}`

Preconditions:
- A non-secured user exists with `{id}`. This can be satisfied by directly inserting a `users` row with `secured=false` plus required related rows, or by calling `POST /users/register` and using the returned user `id`; registration creates `secured=false`.
- If the API establishes the user, the generated `id` from the creation response must be reused as `{id}`.

Successful execution:
- Result:
  The core endpoint deletes the selected user when `secured=false`.
- Invocation:
  Step 1: `DELETE /users/{id}` with `{id}` set to an existing non-secured user id
- Constraints:
  `{id}` must identify an existing user and that user must not be secured.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No user exists with `{id}`. This can be produced by starting from an empty database, deleting the user beforehand, or intentionally not inserting the user directly and not calling `POST /users/register` or `POST /users`.
  - Failure endpoint:
    `DELETE /users/{id}`
  - Why this fails:
    The service cannot find the user by id.
  - Intentionally violated constraints:
    The required target user state is omitted or the wrong generated id is used.
- Branch 2:
  - Preconditions:
    - A secured user exists with `{id}` and `secured=true`. This can be satisfied by directly inserting the user with `secured=true`, or by calling `POST /users` with a valid full user body and `secured=true` and using the returned `id`.
  - Failure endpoint:
    `DELETE /users/{id}`
  - Why this fails:
    The service explicitly refuses to delete secured users.
  - Intentionally violated constraints:
    The target user is intentionally secured before deletion.

Endpoint coverage:
- Covers:
  `DELETE /users/{id}`
- Distinct meaning:
  Removes a user only when the user is not secured.

### Function 8: assign role to user

Function name:
assign role to user

Core endpoint(s):
- `POST /users/{id}/roles/{roleId}`

Preconditions:
- A user exists with `{id}`. This can be satisfied by directly inserting rows into `users`, `contacts`, optional `addresses`, and role association tables, or by calling `POST /users/register` and using the returned user `id`.
- A role exists with `{roleId}`. This can be satisfied by directly inserting a row into `roles`, by using a seeded role from `data.sql`, or by calling `POST /users/rbac/roles` with a non-empty unique role-name string and using the returned role `id`.
- If the API establishes these resources, the generated user `id` and role `id` must be reused in the path.

Successful execution:
- Result:
  The core endpoint associates the existing role with the existing user and returns the updated user presentation.
- Invocation:
  Step 1: `POST /users/{id}/roles/{roleId}` with `{id}` set to an existing user id and `{roleId}` set to an existing role id
- Constraints:
  Both path ids must identify existing resources. If the role is already assigned, the `Set<Role>` prevents an additional duplicate and the call still succeeds.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A role exists with `{roleId}`. This can be satisfied by direct database insertion, by using a seeded role, or by calling `POST /users/rbac/roles` and reusing the returned `id`.
    - No user exists with `{id}`. This can be produced by intentionally not inserting the user directly and not calling `POST /users/register` or `POST /users`.
  - Failure endpoint:
    `POST /users/{id}/roles/{roleId}`
  - Why this fails:
    The service validates the user before validating or saving the role association.
  - Intentionally violated constraints:
    The required target user state is omitted or the wrong generated user id is used.
- Branch 2:
  - Preconditions:
    - A user exists with `{id}`. This can be satisfied by direct database insertion or by calling `POST /users/register` and reusing the returned `id`.
    - No role exists with `{roleId}`. This can be produced by intentionally not inserting the role directly, not using a seeded role id, and not calling `POST /users/rbac/roles`.
  - Failure endpoint:
    `POST /users/{id}/roles/{roleId}`
  - Why this fails:
    The service cannot find the role by id.
  - Intentionally violated constraints:
    The required role state is omitted or the wrong generated role id is used.

Endpoint coverage:
- Covers:
  `POST /users/{id}/roles/{roleId}`
- Distinct meaning:
  Adds or ensures a role association on a user.

### Function 9: remove role from user

Function name:
remove role from user

Core endpoint(s):
- `DELETE /users/{id}/roles/{roleId}`

Preconditions:
- A user exists with `{id}`. This can be satisfied by directly inserting rows into `users`, `contacts`, optional `addresses`, and role association tables, or by calling `POST /users/register` and using the returned user `id`.
- A role exists with `{roleId}`. This can be satisfied by directly inserting a row into `roles`, by using a seeded role from `data.sql`, or by calling `POST /users/rbac/roles` with a non-empty unique role-name string and using the returned role `id`.
- The user-role association exists in `users_roles` for `{id}` and `{roleId}` when the intended effect is visible removal. This can be satisfied by directly inserting the association row or by calling `POST /users/{id}/roles/{roleId}` after the user and role exist.
- If the API establishes these resources, the generated user `id` and role `id` must be reused in both the association setup request and the delete path.

Successful execution:
- Result:
  The core endpoint removes the role association from the existing user and returns the updated user presentation.
- Invocation:
  Step 1: `DELETE /users/{id}/roles/{roleId}` with `{id}` set to an existing user id and `{roleId}` set to an existing role id
- Constraints:
  Both path ids must identify existing resources. If the role exists but is not assigned to the user, the implementation still succeeds and leaves the role set unchanged.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A role exists with `{roleId}`. This can be satisfied by direct database insertion, by using a seeded role, or by calling `POST /users/rbac/roles` and reusing the returned `id`.
    - No user exists with `{id}`. This can be produced by intentionally not inserting the user directly and not calling `POST /users/register` or `POST /users`.
  - Failure endpoint:
    `DELETE /users/{id}/roles/{roleId}`
  - Why this fails:
    The service validates the user before removing a role.
  - Intentionally violated constraints:
    The required target user state is omitted or the wrong generated user id is used.
- Branch 2:
  - Preconditions:
    - A user exists with `{id}`. This can be satisfied by direct database insertion or by calling `POST /users/register` and reusing the returned `id`.
    - No role exists with `{roleId}`. This can be produced by intentionally not inserting the role directly, not using a seeded role id, and not calling `POST /users/rbac/roles`.
  - Failure endpoint:
    `DELETE /users/{id}/roles/{roleId}`
  - Why this fails:
    The service cannot find the role by id.
  - Intentionally violated constraints:
    The required role state is omitted or the wrong generated role id is used.

Endpoint coverage:
- Covers:
  `DELETE /users/{id}/roles/{roleId}`
- Distinct meaning:
  Removes or ensures absence of a role association on a user.

### Function 10: list roles

Function name:
list roles

Core endpoint(s):
- `GET /users/rbac/roles`

Preconditions:
- No prerequisite role state is required. The database may contain zero or more rows in `roles`.

Successful execution:
- Result:
  The core endpoint returns all roles with their associated permissions.
- Invocation:
  Step 1: `GET /users/rbac/roles`
- Constraints:
  The endpoint performs a global read and does not require a role id or request body.

Failure or exceptional branches:
- None identified from the OpenAPI definition or implementation logic.

Endpoint coverage:
- Covers:
  `GET /users/rbac/roles`
- Distinct meaning:
  Reads the global role collection.

### Function 11: create role

Function name:
create role

Core endpoint(s):
- `POST /users/rbac/roles`

Preconditions:
- No prerequisite role state is required beyond the absence of the requested role name.

Successful execution:
- Result:
  The core endpoint creates a new role with a unique role name and no permissions.
- Invocation:
  Step 1: `POST /users/rbac/roles` with a non-empty string request body containing the role name
- Constraints:
  The role-name string must be non-null, non-empty, and not already used in the `roles` table.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request body omits the role-name string or provides a null or empty role name; no database setup is required for this validation failure.
  - Failure endpoint:
    `POST /users/rbac/roles`
  - Why this fails:
    The role service validates the role name before saving.
  - Intentionally violated constraints:
    Required role name is omitted or empty.
- Branch 2:
  - Preconditions:
    - A role with `{roleName}` already exists. This can be satisfied by directly inserting a `roles` row with `role = {roleName}`, by using a seeded role name, or by calling `POST /users/rbac/roles` once with the same request body.
  - Failure endpoint:
    `POST /users/rbac/roles`
  - Why this fails:
    The repository finds an existing role with that name and the service raises `RoleInUseException`.
  - Intentionally violated constraints:
    The unique role-name requirement is violated.

Endpoint coverage:
- Covers:
  `POST /users/rbac/roles`
- Distinct meaning:
  Creates a new RBAC role.

### Function 12: retrieve role by id

Function name:
retrieve role by id

Core endpoint(s):
- `GET /users/rbac/roles/{roleId}`

Preconditions:
- A role exists with `{roleId}`. This can be satisfied by directly inserting a row into `roles`, by using a seeded role from `data.sql`, or by calling `POST /users/rbac/roles` with a non-empty unique role-name string and using the returned role `id`.
- If the API establishes the role, the generated role `id` must be reused as `{roleId}`.

Successful execution:
- Result:
  The core endpoint returns the selected role and its permissions.
- Invocation:
  Step 1: `GET /users/rbac/roles/{roleId}` with `{roleId}` set to an existing role id
- Constraints:
  `{roleId}` must be non-null and must identify a stored role.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No role exists with `{roleId}`. This can be produced by starting from an empty `roles` table, choosing an id that was not seeded, deleting the role beforehand, or intentionally not inserting it directly and not calling `POST /users/rbac/roles`.
  - Failure endpoint:
    `GET /users/rbac/roles/{roleId}`
  - Why this fails:
    The service cannot find the role by id.
  - Intentionally violated constraints:
    The required role state is omitted or the wrong generated role id is used.

Endpoint coverage:
- Covers:
  `GET /users/rbac/roles/{roleId}`
- Distinct meaning:
  Reads one specific role resource.

### Function 13: delete unused role

Function name:
delete unused role

Core endpoint(s):
- `DELETE /users/rbac/roles/{roleId}`

Preconditions:
- A role exists with `{roleId}`. This can be satisfied by directly inserting a row into `roles`, by using a seeded role from `data.sql`, or by calling `POST /users/rbac/roles` with a non-empty unique role-name string and using the returned role `id`.
- No `users_roles` row references `{roleId}`. This can be satisfied by direct database setup that leaves the association table empty for the role or by creating a new role through `POST /users/rbac/roles` and not assigning it to any user.
- If the API establishes the role, the generated role `id` must be reused as `{roleId}`.

Successful execution:
- Result:
  The core endpoint deletes the selected role when it is not assigned to any user.
- Invocation:
  Step 1: `DELETE /users/rbac/roles/{roleId}` with `{roleId}` set to an existing unused role id
- Constraints:
  The role must exist and `roleRepository.countRoleUsage(roleId)` must return zero.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No role exists with `{roleId}`. This can be produced by choosing an id that was not seeded, deleting the role beforehand, or intentionally not inserting it directly and not calling `POST /users/rbac/roles`.
  - Failure endpoint:
    `DELETE /users/rbac/roles/{roleId}`
  - Why this fails:
    The service cannot find the role before deletion.
  - Intentionally violated constraints:
    The required role state is omitted or the wrong generated role id is used.
- Branch 2:
  - Preconditions:
    - A user exists with `{id}`. This can be satisfied by direct database insertion or by calling `POST /users/register` and reusing the returned user `id`.
    - A role exists with `{roleId}`. This can be satisfied by direct database insertion or by calling `POST /users/rbac/roles` and reusing the returned role `id`.
    - The role is assigned to the user through a `users_roles` row for `{id}` and `{roleId}`. This can be satisfied by direct database insertion or by calling `POST /users/{id}/roles/{roleId}` after the user and role exist.
  - Failure endpoint:
    `DELETE /users/rbac/roles/{roleId}`
  - Why this fails:
    The service counts rows in `users_roles` and rejects deletion when the role is in use.
  - Intentionally violated constraints:
    The role is intentionally placed in use before deletion.

Endpoint coverage:
- Covers:
  `DELETE /users/rbac/roles/{roleId}`
- Distinct meaning:
  Deletes a role only if no user currently uses it.

### Function 14: list permissions

Function name:
list permissions

Core endpoint(s):
- `GET /users/rbac/permissions`

Preconditions:
- No prerequisite permission state is required. The database may contain zero or more rows in `permissions`.

Successful execution:
- Result:
  The core endpoint returns all permissions.
- Invocation:
  Step 1: `GET /users/rbac/permissions`
- Constraints:
  The endpoint performs a global read and does not require a permission key or request body.

Failure or exceptional branches:
- None identified from the OpenAPI definition or implementation logic.

Endpoint coverage:
- Covers:
  `GET /users/rbac/permissions`
- Distinct meaning:
  Reads the global permission collection.

### Function 15: create permission

Function name:
create permission

Core endpoint(s):
- `POST /users/rbac/permissions`

Preconditions:
- No prerequisite permission state is required beyond the absence of the requested permission key.

Successful execution:
- Result:
  The core endpoint creates a new permission with a unique permission key, enabled flag, and optional note.
- Invocation:
  Step 1: `POST /users/rbac/permissions` with a `PermissionDTO` JSON body containing non-empty unique `permission`, optional `enabled`, and optional `note`
- Constraints:
  The request body must not be null. `permission` must be non-null, non-empty, and unused. The request body `id` is ignored for creation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request body is missing or `permission` is null or empty; no database setup is required for this validation failure.
  - Failure endpoint:
    `POST /users/rbac/permissions`
  - Why this fails:
    The permission service validates the DTO and permission key before saving.
  - Intentionally violated constraints:
    Required permission key is omitted or empty.
- Branch 2:
  - Preconditions:
    - A permission with `{permissionKey}` already exists. This can be satisfied by directly inserting a `permissions` row with `permission = {permissionKey}`, by using a seeded permission, or by calling `POST /users/rbac/permissions` once with that key.
  - Failure endpoint:
    `POST /users/rbac/permissions`
  - Why this fails:
    The service finds an existing permission with the same key. Implementation detail: it throws `PermissionNotFoundException` for this duplicate case, so the global handler maps it like a not-found error even though the message says the permission already exists.
  - Intentionally violated constraints:
    The unique permission-key requirement is violated.

Endpoint coverage:
- Covers:
  `POST /users/rbac/permissions`
- Distinct meaning:
  Creates a standalone permission resource.

### Function 16: retrieve permission by key

Function name:
retrieve permission by key

Core endpoint(s):
- `GET /users/rbac/permissions/{permissionKey}`

Preconditions:
- A permission exists with key `{permissionKey}`. This can be satisfied by directly inserting a row into `permissions`, by using a seeded permission from `data.sql`, by calling `POST /users/rbac/permissions` with `permission = {permissionKey}`, or by calling `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` with a missing key so the implementation creates it implicitly.
- If the API establishes the permission, the created or supplied permission key must be reused as `{permissionKey}`.

Successful execution:
- Result:
  The core endpoint returns the selected permission by key.
- Invocation:
  Step 1: `GET /users/rbac/permissions/{permissionKey}` with `{permissionKey}` set to an existing permission key
- Constraints:
  `{permissionKey}` must be non-null, non-empty, and must identify a stored permission.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No permission exists with `{permissionKey}`. This can be produced by choosing a key that was not seeded, deleting the permission beforehand, or intentionally not inserting it directly, not calling `POST /users/rbac/permissions`, and not causing implicit creation through `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`.
  - Failure endpoint:
    `GET /users/rbac/permissions/{permissionKey}`
  - Why this fails:
    The permission repository cannot find a permission by key.
  - Intentionally violated constraints:
    The required permission state is omitted or the wrong permission key is used.

Endpoint coverage:
- Covers:
  `GET /users/rbac/permissions/{permissionKey}`
- Distinct meaning:
  Reads one specific permission resource by key.

### Function 17: update permission

Function name:
update permission

Core endpoint(s):
- `PUT /users/rbac/permissions`

Preconditions:
- A permission exists with `{permissionId}`. This can be satisfied by directly inserting a row into `permissions`, by using a seeded permission from `data.sql`, or by calling `POST /users/rbac/permissions` and using the returned permission `id`.
- If the API establishes the permission, the generated `id` from the creation response must be supplied in the update body as `id`.

Successful execution:
- Result:
  The core endpoint updates an existing permission's key, enabled flag, and note.
- Invocation:
  Step 1: `PUT /users/rbac/permissions` with a `PermissionDTO` JSON body containing `id = {permissionId}`, `permission`, `enabled`, and optional `note`
- Constraints:
  The body must not be null and `id` must identify an existing permission. If `permission` changes, the new key must not already belong to a different permission.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The update body is omitted; no database setup is required because the service rejects a null `PermissionDTO`.
  - Failure endpoint:
    `PUT /users/rbac/permissions`
  - Why this fails:
    The service rejects a null permission update body.
  - Intentionally violated constraints:
    Required update body is omitted.
- Branch 2:
  - Preconditions:
    - No permission exists with `{permissionId}`. This can be produced by choosing an id that was not seeded, deleting the permission beforehand, or intentionally not inserting it directly and not calling `POST /users/rbac/permissions`.
  - Failure endpoint:
    `PUT /users/rbac/permissions`
  - Why this fails:
    The service looks up the permission by body `id` before updating it.
  - Intentionally violated constraints:
    The required permission id state is omitted or the wrong generated id is used.
- Branch 3:
  - Preconditions:
    - A target permission exists with `{permissionId}`. This can be satisfied by direct database insertion or by calling `POST /users/rbac/permissions` and reusing the returned `id`.
    - A different permission already owns `{otherPermissionKey}`. This can be satisfied by direct database insertion or by calling `POST /users/rbac/permissions` for another key.
  - Failure endpoint:
    `PUT /users/rbac/permissions`
  - Why this fails:
    The service detects another permission with the requested key and rejects the update.
  - Intentionally violated constraints:
    The update body reuses another permission's unique key.

Endpoint coverage:
- Covers:
  `PUT /users/rbac/permissions`
- Distinct meaning:
  Updates an existing permission resource by body id.

### Function 18: delete unused permission

Function name:
delete unused permission

Core endpoint(s):
- `DELETE /users/rbac/permissions/{permissionKey}`

Preconditions:
- A permission exists with key `{permissionKey}`. This can be satisfied by directly inserting a row into `permissions`, by using a seeded permission from `data.sql`, by calling `POST /users/rbac/permissions`, or by implicit creation through `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`.
- No `permissions_roles` row references the permission id for `{permissionKey}`. This can be satisfied by direct database setup that leaves the association table empty for the permission or by creating a new permission and not assigning it to any role.
- If the API establishes the permission, the created or supplied permission key must be reused as `{permissionKey}`.

Successful execution:
- Result:
  The core endpoint deletes the selected permission when it is not associated with any role.
- Invocation:
  Step 1: `DELETE /users/rbac/permissions/{permissionKey}` with `{permissionKey}` set to an existing unused permission key
- Constraints:
  `{permissionKey}` must be non-empty, must identify an existing permission, and `permissionRepository.countPermissionUsage(permission.id)` must return zero.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No permission exists with `{permissionKey}`. This can be produced by choosing a key that was not seeded, deleting the permission beforehand, or intentionally not inserting it directly, not calling `POST /users/rbac/permissions`, and not causing implicit creation through role-permission assignment.
  - Failure endpoint:
    `DELETE /users/rbac/permissions/{permissionKey}`
  - Why this fails:
    The service cannot find the permission by key.
  - Intentionally violated constraints:
    The required permission state is omitted or the wrong permission key is used.
- Branch 2:
  - Preconditions:
    - A role exists with `{roleId}`. This can be satisfied by direct database insertion or by calling `POST /users/rbac/roles` and reusing the returned `id`.
    - A permission exists with `{permissionKey}`. This can be satisfied by direct database insertion or by calling `POST /users/rbac/permissions` and reusing the request's `permission` value.
    - The permission is associated with the role through a `permissions_roles` row. This can be satisfied by direct database insertion or by calling `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` after the role and permission exist.
  - Failure endpoint:
    `DELETE /users/rbac/permissions/{permissionKey}`
  - Why this fails:
    The service counts rows in `permissions_roles` and rejects deletion when the permission is in use.
  - Intentionally violated constraints:
    The permission is intentionally placed in use before deletion.

Endpoint coverage:
- Covers:
  `DELETE /users/rbac/permissions/{permissionKey}`
- Distinct meaning:
  Deletes a permission only if no role currently uses it.

### Function 19: add existing permission to role

Function name:
add existing permission to role

Core endpoint(s):
- `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`

Preconditions:
- A role exists with `{roleId}`. This can be satisfied by directly inserting a row into `roles`, by using a seeded role from `data.sql`, or by calling `POST /users/rbac/roles` with a non-empty unique role-name string and using the returned role `id`.
- A permission exists with key `{permissionKey}`. This can be satisfied by directly inserting a row into `permissions`, by using a seeded permission from `data.sql`, or by calling `POST /users/rbac/permissions` with `permission = {permissionKey}`.
- The role does not already contain the permission in `permissions_roles`. This can be satisfied by direct database setup that omits the association row or by creating the role and permission independently without calling the association endpoint.
- If the API establishes these resources, the generated role `id` and the permission key from the creation body must be reused in the path.

Successful execution:
- Result:
  The core endpoint associates the existing permission with the existing role and returns the updated role.
- Invocation:
  Step 1: `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` with `{roleId}` set to an existing role id and `{permissionKey}` set to an existing permission key
- Constraints:
  `{roleId}` must identify an existing role, `{permissionKey}` must be non-empty, and the role must not already contain that permission.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A permission exists with key `{permissionKey}`. This can be satisfied by direct database insertion, by using a seeded permission, or by calling `POST /users/rbac/permissions`.
    - No role exists with `{roleId}`. This can be produced by choosing an id that was not seeded, deleting the role beforehand, or intentionally not inserting it directly and not calling `POST /users/rbac/roles`.
  - Failure endpoint:
    `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`
  - Why this fails:
    The service validates role existence before adding or creating the permission association.
  - Intentionally violated constraints:
    The required role state is omitted or the wrong generated role id is used.
- Branch 2:
  - Preconditions:
    - A role exists with `{roleId}`. This can be satisfied by direct database insertion or by calling `POST /users/rbac/roles` and reusing the returned `id`.
    - A permission exists with key `{permissionKey}`. This can be satisfied by direct database insertion or by calling `POST /users/rbac/permissions` and reusing the permission key.
    - The role already contains that permission in `permissions_roles`. This can be satisfied by direct database insertion or by calling `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` once before the failing request.
  - Failure endpoint:
    `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`
  - Why this fails:
    The service detects that the role already contains the permission and rejects the duplicate association.
  - Intentionally violated constraints:
    The role-permission uniqueness requirement is violated by repeating an existing association.

Endpoint coverage:
- Covers:
  `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`
- Distinct meaning:
  Associates an already existing permission with a role.

### Function 20: create missing permission while adding it to role

Function name:
create missing permission while adding it to role

Core endpoint(s):
- `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`

Preconditions:
- A role exists with `{roleId}`. This can be satisfied by directly inserting a row into `roles`, by using a seeded role from `data.sql`, or by calling `POST /users/rbac/roles` with a non-empty unique role-name string and using the returned role `id`.
- No permission exists with key `{permissionKey}`. This can be satisfied by direct database setup that omits that key from `permissions`, by choosing an unused key, or by intentionally not calling `POST /users/rbac/permissions` for that key.
- If the API establishes the role, the generated role `id` must be reused in the path.

Successful execution:
- Result:
  The core endpoint creates a missing permission with `{permissionKey}` and associates that new permission with the existing role.
- Invocation:
  Step 1: `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` with `{roleId}` set to an existing role id and `{permissionKey}` set to a non-empty unused permission key
- Constraints:
  `{roleId}` must identify an existing role. `{permissionKey}` must be non-empty and unused. The implementation creates a new `Permission` with that key before adding it to the role.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No role exists with `{roleId}`. This can be produced by choosing an id that was not seeded, deleting the role beforehand, or intentionally not inserting it directly and not calling `POST /users/rbac/roles`.
    - No permission exists with key `{permissionKey}` because the request is attempting implicit creation, not association of an existing permission.
  - Failure endpoint:
    `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`
  - Why this fails:
    The service validates the role before creating or associating the permission.
  - Intentionally violated constraints:
    The required role state is omitted or the wrong generated role id is used.

Endpoint coverage:
- Covers:
  `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`
- Distinct meaning:
  Creates a missing permission as a side effect and then assigns it to a role.

### Function 21: remove permission from role

Function name:
remove permission from role

Core endpoint(s):
- `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}`

Preconditions:
- A role exists with `{roleId}`. This can be satisfied by directly inserting a row into `roles`, by using a seeded role from `data.sql`, or by calling `POST /users/rbac/roles` with a non-empty unique role-name string and using the returned role `id`.
- A permission exists with key `{permissionKey}`. This can be satisfied by directly inserting a row into `permissions`, by using a seeded permission from `data.sql`, or by calling `POST /users/rbac/permissions` with `permission = {permissionKey}`.
- The role-permission association exists in `permissions_roles` when the intended effect is visible removal. This can be satisfied by directly inserting the association row or by calling `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` after the role and permission exist.
- If the API establishes these resources, the generated role `id` and the permission key from the creation body must be reused in both the association setup request and the delete path.

Successful execution:
- Result:
  The core endpoint removes the permission association from the existing role and returns the updated role.
- Invocation:
  Step 1: `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}` with `{roleId}` set to an existing role id and `{permissionKey}` set to an existing permission key
- Constraints:
  `{roleId}` must identify an existing role and `{permissionKey}` must identify an existing permission. If the permission exists but is not assigned to the role, the implementation still succeeds and leaves the permission list unchanged.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A permission exists with key `{permissionKey}`. This can be satisfied by direct database insertion, by using a seeded permission, or by calling `POST /users/rbac/permissions`.
    - No role exists with `{roleId}`. This can be produced by choosing an id that was not seeded, deleting the role beforehand, or intentionally not inserting it directly and not calling `POST /users/rbac/roles`.
  - Failure endpoint:
    `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}`
  - Why this fails:
    The service validates role existence before removing the permission.
  - Intentionally violated constraints:
    The required role state is omitted or the wrong generated role id is used.
- Branch 2:
  - Preconditions:
    - A role exists with `{roleId}`. This can be satisfied by direct database insertion, by using a seeded role, or by calling `POST /users/rbac/roles`.
    - No permission exists with `{permissionKey}`. This can be produced by choosing a key that was not seeded, deleting the permission beforehand, or intentionally not inserting it directly and not calling `POST /users/rbac/permissions` or the implicit-creation association endpoint.
  - Failure endpoint:
    `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}`
  - Why this fails:
    Unlike the add endpoint, the remove endpoint does not create missing permissions.
  - Intentionally violated constraints:
    The required permission state is omitted or the wrong permission key is used.

Endpoint coverage:
- Covers:
  `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}`
- Distinct meaning:
  Removes or ensures absence of a permission association on a role.

### Function 22: generate password salt

Function name:
generate password salt

Core endpoint(s):
- `GET /users/rbac/salt`

Preconditions:
- No prerequisite resource state is required.

Successful execution:
- Result:
  The core endpoint returns a newly generated 32-character alphanumeric salt string.
- Invocation:
  Step 1: `GET /users/rbac/salt`
- Constraints:
  No path values, query values, or request body are required. The implementation returns `201 Created`, while Swagger documents `200 OK`.

Failure or exceptional branches:
- None identified from the OpenAPI definition or implementation logic.

Endpoint coverage:
- Covers:
  `GET /users/rbac/salt`
- Distinct meaning:
  Generates an auxiliary salt value for password-hashing configuration or setup.
