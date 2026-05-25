Analysis basis: `src` contains shared constants and utilities only, with no REST controller implementation. The OpenAPI file defines the endpoint contract, and `src` confirms the `/quartz-manager` base path, `/auth/login`, and bearer security scheme name. The OpenAPI references DTO schemas that are not present under `components.schemas`, so body field-level rules cannot be inferred from the allowed source set. No supported function is listed for `/quartz-manager/auth/logout` or `/quartz-manager-ui` because they appear only as constants and have no matching OpenAPI operation or controller implementation in the current directory.

### Function 1: authenticate user

Function name:
authenticate user

Core endpoint(s):
- `POST /quartz-manager/auth/login`

Preconditions:
- Credential data for the submitted `{username}` and `{password}` is valid in the authentication backing store. This can be satisfied by configuring or directly inserting the corresponding user credentials if the deployment exposes that store, or by using known valid form values with `POST /quartz-manager/auth/login`.

Successful execution:
- Result:
  The endpoint authenticates the caller and is intended to return a JWT bearer token for later secured API calls. The security scheme says the login API provides the JWT, but the login operation does not document a successful response code or response body.
- Invocation:
  Step 1: `POST /quartz-manager/auth/login` with `application/x-www-form-urlencoded` fields `username={username}` and `password={password}`.
- Constraints:
  `username` and `password` are required form fields. The token value returned by this endpoint, although not modeled in the operation response, must be reused as `Authorization: Bearer {token}` for secured endpoints.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The submitted credential pair is invalid, missing from the authentication backing store, or does not match the stored password. This can be produced by submitting incorrect form fields to `POST /quartz-manager/auth/login` or by intentionally not configuring matching credentials in the backing store.
  - Failure endpoint:
    `POST /quartz-manager/auth/login`
  - Why this fails:
    The OpenAPI documents `401 Unauthorized - Username or password are incorrect!`.
  - Intentionally violated constraints:
    The required valid username/password relationship is violated.

Endpoint coverage:
- Covers:
  `POST /quartz-manager/auth/login`
- Distinct meaning:
  Authenticates a caller and obtains the JWT required by the secured API.

### Function 2: list eligible job classes

Function name:
list eligible job classes

Core endpoint(s):
- `GET /quartz-manager/jobs`

Preconditions:
- A valid authenticated caller context exists for the `quartz-manager-auth` bearer scheme. This can be satisfied by providing a pre-issued JWT accepted by the application or by calling `POST /quartz-manager/auth/login` with form fields `username={username}` and `password={password}`; the returned token value must be reused in the `Authorization: Bearer {token}` header. If authentication data is backed by a database in the deployment, the same state can also be satisfied by directly configuring the user credentials, but no such persistence schema is visible in the current `src`.

Successful execution:
- Result:
  The endpoint returns the list of Java job classes eligible for Quartz Manager.
- Invocation:
  Step 1: `GET /quartz-manager/jobs` with `Authorization: Bearer {token}`.
- Constraints:
  A valid bearer token is required. No resource-specific prerequisite or request parameter is documented.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated caller context is available. This can be produced by intentionally not calling `POST /quartz-manager/auth/login`, by not reusing its token value, or by sending a missing, malformed, expired, or otherwise invalid `Authorization` header.
  - Failure endpoint:
    `GET /quartz-manager/jobs`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    The required bearer-token authentication state is omitted or invalid.
- Branch 2:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - A generic not-found condition exists for the jobs endpoint. The OpenAPI declares `404 Not Found`, but the allowed implementation source does not expose a user-controlled resource state or setup endpoint that produces this condition.
  - Failure endpoint:
    `GET /quartz-manager/jobs`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the current `src` contains no controller logic that reveals the runtime condition.
  - Intentionally violated constraints:
    No concrete request, resource, or value constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/jobs`
- Distinct meaning:
  Lists available Quartz job classes.

### Function 3: retrieve scheduler details

Function name:
retrieve scheduler details

Core endpoint(s):
- `GET /quartz-manager/scheduler`

Preconditions:
- A valid authenticated caller context exists for the `quartz-manager-auth` bearer scheme. This can be satisfied by providing a pre-issued JWT accepted by the application or by calling `POST /quartz-manager/auth/login` with form fields `username={username}` and `password={password}`; the returned token value must be reused in the `Authorization: Bearer {token}` header. If authentication data is backed by a database in the deployment, the same state can also be satisfied by directly configuring the user credentials, but no such persistence schema is visible in the current `src`.

Successful execution:
- Result:
  The endpoint returns the scheduler configuration/details.
- Invocation:
  Step 1: `GET /quartz-manager/scheduler` with `Authorization: Bearer {token}`.
- Constraints:
  A valid bearer token is required. No setup endpoint is documented for the scheduler resource itself.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated caller context is available. This can be produced by intentionally not calling `POST /quartz-manager/auth/login`, by not reusing its token value, or by sending a missing, malformed, expired, or otherwise invalid `Authorization` header.
  - Failure endpoint:
    `GET /quartz-manager/scheduler`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    The required bearer-token authentication state is omitted or invalid.
- Branch 2:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - A generic not-found condition exists for the scheduler endpoint. The OpenAPI declares `404 Not Found`, but the allowed implementation source does not expose a user-controlled scheduler state or setup endpoint that produces this condition.
  - Failure endpoint:
    `GET /quartz-manager/scheduler`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but no implementation condition is visible in the allowed `src`.
  - Intentionally violated constraints:
    No concrete request, resource, or value constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/scheduler`
- Distinct meaning:
  Reads scheduler details/configuration.

### Function 4: start scheduler

Function name:
start scheduler

Core endpoint(s):
- `GET /quartz-manager/scheduler/run`

Preconditions:
- A valid authenticated caller context exists for the `quartz-manager-auth` bearer scheme. This can be satisfied by providing a pre-issued JWT accepted by the application or by calling `POST /quartz-manager/auth/login` with form fields `username={username}` and `password={password}`; the returned token value must be reused in the `Authorization: Bearer {token}` header. If authentication data is backed by a database in the deployment, the same state can also be satisfied by directly configuring the user credentials, but no such persistence schema is visible in the current `src`.

Successful execution:
- Result:
  The endpoint starts the scheduler.
- Invocation:
  Step 1: `GET /quartz-manager/scheduler/run` with `Authorization: Bearer {token}`.
- Constraints:
  A valid bearer token is required. No prior stopped state is documented as required.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated caller context is available. This can be produced by intentionally not calling `POST /quartz-manager/auth/login`, by not reusing its token value, or by sending a missing, malformed, expired, or otherwise invalid `Authorization` header.
  - Failure endpoint:
    `GET /quartz-manager/scheduler/run`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    The required bearer-token authentication state is omitted or invalid.
- Branch 2:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - A generic not-found condition exists for the scheduler start endpoint. The OpenAPI declares `404 Not Found`, but the allowed implementation source does not expose a user-controlled scheduler state or setup endpoint that produces this condition.
  - Failure endpoint:
    `GET /quartz-manager/scheduler/run`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` does not expose the condition.
  - Intentionally violated constraints:
    No concrete request, resource, or value constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/scheduler/run`
- Distinct meaning:
  Starts scheduler execution.

### Function 5: stop scheduler

Function name:
stop scheduler

Core endpoint(s):
- `GET /quartz-manager/scheduler/stop`

Preconditions:
- A valid authenticated caller context exists for the `quartz-manager-auth` bearer scheme. This can be satisfied by providing a pre-issued JWT accepted by the application or by calling `POST /quartz-manager/auth/login` with form fields `username={username}` and `password={password}`; the returned token value must be reused in the `Authorization: Bearer {token}` header. If authentication data is backed by a database in the deployment, the same state can also be satisfied by directly configuring the user credentials, but no such persistence schema is visible in the current `src`.

Successful execution:
- Result:
  The endpoint stops the scheduler.
- Invocation:
  Step 1: `GET /quartz-manager/scheduler/stop` with `Authorization: Bearer {token}`.
- Constraints:
  A valid bearer token is required. No prior running state is documented as required.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated caller context is available. This can be produced by intentionally not calling `POST /quartz-manager/auth/login`, by not reusing its token value, or by sending a missing, malformed, expired, or otherwise invalid `Authorization` header.
  - Failure endpoint:
    `GET /quartz-manager/scheduler/stop`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    The required bearer-token authentication state is omitted or invalid.
- Branch 2:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - A generic not-found condition exists for the scheduler stop endpoint. The OpenAPI declares `404 Not Found`, but the allowed implementation source does not expose a user-controlled scheduler state or setup endpoint that produces this condition.
  - Failure endpoint:
    `GET /quartz-manager/scheduler/stop`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` does not expose the condition.
  - Intentionally violated constraints:
    No concrete request, resource, or value constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/scheduler/stop`
- Distinct meaning:
  Stops scheduler execution.

### Function 6: pause scheduler

Function name:
pause scheduler

Core endpoint(s):
- `GET /quartz-manager/scheduler/pause`

Preconditions:
- A valid authenticated caller context exists for the `quartz-manager-auth` bearer scheme. This can be satisfied by providing a pre-issued JWT accepted by the application or by calling `POST /quartz-manager/auth/login` with form fields `username={username}` and `password={password}`; the returned token value must be reused in the `Authorization: Bearer {token}` header. If authentication data is backed by a database in the deployment, the same state can also be satisfied by directly configuring the user credentials, but no such persistence schema is visible in the current `src`.

Successful execution:
- Result:
  The endpoint pauses the scheduler.
- Invocation:
  Step 1: `GET /quartz-manager/scheduler/pause` with `Authorization: Bearer {token}`.
- Constraints:
  A valid bearer token is required. No additional scheduler state prerequisite is documented.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated caller context is available. This can be produced by intentionally not calling `POST /quartz-manager/auth/login`, by not reusing its token value, or by sending a missing, malformed, expired, or otherwise invalid `Authorization` header.
  - Failure endpoint:
    `GET /quartz-manager/scheduler/pause`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    The required bearer-token authentication state is omitted or invalid.
- Branch 2:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - A generic not-found condition exists for the scheduler pause endpoint. The OpenAPI declares `404 Not Found`, but the allowed implementation source does not expose a user-controlled scheduler state or setup endpoint that produces this condition.
  - Failure endpoint:
    `GET /quartz-manager/scheduler/pause`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` does not expose the condition.
  - Intentionally violated constraints:
    No concrete request, resource, or value constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/scheduler/pause`
- Distinct meaning:
  Pauses scheduler execution.

### Function 7: resume scheduler

Function name:
resume scheduler

Core endpoint(s):
- `GET /quartz-manager/scheduler/resume`

Preconditions:
- A valid authenticated caller context exists for the `quartz-manager-auth` bearer scheme. This can be satisfied by providing a pre-issued JWT accepted by the application or by calling `POST /quartz-manager/auth/login` with form fields `username={username}` and `password={password}`; the returned token value must be reused in the `Authorization: Bearer {token}` header. If authentication data is backed by a database in the deployment, the same state can also be satisfied by directly configuring the user credentials, but no such persistence schema is visible in the current `src`.

Successful execution:
- Result:
  The endpoint resumes the scheduler.
- Invocation:
  Step 1: `GET /quartz-manager/scheduler/resume` with `Authorization: Bearer {token}`.
- Constraints:
  A valid bearer token is required. The OpenAPI does not state that `GET /quartz-manager/scheduler/pause` must be called first.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated caller context is available. This can be produced by intentionally not calling `POST /quartz-manager/auth/login`, by not reusing its token value, or by sending a missing, malformed, expired, or otherwise invalid `Authorization` header.
  - Failure endpoint:
    `GET /quartz-manager/scheduler/resume`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    The required bearer-token authentication state is omitted or invalid.
- Branch 2:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - A generic not-found condition exists for the scheduler resume endpoint. The OpenAPI declares `404 Not Found`, but the allowed implementation source does not expose a user-controlled scheduler state or setup endpoint that produces this condition.
  - Failure endpoint:
    `GET /quartz-manager/scheduler/resume`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` does not expose the condition.
  - Intentionally violated constraints:
    No concrete request, resource, or value constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/scheduler/resume`
- Distinct meaning:
  Resumes scheduler execution.

### Function 8: list triggers

Function name:
list triggers

Core endpoint(s):
- `GET /quartz-manager/triggers`

Preconditions:
- A valid authenticated caller context exists for the `quartz-manager-auth` bearer scheme. This can be satisfied by providing a pre-issued JWT accepted by the application or by calling `POST /quartz-manager/auth/login` with form fields `username={username}` and `password={password}`; the returned token value must be reused in the `Authorization: Bearer {token}` header. If authentication data is backed by a database in the deployment, the same state can also be satisfied by directly configuring the user credentials, but no such persistence schema is visible in the current `src`.

Successful execution:
- Result:
  The endpoint returns the global list of trigger keys/triggers.
- Invocation:
  Step 1: `GET /quartz-manager/triggers` with `Authorization: Bearer {token}`.
- Constraints:
  A valid bearer token is required. This is a global collection list, so no specific trigger must be created first.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated caller context is available. This can be produced by intentionally not calling `POST /quartz-manager/auth/login`, by not reusing its token value, or by sending a missing, malformed, expired, or otherwise invalid `Authorization` header.
  - Failure endpoint:
    `GET /quartz-manager/triggers`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    The required bearer-token authentication state is omitted or invalid.
- Branch 2:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - A generic not-found condition exists for the trigger list endpoint. The OpenAPI declares `404 Not Found`, but the allowed implementation source does not expose a user-controlled trigger state or setup endpoint that produces this condition.
  - Failure endpoint:
    `GET /quartz-manager/triggers`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` contains no controller logic that reveals a user-controlled way to trigger it.
  - Intentionally violated constraints:
    No concrete request, resource, or value constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/triggers`
- Distinct meaning:
  Lists trigger keys/triggers globally.

### Function 9: schedule simple trigger

Function name:
schedule simple trigger

Core endpoint(s):
- `POST /quartz-manager/simple-triggers/{name}`

Preconditions:
- A valid authenticated caller context exists for the `quartz-manager-auth` bearer scheme. This can be satisfied by providing a pre-issued JWT accepted by the application or by calling `POST /quartz-manager/auth/login` with form fields `username={username}` and `password={password}`; the returned token value must be reused in the `Authorization: Bearer {token}` header. If authentication data is backed by a database in the deployment, the same state can also be satisfied by directly configuring the user credentials, but no such persistence schema is visible in the current `src`.

Successful execution:
- Result:
  The endpoint schedules a new simple trigger named `{name}`.
- Invocation:
  Step 1: `POST /quartz-manager/simple-triggers/{name}` with `Authorization: Bearer {token}`, path parameter `name={name}`, `Content-Type: application/json`, and a JSON request body matching `SimpleTriggerInputDTO`.
- Constraints:
  `{name}` is the required path parameter identifying the trigger to schedule. The request body must be valid for `SimpleTriggerInputDTO`, but the OpenAPI file does not define that schema's fields.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated caller context is available. This can be produced by intentionally not calling `POST /quartz-manager/auth/login`, by not reusing its token value, or by sending a missing, malformed, expired, or otherwise invalid `Authorization` header.
  - Failure endpoint:
    `POST /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    The required bearer-token authentication state is omitted or invalid.
- Branch 2:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - The submitted simple-trigger configuration is invalid. This is produced by sending a JSON body to `POST /quartz-manager/simple-triggers/{name}` that does not satisfy the undocumented `SimpleTriggerInputDTO` validation rules; no direct database setup is needed because the invalid state is in the request body.
  - Failure endpoint:
    `POST /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The OpenAPI declares `400 Invalid trigger configuration`.
  - Intentionally violated constraints:
    The JSON body violates the validation rules for `SimpleTriggerInputDTO`.
- Branch 3:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - A generic not-found condition exists for the simple-trigger scheduling endpoint. The OpenAPI declares `404 Not Found`, but the allowed implementation source contains no controller logic or DTO schema showing which dependency is missing.
  - Failure endpoint:
    `POST /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The OpenAPI declares a `404 Not Found` response, but the allowed `src` contains no controller logic or DTO schema showing which dependency is missing.
  - Intentionally violated constraints:
    No concrete request, resource, or value constraint is visible in the allowed files.

Endpoint coverage:
- Covers:
  `POST /quartz-manager/simple-triggers/{name}`
- Distinct meaning:
  Creates/schedules a new simple trigger.

### Function 10: retrieve simple trigger by name

Function name:
retrieve simple trigger by name

Core endpoint(s):
- `GET /quartz-manager/simple-triggers/{name}`

Preconditions:
- A valid authenticated caller context exists for the `quartz-manager-auth` bearer scheme. This can be satisfied by providing a pre-issued JWT accepted by the application or by calling `POST /quartz-manager/auth/login` with form fields `username={username}` and `password={password}`; the returned token value must be reused in the `Authorization: Bearer {token}` header. If authentication data is backed by a database in the deployment, the same state can also be satisfied by directly configuring the user credentials, but no such persistence schema is visible in the current `src`.
- A simple trigger named `{name}` exists in the scheduler store. This can be satisfied by directly inserting/configuring the trigger in the Quartz scheduler store if the deployment exposes it, or by calling `POST /quartz-manager/simple-triggers/{name}` with `Authorization: Bearer {token}`, path parameter `name={name}`, `Content-Type: application/json`, and a valid `SimpleTriggerInputDTO` JSON body.
- The `{name}` consumed by `GET /quartz-manager/simple-triggers/{name}` must identify the same simple trigger established in the scheduler store or created by `POST /quartz-manager/simple-triggers/{name}`.

Successful execution:
- Result:
  The endpoint retrieves the simple trigger named `{name}`.
- Invocation:
  Step 1: `GET /quartz-manager/simple-triggers/{name}` with `Authorization: Bearer {token}` and path parameter `name={name}`.
- Constraints:
  A valid bearer token is required. The `{name}` path value must identify an existing simple trigger.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated caller context is available. This can be produced by intentionally not calling `POST /quartz-manager/auth/login`, by not reusing its token value, or by sending a missing, malformed, expired, or otherwise invalid `Authorization` header.
  - Failure endpoint:
    `GET /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    The required bearer-token authentication state is omitted or invalid.
- Branch 2:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - No simple trigger named `{name}` exists in the scheduler store. This can be produced by starting from an empty scheduler store, deleting the trigger beforehand, or intentionally not inserting it directly and not calling `POST /quartz-manager/simple-triggers/{name}` for the same `{name}`.
  - Failure endpoint:
    `GET /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The OpenAPI explicitly documents `404 Trigger not found`.
  - Intentionally violated constraints:
    The required simple-trigger state for `{name}` is omitted.
- Branch 3:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - A simple trigger named `{createdName}` exists in the scheduler store. This can be satisfied by directly inserting/configuring that trigger or by calling `POST /quartz-manager/simple-triggers/{createdName}` with a valid `SimpleTriggerInputDTO` body.
    - `{otherName}` is different from `{createdName}` and does not identify the trigger created or configured under `{createdName}`.
  - Failure endpoint:
    `GET /quartz-manager/simple-triggers/{otherName}`
  - Why this fails:
    The established trigger state applies only to `{createdName}`, not `{otherName}`.
  - Intentionally violated constraints:
    The path value consumed by the GET endpoint does not match the trigger name established in the scheduler store.

Endpoint coverage:
- Covers:
  `GET /quartz-manager/simple-triggers/{name}`
- Distinct meaning:
  Reads an existing simple trigger by name.

### Function 11: reschedule simple trigger

Function name:
reschedule simple trigger

Core endpoint(s):
- `PUT /quartz-manager/simple-triggers/{name}`

Preconditions:
- A valid authenticated caller context exists for the `quartz-manager-auth` bearer scheme. This can be satisfied by providing a pre-issued JWT accepted by the application or by calling `POST /quartz-manager/auth/login` with form fields `username={username}` and `password={password}`; the returned token value must be reused in the `Authorization: Bearer {token}` header. If authentication data is backed by a database in the deployment, the same state can also be satisfied by directly configuring the user credentials, but no such persistence schema is visible in the current `src`.
- A simple trigger named `{name}` exists in the scheduler store. This can be satisfied by directly inserting/configuring the trigger in the Quartz scheduler store if the deployment exposes it, or by calling `POST /quartz-manager/simple-triggers/{name}` with `Authorization: Bearer {token}`, path parameter `name={name}`, `Content-Type: application/json`, and a valid initial `SimpleTriggerInputDTO` JSON body.
- The `{name}` consumed by `PUT /quartz-manager/simple-triggers/{name}` must identify the same simple trigger established in the scheduler store or created by `POST /quartz-manager/simple-triggers/{name}`.

Successful execution:
- Result:
  The endpoint reschedules the existing simple trigger named `{name}`.
- Invocation:
  Step 1: `PUT /quartz-manager/simple-triggers/{name}` with `Authorization: Bearer {token}`, path parameter `name={name}`, `Content-Type: application/json`, and a valid replacement `SimpleTriggerInputDTO` JSON body.
- Constraints:
  A valid bearer token is required. The `{name}` path value must identify an existing simple trigger. The replacement request body must be valid for `SimpleTriggerInputDTO`, but the schema fields are missing from the OpenAPI file.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No valid authenticated caller context is available. This can be produced by intentionally not calling `POST /quartz-manager/auth/login`, by not reusing its token value, or by sending a missing, malformed, expired, or otherwise invalid `Authorization` header.
  - Failure endpoint:
    `PUT /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The endpoint is protected by the `quartz-manager-auth` bearer security scheme.
  - Intentionally violated constraints:
    The required bearer-token authentication state is omitted or invalid.
- Branch 2:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - No simple trigger named `{name}` exists in the scheduler store. This can be produced by starting from an empty scheduler store, deleting the trigger beforehand, or intentionally not inserting it directly and not calling `POST /quartz-manager/simple-triggers/{name}` for the same `{name}`.
  - Failure endpoint:
    `PUT /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The operation is documented as rescheduling an existing simple trigger and declares `404 Not Found`.
  - Intentionally violated constraints:
    The required existing simple-trigger state for `{name}` is omitted.
- Branch 3:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - A simple trigger named `{name}` exists in the scheduler store. This can be satisfied by directly inserting/configuring the trigger or by calling `POST /quartz-manager/simple-triggers/{name}` with a valid initial `SimpleTriggerInputDTO` body.
    - The replacement simple-trigger configuration is invalid. This is produced by sending a JSON body to `PUT /quartz-manager/simple-triggers/{name}` that does not satisfy the undocumented `SimpleTriggerInputDTO` validation rules.
  - Failure endpoint:
    `PUT /quartz-manager/simple-triggers/{name}`
  - Why this fails:
    The OpenAPI declares `400 Invalid trigger configuration`.
  - Intentionally violated constraints:
    The replacement JSON body violates the validation rules for `SimpleTriggerInputDTO`.
- Branch 4:
  - Preconditions:
    - A valid authenticated caller context exists. This can be satisfied by direct credential setup plus a valid pre-issued JWT, or by calling `POST /quartz-manager/auth/login` and reusing the returned token in the `Authorization` header.
    - A simple trigger named `{createdName}` exists in the scheduler store. This can be satisfied by directly inserting/configuring that trigger or by calling `POST /quartz-manager/simple-triggers/{createdName}` with a valid `SimpleTriggerInputDTO` body.
    - `{otherName}` is different from `{createdName}` and does not identify the trigger created or configured under `{createdName}`.
  - Failure endpoint:
    `PUT /quartz-manager/simple-triggers/{otherName}`
  - Why this fails:
    The established trigger state applies only to `{createdName}`, not `{otherName}`.
  - Intentionally violated constraints:
    The path value consumed by the PUT endpoint does not match the trigger name established in the scheduler store.

Endpoint coverage:
- Covers:
  `PUT /quartz-manager/simple-triggers/{name}`
- Distinct meaning:
  Updates/reschedules an existing simple trigger.
