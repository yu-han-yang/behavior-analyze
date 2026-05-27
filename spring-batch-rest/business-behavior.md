# Domain-Level Behavior Analysis

## Domain Summary

This service exposes a REST control and inspection surface for Spring Batch jobs. The main domain resources are registered batch jobs and their job executions. A job is identified by name, and an execution is identified by a Spring Batch-generated execution id. The only REST mutation is starting a job execution; job registration, job construction, Quartz scheduling, and repository configuration happen through application wiring or utility classes, not through the documented REST API.

The OpenAPI description mentions Quartz schedules, and the source contains Quartz scheduler utilities, but no schedule REST endpoints are documented in `spring-batch-rest.json` or described as REST functions in `full-behavior.md`.

## Available Function Inventory

### Job discovery

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list registered Spring Batch jobs` | `GET /jobs` | Lists jobs known to the service registry. |
| `get registered job by name` | `GET /jobs/{jobName}` | Retrieves a job resource for a named Spring Batch job. |

### Job execution control

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `start job asynchronously` | `POST /jobExecutions` | Starts a registered job using the asynchronous launcher branch. |
| `start job synchronously` | `POST /jobExecutions` | Starts a registered job using the synchronous launcher branch. |

### Job execution inspection

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list job execution history` | `GET /jobExecutions` | Lists stored job executions globally. |
| `find job executions by job name` | `GET /jobExecutions` | Filters execution history by job name. |
| `find job executions by exit code` | `GET /jobExecutions` | Filters execution history by execution exit code. |
| `find job executions by job name and exit code` | `GET /jobExecutions` | Filters execution history by both job name and exit code. |
| `get job execution by id` | `GET /jobExecutions/{id}` | Retrieves one stored execution by generated execution id. |

## Supported Business Behaviors

### Behavior 1: Discover Available Batch Jobs

Business goal:
Allow an operator or client to see which batch jobs the service can expose.

Domain context:
Job launch and inspection are name-based, so discovering registered job names is the entry point for most workflows.

Starting point:
Application-wired service state. No REST function creates registered jobs.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json` to obtain the collection of available job resources and their `job.name` values.

Optional verification workflow:
None. The behavior itself is a read operation.

Existing-state shortcuts:
- If the caller already knows a valid `jobName`, this discovery step can be skipped for later launch or lookup workflows.
- Direct application wiring, `JobBuilder.registerJob(...)`, or `JobBuilder.createJob(...)` can establish registry state. There is no visible database-only setup path for registered jobs.

Parameter and value bindings:
- Returned `job.name` values are reused as `jobName` path values in `get registered job by name` and as request body `name` values in `start job synchronously` or `start job asynchronously`.

Business result:
The caller has a list of batch job names exposed by the service.

Constraints and invariants:
- The endpoint is global and not scoped by owner, tenant, or schedule.
- The visible sample runtime exposes `personJob`.
- Job registration is not persisted or managed by the REST API.

Failure and exceptional cases:
- Failing function: `list registered Spring Batch jobs`
  - Failure condition: No implementation-backed business failure is documented.
  - Why it fails: The OpenAPI documents only `200`; controller source is not visible.
  - Violated prerequisite or constraint: None confirmed.

Implementation notes:
The OpenAPI documents only a `200` response. Tests show `GET /jobs` returns a HAL collection containing `personJob`.

### Behavior 2: Inspect A Named Batch Job

Business goal:
Allow a caller to inspect a specific job resource by name before launching it.

Domain context:
Launching a job requires submitting its name, so named lookup lets a client confirm the intended job identity.

Starting point:
Application-wired service state with at least one exposed job name.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json` to obtain `jobName=personJob` or another returned job name.
2. Use function `get registered job by name` (`GET /jobs/{jobName}`) with path `jobName=personJob` and `Accept=application/hal+json` to retrieve the named job resource.

Optional verification workflow:
None. The behavior itself is a read operation.

Existing-state shortcuts:
- Step 1 can be skipped if the caller already has a job name that should be accepted by the service.
- Direct app/test registration can provide the same job registry state.

Parameter and value bindings:
- The `job.name` returned by `list registered Spring Batch jobs` is reused as path `jobName` in `get registered job by name`.

Business result:
The caller receives a job resource for the requested name.

Constraints and invariants:
- `full-behavior.md` describes this as a registered-job lookup.
- Generated tests indicate the implementation may return `200` for an arbitrary syntactically acceptable `jobName`, which weakens the registry-existence invariant.
- Malformed path values can fail before domain lookup.

Failure and exceptional cases:
- Failing function: `list registered Spring Batch jobs`
  - Failure condition: The registry is empty and the caller needs an API-discovered name.
  - Why it fails: No job name can be obtained through REST.
  - Violated prerequisite or constraint: A usable job name is required for named lookup.
- Failing function: `get registered job by name`
  - Failure condition: The path contains malformed encoded characters such as a problematic backslash sequence.
  - Why it fails: Tests show `GET /jobs/{jobName}` can return `400` with text/html for malformed path input.
  - Violated prerequisite or constraint: `jobName` must be a path-safe name.
- Failing function: `get registered job by name`
  - Failure condition: A name is not registered.
  - Why it fails: Domain expectation says missing names should not resolve; however tests suggest arbitrary names may still return `200`.
  - Violated prerequisite or constraint: Registry existence is expected but may not be enforced.

Implementation notes:
OpenAPI documents only `200`, but tests show `400` is possible. There is an implementation/OpenAPI discrepancy around both error documentation and apparent registry validation.

### Behavior 3: Start A Batch Job Synchronously

Business goal:
Run a batch job and wait for the launch operation to return a completed or terminal execution resource.

Domain context:
Synchronous execution is meaningful when a client wants immediate completion feedback and can tolerate waiting for the job launcher.

Starting point:
Application-wired service state with a registered launchable job.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json` to obtain `jobName=personJob`.
2. Use function `start job synchronously` (`POST /jobExecutions`) with `Content-Type=application/json`, `Accept=application/hal+json`, body `name=personJob`, `asynchronous=false`, and optional `properties={...}` to start the job and receive `jobExecution.id`, `jobExecution.jobName`, `jobExecution.status`, and `jobExecution.exitCode`.

Optional verification workflow:
1. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={jobExecution.id}` captured from step 2 to inspect the stored execution.
2. Use function `find job executions by job name` (`GET /jobExecutions`) with query `jobName=personJob` to inspect executions for the same job.

Existing-state shortcuts:
- Step 1 can be skipped if `name=personJob` or another valid job name is already known.
- Direct app wiring can establish the job. Direct repository seeding can establish execution history for verification, but it does not replace the core start action for this behavior.

Parameter and value bindings:
- `job.name` from step 1 is reused as body `name` in step 2.
- Body `properties` become Spring Batch job parameters. `Date`, `Long`, and `Double` preserve typed parameter form; other values are stringified.
- If `addUniqueJobParameter=true`, the implementation adds generated parameter `uuid`, so repeated starts of the same job can create distinct job instances.
- The returned `jobExecution.id` can be reused as path `id` in `get job execution by id`.

Business result:
A new job execution is created in the Spring Batch repository. For the sample job, synchronous runs complete with `status=COMPLETED`, `exitCode=COMPLETED`, and an end time.

Constraints and invariants:
- Body `name` must resolve through `JobLocator.getJob(name)`.
- Omitting `asynchronous` is equivalent to `asynchronous=false` because the Java boolean defaults to false.
- No owner, tenant, or authorization scope is enforced by the visible REST contract.
- OpenAPI declares date-time fields as strings with date-time format, but observed responses omit timezone and may violate the schema.

Failure and exceptional cases:
- Failing function: `list registered Spring Batch jobs`
  - Failure condition: No jobs are registered or exposed.
  - Why it fails: The workflow cannot obtain a valid body `name`.
  - Violated prerequisite or constraint: A launchable job name is required.
- Failing function: `start job synchronously`
  - Failure condition: Body omits `name` or sets `name=null`.
  - Why it fails: `AdHocStarter.start(JobConfig)` calls `JobLocator.getJob(jobConfig.getName())`; tests show this returns `500`.
  - Violated prerequisite or constraint: Request body `name` is required by implementation, although not marked required by OpenAPI.
- Failing function: `start job synchronously`
  - Failure condition: Body `name` is unknown.
  - Why it fails: `JobLocator` raises `NoSuchJobException`; tests show `404`.
  - Violated prerequisite or constraint: The job must be registered.
- Failing function: `start job synchronously`
  - Failure condition: Job parameters or repository state violate Spring Batch launch rules.
  - Why it fails: `JobLauncher.run(...)` can throw `JobExecutionException`, wrapped as a batch runtime failure.
  - Violated prerequisite or constraint: Spring Batch job parameter, restart, already-running, or already-complete rules.

Implementation notes:
`AdHocStarter` selects a `SyncTaskExecutor` when `asynchronous=false`. The OpenAPI documents only `200`, but implementation/tests show `400`, `404`, and `500` outcomes.

### Behavior 4: Submit A Batch Job Asynchronously

Business goal:
Start a batch job without waiting for completion.

Domain context:
Asynchronous launch supports long-running jobs where the caller wants an execution handle and will inspect status later.

Starting point:
Application-wired service state with a registered launchable job.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json` to obtain `jobName=personJob`.
2. Use function `start job asynchronously` (`POST /jobExecutions`) with `Content-Type=application/json`, `Accept=application/hal+json`, body `name=personJob`, `asynchronous=true`, and optional `properties={...}` to submit the job and receive a job execution resource.

Optional verification workflow:
1. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={jobExecution.id}` captured from step 2 to poll or inspect later state.
2. Use function `find job executions by job name` (`GET /jobExecutions`) with query `jobName=personJob` to inspect later executions for the job.

Existing-state shortcuts:
- Step 1 can be skipped if the job name is already known.
- Direct app/test wiring can register the job.
- Existing execution history can be seeded for later inspection, but the async submission itself must still be performed for this behavior.

Parameter and value bindings:
- `job.name` from step 1 is reused as body `name`.
- `asynchronous=true` selects the async launcher.
- The response execution id, if present, is reused as path `id` in later retrieval.
- Body `properties` are converted to job parameters and can affect job logic.

Business result:
A job execution is created and started. Immediately returned async executions may have `status=STARTING`, `exitCode=UNKNOWN`, and null `startTime` or `endTime`.

Constraints and invariants:
- Body `name` must resolve to a registered job.
- Asynchronous status and exit code are time-dependent.
- Deprecated `JobPropertyResolvers.JobProperties` is unsafe for concurrent asynchronous executions of the same job with different properties.

Failure and exceptional cases:
- Failing function: `start job asynchronously`
  - Failure condition: Body `name` is missing or null.
  - Why it fails: `AdHocStarter` attempts to resolve a null job name and wraps the failure; tests show `500`.
  - Violated prerequisite or constraint: A concrete job name is required.
- Failing function: `start job asynchronously`
  - Failure condition: Body `name` is not registered.
  - Why it fails: `JobLocator.getJob(name)` raises `NoSuchJobException`; tests show `404`.
  - Violated prerequisite or constraint: The requested job must exist in the registry.
- Failing function: `start job asynchronously`
  - Failure condition: Multiple concurrent async executions of the same job use different property maps and job code reads through the deprecated singleton resolver.
  - Why it fails: Resolvers are keyed by job name, so one execution can see another execution's properties.
  - Violated prerequisite or constraint: Per-execution properties require execution-scoped access, not job-name-scoped singleton lookup.

Implementation notes:
`AdHocStarter` selects `SimpleAsyncTaskExecutor` when `asynchronous=true`. OpenAPI does not mark nullable response dates, but async responses can contain null timestamps.

### Behavior 5: Review Global Job Execution History

Business goal:
Allow an operator to view recent or stored executions across jobs.

Domain context:
Execution history is the audit trail for job starts, status, exit code, and failure information.

Starting point:
No prior REST-created execution state is required; the result may be empty.

Required execution workflow:
1. Use function `list job execution history` (`GET /jobExecutions`) with optional query `limitPerJob={limitPerJob}` and `Accept=application/hal+json` to retrieve the execution collection.

Optional verification workflow:
None. The behavior itself is a read operation.

Existing-state shortcuts:
- No setup is required for an empty or current history view.
- Direct Spring Batch metadata seeding or prior calls to `start job synchronously` or `start job asynchronously` can make the result non-empty.

Parameter and value bindings:
- `limitPerJob` controls how many executions are returned per job, when the controller accepts it.
- Returned `jobExecution.id` values can be reused in `get job execution by id`.
- Returned `jobExecution.jobName` and `jobExecution.exitCode` values can be reused in filtered search functions.

Business result:
The caller receives a collection of execution records, typically including job name, id, start/end times, status, exit code, exit description, and exceptions.

Constraints and invariants:
- The repository is Spring Batch metadata; visible config overrides the datasource and uses a map-based repository in `AdHocBatchConfig`.
- OpenAPI says `limitPerJob` defaults to `3`.
- Negative or malformed query values are not consistently validated.

Failure and exceptional cases:
- Failing function: `list job execution history`
  - Failure condition: `limitPerJob` is negative in some query combinations.
  - Why it fails: Tests show an `IllegalArgumentException` and `500` for `jobName=` with `limitPerJob=-3643`.
  - Violated prerequisite or constraint: `limitPerJob` should be non-negative, but this is not declared in OpenAPI.
- Failing function: `list job execution history`
  - Failure condition: A query value is interpreted as an invalid regex in filter logic.
  - Why it fails: Tests show `PatternSyntaxException` for a job name containing an unclosed group.
  - Violated prerequisite or constraint: Filter strings must be regex-safe in implementation, although the API exposes them as plain strings.

Implementation notes:
OpenAPI documents only `200`. Tests show `500` responses for some query combinations and response date-time schema mismatches.

### Behavior 6: Retrieve A Specific Execution Record

Business goal:
Inspect the persisted state of one job execution.

Domain context:
A client that starts a job or sees an execution in history needs a stable id-based lookup.

Starting point:
Application-wired service state with a launchable job, so the workflow can create an execution id through the API.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json` to obtain `jobName=personJob`.
2. Use function `start job synchronously` (`POST /jobExecutions`) with body `name=personJob`, `asynchronous=false`, and optional `properties={...}` to create an execution and capture `jobExecution.id`.
3. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={jobExecution.id}` and `Accept=application/hal+json` to retrieve that execution record.

Optional verification workflow:
1. Use function `list job execution history` (`GET /jobExecutions`) with `limitPerJob=3` to verify that the same execution appears in history.

Existing-state shortcuts:
- Step 1 can be skipped if the job name is known.
- Steps 1 and 2 can be skipped if an equivalent execution already exists and its generated `jobExecution.id` is known.
- Direct Spring Batch metadata seeding can create an execution id, but the id must match the path value used in step 3.

Parameter and value bindings:
- `job.name` from step 1 is reused as POST body `name`.
- The generated `jobExecution.id` from step 2 is reused as path `id` in step 3.
- The returned `jobExecution.jobName` should match the POST body `name`.

Business result:
The caller receives one execution record for the selected generated id.

Constraints and invariants:
- `id` is a numeric Spring Batch execution id.
- Id lookup is not scoped by job name, owner, or tenant.
- No API exists to update or delete the execution record.

Failure and exceptional cases:
- Failing function: `start job synchronously`
  - Failure condition: Unknown or null job name.
  - Why it fails: The job cannot be resolved by `JobLocator`.
  - Violated prerequisite or constraint: A registered job is required to create an execution id.
- Failing function: `get job execution by id`
  - Failure condition: No execution exists for `id`.
  - Why it fails: Tests show `NoSuchJobExecutionException` mapped to `404`.
  - Violated prerequisite or constraint: The path id must identify persisted Spring Batch metadata.
- Failing function: `get job execution by id`
  - Failure condition: Path `id` is non-numeric.
  - Why it fails: The path variable is declared as `int64`, so Spring request binding should reject it.
  - Violated prerequisite or constraint: `id` must be parseable as a long.

Implementation notes:
OpenAPI documents only `200`, but tests show `404`. Responses may violate the declared date-time format.

### Behavior 7: Review Executions For One Job

Business goal:
Inspect execution history for a specific batch job.

Domain context:
Operators commonly need to answer “what happened to this job recently?” rather than viewing all jobs.

Starting point:
Application-wired service state with a launchable named job.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json` to obtain `jobName=personJob`.
2. Use function `start job synchronously` (`POST /jobExecutions`) with body `name=personJob`, `asynchronous=false`, and optional `properties={...}` to create at least one execution for that job.
3. Use function `find job executions by job name` (`GET /jobExecutions`) with query `jobName=personJob`, optional `limitPerJob={limitPerJob}`, and `Accept=application/hal+json` to retrieve executions for that job.

Optional verification workflow:
1. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={jobExecution.id}` captured from step 2 to inspect one returned execution.

Existing-state shortcuts:
- Step 1 can be skipped if the job name is already known.
- Step 2 can be skipped if execution history for `jobName=personJob` already exists.
- Direct Spring Batch metadata seeding can replace step 2, but seeded records must use the same `jobExecution.jobName` consumed by the query.

Parameter and value bindings:
- The same `personJob` value is used as POST body `name` and later as query `jobName`.
- `limitPerJob` limits result count for each job.
- Returned execution ids can be reused for id lookup.

Business result:
The caller receives execution records whose stored `jobExecution.jobName` matches the requested job name.

Constraints and invariants:
- Query `jobName` must match stored execution job names.
- Empty `jobName` may behave like an unscoped or broad search in observed tests.
- Filter implementation appears regex-sensitive.

Failure and exceptional cases:
- Failing function: `start job synchronously`
  - Failure condition: The selected job cannot be launched.
  - Why it fails: `JobLocator` or `JobLauncher` raises an exception.
  - Violated prerequisite or constraint: A launchable registered job is required for API-created history.
- Failing function: `find job executions by job name`
  - Failure condition: Query `jobName` differs from the stored execution job name.
  - Why it fails: The filter excludes non-matching executions.
  - Violated prerequisite or constraint: The query must reuse the stored job name.
- Failing function: `find job executions by job name`
  - Failure condition: Query `jobName` contains regex metacharacters that form an invalid pattern.
  - Why it fails: Tests show `PatternSyntaxException`.
  - Violated prerequisite or constraint: The implementation requires regex-safe filter text, though OpenAPI does not state that.

Implementation notes:
The behavior is supported, but filter semantics are weaker than a normal exact-match string filter because invalid regex-like names can fail.

### Behavior 8: Review Executions By Exit Code

Business goal:
Find executions with a particular outcome, such as `COMPLETED`, `FAILED`, or `UNKNOWN`.

Domain context:
Exit-code filtering supports operational monitoring and failure triage.

Starting point:
Application-wired service state with a launchable job so the workflow can create a known exit code.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json` to obtain `jobName=personJob`.
2. Use function `start job synchronously` (`POST /jobExecutions`) with body `name=personJob`, `asynchronous=false`, and optional `properties={...}` to create an execution and capture `jobExecution.exitCode=COMPLETED` or the actual returned value.
3. Use function `find job executions by exit code` (`GET /jobExecutions`) with query `exitCode={captured jobExecution.exitCode}`, optional `limitPerJob={limitPerJob}`, and `Accept=application/hal+json` to retrieve executions with that outcome.

Optional verification workflow:
1. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={jobExecution.id}` from step 2 to confirm the selected execution's exit code.

Existing-state shortcuts:
- Step 2 can be skipped if an execution with the desired `exitCode` already exists.
- Direct metadata seeding can replace step 2, but the seeded `jobExecution.exitCode` must exactly match the query value.
- For async executions, wait until the execution has reached the desired terminal exit code before using a terminal query such as `exitCode=COMPLETED`.

Parameter and value bindings:
- The returned `jobExecution.exitCode` from step 2 is reused as query `exitCode` in step 3.
- If using async setup instead of sync, the initially returned `UNKNOWN` value may differ from the later terminal value.

Business result:
The caller receives execution records whose stored exit code matches the query.

Constraints and invariants:
- Exit code is stored on the execution record and is time-dependent for async jobs.
- Query strings are not constrained to the enum-like values in the response schema; arbitrary strings can return empty results.

Failure and exceptional cases:
- Failing function: `find job executions by exit code`
  - Failure condition: Query `exitCode` differs from the stored execution exit code.
  - Why it fails: The filter excludes executions with different outcomes.
  - Violated prerequisite or constraint: The query must reuse the stored exit code.
- Failing function: `find job executions by exit code`
  - Failure condition: The execution was just submitted asynchronously and the query uses `exitCode=COMPLETED`.
  - Why it fails: The stored exit code may still be `UNKNOWN`.
  - Violated prerequisite or constraint: Terminal outcome must exist before terminal filtering.
- Failing function: `start job synchronously`
  - Failure condition: Job launch fails before an execution with the desired exit code is created.
  - Why it fails: Spring Batch launch exceptions prevent usable setup state.
  - Violated prerequisite or constraint: A valid registered job and acceptable parameters are required.

Implementation notes:
Tests show no-match exit-code queries can return `200`, but OpenAPI does not document empty-result semantics.

### Behavior 9: Review A Job's Executions By Outcome

Business goal:
Find executions for a specific job that ended with a specific exit code.

Domain context:
This combines job-scoped audit with outcome filtering, which is the most useful read model for operational triage.

Starting point:
Application-wired service state with a launchable named job.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json` to obtain `jobName=personJob`.
2. Use function `start job synchronously` (`POST /jobExecutions`) with body `name=personJob`, `asynchronous=false`, and optional `properties={...}` to create an execution and capture `jobExecution.exitCode`.
3. Use function `find job executions by job name and exit code` (`GET /jobExecutions`) with query `jobName=personJob`, query `exitCode={captured jobExecution.exitCode}`, optional `limitPerJob={limitPerJob}`, and `Accept=application/hal+json` to retrieve matching executions.

Optional verification workflow:
1. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={jobExecution.id}` from step 2 to inspect one matching execution.

Existing-state shortcuts:
- Step 1 can be skipped if `jobName` is known.
- Step 2 can be skipped if a matching execution already exists.
- Direct repository seeding can replace step 2, but both seeded `jobExecution.jobName` and `jobExecution.exitCode` must match the query values.

Parameter and value bindings:
- POST body `name=personJob` is reused as query `jobName=personJob`.
- Returned `jobExecution.exitCode` is reused as query `exitCode`.
- Returned `jobExecution.id` can be reused in id lookup.

Business result:
The caller receives execution records satisfying both job-name and exit-code filters.

Constraints and invariants:
- Both filters must match the same stored execution.
- A matching job name with a different exit code is excluded.
- A matching exit code for a different job is excluded.
- Filter values may be regex-sensitive in implementation.

Failure and exceptional cases:
- Failing function: `find job executions by job name and exit code`
  - Failure condition: Query `jobName` does not equal the stored execution job name.
  - Why it fails: The job-name filter excludes the execution.
  - Violated prerequisite or constraint: Query job name must reuse the stored job name.
- Failing function: `find job executions by job name and exit code`
  - Failure condition: Query `exitCode` does not equal the stored execution exit code.
  - Why it fails: The exit-code filter excludes the execution.
  - Violated prerequisite or constraint: Query exit code must reuse the stored exit code.
- Failing function: `find job executions by job name and exit code`
  - Failure condition: `jobName` contains invalid regex syntax.
  - Why it fails: Tests show `PatternSyntaxException` and `500`.
  - Violated prerequisite or constraint: The implementation requires regex-safe filter text.

Implementation notes:
This behavior is API-supported, but error semantics are underdocumented and query validation is weak.

### Behavior 10: Launch Then Audit A Batch Run End To End

Business goal:
Perform a complete operational workflow: discover a job, run it, retrieve the generated execution, and find it in history.

Domain context:
This is the natural client workflow for “run this batch job and verify the recorded result.”

Starting point:
Application-wired service state with a launchable job.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json` to obtain `jobName=personJob`.
2. Use function `start job synchronously` (`POST /jobExecutions`) with body `name=personJob`, `asynchronous=false`, and optional `properties={...}` to create a completed execution and capture `jobExecution.id` and `jobExecution.exitCode`.
3. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={jobExecution.id}` to retrieve the created execution.
4. Use function `find job executions by job name and exit code` (`GET /jobExecutions`) with query `jobName=personJob` and `exitCode={captured jobExecution.exitCode}` to find the same run in filtered history.

Optional verification workflow:
1. Use function `list job execution history` (`GET /jobExecutions`) with `limitPerJob=3` to inspect the broader execution list.

Existing-state shortcuts:
- Step 1 can be skipped if the job name is known.
- Step 3 can be skipped if the POST response already contains all execution details needed by the client.
- Step 4 can be skipped if filtered audit is not required.
- Existing execution metadata cannot replace step 2 when the business goal is to launch a new run.

Parameter and value bindings:
- `jobName=personJob` flows from discovery to POST body `name`, then to query `jobName`.
- Generated `jobExecution.id` flows from POST response to path `id`.
- Returned `jobExecution.exitCode` flows from POST response to query `exitCode`.

Business result:
A new execution exists and can be inspected by id and found in filtered history.

Constraints and invariants:
- The workflow depends on generated execution id reuse.
- The execution record is global, not owner-scoped.
- For asynchronous launch, step 4 must account for temporary `UNKNOWN` exit code; this synchronous workflow avoids that timing issue.

Failure and exceptional cases:
- Failing function: `list registered Spring Batch jobs`
  - Failure condition: No discoverable job exists.
  - Why it fails: The workflow cannot obtain a valid launch name.
  - Violated prerequisite or constraint: A launchable job must exist.
- Failing function: `start job synchronously`
  - Failure condition: Unknown job, missing name, or invalid launch parameters.
  - Why it fails: `AdHocStarter` resolves the job and delegates to Spring Batch, both of which can reject the launch.
  - Violated prerequisite or constraint: Valid job identity and Spring Batch launch rules.
- Failing function: `get job execution by id`
  - Failure condition: The path id is not the generated id from the launch response.
  - Why it fails: Id lookup retrieves another execution or returns `404`.
  - Violated prerequisite or constraint: Generated id must be reused exactly.
- Failing function: `find job executions by job name and exit code`
  - Failure condition: Query values do not match the stored execution values.
  - Why it fails: The filters exclude the launched execution.
  - Violated prerequisite or constraint: Job name and exit code must be bound from the created execution.

Implementation notes:
This is the strongest fully API-realizable workflow, provided job registration already exists through application wiring. Job registration itself is not API-realizable.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Register Or Manage Batch Jobs Through REST

Priority:
Critical domain gap

Expected business goal:
A client should be able to create, register, update, rename, enable, disable, or delete batch job definitions through the API.

Why it is unsupported:
No available REST function creates or mutates job registry entries.

Existing functions considered:
- `list registered Spring Batch jobs`: can list current registry entries but cannot create them.
- `get registered job by name`: can retrieve or synthesize a named job resource but cannot register executable job logic.
- `start job synchronously`: can launch only a job resolvable by `JobLocator`.
- `start job asynchronously`: can launch only a job resolvable by `JobLocator`.

Missing capability:
A REST endpoint and data model for registering job definitions, binding steps/tasklets, validating job names, updating definitions, and removing jobs.

Proof that function composition is insufficient:
Listing and retrieving jobs do not write registry state. Starting an unknown job fails because `JobLocator.getJob(name)` cannot resolve it. Delete-and-recreate is not possible because there is no REST create or delete.

Evidence from existing functions/source:
`AdHocStarter.start(JobConfig)` calls `jobLocator.getJob(jobConfig.getName())`. `JobBuilder.registerJob(...)` exists only as application/source utility code, not as a REST function.

Business impact:
Clients cannot onboard new jobs or manage job lifecycle through the documented API.

### Missing Behavior 2: Stop, Restart, Abandon, Or Resume Executions

Priority:
Critical domain gap

Expected business goal:
Operators should be able to stop a running execution, restart a failed execution, abandon an execution, or resume supported Spring Batch workflows.

Why it is unsupported:
The REST API only starts executions and reads execution history.

Existing functions considered:
- `start job asynchronously`: starts a run but cannot stop it.
- `start job synchronously`: starts a run but cannot restart a previous execution.
- `get job execution by id`: reads execution state only.
- `list job execution history`: reads execution history only.

Missing capability:
Execution control endpoints such as stop, restart, abandon, or resume, with state validation against Spring Batch execution status.

Proof that function composition is insufficient:
Starting a new job creates a new execution; it does not mutate the status of an existing execution. Reading by id cannot transition state. No function consumes an existing execution id as a control target.

Evidence from existing functions/source:
`POST /jobExecutions` accepts `JobConfig` with `name`, `properties`, and `asynchronous`; it does not accept an existing execution id or control command.

Business impact:
Long-running, failed, or stuck executions cannot be controlled through the API.

### Missing Behavior 3: Manage Quartz Schedules Through REST

Priority:
Critical domain gap

Expected business goal:
A client should be able to create, inspect, pause, resume, update, or delete schedules for batch jobs.

Why it is unsupported:
The OpenAPI description mentions Quartz schedules and the source has `AdHocScheduler`, but `full-behavior.md` and `spring-batch-rest.json` expose no schedule endpoints.

Existing functions considered:
- `list registered Spring Batch jobs`: identifies jobs but not schedules.
- `start job synchronously`: runs immediately, not on a schedule.
- `start job asynchronously`: runs immediately, not on a schedule.
- `list job execution history`: shows executions after they happen, not schedule definitions.

Missing capability:
REST endpoints for schedule lifecycle and scheduler control, including cron/date trigger creation, schedule listing, pause/resume, unschedule, and schedule status.

Proof that function composition is insufficient:
Immediate execution cannot create future or recurring triggers. Execution history cannot reveal intended future schedules. No REST function accepts cron expressions or run dates.

Evidence from existing functions/source:
`AdHocScheduler.schedule(...)`, `start()`, `pause()`, `resume()`, and `stop()` exist in source, but they are not part of the REST function inventory.

Business impact:
The service description promises schedule control, but API clients cannot manage schedules.

### Missing Behavior 4: Safe, Validated Execution Search

Priority:
Important robustness gap

Expected business goal:
Clients should be able to search executions by plain job name and exit code without regex errors or inconsistent limit behavior.

Why it is unsupported:
The available search functions accept string query values but do not reliably validate or treat them as safe literals.

Existing functions considered:
- `find job executions by job name`: can filter by job name but appears regex-sensitive.
- `find job executions by exit code`: can filter by exit code but does not enforce known values.
- `find job executions by job name and exit code`: combines the same weak filters.
- `list job execution history`: accepts `limitPerJob` but negative values can produce inconsistent results.

Missing capability:
Input validation and literal string filtering for `jobName`, explicit constraints for `limitPerJob`, and documented error responses.

Proof that function composition is insufficient:
No existing function sanitizes a job name before passing it to the search implementation. Retrying with modified text changes the search semantics and cannot represent a real job name containing regex metacharacters. Negative limit behavior cannot be repaired by another endpoint.

Evidence from existing functions/source:
Generated tests show `PatternSyntaxException` for a `jobName` containing an unclosed group and `IllegalArgumentException` for a negative `limitPerJob` in some combinations. OpenAPI documents none of those errors.

Business impact:
Operational search can fail for valid-looking names and can return server errors instead of client validation errors.

### Missing Behavior 5: Inspect Or Search Execution Parameters

Priority:
Important robustness gap

Expected business goal:
Clients should be able to see which parameters were used for a job execution and search or audit runs by parameter values.

Why it is unsupported:
Execution resources expose id, job id, job name, timestamps, exit code, status, and exceptions, but not job parameters.

Existing functions considered:
- `start job synchronously`: accepts `properties` and converts them to job parameters.
- `start job asynchronously`: accepts `properties` and converts them to job parameters.
- `get job execution by id`: returns execution status but not the submitted parameters.
- `find job executions by job name`: can filter by job name only.
- `find job executions by exit code`: can filter by outcome only.

Missing capability:
Execution parameter persistence in the REST representation and query filters for parameter keys or values.

Proof that function composition is insufficient:
The POST response and GET responses do not expose the original `properties` map or converted Spring Batch parameters. Once the start request is completed, no REST function can recover or search those values.

Evidence from existing functions/source:
`JobParamUtil` converts raw properties to `JobParameter`; the OpenAPI `JobExecution` schema does not include parameters.

Business impact:
Auditing and troubleshooting are weakened because clients cannot prove which inputs produced a given execution.

### Missing Behavior 6: Purge Or Retain Execution History

Priority:
API ergonomics gap

Expected business goal:
Operators may need to delete old execution records, purge failed test runs, or enforce retention policies.

Why it is unsupported:
All execution-history functions are read-only except starting new executions, which only adds records.

Existing functions considered:
- `list job execution history`: reads stored records.
- `get job execution by id`: reads one record.
- `find job executions by job name`: reads filtered records.
- `find job executions by exit code`: reads filtered records.
- `find job executions by job name and exit code`: reads filtered records.

Missing capability:
Delete, purge, archive, or retention-management endpoints for Spring Batch metadata.

Proof that function composition is insufficient:
Starting more executions cannot remove old ones. Reading by id or filter cannot mutate repository metadata. Direct database cleanup is outside the REST API and is not equivalent to a governed business operation.

Evidence from existing functions/source:
No DELETE endpoint exists in OpenAPI. `AdHocBatchConfig` uses Spring Batch repository infrastructure, but no REST function exposes cleanup.

Business impact:
Execution history can grow or remain polluted without an API-managed cleanup path.

### Missing Behavior 7: Authenticated Or Scoped Batch Administration

Priority:
Important robustness gap

Expected business goal:
Administrative operations should be scoped by authenticated user, tenant, role, or ownership where the deployment domain requires it.

Why it is unsupported:
The OpenAPI and visible behavior contain no authentication, authorization, ownership, or tenant parameters.

Existing functions considered:
- `list registered Spring Batch jobs`: globally lists jobs.
- `get registered job by name`: globally addresses job names.
- `start job synchronously`: launches any resolvable job name.
- `start job asynchronously`: launches any resolvable job name.
- `get job execution by id`: globally retrieves by numeric id.

Missing capability:
Authentication requirements, authorization checks, tenant or owner scoping, and scoped identifiers.

Proof that function composition is insufficient:
No available function establishes an identity or ownership relationship. Headers are not documented for auth. Id and name lookups are global, so chaining functions cannot restrict access.

Evidence from existing functions/source:
OpenAPI declares no security schemes and no owner/tenant fields in schemas.

Business impact:
In multi-user or multi-tenant deployments, job discovery, execution, and history access would be unsafe or ambiguous.

## Cross-Behavior Observations

- Job registration is outside the REST API. Most meaningful workflows depend on application-wired jobs such as `personJob`.
- The API is execution-centric: it can start jobs and inspect execution records, but cannot manage job definitions, schedules, or execution control transitions.
- OpenAPI documents only `200` responses for core endpoints, while tests show `400`, `404`, and `500`.
- Query validation is weak. Some job-name filters appear regex-sensitive, and negative `limitPerJob` can cause server errors.
- Response schemas are not fully accurate: timestamp fields may be null for async starts and may not match the documented date-time format.
- The implementation adds a generated `uuid` job parameter by default, making repeated starts distinct.
- Asynchronous property resolution through the deprecated singleton resolver is unsafe for concurrent runs of the same job with different properties.
- There is no visible authentication, authorization, ownership, or tenant scoping.

## Coverage Summary

Supported domain areas:
- Listing exposed Spring Batch jobs.
- Retrieving a named job resource.
- Starting synchronous and asynchronous job executions.
- Listing and filtering execution history by job name and exit code.
- Retrieving a job execution by generated id.

Partially supported domain areas:
- Operational audit is supported at status/exit-code level but not parameter level.
- Async monitoring is possible through execution lookup, but terminal-state timing is caller-managed.
- Job lookup exists, but registry validation appears inconsistent with the domain description.

Unsupported domain areas:
- REST job registration and lifecycle management.
- Execution stop, restart, abandon, or resume.
- REST Quartz schedule management.
- Safe validated search semantics.
- Execution parameter audit/search.
- Retention or purge management.
- Authenticated or scoped administration.