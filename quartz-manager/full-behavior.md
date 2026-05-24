Analysis basis: `src` contains shared constants/utilities only, with no REST controller implementation. The OpenAPI file defines the endpoint contract, and `src` confirms the `/quartz-manager` base path, `/auth/login`, and bearer auth scheme name. The OpenAPI references DTO schemas that are not present under `components.schemas`, so body field-level rules cannot be inferred.

### Behavior 1: authenticate user

Behavior name:
authenticate user

Successful execution:
- Result:
  This behavior authenticates a user with form credentials and is intended to return a JWT bearer token for later secured API calls. The security scheme says the login API provides the JWT, but the login operation does not document a successful response code or response body.
- Endpoint sequence:
  Step 1: `POST /quartz-manager/auth/login` with `application/x-www-form-urlencoded` fields `username` and `password`.
- Constraints:
  `username` and `password` are required form fields. The JWT returned by this endpoint, although not modeled in the operation response, must be reused as the bearer token for secured endpoints.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The submitted username or password is incorrect.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
  - Failure endpoint:
    `POST /quartz-manager/auth/login`
  - Why this fails:
    The OpenAPI documents `401 Unauthorized - Username or password are incorrect!`.
  - Intentionally violated constraints:
    The form credentials are invalid.

Endpoint coverage:
- Covers:
  `POST /quartz-manager/auth/login`
- Distinct meaning:
  Authenticates a caller and obtains the JWT required by the rest of the API.

### Behavior 2: list eligible job classes

Behavior name:
list eligible job classes

Successful execution:
- Result:
  This behavior returns the list of Java job classes eligible for Quartz Manager.
- Endpoint sequence:
  Step 1: `POST /quartz-manager/auth/login` to obtain a JWT bearer token.
  Step 2: `GET /quartz-manager/jobs` using the JWT from Step 1.
- Constraints:
  Step 2 requires a bearer token obtained from Step 1. No resource-specific prerequisite is documented.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The caller does not provide a valid JWT bearer token.
  - Endpoint group:
    Step 1: `GET /quartz-manager/jobs`
  - Failure endpoint:
    `GET /quartz-manager/jobs`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    `POST /quartz-manager/auth/login` is omitted or its JWT is not reused.
- Branch 2:
  - Unsatisfied condition:
    A generic not-found condition occurs.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `GET /quartz-manager/jobs`
  - Failure endpoint:
    `GET /quartz-manager/jobs`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` files contain no controller logic that reveals a user-controlled way to trigger it.
  - Intentionally violated constraints:
    No concrete request constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/jobs`
- Distinct meaning:
  Lists available Quartz job classes.

### Behavior 3: retrieve scheduler details

Behavior name:
retrieve scheduler details

Successful execution:
- Result:
  This behavior returns the scheduler configuration/details.
- Endpoint sequence:
  Step 1: `POST /quartz-manager/auth/login` to obtain a JWT bearer token.
  Step 2: `GET /quartz-manager/scheduler` using the JWT from Step 1.
- Constraints:
  Step 2 requires a bearer token obtained from Step 1. No setup endpoint is documented for the scheduler itself.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The caller does not provide a valid JWT bearer token.
  - Endpoint group:
    Step 1: `GET /quartz-manager/scheduler`
  - Failure endpoint:
    `GET /quartz-manager/scheduler`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    `POST /quartz-manager/auth/login` is omitted or its JWT is not reused.
- Branch 2:
  - Unsatisfied condition:
    A generic not-found condition occurs.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `GET /quartz-manager/scheduler`
  - Failure endpoint:
    `GET /quartz-manager/scheduler`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but no implementation condition is visible in the allowed `src`.
  - Intentionally violated constraints:
    No concrete request constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/scheduler`
- Distinct meaning:
  Reads scheduler details/configuration.

### Behavior 4: start scheduler

Behavior name:
start scheduler

Successful execution:
- Result:
  This behavior starts the scheduler.
- Endpoint sequence:
  Step 1: `POST /quartz-manager/auth/login` to obtain a JWT bearer token.
  Step 2: `GET /quartz-manager/scheduler/run` using the JWT from Step 1.
- Constraints:
  Step 2 requires a bearer token obtained from Step 1. No prior stopped state is documented as required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The caller does not provide a valid JWT bearer token.
  - Endpoint group:
    Step 1: `GET /quartz-manager/scheduler/run`
  - Failure endpoint:
    `GET /quartz-manager/scheduler/run`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    `POST /quartz-manager/auth/login` is omitted or its JWT is not reused.
- Branch 2:
  - Unsatisfied condition:
    A generic not-found condition occurs.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `GET /quartz-manager/scheduler/run`
  - Failure endpoint:
    `GET /quartz-manager/scheduler/run`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` does not expose the condition.
  - Intentionally violated constraints:
    No concrete request constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/scheduler/run`
- Distinct meaning:
  Starts scheduler execution.

### Behavior 5: stop scheduler

Behavior name:
stop scheduler

Successful execution:
- Result:
  This behavior stops the scheduler.
- Endpoint sequence:
  Step 1: `POST /quartz-manager/auth/login` to obtain a JWT bearer token.
  Step 2: `GET /quartz-manager/scheduler/stop` using the JWT from Step 1.
- Constraints:
  Step 2 requires a bearer token obtained from Step 1. No prior running state is documented as required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The caller does not provide a valid JWT bearer token.
  - Endpoint group:
    Step 1: `GET /quartz-manager/scheduler/stop`
  - Failure endpoint:
    `GET /quartz-manager/scheduler/stop`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    `POST /quartz-manager/auth/login` is omitted or its JWT is not reused.
- Branch 2:
  - Unsatisfied condition:
    A generic not-found condition occurs.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `GET /quartz-manager/scheduler/stop`
  - Failure endpoint:
    `GET /quartz-manager/scheduler/stop`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` does not expose the condition.
  - Intentionally violated constraints:
    No concrete request constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/scheduler/stop`
- Distinct meaning:
  Stops scheduler execution.

### Behavior 6: pause scheduler

Behavior name:
pause scheduler

Successful execution:
- Result:
  This behavior pauses the scheduler.
- Endpoint sequence:
  Step 1: `POST /quartz-manager/auth/login` to obtain a JWT bearer token.
  Step 2: `GET /quartz-manager/scheduler/pause` using the JWT from Step 1.
- Constraints:
  Step 2 requires a bearer token obtained from Step 1. No additional scheduler state prerequisite is documented.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The caller does not provide a valid JWT bearer token.
  - Endpoint group:
    Step 1: `GET /quartz-manager/scheduler/pause`
  - Failure endpoint:
    `GET /quartz-manager/scheduler/pause`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    `POST /quartz-manager/auth/login` is omitted or its JWT is not reused.
- Branch 2:
  - Unsatisfied condition:
    A generic not-found condition occurs.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `GET /quartz-manager/scheduler/pause`
  - Failure endpoint:
    `GET /quartz-manager/scheduler/pause`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` does not expose the condition.
  - Intentionally violated constraints:
    No concrete request constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/scheduler/pause`
- Distinct meaning:
  Pauses scheduler execution.

### Behavior 7: resume scheduler

Behavior name:
resume scheduler

Successful execution:
- Result:
  This behavior resumes the scheduler.
- Endpoint sequence:
  Step 1: `POST /quartz-manager/auth/login` to obtain a JWT bearer token.
  Step 2: `GET /quartz-manager/scheduler/resume` using the JWT from Step 1.
- Constraints:
  Step 2 requires a bearer token obtained from Step 1. The OpenAPI does not state that `GET /quartz-manager/scheduler/pause` must be called first.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The caller does not provide a valid JWT bearer token.
  - Endpoint group:
    Step 1: `GET /quartz-manager/scheduler/resume`
  - Failure endpoint:
    `GET /quartz-manager/scheduler/resume`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    `POST /quartz-manager/auth/login` is omitted or its JWT is not reused.
- Branch 2:
  - Unsatisfied condition:
    A generic not-found condition occurs.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `GET /quartz-manager/scheduler/resume`
  - Failure endpoint:
    `GET /quartz-manager/scheduler/resume`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` does not expose the condition.
  - Intentionally violated constraints:
    No concrete request constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/scheduler/resume`
- Distinct meaning:
  Resumes scheduler execution.

### Behavior 8: list triggers

Behavior name:
list triggers

Successful execution:
- Result:
  This behavior returns the list of triggers.
- Endpoint sequence:
  Step 1: `POST /quartz-manager/auth/login` to obtain a JWT bearer token.
  Step 2: `GET /quartz-manager/triggers` using the JWT from Step 1.
- Constraints:
  Step 2 requires a bearer token obtained from Step 1. This is a global collection list, so no specific trigger must be created first.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The caller does not provide a valid JWT bearer token.
  - Endpoint group:
    Step 1: `GET /quartz-manager/triggers`
  - Failure endpoint:
    `GET /quartz-manager/triggers`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    `POST /quartz-manager/auth/login` is omitted or its JWT is not reused.
- Branch 2:
  - Unsatisfied condition:
    A generic not-found condition occurs.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `GET /quartz-manager/triggers`
  - Failure endpoint:
    `GET /quartz-manager/triggers`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` does not expose the condition.
  - Intentionally violated constraints:
    No concrete request constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/triggers`
- Distinct meaning:
  Lists trigger keys/triggers globally.

### Behavior 9: schedule simple trigger

Behavior name:
schedule simple trigger

Successful execution:
- Result:
  This behavior schedules a new simple trigger named `{name}`.
- Endpoint sequence:
  Step 1: `POST /quartz-manager/auth/login` to obtain a JWT bearer token.
  Step 2: `POST /quartz-manager/simple-triggers/{name}` using the JWT from Step 1 and a JSON request body matching `SimpleTriggerInputDTO`.
- Constraints:
  `{name}` is the required path parameter identifying the trigger to schedule. The request body must be valid for `SimpleTriggerInputDTO`, but the OpenAPI file does not define that schema’s fields.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The caller does not provide a valid JWT bearer token.
  - Endpoint group:
    Step 1: `POST /quartz-manager/simple-triggers/{name}`
  - Failure endpoint:
    `POST /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    `POST /quartz-manager/auth/login` is omitted or its JWT is not reused.
- Branch 2:
  - Unsatisfied condition:
    The trigger configuration is invalid.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `POST /quartz-manager/simple-triggers/{name}`
  - Failure endpoint:
    `POST /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The OpenAPI declares `400 Invalid trigger configuration`.
  - Intentionally violated constraints:
    The JSON body does not satisfy the undocumented `SimpleTriggerInputDTO` validation rules.
- Branch 3:
  - Unsatisfied condition:
    A generic not-found condition occurs.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `POST /quartz-manager/simple-triggers/{name}`
  - Failure endpoint:
    `POST /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` contains no controller logic or DTO schema showing which dependency is missing.
  - Intentionally violated constraints:
    No concrete request constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `POST /quartz-manager/simple-triggers/{name}`
- Distinct meaning:
  Creates/schedules a new simple trigger.

### Behavior 10: retrieve simple trigger by name

Behavior name:
retrieve simple trigger by name

Successful execution:
- Result:
  This behavior retrieves the simple trigger named `{name}`.
- Endpoint sequence:
  Step 1: `POST /quartz-manager/auth/login` to obtain a JWT bearer token.
  Step 2: `POST /quartz-manager/simple-triggers/{name}` using the JWT from Step 1 and a valid `SimpleTriggerInputDTO` body.
  Step 3: `GET /quartz-manager/simple-triggers/{name}` using the JWT from Step 1.
- Constraints:
  The `{name}` path value in Step 3 must match the `{name}` path value used in Step 2. Step 2 creates the trigger state required by the resource-specific read in Step 3.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The caller does not provide a valid JWT bearer token.
  - Endpoint group:
    Step 1: `GET /quartz-manager/simple-triggers/{name}`
  - Failure endpoint:
    `GET /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    `POST /quartz-manager/auth/login` is omitted or its JWT is not reused.
- Branch 2:
  - Unsatisfied condition:
    The requested trigger does not exist.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `GET /quartz-manager/simple-triggers/{name}`
  - Failure endpoint:
    `GET /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The OpenAPI explicitly documents `404 Trigger not found`.
  - Intentionally violated constraints:
    The prerequisite `POST /quartz-manager/simple-triggers/{name}` for the same `{name}` is intentionally omitted.
- Branch 3:
  - Unsatisfied condition:
    The caller creates one trigger but reads a different trigger name.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `POST /quartz-manager/simple-triggers/{createdName}`
    Step 3: `GET /quartz-manager/simple-triggers/{otherName}`
  - Failure endpoint:
    `GET /quartz-manager/simple-triggers/{otherName}`
  - Why this fails:
    Step 2 establishes trigger state only for `{createdName}`, not `{otherName}`.
  - Intentionally violated constraints:
    The path value consumed by the GET endpoint does not match the trigger name established by the POST endpoint.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/simple-triggers/{name}`
- Distinct meaning:
  Reads an existing simple trigger by name.

### Behavior 11: reschedule simple trigger

Behavior name:
reschedule simple trigger

Successful execution:
- Result:
  This behavior reschedules the existing simple trigger named `{name}`.
- Endpoint sequence:
  Step 1: `POST /quartz-manager/auth/login` to obtain a JWT bearer token.
  Step 2: `POST /quartz-manager/simple-triggers/{name}` using the JWT from Step 1 and a valid `SimpleTriggerInputDTO` body.
  Step 3: `PUT /quartz-manager/simple-triggers/{name}` using the JWT from Step 1 and a valid replacement `SimpleTriggerInputDTO` body.
- Constraints:
  The `{name}` path value in Step 3 must match the `{name}` path value used in Step 2. Step 2 creates the trigger state required by the reschedule operation. The Step 3 request body must be valid for `SimpleTriggerInputDTO`, but the schema fields are missing from the OpenAPI file.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The caller does not provide a valid JWT bearer token.
  - Endpoint group:
    Step 1: `PUT /quartz-manager/simple-triggers/{name}`
  - Failure endpoint:
    `PUT /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    `POST /quartz-manager/auth/login` is omitted or its JWT is not reused.
- Branch 2:
  - Unsatisfied condition:
    The trigger to reschedule does not exist.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `PUT /quartz-manager/simple-triggers/{name}`
  - Failure endpoint:
    `PUT /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The operation is documented as rescheduling an existing simple trigger and declares `404 Not Found`.
  - Intentionally violated constraints:
    The prerequisite `POST /quartz-manager/simple-triggers/{name}` for the same `{name}` is intentionally omitted.
- Branch 3:
  - Unsatisfied condition:
    The replacement trigger configuration is invalid.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `POST /quartz-manager/simple-triggers/{name}`
    Step 3: `PUT /quartz-manager/simple-triggers/{name}`
  - Failure endpoint:
    `PUT /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The OpenAPI declares `400 Invalid trigger configuration`.
  - Intentionally violated constraints:
    Step 3 sends a JSON body that does not satisfy the undocumented `SimpleTriggerInputDTO` validation rules.
- Branch 4:
  - Unsatisfied condition:
    The caller creates one trigger but reschedules a different trigger name.
  - Endpoint group:
    Step 1: `POST /quartz-manager/auth/login`
    Step 2: `POST /quartz-manager/simple-triggers/{createdName}`
    Step 3: `PUT /quartz-manager/simple-triggers/{otherName}`
  - Failure endpoint:
    `PUT /quartz-manager/simple-triggers/{otherName}`
  - Why this fails:
    Step 2 establishes trigger state only for `{createdName}`, not `{otherName}`.
  - Intentionally violated constraints:
    The path value consumed by the PUT endpoint does not match the trigger name established by the POST endpoint.

Endpoint coverage:
- Covers:
  `PUT /quartz-manager/simple-triggers/{name}`
- Distinct meaning:
  Updates/reschedules an existing simple trigger.

Unclear or auxiliary endpoints:
None among the OpenAPI paths. The source defines constants for `/quartz-manager/auth/logout` and `/quartz-manager-ui`, but no matching OpenAPI endpoint or controller implementation is present in the allowed inputs, so they are not listed as supported REST API behaviors.