Source/OpenAPI discrepancies observed: the implementation returns `201 Created` for several create/update/association endpoints where the Swagger file also lists `200 OK`; the salt endpoint returns `201 Created` in code while Swagger documents `200 OK`. Swagger does not mark request-body fields as required, but the implementation validates several fields and database schema requires others.

### Behavior 1: authenticate user

Behavior name:
authenticate user

Successful execution:
- Result:
  This behavior logs in an enabled user and returns that user’s presentation data, including roles and enabled permissions. The login timestamp is updated.
- Endpoint sequence:
  Step 1: `POST /users/register` with a unique `username`, valid `password`, valid `email`, `gender` equal to `MALE` or `FEMALE`, and non-null user identity fields.
  Step 2: `POST /login` with `username` and plaintext `password` matching the values from Step 1.
- Constraints:
  The user created by Step 1 must be enabled. Registration sets `enabled=true`. The login password is compared against the stored encrypted password using the configured application salt. The `username` from Step 1 must be reused in Step 2.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Username or password is null or empty.
  - Endpoint group:
    Step 1: `POST /login` with missing or empty `username` or `password`.
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    The login service rejects empty credentials before looking up a user.
  - Intentionally violated constraints:
    Required login credentials are omitted or empty.
- Branch 2:
  - Unsatisfied condition:
    No user exists for the supplied username.
  - Endpoint group:
    Step 1: `POST /login` with a `username` that was not created by `POST /users/register` or `POST /users`.
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    The implementation cannot find a user by username and raises an invalid-login error.
  - Intentionally violated constraints:
    The prerequisite user creation endpoint is intentionally omitted.
- Branch 3:
  - Unsatisfied condition:
    Password does not match the stored encrypted password.
  - Endpoint group:
    Step 1: `POST /users/register`
    Step 2: `POST /login` with the same `username` but a different valid plaintext `password`.
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    The password hash comparison fails.
  - Intentionally violated constraints:
    Step 2 does not reuse the plaintext password supplied in Step 1.
- Branch 4:
  - Unsatisfied condition:
    User exists but is disabled.
  - Endpoint group:
    Step 1: `POST /users/register`
    Step 2: `PUT /users/{id}` using the `id` returned by Step 1 and a valid full user body with `enabled=false`.
    Step 3: `POST /login` with the updated user’s `username` and `password`.
  - Failure endpoint:
    `POST /login`
  - Why this fails:
    The implementation rejects login for users whose `enabled` flag is false.
  - Intentionally violated constraints:
    The user is intentionally put into a disabled state before login.

Endpoint coverage:
- Covers:
  `POST /login`
- Distinct meaning:
  Authenticates an existing enabled user and updates login metadata.

### Behavior 2: list users

Behavior name:
list users

Successful execution:
- Result:
  This behavior retrieves the presentation list of all users currently stored by the service.
- Endpoint sequence:
  Step 1: `GET /users`
- Constraints:
  No specific user must already exist. The result may be an empty or populated `UserListDTO`.

Failure or exceptional branches:
- None identified from the OpenAPI definition or implementation logic.

Endpoint coverage:
- Covers:
  `GET /users`
- Distinct meaning:
  Reads the global user collection.

### Behavior 3: create full user

Behavior name:
create full user

Successful execution:
- Result:
  This behavior creates a full user account with credentials, identity data, contact data, address data, enabled status, secured flag, and the default `USER` role.
- Endpoint sequence:
  Step 1: `POST /users`
- Constraints:
  The body must contain a unique `username`, unique valid `email`, valid `password`, valid non-empty `phone`, and `gender` equal to `MALE` or `FEMALE`. `name` and `surname` must satisfy the non-null database columns. The implementation always sets `enabled=true` during creation, regardless of the request body’s `enabled` value. The request body’s `secured` value is stored. The seeded role with id `1` (`Role.USER`) must already exist; `src/main/resources/data.sql` creates it.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The request body is missing or required validated fields are invalid.
  - Endpoint group:
    Step 1: `POST /users` with a null body, invalid password, invalid email, missing or invalid phone, or unsupported `gender`.
  - Failure endpoint:
    `POST /users`
  - Why this fails:
    The service validates the DTO, password, email, phone, and gender before saving.
  - Intentionally violated constraints:
    Required body values or format rules are intentionally violated.
- Branch 2:
  - Unsatisfied condition:
    Username is already used.
  - Endpoint group:
    Step 1: `POST /users`
    Step 2: `POST /users` with the same `username` as Step 1.
  - Failure endpoint:
    `POST /users`
  - Why this fails:
    The service looks up the username and rejects duplicates.
  - Intentionally violated constraints:
    Step 2 reuses the unique `username` from Step 1.
- Branch 3:
  - Unsatisfied condition:
    Email is already used.
  - Endpoint group:
    Step 1: `POST /users`
    Step 2: `POST /users` with a different `username` but the same `email` as Step 1.
  - Failure endpoint:
    `POST /users`
  - Why this fails:
    The service looks up contact email and rejects duplicates.
  - Intentionally violated constraints:
    Step 2 reuses the unique `email` from Step 1.

Endpoint coverage:
- Covers:
  `POST /users`
- Distinct meaning:
  Creates a complete user record with contact, address, credentials, default role, and secured flag.

### Behavior 4: register minimal user account

Behavior name:
register minimal user account

Successful execution:
- Result:
  This behavior creates a minimal user account with username, encrypted password, identity fields, email contact, an empty address, `enabled=true`, `secured=false`, and the default `USER` role.
- Endpoint sequence:
  Step 1: `POST /users/register`
- Constraints:
  The body must contain a unique `username`, unique valid `email`, valid `password`, and `gender` equal to `MALE` or `FEMALE`. `name` and `surname` must satisfy the non-null database columns. The seeded role with id `1` (`Role.USER`) must already exist; `src/main/resources/data.sql` creates it.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The request body is missing or required validated fields are invalid.
  - Endpoint group:
    Step 1: `POST /users/register` with a null body, invalid password, invalid email, or unsupported `gender`.
  - Failure endpoint:
    `POST /users/register`
  - Why this fails:
    The service validates registration data before creating the account.
  - Intentionally violated constraints:
    Required registration fields or format rules are intentionally violated.
- Branch 2:
  - Unsatisfied condition:
    Username is already used.
  - Endpoint group:
    Step 1: `POST /users/register`
    Step 2: `POST /users/register` with the same `username` as Step 1.
  - Failure endpoint:
    `POST /users/register`
  - Why this fails:
    The service rejects duplicate usernames.
  - Intentionally violated constraints:
    Step 2 reuses the unique `username` from Step 1.
- Branch 3:
  - Unsatisfied condition:
    Email is already used.
  - Endpoint group:
    Step 1: `POST /users/register`
    Step 2: `POST /users/register` with a different `username` but the same `email` as Step 1.
  - Failure endpoint:
    `POST /users/register`
  - Why this fails:
    The service rejects duplicate contact emails.
  - Intentionally violated constraints:
    Step 2 reuses the unique `email` from Step 1.

Endpoint coverage:
- Covers:
  `POST /users/register`
- Distinct meaning:
  Creates a reduced user account for self-registration.

### Behavior 5: retrieve user by id

Behavior name:
retrieve user by id

Successful execution:
- Result:
  This behavior retrieves the user presentation data for a specific user id.
- Endpoint sequence:
  Step 1: `POST /users/register`
  Step 2: `GET /users/{id}` using the `id` returned by Step 1.
- Constraints:
  `{id}` in Step 2 must be the user id produced by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No user exists for `{id}`.
  - Endpoint group:
    Step 1: `GET /users/{id}` with an id not returned by any prior documented user creation endpoint.
  - Failure endpoint:
    `GET /users/{id}`
  - Why this fails:
    The user repository lookup by id returns empty.
  - Intentionally violated constraints:
    The prerequisite `POST /users/register` or `POST /users` is intentionally omitted.

Endpoint coverage:
- Covers:
  `GET /users/{id}`
- Distinct meaning:
  Reads one specific user resource.

### Behavior 6: update user

Behavior name:
update user

Successful execution:
- Result:
  This behavior updates an existing user’s username, password, identity fields, gender, birth date, enabled flag, note, contact fields, address fields, and update timestamp.
- Endpoint sequence:
  Step 1: `POST /users/register`
  Step 2: `PUT /users/{id}` using the `id` returned by Step 1 and a valid full `CreateOrUpdateUserDTO` body.
- Constraints:
  `{id}` in Step 2 must come from Step 1. The update body must contain a valid password, valid email, valid non-empty phone, and `gender` equal to `MALE` or `FEMALE`. The updated `username` and `email` may match the same user’s existing values but must not belong to another user. The `secured` field is not updated by this endpoint.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Target user does not exist.
  - Endpoint group:
    Step 1: `PUT /users/{id}` with an id not returned by any prior documented user creation endpoint.
  - Failure endpoint:
    `PUT /users/{id}`
  - Why this fails:
    The service looks up the user id before applying updates.
  - Intentionally violated constraints:
    The prerequisite user creation endpoint is intentionally omitted.
- Branch 2:
  - Unsatisfied condition:
    Update body is missing or contains invalid validated fields.
  - Endpoint group:
    Step 1: `POST /users/register`
    Step 2: `PUT /users/{id}` using the id from Step 1 but a null body, invalid password, invalid email, missing or invalid phone, or unsupported `gender`.
  - Failure endpoint:
    `PUT /users/{id}`
  - Why this fails:
    The service validates the update DTO before saving.
  - Intentionally violated constraints:
    Step 2 provides invalid update data.
- Branch 3:
  - Unsatisfied condition:
    Updated username belongs to another user.
  - Endpoint group:
    Step 1: `POST /users/register`
    Step 2: `POST /users/register`
    Step 3: `PUT /users/{id}` using the id from Step 1 but the `username` from Step 2.
  - Failure endpoint:
    `PUT /users/{id}`
  - Why this fails:
    The service permits the current user’s own username but rejects another user’s username.
  - Intentionally violated constraints:
    Step 3 reuses another user’s unique `username`.
- Branch 4:
  - Unsatisfied condition:
    Updated email belongs to another user.
  - Endpoint group:
    Step 1: `POST /users/register`
    Step 2: `POST /users/register`
    Step 3: `PUT /users/{id}` using the id from Step 1 but the `email` from Step 2.
  - Failure endpoint:
    `PUT /users/{id}`
  - Why this fails:
    The service permits the current user’s own email but rejects another user’s email.
  - Intentionally violated constraints:
    Step 3 reuses another user’s unique `email`.

Endpoint coverage:
- Covers:
  `PUT /users/{id}`
- Distinct meaning:
  Replaces editable user profile, contact, address, credential, and enabled-state fields.

### Behavior 7: delete non-secured user

Behavior name:
delete non-secured user

Successful execution:
- Result:
  This behavior deletes an existing user whose `secured` flag is false.
- Endpoint sequence:
  Step 1: `POST /users/register`
  Step 2: `DELETE /users/{id}` using the `id` returned by Step 1.
- Constraints:
  `{id}` in Step 2 must identify the user from Step 1. Registration creates `secured=false`, which satisfies the delete precondition.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Target user does not exist.
  - Endpoint group:
    Step 1: `DELETE /users/{id}` with an id not returned by any prior documented user creation endpoint.
  - Failure endpoint:
    `DELETE /users/{id}`
  - Why this fails:
    The service cannot find the user by id.
  - Intentionally violated constraints:
    The prerequisite user creation endpoint is intentionally omitted.
- Branch 2:
  - Unsatisfied condition:
    Target user is secured.
  - Endpoint group:
    Step 1: `POST /users` with a valid full user body and `secured=true`.
    Step 2: `DELETE /users/{id}` using the `id` returned by Step 1.
  - Failure endpoint:
    `DELETE /users/{id}`
  - Why this fails:
    The service explicitly refuses to delete secured users.
  - Intentionally violated constraints:
    Step 1 intentionally creates a secured user.

Endpoint coverage:
- Covers:
  `DELETE /users/{id}`
- Distinct meaning:
  Removes a user only when the user is not secured.

### Behavior 8: assign role to user

Behavior name:
assign role to user

Successful execution:
- Result:
  This behavior associates an existing role with an existing user and returns the updated user presentation.
- Endpoint sequence:
  Step 1: `POST /users/register`
  Step 2: `POST /users/rbac/roles`
  Step 3: `POST /users/{id}/roles/{roleId}` using the user `id` returned by Step 1 and the `roleId` returned by Step 2.
- Constraints:
  `{id}` must identify an existing user. `{roleId}` must identify an existing role. If the role is already assigned, the set prevents a duplicate and the call still succeeds.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    User does not exist.
  - Endpoint group:
    Step 1: `POST /users/rbac/roles`
    Step 2: `POST /users/{id}/roles/{roleId}` using the role id from Step 1 but a user id not returned by any user creation endpoint.
  - Failure endpoint:
    `POST /users/{id}/roles/{roleId}`
  - Why this fails:
    The service validates the user before validating or saving the role association.
  - Intentionally violated constraints:
    The prerequisite user creation endpoint is intentionally omitted.
- Branch 2:
  - Unsatisfied condition:
    Role does not exist.
  - Endpoint group:
    Step 1: `POST /users/register`
    Step 2: `POST /users/{id}/roles/{roleId}` using the user id from Step 1 but a role id not returned by `POST /users/rbac/roles` and not seeded.
  - Failure endpoint:
    `POST /users/{id}/roles/{roleId}`
  - Why this fails:
    The service cannot find the role by id.
  - Intentionally violated constraints:
    The prerequisite role creation endpoint is intentionally omitted.

Endpoint coverage:
- Covers:
  `POST /users/{id}/roles/{roleId}`
- Distinct meaning:
  Adds or ensures a role association on a user.

### Behavior 9: remove role from user

Behavior name:
remove role from user

Successful execution:
- Result:
  This behavior removes an existing role association from an existing user and returns the updated user presentation.
- Endpoint sequence:
  Step 1: `POST /users/register`
  Step 2: `POST /users/rbac/roles`
  Step 3: `POST /users/{id}/roles/{roleId}` using the user `id` from Step 1 and the `roleId` from Step 2.
  Step 4: `DELETE /users/{id}/roles/{roleId}` using the same `id` and `roleId`.
- Constraints:
  The user and role must exist. Step 3 is required when the intended visible action is removal of an assigned role. If the role exists but is not assigned, the implementation still succeeds and leaves the user’s role set unchanged.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    User does not exist.
  - Endpoint group:
    Step 1: `POST /users/rbac/roles`
    Step 2: `DELETE /users/{id}/roles/{roleId}` using the role id from Step 1 but a user id not returned by any user creation endpoint.
  - Failure endpoint:
    `DELETE /users/{id}/roles/{roleId}`
  - Why this fails:
    The service validates the user before removing a role.
  - Intentionally violated constraints:
    The prerequisite user creation endpoint is intentionally omitted.
- Branch 2:
  - Unsatisfied condition:
    Role does not exist.
  - Endpoint group:
    Step 1: `POST /users/register`
    Step 2: `DELETE /users/{id}/roles/{roleId}` using the user id from Step 1 but a role id not returned by `POST /users/rbac/roles` and not seeded.
  - Failure endpoint:
    `DELETE /users/{id}/roles/{roleId}`
  - Why this fails:
    The service cannot find the role by id.
  - Intentionally violated constraints:
    The prerequisite role creation endpoint is intentionally omitted.

Endpoint coverage:
- Covers:
  `DELETE /users/{id}/roles/{roleId}`
- Distinct meaning:
  Removes or ensures absence of a role association on a user.

### Behavior 10: list roles

Behavior name:
list roles

Successful execution:
- Result:
  This behavior retrieves all roles with their associated permissions.
- Endpoint sequence:
  Step 1: `GET /users/rbac/roles`
- Constraints:
  No specific role must already exist. The result may be empty or populated.

Failure or exceptional branches:
- None identified from the OpenAPI definition or implementation logic.

Endpoint coverage:
- Covers:
  `GET /users/rbac/roles`
- Distinct meaning:
  Reads the global role collection.

### Behavior 11: create role

Behavior name:
create role

Successful execution:
- Result:
  This behavior creates a new role with a unique role name and no permissions.
- Endpoint sequence:
  Step 1: `POST /users/rbac/roles` with a non-empty string body containing the role name.
- Constraints:
  The role name must be non-null, non-empty, and not already used.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Role name is null or empty.
  - Endpoint group:
    Step 1: `POST /users/rbac/roles` with an empty or missing string body.
  - Failure endpoint:
    `POST /users/rbac/roles`
  - Why this fails:
    The role service validates the role name before saving.
  - Intentionally violated constraints:
    Required role name is omitted or empty.
- Branch 2:
  - Unsatisfied condition:
    Role name already exists.
  - Endpoint group:
    Step 1: `POST /users/rbac/roles`
    Step 2: `POST /users/rbac/roles` with the same role name as Step 1.
  - Failure endpoint:
    `POST /users/rbac/roles`
  - Why this fails:
    The role repository finds an existing role with that name.
  - Intentionally violated constraints:
    Step 2 reuses the unique role name from Step 1.

Endpoint coverage:
- Covers:
  `POST /users/rbac/roles`
- Distinct meaning:
  Creates a new RBAC role.

### Behavior 12: retrieve role by id

Behavior name:
retrieve role by id

Successful execution:
- Result:
  This behavior retrieves a specific role and its permissions by role id.
- Endpoint sequence:
  Step 1: `POST /users/rbac/roles`
  Step 2: `GET /users/rbac/roles/{roleId}` using the `id` returned by Step 1.
- Constraints:
  `{roleId}` in Step 2 must be the role id produced by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Role does not exist.
  - Endpoint group:
    Step 1: `GET /users/rbac/roles/{roleId}` with an id not returned by `POST /users/rbac/roles` and not seeded.
  - Failure endpoint:
    `GET /users/rbac/roles/{roleId}`
  - Why this fails:
    The service cannot find the role by id.
  - Intentionally violated constraints:
    The prerequisite role creation endpoint is intentionally omitted.

Endpoint coverage:
- Covers:
  `GET /users/rbac/roles/{roleId}`
- Distinct meaning:
  Reads one specific role resource.

### Behavior 13: delete unused role

Behavior name:
delete unused role

Successful execution:
- Result:
  This behavior deletes an existing role that is not assigned to any user.
- Endpoint sequence:
  Step 1: `POST /users/rbac/roles`
  Step 2: `DELETE /users/rbac/roles/{roleId}` using the `id` returned by Step 1.
- Constraints:
  `{roleId}` must identify an existing role. The role must have zero rows in the `users_roles` association table.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Role does not exist.
  - Endpoint group:
    Step 1: `DELETE /users/rbac/roles/{roleId}` with an id not returned by `POST /users/rbac/roles` and not seeded.
  - Failure endpoint:
    `DELETE /users/rbac/roles/{roleId}`
  - Why this fails:
    The service cannot find the role before deletion.
  - Intentionally violated constraints:
    The prerequisite role creation endpoint is intentionally omitted.
- Branch 2:
  - Unsatisfied condition:
    Role is assigned to at least one user.
  - Endpoint group:
    Step 1: `POST /users/register`
    Step 2: `POST /users/rbac/roles`
    Step 3: `POST /users/{id}/roles/{roleId}` using the user id from Step 1 and role id from Step 2.
    Step 4: `DELETE /users/rbac/roles/{roleId}` using the same role id from Step 2.
  - Failure endpoint:
    `DELETE /users/rbac/roles/{roleId}`
  - Why this fails:
    The service counts role usage in `users_roles` and rejects deletion when usage is greater than zero.
  - Intentionally violated constraints:
    Step 3 intentionally makes the role in use before Step 4.

Endpoint coverage:
- Covers:
  `DELETE /users/rbac/roles/{roleId}`
- Distinct meaning:
  Deletes a role only if no user currently uses it.

### Behavior 14: list permissions

Behavior name:
list permissions

Successful execution:
- Result:
  This behavior retrieves all permissions.
- Endpoint sequence:
  Step 1: `GET /users/rbac/permissions`
- Constraints:
  No specific permission must already exist. The result may be empty or populated.

Failure or exceptional branches:
- None identified from the OpenAPI definition or implementation logic.

Endpoint coverage:
- Covers:
  `GET /users/rbac/permissions`
- Distinct meaning:
  Reads the global permission collection.

### Behavior 15: create permission

Behavior name:
create permission

Successful execution:
- Result:
  This behavior creates a new permission with a unique permission key, enabled flag, and optional note.
- Endpoint sequence:
  Step 1: `POST /users/rbac/permissions`
- Constraints:
  The body must be a `PermissionDTO` with a non-empty unique `permission` value. The implementation stores `permission`, `enabled`, and `note`; the request `id` is not used for creation.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Permission body is missing or `permission` is null or empty.
  - Endpoint group:
    Step 1: `POST /users/rbac/permissions` with a null body or empty `permission`.
  - Failure endpoint:
    `POST /users/rbac/permissions`
  - Why this fails:
    The permission service validates the DTO and key before saving.
  - Intentionally violated constraints:
    Required permission key is omitted or empty.
- Branch 2:
  - Unsatisfied condition:
    Permission key already exists.
  - Endpoint group:
    Step 1: `POST /users/rbac/permissions`
    Step 2: `POST /users/rbac/permissions` with the same `permission` key as Step 1.
  - Failure endpoint:
    `POST /users/rbac/permissions`
  - Why this fails:
    The service finds an existing permission with the same key. Implementation detail: it throws `PermissionNotFoundException` for this duplicate case, so the global handler maps it like a not-found error even though the message says the permission already exists.
  - Intentionally violated constraints:
    Step 2 reuses the unique permission key from Step 1.

Endpoint coverage:
- Covers:
  `POST /users/rbac/permissions`
- Distinct meaning:
  Creates a standalone permission resource.

### Behavior 16: retrieve permission by key

Behavior name:
retrieve permission by key

Successful execution:
- Result:
  This behavior retrieves a specific permission by its permission key.
- Endpoint sequence:
  Step 1: `POST /users/rbac/permissions`
  Step 2: `GET /users/rbac/permissions/{permissionKey}` using the `permission` value from Step 1 as `{permissionKey}`.
- Constraints:
  `{permissionKey}` must equal the permission key created in Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Permission does not exist.
  - Endpoint group:
    Step 1: `GET /users/rbac/permissions/{permissionKey}` with a key not created by `POST /users/rbac/permissions` and not implicitly created by role-permission assignment.
  - Failure endpoint:
    `GET /users/rbac/permissions/{permissionKey}`
  - Why this fails:
    The permission repository cannot find a permission by key.
  - Intentionally violated constraints:
    The prerequisite permission creation endpoint is intentionally omitted.

Endpoint coverage:
- Covers:
  `GET /users/rbac/permissions/{permissionKey}`
- Distinct meaning:
  Reads one specific permission resource by key.

### Behavior 17: update permission

Behavior name:
update permission

Successful execution:
- Result:
  This behavior updates an existing permission’s key, enabled flag, and note.
- Endpoint sequence:
  Step 1: `POST /users/rbac/permissions`
  Step 2: `PUT /users/rbac/permissions` with body `id` equal to the `id` returned by Step 1 and `permission` either unchanged or changed to another unused key.
- Constraints:
  The update body must include an `id` for an existing permission. If `permission` changes, the new key must not already belong to a different permission.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Permission body is missing.
  - Endpoint group:
    Step 1: `PUT /users/rbac/permissions` with no body.
  - Failure endpoint:
    `PUT /users/rbac/permissions`
  - Why this fails:
    The service rejects a null `PermissionDTO`.
  - Intentionally violated constraints:
    Required update body is omitted.
- Branch 2:
  - Unsatisfied condition:
    Permission id does not identify an existing permission.
  - Endpoint group:
    Step 1: `PUT /users/rbac/permissions` with an `id` not returned by `POST /users/rbac/permissions`.
  - Failure endpoint:
    `PUT /users/rbac/permissions`
  - Why this fails:
    The service looks up the permission by id before updating it.
  - Intentionally violated constraints:
    The prerequisite permission creation endpoint is intentionally omitted.
- Branch 3:
  - Unsatisfied condition:
    New permission key belongs to a different permission.
  - Endpoint group:
    Step 1: `POST /users/rbac/permissions`
    Step 2: `POST /users/rbac/permissions`
    Step 3: `PUT /users/rbac/permissions` with `id` from Step 1 and `permission` key from Step 2.
  - Failure endpoint:
    `PUT /users/rbac/permissions`
  - Why this fails:
    The service detects another permission with the requested key and rejects the update.
  - Intentionally violated constraints:
    Step 3 reuses another permission’s unique key.

Endpoint coverage:
- Covers:
  `PUT /users/rbac/permissions`
- Distinct meaning:
  Updates an existing permission resource by body id.

### Behavior 18: delete unused permission

Behavior name:
delete unused permission

Successful execution:
- Result:
  This behavior deletes an existing permission when it is not associated with any role.
- Endpoint sequence:
  Step 1: `POST /users/rbac/permissions`
  Step 2: `DELETE /users/rbac/permissions/{permissionKey}` using the `permission` value from Step 1 as `{permissionKey}`.
- Constraints:
  `{permissionKey}` must identify an existing permission. The permission must have zero rows in the `permissions_roles` association table.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Permission does not exist.
  - Endpoint group:
    Step 1: `DELETE /users/rbac/permissions/{permissionKey}` with a key not created by `POST /users/rbac/permissions` and not implicitly created by role-permission assignment.
  - Failure endpoint:
    `DELETE /users/rbac/permissions/{permissionKey}`
  - Why this fails:
    The service cannot find the permission by key.
  - Intentionally violated constraints:
    The prerequisite permission creation endpoint is intentionally omitted.
- Branch 2:
  - Unsatisfied condition:
    Permission is associated with at least one role.
  - Endpoint group:
    Step 1: `POST /users/rbac/roles`
    Step 2: `POST /users/rbac/permissions`
    Step 3: `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` using the role id from Step 1 and permission key from Step 2.
    Step 4: `DELETE /users/rbac/permissions/{permissionKey}` using the same permission key from Step 2.
  - Failure endpoint:
    `DELETE /users/rbac/permissions/{permissionKey}`
  - Why this fails:
    The service counts permission usage in `permissions_roles` and rejects deletion when usage is greater than zero.
  - Intentionally violated constraints:
    Step 3 intentionally makes the permission in use before Step 4.

Endpoint coverage:
- Covers:
  `DELETE /users/rbac/permissions/{permissionKey}`
- Distinct meaning:
  Deletes a permission only if no role currently uses it.

### Behavior 19: add existing permission to role

Behavior name:
add existing permission to role

Successful execution:
- Result:
  This behavior associates an already existing permission with an existing role and returns the updated role.
- Endpoint sequence:
  Step 1: `POST /users/rbac/roles`
  Step 2: `POST /users/rbac/permissions`
  Step 3: `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` using the role id from Step 1 and permission key from Step 2.
- Constraints:
  `{roleId}` must identify an existing role. `{permissionKey}` must identify an existing permission. The permission must not already be associated with the role.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Role does not exist.
  - Endpoint group:
    Step 1: `POST /users/rbac/permissions`
    Step 2: `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` using the permission key from Step 1 but a role id not returned by `POST /users/rbac/roles` and not seeded.
  - Failure endpoint:
    `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`
  - Why this fails:
    The service validates role existence before adding the permission.
  - Intentionally violated constraints:
    The prerequisite role creation endpoint is intentionally omitted.
- Branch 2:
  - Unsatisfied condition:
    Permission is already associated with the role.
  - Endpoint group:
    Step 1: `POST /users/rbac/roles`
    Step 2: `POST /users/rbac/permissions`
    Step 3: `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` using the role id from Step 1 and permission key from Step 2.
    Step 4: `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` repeating the same role id and permission key.
  - Failure endpoint:
    `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`
  - Why this fails:
    The service detects that the role already contains the permission and rejects the duplicate association.
  - Intentionally violated constraints:
    Step 4 repeats the association created in Step 3.

Endpoint coverage:
- Covers:
  `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`
- Distinct meaning:
  Associates an existing permission with a role.

### Behavior 20: create missing permission while adding it to role

Behavior name:
create missing permission while adding it to role

Successful execution:
- Result:
  This behavior creates a permission implicitly when `{permissionKey}` does not exist, then associates that new permission with the existing role.
- Endpoint sequence:
  Step 1: `POST /users/rbac/roles`
  Step 2: `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` using the role id from Step 1 and a permission key that has not been created by `POST /users/rbac/permissions`.
- Constraints:
  `{roleId}` must identify an existing role. `{permissionKey}` must be non-empty and unused. The implementation creates a new `Permission` with that key and then adds it to the role. This is a distinct implementation behavior of the same endpoint used to add existing permissions.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Role does not exist.
  - Endpoint group:
    Step 1: `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` with a non-empty new permission key but a role id not returned by `POST /users/rbac/roles` and not seeded.
  - Failure endpoint:
    `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`
  - Why this fails:
    The service validates the role before creating or associating the permission.
  - Intentionally violated constraints:
    The prerequisite role creation endpoint is intentionally omitted.

Endpoint coverage:
- Covers:
  `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`
- Distinct meaning:
  Creates a missing permission as a side effect, then assigns it to a role.

### Behavior 21: remove permission from role

Behavior name:
remove permission from role

Successful execution:
- Result:
  This behavior removes an existing permission from an existing role and returns the updated role.
- Endpoint sequence:
  Step 1: `POST /users/rbac/roles`
  Step 2: `POST /users/rbac/permissions`
  Step 3: `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` using the role id from Step 1 and permission key from Step 2.
  Step 4: `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}` using the same role id and permission key.
- Constraints:
  The role and permission must exist. Step 3 is required when the intended visible action is removal of an assigned permission. If the permission exists but is not assigned to the role, the implementation still succeeds and leaves the role permissions unchanged.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Role does not exist.
  - Endpoint group:
    Step 1: `POST /users/rbac/permissions`
    Step 2: `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}` using the permission key from Step 1 but a role id not returned by `POST /users/rbac/roles` and not seeded.
  - Failure endpoint:
    `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}`
  - Why this fails:
    The service validates role existence before removing the permission.
  - Intentionally violated constraints:
    The prerequisite role creation endpoint is intentionally omitted.
- Branch 2:
  - Unsatisfied condition:
    Permission does not exist.
  - Endpoint group:
    Step 1: `POST /users/rbac/roles`
    Step 2: `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}` using the role id from Step 1 but a permission key not created by `POST /users/rbac/permissions` and not implicitly created by role-permission assignment.
  - Failure endpoint:
    `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}`
  - Why this fails:
    Unlike the add endpoint, the remove endpoint does not create missing permissions.
  - Intentionally violated constraints:
    The prerequisite permission creation endpoint is intentionally omitted.

Endpoint coverage:
- Covers:
  `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}`
- Distinct meaning:
  Removes or ensures absence of a permission association on a role.

### Behavior 22: generate password salt

Behavior name:
generate password salt

Successful execution:
- Result:
  This behavior returns a newly generated 32-character salt string using alphanumeric characters.
- Endpoint sequence:
  Step 1: `GET /users/rbac/salt`
- Constraints:
  No prerequisite resource state is required. Implementation returns `201 Created`, while Swagger documents `200 OK`.

Failure or exceptional branches:
- None identified from the OpenAPI definition or implementation logic.

Endpoint coverage:
- Covers:
  `GET /users/rbac/salt`
- Distinct meaning:
  Generates an auxiliary salt value for password-hashing configuration or setup.