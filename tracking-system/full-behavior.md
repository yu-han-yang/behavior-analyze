Implementation notes:

- `/api/**` is explicitly permitted by the Spring Security configuration, although the Swagger file documents `401` and `403` responses on many endpoints.
- API exception handling maps `ObjectNotFoundException`, `ObjectAlreadyExistsException`, `UsernameNotFoundException`, `DateTimeParseException`, `NumberFormatException`, `BadCredentialsException`, and generic `RuntimeException` to `400 BAD_REQUEST`.
- Most create and update endpoints delegate directly to `JpaRepository.save`, so they behave as save/upsert operations unless a database constraint or service dereference fails.

### Function 1: authenticate credential

Function name:
authenticate credential

Core endpoint(s):
- `POST /api/authenticate`
- `POST /api/authenticate/`

Preconditions:
- A credential with `username = {username}` exists in the database with a BCrypt password matching `{password}` and an `enabled` value. This can be satisfied by directly inserting a `user_credentials` row with the encoded password or by creating an employee through `POST /api/employees` with nested `credential.username`, `credential.password`, and `credential.role`, because employee save encodes the password and enables the credential.

Successful execution:
- Result:
  The endpoint validates `{username}` and `{password}` and returns `{username, isEligible}`, where `isEligible` is the stored credential `enabled` value.
- Invocation:
  Step 1: `POST /api/authenticate` with a JSON body containing `username={username}` and `password={password}`
- Constraints:
  The username must identify an existing credential and the password must match the BCrypt value accepted by Spring Security.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No credential exists for `{username}`, or the stored password for `{username}` does not match `{password}`. This can be produced by omitting direct insertion and not creating the credential through `POST /api/employees`, or by supplying a mismatched password.
  - Failure endpoint:
    `POST /api/authenticate`
  - Why this fails:
    `AuthenticationManager.authenticate(...)` throws `BadCredentialsException`, or credential lookup throws `UsernameNotFoundException`; the API returns `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    The credential existence or password-match requirement is violated.

Endpoint coverage:
- Covers:
  `POST /api/authenticate`
  `POST /api/authenticate/`
- Distinct meaning:
  Credential verification that returns eligibility, not token issuance.

### Function 2: list locations

Function name:
list locations

Core endpoint(s):
- `GET /api/locations`
- `GET /api/locations/`

Preconditions:
- None. The collection can be empty or contain any number of `locations` rows.

Successful execution:
- Result:
  The endpoint returns all stored locations.
- Invocation:
  Step 1: `GET /api/locations`
- Constraints:
  No request values are required.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No request-controlled invalid state is visible in the implementation.
  - Failure endpoint:
    `GET /api/locations`
  - Why this fails:
    Only an unexpected repository or runtime failure is visible for normal input.
  - Intentionally violated constraints:
    None visible in the implementation.

Endpoint coverage:
- Covers:
  `GET /api/locations`
  `GET /api/locations/`
- Distinct meaning:
  Collection read for locations.

### Function 3: save location

Function name:
save location

Core endpoint(s):
- `POST /api/locations`
- `POST /api/locations/save`

Preconditions:
- None. The request body supplies the location state; direct database setup is not required.

Successful execution:
- Result:
  The endpoint creates a new location when `locationId` is omitted, or persists the supplied location state through repository `save`.
- Invocation:
  Step 1: `POST /api/locations` with a JSON body containing location fields such as `adr`, `postalCode`, and `city`, and optionally `locationId`
- Constraints:
  The controller does not use `@Valid`, so visible validation annotations are not enforced at controller entry. The body must still be valid JSON that Jackson can deserialize and the persistence layer can store.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No existing resource state is required; the failing request body is malformed JSON or contains values the persistence layer cannot store.
  - Failure endpoint:
    `POST /api/locations`
  - Why this fails:
    Deserialization or repository/runtime exceptions are handled as `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    The JSON deserialization or persistence requirement is violated.

Endpoint coverage:
- Covers:
  `POST /api/locations`
  `POST /api/locations/save`
- Distinct meaning:
  Save or upsert a location record.

### Function 4: retrieve location by id

Function name:
retrieve location by id

Core endpoint(s):
- `GET /api/locations/{id}`

Preconditions:
- A location exists in the database with `location_id = {id}` and fields such as `adr`, `postal_code`, and `city`. This can be satisfied by directly inserting a `locations` row or by calling `POST /api/locations` with location fields.
- The `{id}` path value used by `GET /api/locations/{id}` must be the `locationId` generated by direct insertion or returned by `POST /api/locations`.

Successful execution:
- Result:
  The endpoint retrieves the location identified by `{id}`.
- Invocation:
  Step 1: `GET /api/locations/{id}` with numeric path value `id={locationId}`
- Constraints:
  `{id}` must parse as an integer and identify an existing location.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No location exists with `location_id = {id}`. This can be produced by starting from an empty database, deleting the location beforehand, or intentionally not inserting it directly and not calling `POST /api/locations`.
  - Failure endpoint:
    `GET /api/locations/{id}`
  - Why this fails:
    `LocationService.findById` throws `ObjectNotFoundException`; the API returns `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    The required location state was omitted or the wrong id was reused.
- Branch 2:
  - Preconditions:
    - The path value `{id}` is not parseable as an integer.
  - Failure endpoint:
    `GET /api/locations/{id}`
  - Why this fails:
    The controller calls `Integer.parseInt(id)`, causing `NumberFormatException`.
  - Intentionally violated constraints:
    The numeric path-id requirement is violated.

Endpoint coverage:
- Covers:
  `GET /api/locations/{id}`
- Distinct meaning:
  Resource read for one location.

### Function 5: update location

Function name:
update location

Core endpoint(s):
- `PUT /api/locations`
- `PUT /api/locations/update`

Preconditions:
- A location exists with `location_id = {locationId}` when the intent is to replace an existing record. This can be satisfied by directly inserting a `locations` row or by calling `POST /api/locations` with location fields.
- The update body should reuse the generated or returned `locationId`. The implementation delegates to `save`, so an unknown or absent id can be inserted rather than rejected.

Successful execution:
- Result:
  The endpoint persists replacement location state.
- Invocation:
  Step 1: `PUT /api/locations` with a JSON body containing `locationId={locationId}`, `adr`, `postalCode`, and `city`
- Constraints:
  The body must be valid JSON that can be deserialized to `Location` and saved.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A location may already exist with `location_id = {locationId}` through direct insertion or `POST /api/locations`, but the failing update body is malformed JSON or contains values the persistence layer rejects.
  - Failure endpoint:
    `PUT /api/locations`
  - Why this fails:
    Runtime exceptions are mapped to `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    The request body or persistence constraints are violated.

Endpoint coverage:
- Covers:
  `PUT /api/locations`
  `PUT /api/locations/update`
- Distinct meaning:
  Save replacement state for a location.

### Function 6: delete location

Function name:
delete location

Core endpoint(s):
- `DELETE /api/locations/{id}`
- `DELETE /api/locations/delete/{id}`

Preconditions:
- A location exists in the database with `location_id = {id}`. This can be satisfied by directly inserting a `locations` row or by calling `POST /api/locations`.
- The `{id}` path value must be the `locationId` generated by direct insertion or returned by `POST /api/locations`.

Successful execution:
- Result:
  The endpoint deletes the location identified by `{id}`.
- Invocation:
  Step 1: `DELETE /api/locations/{id}` with numeric path value `id={locationId}`
- Constraints:
  `{id}` must parse as an integer and identify an existing location. Existing departments referencing the location can cause a database constraint failure.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No location exists with `location_id = {id}`. This can be produced by starting from an empty database, deleting it beforehand, or intentionally not inserting it directly and not calling `POST /api/locations`.
  - Failure endpoint:
    `DELETE /api/locations/{id}`
  - Why this fails:
    The service calls `findById` before deletion and throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    The required location state was omitted.
- Branch 2:
  - Preconditions:
    - The path value `{id}` is not parseable as an integer.
  - Failure endpoint:
    `DELETE /api/locations/{id}`
  - Why this fails:
    The controller parses `{id}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric path-id requirement is violated.
- Branch 3:
  - Preconditions:
    - A location exists with `location_id = {id}` through direct insertion or `POST /api/locations`.
    - At least one department row references that location through `location_id = {id}`. This can be satisfied by directly inserting a `departments` row or by calling `POST /api/departments` with `location.locationId={id}`.
  - Failure endpoint:
    `DELETE /api/locations/{id}`
  - Why this fails:
    The database foreign key from departments to locations can reject deleting a referenced location.
  - Intentionally violated constraints:
    The parent location is deleted while child departments still reference it.

Endpoint coverage:
- Covers:
  `DELETE /api/locations/{id}`
  `DELETE /api/locations/delete/{id}`
- Distinct meaning:
  Delete one location.

### Function 7: list departments

Function name:
list departments

Core endpoint(s):
- `GET /api/departments`
- `GET /api/departments/`

Preconditions:
- None. The collection can be empty or contain any number of `departments` rows.

Successful execution:
- Result:
  The endpoint returns all stored departments.
- Invocation:
  Step 1: `GET /api/departments`
- Constraints:
  No request values are required.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No request-controlled invalid state is visible in the implementation.
  - Failure endpoint:
    `GET /api/departments`
  - Why this fails:
    Only unexpected repository or runtime errors are visible.
  - Intentionally violated constraints:
    None visible in the implementation.

Endpoint coverage:
- Covers:
  `GET /api/departments`
  `GET /api/departments/`
- Distinct meaning:
  Collection read for departments.

### Function 8: save department

Function name:
save department

Core endpoint(s):
- `POST /api/departments`
- `POST /api/departments/save`

Preconditions:
- A location exists with `location_id = {locationId}` for the department relationship. This can be satisfied by directly inserting a `locations` row or by calling `POST /api/locations` with `adr`, `postalCode`, and `city`.
- The request body must reuse the generated or returned `locationId` as `location.locationId`.

Successful execution:
- Result:
  The endpoint saves a department associated with a location.
- Invocation:
  Step 1: `POST /api/departments` with a JSON body containing `departmentName={departmentName}` and `location.locationId={locationId}`
- Constraints:
  The referenced location id must exist unless database constraints are disabled. The controller does not enforce bean validation before saving.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No location exists with `location_id = {locationId}`. This can be produced by intentionally not inserting the location directly and not calling `POST /api/locations`, or by using a deleted or wrong id.
  - Failure endpoint:
    `POST /api/departments`
  - Why this fails:
    The database foreign key from departments to locations can reject a missing `location_id`.
  - Intentionally violated constraints:
    The department references a nonexistent location.

Endpoint coverage:
- Covers:
  `POST /api/departments`
  `POST /api/departments/save`
- Distinct meaning:
  Save or upsert a department and its location relationship.

### Function 9: retrieve department by id

Function name:
retrieve department by id

Core endpoint(s):
- `GET /api/departments/{id}`

Preconditions:
- A location exists with `location_id = {locationId}`. This can be satisfied by direct insertion into `locations` or by calling `POST /api/locations`.
- A department exists with `department_id = {departmentId}`, `departmentName={departmentName}`, and `location_id = {locationId}`. This can be satisfied by direct insertion into `departments` or by calling `POST /api/departments` with `location.locationId={locationId}`.
- The `{id}` path value must be the `departmentId` generated by direct insertion or returned by `POST /api/departments`.

Successful execution:
- Result:
  The endpoint retrieves the department identified by `{id}`.
- Invocation:
  Step 1: `GET /api/departments/{id}` with numeric path value `id={departmentId}`
- Constraints:
  `{id}` must parse as an integer and identify an existing department.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No department exists with `department_id = {id}`. This can be produced by omitting direct insertion and not calling `POST /api/departments`.
  - Failure endpoint:
    `GET /api/departments/{id}`
  - Why this fails:
    `DepartmentService.findById` throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    The required department state was omitted.
- Branch 2:
  - Preconditions:
    - The path value `{id}` is not parseable as an integer.
  - Failure endpoint:
    `GET /api/departments/{id}`
  - Why this fails:
    The controller parses `{id}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric path-id requirement is violated.

Endpoint coverage:
- Covers:
  `GET /api/departments/{id}`
- Distinct meaning:
  Resource read for one department.

### Function 10: update department

Function name:
update department

Core endpoint(s):
- `PUT /api/departments`
- `PUT /api/departments/update`

Preconditions:
- A location exists with `location_id = {locationId}` for the existing department. This can be satisfied by directly inserting a `locations` row or by calling `POST /api/locations`.
- A department exists with `department_id = {departmentId}` and `location_id = {locationId}`. This can be satisfied by direct insertion into `departments` or by calling `POST /api/departments` with `location.locationId={locationId}`.
- The update body should reuse the generated or returned `departmentId`; the implementation delegates to `save`, so a new id with valid relationships may be inserted rather than rejected.

Successful execution:
- Result:
  The endpoint persists replacement department state.
- Invocation:
  Step 1: `PUT /api/departments` with a JSON body containing `departmentId={departmentId}`, `departmentName={departmentName}`, and `location.locationId={locationId}`
- Constraints:
  The referenced location id must exist. The body must be valid JSON that can be saved.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A department exists with `department_id = {departmentId}` through direct insertion or `POST /api/departments`.
    - No location exists with the updated `location.locationId = {badLocationId}` because that row was not inserted directly and `POST /api/locations` was not called for it.
  - Failure endpoint:
    `PUT /api/departments`
  - Why this fails:
    The department-location foreign key can reject the missing location.
  - Intentionally violated constraints:
    The updated department references a nonexistent location.

Endpoint coverage:
- Covers:
  `PUT /api/departments`
  `PUT /api/departments/update`
- Distinct meaning:
  Save replacement state for a department.

### Function 11: delete department

Function name:
delete department

Core endpoint(s):
- `DELETE /api/departments/{id}`
- `DELETE /api/departments/delete/{id}`

Preconditions:
- A location exists with `location_id = {locationId}`. This can be satisfied by direct insertion into `locations` or by calling `POST /api/locations`.
- A department exists with `department_id = {id}` and `location_id = {locationId}`. This can be satisfied by direct insertion into `departments` or by calling `POST /api/departments` with `location.locationId={locationId}`.
- The `{id}` path value must be the `departmentId` generated by direct insertion or returned by `POST /api/departments`.

Successful execution:
- Result:
  The endpoint deletes the department identified by `{id}`.
- Invocation:
  Step 1: `DELETE /api/departments/{id}` with numeric path value `id={departmentId}`
- Constraints:
  `{id}` must parse as an integer and identify an existing department. Existing employees referencing the department can cause a database constraint failure.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No department exists with `department_id = {id}`. This can be produced by omitting direct insertion and not calling `POST /api/departments`.
  - Failure endpoint:
    `DELETE /api/departments/{id}`
  - Why this fails:
    The service calls `findById` before deletion and throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    The required department state was omitted.
- Branch 2:
  - Preconditions:
    - The path value `{id}` is not parseable as an integer.
  - Failure endpoint:
    `DELETE /api/departments/{id}`
  - Why this fails:
    The controller parses `{id}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric path-id requirement is violated.
- Branch 3:
  - Preconditions:
    - A department exists with `department_id = {id}` through direct insertion or `POST /api/departments`.
    - At least one employee row references that department through `department_id = {id}`. This can be satisfied by directly inserting an `employees` row or by calling `POST /api/employees` with a manager whose department is `{id}`.
  - Failure endpoint:
    `DELETE /api/departments/{id}`
  - Why this fails:
    The employees-departments foreign key can reject deleting a referenced department.
  - Intentionally violated constraints:
    The parent department is deleted while employees still reference it.

Endpoint coverage:
- Covers:
  `DELETE /api/departments/{id}`
  `DELETE /api/departments/delete/{id}`
- Distinct meaning:
  Delete one department.

### Function 12: list projects

Function name:
list projects

Core endpoint(s):
- `GET /api/projects`
- `GET /api/projects/`

Preconditions:
- None. The collection can be empty or contain any number of `projects` rows.

Successful execution:
- Result:
  The endpoint returns all stored projects.
- Invocation:
  Step 1: `GET /api/projects`
- Constraints:
  No request values are required.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No request-controlled invalid state is visible in the implementation.
  - Failure endpoint:
    `GET /api/projects`
  - Why this fails:
    Only unexpected repository or runtime errors are visible.
  - Intentionally violated constraints:
    None visible in the implementation.

Endpoint coverage:
- Covers:
  `GET /api/projects`
  `GET /api/projects/`
- Distinct meaning:
  Collection read for projects.

### Function 13: save project

Function name:
save project

Core endpoint(s):
- `POST /api/projects`
- `POST /api/projects/save`

Preconditions:
- None. The request body supplies the project state; direct database setup is not required.

Successful execution:
- Result:
  The endpoint saves a project with title, start date, end date, and status.
- Invocation:
  Step 1: `POST /api/projects` with a JSON body containing `title`, `startDate`, `endDate`, and `status`, and optionally `projectId`
- Constraints:
  `startDate` and `endDate` use `dd-MM-yyyy`. The implementation delegates directly to repository `save`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No existing resource state is required; the request uses malformed JSON or date text that cannot be deserialized as `dd-MM-yyyy`.
  - Failure endpoint:
    `POST /api/projects`
  - Why this fails:
    JSON/date/runtime exceptions are handled as `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    The date format or request-body deserialization requirement is violated.

Endpoint coverage:
- Covers:
  `POST /api/projects`
  `POST /api/projects/save`
- Distinct meaning:
  Save or upsert a project.

### Function 14: retrieve project by id

Function name:
retrieve project by id

Core endpoint(s):
- `GET /api/projects/{id}`

Preconditions:
- A project exists with `project_id = {projectId}` and fields such as `title`, `start_date`, `end_date`, and `status`. This can be satisfied by directly inserting a `projects` row or by calling `POST /api/projects`.
- The `{id}` path value must be the `projectId` generated by direct insertion or returned by `POST /api/projects`.

Successful execution:
- Result:
  The endpoint retrieves the project identified by `{id}`.
- Invocation:
  Step 1: `GET /api/projects/{id}` with numeric path value `id={projectId}`
- Constraints:
  `{id}` must parse as an integer and identify an existing project.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No project exists with `project_id = {id}`. This can be produced by omitting direct insertion and not calling `POST /api/projects`.
  - Failure endpoint:
    `GET /api/projects/{id}`
  - Why this fails:
    `ProjectService.findById` throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    The required project state was omitted.
- Branch 2:
  - Preconditions:
    - The path value `{id}` is not parseable as an integer.
  - Failure endpoint:
    `GET /api/projects/{id}`
  - Why this fails:
    The controller parses `{id}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric path-id requirement is violated.

Endpoint coverage:
- Covers:
  `GET /api/projects/{id}`
- Distinct meaning:
  Resource read for one project.

### Function 15: update project

Function name:
update project

Core endpoint(s):
- `PUT /api/projects`
- `PUT /api/projects/update`

Preconditions:
- A project exists with `project_id = {projectId}` when the intent is to replace an existing project. This can be satisfied by direct insertion into `projects` or by calling `POST /api/projects`.
- The update body should reuse the generated or returned `projectId`; the implementation delegates to `save`, so an unknown or absent id can be inserted rather than rejected.

Successful execution:
- Result:
  The endpoint persists replacement project state.
- Invocation:
  Step 1: `PUT /api/projects` with a JSON body containing `projectId={projectId}`, `title`, `startDate`, `endDate`, and `status`
- Constraints:
  Dates use `dd-MM-yyyy`; the body must be valid JSON that can be deserialized and saved.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A project may already exist through direct insertion or `POST /api/projects`, but the update body contains malformed JSON or invalid `startDate` or `endDate` text.
  - Failure endpoint:
    `PUT /api/projects`
  - Why this fails:
    Runtime exceptions are handled as `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    The date format or request-body deserialization requirement is violated.

Endpoint coverage:
- Covers:
  `PUT /api/projects`
  `PUT /api/projects/update`
- Distinct meaning:
  Save replacement state for a project.

### Function 16: delete project

Function name:
delete project

Core endpoint(s):
- `DELETE /api/projects/{id}`
- `DELETE /api/projects/delete/{id}`

Preconditions:
- A project exists with `project_id = {id}`. This can be satisfied by directly inserting a `projects` row or by calling `POST /api/projects`.
- The `{id}` path value must be the `projectId` generated by direct insertion or returned by `POST /api/projects`.

Successful execution:
- Result:
  The endpoint deletes the project identified by `{id}`.
- Invocation:
  Step 1: `DELETE /api/projects/{id}` with numeric path value `id={projectId}`
- Constraints:
  `{id}` must parse as an integer and identify an existing project. Existing assignments referencing the project can prevent deletion.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No project exists with `project_id = {id}`. This can be produced by omitting direct insertion and not calling `POST /api/projects`.
  - Failure endpoint:
    `DELETE /api/projects/{id}`
  - Why this fails:
    The service calls `findById` before deletion and throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    The required project state was omitted.
- Branch 2:
  - Preconditions:
    - The path value `{id}` is not parseable as an integer.
  - Failure endpoint:
    `DELETE /api/projects/{id}`
  - Why this fails:
    The controller parses `{id}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric path-id requirement is violated.
- Branch 3:
  - Preconditions:
    - A project exists with `project_id = {id}` through direct insertion or `POST /api/projects`.
    - At least one assignment row references that project through `project_id = {id}`. This can be satisfied by directly inserting an `assignments` row or by calling `POST /api/assignments` with `projectId={id}`.
  - Failure endpoint:
    `DELETE /api/projects/{id}`
  - Why this fails:
    The assignments-projects foreign key can reject deleting a referenced project.
  - Intentionally violated constraints:
    The parent project is deleted while assignments still reference it.

Endpoint coverage:
- Covers:
  `DELETE /api/projects/{id}`
  `DELETE /api/projects/delete/{id}`
- Distinct meaning:
  Delete one project.

### Function 17: list credentials

Function name:
list credentials

Core endpoint(s):
- `GET /api/credentials`
- `GET /api/credentials/`

Preconditions:
- None. The collection can be empty or contain any number of `user_credentials` rows.

Successful execution:
- Result:
  The endpoint returns all stored credentials.
- Invocation:
  Step 1: `GET /api/credentials`
- Constraints:
  No request values are required.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No request-controlled invalid state is visible in the implementation.
  - Failure endpoint:
    `GET /api/credentials`
  - Why this fails:
    Only unexpected repository or runtime errors are visible.
  - Intentionally violated constraints:
    None visible in the implementation.

Endpoint coverage:
- Covers:
  `GET /api/credentials`
  `GET /api/credentials/`
- Distinct meaning:
  Collection read for credentials.

### Function 18: save credential

Function name:
save credential

Core endpoint(s):
- `POST /api/credentials`
- `POST /api/credentials/save`

Preconditions:
- None. The request body supplies the credential state; direct database setup is not required.

Successful execution:
- Result:
  The endpoint saves a credential record.
- Invocation:
  Step 1: `POST /api/credentials` with a JSON body containing `username`, `password`, `enabled`, `role`, and optionally `credentialId`
- Constraints:
  `username` must remain unique. Unlike credential update and employee save, this endpoint does not encode the password in the visible implementation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A credential with `username = {username}` already exists. This can be satisfied by directly inserting a `user_credentials` row or by calling `POST /api/credentials` once before the failing request with the same `username`.
  - Failure endpoint:
    `POST /api/credentials`
  - Why this fails:
    The credential entity and table define username as unique.
  - Intentionally violated constraints:
    The credential username uniqueness requirement is violated.

Endpoint coverage:
- Covers:
  `POST /api/credentials`
  `POST /api/credentials/save`
- Distinct meaning:
  Save or upsert a login credential record.

### Function 19: retrieve credential by id

Function name:
retrieve credential by id

Core endpoint(s):
- `GET /api/credentials/{id}`

Preconditions:
- A credential exists with `user_id = {credentialId}` and fields such as `username`, `password`, `enabled`, and `role`. This can be satisfied by directly inserting a `user_credentials` row or by calling `POST /api/credentials`.
- The `{id}` path value must be the `credentialId` generated by direct insertion or returned by `POST /api/credentials`.

Successful execution:
- Result:
  The endpoint retrieves the credential identified by `{id}`.
- Invocation:
  Step 1: `GET /api/credentials/{id}` with numeric path value `id={credentialId}`
- Constraints:
  `{id}` must parse as an integer and identify an existing credential.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No credential exists with `user_id = {id}`. This can be produced by omitting direct insertion and not calling `POST /api/credentials`.
  - Failure endpoint:
    `GET /api/credentials/{id}`
  - Why this fails:
    `CredentialService.findById` throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    The required credential state was omitted.
- Branch 2:
  - Preconditions:
    - The path value `{id}` is not parseable as an integer.
  - Failure endpoint:
    `GET /api/credentials/{id}`
  - Why this fails:
    The controller parses `{id}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric path-id requirement is violated.

Endpoint coverage:
- Covers:
  `GET /api/credentials/{id}`
- Distinct meaning:
  Resource read for one credential.

### Function 20: retrieve credential by username

Function name:
retrieve credential by username

Core endpoint(s):
- `GET /api/credentials/username/{username}`

Preconditions:
- A credential exists with `username = {username}`. This can be satisfied by directly inserting a `user_credentials` row or by calling `POST /api/credentials` with `username={username}`.
- The `{username}` path value must match the stored username after the controller trims surrounding whitespace. The repository query compares `LOWER(username)` to the supplied parameter, so using lowercase path values is the reliable form for mixed-case stored usernames.

Successful execution:
- Result:
  The endpoint retrieves the credential identified by `{username}`.
- Invocation:
  Step 1: `GET /api/credentials/username/{username}` with path value `username={username}`
- Constraints:
  The username must exist.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No credential exists with `username = {username}`. This can be produced by omitting direct insertion and not calling `POST /api/credentials`, or by using a different path value than the stored username.
  - Failure endpoint:
    `GET /api/credentials/username/{username}`
  - Why this fails:
    `CredentialService.findByUsername` throws `UsernameNotFoundException`.
  - Intentionally violated constraints:
    The required credential username state was omitted or mismatched.

Endpoint coverage:
- Covers:
  `GET /api/credentials/username/{username}`
- Distinct meaning:
  Lookup credential by username.

### Function 21: update credential

Function name:
update credential

Core endpoint(s):
- `PUT /api/credentials`
- `PUT /api/credentials/update`

Preconditions:
- A credential exists with `user_id = {credentialId}` when the intent is to replace an existing credential. This can be satisfied by directly inserting a `user_credentials` row or by calling `POST /api/credentials`.
- The update body should reuse the generated or returned `credentialId`; the implementation delegates to `save`, so an unknown id with a unique username may be inserted rather than rejected.

Successful execution:
- Result:
  The endpoint persists credential state and BCrypt-encodes the supplied password before saving.
- Invocation:
  Step 1: `PUT /api/credentials` with a JSON body containing `credentialId={credentialId}`, `username`, `password`, `enabled`, and `role`
- Constraints:
  `username` must remain unique. The supplied password is encoded by the service before persistence.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A credential exists with `username = {existingUsername}`. This can be satisfied by direct insertion or by calling `POST /api/credentials`.
    - A different credential exists with `user_id = {credentialId}`. This can be satisfied by direct insertion or by calling `POST /api/credentials` with another username.
  - Failure endpoint:
    `PUT /api/credentials`
  - Why this fails:
    Updating the second credential to use `{existingUsername}` violates the unique username constraint.
  - Intentionally violated constraints:
    The credential username uniqueness requirement is violated across two credential rows.

Endpoint coverage:
- Covers:
  `PUT /api/credentials`
  `PUT /api/credentials/update`
- Distinct meaning:
  Update credential fields and encode password.

### Function 22: delete credential by id

Function name:
delete credential by id

Core endpoint(s):
- `DELETE /api/credentials/{id}`
- `DELETE /api/credentials/delete/{id}`

Preconditions:
- A credential exists with `user_id = {id}`. This can be satisfied by directly inserting a `user_credentials` row or by calling `POST /api/credentials`.
- The `{id}` path value must be the `credentialId` generated by direct insertion or returned by `POST /api/credentials`.

Successful execution:
- Result:
  The endpoint deletes the credential identified by `{id}`.
- Invocation:
  Step 1: `DELETE /api/credentials/{id}` with numeric path value `id={credentialId}`
- Constraints:
  `{id}` must parse as an integer and identify an existing credential.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No credential exists with `user_id = {id}`. This can be produced by omitting direct insertion and not calling `POST /api/credentials`.
  - Failure endpoint:
    `DELETE /api/credentials/{id}`
  - Why this fails:
    The service calls `findById` before deletion and throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    The required credential state was omitted.
- Branch 2:
  - Preconditions:
    - The path value `{id}` is not parseable as an integer.
  - Failure endpoint:
    `DELETE /api/credentials/{id}`
  - Why this fails:
    The controller parses `{id}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric path-id requirement is violated.

Endpoint coverage:
- Covers:
  `DELETE /api/credentials/{id}`
  `DELETE /api/credentials/delete/{id}`
- Distinct meaning:
  Delete one credential by numeric id.

### Function 23: delete credential by username

Function name:
delete credential by username

Core endpoint(s):
- `DELETE /api/credentials/username/{username}`

Preconditions:
- A credential exists with `username = {username}`. This can be satisfied by directly inserting a `user_credentials` row or by calling `POST /api/credentials` with `username={username}`.
- The `{username}` path value must match the stored username. Unlike the get-by-username endpoint, this controller method does not trim the path value before lookup.

Successful execution:
- Result:
  The endpoint deletes the credential identified by `{username}`.
- Invocation:
  Step 1: `DELETE /api/credentials/username/{username}` with path value `username={username}`
- Constraints:
  The username must exist.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No credential exists with `username = {username}`. This can be produced by omitting direct insertion and not calling `POST /api/credentials`, or by using a different path value than the stored username.
  - Failure endpoint:
    `DELETE /api/credentials/username/{username}`
  - Why this fails:
    `CredentialService.findByUsername` throws `UsernameNotFoundException`.
  - Intentionally violated constraints:
    The required credential username state was omitted or mismatched.

Endpoint coverage:
- Covers:
  `DELETE /api/credentials/username/{username}`
- Distinct meaning:
  Delete one credential by username.

### Function 24: list employees

Function name:
list employees

Core endpoint(s):
- `GET /api/employees`
- `GET /api/employees/`

Preconditions:
- None. The collection can be empty or contain any number of `employees` rows.

Successful execution:
- Result:
  The endpoint returns all stored employees.
- Invocation:
  Step 1: `GET /api/employees`
- Constraints:
  No request values are required.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No request-controlled invalid state is visible in the implementation.
  - Failure endpoint:
    `GET /api/employees`
  - Why this fails:
    Only unexpected repository or runtime errors are visible.
  - Intentionally violated constraints:
    None visible in the implementation.

Endpoint coverage:
- Covers:
  `GET /api/employees`
  `GET /api/employees/`
- Distinct meaning:
  Collection read for employees.

### Function 25: save employee with credential

Function name:
save employee with credential

Core endpoint(s):
- `POST /api/employees`
- `POST /api/employees/save`

Preconditions:
- A manager employee exists with `employee_id = {managerId}` and a non-null `department_id`. This can be satisfied by direct database insertion into `employees` linked to a department, by using seeded manager data, or by calling `POST /api/employees` only after an existing manager is already available.
- A department exists for the manager's `department_id`. This can be satisfied by directly inserting a `departments` row linked to a location or by calling `POST /api/departments` with a valid `location.locationId`.
- The request body must reference the manager as `manager.employeeId={managerId}` and include enough nested manager data for `manager.department` to be non-null after deserialization.

Successful execution:
- Result:
  The endpoint saves an employee and cascades a credential. The service sets the employee department from the manager department, encodes the credential password, enables the credential, normalizes the role to start with `ROLE_`, and links the credential back to the employee.
- Invocation:
  Step 1: `POST /api/employees` with a JSON body containing employee fields, `manager` with a usable `department`, and nested `credential.username`, `credential.password`, and `credential.role`
- Constraints:
  `manager`, `manager.department`, and `credential` must be present. Nested credential username must be unique. `hiredate` uses `dd-MM-yyyy` when supplied.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid manager object with a non-null department is present in the request body, or no credential object is present in the request body.
  - Failure endpoint:
    `POST /api/employees`
  - Why this fails:
    The service dereferences `employee.getManager().getDepartment()` and `employee.getCredential()`, causing a runtime exception handled as `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    The required manager, manager department, or credential state was omitted.
- Branch 2:
  - Preconditions:
    - A credential with `username = {username}` already exists. This can be satisfied by direct insertion into `user_credentials`, by calling `POST /api/credentials`, or by calling `POST /api/employees` once with nested `credential.username={username}`.
    - The failing employee body uses nested `credential.username={username}`.
  - Failure endpoint:
    `POST /api/employees`
  - Why this fails:
    Employee save cascades credential persistence, and credential username is unique.
  - Intentionally violated constraints:
    The nested credential username uniqueness requirement is violated.

Endpoint coverage:
- Covers:
  `POST /api/employees`
  `POST /api/employees/save`
- Distinct meaning:
  Save or upsert an employee and nested login credential.

### Function 26: retrieve employee by id

Function name:
retrieve employee by id

Core endpoint(s):
- `GET /api/employees/{id}`

Preconditions:
- An employee exists with `employee_id = {employeeId}`. This can be satisfied by directly inserting an `employees` row with valid referenced department and manager ids when needed, or by calling `POST /api/employees` with a valid manager and nested credential.
- If the employee was created through the API, an existing manager with non-null department must have been available for `POST /api/employees`.
- The `{id}` path value must be the `employeeId` generated by direct insertion or returned by `POST /api/employees`.

Successful execution:
- Result:
  The endpoint retrieves the employee identified by `{id}`.
- Invocation:
  Step 1: `GET /api/employees/{id}` with numeric path value `id={employeeId}`
- Constraints:
  `{id}` must parse as an integer and identify an existing employee.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No employee exists with `employee_id = {id}`. This can be produced by omitting direct insertion and not calling `POST /api/employees`.
  - Failure endpoint:
    `GET /api/employees/{id}`
  - Why this fails:
    `EmployeeService.findById` throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    The required employee state was omitted.
- Branch 2:
  - Preconditions:
    - The path value `{id}` is not parseable as an integer.
  - Failure endpoint:
    `GET /api/employees/{id}`
  - Why this fails:
    The controller parses `{id}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric path-id requirement is violated.

Endpoint coverage:
- Covers:
  `GET /api/employees/{id}`
- Distinct meaning:
  Resource read for one employee.

### Function 27: retrieve employee by username

Function name:
retrieve employee by username

Core endpoint(s):
- `GET /api/employees/username/{username}`

Preconditions:
- An employee exists and is linked to a credential with `username = {username}`. This can be satisfied by directly inserting linked `employees` and `user_credentials` rows or by calling `POST /api/employees` with nested `credential.username={username}`.
- If the employee was created through the API, an existing manager with non-null department must have been available for `POST /api/employees`.

Successful execution:
- Result:
  The endpoint retrieves the employee associated with `{username}`.
- Invocation:
  Step 1: `GET /api/employees/username/{username}` with path value `username={username}`
- Constraints:
  The username must identify a credential whose `employee_id` links to an employee.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No credential exists with `username = {username}`, or the credential is not linked to an employee. This can be produced by omitting direct insertion and not calling `POST /api/employees`, or by creating an unlinked credential with `POST /api/credentials`.
  - Failure endpoint:
    `GET /api/employees/username/{username}`
  - Why this fails:
    Credential lookup throws `UsernameNotFoundException` when the username is missing; an unlinked credential can lead to a null employee result.
  - Intentionally violated constraints:
    The required employee-credential linkage is missing or mismatched.

Endpoint coverage:
- Covers:
  `GET /api/employees/username/{username}`
- Distinct meaning:
  Lookup employee through credential username.

### Function 28: update employee with credential

Function name:
update employee with credential

Core endpoint(s):
- `PUT /api/employees`
- `PUT /api/employees/update`

Preconditions:
- An employee exists with `employee_id = {employeeId}` and a linked credential when the intent is to replace an existing employee. This can be satisfied by directly inserting linked `employees` and `user_credentials` rows or by calling `POST /api/employees` with a valid manager and nested credential.
- A manager employee exists with a non-null department. This can be satisfied by direct insertion, seeded data, or a previous successful `POST /api/employees` after a manager was available.
- The update body should reuse `employeeId={employeeId}` and include `manager.department` and `credential`; the implementation delegates to `save`, so a new employee with valid relationships may be inserted rather than rejected.

Successful execution:
- Result:
  The endpoint persists employee and nested credential state. The service encodes the password, enables the credential, normalizes the role if needed, links the credential to the employee, and sets the employee department from the manager department.
- Invocation:
  Step 1: `PUT /api/employees` with a JSON body containing `employeeId={employeeId}`, employee fields, `manager` with a usable `department`, and nested `credential`
- Constraints:
  `manager`, `manager.department`, and `credential` must be present. Nested credential username must remain unique. `hiredate` uses `dd-MM-yyyy` when supplied.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An employee may exist through direct insertion or `POST /api/employees`, but the update body omits `manager`, `manager.department`, or `credential`.
  - Failure endpoint:
    `PUT /api/employees`
  - Why this fails:
    The service dereferences those objects before saving.
  - Intentionally violated constraints:
    The required manager, manager department, or credential object is omitted.
- Branch 2:
  - Preconditions:
    - A credential with `username = {existingUsername}` already exists and belongs to another employee. This can be satisfied by direct insertion or by a prior `POST /api/employees`.
    - The failing update body uses nested `credential.username={existingUsername}` for `{employeeId}`.
  - Failure endpoint:
    `PUT /api/employees`
  - Why this fails:
    Cascaded credential persistence can violate the unique username constraint.
  - Intentionally violated constraints:
    The nested credential username uniqueness requirement is violated.

Endpoint coverage:
- Covers:
  `PUT /api/employees`
  `PUT /api/employees/update`
- Distinct meaning:
  Update employee and nested credential state.

### Function 29: delete employee by id

Function name:
delete employee by id

Core endpoint(s):
- `DELETE /api/employees/{id}`
- `DELETE /api/employees/delete/{id}`

Preconditions:
- An employee exists with `employee_id = {id}`. This can be satisfied by directly inserting an `employees` row or by calling `POST /api/employees` with a valid manager and nested credential.
- If the employee was created through the API, an existing manager with non-null department must have been available for `POST /api/employees`.
- The `{id}` path value must be the `employeeId` generated by direct insertion or returned by `POST /api/employees`.

Successful execution:
- Result:
  The endpoint deletes the employee identified by `{id}`.
- Invocation:
  Step 1: `DELETE /api/employees/{id}` with numeric path value `id={employeeId}`
- Constraints:
  `{id}` must parse as an integer and identify an existing employee. Existing assignments, credentials, or managed employees can affect database deletion outcomes.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No employee exists with `employee_id = {id}`. This can be produced by omitting direct insertion and not calling `POST /api/employees`.
  - Failure endpoint:
    `DELETE /api/employees/{id}`
  - Why this fails:
    The service calls `findById` before deletion and throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    The required employee state was omitted.
- Branch 2:
  - Preconditions:
    - The path value `{id}` is not parseable as an integer.
  - Failure endpoint:
    `DELETE /api/employees/{id}`
  - Why this fails:
    The controller parses `{id}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric path-id requirement is violated.
- Branch 3:
  - Preconditions:
    - An employee exists with `employee_id = {id}` through direct insertion or `POST /api/employees`.
    - Another row references that employee as `manager_id`, or an assignment references that employee through `employee_id`. This can be satisfied by direct insertion or by using `POST /api/employees` or `POST /api/assignments` to create the dependent state.
  - Failure endpoint:
    `DELETE /api/employees/{id}`
  - Why this fails:
    Employee self-references or assignment foreign keys can reject deleting a referenced employee.
  - Intentionally violated constraints:
    The employee is deleted while dependent rows still reference it.

Endpoint coverage:
- Covers:
  `DELETE /api/employees/{id}`
  `DELETE /api/employees/delete/{id}`
- Distinct meaning:
  Delete one employee by numeric id.

### Function 30: delete employee by username

Function name:
delete employee by username

Core endpoint(s):
- `DELETE /api/employees/username/{username}`

Preconditions:
- An employee exists and is linked to a credential with `username = {username}`. This can be satisfied by directly inserting linked `employees` and `user_credentials` rows or by calling `POST /api/employees` with nested `credential.username={username}`.
- If the employee was created through the API, an existing manager with non-null department must have been available for `POST /api/employees`.

Successful execution:
- Result:
  The endpoint deletes the employee associated with `{username}`.
- Invocation:
  Step 1: `DELETE /api/employees/username/{username}` with path value `username={username}`
- Constraints:
  The username must identify a credential whose `employee_id` links to an employee.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No credential exists with `username = {username}`, or the credential is not linked to an employee. This can be produced by omitting direct insertion and not calling `POST /api/employees`, or by creating only an unlinked credential with `POST /api/credentials`.
  - Failure endpoint:
    `DELETE /api/employees/username/{username}`
  - Why this fails:
    Employee deletion by username delegates to credential lookup and then deletes the linked employee; missing username throws `UsernameNotFoundException`, and missing linkage prevents a valid employee delete.
  - Intentionally violated constraints:
    The required employee-credential linkage is missing or mismatched.

Endpoint coverage:
- Covers:
  `DELETE /api/employees/username/{username}`
- Distinct meaning:
  Delete employee through credential username.

### Function 31: list employees by department

Function name:
list employees by department

Core endpoint(s):
- `GET /api/employees/data/department/{departmentId}`

Preconditions:
- A location exists with `location_id = {locationId}` if a department is created through the API. This can be satisfied by direct insertion into `locations` or by calling `POST /api/locations`.
- A department exists with `department_id = {departmentId}` and `location_id = {locationId}` when the expected result should include department-scoped employees. This can be satisfied by direct insertion into `departments` or by calling `POST /api/departments` with `location.locationId={locationId}`.
- Employees may exist with `department_id = {departmentId}`. This can be satisfied by direct insertion into `employees` or by calling `POST /api/employees` with a manager whose department is `{departmentId}`.

Successful execution:
- Result:
  The endpoint returns employees whose `department_id` equals `{departmentId}`. If no department or employees exist for that id, the implementation can return an empty list rather than failing.
- Invocation:
  Step 1: `GET /api/employees/data/department/{departmentId}` with numeric path value `departmentId={departmentId}`
- Constraints:
  `{departmentId}` must parse as an integer.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The path value `{departmentId}` is not parseable as an integer.
  - Failure endpoint:
    `GET /api/employees/data/department/{departmentId}`
  - Why this fails:
    The controller parses `{departmentId}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric department id requirement is violated.

Endpoint coverage:
- Covers:
  `GET /api/employees/data/department/{departmentId}`
- Distinct meaning:
  Filter employees by department id.

### Function 32: list projects assigned to employee

Function name:
list projects assigned to employee

Core endpoint(s):
- `GET /api/employees/data/employee-project-data/{employeeId}`

Preconditions:
- A project exists with `project_id = {projectId}`. This can be satisfied by directly inserting a `projects` row or by calling `POST /api/projects`.
- An employee exists with `employee_id = {employeeId}`. This can be satisfied by directly inserting an `employees` row with valid references or by calling `POST /api/employees` with a valid manager and nested credential.
- An assignment exists linking `employee_id = {employeeId}` to `project_id = {projectId}` with `commitDate={commitDate}`. This can be satisfied by directly inserting an `assignments` row or by calling `POST /api/assignments` with `employeeId={employeeId}`, `projectId={projectId}`, and `commitDate={commitDate}`.
- The `{employeeId}` path value must reuse the employee id in the assignment.

Successful execution:
- Result:
  The endpoint returns project summary rows for projects assigned to `{employeeId}`.
- Invocation:
  Step 1: `GET /api/employees/data/employee-project-data/{employeeId}` with numeric path value `employeeId={employeeId}`
- Constraints:
  `{employeeId}` must parse as an integer. Missing assignment rows produce an empty list rather than a not-found error.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The path value `{employeeId}` is not parseable as an integer.
  - Failure endpoint:
    `GET /api/employees/data/employee-project-data/{employeeId}`
  - Why this fails:
    The controller parses `{employeeId}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric employee id requirement is violated.

Endpoint coverage:
- Covers:
  `GET /api/employees/data/employee-project-data/{employeeId}`
- Distinct meaning:
  Employee-facing assigned project list.

### Function 33: list projects managed by employee

Function name:
list projects managed by employee

Core endpoint(s):
- `GET /api/employees/data/manager-project-data/{employeeId}`

Preconditions:
- A project exists with `project_id = {projectId}`. This can be satisfied by directly inserting a `projects` row or by calling `POST /api/projects`.
- A subordinate employee exists with `employee_id = {subordinateEmployeeId}` and `manager_id = {employeeId}`. This can be satisfied by directly inserting an `employees` row or by calling `POST /api/employees` with `manager.employeeId={employeeId}` after a manager exists.
- An assignment exists linking `employee_id = {subordinateEmployeeId}` to `project_id = {projectId}` with `commitDate={commitDate}`. This can be satisfied by directly inserting an `assignments` row or by calling `POST /api/assignments` with `employeeId={subordinateEmployeeId}`, `projectId={projectId}`, and `commitDate={commitDate}`.
- The `{employeeId}` path value must be the manager id referenced by the subordinate employee.

Successful execution:
- Result:
  The endpoint returns project summary rows for projects assigned to employees managed by `{employeeId}`.
- Invocation:
  Step 1: `GET /api/employees/data/manager-project-data/{employeeId}` with numeric path value `employeeId={managerId}`
- Constraints:
  `{employeeId}` must parse as an integer. Missing subordinate assignment rows produce an empty list.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The path value `{employeeId}` is not parseable as an integer.
  - Failure endpoint:
    `GET /api/employees/data/manager-project-data/{employeeId}`
  - Why this fails:
    The controller parses `{employeeId}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric manager id requirement is violated.

Endpoint coverage:
- Covers:
  `GET /api/employees/data/manager-project-data/{employeeId}`
- Distinct meaning:
  Manager-facing assigned project list.

### Function 34: list assignments

Function name:
list assignments

Core endpoint(s):
- `GET /api/assignments`
- `GET /api/assignments/`

Preconditions:
- None. The collection can be empty or contain any number of `assignments` rows.

Successful execution:
- Result:
  The endpoint returns all stored assignment records.
- Invocation:
  Step 1: `GET /api/assignments`
- Constraints:
  No request values are required.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No request-controlled invalid state is visible in the implementation.
  - Failure endpoint:
    `GET /api/assignments`
  - Why this fails:
    Only unexpected repository or runtime errors are visible.
  - Intentionally violated constraints:
    None visible in the implementation.

Endpoint coverage:
- Covers:
  `GET /api/assignments`
  `GET /api/assignments/`
- Distinct meaning:
  Collection read for assignments.

### Function 35: save assignment

Function name:
save assignment

Core endpoint(s):
- `POST /api/assignments`
- `POST /api/assignments/save`

Preconditions:
- A project exists with `project_id = {projectId}`. This can be satisfied by directly inserting a `projects` row or by calling `POST /api/projects`.
- An employee exists with `employee_id = {employeeId}`. This can be satisfied by directly inserting an `employees` row with valid references or by calling `POST /api/employees` with a valid manager and nested credential.
- The assignment body must reuse `projectId={projectId}` and `employeeId={employeeId}` from those rows, and must include `commitDate={commitDate}` formatted as `dd-MM-yyyyHH:mm:ss`.

Successful execution:
- Result:
  The endpoint saves an assignment keyed by employee id, project id, and commit date.
- Invocation:
  Step 1: `POST /api/assignments` with a JSON body containing `employeeId={employeeId}`, `projectId={projectId}`, `commitDate={commitDate}`, and optional commit descriptions
- Constraints:
  The employee and project foreign keys must exist. The composite key is `(employeeId, projectId, commitDate)`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No employee exists with `employee_id = {employeeId}`, or no project exists with `project_id = {projectId}`. This can be produced by intentionally not inserting the missing row directly and not calling `POST /api/employees` or `POST /api/projects`.
  - Failure endpoint:
    `POST /api/assignments`
  - Why this fails:
    Assignment foreign keys require existing employee and project rows.
  - Intentionally violated constraints:
    The assignment references a nonexistent employee or project.
- Branch 2:
  - Preconditions:
    - A project exists with `project_id = {projectId}` through direct insertion or `POST /api/projects`.
    - An employee exists with `employee_id = {employeeId}` through direct insertion or `POST /api/employees`.
    - The failing assignment body contains a `commitDate` value not deserializable as `dd-MM-yyyyHH:mm:ss`.
  - Failure endpoint:
    `POST /api/assignments`
  - Why this fails:
    Assignment `commitDate` is a `LocalDateTime` using `dd-MM-yyyyHH:mm:ss`.
  - Intentionally violated constraints:
    The assignment commit-date format requirement is violated.
- Branch 3:
  - Preconditions:
    - An assignment already exists with `employee_id={employeeId}`, `project_id={projectId}`, and `commit_date={commitDate}`. This can be satisfied by directly inserting the `assignments` row or by calling `POST /api/assignments` once with the same composite key.
  - Failure endpoint:
    `POST /api/assignments`
  - Why this fails:
    The assignment table primary key is `(employee_id, project_id, commit_date)`, so a duplicate composite key can be rejected.
  - Intentionally violated constraints:
    The assignment composite-key uniqueness requirement is violated.

Endpoint coverage:
- Covers:
  `POST /api/assignments`
  `POST /api/assignments/save`
- Distinct meaning:
  Save or upsert an assignment or commit record.

### Function 36: retrieve assignment by composite id

Function name:
retrieve assignment by composite id

Core endpoint(s):
- `GET /api/assignments/{employeeId}/{projectId}/{commitDate}`

Preconditions:
- A project exists with `project_id = {projectId}`. This can be satisfied by directly inserting a `projects` row or by calling `POST /api/projects`.
- An employee exists with `employee_id = {employeeId}`. This can be satisfied by directly inserting an `employees` row or by calling `POST /api/employees` with a valid manager and nested credential.
- An assignment exists with `employee_id={employeeId}`, `project_id={projectId}`, and `commit_date={commitDate}`. This can be satisfied by directly inserting an `assignments` row or by calling `POST /api/assignments` with those values.
- The path values must match the assignment composite key exactly, and `{commitDate}` must use `dd-MM-yyyyHH:mm:ss`.

Successful execution:
- Result:
  The endpoint retrieves the assignment matching `{employeeId}`, `{projectId}`, and `{commitDate}`.
- Invocation:
  Step 1: `GET /api/assignments/{employeeId}/{projectId}/{commitDate}` with numeric `employeeId`, numeric `projectId`, and formatted `commitDate`
- Constraints:
  Both ids must parse as integers; `commitDate` must parse as `dd-MM-yyyyHH:mm:ss`; the composite key must exist.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No assignment exists with the composite key `{employeeId}`, `{projectId}`, and `{commitDate}`. This can be produced by omitting direct insertion and not calling `POST /api/assignments`, or by using mismatched path values.
  - Failure endpoint:
    `GET /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    `AssignmentService.findById` throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    The required assignment composite key state is missing or mismatched.
- Branch 2:
  - Preconditions:
    - The `{commitDate}` path value is not parseable as `dd-MM-yyyyHH:mm:ss`.
  - Failure endpoint:
    `GET /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    The controller parses the date with `DateTimeFormatter.ofPattern("dd-MM-yyyyHH:mm:ss")`.
  - Intentionally violated constraints:
    The commit-date path format requirement is violated.
- Branch 3:
  - Preconditions:
    - `{employeeId}` or `{projectId}` is not parseable as an integer.
  - Failure endpoint:
    `GET /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    The controller parses both ids with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric assignment id requirement is violated.

Endpoint coverage:
- Covers:
  `GET /api/assignments/{employeeId}/{projectId}/{commitDate}`
- Distinct meaning:
  Read the raw assignment entity by composite key.

### Function 37: update assignment

Function name:
update assignment

Core endpoint(s):
- `PUT /api/assignments`
- `PUT /api/assignments/update`

Preconditions:
- A project exists with `project_id = {projectId}`. This can be satisfied by directly inserting a `projects` row or by calling `POST /api/projects`.
- An employee exists with `employee_id = {employeeId}`. This can be satisfied by directly inserting an `employees` row or by calling `POST /api/employees` with a valid manager and nested credential.
- An assignment exists with `employee_id={employeeId}`, `project_id={projectId}`, and `commit_date={commitDate}` when the intent is to replace an existing assignment. This can be satisfied by directly inserting an `assignments` row or by calling `POST /api/assignments` with those values.
- The update body should reuse the same composite key. The implementation delegates to `save`, so a new composite key with valid foreign keys may be inserted rather than rejected.

Successful execution:
- Result:
  The endpoint saves replacement assignment values for a composite assignment key.
- Invocation:
  Step 1: `PUT /api/assignments` with a JSON body containing `employeeId={employeeId}`, `projectId={projectId}`, `commitDate={commitDate}`, and updated commit descriptions
- Constraints:
  Employee and project ids must exist. `commitDate` must deserialize as `dd-MM-yyyyHH:mm:ss`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No employee exists with `employee_id = {employeeId}`, or no project exists with `project_id = {projectId}`. This can be produced by omitting direct insertion and not calling `POST /api/employees` or `POST /api/projects`.
  - Failure endpoint:
    `PUT /api/assignments`
  - Why this fails:
    Assignment foreign keys require existing employee and project rows.
  - Intentionally violated constraints:
    The assignment references a nonexistent employee or project.
- Branch 2:
  - Preconditions:
    - A project exists with `project_id = {projectId}` through direct insertion or `POST /api/projects`.
    - An employee exists with `employee_id = {employeeId}` through direct insertion or `POST /api/employees`.
    - The failing update body contains `commitDate` text not deserializable as `dd-MM-yyyyHH:mm:ss`.
  - Failure endpoint:
    `PUT /api/assignments`
  - Why this fails:
    Assignment `commitDate` is a `LocalDateTime` using `dd-MM-yyyyHH:mm:ss`.
  - Intentionally violated constraints:
    The assignment commit-date format requirement is violated.

Endpoint coverage:
- Covers:
  `PUT /api/assignments`
  `PUT /api/assignments/update`
- Distinct meaning:
  Update or upsert an assignment record.

### Function 38: delete assignment

Function name:
delete assignment

Core endpoint(s):
- `DELETE /api/assignments/{employeeId}/{projectId}/{commitDate}`
- `DELETE /api/assignments/delete/{employeeId}/{projectId}/{commitDate}`

Preconditions:
- A project exists with `project_id = {projectId}`. This can be satisfied by directly inserting a `projects` row or by calling `POST /api/projects`.
- An employee exists with `employee_id = {employeeId}`. This can be satisfied by directly inserting an `employees` row or by calling `POST /api/employees` with a valid manager and nested credential.
- An assignment exists with `employee_id={employeeId}`, `project_id={projectId}`, and `commit_date={commitDate}`. This can be satisfied by directly inserting an `assignments` row or by calling `POST /api/assignments` with those values.
- The path values must match the assignment composite key exactly, and `{commitDate}` must use `dd-MM-yyyyHH:mm:ss`.

Successful execution:
- Result:
  The endpoint deletes the assignment matching `{employeeId}`, `{projectId}`, and `{commitDate}`.
- Invocation:
  Step 1: `DELETE /api/assignments/{employeeId}/{projectId}/{commitDate}` with numeric ids and formatted `commitDate`
- Constraints:
  Both ids must parse as integers; `commitDate` must parse as `dd-MM-yyyyHH:mm:ss`; the composite key must exist.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No assignment exists with the composite key `{employeeId}`, `{projectId}`, and `{commitDate}`. This can be produced by omitting direct insertion and not calling `POST /api/assignments`, or by using mismatched path values.
  - Failure endpoint:
    `DELETE /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    Delete calls `findById` first, which throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    The required assignment composite key state is missing or mismatched.
- Branch 2:
  - Preconditions:
    - The `{commitDate}` path value is not parseable as `dd-MM-yyyyHH:mm:ss`.
  - Failure endpoint:
    `DELETE /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    The controller parses the date with `DateTimeFormatter.ofPattern("dd-MM-yyyyHH:mm:ss")`.
  - Intentionally violated constraints:
    The commit-date path format requirement is violated.
- Branch 3:
  - Preconditions:
    - `{employeeId}` or `{projectId}` is not parseable as an integer.
  - Failure endpoint:
    `DELETE /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    The controller parses both ids with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric assignment id requirement is violated.

Endpoint coverage:
- Covers:
  `DELETE /api/assignments/{employeeId}/{projectId}/{commitDate}`
  `DELETE /api/assignments/delete/{employeeId}/{projectId}/{commitDate}`
- Distinct meaning:
  Delete one assignment by composite key.

### Function 39: list project commits for project

Function name:
list project commits for project

Core endpoint(s):
- `GET /api/assignments/data/project-commit/{projectId}`

Preconditions:
- A project exists with `project_id = {projectId}`. This can be satisfied by directly inserting a `projects` row or by calling `POST /api/projects`.
- An employee exists with `employee_id = {employeeId}`. This can be satisfied by directly inserting an `employees` row or by calling `POST /api/employees` with a valid manager and nested credential.
- An assignment exists with `employee_id={employeeId}`, `project_id={projectId}`, and `commit_date={commitDate}`. This can be satisfied by directly inserting an `assignments` row or by calling `POST /api/assignments` with those values.
- To appear in the result, the assignment must have `commit_emp_desc` not null or `LOWER(commit_mgr_desc) != 'init'`.

Successful execution:
- Result:
  The endpoint returns commit projection rows for `{projectId}`, excluding rows where employee description is null and manager description is `init`.
- Invocation:
  Step 1: `GET /api/assignments/data/project-commit/{projectId}` with numeric path value `projectId={projectId}`
- Constraints:
  `{projectId}` must parse as an integer. Missing matching rows produce an empty list.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The path value `{projectId}` is not parseable as an integer.
  - Failure endpoint:
    `GET /api/assignments/data/project-commit/{projectId}`
  - Why this fails:
    The controller parses `{projectId}` with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric project id requirement is violated.

Endpoint coverage:
- Covers:
  `GET /api/assignments/data/project-commit/{projectId}`
- Distinct meaning:
  Project-level commit report.

### Function 40: list project commits for employee and project

Function name:
list project commits for employee and project

Core endpoint(s):
- `GET /api/assignments/data/project-commit/{employeeId}/{projectId}`

Preconditions:
- A project exists with `project_id = {projectId}`. This can be satisfied by directly inserting a `projects` row or by calling `POST /api/projects`.
- An employee exists with `employee_id = {employeeId}`. This can be satisfied by directly inserting an `employees` row or by calling `POST /api/employees` with a valid manager and nested credential.
- An assignment exists with `employee_id={employeeId}`, `project_id={projectId}`, and `commit_date={commitDate}`. This can be satisfied by directly inserting an `assignments` row or by calling `POST /api/assignments` with those values.
- To appear in the result, the assignment must have `commit_emp_desc` not null or `LOWER(commit_mgr_desc) != 'init'`.

Successful execution:
- Result:
  The endpoint returns commit projection rows for one employee on one project, excluding initial-only rows.
- Invocation:
  Step 1: `GET /api/assignments/data/project-commit/{employeeId}/{projectId}` with numeric path values `employeeId={employeeId}` and `projectId={projectId}`
- Constraints:
  Both ids must parse as integers. Missing matching rows produce an empty list.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{employeeId}` or `{projectId}` is not parseable as an integer.
  - Failure endpoint:
    `GET /api/assignments/data/project-commit/{employeeId}/{projectId}`
  - Why this fails:
    The controller parses both values with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric employee or project id requirement is violated.

Endpoint coverage:
- Covers:
  `GET /api/assignments/data/project-commit/{employeeId}/{projectId}`
- Distinct meaning:
  Employee-and-project commit report.

### Function 41: retrieve one projected project commit

Function name:
retrieve one projected project commit

Core endpoint(s):
- `GET /api/assignments/data/project-commit/{employeeId}/{projectId}/{commitDate}`

Preconditions:
- A project exists with `project_id = {projectId}`. This can be satisfied by directly inserting a `projects` row or by calling `POST /api/projects`.
- An employee exists with `employee_id = {employeeId}`. This can be satisfied by directly inserting an `employees` row or by calling `POST /api/employees` with a valid manager and nested credential.
- An assignment exists with `employee_id={employeeId}`, `project_id={projectId}`, and `commit_date={commitDate}`. This can be satisfied by directly inserting an `assignments` row or by calling `POST /api/assignments` with those values.
- The path values must match the assignment composite key exactly, and `{commitDate}` must use `dd-MM-yyyyHH:mm:ss`.

Successful execution:
- Result:
  The endpoint returns a projected commit row for one employee, project, and commit date.
- Invocation:
  Step 1: `GET /api/assignments/data/project-commit/{employeeId}/{projectId}/{commitDate}` with numeric ids and formatted `commitDate`
- Constraints:
  Both ids must parse as integers; `commitDate` must parse as `dd-MM-yyyyHH:mm:ss`; the projected commit query must find a row.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No projected commit exists for `{employeeId}`, `{projectId}`, and `{commitDate}`. This can be produced by omitting direct insertion and not calling `POST /api/assignments`, or by using mismatched path values.
  - Failure endpoint:
    `GET /api/assignments/data/project-commit/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    Repository lookup returns empty and the service throws `NoSuchElementException`, handled as `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    The required projected commit row is missing or mismatched.
- Branch 2:
  - Preconditions:
    - The `{commitDate}` path value is not parseable as `dd-MM-yyyyHH:mm:ss`.
  - Failure endpoint:
    `GET /api/assignments/data/project-commit/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    The controller parses the date with `DateTimeFormatter.ofPattern("dd-MM-yyyyHH:mm:ss")`.
  - Intentionally violated constraints:
    The commit-date path format requirement is violated.
- Branch 3:
  - Preconditions:
    - `{employeeId}` or `{projectId}` is not parseable as an integer.
  - Failure endpoint:
    `GET /api/assignments/data/project-commit/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    The controller parses both ids with `Integer.parseInt`.
  - Intentionally violated constraints:
    The numeric employee or project id requirement is violated.

Endpoint coverage:
- Covers:
  `GET /api/assignments/data/project-commit/{employeeId}/{projectId}/{commitDate}`
- Distinct meaning:
  Read one commit report projection, not the raw assignment entity.

### Auxiliary endpoint without reliable implemented function

`GET /api/assignments/{employeeId}/{projectId}` appears in Swagger and in `AssignmentResource`, but the implementation returns `new ResponseEntity<>(null, null)` and contains a TODO stating that the method still needs to be created. It does not call a service method and does not implement a reliable function-level capability.
