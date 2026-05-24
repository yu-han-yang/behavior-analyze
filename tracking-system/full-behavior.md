Analyzed only `tracking-system.json` and `src`.

Implementation note: `/api/**` is explicitly `permitAll`; Swagger still lists `401/403` on most endpoints. The implementation generally returns `400 BAD_REQUEST` for handled runtime exceptions. Create/update methods mostly delegate to `JpaRepository.save`, so they behave as save/upsert operations rather than strict REST-only create/update.

### Behavior 1: authenticate credential

Behavior name:
authenticate credential

Successful execution:
- Result:
  Validates `{username}` and `{password}` and returns `{username, isEligible}`, where `isEligible` is the stored credential `enabled` value.
- Endpoint sequence:
  Step 1: `POST /api/authenticate`
  Alternative Step 1: `POST /api/authenticate/`
- Constraints:
  The request body must contain `username` and `password`. The username must identify an existing credential, and the password must match the BCrypt password used by Spring Security.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The username does not exist or the password is invalid.
  - Endpoint group:
    Step 1: `POST /api/authenticate`
  - Failure endpoint:
    `POST /api/authenticate`
  - Why this fails:
    `AuthenticationManager.authenticate(...)` throws `BadCredentialsException` or user lookup throws `UsernameNotFoundException`; the API exception handler returns `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    Use a missing username or mismatched password.

Endpoint coverage:
- Covers:
  `POST /api/authenticate`
  `POST /api/authenticate/`
- Distinct meaning:
  Login-style credential verification, not token issuance.

### Behavior 2: list locations

Behavior name:
list locations

Successful execution:
- Result:
  Returns all stored locations.
- Endpoint sequence:
  Step 1: `GET /api/locations`
  Alternative Step 1: `GET /api/locations/`
- Constraints:
  No specific existing location is required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No implementation-specific failure branch is visible for normal input.
  - Endpoint group:
    Step 1: `GET /api/locations`
  - Failure endpoint:
    `GET /api/locations`
  - Why this fails:
    Only an unexpected repository/runtime failure would fail; no documented request value can intentionally trigger a business failure.
  - Intentionally violated constraints:
    None visible in allowed files.

Endpoint coverage:
- Covers:
  `GET /api/locations`
  `GET /api/locations/`
- Distinct meaning:
  Collection read for locations.

### Behavior 3: save location

Behavior name:
save location

Successful execution:
- Result:
  Creates a new location when `locationId` is omitted, or persists the supplied location state through repository `save`.
- Endpoint sequence:
  Step 1: `POST /api/locations`
  Alternative Step 1: `POST /api/locations/save`
- Constraints:
  Body fields are `adr`, `postalCode`, and `city`. Validation annotations exist, but the API controller does not use `@Valid`, so controller-level validation is not enforced by the visible code.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Request body cannot be deserialized or persistence fails.
  - Endpoint group:
    Step 1: `POST /api/locations`
  - Failure endpoint:
    `POST /api/locations`
  - Why this fails:
    A runtime exception is handled as `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    Provide malformed JSON or values that the persistence layer cannot store.

Endpoint coverage:
- Covers:
  `POST /api/locations`
  `POST /api/locations/save`
- Distinct meaning:
  Save/upsert a location record.

### Behavior 4: retrieve location by id

Behavior name:
retrieve location by id

Successful execution:
- Result:
  Retrieves the location identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /api/locations`
  Step 2: `GET /api/locations/{id}`
- Constraints:
  `{id}` in Step 2 must be the `locationId` returned by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The location id does not exist.
  - Endpoint group:
    Step 1: `GET /api/locations/{id}`
  - Failure endpoint:
    `GET /api/locations/{id}`
  - Why this fails:
    `LocationService.findById` throws `ObjectNotFoundException`; the API returns `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    Omit the prerequisite `POST /api/locations` or use an id not returned by it.
- Branch 2:
  - Unsatisfied condition:
    `{id}` is not parseable as an integer.
  - Endpoint group:
    Step 1: `GET /api/locations/{id}`
  - Failure endpoint:
    `GET /api/locations/{id}`
  - Why this fails:
    The controller calls `Integer.parseInt(id)`.
  - Intentionally violated constraints:
    Use a non-numeric `{id}`.

Endpoint coverage:
- Covers:
  `GET /api/locations/{id}`
- Distinct meaning:
  Resource read for one location.

### Behavior 5: update location

Behavior name:
update location

Successful execution:
- Result:
  Replaces the stored location state with the request body values.
- Endpoint sequence:
  Step 1: `POST /api/locations`
  Step 2: `PUT /api/locations`
  Alternative Step 2: `PUT /api/locations/update`
- Constraints:
  The body in Step 2 must include the `locationId` produced by Step 1. The implementation does not check existence before `save`, so an unknown or absent id may be persisted rather than rejected.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Request body cannot be deserialized or persistence fails.
  - Endpoint group:
    Step 1: `POST /api/locations`
    Step 2: `PUT /api/locations`
  - Failure endpoint:
    `PUT /api/locations`
  - Why this fails:
    Runtime exceptions are mapped to `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    Send malformed JSON or values the persistence layer rejects.

Endpoint coverage:
- Covers:
  `PUT /api/locations`
  `PUT /api/locations/update`
- Distinct meaning:
  Save replacement state for a location.

### Behavior 6: delete location

Behavior name:
delete location

Successful execution:
- Result:
  Deletes the location identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /api/locations`
  Step 2: `DELETE /api/locations/{id}`
  Alternative Step 2: `DELETE /api/locations/delete/{id}`
- Constraints:
  `{id}` must be the `locationId` returned by Step 1. If departments reference this location, database constraints may prevent deletion.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The location id does not exist.
  - Endpoint group:
    Step 1: `DELETE /api/locations/{id}`
  - Failure endpoint:
    `DELETE /api/locations/{id}`
  - Why this fails:
    The service calls `findById` before delete and throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    Omit the prerequisite `POST /api/locations`.
- Branch 2:
  - Unsatisfied condition:
    The path id is not numeric.
  - Endpoint group:
    Step 1: `DELETE /api/locations/{id}`
  - Failure endpoint:
    `DELETE /api/locations/{id}`
  - Why this fails:
    The controller parses `{id}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric `{id}`.

Endpoint coverage:
- Covers:
  `DELETE /api/locations/{id}`
  `DELETE /api/locations/delete/{id}`
- Distinct meaning:
  Delete one location.

### Behavior 7: list departments

Behavior name:
list departments

Successful execution:
- Result:
  Returns all stored departments.
- Endpoint sequence:
  Step 1: `GET /api/departments`
  Alternative Step 1: `GET /api/departments/`
- Constraints:
  No specific department is required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No implementation-specific request failure is visible.
  - Endpoint group:
    Step 1: `GET /api/departments`
  - Failure endpoint:
    `GET /api/departments`
  - Why this fails:
    Only unexpected repository/runtime errors are visible.
  - Intentionally violated constraints:
    None visible in allowed files.

Endpoint coverage:
- Covers:
  `GET /api/departments`
  `GET /api/departments/`
- Distinct meaning:
  Collection read for departments.

### Behavior 8: save department

Behavior name:
save department

Successful execution:
- Result:
  Saves a department associated with a location.
- Endpoint sequence:
  Step 1: `POST /api/locations`
  Step 2: `POST /api/departments`
  Alternative Step 2: `POST /api/departments/save`
- Constraints:
  Step 2 body must include `departmentName` and `location.locationId` from Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The referenced location does not exist.
  - Endpoint group:
    Step 1: `POST /api/departments`
  - Failure endpoint:
    `POST /api/departments`
  - Why this fails:
    The database foreign key from departments to locations can reject a missing `location_id`.
  - Intentionally violated constraints:
    Omit `POST /api/locations` and send a nonexistent `location.locationId`.

Endpoint coverage:
- Covers:
  `POST /api/departments`
  `POST /api/departments/save`
- Distinct meaning:
  Save/upsert a department and its location relationship.

### Behavior 9: retrieve department by id

Behavior name:
retrieve department by id

Successful execution:
- Result:
  Retrieves the department identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /api/locations`
  Step 2: `POST /api/departments`
  Step 3: `GET /api/departments/{id}`
- Constraints:
  Step 2 must use `location.locationId` from Step 1. `{id}` in Step 3 must be `departmentId` from Step 2.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The department id does not exist.
  - Endpoint group:
    Step 1: `GET /api/departments/{id}`
  - Failure endpoint:
    `GET /api/departments/{id}`
  - Why this fails:
    `DepartmentService.findById` throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    Omit the prerequisite department creation.
- Branch 2:
  - Unsatisfied condition:
    `{id}` is not numeric.
  - Endpoint group:
    Step 1: `GET /api/departments/{id}`
  - Failure endpoint:
    `GET /api/departments/{id}`
  - Why this fails:
    The controller parses `{id}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric path value.

Endpoint coverage:
- Covers:
  `GET /api/departments/{id}`
- Distinct meaning:
  Resource read for one department.

### Behavior 10: update department

Behavior name:
update department

Successful execution:
- Result:
  Replaces the stored department state.
- Endpoint sequence:
  Step 1: `POST /api/locations`
  Step 2: `POST /api/departments`
  Step 3: `PUT /api/departments`
  Alternative Step 3: `PUT /api/departments/update`
- Constraints:
  Step 2 and Step 3 bodies must reference a valid `location.locationId`. Step 3 body must include `departmentId` from Step 2.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The updated body references a missing location.
  - Endpoint group:
    Step 1: `POST /api/locations`
    Step 2: `POST /api/departments`
    Step 3: `PUT /api/departments`
  - Failure endpoint:
    `PUT /api/departments`
  - Why this fails:
    The department-location foreign key can reject the update.
  - Intentionally violated constraints:
    Use a `location.locationId` in Step 3 that was not produced by `POST /api/locations`.

Endpoint coverage:
- Covers:
  `PUT /api/departments`
  `PUT /api/departments/update`
- Distinct meaning:
  Save replacement state for a department.

### Behavior 11: delete department

Behavior name:
delete department

Successful execution:
- Result:
  Deletes the department identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /api/locations`
  Step 2: `POST /api/departments`
  Step 3: `DELETE /api/departments/{id}`
  Alternative Step 3: `DELETE /api/departments/delete/{id}`
- Constraints:
  `{id}` must be `departmentId` from Step 2. Existing employees referencing the department may prevent deletion.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The department id does not exist.
  - Endpoint group:
    Step 1: `DELETE /api/departments/{id}`
  - Failure endpoint:
    `DELETE /api/departments/{id}`
  - Why this fails:
    The service calls `findById` before delete.
  - Intentionally violated constraints:
    Omit the prerequisite department creation.
- Branch 2:
  - Unsatisfied condition:
    `{id}` is not numeric.
  - Endpoint group:
    Step 1: `DELETE /api/departments/{id}`
  - Failure endpoint:
    `DELETE /api/departments/{id}`
  - Why this fails:
    The controller parses `{id}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric path value.

Endpoint coverage:
- Covers:
  `DELETE /api/departments/{id}`
  `DELETE /api/departments/delete/{id}`
- Distinct meaning:
  Delete one department.

### Behavior 12: list projects

Behavior name:
list projects

Successful execution:
- Result:
  Returns all stored projects.
- Endpoint sequence:
  Step 1: `GET /api/projects`
  Alternative Step 1: `GET /api/projects/`
- Constraints:
  No specific project is required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No implementation-specific request failure is visible.
  - Endpoint group:
    Step 1: `GET /api/projects`
  - Failure endpoint:
    `GET /api/projects`
  - Why this fails:
    Only unexpected repository/runtime errors are visible.
  - Intentionally violated constraints:
    None visible in allowed files.

Endpoint coverage:
- Covers:
  `GET /api/projects`
  `GET /api/projects/`
- Distinct meaning:
  Collection read for projects.

### Behavior 13: save project

Behavior name:
save project

Successful execution:
- Result:
  Saves a project with title, start date, end date, and status.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Alternative Step 1: `POST /api/projects/save`
- Constraints:
  Body fields include `title`, `startDate`, `endDate`, and `status`; dates use `dd-MM-yyyy`. The implementation delegates directly to repository `save`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Date fields cannot be deserialized or persistence fails.
  - Endpoint group:
    Step 1: `POST /api/projects`
  - Failure endpoint:
    `POST /api/projects`
  - Why this fails:
    JSON/date/runtime exceptions are handled as `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    Use invalid date text or malformed JSON.

Endpoint coverage:
- Covers:
  `POST /api/projects`
  `POST /api/projects/save`
- Distinct meaning:
  Save/upsert a project.

### Behavior 14: retrieve project by id

Behavior name:
retrieve project by id

Successful execution:
- Result:
  Retrieves the project identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Step 2: `GET /api/projects/{id}`
- Constraints:
  `{id}` in Step 2 must be `projectId` returned by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The project id does not exist.
  - Endpoint group:
    Step 1: `GET /api/projects/{id}`
  - Failure endpoint:
    `GET /api/projects/{id}`
  - Why this fails:
    `ProjectService.findById` throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    Omit the prerequisite project creation.
- Branch 2:
  - Unsatisfied condition:
    `{id}` is not numeric.
  - Endpoint group:
    Step 1: `GET /api/projects/{id}`
  - Failure endpoint:
    `GET /api/projects/{id}`
  - Why this fails:
    The controller parses `{id}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric path value.

Endpoint coverage:
- Covers:
  `GET /api/projects/{id}`
- Distinct meaning:
  Resource read for one project.

### Behavior 15: update project

Behavior name:
update project

Successful execution:
- Result:
  Replaces the stored project state.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Step 2: `PUT /api/projects`
  Alternative Step 2: `PUT /api/projects/update`
- Constraints:
  Step 2 body must include `projectId` from Step 1. Dates use `dd-MM-yyyy`. The implementation does not verify existence before save.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Date fields cannot be deserialized or persistence fails.
  - Endpoint group:
    Step 1: `POST /api/projects`
    Step 2: `PUT /api/projects`
  - Failure endpoint:
    `PUT /api/projects`
  - Why this fails:
    Runtime exceptions are handled as `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    Send invalid date text or malformed JSON.

Endpoint coverage:
- Covers:
  `PUT /api/projects`
  `PUT /api/projects/update`
- Distinct meaning:
  Save replacement state for a project.

### Behavior 16: delete project

Behavior name:
delete project

Successful execution:
- Result:
  Deletes the project identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Step 2: `DELETE /api/projects/{id}`
  Alternative Step 2: `DELETE /api/projects/delete/{id}`
- Constraints:
  `{id}` must be `projectId` from Step 1. Existing assignments referencing the project may prevent deletion.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The project id does not exist.
  - Endpoint group:
    Step 1: `DELETE /api/projects/{id}`
  - Failure endpoint:
    `DELETE /api/projects/{id}`
  - Why this fails:
    The service calls `findById` before delete.
  - Intentionally violated constraints:
    Omit the prerequisite project creation.
- Branch 2:
  - Unsatisfied condition:
    `{id}` is not numeric.
  - Endpoint group:
    Step 1: `DELETE /api/projects/{id}`
  - Failure endpoint:
    `DELETE /api/projects/{id}`
  - Why this fails:
    The controller parses `{id}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric path value.

Endpoint coverage:
- Covers:
  `DELETE /api/projects/{id}`
  `DELETE /api/projects/delete/{id}`
- Distinct meaning:
  Delete one project.

### Behavior 17: list credentials

Behavior name:
list credentials

Successful execution:
- Result:
  Returns all stored credentials.
- Endpoint sequence:
  Step 1: `GET /api/credentials`
  Alternative Step 1: `GET /api/credentials/`
- Constraints:
  No specific credential is required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No implementation-specific request failure is visible.
  - Endpoint group:
    Step 1: `GET /api/credentials`
  - Failure endpoint:
    `GET /api/credentials`
  - Why this fails:
    Only unexpected repository/runtime errors are visible.
  - Intentionally violated constraints:
    None visible in allowed files.

Endpoint coverage:
- Covers:
  `GET /api/credentials`
  `GET /api/credentials/`
- Distinct meaning:
  Collection read for credentials.

### Behavior 18: save credential

Behavior name:
save credential

Successful execution:
- Result:
  Saves a credential record.
- Endpoint sequence:
  Step 1: `POST /api/credentials`
  Alternative Step 1: `POST /api/credentials/save`
- Constraints:
  Body fields are `username`, `password`, `enabled`, and `role`. Unlike credential updates and employee creation, this save method does not encode the password in the visible implementation.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The username duplicates an existing credential username.
  - Endpoint group:
    Step 1: `POST /api/credentials`
    Step 2: `POST /api/credentials`
  - Failure endpoint:
    `POST /api/credentials`
  - Why this fails:
    The credential entity/table defines username as unique.
  - Intentionally violated constraints:
    Use the same `username` in both bodies.

Endpoint coverage:
- Covers:
  `POST /api/credentials`
  `POST /api/credentials/save`
- Distinct meaning:
  Save/upsert a login credential record.

### Behavior 19: retrieve credential by id

Behavior name:
retrieve credential by id

Successful execution:
- Result:
  Retrieves the credential identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /api/credentials`
  Step 2: `GET /api/credentials/{id}`
- Constraints:
  `{id}` in Step 2 must be `credentialId` returned by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The credential id does not exist.
  - Endpoint group:
    Step 1: `GET /api/credentials/{id}`
  - Failure endpoint:
    `GET /api/credentials/{id}`
  - Why this fails:
    `CredentialService.findById` throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    Omit the prerequisite credential creation.
- Branch 2:
  - Unsatisfied condition:
    `{id}` is not numeric.
  - Endpoint group:
    Step 1: `GET /api/credentials/{id}`
  - Failure endpoint:
    `GET /api/credentials/{id}`
  - Why this fails:
    The controller parses `{id}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric path value.

Endpoint coverage:
- Covers:
  `GET /api/credentials/{id}`
- Distinct meaning:
  Resource read for one credential.

### Behavior 20: retrieve credential by username

Behavior name:
retrieve credential by username

Successful execution:
- Result:
  Retrieves the credential identified by `{username}`.
- Endpoint sequence:
  Step 1: `POST /api/credentials`
  Step 2: `GET /api/credentials/username/{username}`
- Constraints:
  `{username}` in Step 2 must match the `username` saved in Step 1. The implementation trims username only for this lookup endpoint.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The username does not exist.
  - Endpoint group:
    Step 1: `GET /api/credentials/username/{username}`
  - Failure endpoint:
    `GET /api/credentials/username/{username}`
  - Why this fails:
    `CredentialService.findByUsername` throws `UsernameNotFoundException`.
  - Intentionally violated constraints:
    Omit credential creation or use a different username.

Endpoint coverage:
- Covers:
  `GET /api/credentials/username/{username}`
- Distinct meaning:
  Lookup credential by username.

### Behavior 21: update credential

Behavior name:
update credential

Successful execution:
- Result:
  Replaces credential state and BCrypt-encodes the supplied password before saving.
- Endpoint sequence:
  Step 1: `POST /api/credentials`
  Step 2: `PUT /api/credentials`
  Alternative Step 2: `PUT /api/credentials/update`
- Constraints:
  Step 2 body must include `credentialId` from Step 1 to update that credential. If username is changed, it must remain unique.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The updated username duplicates another credential.
  - Endpoint group:
    Step 1: `POST /api/credentials`
    Step 2: `POST /api/credentials`
    Step 3: `PUT /api/credentials`
  - Failure endpoint:
    `PUT /api/credentials`
  - Why this fails:
    The username column is unique.
  - Intentionally violated constraints:
    Use Step 1’s username in Step 3 while updating the credential created in Step 2.

Endpoint coverage:
- Covers:
  `PUT /api/credentials`
  `PUT /api/credentials/update`
- Distinct meaning:
  Update credential fields and encode password.

### Behavior 22: delete credential by id

Behavior name:
delete credential by id

Successful execution:
- Result:
  Deletes the credential identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /api/credentials`
  Step 2: `DELETE /api/credentials/{id}`
  Alternative Step 2: `DELETE /api/credentials/delete/{id}`
- Constraints:
  `{id}` must be `credentialId` from Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The credential id does not exist.
  - Endpoint group:
    Step 1: `DELETE /api/credentials/{id}`
  - Failure endpoint:
    `DELETE /api/credentials/{id}`
  - Why this fails:
    The service calls `findById` before delete.
  - Intentionally violated constraints:
    Omit the prerequisite credential creation.
- Branch 2:
  - Unsatisfied condition:
    `{id}` is not numeric.
  - Endpoint group:
    Step 1: `DELETE /api/credentials/{id}`
  - Failure endpoint:
    `DELETE /api/credentials/{id}`
  - Why this fails:
    The controller parses `{id}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric path value.

Endpoint coverage:
- Covers:
  `DELETE /api/credentials/{id}`
  `DELETE /api/credentials/delete/{id}`
- Distinct meaning:
  Delete one credential by numeric id.

### Behavior 23: delete credential by username

Behavior name:
delete credential by username

Successful execution:
- Result:
  Deletes the credential identified by `{username}`.
- Endpoint sequence:
  Step 1: `POST /api/credentials`
  Step 2: `DELETE /api/credentials/username/{username}`
- Constraints:
  `{username}` must match the username saved in Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The username does not exist.
  - Endpoint group:
    Step 1: `DELETE /api/credentials/username/{username}`
  - Failure endpoint:
    `DELETE /api/credentials/username/{username}`
  - Why this fails:
    `CredentialService.findByUsername` throws `UsernameNotFoundException`.
  - Intentionally violated constraints:
    Omit credential creation or use a different username.

Endpoint coverage:
- Covers:
  `DELETE /api/credentials/username/{username}`
- Distinct meaning:
  Delete one credential by username.

### Behavior 24: list employees

Behavior name:
list employees

Successful execution:
- Result:
  Returns all stored employees.
- Endpoint sequence:
  Step 1: `GET /api/employees`
  Alternative Step 1: `GET /api/employees/`
- Constraints:
  No specific employee is required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No implementation-specific request failure is visible.
  - Endpoint group:
    Step 1: `GET /api/employees`
  - Failure endpoint:
    `GET /api/employees`
  - Why this fails:
    Only unexpected repository/runtime errors are visible.
  - Intentionally violated constraints:
    None visible in allowed files.

Endpoint coverage:
- Covers:
  `GET /api/employees`
  `GET /api/employees/`
- Distinct meaning:
  Collection read for employees.

### Behavior 25: save employee with credential

Behavior name:
save employee with credential

Successful execution:
- Result:
  Saves an employee and cascades a credential; the service sets the employee department from the manager department, encodes the credential password, enables the credential, normalizes the role to start with `ROLE_`, and links the credential back to the employee.
- Endpoint sequence:
  Step 1: `POST /api/employees`
  Alternative Step 1: `POST /api/employees/save`
- Constraints:
  Body must include `manager` with a usable `department`, and `credential` with `username`, `password`, and `role`. The API has no documented bootstrap endpoint for creating the first manager without already having a manager, so the first successful employee save relies on an existing manager record, such as seeded data.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `manager` or `credential` is missing.
  - Endpoint group:
    Step 1: `POST /api/employees`
  - Failure endpoint:
    `POST /api/employees`
  - Why this fails:
    The service dereferences `employee.getManager().getDepartment()` and `employee.getCredential()`, causing a runtime exception handled as `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    Omit `manager`, `manager.department`, or `credential`.
- Branch 2:
  - Unsatisfied condition:
    Credential username duplicates an existing credential.
  - Endpoint group:
    Step 1: `POST /api/employees`
    Step 2: `POST /api/employees`
  - Failure endpoint:
    `POST /api/employees`
  - Why this fails:
    Employee save cascades credential persistence and credential username is unique.
  - Intentionally violated constraints:
    Use the same nested `credential.username` in both employee bodies.

Endpoint coverage:
- Covers:
  `POST /api/employees`
  `POST /api/employees/save`
- Distinct meaning:
  Save/upsert an employee and nested login credential.

### Behavior 26: retrieve employee by id

Behavior name:
retrieve employee by id

Successful execution:
- Result:
  Retrieves the employee identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /api/employees`
  Step 2: `GET /api/employees/{id}`
- Constraints:
  `{id}` in Step 2 must be `employeeId` returned by Step 1. Step 1 itself requires an existing manager as described above.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The employee id does not exist.
  - Endpoint group:
    Step 1: `GET /api/employees/{id}`
  - Failure endpoint:
    `GET /api/employees/{id}`
  - Why this fails:
    `EmployeeService.findById` throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    Omit employee creation.
- Branch 2:
  - Unsatisfied condition:
    `{id}` is not numeric.
  - Endpoint group:
    Step 1: `GET /api/employees/{id}`
  - Failure endpoint:
    `GET /api/employees/{id}`
  - Why this fails:
    The controller parses `{id}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric path value.

Endpoint coverage:
- Covers:
  `GET /api/employees/{id}`
- Distinct meaning:
  Resource read for one employee.

### Behavior 27: retrieve employee by username

Behavior name:
retrieve employee by username

Successful execution:
- Result:
  Retrieves the employee associated with `{username}`.
- Endpoint sequence:
  Step 1: `POST /api/employees`
  Step 2: `GET /api/employees/username/{username}`
- Constraints:
  `{username}` must match the nested `credential.username` saved in Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The username does not exist.
  - Endpoint group:
    Step 1: `GET /api/employees/username/{username}`
  - Failure endpoint:
    `GET /api/employees/username/{username}`
  - Why this fails:
    Employee lookup delegates to credential lookup, which throws `UsernameNotFoundException`.
  - Intentionally violated constraints:
    Omit employee/credential creation or use a different username.

Endpoint coverage:
- Covers:
  `GET /api/employees/username/{username}`
- Distinct meaning:
  Lookup employee through credential username.

### Behavior 28: update employee with credential

Behavior name:
update employee with credential

Successful execution:
- Result:
  Replaces employee state and nested credential state; password is encoded, credential is enabled, role is normalized if needed, and department is set from the manager department.
- Endpoint sequence:
  Step 1: `POST /api/employees`
  Step 2: `PUT /api/employees`
  Alternative Step 2: `PUT /api/employees/update`
- Constraints:
  Step 2 body must include `employeeId` from Step 1, a non-null `manager.department`, and a non-null `credential`. Username uniqueness still applies.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `manager` or `credential` is missing in the update body.
  - Endpoint group:
    Step 1: `POST /api/employees`
    Step 2: `PUT /api/employees`
  - Failure endpoint:
    `PUT /api/employees`
  - Why this fails:
    The service dereferences those objects before saving.
  - Intentionally violated constraints:
    Omit `manager`, `manager.department`, or `credential` from Step 2.

Endpoint coverage:
- Covers:
  `PUT /api/employees`
  `PUT /api/employees/update`
- Distinct meaning:
  Update employee and nested credential state.

### Behavior 29: delete employee by id

Behavior name:
delete employee by id

Successful execution:
- Result:
  Deletes the employee identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /api/employees`
  Step 2: `DELETE /api/employees/{id}`
  Alternative Step 2: `DELETE /api/employees/delete/{id}`
- Constraints:
  `{id}` must be `employeeId` from Step 1. Existing assignments, credentials, or managed employees may affect database deletion behavior.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The employee id does not exist.
  - Endpoint group:
    Step 1: `DELETE /api/employees/{id}`
  - Failure endpoint:
    `DELETE /api/employees/{id}`
  - Why this fails:
    The service calls `findById` before delete.
  - Intentionally violated constraints:
    Omit employee creation.
- Branch 2:
  - Unsatisfied condition:
    `{id}` is not numeric.
  - Endpoint group:
    Step 1: `DELETE /api/employees/{id}`
  - Failure endpoint:
    `DELETE /api/employees/{id}`
  - Why this fails:
    The controller parses `{id}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric path value.

Endpoint coverage:
- Covers:
  `DELETE /api/employees/{id}`
  `DELETE /api/employees/delete/{id}`
- Distinct meaning:
  Delete one employee by numeric id.

### Behavior 30: delete employee by username

Behavior name:
delete employee by username

Successful execution:
- Result:
  Deletes the employee associated with `{username}`.
- Endpoint sequence:
  Step 1: `POST /api/employees`
  Step 2: `DELETE /api/employees/username/{username}`
- Constraints:
  `{username}` must match the nested credential username saved in Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The username does not exist.
  - Endpoint group:
    Step 1: `DELETE /api/employees/username/{username}`
  - Failure endpoint:
    `DELETE /api/employees/username/{username}`
  - Why this fails:
    Employee deletion by username delegates to credential lookup.
  - Intentionally violated constraints:
    Omit employee/credential creation or use a different username.

Endpoint coverage:
- Covers:
  `DELETE /api/employees/username/{username}`
- Distinct meaning:
  Delete employee through credential username.

### Behavior 31: list employees by department

Behavior name:
list employees by department

Successful execution:
- Result:
  Returns employees whose `department_id` equals `{departmentId}`.
- Endpoint sequence:
  Step 1: `POST /api/locations`
  Step 2: `POST /api/departments`
  Step 3: `GET /api/employees/data/department/{departmentId}`
- Constraints:
  Step 2 must use `location.locationId` from Step 1. `{departmentId}` in Step 3 should be `departmentId` from Step 2. Implementation does not verify the department exists; a missing department id can return an empty list.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{departmentId}` is not numeric.
  - Endpoint group:
    Step 1: `GET /api/employees/data/department/{departmentId}`
  - Failure endpoint:
    `GET /api/employees/data/department/{departmentId}`
  - Why this fails:
    The controller parses `{departmentId}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric path value.

Endpoint coverage:
- Covers:
  `GET /api/employees/data/department/{departmentId}`
- Distinct meaning:
  Filter employees by department id.

### Behavior 32: list projects assigned to employee

Behavior name:
list projects assigned to employee

Successful execution:
- Result:
  Returns project summary rows for projects assigned to `{employeeId}`.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Step 2: `POST /api/employees`
  Step 3: `POST /api/assignments`
  Step 4: `GET /api/employees/data/employee-project-data/{employeeId}`
- Constraints:
  Step 3 body must use `projectId` from Step 1 and `employeeId` from Step 2. `{employeeId}` in Step 4 must be the same employee id. Step 2 requires an existing manager as described for employee save.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{employeeId}` is not numeric.
  - Endpoint group:
    Step 1: `GET /api/employees/data/employee-project-data/{employeeId}`
  - Failure endpoint:
    `GET /api/employees/data/employee-project-data/{employeeId}`
  - Why this fails:
    The controller parses `{employeeId}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric path value.

Endpoint coverage:
- Covers:
  `GET /api/employees/data/employee-project-data/{employeeId}`
- Distinct meaning:
  Employee-facing assigned project list.

### Behavior 33: list projects managed by employee

Behavior name:
list projects managed by employee

Successful execution:
- Result:
  Returns project summary rows for projects assigned to employees managed by `{employeeId}`.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Step 2: `POST /api/employees`
  Step 3: `POST /api/assignments`
  Step 4: `GET /api/employees/data/manager-project-data/{employeeId}`
- Constraints:
  `{employeeId}` in Step 4 is a manager id. Step 2 must create or use a subordinate employee whose `manager.employeeId` equals that manager id, and Step 3 must assign that subordinate employee to the project. The API cannot bootstrap the first manager without existing employee data.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{employeeId}` is not numeric.
  - Endpoint group:
    Step 1: `GET /api/employees/data/manager-project-data/{employeeId}`
  - Failure endpoint:
    `GET /api/employees/data/manager-project-data/{employeeId}`
  - Why this fails:
    The controller parses `{employeeId}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric path value.

Endpoint coverage:
- Covers:
  `GET /api/employees/data/manager-project-data/{employeeId}`
- Distinct meaning:
  Manager-facing assigned project list.

### Behavior 34: list assignments

Behavior name:
list assignments

Successful execution:
- Result:
  Returns all stored assignment records.
- Endpoint sequence:
  Step 1: `GET /api/assignments`
  Alternative Step 1: `GET /api/assignments/`
- Constraints:
  No specific assignment is required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No implementation-specific request failure is visible.
  - Endpoint group:
    Step 1: `GET /api/assignments`
  - Failure endpoint:
    `GET /api/assignments`
  - Why this fails:
    Only unexpected repository/runtime errors are visible.
  - Intentionally violated constraints:
    None visible in allowed files.

Endpoint coverage:
- Covers:
  `GET /api/assignments`
  `GET /api/assignments/`
- Distinct meaning:
  Collection read for assignments.

### Behavior 35: save assignment

Behavior name:
save assignment

Successful execution:
- Result:
  Saves an assignment keyed by employee id, project id, and commit date.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Step 2: `POST /api/employees`
  Step 3: `POST /api/assignments`
  Alternative Step 3: `POST /api/assignments/save`
- Constraints:
  Step 3 body must use `projectId` from Step 1, `employeeId` from Step 2, and `commitDate` formatted as `dd-MM-yyyyHH:mm:ss`. The assignment table has foreign keys to projects and employees.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Employee or project id does not exist.
  - Endpoint group:
    Step 1: `POST /api/assignments`
  - Failure endpoint:
    `POST /api/assignments`
  - Why this fails:
    Assignment foreign keys require existing employee and project rows.
  - Intentionally violated constraints:
    Omit `POST /api/projects` or `POST /api/employees` and use nonexistent ids.
- Branch 2:
  - Unsatisfied condition:
    `commitDate` cannot be deserialized.
  - Endpoint group:
    Step 1: `POST /api/projects`
    Step 2: `POST /api/employees`
    Step 3: `POST /api/assignments`
  - Failure endpoint:
    `POST /api/assignments`
  - Why this fails:
    Assignment `commitDate` is a `LocalDateTime` using `dd-MM-yyyyHH:mm:ss`.
  - Intentionally violated constraints:
    Use a different date format.

Endpoint coverage:
- Covers:
  `POST /api/assignments`
  `POST /api/assignments/save`
- Distinct meaning:
  Save/upsert an assignment or commit record.

### Behavior 36: retrieve assignment by composite id

Behavior name:
retrieve assignment by composite id

Successful execution:
- Result:
  Retrieves the assignment matching `{employeeId}`, `{projectId}`, and `{commitDate}`.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Step 2: `POST /api/employees`
  Step 3: `POST /api/assignments`
  Step 4: `GET /api/assignments/{employeeId}/{projectId}/{commitDate}`
- Constraints:
  Step 3 must use ids from Steps 1 and 2. Step 4 path values must match Step 3 body values exactly. `{commitDate}` uses `dd-MM-yyyyHH:mm:ss`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The composite assignment id does not exist.
  - Endpoint group:
    Step 1: `GET /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Failure endpoint:
    `GET /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    `AssignmentService.findById` throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    Omit assignment creation or use mismatched path values.
- Branch 2:
  - Unsatisfied condition:
    `{commitDate}` has the wrong format.
  - Endpoint group:
    Step 1: `GET /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Failure endpoint:
    `GET /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    The controller parses the date with `dd-MM-yyyyHH:mm:ss`.
  - Intentionally violated constraints:
    Use a different date format.

Endpoint coverage:
- Covers:
  `GET /api/assignments/{employeeId}/{projectId}/{commitDate}`
- Distinct meaning:
  Read the raw assignment entity by composite key.

### Behavior 37: update assignment

Behavior name:
update assignment

Successful execution:
- Result:
  Saves replacement assignment values for an existing composite assignment key.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Step 2: `POST /api/employees`
  Step 3: `POST /api/assignments`
  Step 4: `PUT /api/assignments`
  Alternative Step 4: `PUT /api/assignments/update`
- Constraints:
  Step 4 body should use the same `employeeId`, `projectId`, and `commitDate` as Step 3 to update that assignment. The implementation delegates to `save`, so a new composite key with valid foreign keys may be inserted instead of rejected.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Employee or project id does not exist.
  - Endpoint group:
    Step 1: `PUT /api/assignments`
  - Failure endpoint:
    `PUT /api/assignments`
  - Why this fails:
    Assignment foreign keys require existing employee and project rows.
  - Intentionally violated constraints:
    Use nonexistent `employeeId` or `projectId`.

Endpoint coverage:
- Covers:
  `PUT /api/assignments`
  `PUT /api/assignments/update`
- Distinct meaning:
  Update or upsert an assignment record.

### Behavior 38: delete assignment

Behavior name:
delete assignment

Successful execution:
- Result:
  Deletes the assignment matching `{employeeId}`, `{projectId}`, and `{commitDate}`.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Step 2: `POST /api/employees`
  Step 3: `POST /api/assignments`
  Step 4: `DELETE /api/assignments/{employeeId}/{projectId}/{commitDate}`
  Alternative Step 4: `DELETE /api/assignments/delete/{employeeId}/{projectId}/{commitDate}`
- Constraints:
  Step 4 path values must match the composite key saved in Step 3. `{commitDate}` uses `dd-MM-yyyyHH:mm:ss`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The composite assignment id does not exist.
  - Endpoint group:
    Step 1: `DELETE /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Failure endpoint:
    `DELETE /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    Delete calls `findById` first, which throws `ObjectNotFoundException`.
  - Intentionally violated constraints:
    Omit assignment creation or use mismatched path values.
- Branch 2:
  - Unsatisfied condition:
    `{commitDate}` has the wrong format.
  - Endpoint group:
    Step 1: `DELETE /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Failure endpoint:
    `DELETE /api/assignments/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    The controller parses the date with `dd-MM-yyyyHH:mm:ss`.
  - Intentionally violated constraints:
    Use a different date format.

Endpoint coverage:
- Covers:
  `DELETE /api/assignments/{employeeId}/{projectId}/{commitDate}`
  `DELETE /api/assignments/delete/{employeeId}/{projectId}/{commitDate}`
- Distinct meaning:
  Delete one assignment by composite key.

### Behavior 39: list project commits for project

Behavior name:
list project commits for project

Successful execution:
- Result:
  Returns commit projection rows for `{projectId}`, excluding rows where employee description is null and manager description is `init`.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Step 2: `POST /api/employees`
  Step 3: `POST /api/assignments`
  Step 4: `GET /api/assignments/data/project-commit/{projectId}`
- Constraints:
  Step 3 must use `projectId` from Step 1 and `employeeId` from Step 2. Step 4 must reuse Step 1’s `projectId`. To appear in the result, the assignment must have `commitEmpDesc` not null or `commitMgrDesc` not equal to `init` case-insensitively.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{projectId}` is not numeric.
  - Endpoint group:
    Step 1: `GET /api/assignments/data/project-commit/{projectId}`
  - Failure endpoint:
    `GET /api/assignments/data/project-commit/{projectId}`
  - Why this fails:
    The controller parses `{projectId}` as an integer.
  - Intentionally violated constraints:
    Use a non-numeric path value.

Endpoint coverage:
- Covers:
  `GET /api/assignments/data/project-commit/{projectId}`
- Distinct meaning:
  Project-level commit report.

### Behavior 40: list project commits for employee and project

Behavior name:
list project commits for employee and project

Successful execution:
- Result:
  Returns commit projection rows for one employee on one project, excluding initial-only rows.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Step 2: `POST /api/employees`
  Step 3: `POST /api/assignments`
  Step 4: `GET /api/assignments/data/project-commit/{employeeId}/{projectId}`
- Constraints:
  Step 4 must reuse `employeeId` from Step 2 and `projectId` from Step 1. Step 3 must create the matching assignment. To appear in the result, the assignment must have `commitEmpDesc` not null or `commitMgrDesc` not equal to `init`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{employeeId}` or `{projectId}` is not numeric.
  - Endpoint group:
    Step 1: `GET /api/assignments/data/project-commit/{employeeId}/{projectId}`
  - Failure endpoint:
    `GET /api/assignments/data/project-commit/{employeeId}/{projectId}`
  - Why this fails:
    The controller parses both values as integers.
  - Intentionally violated constraints:
    Use a non-numeric employee or project id.

Endpoint coverage:
- Covers:
  `GET /api/assignments/data/project-commit/{employeeId}/{projectId}`
- Distinct meaning:
  Employee-and-project commit report.

### Behavior 41: retrieve one projected project commit

Behavior name:
retrieve one projected project commit

Successful execution:
- Result:
  Returns a projected commit row for one employee, project, and commit date.
- Endpoint sequence:
  Step 1: `POST /api/projects`
  Step 2: `POST /api/employees`
  Step 3: `POST /api/assignments`
  Step 4: `GET /api/assignments/data/project-commit/{employeeId}/{projectId}/{commitDate}`
- Constraints:
  Step 4 path values must reuse `employeeId`, `projectId`, and `commitDate` from Step 3. `{commitDate}` uses `dd-MM-yyyyHH:mm:ss`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No projected commit exists for the composite values.
  - Endpoint group:
    Step 1: `GET /api/assignments/data/project-commit/{employeeId}/{projectId}/{commitDate}`
  - Failure endpoint:
    `GET /api/assignments/data/project-commit/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    Repository lookup returns empty and the service throws `NoSuchElementException`, handled as `400 BAD_REQUEST`.
  - Intentionally violated constraints:
    Omit assignment creation or use mismatched path values.
- Branch 2:
  - Unsatisfied condition:
    `{commitDate}` has the wrong format.
  - Endpoint group:
    Step 1: `GET /api/assignments/data/project-commit/{employeeId}/{projectId}/{commitDate}`
  - Failure endpoint:
    `GET /api/assignments/data/project-commit/{employeeId}/{projectId}/{commitDate}`
  - Why this fails:
    The controller parses the date with `dd-MM-yyyyHH:mm:ss`.
  - Intentionally violated constraints:
    Use a different date format.

Endpoint coverage:
- Covers:
  `GET /api/assignments/data/project-commit/{employeeId}/{projectId}/{commitDate}`
- Distinct meaning:
  Read one commit report projection, not the raw assignment entity.

### Unclear or auxiliary endpoints

`GET /api/assignments/{employeeId}/{projectId}` is documented in Swagger as `findAllByEmployeeIdAndManagerId`, but the implementation returns `new ResponseEntity<>(null, null)` and contains a TODO saying the method still needs to be created. It does not call a service method and does not implement a reliable user-visible behavior.