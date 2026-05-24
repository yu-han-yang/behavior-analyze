# Domain-Level Behavior Analysis

## Domain Summary

The `user-management` service is described by its OpenAPI contract as: Api Documentation

The core business concepts are:

- login-rest-controller: endpoint group for login rest controller behavior.
- user-rest-controller: endpoint group for user rest controller behavior.
- rbac-rest-controller: endpoint group for rbac rest controller behavior.
- register-rest-controller: endpoint group for register rest controller behavior.

Contract-level limits: this document captures externally visible business behavior from the API contract. Implementation-only validation, persistence side effects, authorization rules, and asynchronous processing details may be stricter than what is visible here.

## Available Function Inventory

### login-rest-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `login` | `POST /login` | login. |

### rbac-rest-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `get permission presentation list` | `GET /users/rbac/permissions` | getPermissionPresentationList. |
| `create permission` | `POST /users/rbac/permissions` | createPermission. |
| `update permission` | `PUT /users/rbac/permissions` | updatePermission. |
| `get permission by key` | `GET /users/rbac/permissions/{permissionKey}` | getPermissionByKey. |
| `delete permission by key` | `DELETE /users/rbac/permissions/{permissionKey}` | deletePermissionByKey. |
| `get role presentation list` | `GET /users/rbac/roles` | getRolePresentationList. |
| `create role` | `POST /users/rbac/roles` | createRole. |
| `get role by id` | `GET /users/rbac/roles/{roleId}` | getRoleById. |
| `delete role by id` | `DELETE /users/rbac/roles/{roleId}` | deleteRoleById. |
| `add permission on role` | `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}` | addPermissionOnRole. |
| `remove permission on role` | `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}` | removePermissionOnRole. |
| `generate salt` | `GET /users/rbac/salt` | generateSalt. |

### register-rest-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `register new user account` | `POST /users/register` | registerNewUserAccount. |

### user-rest-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `get user presentation list` | `GET /users` | getUserPresentationList. |
| `create user` | `POST /users` | createUser. |
| `get user by id` | `GET /users/{id}` | getUserById. |
| `update user` | `PUT /users/{id}` | updateUser. |
| `delete by id` | `DELETE /users/{id}` | deleteById. |
| `add role` | `POST /users/{id}/roles/{roleId}` | addRole. |
| `remove role` | `DELETE /users/{id}/roles/{roleId}` | removeRole. |

## Supported Business Behaviors

### Behavior 1: Login

Business goal:
login.

Domain context:
This behavior belongs to the `login-rest-controller` capability area and operates through `POST /login`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `login` (`POST /login`) with body: loginRequestDTO required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `loginRequestDTO`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `login`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `login`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `login`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 2: Get User Presentation List

Business goal:
getUserPresentationList.

Domain context:
This behavior belongs to the `user-rest-controller` capability area and operates through `GET /users`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `get user presentation list` (`GET /users`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `get user presentation list`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get user presentation list`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get user presentation list`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 3: Create User

Business goal:
createUser.

Domain context:
This behavior belongs to the `user-rest-controller` capability area and operates through `POST /users`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `create user` (`POST /users`) with body: createOrUpdateUserDTO required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `createOrUpdateUserDTO`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `create user`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create user`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create user`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 4: Get Permission Presentation List

Business goal:
getPermissionPresentationList.

Domain context:
This behavior belongs to the `rbac-rest-controller` capability area and operates through `GET /users/rbac/permissions`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `get permission presentation list` (`GET /users/rbac/permissions`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `get permission presentation list`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get permission presentation list`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get permission presentation list`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 5: Create Permission

Business goal:
createPermission.

Domain context:
This behavior belongs to the `rbac-rest-controller` capability area and operates through `POST /users/rbac/permissions`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `create permission` (`POST /users/rbac/permissions`) with body: permissionDTO required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `permissionDTO`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `create permission`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create permission`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create permission`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 6: Update Permission

Business goal:
updatePermission.

Domain context:
This behavior belongs to the `rbac-rest-controller` capability area and operates through `PUT /users/rbac/permissions`.

Starting point:
The target resource identified by the path should exist unless the implementation treats the request as an upsert.

Required execution workflow:
1. Use function `update permission` (`PUT /users/rbac/permissions`) with body: permissionDTO required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the update operation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The addressed resource is replaced with the submitted representation or command result.

Constraints and invariants:
- Required request values: `permissionDTO`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `update permission`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `update permission`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `update permission`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 7: Get Permission By Key

Business goal:
getPermissionByKey.

Domain context:
This behavior belongs to the `rbac-rest-controller` capability area and operates through `GET /users/rbac/permissions/{permissionKey}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `get permission by key` (`GET /users/rbac/permissions/{permissionKey}`) with path: permissionKey required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `permissionKey` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `permissionKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `get permission by key`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get permission by key`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get permission by key`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 8: Delete Permission By Key

Business goal:
deletePermissionByKey.

Domain context:
This behavior belongs to the `rbac-rest-controller` capability area and operates through `DELETE /users/rbac/permissions/{permissionKey}`.

Starting point:
The target resource identified by the path should exist and be eligible for removal.

Required execution workflow:
1. Use function `delete permission by key` (`DELETE /users/rbac/permissions/{permissionKey}`) with path: permissionKey required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the resource is absent or no longer active.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the delete operation.

Parameter and value bindings:
- Path values `permissionKey` identify the business resource scope for the operation.

Business result:
The addressed resource is removed, cancelled, or detached from its previous scope.

Constraints and invariants:
- Required request values: `permissionKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `delete permission by key`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `delete permission by key`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 204 No Content. Failure responses: 401 Unauthorized; 403 Forbidden.

### Behavior 9: Get Role Presentation List

Business goal:
getRolePresentationList.

Domain context:
This behavior belongs to the `rbac-rest-controller` capability area and operates through `GET /users/rbac/roles`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `get role presentation list` (`GET /users/rbac/roles`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `get role presentation list`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get role presentation list`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get role presentation list`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 10: Create Role

Business goal:
createRole.

Domain context:
This behavior belongs to the `rbac-rest-controller` capability area and operates through `POST /users/rbac/roles`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `create role` (`POST /users/rbac/roles`) with body: role required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `role`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `create role`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create role`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create role`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 11: Get Role By Id

Business goal:
getRoleById.

Domain context:
This behavior belongs to the `rbac-rest-controller` capability area and operates through `GET /users/rbac/roles/{roleId}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `get role by id` (`GET /users/rbac/roles/{roleId}`) with path: roleId required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `roleId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `roleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `get role by id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get role by id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get role by id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 12: Delete Role By Id

Business goal:
deleteRoleById.

Domain context:
This behavior belongs to the `rbac-rest-controller` capability area and operates through `DELETE /users/rbac/roles/{roleId}`.

Starting point:
The target resource identified by the path should exist and be eligible for removal.

Required execution workflow:
1. Use function `delete role by id` (`DELETE /users/rbac/roles/{roleId}`) with path: roleId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the resource is absent or no longer active.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the delete operation.

Parameter and value bindings:
- Path values `roleId` identify the business resource scope for the operation.

Business result:
The addressed resource is removed, cancelled, or detached from its previous scope.

Constraints and invariants:
- Required request values: `roleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `delete role by id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `delete role by id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 204 No Content. Failure responses: 401 Unauthorized; 403 Forbidden.

### Behavior 13: Add Permission On Role

Business goal:
addPermissionOnRole.

Domain context:
This behavior belongs to the `rbac-rest-controller` capability area and operates through `POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `add permission on role` (`POST /users/rbac/roles/{roleId}/permissions/{permissionKey}`) with path: permissionKey required, roleId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `permissionKey`, `roleId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `permissionKey`, `roleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `add permission on role`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `add permission on role`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `add permission on role`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 14: Remove Permission On Role

Business goal:
removePermissionOnRole.

Domain context:
This behavior belongs to the `rbac-rest-controller` capability area and operates through `DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}`.

Starting point:
The target resource identified by the path should exist and be eligible for removal.

Required execution workflow:
1. Use function `remove permission on role` (`DELETE /users/rbac/roles/{roleId}/permissions/{permissionKey}`) with path: permissionKey required, roleId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the resource is absent or no longer active.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the delete operation.

Parameter and value bindings:
- Path values `permissionKey`, `roleId` identify the business resource scope for the operation.

Business result:
The addressed resource is removed, cancelled, or detached from its previous scope.

Constraints and invariants:
- Required request values: `permissionKey`, `roleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `remove permission on role`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `remove permission on role`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 204 No Content. Failure responses: 401 Unauthorized; 403 Forbidden.

### Behavior 15: Generate Salt

Business goal:
generateSalt.

Domain context:
This behavior belongs to the `rbac-rest-controller` capability area and operates through `GET /users/rbac/salt`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `generate salt` (`GET /users/rbac/salt`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `generate salt`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `generate salt`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `generate salt`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 16: Register New User Account

Business goal:
registerNewUserAccount.

Domain context:
This behavior belongs to the `register-rest-controller` capability area and operates through `POST /users/register`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `register new user account` (`POST /users/register`) with body: registerUserAccountDTO required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `registerUserAccountDTO`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `register new user account`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `register new user account`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `register new user account`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 17: Get User By Id

Business goal:
getUserById.

Domain context:
This behavior belongs to the `user-rest-controller` capability area and operates through `GET /users/{id}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `get user by id` (`GET /users/{id}`) with path: id required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `id` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `id`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `get user by id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get user by id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get user by id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 18: Update User

Business goal:
updateUser.

Domain context:
This behavior belongs to the `user-rest-controller` capability area and operates through `PUT /users/{id}`.

Starting point:
The target resource identified by the path should exist unless the implementation treats the request as an upsert.

Required execution workflow:
1. Use function `update user` (`PUT /users/{id}`) with path: id required; body: updateUserDTO required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the update operation.

Parameter and value bindings:
- Path values `id` identify the business resource scope for the operation.

Business result:
The addressed resource is replaced with the submitted representation or command result.

Constraints and invariants:
- Required request values: `id`, `updateUserDTO`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `update user`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `update user`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `update user`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 19: Delete By Id

Business goal:
deleteById.

Domain context:
This behavior belongs to the `user-rest-controller` capability area and operates through `DELETE /users/{id}`.

Starting point:
The target resource identified by the path should exist and be eligible for removal.

Required execution workflow:
1. Use function `delete by id` (`DELETE /users/{id}`) with path: id required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the resource is absent or no longer active.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the delete operation.

Parameter and value bindings:
- Path values `id` identify the business resource scope for the operation.

Business result:
The addressed resource is removed, cancelled, or detached from its previous scope.

Constraints and invariants:
- Required request values: `id`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `delete by id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `delete by id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 204 No Content. Failure responses: 401 Unauthorized; 403 Forbidden.

### Behavior 20: Add Role

Business goal:
addRole.

Domain context:
This behavior belongs to the `user-rest-controller` capability area and operates through `POST /users/{id}/roles/{roleId}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `add role` (`POST /users/{id}/roles/{roleId}`) with path: id required, roleId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `id`, `roleId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `id`, `roleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `add role`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `add role`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `add role`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 21: Remove Role

Business goal:
removeRole.

Domain context:
This behavior belongs to the `user-rest-controller` capability area and operates through `DELETE /users/{id}/roles/{roleId}`.

Starting point:
The target resource identified by the path should exist and be eligible for removal.

Required execution workflow:
1. Use function `remove role` (`DELETE /users/{id}/roles/{roleId}`) with path: id required, roleId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the resource is absent or no longer active.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the delete operation.

Parameter and value bindings:
- Path values `id`, `roleId` identify the business resource scope for the operation.

Business result:
The addressed resource is removed, cancelled, or detached from its previous scope.

Constraints and invariants:
- Required request values: `id`, `roleId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `remove role`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `remove role`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 204 No Content. Failure responses: 401 Unauthorized; 403 Forbidden.
