# Domain-Level Workflow and State Behavior Analysis

| No. | Behavior name | Business goal |
|---:|---|---|
| 1 | [Behavior 1: Obtain API access token](#behavior-1) | Authenticate credentials and obtain the bearer token used by protected APIs. |
| 2 | [Behavior 2: Inspect eligible job classes](#behavior-2) | Discover the Java job classes available to the scheduler. |
| 3 | [Behavior 3: Inspect scheduler status and configuration](#behavior-3) | Read the singleton scheduler identity, status, and trigger-key view. |
| 4 | [Behavior 4: Start scheduler execution](#behavior-4) | Move the scheduler toward active execution. |
| 5 | [Behavior 5: Stop scheduler execution](#behavior-5) | Stop the scheduler from executing scheduled work. |
| 6 | [Behavior 6: Pause scheduler execution](#behavior-6) | Temporarily pause scheduler activity while retaining configuration. |
| 7 | [Behavior 7: Resume scheduler execution](#behavior-7) | Resume scheduler activity after a pause or stopped-like inactive condition. |
| 8 | [Behavior 8: List scheduler triggers](#behavior-8) | Read the global trigger inventory known to Quartz Manager. |
| 9 | [Behavior 9: Schedule a named simple trigger](#behavior-9) | Create a named simple trigger in the scheduler store. |
| 10 | [Behavior 10: Retrieve a named simple trigger](#behavior-10) | Read the stored configuration for a specific simple trigger. |
| 11 | [Behavior 11: Reschedule a named simple trigger](#behavior-11) | Replace the timing/configuration of an existing simple trigger. |

## Domain Summary

Quartz Manager is a secured operational REST API for a singleton Quartz scheduler. The main aggregate roots and lifecycle resources visible in the available files are the authenticated caller context represented by a JWT bearer token, the singleton scheduler, global job-class discovery, global trigger listing, and named simple triggers addressed by `{name}`.

The dominant state machines are operational rather than business-case oriented: unauthenticated caller to authenticated caller, scheduler execution state changes through start/stop/pause/resume controls, and simple-trigger lifecycle changes from absent to scheduled to rescheduled. The API has no tenant id, scheduler id, trigger group id, job id, or ownership id in its paths, so all exposed scheduler and trigger behavior is global to the service instance. The visible Java source contains shared constants and utilities only; the OpenAPI contract and generated tests provide the main evidence for REST shape and observed runtime behavior.

The OpenAPI references DTO schemas including `SimpleTriggerInputDTO`, `SimpleTriggerDTO`, `TriggerDTO`, `TriggerKeyDTO`, `SchedulerDTO`, and `ExceptionResponse`, but the `components.schemas` section does not define them. Body-level field rules for simple triggers therefore cannot be inferred from the available source set. Implementation/OpenAPI discrepancies are important in this service: login returns `accessToken` in generated tests although no success response is documented; jobs are documented as `type=string` but behave like a JSON list in tests; several endpoints documented with 200/204/404 responses are observed returning 500 in generated tests.

## Supported Business Behaviors

<a id="behavior-1"></a>
### Behavior 1: Obtain API access token

Business goal:
Authenticate a caller and obtain the JWT bearer token required by protected Quartz Manager operations.

API group boundary:
This is an atomic authentication behavior. The single function is itself the lifecycle transition from an unauthenticated request to an authenticated caller context.

Domain context:
All scheduler, job, and trigger operations in the OpenAPI use the `quartz-manager-auth` bearer security scheme. Login is therefore the gateway workflow for the service, even though the API exposes no registration, refresh, or logout operation.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: A credential pair exists in the authentication backing store, but the caller has no reusable bearer token.
- Transition trigger: The caller submits form credentials to the login endpoint.
- Intermediate states: The authentication layer validates `username` and `password` against configured credentials.
- State after: The caller holds a JWT token value that can be sent as `Authorization: Bearer {token}`.
- Invalid or blocked transitions: Missing or incorrect credentials block token issuance with 401. The API exposes no token refresh, token revocation, or user provisioning transition.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with `Content-Type=application/x-www-form-urlencoded`, form `username={username}`, and form `password={password}` to authenticate the caller and obtain `{token}` from the response.

Optional verification workflow:
None.

Existing-state shortcuts:
- The login call can be skipped for later protected behaviors when the caller already has a valid, unexpired `{token}` accepted by the service.
- Credential setup is outside the documented REST API and may be provided by deployment configuration or direct setup of the authentication backing store.
- The token must still be valid for the same service instance and the `quartz-manager-auth` security scheme.

Parameter and value bindings:
- The submitted `username` and `password` must match one configured credential pair.
- The response value `{token}`, observed in generated tests as `accessToken`, is reused by protected functions as `Authorization: Bearer {token}`.
- No resource id, tenant id, scheduler id, or role id is returned or bound by the login contract.

Business result:
The caller has an authenticated API context that can authorize protected scheduler, job, and trigger calls. No scheduler, job, or trigger state is changed.

Constraints and invariants:
- `username` and `password` are required form fields.
- The documented login operation only declares 401, while generated tests show a successful body containing `accessToken`.
- Authentication is global to the exposed API surface; no per-resource ownership scope is visible.
- No logout endpoint is exposed in OpenAPI, despite a logout path constant in source.

Failure and exceptional cases:
None.

Implementation notes:
`QuartzManagerPaths` defines `/quartz-manager/auth/login` and `/quartz-manager/auth/logout`, but only login appears in the OpenAPI. `OpenAPIConfigConsts` names the bearer scheme as `quartz-manager-auth`. Generated tests authenticate successfully with configured users such as `foo` and `foo2` and extract `accessToken`, which is an OpenAPI documentation gap.

<a id="behavior-2"></a>
### Behavior 2: Inspect eligible job classes

Business goal:
Allow an operator to discover which Java job classes are eligible for use by Quartz Manager.

API group boundary:
This is an atomic read-only job discovery behavior. `list eligible job classes` is itself the domain lookup, and it shares only the authenticated caller context with the rest of the service.

Domain context:
Quartz jobs represent executable work units. The exposed API allows discovery of eligible classes but does not create, bind, update, or delete jobs as first-class resources.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: The service has a configured classpath or job eligibility source, and the caller has no domain data dependency beyond credentials.
- Transition trigger: The caller asks for the eligible job-class list.
- Intermediate states: The bearer token is validated.
- State after: Scheduler, job, and trigger state remain unchanged; the caller receives the current eligible class list.
- Invalid or blocked transitions: Missing or invalid authentication blocks the read with 401. OpenAPI declares 404, but no visible source condition explains a user-controlled not-found state.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with `Content-Type=application/x-www-form-urlencoded`, form `username={username}`, and form `password={password}` to obtain `{token}`.
2. Use function `list eligible job classes` (`GET /quartz-manager/jobs`) with header `Authorization=Bearer {token}` to retrieve the eligible job-class list.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped when a valid `{token}` already exists for the same service instance.
- The eligible job-class source can be changed only outside these APIs, for example by deployment/classpath configuration.
- The lookup action itself cannot be skipped when the business goal is to read the current list.

Parameter and value bindings:
- `{token}` returned by `authenticate user` is reused as the bearer credential for `list eligible job classes`.
- `list eligible job classes` accepts no documented path, query, body, or cursor values.
- Generated tests send extra query parameters and still receive 200, so those values are not part of the domain binding.

Business result:
The caller receives the current globally configured eligible job-class list. Generated tests observed an empty JSON list. No job or trigger is created.

Constraints and invariants:
- The endpoint is protected by `quartz-manager-auth`.
- The result is global; no tenant, user, scheduler id, or job group is supplied.
- The API provides discovery only, not job registration or job lifecycle control.
- OpenAPI says the 200 response schema is a string, while tests assert JSON array semantics.

Failure and exceptional cases:
None.

Implementation notes:
The job list is a read-only capability. The generated tests show successful reads with additional ignored query parameters and an empty list response. This differs from the OpenAPI `type=string` schema and should be treated as an API documentation discrepancy.

<a id="behavior-3"></a>
### Behavior 3: Inspect scheduler status and configuration

Business goal:
Read the singleton scheduler's identity, instance id, status, and trigger-key view.

API group boundary:
This is an atomic read-only scheduler behavior. `retrieve scheduler details` targets the singleton scheduler aggregate; no scheduler id or child resource id is required.

Domain context:
Operators need a stable way to inspect scheduler state before or after control operations. The service exposes a singleton scheduler rather than a collection of schedulers.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: A scheduler exists as a configured service resource.
- Transition trigger: The caller requests scheduler details.
- Intermediate states: The bearer token is validated, then scheduler metadata is read.
- State after: Scheduler state is unchanged; the caller receives scheduler details.
- Invalid or blocked transitions: Missing authentication blocks the read with 401. A documented 404 has no visible user-controlled setup path in the available source.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with `Content-Type=application/x-www-form-urlencoded`, form `username={username}`, and form `password={password}` to obtain `{token}`.
2. Use function `retrieve scheduler details` (`GET /quartz-manager/scheduler`) with header `Authorization=Bearer {token}` to inspect the singleton scheduler.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped when `{token}` is already valid for the service.
- The singleton scheduler is established by deployment/runtime configuration, not by an exposed create-scheduler API.
- The read action itself remains required for this inspection behavior.

Parameter and value bindings:
- `{token}` from `authenticate user` is reused by `retrieve scheduler details`.
- No scheduler id is provided; the endpoint implicitly targets the single configured scheduler.
- Extra query parameters observed in generated tests do not bind to scheduler identity or filtering.

Business result:
The caller receives scheduler details. Generated tests observed `name=example`, `instanceId=NON_CLUSTERED`, `status=STOPPED`, and `triggerKeys=null`.

Constraints and invariants:
- The scheduler is a singleton resource from the API perspective.
- Authentication is required.
- No ownership, tenant, or scheduler selection is available.
- The read does not recalculate, start, stop, pause, or resume the scheduler.

Failure and exceptional cases:
None.

Implementation notes:
This endpoint is one of the better-observed successful operations in the generated tests. It tolerates unexpected query parameters in tests while preserving the same singleton response.

<a id="behavior-4"></a>
### Behavior 4: Start scheduler execution

Business goal:
Move the scheduler into active execution so scheduled work can run.

API group boundary:
This is an atomic scheduler lifecycle transition. `start scheduler` targets the singleton scheduler execution state.

Domain context:
Starting the scheduler is an operational control that changes whether Quartz can execute scheduled triggers. It is distinct from resume because the API exposes separate endpoints and the required workflow must not merge alternative state transitions.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: A singleton scheduler exists and may be stopped or otherwise inactive.
- Transition trigger: The caller invokes the scheduler run endpoint.
- Intermediate states: The bearer token is validated; the scheduler start routine is invoked.
- State after: On documented success, the scheduler is started and the response is 204 with no body. In generated tests, the endpoint returned 500 under reset-state conditions, so the final running state was not demonstrated.
- Invalid or blocked transitions: Missing authentication returns 401. Internal scheduler start failure can surface as 500 rather than a domain-specific blocked transition.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with `Content-Type=application/x-www-form-urlencoded`, form `username={username}`, and form `password={password}` to obtain `{token}`.
2. Use function `start scheduler` (`GET /quartz-manager/scheduler/run`) with header `Authorization=Bearer {token}` to start the singleton scheduler.

Optional verification workflow:
1. Use function `retrieve scheduler details` (`GET /quartz-manager/scheduler`) with header `Authorization=Bearer {token}` to inspect the resulting scheduler status.

Existing-state shortcuts:
- Step 1 can be skipped when a valid `{token}` is already available.
- The singleton scheduler must already exist through runtime configuration.
- The start action itself cannot be skipped when the business goal is to start execution.

Parameter and value bindings:
- `{token}` from `authenticate user` is reused by `start scheduler` and optional status inspection.
- No scheduler id is accepted; the transition applies to the singleton scheduler.
- Query parameters are not documented and have no intended domain binding.

Business result:
On the OpenAPI success path, the scheduler transitions toward a running/started state and returns 204. No trigger or job definition is created. If the runtime start routine fails, generated tests show a 500 error response and no confirmed started state.

Constraints and invariants:
- Authentication is required.
- OpenAPI does not require a prior stopped state and does not define idempotency semantics.
- The state-changing operation is exposed as GET.
- No domain-specific error response is documented for invalid scheduler state or failed startup.

Failure and exceptional cases:
None.

Implementation notes:
OpenAPI documents 204 for successful start. Generated tests repeatedly observe 500 for `/quartz-manager/scheduler/run`, which indicates an implementation/runtime discrepancy and weak domain error mapping for scheduler startup.

<a id="behavior-5"></a>
### Behavior 5: Stop scheduler execution

Business goal:
Stop the scheduler so it no longer executes scheduled work.

API group boundary:
This is an atomic scheduler lifecycle transition. `stop scheduler` targets the singleton scheduler execution state.

Domain context:
Stopping is an operational safety control. It is modeled separately from pause because the service exposes a distinct endpoint and both transitions have different domain meanings.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: A singleton scheduler exists and may be running, paused, or already stopped.
- Transition trigger: The caller invokes the scheduler stop endpoint.
- Intermediate states: The bearer token is validated; the scheduler stop routine is invoked.
- State after: The scheduler is requested to stop, and successful calls return 204 with no body.
- Invalid or blocked transitions: Missing authentication returns 401. OpenAPI declares 404, but no visible resource-selection state can produce it.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with `Content-Type=application/x-www-form-urlencoded`, form `username={username}`, and form `password={password}` to obtain `{token}`.
2. Use function `stop scheduler` (`GET /quartz-manager/scheduler/stop`) with header `Authorization=Bearer {token}` to stop the singleton scheduler.

Optional verification workflow:
1. Use function `retrieve scheduler details` (`GET /quartz-manager/scheduler`) with header `Authorization=Bearer {token}` to inspect the resulting scheduler status.

Existing-state shortcuts:
- Step 1 can be skipped when a valid `{token}` is already available.
- The singleton scheduler must exist through deployment/runtime configuration.
- The stop action itself cannot be skipped when the business goal is to stop execution.

Parameter and value bindings:
- `{token}` from `authenticate user` is reused by `stop scheduler` and optional status inspection.
- No scheduler id, body, or query value identifies the scheduler; the target is implicit.
- Generated tests show extra query values are ignored for successful 204 stop calls.

Business result:
The scheduler receives a stop command. Generated tests observed 204 and an empty body. The API does not expose whether stopping is graceful, immediate, or idempotent.

Constraints and invariants:
- Authentication is required.
- OpenAPI does not require the scheduler to be running before stop.
- The state-changing operation is exposed as GET.
- No job, trigger, or scheduler configuration is deleted by the stop contract.

Failure and exceptional cases:
None.

Implementation notes:
Unlike `start scheduler` and `resume scheduler`, generated tests show repeated successful 204 responses for `stop scheduler`, including calls with unrelated query parameters.

<a id="behavior-6"></a>
### Behavior 6: Pause scheduler execution

Business goal:
Temporarily pause scheduler activity while retaining scheduler configuration and trigger definitions.

API group boundary:
This is an atomic scheduler lifecycle transition. `pause scheduler` targets the singleton scheduler execution state.

Domain context:
Pause is operationally useful when scheduled execution should be suspended without treating the scheduler as fully stopped or destroying trigger configuration.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: A singleton scheduler exists and may be active or already inactive.
- Transition trigger: The caller invokes the scheduler pause endpoint.
- Intermediate states: The bearer token is validated; the scheduler pause routine is invoked.
- State after: The scheduler is requested to enter a paused state, and successful calls return 204 with no body.
- Invalid or blocked transitions: Missing authentication returns 401. No documented precondition requires the scheduler to be running first.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with `Content-Type=application/x-www-form-urlencoded`, form `username={username}`, and form `password={password}` to obtain `{token}`.
2. Use function `pause scheduler` (`GET /quartz-manager/scheduler/pause`) with header `Authorization=Bearer {token}` to pause the singleton scheduler.

Optional verification workflow:
1. Use function `retrieve scheduler details` (`GET /quartz-manager/scheduler`) with header `Authorization=Bearer {token}` to inspect the resulting scheduler status.

Existing-state shortcuts:
- Step 1 can be skipped when a valid `{token}` is already available.
- The singleton scheduler must already exist through service runtime configuration.
- The pause action itself cannot be skipped when the business goal is to pause execution.

Parameter and value bindings:
- `{token}` from `authenticate user` is reused by `pause scheduler` and optional status inspection.
- No scheduler id, trigger id, or body value is accepted.
- Extra query parameters are not part of the domain model and were tolerated in generated tests.

Business result:
The scheduler receives a pause command. Generated tests observed 204 and an empty body. No trigger or job definitions are removed.

Constraints and invariants:
- Authentication is required.
- OpenAPI does not require a prior running state.
- The state-changing operation is exposed as GET.
- Pause applies to the singleton scheduler, not to a specific trigger.

Failure and exceptional cases:
None.

Implementation notes:
Generated tests show `pause scheduler` succeeds with 204 under reset-state conditions, including calls with unexpected query parameters. The endpoint name and OpenAPI summary use GET for a mutating control operation.

<a id="behavior-7"></a>
### Behavior 7: Resume scheduler execution

Business goal:
Resume scheduler activity after it has been paused or otherwise made inactive.

API group boundary:
This is an atomic scheduler lifecycle transition. `resume scheduler` targets the singleton scheduler execution state and is separate from `start scheduler` because it is a distinct exposed operation.

Domain context:
Resume is the counterpart to pausing in a scheduler lifecycle. The API does not enforce or document that a prior pause call must exist before resume.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: A singleton scheduler exists and may be paused or inactive.
- Transition trigger: The caller invokes the scheduler resume endpoint.
- Intermediate states: The bearer token is validated; the scheduler resume/start routine is invoked.
- State after: On documented success, the scheduler resumes and the response is 204 with no body. In generated tests, resume returned 500 under reset-state conditions, so the final resumed state was not demonstrated.
- Invalid or blocked transitions: Missing authentication returns 401. Internal resume failure can surface as 500 rather than a domain-specific invalid-state response.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with `Content-Type=application/x-www-form-urlencoded`, form `username={username}`, and form `password={password}` to obtain `{token}`.
2. Use function `resume scheduler` (`GET /quartz-manager/scheduler/resume`) with header `Authorization=Bearer {token}` to resume the singleton scheduler.

Optional verification workflow:
1. Use function `retrieve scheduler details` (`GET /quartz-manager/scheduler`) with header `Authorization=Bearer {token}` to inspect the resulting scheduler status.

Existing-state shortcuts:
- Step 1 can be skipped when a valid `{token}` is already available.
- If the scheduler has already been paused by external means or a previous service call, no API setup call is required before resume.
- The resume action itself cannot be skipped when the business goal is to resume execution.

Parameter and value bindings:
- `{token}` from `authenticate user` is reused by `resume scheduler` and optional status inspection.
- No scheduler id is accepted; the operation applies to the singleton scheduler.
- There is no value binding to a prior `pause scheduler` response because pause returns no body and resume accepts no transition token.

Business result:
On the OpenAPI success path, scheduler execution resumes and the endpoint returns 204. In generated tests, runtime failures returned 500, leaving the resumed state unconfirmed.

Constraints and invariants:
- Authentication is required.
- OpenAPI does not require an existing paused state.
- The state-changing operation is exposed as GET.
- The implementation appears to share or call a start routine, based on generated-test fault labels.

Failure and exceptional cases:
None.

Implementation notes:
OpenAPI documents 204 for resume, but generated tests repeatedly observe 500. This is both a state-machine robustness issue and an error-contract discrepancy.

<a id="behavior-8"></a>
### Behavior 8: List scheduler triggers

Business goal:
Read the global trigger inventory known to Quartz Manager.

API group boundary:
This is an atomic read-only trigger inventory behavior. `list triggers` targets the global trigger collection and does not consume a trigger name returned by another function.

Domain context:
Operators need visibility into scheduled triggers. The API exposes a global list endpoint, separate from the named simple-trigger retrieve endpoint.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: The scheduler has a trigger store that may be empty or populated.
- Transition trigger: The caller requests the global trigger list.
- Intermediate states: The bearer token is validated; the trigger service reads trigger keys or trigger summaries.
- State after: Trigger and scheduler state remain unchanged; the caller receives the trigger list on success.
- Invalid or blocked transitions: Missing authentication returns 401. Generated tests show internal trigger-service failures returning 500, despite OpenAPI documenting 200 and 404.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with `Content-Type=application/x-www-form-urlencoded`, form `username={username}`, and form `password={password}` to obtain `{token}`.
2. Use function `list triggers` (`GET /quartz-manager/triggers`) with header `Authorization=Bearer {token}` to retrieve the global trigger list.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped when a valid `{token}` is already available.
- Trigger rows can be established outside the API through the Quartz scheduler store, but the list behavior does not require a specific trigger.
- The list action itself remains required when the business goal is inventory visibility.

Parameter and value bindings:
- `{token}` from `authenticate user` is reused by `list triggers`.
- No trigger name, group, cursor, filter, or body value is accepted.
- Extra query parameters used in generated tests are not documented domain inputs.

Business result:
On success, the caller receives the current global trigger list or trigger-key view. No trigger state changes. Generated tests show 500 responses in several list-trigger calls, so successful list behavior is contract-supported but not demonstrated by those failing cases.

Constraints and invariants:
- Authentication is required.
- The trigger list is global and not scoped by caller identity.
- OpenAPI references `TriggerKeyDTO` for the 200 response, but the schema is not defined.
- No filtering, pagination, grouping, or ownership check is documented.

Failure and exceptional cases:
None.

Implementation notes:
The generated tests identify `GET /quartz-manager/triggers` as a potential fault path with 500 responses. This conflicts with the OpenAPI response model and weakens the endpoint as an operational inventory behavior.

<a id="behavior-9"></a>
### Behavior 9: Schedule a named simple trigger

Business goal:
Create a named simple trigger in the scheduler store.

API group boundary:
This is an atomic simple-trigger lifecycle transition. `schedule simple trigger` creates the trigger resource addressed by the caller-provided `{name}` path value.

Domain context:
Simple triggers are scheduler child resources that determine when work should fire. The API models the trigger name as the stable external identifier, not as a generated id returned by a create call.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: No simple trigger named `{name}` exists, or the API is asked to schedule a new trigger under `{name}`.
- Transition trigger: The caller submits a valid `SimpleTriggerInputDTO` body to the named simple-trigger endpoint.
- Intermediate states: The bearer token is validated; request media type and body validation run; the scheduler store receives the new trigger definition.
- State after: A simple trigger named `{name}` exists in the scheduler store, and the documented success response is 201 with `SimpleTriggerDTO`.
- Invalid or blocked transitions: Missing authentication returns 401; missing JSON media type returns 415 in generated tests; invalid body returns 400; OpenAPI declares 404 for an unspecified missing dependency.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with `Content-Type=application/x-www-form-urlencoded`, form `username={username}`, and form `password={password}` to obtain `{token}`.
2. Use function `schedule simple trigger` (`POST /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}`, path `name={name}`, `Content-Type=application/json`, and body `{simpleTriggerInput}` conforming to `SimpleTriggerInputDTO` to create the named simple trigger.

Optional verification workflow:
1. Use function `retrieve simple trigger by name` (`GET /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}` and path `name={name}` to inspect the created trigger.

Existing-state shortcuts:
- Step 1 can be skipped when a valid `{token}` already exists.
- Equivalent trigger state can be inserted directly into the Quartz scheduler store, but it must use the same `{name}` and be visible to this service instance.
- The schedule action itself cannot be skipped when the business goal is API-level creation.

Parameter and value bindings:
- `{token}` from `authenticate user` is reused by `schedule simple trigger` and optional retrieval.
- The caller-provided path `name={name}` becomes the trigger identifier consumed by later `retrieve simple trigger by name` or `reschedule simple trigger` calls.
- The JSON body `{simpleTriggerInput}` must satisfy `SimpleTriggerInputDTO`, but field names and validation rules are not defined in the OpenAPI components.
- No generated trigger id is documented; the path name is the durable binding value.

Business result:
On documented success, a simple trigger named `{name}` exists and is returned as `SimpleTriggerDTO` with HTTP 201. No separate job resource is created through a documented endpoint. If body validation fails, no valid trigger creation is established.

Constraints and invariants:
- Authentication is required.
- `Content-Type=application/json` is required for the body; generated tests observe 415 when the media type is absent.
- An empty JSON object fails with 400 in generated tests, so nontrivial body validation exists even though schema fields are missing.
- The API does not document duplicate-name behavior or ownership scoping.

Failure and exceptional cases:
None.

Implementation notes:
No successful POST simple-trigger call appears in the generated tests, so the positive create path is supported by OpenAPI and `full-behavior.md` rather than directly observed in tests. The validation failures show that the missing DTO schema is operationally significant.

<a id="behavior-10"></a>
### Behavior 10: Retrieve a named simple trigger

Business goal:
Read the stored configuration for a specific named simple trigger.

API group boundary:
This is a named simple-trigger lookup behavior. The workflow uses the same `{name}` aggregate identifier across trigger creation and retrieval so the read is scoped to the trigger established earlier.

Domain context:
After a trigger is scheduled, operators need to inspect its persisted configuration and status. The API retrieves simple triggers by path name, not by generated id.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: A scheduler exists, and the workflow can create or rely on a simple trigger named `{name}`.
- Transition trigger: The caller requests `GET /quartz-manager/simple-triggers/{name}`.
- Intermediate states: The bearer token is validated; the trigger identified by `{name}` is looked up in the scheduler store.
- State after: Trigger state is unchanged; the caller receives the named trigger on success.
- Invalid or blocked transitions: Missing authentication returns 401. OpenAPI documents 404 for an absent trigger, but generated tests show 500 for unknown names, indicating missing not-found mapping.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with `Content-Type=application/x-www-form-urlencoded`, form `username={username}`, and form `password={password}` to obtain `{token}`.
2. Use function `schedule simple trigger` (`POST /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}`, path `name={name}`, `Content-Type=application/json`, and body `{simpleTriggerInput}` conforming to `SimpleTriggerInputDTO` to establish the trigger to be retrieved.
3. Use function `retrieve simple trigger by name` (`GET /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}` and path `name={name}` to retrieve the same simple trigger.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped when a valid `{token}` already exists.
- Step 2 can be skipped when an equivalent simple trigger already exists in the scheduler store under the same `{name}` and is visible to this service instance.
- Direct Quartz scheduler-store setup is equivalent only when the trigger name, trigger type, and service visibility match the later GET path.
- The retrieval action itself cannot be skipped when the business goal is inspection.

Parameter and value bindings:
- `{token}` from `authenticate user` is reused for both trigger creation and retrieval.
- The exact path value `name={name}` used by `schedule simple trigger` must be reused by `retrieve simple trigger by name`.
- The body `{simpleTriggerInput}` creates the persisted trigger configuration that the later GET reads.
- No generated id is consumed by the retrieval; the caller-supplied name is the binding key.

Business result:
The caller receives the simple trigger stored under `{name}`. The trigger remains present and unchanged by the read. If the trigger was created in step 2, the workflow also establishes the trigger as persisted scheduler state before reading it.

Constraints and invariants:
- Authentication is required for both setup and read.
- The trigger name in the GET path must match an existing simple trigger.
- OpenAPI documents 404 Trigger not found, but generated tests observe 500 for unknown trigger names.
- No ownership or tenant scoping prevents one authenticated caller from reading a globally named trigger.

Failure and exceptional cases:
None.

Implementation notes:
The documented not-found response is 404, but generated tests show 500 for absent simple triggers. This is a significant state-transition quirk because a normal lookup miss is reported as an internal server error.

<a id="behavior-11"></a>
### Behavior 11: Reschedule a named simple trigger

Business goal:
Replace the timing or configuration of an existing named simple trigger.

API group boundary:
This is a simple-trigger lifecycle update behavior. It binds the same `{name}` across initial scheduling and rescheduling, and the second function consumes the trigger state created by the first.

Domain context:
Rescheduling is a core trigger lifecycle operation: an operator keeps the trigger identity stable while changing its scheduling configuration.

Starting point:
Pre-existing service/upstream state required

State transition summary:
- State before: A simple trigger named `{name}` exists in the scheduler store.
- Transition trigger: The caller submits a replacement `SimpleTriggerInputDTO` body to the PUT endpoint for the same `{name}`.
- Intermediate states: The bearer token is validated; the replacement body is validated; the scheduler store updates the trigger schedule/configuration.
- State after: The trigger named `{name}` still exists, but its schedule/configuration reflects `{replacementSimpleTriggerInput}`. The documented success response is 200 with `TriggerDTO`.
- Invalid or blocked transitions: Missing authentication returns 401; missing JSON media type returns 415 in tests; invalid body returns 400; absent trigger is documented as 404; some generated invalid PUT calls return 500.

Required execution workflow:
1. Use function `authenticate user` (`POST /quartz-manager/auth/login`) with `Content-Type=application/x-www-form-urlencoded`, form `username={username}`, and form `password={password}` to obtain `{token}`.
2. Use function `schedule simple trigger` (`POST /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}`, path `name={name}`, `Content-Type=application/json`, and body `{initialSimpleTriggerInput}` conforming to `SimpleTriggerInputDTO` to create the trigger that will be rescheduled.
3. Use function `reschedule simple trigger` (`PUT /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}`, path `name={name}`, `Content-Type=application/json`, and body `{replacementSimpleTriggerInput}` conforming to `SimpleTriggerInputDTO` to replace the trigger schedule/configuration.

Optional verification workflow:
1. Use function `retrieve simple trigger by name` (`GET /quartz-manager/simple-triggers/{name}`) with header `Authorization=Bearer {token}` and path `name={name}` to inspect the updated trigger.

Existing-state shortcuts:
- Step 1 can be skipped when a valid `{token}` already exists.
- Step 2 can be skipped when a simple trigger already exists under the same `{name}` in the scheduler store and is visible to this service instance.
- Direct scheduler-store setup is equivalent only when the existing trigger is a simple trigger whose name matches the later PUT path.
- The reschedule action itself cannot be skipped when the business goal is to update the trigger.

Parameter and value bindings:
- `{token}` from `authenticate user` is reused by create, update, and optional read steps.
- The exact path value `name={name}` must be reused between `schedule simple trigger`, `reschedule simple trigger`, and optional `retrieve simple trigger by name`.
- `{initialSimpleTriggerInput}` establishes the prior trigger state; `{replacementSimpleTriggerInput}` is the state consumed by the update transition.
- No generated trigger id or version token is used, so concurrency and lost-update protection are not represented.

Business result:
The trigger named `{name}` remains present, but its scheduling configuration is replaced by the PUT body. On documented success, the response is 200 with `TriggerDTO`. The API does not document whether unchanged fields are preserved, recalculated, or overwritten when omitted from the DTO.

Constraints and invariants:
- Authentication is required.
- The trigger identified by `{name}` must already exist.
- `Content-Type=application/json` is required; generated tests observe 415 without it.
- The replacement body must satisfy hidden `SimpleTriggerInputDTO` validation; empty JSON fails with 400 in generated tests.
- No ETag, version, owner, or trigger group constraint is visible.

Failure and exceptional cases:
None.

Implementation notes:
The positive PUT path is documented but not demonstrated in generated tests. The tests do demonstrate authentication, media-type, and validation behavior. Because the DTO schema is absent, consumers cannot infer a valid replacement body from OpenAPI alone.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Unschedule or delete a simple trigger

Priority:
Critical domain gap

Expected business goal:
Remove a named trigger when it should no longer fire.

Why it is unsupported:
No DELETE endpoint exists for `/quartz-manager/simple-triggers/{name}` or `/quartz-manager/triggers/{name}`. The available functions can create, read, list, and update trigger state, but none removes it.

Existing functions considered:
- `schedule simple trigger`: creates a named trigger and cannot remove an existing one.
- `retrieve simple trigger by name`: reads a named trigger and has no mutation effect.
- `reschedule simple trigger`: changes an existing trigger but keeps the trigger identity alive.
- `list triggers`: lists global trigger state but exposes no removal action.

Missing capability:
A delete or unschedule endpoint that accepts the trigger name and removes the trigger from the scheduler store, with clear behavior for missing triggers and in-flight executions.

Proof that function composition is insufficient:
Rescheduling a trigger is not equivalent to deletion because the trigger remains present and may still fire under a different schedule. Stopping or pausing the entire scheduler is not equivalent because it affects all triggers globally rather than one named trigger.

Evidence from existing functions/source:
- The OpenAPI path `/quartz-manager/simple-triggers/{name}` contains GET, POST, and PUT only.
- `QuartzManagerPaths` only defines base auth/UI constants and does not expose a trigger delete path.
- `list triggers` and `retrieve simple trigger by name` can show state but cannot remove it.

Business impact:
Operators cannot complete the trigger lifecycle through REST. Obsolete or erroneous triggers require direct scheduler-store repair or full scheduler-level controls that are too broad.

### Missing Behavior 2: Trigger-level pause and resume

Priority:
Critical domain gap

Expected business goal:
Pause or resume one trigger without changing the entire scheduler.

Why it is unsupported:
The API exposes scheduler-level pause/resume only. No endpoint accepts a trigger name for pausing or resuming a specific trigger.

Existing functions considered:
- `pause scheduler`: pauses the singleton scheduler globally and cannot target a trigger.
- `resume scheduler`: resumes the singleton scheduler globally and cannot target a trigger.
- `retrieve simple trigger by name`: can locate a trigger by name but has no control transition.
- `reschedule simple trigger`: changes trigger configuration but is not a pause/resume state transition.

Missing capability:
Trigger-scoped lifecycle endpoints, for example pause and resume operations keyed by `{name}` or a documented trigger key, with not-found and invalid-state responses.

Proof that function composition is insufficient:
Reading a trigger and then pausing the scheduler affects all triggers, not just the named trigger. Rescheduling cannot represent a reversible paused state unless the domain model explicitly treats a special schedule value as pause, and no such rule is documented.

Evidence from existing functions/source:
- Only `/quartz-manager/scheduler/pause` and `/quartz-manager/scheduler/resume` exist for pause/resume.
- `retrieve simple trigger by name` and `reschedule simple trigger` are scoped to `{name}`, but neither exposes a trigger execution-state transition.

Business impact:
Operators lack safe fine-grained control. A single problematic trigger can only be addressed by global scheduler actions, direct store manipulation, or a risky schedule rewrite.

### Missing Behavior 3: Reliable not-found handling for trigger lookup

Priority:
Important robustness gap

Expected business goal:
Return a clear domain-level not-found response when a caller requests a trigger name that does not exist.

Why it is unsupported:
OpenAPI documents 404 for missing simple triggers, but generated tests observe 500 for unknown names. No available composition can transform the server-side 500 into a true API-level 404.

Existing functions considered:
- `retrieve simple trigger by name`: should provide the lookup result but returns 500 in generated tests for absent names.
- `list triggers`: could be used by a client to inspect available triggers, but it also shows 500 failures in generated tests and has no documented filter.
- `schedule simple trigger`: can create a trigger but cannot validate absence safely before lookup.

Missing capability:
Implementation-level not-found mapping from scheduler lookup miss to the documented 404 response, plus a reliable trigger-list or existence-check endpoint.

Proof that function composition is insufficient:
A client-side precheck with `list triggers` is not equivalent because the list endpoint itself can fail with 500 and has no guaranteed consistency with the subsequent GET. Creating a missing trigger before reading it changes business state and does not answer whether the original trigger existed.

Evidence from existing functions/source:
- OpenAPI documents `404 Trigger not found` for `retrieve simple trigger by name`.
- Generated tests record 500 responses attributed to `AbstractSchedulerService_18_getTriggerByName`.
- `list triggers` generated tests record 500 responses attributed to `TriggerService_28_fetchTriggers`.

Business impact:
Clients cannot distinguish an absent trigger from a server fault. Automated operations may retry, alert, or repair incorrectly.

### Missing Behavior 4: Safe scheduler start and resume with domain errors

Priority:
Important robustness gap

Expected business goal:
Start or resume the scheduler with predictable handling for invalid runtime state, missing configuration, or already-active conditions.

Why it is unsupported:
OpenAPI documents 204 for `start scheduler` and `resume scheduler`, but generated tests observe 500 responses. The API exposes no validation or preflight function that explains whether the scheduler can be started or resumed.

Existing functions considered:
- `start scheduler`: is the intended start transition but can return 500 in generated tests.
- `resume scheduler`: is the intended resume transition but can return 500 in generated tests.
- `retrieve scheduler details`: reads current status but does not validate whether start/resume is allowed.
- `pause scheduler`: can pause successfully in tests but does not repair start/resume prerequisites.
- `stop scheduler`: can stop successfully in tests but does not guarantee a later start will succeed.

Missing capability:
Domain-specific scheduler control validation and error mapping, including explicit responses for already running, not initialized, missing runtime resources, or illegal transition.

Proof that function composition is insufficient:
Calling `retrieve scheduler details` before start/resume only reads status; it cannot create missing runtime prerequisites or force the start/resume endpoint to return a domain error. Stop-then-start is not equivalent because generated tests already show start failures under reset-state conditions.

Evidence from existing functions/source:
- Generated tests attribute 500 responses for `start scheduler` and `resume scheduler` to `SchedulerService_25_start`.
- OpenAPI declares only 204 and 404 for those control endpoints.
- No endpoint exposes scheduler health, readiness, or transition validation.

Business impact:
Operational automation cannot safely distinguish transient runtime failures from invalid commands. Start/resume workflows may fail with generic 500s rather than actionable scheduler-state errors.

### Missing Behavior 5: Token revocation or logout

Priority:
Important robustness gap

Expected business goal:
End an authenticated API session or revoke a bearer token.

Why it is unsupported:
Only login is documented as an auth operation. A logout path constant exists in source, but there is no OpenAPI operation or controller evidence for it in the available files.

Existing functions considered:
- `authenticate user`: obtains a token but cannot revoke it.

Missing capability:
A logout or token-revocation endpoint, plus documented token lifetime and invalidation semantics.

Proof that function composition is insufficient:
Not using a token is only a client behavior; it does not invalidate the token server-side. Reauthenticating produces another token and does not revoke earlier credentials.

Evidence from existing functions/source:
- `QuartzManagerPaths` defines `/quartz-manager/auth/logout`.
- `quartz-manager.json` exposes `/quartz-manager/auth/login` but no logout operation.
- `authenticate user` has no documented paired revocation behavior.

Business impact:
The service lacks an API-level session lifecycle close. Compromised or stale tokens cannot be invalidated through the documented REST surface.

### Missing Behavior 6: Self-describing simple-trigger configuration

Priority:
API ergonomics gap

Expected business goal:
Allow API consumers to construct valid simple-trigger create and update payloads from the published contract.

Why it is unsupported:
The OpenAPI references `SimpleTriggerInputDTO`, but `components.schemas` does not define it. Generated tests show empty JSON fails with 400, so validation exists but is not discoverable from the contract.

Existing functions considered:
- `schedule simple trigger`: requires a valid `SimpleTriggerInputDTO` body but the schema is missing.
- `reschedule simple trigger`: requires the same hidden body contract.
- `retrieve simple trigger by name`: could show a returned trigger shape after creation, but it cannot create the initial valid request body.

Missing capability:
Complete OpenAPI schema definitions, examples, field-level validation rules, and response schemas for trigger DTOs and error DTOs.

Proof that function composition is insufficient:
Calling read endpoints cannot reveal the required create body when no trigger exists. Trial-and-error POST/PUT calls cause validation failures and cannot be relied on as contract discovery.

Evidence from existing functions/source:
- OpenAPI references `SimpleTriggerInputDTO`, `SimpleTriggerDTO`, `TriggerDTO`, `TriggerKeyDTO`, `SchedulerDTO`, and `ExceptionResponse`.
- The `components` section defines only `securitySchemes`.
- Generated tests observe 400 for empty JSON bodies submitted to `schedule simple trigger` and `reschedule simple trigger`.

Business impact:
Integrators cannot reliably create or update triggers from the published API specification. This raises integration cost and increases invalid configuration attempts.

### Missing Behavior 7: First-class job lifecycle management

Priority:
API ergonomics gap

Expected business goal:
Register, inspect, update, or remove scheduler jobs as domain resources before binding them to triggers.

Why it is unsupported:
The API only lists eligible job classes. No endpoint creates a job detail, assigns a job class to a trigger, lists scheduled jobs, or deletes a job.

Existing functions considered:
- `list eligible job classes`: discovers Java classes but does not create or manage job instances.
- `schedule simple trigger`: may internally require job-related fields in `SimpleTriggerInputDTO`, but the schema is absent and no job aggregate is exposed.
- `retrieve scheduler details`: reads scheduler metadata and does not manage jobs.

Missing capability:
Job resource endpoints for create, retrieve, list, update, delete, and trigger binding, or a documented trigger payload schema that explicitly covers job identity and job data.

Proof that function composition is insufficient:
A class list is not a persisted job. Creating a trigger with an undocumented body cannot provide a discoverable, manageable job lifecycle or job identity that later operations can reference.

Evidence from existing functions/source:
- OpenAPI exposes only `GET /quartz-manager/jobs` for jobs.
- No path contains a job id, job name, job group, or job data map.
- `list eligible job classes` generated tests return an empty list and do not create state.

Business impact:
The domain model is incomplete for clients that need to manage scheduled work, not only scheduler controls and trigger timing.

### Missing Behavior 8: Ownership, tenancy, and audit trail for scheduler changes

Priority:
API ergonomics gap

Expected business goal:
Restrict and audit who can create, update, or control scheduler resources.

Why it is unsupported:
The exposed endpoints use bearer authentication but no path, body, or response field models owner, tenant, role, permission, or audit event. No audit lookup endpoint is available.

Existing functions considered:
- `authenticate user`: creates caller context but returns no documented role or scope.
- `schedule simple trigger`: creates global trigger state without owner or tenant values.
- `reschedule simple trigger`: updates global trigger state without version, owner, or audit parameters.
- `start scheduler`: changes global scheduler state without actor-specific audit output.
- `stop scheduler`: changes global scheduler state without actor-specific audit output.
- `pause scheduler`: changes global scheduler state without actor-specific audit output.
- `resume scheduler`: changes global scheduler state without actor-specific audit output.

Missing capability:
Role/scope enforcement, ownership fields, tenant scoping, audit event persistence, and audit search endpoints for scheduler and trigger mutations.

Proof that function composition is insufficient:
Passing the same bearer token to mutating endpoints authorizes the call but does not persist or expose ownership/audit records. Reading scheduler or trigger state after a mutation does not reveal who made the change.

Evidence from existing functions/source:
- OpenAPI security uses a single bearer scheme with no documented scopes.
- Paths contain no tenant, owner, or audit identifiers.
- No audit or history endpoints are present.

Business impact:
The API is difficult to operate in multi-user or regulated environments because resource ownership, accountability, and change history are not represented.

## Cross-Behavior Observations

- The service is centered on a singleton scheduler and global trigger namespace. There is no scheduler collection, tenant id, or ownership boundary in the REST shape.
- Authentication is the only cross-cutting value binding. `authenticate user` returns a token in generated tests, and every protected behavior reuses it in `Authorization: Bearer {token}`.
- Simple triggers use caller-supplied path names as durable identifiers. No generated trigger id, version, ETag, or concurrency token is exposed.
- Scheduler control operations are state-changing but use GET, which can surprise caches, crawlers, and clients that assume GET is safe.
- Stop and pause are observed as successful 204 operations in generated tests, while start and resume are observed returning 500 despite OpenAPI documenting 204.
- Trigger list and trigger lookup have weak error mapping. Generated tests show 500 for cases that the contract presents as successful reads or 404 not-found responses.
- OpenAPI DTO references are incomplete because `components.schemas` contains only the security scheme. This is especially harmful for `schedule simple trigger` and `reschedule simple trigger`, where valid request bodies are required.
- Extra query parameters are often ignored by scheduler and job reads/controls, but this tolerance is not documented as part of the domain contract.
- Access control appears to be authentication-only in the visible contract. There are no scopes, roles, owners, tenants, or per-resource authorization checks.
- No cascading delete, recalculation, audit trail, token revocation, trigger-specific pause/resume, or safe cleanup behavior is exposed.

## Coverage Summary

Fully supported workflow/state areas include obtaining an access token, reading scheduler details, listing eligible job classes, stopping the scheduler, and pausing the scheduler, subject to valid authentication and singleton service configuration.

Partially supported areas include starting and resuming the scheduler, listing triggers, retrieving simple triggers, scheduling simple triggers, and rescheduling simple triggers. These are exposed in the contract, but tests reveal 500 responses for several paths and the trigger DTO schemas are missing.

Unsupported or unsafe areas include trigger deletion, trigger-level pause/resume, reliable trigger not-found behavior, safe scheduler start/resume error modeling, token revocation, self-describing trigger payloads, first-class job lifecycle management, and ownership/audit controls.
