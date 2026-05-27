# Domain-Level Behavior Analysis

## Domain Summary

Quartz Manager is a secured REST API for inspecting and controlling a Quartz scheduler. Its main domain resources are:

- authenticated API sessions represented by JWT bearer tokens
- the global Quartz scheduler and its execution state
- eligible Java job classes
- global trigger keys/triggers
- named simple triggers

The service is operational rather than tenant-scoped: the exposed scheduler and trigger APIs operate globally. The visible source under `src` only contains shared constants/utilities, so the OpenAPI contract, `full-behavior.md`, and generated tests provide most of the behavioral evidence. The OpenAPI references DTO schemas such as `SimpleTriggerInputDTO`, `SimpleTriggerDTO`, `TriggerDTO`, `TriggerKeyDTO`, `SchedulerDTO`, and `ExceptionResponse`, but those schemas are not defined under `components.schemas`.

## Available Function Inventory

### Authentication

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `authenticate user` | `POST /quartz-manager/auth/login` | Authenticates credentials and obtains a JWT bearer token for secured scheduler operations. |

### Jobs

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list eligible job classes` | `GET /quartz-manager/jobs` | Lists Java job classes eligible for use by Quartz Manager. |

### Scheduler

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `retrieve scheduler details` | `GET /quartz-manager/scheduler` | Reads scheduler configuration/status details. |
| `start scheduler` | `GET /quartz-manager/scheduler/run` | Starts scheduler execution. |
| `stop scheduler` | `GET /quartz-manager/scheduler/stop` | Stops scheduler execution. |
| `pause scheduler` | `GET /quartz-manager/scheduler/pause` | Pauses scheduler execution. |
| `resume scheduler` | `GET /quartz-manager/scheduler/resume` | Resumes scheduler execution. |

### Triggers

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list triggers` | `GET /quartz-manager/triggers` | Lists global scheduler triggers or trigger keys. |
| `schedule simple trigger` | `POST /quartz-manager/simple-triggers/{name}` | Creates/schedules a named simple trigger. |
| `retrieve simple trigger by name` | `GET /quartz-manager/simple-triggers/{name}` | Reads one named simple trigger. |
| `reschedule simple trigger` | `PUT /quartz-manager/simple-triggers/{name}` | Replaces/reschedules an existing named simple trigger. |

## Supported Business Behaviors

### Behavior 1: Obtain API Access

Business goal:
Authenticate a caller so protected Quartz Manager functions can be used.

Domain context:
All scheduler, job, and trigger functions require the `quartz-manager-auth` bearer security scheme. Login is the gateway behavior for the rest of the API.

Starting point:
Credential state exists in the authentication backing store. The API exposes no user-registration function.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with `Content-Type=application/x-www-form-urlencoded`, form `username={username}`, and form `password={password}` to obtain an access token.

Optional verification workflow:
None.

Existing-state shortcuts:
- If a valid JWT already exists, this login step can be skipped for later secured behaviors.
- Direct credential setup is possible only outside the exposed API, for example by deployment configuration or database setup.
- The token must still be accepted by the `quartz-manager-auth` bearer scheme.

Parameter and value bindings:
- The submitted `username` and `password` must match the backing credential store.
- The response field `accessToken` observed in generated tests is reused later as `Authorization: Bearer {token}`.
- OpenAPI says the login API provides the JWT, but the login operation does not document a successful response body.

Business result:
The caller has a bearer token that can authorize protected scheduler, job, and trigger calls.

Constraints and invariants:
- `username` and `password` are required form fields.
- There is no exposed user creation, user lookup, token refresh, or token revocation behavior.
- The token is global API authorization; no endpoint-level ownership scoping is visible.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: `username`/`password` is missing, invalid, or not configured.
  - Why it fails: OpenAPI documents `401 Unauthorized - Username or password are incorrect!`.
  - Violated prerequisite or constraint: The submitted credential pair does not match the authentication backing store.

Implementation notes:
Generated tests authenticate successfully with configured credentials such as `username=foo&password=bar` and extract `accessToken`, despite OpenAPI documenting only the 401 response.

### Behavior 2: Inspect Eligible Job Classes

Business goal:
Discover which Java job classes the scheduler can use.

Domain context:
Job classes represent executable work units. This API only exposes discovery, not job creation or job binding.

Starting point:
No prior scheduler/job/trigger state; valid credentials are configured.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with form `username={username}` and `password={password}` to obtain `{token}`.
2. Use function `list eligible job classes` (`GET /quartz-manager/jobs`) with header `Authorization=Bearer {token}` to list eligible job classes.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped if a valid `{token}` already exists.
- Direct classpath/configuration changes may alter the eligible job list, but no API function creates this state.

Parameter and value bindings:
- `{token}` returned by `authenticate user` is reused in the `Authorization` header.
- No job id, class name, pagination cursor, or filter value is accepted by `list eligible job classes`.

Business result:
The caller receives the current list of eligible job classes. Generated tests observed an empty JSON list.

Constraints and invariants:
- The endpoint is authenticated.
- The list is global, not user-owned.
- No search, filter, registration, or activation parameter is exposed.
- Extra query parameters are ignored in generated tests.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: Invalid credentials.
  - Why it fails: Login returns 401 for incorrect credentials.
  - Violated prerequisite or constraint: No valid bearer token can be produced.
- Failing function: `list eligible job classes`
  - Failure condition: Missing, malformed, expired, or invalid bearer token.
  - Why it fails: The endpoint is protected by `quartz-manager-auth`.
  - Violated prerequisite or constraint: Authenticated caller context is missing.
- Failing function: `list eligible job classes`
  - Failure condition: OpenAPI-declared generic `404 Not Found`.
  - Why it fails: The current visible source does not expose a user-controlled condition that causes this.
  - Violated prerequisite or constraint: No concrete implementation-backed resource constraint is visible.

Implementation notes:
The OpenAPI response schema says `type=string`, but generated tests assert a JSON array with `size() == 0`; this is an OpenAPI/runtime discrepancy.

### Behavior 3: Inspect Scheduler Status and Configuration

Business goal:
Read the current scheduler identity, configuration, status, and trigger-key view.

Domain context:
Operators need to know whether the scheduler is stopped/running/paused and which triggers are attached.

Starting point:
No prior scheduler/job/trigger state; valid credentials are configured.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with form `username={username}` and `password={password}` to obtain `{token}`.
2. Use function `retrieve scheduler details` (`GET /quartz-manager/scheduler`) with header `Authorization=Bearer {token}` to inspect scheduler details.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped if a valid `{token}` already exists.
- Scheduler configuration may be supplied by deployment or direct configuration, but no API function creates the scheduler resource.

Parameter and value bindings:
- `{token}` is reused in the `Authorization` header.
- No scheduler id is supplied; the operation targets the singleton scheduler.

Business result:
The caller receives scheduler details. Generated tests observed fields such as `name=example`, `instanceId=NON_CLUSTERED`, `status=STOPPED`, and `triggerKeys=null`.

Constraints and invariants:
- The scheduler is a singleton API resource.
- The endpoint is authenticated.
- No ownership, tenant, scheduler id, or query filter is exposed.
- Extra query parameters are ignored in generated tests.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: Invalid credentials.
  - Why it fails: Login returns 401.
  - Violated prerequisite or constraint: A valid bearer token cannot be obtained.
- Failing function: `retrieve scheduler details`
  - Failure condition: Missing or invalid bearer token.
  - Why it fails: The endpoint is protected by `quartz-manager-auth`.
  - Violated prerequisite or constraint: Authenticated caller context is missing.
- Failing function: `retrieve scheduler details`
  - Failure condition: OpenAPI-declared `404 Not Found`.
  - Why it fails: No visible source or API setup path exposes a missing scheduler state.
  - Violated prerequisite or constraint: No concrete request-level constraint is visible.

Implementation notes:
The endpoint is a read operation. Unlike `run`, `resume`, and trigger listing, generated tests show successful 200 responses for this endpoint.

### Behavior 4: Stop Scheduler Execution

Business goal:
Stop the Quartz scheduler.

Domain context:
Stopping the scheduler is an operational control used to prevent further scheduled execution.

Starting point:
No prior scheduler/job/trigger state; valid credentials are configured.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with form `username={username}` and `password={password}` to obtain `{token}`.
2. Use function `stop scheduler` (`GET /quartz-manager/scheduler/stop`) with header `Authorization=Bearer {token}` to stop the scheduler.

Optional verification workflow:
1. Use function `retrieve scheduler details` (`GET /quartz-manager/scheduler`) with header `Authorization=Bearer {token}` to inspect the resulting scheduler status.

Existing-state shortcuts:
- Step 1 can be skipped if a valid `{token}` already exists.
- No API setup is required to create the scheduler; it is assumed to exist as a singleton deployment resource.
- The stop action itself cannot be skipped when the business goal is to stop the scheduler.

Parameter and value bindings:
- `{token}` from authentication is reused for both stop and optional status inspection.
- The operation has no scheduler id; it controls the singleton scheduler.

Business result:
The scheduler is requested to stop. OpenAPI documents `204 Got stopped successfully`; generated tests observed 204 with an empty body.

Constraints and invariants:
- A valid bearer token is required.
- OpenAPI does not require the scheduler to be running before stop is called.
- The operation uses GET despite mutating scheduler state.
- Extra query parameters are ignored in generated tests.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: Invalid credentials.
  - Why it fails: Login returns 401.
  - Violated prerequisite or constraint: No valid caller context.
- Failing function: `stop scheduler`
  - Failure condition: Missing or invalid bearer token.
  - Why it fails: Security scheme rejects unauthenticated access.
  - Violated prerequisite or constraint: `Authorization: Bearer {token}` is absent or invalid.
- Failing function: `stop scheduler`
  - Failure condition: OpenAPI-declared `404 Not Found`.
  - Why it fails: The visible source does not show a user-controlled way to remove or misaddress the singleton scheduler.
  - Violated prerequisite or constraint: No concrete missing-resource condition is visible.

Implementation notes:
Generated tests show `stop scheduler` succeeds more reliably than `start scheduler` and `resume scheduler`.

### Behavior 5: Pause Scheduler Execution

Business goal:
Pause scheduler activity without necessarily destroying scheduler configuration.

Domain context:
Pausing is useful when scheduled execution should temporarily halt while preserving scheduler setup.

Starting point:
No prior scheduler/job/trigger state; valid credentials are configured.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with form `username={username}` and `password={password}` to obtain `{token}`.
2. Use function `pause scheduler` (`GET /quartz-manager/scheduler/pause`) with header `Authorization=Bearer {token}` to pause the scheduler.

Optional verification workflow:
1. Use function `retrieve scheduler details` (`GET /quartz-manager/scheduler`) with header `Authorization=Bearer {token}` to inspect the resulting scheduler status.

Existing-state shortcuts:
- Step 1 can be skipped if a valid `{token}` already exists.
- No API setup is required to create the scheduler.
- The pause action itself cannot be skipped for this behavior.

Parameter and value bindings:
- `{token}` from authentication is reused for pause and optional status inspection.
- The operation targets the singleton scheduler; no scheduler id is accepted.

Business result:
The scheduler is requested to pause. OpenAPI documents 204 success; generated tests observed 204 with an empty body.

Constraints and invariants:
- A valid bearer token is required.
- OpenAPI does not require a prior running state.
- The operation uses GET despite changing scheduler state.
- Extra query parameters are ignored in generated tests.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: Invalid credentials.
  - Why it fails: Login returns 401.
  - Violated prerequisite or constraint: No valid bearer token.
- Failing function: `pause scheduler`
  - Failure condition: Missing or invalid bearer token.
  - Why it fails: Security scheme rejects unauthenticated access.
  - Violated prerequisite or constraint: Authenticated caller context is missing.
- Failing function: `pause scheduler`
  - Failure condition: OpenAPI-declared `404 Not Found`.
  - Why it fails: No implementation-backed missing scheduler condition is visible.
  - Violated prerequisite or constraint: No concrete resource constraint is visible.

Implementation notes:
Generated tests observed successful pause calls for multiple configured users, which suggests no user-specific scheduler ownership check.

### Behavior 6: Start Scheduler Execution

Business goal:
Start the scheduler so it can execute scheduled work.

Domain context:
Starting is a core operational transition for a scheduler service.

Starting point:
No prior scheduler/job/trigger state; valid credentials are configured.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with form `username={username}` and `password={password}` to obtain `{token}`.
2. Use function `start scheduler` (`GET /quartz-manager/scheduler/run`) with header `Authorization=Bearer {token}` to start scheduler execution.

Optional verification workflow:
1. Use function `retrieve scheduler details` (`GET /quartz-manager/scheduler`) with header `Authorization=Bearer {token}` to inspect whether status changed.

Existing-state shortcuts:
- Step 1 can be skipped if a valid `{token}` already exists.
- No API setup creates the scheduler; it is a singleton deployment resource.
- Calling `stop scheduler` first is an optional operational setup path, but OpenAPI does not require it.

Parameter and value bindings:
- `{token}` is reused in the `Authorization` header.
- No scheduler id or expected prior status is supplied.

Business result:
By contract, the scheduler should start and return 204. However, generated tests repeatedly observed 500 Internal Server Error for this endpoint, with failure attributed to `SchedulerService_25_start`.

Constraints and invariants:
- A valid bearer token is required.
- No prior stopped state is documented as required.
- The operation uses GET while mutating scheduler execution state.
- The implementation evidence indicates the documented 204 path may not be reliable in the tested runtime.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: Invalid credentials.
  - Why it fails: Login returns 401.
  - Violated prerequisite or constraint: No valid token.
- Failing function: `start scheduler`
  - Failure condition: Missing or invalid bearer token.
  - Why it fails: Security scheme rejects unauthenticated access.
  - Violated prerequisite or constraint: Authenticated caller context is missing.
- Failing function: `start scheduler`
  - Failure condition: Runtime start operation throws.
  - Why it fails: Generated tests observed 500 from `SchedulerService_25_start`.
  - Violated prerequisite or constraint: The implementation cannot complete the scheduler start transition in the tested state.
- Failing function: `start scheduler`
  - Failure condition: OpenAPI-declared `404 Not Found`.
  - Why it fails: No visible source exposes a user-controlled missing scheduler condition.
  - Violated prerequisite or constraint: No concrete resource constraint is visible.

Implementation notes:
OpenAPI says 204 success, while generated tests show 500 for authenticated calls. This is a significant contract/runtime discrepancy.

### Behavior 7: Resume Scheduler Execution

Business goal:
Resume scheduler processing after a pause.

Domain context:
Resume complements pause and should restore processing without rebuilding scheduler configuration.

Starting point:
No prior scheduler/job/trigger state; valid credentials are configured.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with form `username={username}` and `password={password}` to obtain `{token}`.
2. Use function `resume scheduler` (`GET /quartz-manager/scheduler/resume`) with header `Authorization=Bearer {token}` to resume scheduler execution.

Optional verification workflow:
1. Use function `retrieve scheduler details` (`GET /quartz-manager/scheduler`) with header `Authorization=Bearer {token}` to inspect scheduler status.

Existing-state shortcuts:
- Step 1 can be skipped if a valid `{token}` already exists.
- Calling `pause scheduler` first is the natural setup path for a pause/resume workflow, but OpenAPI does not require it.
- Direct scheduler state setup may establish a paused scheduler outside the exposed API.

Parameter and value bindings:
- `{token}` is reused in the `Authorization` header.
- The same singleton scheduler is affected by both optional `pause scheduler` and `resume scheduler`.

Business result:
By contract, the scheduler should resume and return 204. Generated tests repeatedly observed 500 Internal Server Error for authenticated resume calls, attributed to scheduler service start/resume handling.

Constraints and invariants:
- A valid bearer token is required.
- OpenAPI does not state that `pause scheduler` must be called first.
- The operation uses GET while mutating scheduler state.
- Runtime behavior appears inconsistent with documented 204 success.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: Invalid credentials.
  - Why it fails: Login returns 401.
  - Violated prerequisite or constraint: No valid token.
- Failing function: `resume scheduler`
  - Failure condition: Missing or invalid bearer token.
  - Why it fails: Security scheme rejects unauthenticated access.
  - Violated prerequisite or constraint: Authenticated caller context is missing.
- Failing function: `resume scheduler`
  - Failure condition: Runtime resume/start operation throws.
  - Why it fails: Generated tests observed 500 responses for authenticated calls.
  - Violated prerequisite or constraint: The implementation cannot complete the transition in the tested state.
- Failing function: `resume scheduler`
  - Failure condition: OpenAPI-declared `404 Not Found`.
  - Why it fails: No visible missing-scheduler setup path exists.
  - Violated prerequisite or constraint: No concrete resource constraint is visible.

Implementation notes:
The API exposes both pause and resume, but generated tests show pause succeeds while resume fails in the tested runtime.

### Behavior 8: Schedule a Named Simple Trigger

Business goal:
Create a named simple trigger in the scheduler store.

Domain context:
Simple triggers represent scheduled firing rules. The trigger name is the stable handle used for later retrieval and rescheduling.

Starting point:
No prior trigger state; valid credentials are configured.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with form `username={username}` and `password={password}` to obtain `{token}`.
2. Use function `schedule simple trigger` (`POST /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}`, path `name={triggerName}`, `Content-Type=application/json`, and body `{initialSimpleTriggerInput}` matching `SimpleTriggerInputDTO` to create the named trigger.

Optional verification workflow:
1. Use function `retrieve simple trigger by name` (`GET /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}` and path `name={triggerName}` to inspect the created trigger.
2. Use function `list triggers` (`GET /quartz-manager/triggers`) with header `Authorization=Bearer {token}` to inspect the global trigger list, noting generated tests observed 500 for this endpoint.

Existing-state shortcuts:
- Step 1 can be skipped if a valid `{token}` already exists.
- Direct Quartz scheduler-store setup can create an equivalent trigger, but the same `{triggerName}` must be used by later retrieval/reschedule calls.
- The schedule action cannot be skipped when the behavior is to create the trigger through the API.

Parameter and value bindings:
- `{token}` from `authenticate user` is reused in the `Authorization` header.
- Path `name={triggerName}` becomes the trigger identity consumed later by `retrieve simple trigger by name` and `reschedule simple trigger`.
- `{initialSimpleTriggerInput}` creates the scheduling configuration; its exact fields are unavailable because `SimpleTriggerInputDTO` is referenced but not defined in OpenAPI.

Business result:
A simple trigger named `{triggerName}` should exist in the scheduler store with the submitted simple-trigger configuration. OpenAPI documents 201 success with `SimpleTriggerDTO`.

Constraints and invariants:
- A valid bearer token is required.
- `name` is required and identifies the trigger.
- The request body must be valid JSON for `SimpleTriggerInputDTO`.
- The OpenAPI does not define uniqueness/conflict behavior if `{triggerName}` already exists.
- Generated tests show `{}` returns 400, and wrong/missing JSON media type returns 415.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: Invalid credentials.
  - Why it fails: Login returns 401.
  - Violated prerequisite or constraint: No valid token.
- Failing function: `schedule simple trigger`
  - Failure condition: Missing or invalid bearer token.
  - Why it fails: Security scheme rejects unauthenticated access.
  - Violated prerequisite or constraint: Authenticated caller context is missing.
- Failing function: `schedule simple trigger`
  - Failure condition: Body is syntactically acceptable JSON but violates `SimpleTriggerInputDTO`, such as `{}` in generated tests.
  - Why it fails: OpenAPI documents `400 Invalid trigger configuration`; tests observed 400 Bad Request.
  - Violated prerequisite or constraint: Required trigger configuration fields or rules are missing.
- Failing function: `schedule simple trigger`
  - Failure condition: `Content-Type` is missing or not `application/json`.
  - Why it fails: Generated tests observed 415 Unsupported Media Type.
  - Violated prerequisite or constraint: The endpoint requires JSON input.
- Failing function: `schedule simple trigger`
  - Failure condition: OpenAPI-declared `404 Not Found`.
  - Why it fails: The current visible source does not expose which dependency could be missing.
  - Violated prerequisite or constraint: No concrete missing dependency is visible.

Implementation notes:
The lack of `SimpleTriggerInputDTO` schema prevents domain-level analysis of schedule fields such as start time, interval, repeat count, misfire handling, or job binding.

### Behavior 9: Retrieve a Named Simple Trigger

Business goal:
Inspect the scheduling definition for a specific simple trigger.

Domain context:
Operators need to inspect an individual trigger before deciding whether to reschedule or troubleshoot it.

Starting point:
No prior trigger state; valid credentials are configured.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with form `username={username}` and `password={password}` to obtain `{token}`.
2. Use function `schedule simple trigger` (`POST /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}`, path `name={triggerName}`, `Content-Type=application/json`, and valid body `{initialSimpleTriggerInput}` to create the trigger to be retrieved.
3. Use function `retrieve simple trigger by name` (`GET /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}` and path `name={triggerName}` to retrieve it.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped if a valid `{token}` already exists.
- Step 2 can be skipped if a simple trigger already exists under the same `{triggerName}`, either from a prior API call or direct Quartz scheduler-store setup.
- The `retrieve simple trigger by name` action cannot be skipped for this read behavior.
- The existing trigger must be a simple trigger addressable by the same path name.

Parameter and value bindings:
- `{token}` is reused across create and read.
- The same path `name={triggerName}` must be used in both `schedule simple trigger` and `retrieve simple trigger by name`.
- If `GET` uses `{otherName}` instead of `{triggerName}`, it addresses a different trigger and does not retrieve the created one.

Business result:
The caller receives the simple-trigger representation for `{triggerName}` without changing scheduler state.

Constraints and invariants:
- A valid bearer token is required.
- The named simple trigger must exist.
- No group/scope parameter is exposed; name is the only API identity.
- Generated tests show missing trigger retrieval may return 500 instead of the OpenAPI-declared 404.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: Invalid credentials.
  - Why it fails: Login returns 401.
  - Violated prerequisite or constraint: No valid token.
- Failing function: `schedule simple trigger`
  - Failure condition: Invalid initial body or unsupported media type.
  - Why it fails: The setup trigger cannot be created; OpenAPI/test evidence shows 400/415.
  - Violated prerequisite or constraint: Valid trigger state for `{triggerName}` is not established.
- Failing function: `retrieve simple trigger by name`
  - Failure condition: Missing or invalid bearer token.
  - Why it fails: Security scheme rejects unauthenticated access.
  - Violated prerequisite or constraint: Authenticated caller context is missing.
- Failing function: `retrieve simple trigger by name`
  - Failure condition: No simple trigger named `{triggerName}` exists.
  - Why it fails: OpenAPI documents `404 Trigger not found`, but generated tests observed 500 from `AbstractSchedulerService_18_getTriggerByName`.
  - Violated prerequisite or constraint: Required trigger state is absent.
- Failing function: `retrieve simple trigger by name`
  - Failure condition: `name={otherName}` differs from the created trigger name.
  - Why it fails: The path name is the trigger identity.
  - Violated prerequisite or constraint: The lookup does not reference the established trigger.

Implementation notes:
OpenAPI and `full-behavior.md` model missing trigger as 404, but tests show implementation may surface it as 500.

### Behavior 10: Reschedule an Existing Simple Trigger

Business goal:
Change the schedule configuration for an existing named simple trigger.

Domain context:
Rescheduling lets operators adjust timing without creating a new trigger identity.

Starting point:
No prior trigger state; valid credentials are configured.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with form `username={username}` and `password={password}` to obtain `{token}`.
2. Use function `schedule simple trigger` (`POST /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}`, path `name={triggerName}`, `Content-Type=application/json`, and valid body `{initialSimpleTriggerInput}` to establish the trigger.
3. Use function `reschedule simple trigger` (`PUT /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}`, path `name={triggerName}`, `Content-Type=application/json`, and valid body `{replacementSimpleTriggerInput}` matching `SimpleTriggerInputDTO` to replace the trigger schedule.

Optional verification workflow:
1. Use function `retrieve simple trigger by name` (`GET /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}` and path `name={triggerName}` to inspect the replacement state.

Existing-state shortcuts:
- Step 1 can be skipped if a valid `{token}` already exists.
- Step 2 can be skipped if a simple trigger already exists under `{triggerName}`, either through prior API use or direct Quartz store setup.
- The reschedule action cannot be skipped for this behavior.
- Direct database/scheduler-store setup must preserve the same trigger name and simple-trigger type.

Parameter and value bindings:
- `{token}` is reused across schedule, reschedule, and optional retrieval.
- Path `name={triggerName}` binds the initial trigger and replacement operation to the same domain resource.
- `{replacementSimpleTriggerInput}` intentionally differs from `{initialSimpleTriggerInput}` to represent the new schedule configuration; the trigger identity remains the same.

Business result:
The trigger named `{triggerName}` should retain its identity while its simple-trigger schedule configuration changes to `{replacementSimpleTriggerInput}`. OpenAPI documents 200 success with `TriggerDTO`.

Constraints and invariants:
- A valid bearer token is required.
- The trigger named `{triggerName}` must already exist.
- The replacement body must be valid for `SimpleTriggerInputDTO`.
- No partial update/patch behavior is exposed.
- No optimistic locking, ETag, version, or last-modified guard is exposed.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: Invalid credentials.
  - Why it fails: Login returns 401.
  - Violated prerequisite or constraint: No valid token.
- Failing function: `schedule simple trigger`
  - Failure condition: Invalid initial trigger body.
  - Why it fails: Setup cannot create the existing trigger; OpenAPI/test evidence shows 400/415.
  - Violated prerequisite or constraint: Existing trigger state is not established.
- Failing function: `reschedule simple trigger`
  - Failure condition: Missing or invalid bearer token.
  - Why it fails: Security scheme rejects unauthenticated access.
  - Violated prerequisite or constraint: Authenticated caller context is missing.
- Failing function: `reschedule simple trigger`
  - Failure condition: No trigger exists for path `name={triggerName}`.
  - Why it fails: The operation is documented as rescheduling an existing trigger and declares 404.
  - Violated prerequisite or constraint: Existing trigger state is missing.
- Failing function: `reschedule simple trigger`
  - Failure condition: Replacement body is invalid, such as `{}` in generated tests.
  - Why it fails: OpenAPI documents `400 Invalid trigger configuration`; tests observed 400.
  - Violated prerequisite or constraint: Replacement trigger configuration is invalid.
- Failing function: `reschedule simple trigger`
  - Failure condition: Missing or non-JSON content type.
  - Why it fails: Generated tests observed 415 Unsupported Media Type.
  - Violated prerequisite or constraint: JSON request-body requirement is violated.
- Failing function: `reschedule simple trigger`
  - Failure condition: Path contains problematic semicolon-style content.
  - Why it fails: Generated tests observed a 500 attributed to authentication success handling for a semicolon path.
  - Violated prerequisite or constraint: Path value handling is not robust for that name form.

Implementation notes:
The schema gap makes it impossible to distinguish valid schedule changes from invalid ones except by runtime validation.

### Behavior 11: List Global Triggers

Business goal:
Inspect the scheduler’s global trigger list.

Domain context:
A scheduler operator needs a collection view to see what triggers exist before inspecting or rescheduling an individual trigger.

Starting point:
No prior trigger state; valid credentials are configured.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with form `username={username}` and `password={password}` to obtain `{token}`.
2. Use function `list triggers` (`GET /quartz-manager/triggers`) with header `Authorization=Bearer {token}` to list triggers.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped if a valid `{token}` already exists.
- Direct Quartz scheduler-store setup can create triggers before listing if a non-empty list is required.
- No setup function is required for an empty collection listing by contract.

Parameter and value bindings:
- `{token}` is reused in the `Authorization` header.
- No trigger name, group, cursor, page, or filter value is accepted.

Business result:
By contract, the caller receives the global trigger list. Generated tests repeatedly observed 500 Internal Server Error for authenticated calls.

Constraints and invariants:
- A valid bearer token is required.
- The list is global and unscoped.
- OpenAPI response schema references `TriggerKeyDTO`, but the schema is missing.
- No pagination or filtering is exposed.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: Invalid credentials.
  - Why it fails: Login returns 401.
  - Violated prerequisite or constraint: No valid token.
- Failing function: `list triggers`
  - Failure condition: Missing or invalid bearer token.
  - Why it fails: Security scheme rejects unauthenticated access.
  - Violated prerequisite or constraint: Authenticated caller context is missing.
- Failing function: `list triggers`
  - Failure condition: Runtime trigger fetch throws.
  - Why it fails: Generated tests observed 500 from `TriggerService_28_fetchTriggers`.
  - Violated prerequisite or constraint: The implementation cannot produce the collection view in the tested state.
- Failing function: `list triggers`
  - Failure condition: OpenAPI-declared `404 Not Found`.
  - Why it fails: No user-controlled missing collection condition is visible.
  - Violated prerequisite or constraint: No concrete resource constraint is visible.

Implementation notes:
This is a documented behavior, but the tested implementation appears unreliable for normal authenticated calls.

### Behavior 12: Create, Inspect, and Change a Simple Trigger

Business goal:
Carry out the normal operational workflow for a named trigger: create it, inspect it, and later change its schedule.

Domain context:
This is the meaningful trigger-management workflow assembled from the simple-trigger functions.

Starting point:
No prior trigger state; valid credentials are configured.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with form `username={username}` and `password={password}` to obtain `{token}`.
2. Use function `schedule simple trigger` (`POST /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}`, path `name={triggerName}`, `Content-Type=application/json`, and valid body `{initialSimpleTriggerInput}` to create the trigger.
3. Use function `retrieve simple trigger by name` (`GET /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}` and path `name={triggerName}` to inspect the created trigger.
4. Use function `reschedule simple trigger` (`PUT /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}`, path `name={triggerName}`, `Content-Type=application/json`, and valid body `{replacementSimpleTriggerInput}` to change the schedule.

Optional verification workflow:
1. Use function `retrieve simple trigger by name` (`GET /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}` and path `name={triggerName}` to inspect the final replacement state.
2. Use function `list triggers` (`GET /quartz-manager/triggers`) with header `Authorization=Bearer {token}` to inspect whether the trigger appears globally, noting observed 500 risk.

Existing-state shortcuts:
- Step 1 can be skipped with an existing valid `{token}`.
- Step 2 can be skipped if `{triggerName}` already exists as a simple trigger and the business workflow starts at inspection/rescheduling existing state.
- Direct Quartz store setup can replace step 2 only if it creates the same named simple trigger.
- Steps 3 and 4 cannot be skipped if the behavior specifically requires both inspection and rescheduling.

Parameter and value bindings:
- `{token}` is reused across every step.
- The same `name={triggerName}` binds create, retrieve, reschedule, and final verification to one domain trigger.
- `{initialSimpleTriggerInput}` and `{replacementSimpleTriggerInput}` intentionally differ; the domain meaning is changing schedule configuration while retaining trigger identity.

Business result:
A named simple trigger exists, has been inspected, and has its schedule replaced by the submitted replacement configuration.

Constraints and invariants:
- Trigger identity is path-name based.
- Body schema rules are enforced but undocumented.
- No delete, disable, pause-trigger, or trigger-group operation is available.
- No transaction covers the full workflow; a create can succeed while later read or reschedule fails.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: Invalid credentials.
  - Why it fails: Login returns 401.
  - Violated prerequisite or constraint: No valid API session.
- Failing function: `schedule simple trigger`
  - Failure condition: Invalid or unsupported request body.
  - Why it fails: OpenAPI/tests show 400/415.
  - Violated prerequisite or constraint: Valid initial trigger state is not created.
- Failing function: `retrieve simple trigger by name`
  - Failure condition: Trigger does not exist under `{triggerName}` or lookup throws.
  - Why it fails: OpenAPI says 404 for missing trigger; tests observed 500 for missing-trigger paths.
  - Violated prerequisite or constraint: The lookup must match an existing trigger identity.
- Failing function: `reschedule simple trigger`
  - Failure condition: Replacement body is invalid or the trigger is missing.
  - Why it fails: OpenAPI documents 400/404; tests show invalid body returns 400.
  - Violated prerequisite or constraint: Existing named trigger and valid replacement schedule are required.

Implementation notes:
This is the closest supported trigger lifecycle, but it is incomplete because no delete/unschedule function exists and trigger listing/retrieval have observed 500 failure modes.

### Behavior 13: Operationally Quiesce the Scheduler

Business goal:
Move the scheduler into a non-executing state by pausing and/or stopping it.

Domain context:
Operations teams often need to halt scheduled work during maintenance.

Starting point:
No prior scheduler/job/trigger state; valid credentials are configured.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with form `username={username}` and `password={password}` to obtain `{token}`.
2. Use function `pause scheduler` (`GET /quartz-manager/scheduler/pause`) with header `Authorization=Bearer {token}` to pause execution.
3. Use function `stop scheduler` (`GET /quartz-manager/scheduler/stop`) with header `Authorization=Bearer {token}` to stop the scheduler.

Optional verification workflow:
1. Use function `retrieve scheduler details` (`GET /quartz-manager/scheduler`) with header `Authorization=Bearer {token}` to inspect the final status.

Existing-state shortcuts:
- Step 1 can be skipped if a valid `{token}` already exists.
- If the scheduler is already paused, step 2 may be skipped when the business goal is only final stopped state.
- If the scheduler is already stopped, both state-change calls may be operationally unnecessary, but the API does not expose a conditional stop command.

Parameter and value bindings:
- `{token}` is reused across pause, stop, and optional status inspection.
- Both state-changing calls operate on the same singleton scheduler.

Business result:
The scheduler is requested to be paused and then stopped. Generated tests show both functions can return 204.

Constraints and invariants:
- No scheduler id or tenant is exposed.
- No precondition requires running state before pause/stop.
- The API does not expose an atomic maintenance-mode transition.
- Both mutating operations use GET.

Failure and exceptional cases:
- Failing function: `authenticate user`
  - Failure condition: Invalid credentials.
  - Why it fails: Login returns 401.
  - Violated prerequisite or constraint: No valid token.
- Failing function: `pause scheduler`
  - Failure condition: Missing/invalid bearer token or undocumented missing scheduler condition.
  - Why it fails: Security scheme enforces authentication; OpenAPI declares 404.
  - Violated prerequisite or constraint: Authenticated singleton scheduler operation cannot be completed.
- Failing function: `stop scheduler`
  - Failure condition: Missing/invalid bearer token or undocumented missing scheduler condition.
  - Why it fails: Security scheme enforces authentication; OpenAPI declares 404.
  - Violated prerequisite or constraint: Authenticated singleton scheduler operation cannot be completed.

Implementation notes:
This composite workflow is better supported by tests than a full pause/resume/start cycle because `pause scheduler` and `stop scheduler` have observed 204 success, while `start scheduler` and `resume scheduler` show 500s.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Manage Users, Sessions, and Token Revocation

Priority:
Critical domain gap

Expected business goal:
Administrators should be able to create users, list users, disable users, rotate credentials, refresh tokens, and revoke/logout active sessions.

Why it is unsupported:
Only `authenticate user` exists. There is no OpenAPI operation for user creation, credential update, token refresh, token introspection, or logout. Source constants include `/quartz-manager/auth/logout`, but no matching OpenAPI operation or controller source is present.

Existing functions considered:
- `authenticate user`: obtains a token from existing credentials, but cannot create, update, list, disable, or revoke credentials/tokens.

Missing capability:
User/session lifecycle endpoints and token revocation behavior.

Proof that function composition is insufficient:
Repeated login calls can only create more tokens for credentials that already exist. They cannot create credential state, invalidate a token, distinguish active sessions, or revoke access after compromise.

Evidence from existing functions/source:
- `full-behavior.md` explicitly excludes logout because it appears only as a constant.
- `QuartzManagerPaths` defines `QUARTZ_MANAGER_LOGOUT_PATH`, but `quartz-manager.json` has no logout path.

Business impact:
Authentication is usable for testing and access, but not administrable or safely revocable.

### Missing Behavior 2: Reliably Start or Resume Scheduler Execution

Priority:
Critical domain gap

Expected business goal:
Operators should be able to start a stopped scheduler and resume a paused scheduler with a documented, observable state transition.

Why it is unsupported:
The functions exist by contract, but generated tests repeatedly observed 500 Internal Server Error for authenticated `start scheduler` and `resume scheduler` calls.

Existing functions considered:
- `start scheduler`: intended to start execution, but observed 500 from scheduler service start.
- `resume scheduler`: intended to resume execution, but observed 500 in generated tests.
- `pause scheduler`: can pause, but cannot resume.
- `stop scheduler`: can stop, but cannot start.
- `retrieve scheduler details`: can inspect status, but cannot repair the transition.

Missing capability:
A reliable implementation-backed start/resume transition, plus clear state preconditions and error mapping.

Proof that function composition is insufficient:
No sequence of `pause scheduler`, `stop scheduler`, or `retrieve scheduler details` can replace a successful start/resume transition. The state that must be produced is an executing scheduler, and only the failing functions claim to produce it.

Evidence from existing functions/source:
- OpenAPI documents 204 for `/scheduler/run` and `/scheduler/resume`.
- Generated tests show 500 for authenticated calls to those paths.

Business impact:
The API can quiesce the scheduler but may not be able to restore execution, making operational maintenance workflows unsafe.

### Missing Behavior 3: Unschedule or Delete a Simple Trigger

Priority:
Critical domain gap

Expected business goal:
Operators should be able to remove a trigger that should no longer fire.

Why it is unsupported:
No `DELETE /quartz-manager/simple-triggers/{name}` or equivalent unschedule endpoint exists.

Existing functions considered:
- `schedule simple trigger`: creates a trigger.
- `retrieve simple trigger by name`: reads a trigger.
- `reschedule simple trigger`: changes an existing trigger but does not remove it.
- `list triggers`: lists triggers but does not mutate them.

Missing capability:
Delete/unschedule endpoint and persisted trigger removal behavior.

Proof that function composition is insufficient:
Rescheduling can change timing but cannot remove trigger identity from the scheduler store. Delete-and-recreate is not equivalent because there is no delete. Direct database cleanup would bypass API behavior and is not an API-realizable workflow.

Evidence from existing functions/source:
- `quartz-manager.json` exposes GET/POST/PUT for `/simple-triggers/{name}` only.
- `full-behavior.md` lists no delete function.

Business impact:
Erroneous or obsolete triggers cannot be safely retired through the API.

### Missing Behavior 4: Manage Job Definitions and Bind Triggers to Jobs Explicitly

Priority:
Important robustness gap

Expected business goal:
A scheduler service normally lets users define which job a trigger fires, inspect job metadata, and manage job lifecycle.

Why it is unsupported:
The API only lists eligible job classes. It exposes no job creation, retrieval by class/name, activation, update, deletion, or explicit trigger-to-job binding endpoint. The trigger input schema is missing, so any job binding hidden inside `SimpleTriggerInputDTO` cannot be verified from the contract.

Existing functions considered:
- `list eligible job classes`: lists static eligible classes only.
- `schedule simple trigger`: may schedule a trigger, but the schema does not disclose job binding fields.
- `retrieve simple trigger by name`: reads trigger state, not job metadata.

Missing capability:
Job lifecycle endpoints and documented trigger-to-job association fields.

Proof that function composition is insufficient:
Listing classes cannot create a job detail or bind a trigger to a specific job. Without a documented job identity in the API, a client cannot prove which job will run when a trigger fires.

Evidence from existing functions/source:
- OpenAPI only has `GET /quartz-manager/jobs`.
- `SimpleTriggerInputDTO` is referenced but undefined.

Business impact:
Trigger scheduling is under-specified and cannot support reliable job orchestration from the API contract alone.

### Missing Behavior 5: Validate or Preview Trigger Configuration Without Persisting It

Priority:
Important robustness gap

Expected business goal:
Clients should be able to validate a trigger schedule, preview next fire times, and detect invalid timing before creating or replacing a trigger.

Why it is unsupported:
Only create and reschedule endpoints validate as part of mutation. There is no dry-run validation or preview endpoint.

Existing functions considered:
- `schedule simple trigger`: validates while creating state.
- `reschedule simple trigger`: validates while replacing state.
- `retrieve simple trigger by name`: can inspect existing state only.

Missing capability:
Validation/preview endpoint, next-fire-time calculation, and non-mutating schedule analysis.

Proof that function composition is insufficient:
Using `schedule simple trigger` as validation mutates state on success. Without delete, a validation-by-create workaround leaves persistent triggers behind and is not equivalent to a dry run.

Evidence from existing functions/source:
- OpenAPI has no validation or preview path.
- No delete endpoint exists to clean up successful trial schedules.

Business impact:
Clients must risk persistent scheduler changes to test schedule validity.

### Missing Behavior 6: Query, Filter, or Page Triggers

Priority:
API ergonomics gap

Expected business goal:
Operators should be able to search triggers by name, group, status, job class, or schedule criteria, especially when many triggers exist.

Why it is unsupported:
`list triggers` takes no documented query parameters, and `retrieve simple trigger by name` requires exact name lookup.

Existing functions considered:
- `list triggers`: global unfiltered list.
- `retrieve simple trigger by name`: exact single-trigger lookup only.

Missing capability:
Trigger search/filter parameters, pagination cursor, sorting, and trigger group scoping.

Proof that function composition is insufficient:
A client can only list everything or fetch one exact name. If the list endpoint fails or is large, there is no alternative way to discover matching triggers or page through results.

Evidence from existing functions/source:
- OpenAPI defines no query parameters for `GET /quartz-manager/triggers`.
- Generated tests show arbitrary extra query parameters do not provide filtering semantics.

Business impact:
Trigger discovery does not scale and is operationally brittle.

### Missing Behavior 7: Enforce or Expose Ownership and Tenant Scoping

Priority:
Important robustness gap

Expected business goal:
In multi-user operation, users should only control scheduler resources they own or are authorized to manage.

Why it is unsupported:
The API exposes global scheduler and trigger operations with bearer authentication but no owner, tenant, project, group, or role-scoped path/query/body field.

Existing functions considered:
- `authenticate user`: identifies a caller but exposes no role or scope in the API contract.
- All scheduler and trigger functions: operate on singleton/global resources.
- `list eligible job classes`: global discovery.

Missing capability:
Ownership model, tenant scoping, role authorization, and resource-level access checks.

Proof that function composition is insufficient:
Passing different valid tokens can authenticate different users, but no function creates owner links or scopes scheduler/trigger resources by owner. Composition cannot enforce a relationship that is neither represented nor checked by exposed parameters.

Evidence from existing functions/source:
- Generated tests use `foo` and `foo2` tokens against the same global endpoints.
- No endpoint includes user/tenant/owner path or query parameters.

Business impact:
Any authenticated caller may be able to affect global scheduler state, depending on unseen security configuration.

### Missing Behavior 8: Contract-Level DTO Schema Discovery

Priority:
API ergonomics gap

Expected business goal:
Clients should know exactly which fields are required for trigger creation, trigger responses, scheduler responses, and error responses.

Why it is unsupported:
The OpenAPI references DTO schemas but does not define them under `components.schemas`.

Existing functions considered:
- `schedule simple trigger`: requires `SimpleTriggerInputDTO`, undefined.
- `reschedule simple trigger`: requires `SimpleTriggerInputDTO`, undefined.
- `retrieve scheduler details`: returns `SchedulerDTO`, undefined.
- `list triggers`: returns `TriggerKeyDTO`, undefined.

Missing capability:
Complete OpenAPI component schemas.

Proof that function composition is insufficient:
No sequence of calls reveals the formal required fields, validation rules, formats, enums, or nullable fields needed to generate a correct client.

Evidence from existing functions/source:
- `quartz-manager.json` contains `$ref` entries for missing schemas.
- Generated tests only show negative examples such as `{}` returning 400.

Business impact:
API consumers cannot reliably construct valid trigger requests or strongly typed clients from the contract.

## Cross-Behavior Observations

- All non-login domain APIs are protected by `quartz-manager-auth`.
- Login success is under-documented: OpenAPI lists only 401, while tests extract `accessToken`.
- Several mutating operations use GET: scheduler start, stop, pause, and resume.
- The scheduler is modeled as a singleton global resource with no exposed scheduler id.
- Trigger identity is path-name based; no trigger group or owner scope is exposed.
- `SimpleTriggerInputDTO` validation exists but is not contractually described.
- OpenAPI declares many 404 responses, but visible source and tests do not show clear user-controlled setup paths for most of them.
- Generated tests show important runtime discrepancies: `list triggers`, missing simple-trigger retrieval, `start scheduler`, and `resume scheduler` can return 500 where the contract suggests normal 200/204/404 behavior.
- `pause scheduler` and `stop scheduler` appear operationally successful in tests, returning 204.
- No API-realizable cleanup path exists for created triggers.

## Coverage Summary

Supported domain areas:
- JWT-based access acquisition from existing credentials
- global scheduler inspection
- scheduler pause and stop controls
- eligible job-class discovery
- named simple-trigger create/read/reschedule by contract

Partially supported domain areas:
- scheduler start/resume, because functions exist but generated tests show 500 failures
- trigger listing, because the function exists but generated tests show 500 failures
- trigger scheduling, because the body schema and validation rules are missing from OpenAPI

Unsupported domain areas:
- user/session administration and token revocation
- trigger deletion/unscheduling
- job lifecycle management and explicit job binding
- trigger validation/preview without mutation
- trigger search, pagination, grouping, ownership, and tenant scoping
- complete OpenAPI DTO schema support