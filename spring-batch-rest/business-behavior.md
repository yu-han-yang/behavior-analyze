# Domain-Level Workflow and State Behavior Analysis

| No. | Behavior name | Business goal |
|---:|---|---|
| 1 | [Behavior 1: Discover registered batch jobs](#behavior-1) | Find the batch jobs currently exposed by the service. |
| 2 | [Behavior 2: Inspect a named batch job](#behavior-2) | Retrieve the domain resource for one job name. |
| 3 | [Behavior 3: Start a synchronous batch execution](#behavior-3) | Run a registered job and receive the returned execution state. |
| 4 | [Behavior 4: Submit an asynchronous batch execution](#behavior-4) | Start a registered job without waiting for completion. |
| 5 | [Behavior 5: Review global execution history](#behavior-5) | View stored job executions across jobs. |
| 6 | [Behavior 6: Retrieve a specific execution record](#behavior-6) | Inspect one generated execution by id. |
| 7 | [Behavior 7: Review executions for one job](#behavior-7) | View execution history scoped to one job name. |
| 8 | [Behavior 8: Review executions by outcome](#behavior-8) | Find executions with a selected exit code. |
| 9 | [Behavior 9: Review one job's executions by outcome](#behavior-9) | Find executions matching both job name and exit code. |

## Domain Summary

This service exposes a REST control and inspection surface for Spring Batch. The main aggregate roots are registered jobs, identified by job name, and job executions, identified by Spring Batch-generated execution ids. The central lifecycle transition is creating a job execution from a registered job. Execution records then move through Spring Batch states such as STARTING, STARTED, COMPLETED, FAILED, STOPPED, ABANDONED, and UNKNOWN. The REST API can start executions and read execution history, but job registration, job definition construction, Quartz scheduling, and repository configuration are established outside the documented REST surface.

The OpenAPI description mentions Quartz schedules, and the source contains Quartz utilities, but no schedule endpoint appears in `spring-batch-rest.json` or in the extracted function list. The visible implementation also lacks the REST controller source, so controller-level read and error behavior is verified mainly from OpenAPI, `full-behavior.md`, and generated tests. Launch and parameter behavior is implementation-backed by `AdHocStarter`, `JobConfig`, `JobParamUtil`, `JobPropertyResolvers`, and Spring Batch repository configuration.

## Supported Business Behaviors

<a id="behavior-1"></a>
### Behavior 1: Discover registered batch jobs

Business goal:
Allow an operator or client to discover which Spring Batch jobs the service can expose for lookup or launch.

API group boundary:
This is an atomic read behavior. The single function is itself the registry discovery workflow over the global job registry.

Domain context:
All launch workflows require a job name. The registry list is the REST-visible way to obtain names that can be reused in job lookup or execution creation.

Starting point:
Pre-existing service/upstream state required. Registered jobs must already exist through application wiring, `JobBuilder.registerJob(...)`, `JobBuilder.createJob(...)`, or test fixture setup.

State transition summary:
- State before: The in-memory Spring Batch `JobRegistry` may contain zero or more registered jobs.
- Transition trigger: A caller requests the global job collection.
- Intermediate states: No persisted service state changes.
- State after: Registry state is unchanged, and the caller has a HAL collection of job resources.
- Invalid or blocked transitions: No implementation-backed business failure is documented; malformed request negotiation may still fail at the HTTP layer.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json`, no path values, no query values, no body, and the caller's ordinary HTTP context to return registered job resources and their `job.name` values.

Optional verification workflow:
None.

Existing-state shortcuts:
- If a caller already knows a valid job name in the same service instance, this discovery step can be skipped for later lookup or launch behaviors.
- Direct application or test registration can establish equivalent registry state.
- There is no visible database-only setup path for job registration because the visible configuration uses registry wiring rather than persisted job-definition rows.

Parameter and value bindings:
- Returned `job.name` values can be reused as path `jobName` in `get registered job by name`.
- Returned `job.name` values can be reused as request body `name` in `start job synchronously` and `start job asynchronously`.
- The behavior is global; there is no tenant, owner, role, or caller-scope binding in the OpenAPI contract.

Business result:
The caller obtains the exposed job-name set. No job execution is created, no job definition is changed, and no registry membership is added or removed.

Constraints and invariants:
- The endpoint is not scoped by job, tenant, schedule, or user.
- OpenAPI documents a `200` HAL collection response only.
- The visible source includes registry helpers but not the REST controller implementation.
- Generated tests show the sample service exposes `personJob`.

Failure and exceptional cases:
None.

Implementation notes:
The behavior depends on pre-existing Spring application wiring. Unknown query parameters are tolerated in generated tests, and OpenAPI documents no non-200 responses.

<a id="behavior-2"></a>
### Behavior 2: Inspect a named batch job

Business goal:
Retrieve the resource representation for a specific batch job name before launching or auditing it.

API group boundary:
The functions share the job-name aggregate key. `list registered Spring Batch jobs` returns a `job.name` value that is consumed as the path value by `get registered job by name`.

Domain context:
Named inspection confirms the job identity that a caller intends to use in later execution workflows.

Starting point:
Pre-existing service/upstream state required. A usable job name must exist through application wiring or equivalent registry setup.

State transition summary:
- State before: A job name is expected to be present in the registry.
- Transition trigger: The caller resolves the name and requests the named job resource.
- Intermediate states: No registry or execution state changes.
- State after: The caller has a job resource for the path-scoped job name.
- Invalid or blocked transitions: Missing registry entries should be blocked by domain expectation, but generated tests show arbitrary path names may return `200`; malformed path encoding can return `400`.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json`, no path values, no query values, and no body to obtain a returned `job.name` value.
2. Use function `get registered job by name` (`GET /jobs/{jobName}`) with path `jobName={job.name from step 1}`, `Accept=application/hal+json`, no query values, and no body to retrieve that named job resource.

Optional verification workflow:
None.

Existing-state shortcuts:
- Step 1 can be skipped if the caller already has a service-valid job name in the same registry scope.
- Direct application registration through `JobBuilder.registerJob(...)` or `JobBuilder.createJob(...)` can replace REST discovery.
- The path value must still be path-safe and refer to the same service registry context.

Parameter and value bindings:
- The `job.name` returned by `list registered Spring Batch jobs` is reused exactly as path `jobName` in `get registered job by name`.
- Caller identity is not bound to the job resource; OpenAPI declares no security schemes or owner fields.

Business result:
The caller receives a job resource for the requested name. Persisted service state is unchanged. If the implementation returns a job resource for a name not actually registered, the response represents a weak or synthetic lookup rather than a proven launchable job.

Constraints and invariants:
- Domain expectation from `full-behavior.md` is that `jobName` identifies a registered job.
- Generated tests indicate this invariant may not be enforced for named job lookup.
- A job resource retrieved by name is not sufficient proof that `POST /jobExecutions` can launch the same name; launch still resolves through `JobLocator.getJob(name)`.

Failure and exceptional cases:
None.

Implementation notes:
OpenAPI documents only `200`. Generated tests expose an implementation/OpenAPI discrepancy: `GET /jobs/{jobName}` can return `400`, and registry validation appears weaker than launch validation.

<a id="behavior-3"></a>
### Behavior 3: Start a synchronous batch execution

Business goal:
Run a registered batch job through the synchronous launcher and receive the resulting execution resource after the launcher returns.

API group boundary:
The functions share the job-name lifecycle key. `list registered Spring Batch jobs` supplies the request body `name` consumed by `start job synchronously`, and the POST creates a new Spring Batch execution.

Domain context:
Synchronous launch is the core state transition from a registered job definition to a stored execution record with a terminal or returned execution status.

Starting point:
Pre-existing service/upstream state required. The job must already be registered and resolvable by `JobLocator`.

State transition summary:
- State before: A registered job exists; no new execution for this request exists yet.
- Transition trigger: A caller posts a `JobConfig` with `name` and `asynchronous=false` or omits `asynchronous`.
- Intermediate states: `AdHocStarter` resolves the job, enables property resolution, converts `properties` to Spring Batch parameters, optionally adds `uuid`, and calls the synchronous `JobLauncher`.
- State after: A new execution exists in the Spring Batch repository with generated execution id, job name, parameters, status, exit code, timestamps, and exceptions.
- Invalid or blocked transitions: Missing name, unknown job name, invalid launch parameters, already-running, already-complete, restart, repository, or job validation errors block launch.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json`, no path values, no query values, and no body to obtain a returned `job.name` value.
2. Use function `start job synchronously` (`POST /jobExecutions`) with `Content-Type=application/json`, `Accept=application/hal+json`, body `name={job.name from step 1}`, body `asynchronous=false`, body `properties={client supplied key/value map or empty object}`, no path values, and no required query values to create the execution and receive its generated execution fields.

Optional verification workflow:
1. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={generated execution id from step 2}`, `Accept=application/hal+json`, no query values, and no body to inspect the stored execution.
2. Use function `find job executions by job name` (`GET /jobExecutions`) with query `jobName={body name from step 2}`, optional query `limitPerJob={positive integer}`, `Accept=application/hal+json`, and no body to inspect history for the same job.

Existing-state shortcuts:
- Step 1 can be skipped if the caller already knows a launchable job name in the same `JobRegistry`.
- Direct application wiring, `JobBuilder.registerJob(...)`, or `JobBuilder.createJob(...)` can establish the required job registration.
- Direct repository seeding can create execution records for verification, but it does not replace the core launch action.

Parameter and value bindings:
- `job.name` from discovery is reused exactly as POST body `name`.
- Body `asynchronous=false` selects the synchronous launcher; omitting it has the same Java default.
- Body `properties` is converted to Spring Batch `JobParameter` values: `Date`, `Long`, and `Double` keep typed parameter form; other values are stringified.
- If `com.github.chrisgleissner.springbatchrest.addUniqueJobParameter=true`, a generated `uuid` parameter is added.
- The generated execution id from the response can be reused as path `id` in `get job execution by id`.

Business result:
A new execution record exists for the selected job. In generated tests for the sample `personJob`, synchronous runs return `status=COMPLETED`, `exitCode=COMPLETED`, empty `exitDescription`, no exceptions, and non-null start/end timestamps. No job definition is changed.

Constraints and invariants:
- `name` must resolve through `JobLocator.getJob(name)`.
- OpenAPI does not mark `name` as required, but implementation requires it.
- Spring Batch launch invariants still apply, including parameter validity and job instance/restart rules.
- The API has no visible caller authorization, tenant scope, or ownership restriction.

Failure and exceptional cases:
- Failing function: `start job synchronously`
  - Source discriminator: `RuntimeException` wrapping the `JobLocator.getJob(jobConfig.getName())` null-name branch
  - Failure condition: The parsed `JobConfig` has `name=null` because the body omits `name` or explicitly sets it to null.
  - Why it fails: `AdHocStarter.start(JobConfig)` immediately resolves the job before creating parameters or selecting the launcher, and the Spring Batch locator cannot resolve a null job identity.
  - Violated prerequisite or constraint: A launch request must identify one concrete registered Spring Batch job.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java` `AdHocStarter.start(JobConfig)`

- Failing function: `start job synchronously`
  - Source discriminator: `NoSuchJobException` from `JobLocator.getJob(jobConfig.getName())`
  - Failure condition: The parsed `JobConfig.name` is non-null, including an empty string, but no job with that exact name is registered in the `JobRegistry`.
  - Why it fails: `AdHocStarter.start(JobConfig)` resolves the job before launch; an unregistered name prevents construction of the `JobExecution`.
  - Violated prerequisite or constraint: The requested job name must already be registered and resolvable by Spring Batch.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java` `AdHocStarter.start(JobConfig)`

- Failing function: `start job synchronously`
  - Source discriminator: `JobParametersInvalidException`
  - Failure condition: The registered job's parameter validator rejects the `JobParameters` created from the request `properties` and optional generated `uuid`.
  - Why it fails: `AdHocStarter.start(JobConfig)` converts the raw property map and calls `JobLauncher.run(...)`; Spring Batch aborts before creating a successful launch when job-specific parameter validation fails.
  - Violated prerequisite or constraint: The supplied parameters must satisfy the selected job's declared parameter rules.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java` `AdHocStarter.start(JobConfig)`; `src/main/java/com/github/chrisgleissner/springbatchrest/util/JobParamUtil.java` `JobParamUtil.convertRawToParamMap(...)`

- Failing function: `start job synchronously`
  - Source discriminator: `JobExecutionAlreadyRunningException`
  - Failure condition: The launch would create a job instance whose identifying parameters already have a running execution.
  - Why it fails: `JobLauncher.run(...)` enforces Spring Batch lifecycle rules and rejects a second concurrent execution of the same job instance.
  - Violated prerequisite or constraint: A job instance with the same identifying parameters must not already be running.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java` `AdHocStarter.start(JobConfig)`

- Failing function: `start job synchronously`
  - Source discriminator: `JobInstanceAlreadyCompleteException`
  - Failure condition: The launch reuses identifying parameters for a job instance that has already completed and is not eligible to run again.
  - Why it fails: `JobLauncher.run(...)` rejects a completed job instance instead of creating a duplicate execution for the same completed instance.
  - Violated prerequisite or constraint: A completed Spring Batch job instance cannot be relaunched with the same identifying parameters unless the job configuration allows a distinct instance.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java` `AdHocStarter.start(JobConfig)`

- Failing function: `start job synchronously`
  - Source discriminator: `JobRestartException`
  - Failure condition: The launch targets an existing job instance whose persisted restart state is not restartable.
  - Why it fails: `JobLauncher.run(...)` applies Spring Batch restart rules and rejects a launch that would resume from an invalid or non-restartable execution state.
  - Violated prerequisite or constraint: Restarting a job instance requires persisted execution state that Spring Batch considers restartable.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java` `AdHocStarter.start(JobConfig)`

- Failing function: `start job synchronously`
  - Source discriminator: `JobExecutionController.put` failed-exit-code branch
  - Failure condition: The synchronous launch returns a `JobExecutionResource` whose `jobExecution.exitCode` is `FAILED`.
  - Why it fails: After `JobExecutionService.launch(...)` returns, the controller explicitly maps a failed job outcome to an error response instead of a normal `200` response.
  - Violated prerequisite or constraint: The requested synchronous execution must not finish with Spring Batch `ExitStatus.FAILED`.
  - Implementation evidence: `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/JobExecutionController.java` `JobExecutionController.put(...)`
  - Persisted outcome despite failure: The Spring Batch execution record has already been created and returned from the launcher with failed status, exit code, timestamps, and exception details available in the repository.

Implementation notes:
`AdHocStarter` uses a `SyncTaskExecutor` when `asynchronous=false`. OpenAPI documents only `200`, but generated tests show `400`, `404`, and `500`. Timestamp strings in responses can lack the timezone format required by the OpenAPI `date-time` schema.

<a id="behavior-4"></a>
### Behavior 4: Submit an asynchronous batch execution

Business goal:
Submit a registered batch job through the asynchronous launcher and receive an execution handle before completion.

API group boundary:
The functions share the job-name lifecycle key. Discovery supplies the POST body `name`, and `start job asynchronously` creates the execution in an asynchronous Spring Batch state path.

Domain context:
Asynchronous launch is meaningful for long-running jobs where the caller needs an execution id and later inspection rather than immediate terminal status.

Starting point:
Pre-existing service/upstream state required. The job must already be registered and resolvable by `JobLocator`.

State transition summary:
- State before: A registered job exists; no new execution for this request exists yet.
- Transition trigger: A caller posts a `JobConfig` with `name` and `asynchronous=true`.
- Intermediate states: `AdHocStarter` resolves the job, enables job-name-scoped property resolution, converts properties, optionally adds `uuid`, and uses `SimpleAsyncTaskExecutor`.
- State after: A new execution exists and may initially be STARTING with UNKNOWN exit code and null timestamps.
- Invalid or blocked transitions: Missing name, unknown job, invalid body, invalid launch parameters, or Spring Batch infrastructure failures block submission; concurrent async property resolution can produce incorrect per-execution property behavior without necessarily returning an HTTP error.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json`, no path values, no query values, and no body to obtain a returned `job.name` value.
2. Use function `start job asynchronously` (`POST /jobExecutions`) with `Content-Type=application/json`, `Accept=application/hal+json`, body `name={job.name from step 1}`, body `asynchronous=true`, body `properties={client supplied key/value map or empty object}`, no path values, and no required query values to submit the execution and receive its generated execution fields.

Optional verification workflow:
1. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={generated execution id from step 2}`, `Accept=application/hal+json`, no query values, and no body to inspect later execution state.
2. Use function `find job executions by job name` (`GET /jobExecutions`) with query `jobName={body name from step 2}`, optional query `limitPerJob={positive integer}`, `Accept=application/hal+json`, and no body to inspect later history for the same job.

Existing-state shortcuts:
- Step 1 can be skipped if the caller already knows a launchable job name in the same registry scope.
- Direct application wiring or `JobBuilder` utilities can establish job registration.
- Existing execution history can support later verification, but it does not replace the core async submission.

Parameter and value bindings:
- `job.name` from discovery is reused as POST body `name`.
- Body `asynchronous=true` selects the async launcher.
- Body `properties` is converted to Spring Batch job parameters and also made available through the deprecated job-name-scoped resolver.
- Generated execution id is reused for id-based polling or retrieval.
- The same caller context is not preserved by any documented security or ownership field.

Business result:
A new execution record exists for the selected job. The immediate response may report STARTING, UNKNOWN exit code, null `startTime`, and null `endTime`. Later repository updates can transition the execution to a terminal status.

Constraints and invariants:
- `name` must resolve through `JobLocator.getJob(name)`.
- Async status and exit code are time-dependent.
- OpenAPI does not mark response timestamps nullable, but async responses can contain null values.
- Deprecated `JobPropertyResolvers.JobProperties` is not safe for concurrent asynchronous executions of the same job with different property maps.

Failure and exceptional cases:
- Failing function: `start job asynchronously`
  - Source discriminator: `RuntimeException` wrapping the `JobLocator.getJob(jobConfig.getName())` null-name branch
  - Failure condition: The parsed `JobConfig` has `name=null` because the body omits `name` or explicitly sets it to null.
  - Why it fails: The asynchronous branch still resolves the job before choosing `SimpleAsyncTaskExecutor`; a null job identity cannot be resolved to a launchable `Job`.
  - Violated prerequisite or constraint: A launch request must identify one concrete registered Spring Batch job.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java` `AdHocStarter.start(JobConfig)`

- Failing function: `start job asynchronously`
  - Source discriminator: `NoSuchJobException` from `JobLocator.getJob(jobConfig.getName())`
  - Failure condition: The parsed `JobConfig.name` is non-null, including an empty string, but no job with that exact name is registered in the `JobRegistry`.
  - Why it fails: `AdHocStarter.start(JobConfig)` resolves the job before submitting asynchronous work, so an unregistered name prevents execution submission.
  - Violated prerequisite or constraint: The requested job name must already be registered and resolvable by Spring Batch.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java` `AdHocStarter.start(JobConfig)`

- Failing function: `start job asynchronously`
  - Source discriminator: `JobParametersInvalidException`
  - Failure condition: The registered job's parameter validator rejects the `JobParameters` created from the request `properties` and optional generated `uuid`.
  - Why it fails: `AdHocStarter.start(JobConfig)` converts the raw property map and calls `JobLauncher.run(...)`; Spring Batch aborts submission when job-specific parameter validation fails.
  - Violated prerequisite or constraint: The supplied parameters must satisfy the selected job's declared parameter rules.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java` `AdHocStarter.start(JobConfig)`; `src/main/java/com/github/chrisgleissner/springbatchrest/util/JobParamUtil.java` `JobParamUtil.convertRawToParamMap(...)`

- Failing function: `start job asynchronously`
  - Source discriminator: `JobExecutionAlreadyRunningException`
  - Failure condition: The submission would create a job instance whose identifying parameters already have a running execution.
  - Why it fails: `JobLauncher.run(...)` enforces Spring Batch lifecycle rules before accepting the asynchronous launch.
  - Violated prerequisite or constraint: A job instance with the same identifying parameters must not already be running.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java` `AdHocStarter.start(JobConfig)`

- Failing function: `start job asynchronously`
  - Source discriminator: `JobInstanceAlreadyCompleteException`
  - Failure condition: The submission reuses identifying parameters for a job instance that has already completed and is not eligible to run again.
  - Why it fails: `JobLauncher.run(...)` rejects a completed job instance before accepting a new asynchronous execution for it.
  - Violated prerequisite or constraint: A completed Spring Batch job instance cannot be relaunched with the same identifying parameters unless the job configuration allows a distinct instance.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java` `AdHocStarter.start(JobConfig)`

- Failing function: `start job asynchronously`
  - Source discriminator: `JobRestartException`
  - Failure condition: The submission targets an existing job instance whose persisted restart state is not restartable.
  - Why it fails: `JobLauncher.run(...)` applies Spring Batch restart rules before accepting asynchronous work and rejects invalid restart state.
  - Violated prerequisite or constraint: Restarting a job instance requires persisted execution state that Spring Batch considers restartable.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java` `AdHocStarter.start(JobConfig)`

- Failing function: `start job asynchronously`
  - Source discriminator: `JobPropertyResolvers.started(JobConfig)` job-name-keyed resolver overwrite
  - Failure condition: Multiple concurrent asynchronous executions of the same job use different `properties`, and job code reads those properties through deprecated `JobPropertyResolvers.JobProperties`.
  - Why it fails: `JobPropertyResolvers` stores one resolver per job name, so a later execution can overwrite the resolver while an earlier same-name execution is still running.
  - Violated prerequisite or constraint: Per-execution property values require execution-scoped resolution, not a shared job-name-scoped resolver.
  - Implementation evidence: `src/main/java/com/github/chrisgleissner/springbatchrest/util/core/property/JobPropertyResolvers.java` `JobPropertyResolvers.started(JobConfig)` and `JobPropertyResolvers.of(String)`

Implementation notes:
`AdHocStarter` uses `SimpleAsyncTaskExecutor` for async launches. The response schema disagrees with observed async responses because timestamp fields can be null. The concurrency property resolver problem is documented directly in source comments.

<a id="behavior-5"></a>
### Behavior 5: Review global execution history

Business goal:
Allow an operator or client to inspect stored job executions across the service.

API group boundary:
This is an atomic read behavior. The single function is the global execution-history read model.

Domain context:
Execution history is the audit surface for job runs, statuses, exit codes, exceptions, and timing.

Starting point:
No prior service state.

State transition summary:
- State before: The Spring Batch repository may contain zero or more executions.
- Transition trigger: A caller requests the global execution collection.
- Intermediate states: No repository state changes.
- State after: Repository state is unchanged, and the caller receives a collection.
- Invalid or blocked transitions: Negative `limitPerJob` and some filter combinations can return server errors even though OpenAPI documents only `200`.

Required execution workflow:
1. Use function `list job execution history` (`GET /jobExecutions`) with optional query `limitPerJob={positive integer or omitted}`, `Accept=application/hal+json`, no path values, and no body to retrieve execution records across jobs.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup is needed for an empty history view.
- Prior calls to `start job synchronously` or `start job asynchronously` can make the result non-empty.
- Direct Spring Batch metadata seeding can create history, but the seeded data must belong to the same repository instance.

Parameter and value bindings:
- Query `limitPerJob` controls per-job result count when accepted by the implementation.
- Returned generated execution ids can be reused in `get job execution by id`.
- Returned `jobExecution.jobName` values can be reused in `find job executions by job name`.
- Returned `jobExecution.exitCode` values can be reused in `find job executions by exit code`.

Business result:
The caller receives a HAL collection of execution records. No execution is created, deleted, updated, stopped, restarted, or abandoned.

Constraints and invariants:
- The read is global, not owner- or tenant-scoped.
- OpenAPI documents `limitPerJob` defaulting to `3`.
- Visible `AdHocBatchConfig` uses a map-based Spring Batch repository when that configuration is active.
- Negative limits are not declared invalid by OpenAPI but can fail in implementation.

Failure and exceptional cases:
- Failing function: `list job execution history`
  - Source discriminator: `IllegalArgumentException` from `Stream.limit(limitPerJob)` in the cached-provider path
  - Failure condition: `limitPerJob` is negative while the cached provider path is selected and at least one cached execution entry is being scanned.
  - Why it fails: `CachedJobExecutionProvider.getJobExecutions(...)` passes the negative limit into `Stream.limit(...)`, which rejects negative limits after the execution-history function has been entered.
  - Violated prerequisite or constraint: The per-job execution-history limit must be non-negative.
  - Implementation evidence: `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/provider/CachedJobExecutionProvider.java` `CachedJobExecutionProvider.getJobExecutions(...)`

Implementation notes:
OpenAPI documents only `200`; generated tests show `500`. Response date-time strings observed in history can violate the declared OpenAPI date-time format.

<a id="behavior-6"></a>
### Behavior 6: Retrieve a specific execution record

Business goal:
Inspect the persisted state of one job execution using its generated id.

API group boundary:
The functions share a response-to-request id binding. `start job synchronously` creates an execution and returns the generated id consumed by `get job execution by id`.

Domain context:
Id-based lookup is the stable follow-up workflow after a job is started or discovered in history.

Starting point:
Pre-existing service/upstream state required. A registered launchable job must exist if the workflow creates the execution through REST.

State transition summary:
- State before: A registered job exists; the target execution id does not exist until launch.
- Transition trigger: The caller creates an execution and then requests it by generated id.
- Intermediate states: The synchronous launch creates and persists the execution record.
- State after: The execution record is unchanged by the lookup and is returned to the caller.
- Invalid or blocked transitions: Missing launchable job blocks id creation; nonexistent or non-numeric ids block retrieval.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json`, no path values, no query values, and no body to obtain a returned `job.name` value.
2. Use function `start job synchronously` (`POST /jobExecutions`) with `Content-Type=application/json`, `Accept=application/hal+json`, body `name={job.name from step 1}`, body `asynchronous=false`, body `properties={client supplied key/value map or empty object}`, no path values, and no required query values to create an execution and capture its generated execution id.
3. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={generated execution id from step 2}`, `Accept=application/hal+json`, no query values, and no body to retrieve the same execution record.

Optional verification workflow:
1. Use function `list job execution history` (`GET /jobExecutions`) with optional query `limitPerJob={positive integer}`, `Accept=application/hal+json`, no path values, and no body to verify the generated execution appears in history.

Existing-state shortcuts:
- Step 1 can be skipped if a launchable job name is already known.
- Steps 1 and 2 can be skipped if an equivalent execution already exists in the same Spring Batch repository and its generated id is known.
- Direct Spring Batch metadata seeding can replace the launch setup only for lookup testing; the path id must still match a real persisted execution.

Parameter and value bindings:
- `job.name` from discovery is reused as POST body `name`.
- The generated execution id from the POST response is reused exactly as path `id`.
- The returned `jobExecution.jobName` should match the POST body `name`.
- Generated tests assert a `jobExecution.jobId` response field; OpenAPI also defines `id`, so clients may need to bind to the actual representation returned by the running service.

Business result:
The caller receives one execution record. No execution state changes during retrieval. The record includes job name, status, exit code, exit description, timestamps, and exception list when represented.

Constraints and invariants:
- Path `id` must be parseable as an `int64`.
- Id lookup is global and not scoped by job name, caller, tenant, or owner.
- No endpoint updates or deletes the execution after retrieval.

Failure and exceptional cases:
- Failing function: `get job execution by id`
  - Source discriminator: `javax.batch.operations.NoSuchJobExecutionException`
  - Failure condition: The parsed numeric path `id` does not identify any persisted Spring Batch job execution in the repository visible to `JobExplorer`.
  - Why it fails: `JobExecutionService.jobExecution(long)` calls `jobExplorer.getJobExecution(id)` and throws when the lookup returns null.
  - Violated prerequisite or constraint: The requested id must identify existing Spring Batch execution metadata.
  - Implementation evidence: `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/JobExecutionService.java` `JobExecutionService.jobExecution(long)`; `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/ResponseExceptionHandler.java` `ResponseExceptionHandler.handleNoSuchJobExecutionException(...)`

Implementation notes:
OpenAPI documents only `200`, but generated tests show `404`. Response timestamp format can disagree with the OpenAPI date-time schema.

<a id="behavior-7"></a>
### Behavior 7: Review executions for one job

Business goal:
Inspect execution history scoped to one batch job.

API group boundary:
The functions share the job-name aggregate key. Discovery obtains `job.name`, launch creates an execution with that same job name, and the filter consumes the same value as query `jobName`.

Domain context:
Operators usually need to answer what happened to a specific job, not only inspect global execution history.

Starting point:
Pre-existing service/upstream state required. A registered launchable job must exist if the workflow establishes matching history through REST.

State transition summary:
- State before: A registered job exists, and matching execution history may or may not already exist.
- Transition trigger: The caller creates or relies on history, then filters by job name.
- Intermediate states: The setup launch creates an execution whose stored `jobExecution.jobName` equals the POST body `name`.
- State after: The caller receives records matching query `jobName`; repository state is unchanged by the read.
- Invalid or blocked transitions: Unknown jobs block setup; mismatched or regex-unsafe job-name filters can produce empty results or server errors.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json`, no path values, no query values, and no body to obtain a returned `job.name` value.
2. Use function `start job synchronously` (`POST /jobExecutions`) with `Content-Type=application/json`, `Accept=application/hal+json`, body `name={job.name from step 1}`, body `asynchronous=false`, body `properties={client supplied key/value map or empty object}`, no path values, and no required query values to create at least one execution for that job.
3. Use function `find job executions by job name` (`GET /jobExecutions`) with query `jobName={body name from step 2}`, optional query `limitPerJob={positive integer}`, `Accept=application/hal+json`, no path values, and no body to retrieve executions for that job.

Optional verification workflow:
1. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={generated execution id from step 2}`, `Accept=application/hal+json`, no query values, and no body to inspect one matching execution.

Existing-state shortcuts:
- Step 1 can be skipped if the job name is already known.
- Step 2 can be skipped if execution history for the same `jobName` already exists in the same repository.
- Direct metadata seeding can replace step 2 if seeded records use the same stored `jobExecution.jobName` consumed by query `jobName`.

Parameter and value bindings:
- The same job-name value flows from `job.name` to POST body `name` and then to query `jobName`.
- `limitPerJob` must be a non-negative result limit in practice.
- Returned execution ids can be reused for id lookup.

Business result:
The caller receives execution records whose stored job name matches the query. No execution is changed by the filter operation.

Constraints and invariants:
- Query `jobName` must match the stored execution job name.
- Empty `jobName` can behave like a broad or unstable filter in generated tests.
- Some job-name values appear to be interpreted through regex-sensitive provider logic.
- The API does not enforce caller-specific ownership of the job name.

Failure and exceptional cases:
- Failing function: `find job executions by job name`
  - Source discriminator: `PatternSyntaxException` from `Pattern.compile(jobNameRegexp)` in the cached-provider path
  - Failure condition: `jobName` is present after controller trimming, `limitPerJob` selects the cached-provider path, and the supplied job-name filter is not valid Java regular-expression syntax.
  - Why it fails: `CachedJobExecutionProvider.getJobExecutions(...)` compiles `jobName` as a regex before filtering cached job-name keys.
  - Violated prerequisite or constraint: Cached job-name filtering requires a regex-safe `jobName` value.
  - Implementation evidence: `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/provider/CachedJobExecutionProvider.java` `CachedJobExecutionProvider.getJobExecutions(...)`

- Failing function: `find job executions by job name`
  - Source discriminator: `PatternSyntaxException` from `Pattern.compile(jobNameRegexp)` in the all-provider fallback path
  - Failure condition: `jobName` is present after controller trimming, `limitPerJob` is greater than the configured cache size, and the supplied job-name filter is not valid Java regular-expression syntax.
  - Why it fails: `CachedJobExecutionProvider.getJobExecutions(...)` delegates large-limit searches to `AllJobExecutionProvider.getJobExecutions(...)`, which also compiles `jobName` as a regex before filtering repository job names.
  - Violated prerequisite or constraint: Repository-backed job-name filtering requires a regex-safe `jobName` value.
  - Implementation evidence: `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/provider/CachedJobExecutionProvider.java` `CachedJobExecutionProvider.getJobExecutions(...)`; `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/provider/AllJobExecutionProvider.java` `AllJobExecutionProvider.getJobExecutions(...)`

- Failing function: `find job executions by job name`
  - Source discriminator: `IllegalArgumentException` from `Stream.limit(limitPerJob)` in the cached-provider path
  - Failure condition: `limitPerJob` is negative, the cached-provider path is selected, and the job-name filter matches at least one cached job execution bucket.
  - Why it fails: `CachedJobExecutionProvider.getJobExecutions(...)` passes the negative limit into `Stream.limit(...)` while flattening executions for the matching job.
  - Violated prerequisite or constraint: The per-job execution-history limit must be non-negative.
  - Implementation evidence: `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/provider/CachedJobExecutionProvider.java` `CachedJobExecutionProvider.getJobExecutions(...)`

Implementation notes:
This behavior is supported, but not as a robust literal exact-match search. OpenAPI documents only `200` and does not document regex-sensitive failures.

<a id="behavior-8"></a>
### Behavior 8: Review executions by outcome

Business goal:
Find job executions with a selected exit code, for example COMPLETED, FAILED, or UNKNOWN.

API group boundary:
The functions share execution outcome state. The setup launch returns or stores an execution `exitCode`, and `find job executions by exit code` consumes that value as query `exitCode`.

Domain context:
Outcome filtering supports operational monitoring, success review, and failure triage.

Starting point:
Pre-existing service/upstream state required. A registered launchable job must exist if the workflow establishes a known exit code through REST.

State transition summary:
- State before: A registered job exists, and matching execution history may or may not already exist.
- Transition trigger: The caller creates or relies on an execution outcome, then filters by exit code.
- Intermediate states: The setup launch creates an execution and produces a stored `jobExecution.exitCode`.
- State after: The caller receives records matching query `exitCode`; repository state is unchanged by the read.
- Invalid or blocked transitions: Querying for a terminal code before async completion, using a non-matching code, or using provider-bug-triggering query combinations can block or weaken the result.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json`, no path values, no query values, and no body to obtain a returned `job.name` value.
2. Use function `start job synchronously` (`POST /jobExecutions`) with `Content-Type=application/json`, `Accept=application/hal+json`, body `name={job.name from step 1}`, body `asynchronous=false`, body `properties={client supplied key/value map or empty object}`, no path values, and no required query values to create an execution and capture its returned `jobExecution.exitCode`.
3. Use function `find job executions by exit code` (`GET /jobExecutions`) with query `exitCode={jobExecution.exitCode from step 2}`, optional query `limitPerJob={positive integer}`, `Accept=application/hal+json`, no path values, and no body to retrieve executions with that outcome.

Optional verification workflow:
1. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={generated execution id from step 2}`, `Accept=application/hal+json`, no query values, and no body to confirm the selected execution's exit code.

Existing-state shortcuts:
- Step 2 can be skipped if an execution with the desired exit code already exists in the same repository.
- Direct metadata seeding can replace step 2 if seeded `jobExecution.exitCode` exactly matches the query value.
- For asynchronous setup, the caller must wait until the desired stored exit code exists before filtering for a terminal value.

Parameter and value bindings:
- The returned `jobExecution.exitCode` from setup is reused exactly as query `exitCode`.
- If async launch is used outside this required workflow, an initially returned UNKNOWN value may differ from the later terminal value.
- `limitPerJob` constrains returned records when accepted by the provider.

Business result:
The caller receives execution records whose stored exit code matches the query. No execution status or exit code is changed by the read.

Constraints and invariants:
- Exit code is time-dependent for asynchronous jobs.
- The query value is not constrained by OpenAPI to known exit-code strings.
- Empty results can be a valid outcome when no execution matches.

Failure and exceptional cases:
- Failing function: `find job executions by exit code`
  - Source discriminator: `NullPointerException` from `copyOf(jobExecutionsByExitCode.get(exitCode))` in `CachedJobExecutionProvider.JobExecutions.getJobExecutions(...)`
  - Failure condition: The cached-provider path is selected, at least one cached job execution bucket is scanned, and the requested `exitCode` has no queue in that bucket's exit-code index.
  - Why it fails: The cached provider assumes that a present `exitCode` filter maps to a non-null queue and passes the missing queue to Guava `copyOf(...)`.
  - Violated prerequisite or constraint: Cached outcome filtering requires the requested exit-code bucket to exist for every scanned cached job bucket.
  - Implementation evidence: `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/provider/CachedJobExecutionProvider.java` `CachedJobExecutionProvider.JobExecutions.getJobExecutions(Optional<String>)`

- Failing function: `find job executions by exit code`
  - Source discriminator: `IllegalArgumentException` from `Stream.limit(limitPerJob)` in the cached-provider path
  - Failure condition: `limitPerJob` is negative, the cached-provider path is selected, and the requested `exitCode` exists in the scanned cached exit-code bucket.
  - Why it fails: `CachedJobExecutionProvider.getJobExecutions(...)` obtains the matching exit-code queue and then passes the negative limit into `Stream.limit(...)`.
  - Violated prerequisite or constraint: The per-job execution-history limit must be non-negative.
  - Implementation evidence: `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/provider/CachedJobExecutionProvider.java` `CachedJobExecutionProvider.getJobExecutions(...)`

Implementation notes:
OpenAPI documents only `200`. Generated tests show no-match and malformed combinations can behave inconsistently; some return `200`, while others return `500`.

<a id="behavior-9"></a>
### Behavior 9: Review one job's executions by outcome

Business goal:
Find executions for a specific job that also match a specific exit code.

API group boundary:
The functions share both job-name and execution-outcome state. The setup launch binds POST body `name` to stored `jobExecution.jobName` and returns `jobExecution.exitCode`, and the final read consumes both values.

Domain context:
This is the most useful operational read model for questions like “which runs of this job completed or failed?”

Starting point:
Pre-existing service/upstream state required. A registered launchable job must exist if the workflow establishes matching history through REST.

State transition summary:
- State before: A registered job exists, and matching execution history may or may not already exist.
- Transition trigger: The caller creates or relies on an execution, then filters by both job name and exit code.
- Intermediate states: The setup launch stores an execution with the selected job name and returned exit code.
- State after: The caller receives records satisfying both filters; repository state is unchanged by the read.
- Invalid or blocked transitions: Either filter mismatch excludes the target execution; regex-sensitive job-name values or invalid limits can return server errors.

Required execution workflow:
1. Use function `list registered Spring Batch jobs` (`GET /jobs`) with `Accept=application/hal+json`, no path values, no query values, and no body to obtain a returned `job.name` value.
2. Use function `start job synchronously` (`POST /jobExecutions`) with `Content-Type=application/json`, `Accept=application/hal+json`, body `name={job.name from step 1}`, body `asynchronous=false`, body `properties={client supplied key/value map or empty object}`, no path values, and no required query values to create an execution and capture its returned `jobExecution.exitCode`.
3. Use function `find job executions by job name and exit code` (`GET /jobExecutions`) with query `jobName={body name from step 2}`, query `exitCode={jobExecution.exitCode from step 2}`, optional query `limitPerJob={positive integer}`, `Accept=application/hal+json`, no path values, and no body to retrieve executions matching both values.

Optional verification workflow:
1. Use function `get job execution by id` (`GET /jobExecutions/{id}`) with path `id={generated execution id from step 2}`, `Accept=application/hal+json`, no query values, and no body to inspect one matching execution.

Existing-state shortcuts:
- Step 1 can be skipped if the job name is already known.
- Step 2 can be skipped if a matching execution already exists in the same repository.
- Direct metadata seeding can replace step 2 if both seeded `jobExecution.jobName` and seeded `jobExecution.exitCode` match the query values.

Parameter and value bindings:
- POST body `name` is reused exactly as query `jobName`.
- Returned `jobExecution.exitCode` is reused exactly as query `exitCode`.
- Returned generated execution id can be reused in id lookup.
- Both filters must bind to the same repository scope.

Business result:
The caller receives execution records for the selected job and selected outcome. Executions for the same job with a different exit code and executions for other jobs with the same exit code are excluded.

Constraints and invariants:
- Both filters must match the same stored execution.
- Query `jobName` may be regex-sensitive in implementation.
- `limitPerJob` should be non-negative.
- No ownership, tenant, or authorization rule limits who can use the filters.

Failure and exceptional cases:
- Failing function: `find job executions by job name and exit code`
  - Source discriminator: `PatternSyntaxException` from `Pattern.compile(jobNameRegexp)` in the cached-provider path
  - Failure condition: `jobName` is present after controller trimming, `limitPerJob` selects the cached-provider path, and the supplied job-name filter is not valid Java regular-expression syntax.
  - Why it fails: `CachedJobExecutionProvider.getJobExecutions(...)` compiles `jobName` as a regex before applying the combined job-name and exit-code filters.
  - Violated prerequisite or constraint: Cached combined filtering requires a regex-safe `jobName` value.
  - Implementation evidence: `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/provider/CachedJobExecutionProvider.java` `CachedJobExecutionProvider.getJobExecutions(...)`

- Failing function: `find job executions by job name and exit code`
  - Source discriminator: `PatternSyntaxException` from `Pattern.compile(jobNameRegexp)` in the all-provider fallback path
  - Failure condition: `jobName` is present after controller trimming, `limitPerJob` is greater than the configured cache size, and the supplied job-name filter is not valid Java regular-expression syntax.
  - Why it fails: `CachedJobExecutionProvider.getJobExecutions(...)` delegates large-limit combined searches to `AllJobExecutionProvider.getJobExecutions(...)`, which compiles `jobName` as a regex before applying repository-backed filters.
  - Violated prerequisite or constraint: Repository-backed combined filtering requires a regex-safe `jobName` value.
  - Implementation evidence: `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/provider/CachedJobExecutionProvider.java` `CachedJobExecutionProvider.getJobExecutions(...)`; `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/provider/AllJobExecutionProvider.java` `AllJobExecutionProvider.getJobExecutions(...)`

- Failing function: `find job executions by job name and exit code`
  - Source discriminator: `NullPointerException` from `copyOf(jobExecutionsByExitCode.get(exitCode))` in `CachedJobExecutionProvider.JobExecutions.getJobExecutions(...)`
  - Failure condition: The cached-provider path is selected, `jobName` matches at least one cached job execution bucket, and the requested `exitCode` has no queue in that matched bucket's exit-code index.
  - Why it fails: The cached provider assumes that a present `exitCode` filter maps to a non-null queue for the matched job and passes the missing queue to Guava `copyOf(...)`.
  - Violated prerequisite or constraint: Cached combined filtering requires the matched job bucket to contain the requested exit-code bucket.
  - Implementation evidence: `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/provider/CachedJobExecutionProvider.java` `CachedJobExecutionProvider.JobExecutions.getJobExecutions(Optional<String>)`

- Failing function: `find job executions by job name and exit code`
  - Source discriminator: `IllegalArgumentException` from `Stream.limit(limitPerJob)` in the cached-provider path
  - Failure condition: `limitPerJob` is negative, the cached-provider path is selected, `jobName` matches at least one cached job execution bucket, and the requested `exitCode` exists in that bucket.
  - Why it fails: `CachedJobExecutionProvider.getJobExecutions(...)` obtains the matched exit-code queue and then passes the negative limit into `Stream.limit(...)`.
  - Violated prerequisite or constraint: The per-job execution-history limit must be non-negative.
  - Implementation evidence: `api/src/main/java/com/github/chrisgleissner/springbatchrest/api/core/jobexecution/provider/CachedJobExecutionProvider.java` `CachedJobExecutionProvider.getJobExecutions(...)`

Implementation notes:
This behavior is supported as an exposed query pattern, but it has underdocumented error semantics. OpenAPI lists optional plain string filters and only `200` responses, while generated tests show `500`.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Register or manage batch jobs through REST

Priority:
Critical domain gap

Expected business goal:
A client should be able to create, register, update, rename, enable, disable, or delete batch job definitions through the API.

Why it is unsupported:
No available REST function creates or mutates job registry entries. Job registration is done by application wiring or Java utility code outside the REST surface.

Existing functions considered:
- `list registered Spring Batch jobs`: Lists registry entries but cannot create or mutate them.
- `get registered job by name`: Retrieves a job resource by path name but cannot register executable job logic.
- `start job synchronously`: Launches only a job resolvable by `JobLocator`.
- `start job asynchronously`: Launches only a job resolvable by `JobLocator`.

Missing capability:
REST endpoints and persistence/model support for job-definition registration, validation, update, removal, enablement, and disablement.

Proof that function composition is insufficient:
Listing and retrieving jobs do not write registry state. Starting an unknown job fails because `AdHocStarter` calls `JobLocator.getJob(name)`. No delete-and-recreate workaround exists because no REST create or delete exists.

Evidence from existing functions/source:
`AdHocStarter.start(JobConfig)` resolves the job before launching. `JobBuilder.registerJob(...)` and `JobBuilder.createJob(...)` exist only as source-level utilities, not extracted REST functions.

Business impact:
Clients cannot onboard, retire, rename, or govern job definitions through the documented API.

### Missing Behavior 2: Stop, restart, abandon, or resume executions

Priority:
Critical domain gap

Expected business goal:
Operators should be able to stop a running execution, restart a failed execution, abandon an execution, or resume a supported Spring Batch workflow.

Why it is unsupported:
The REST API starts executions and reads execution history, but no function consumes an existing execution id as a control target.

Existing functions considered:
- `start job asynchronously`: Creates a new execution but cannot stop an existing one.
- `start job synchronously`: Creates a new execution but cannot restart a previous one.
- `get job execution by id`: Reads execution state only.
- `list job execution history`: Reads stored executions only.

Missing capability:
Execution-control endpoints for stop, restart, abandon, and resume, with state validation against Spring Batch execution status.

Proof that function composition is insufficient:
Starting a job creates another execution; it does not mutate the status of an existing execution. Reading by id cannot transition state. No available request body contains an execution id plus a control command.

Evidence from existing functions/source:
`POST /jobExecutions` accepts `JobConfig` with `name`, `properties`, and `asynchronous`. It does not accept a prior execution id.

Business impact:
Long-running, failed, stuck, or incorrectly started executions cannot be controlled through REST.

### Missing Behavior 3: Manage Quartz schedules through REST

Priority:
Critical domain gap

Expected business goal:
A client should be able to create, inspect, pause, resume, update, or delete future and recurring schedules for batch jobs.

Why it is unsupported:
OpenAPI mentions Quartz schedules in the service description, and source contains scheduler utilities, but no schedule functions exist in `full-behavior.md` or `spring-batch-rest.json`.

Existing functions considered:
- `list registered Spring Batch jobs`: Identifies jobs but not schedules.
- `start job synchronously`: Runs immediately, not on a schedule.
- `start job asynchronously`: Runs immediately, not on a schedule.
- `list job execution history`: Shows executions after they exist, not future triggers.

Missing capability:
REST endpoints for schedule lifecycle and scheduler control, including cron/date trigger creation, schedule listing, pause, resume, unschedule, and schedule status.

Proof that function composition is insufficient:
Immediate execution cannot create future or recurring triggers. Execution history cannot reveal intended future schedules. No REST function accepts cron expressions, run dates, trigger ids, or scheduler commands.

Evidence from existing functions/source:
`AdHocScheduler.schedule(...)`, `start()`, `pause()`, `resume()`, and `stop()` exist in source but are not exposed as REST functions.

Business impact:
The service description implies schedule control, but API clients cannot manage schedules.

### Missing Behavior 4: Safe validated execution search

Priority:
Important robustness gap

Expected business goal:
Clients should be able to search executions by literal job name and exit code with documented validation for limits and bad inputs.

Why it is unsupported:
Available search functions accept plain string query values but generated tests show regex-sensitive job-name failures, null-provider failures, and negative-limit server errors.

Existing functions considered:
- `find job executions by job name`: Filters by job name but can fail on regex-like input.
- `find job executions by exit code`: Filters by outcome but does not validate known values.
- `find job executions by job name and exit code`: Combines weak filter behavior.
- `list job execution history`: Accepts `limitPerJob` but negative values can produce `500`.

Missing capability:
Literal string filtering, explicit `limitPerJob` constraints, validation errors documented as `400`, and provider-safe empty-result handling.

Proof that function composition is insufficient:
No existing function sanitizes or validates filter values before search. Retrying with escaped text changes the literal job name being searched. Negative-limit handling cannot be repaired by another endpoint.

Evidence from existing functions/source:
Generated tests show `PatternSyntaxException`, `NullPointerException`, and `IllegalArgumentException` for `GET /jobExecutions`; OpenAPI documents only `200`.

Business impact:
Operational search can fail unpredictably and return server errors for client-controlled inputs.

### Missing Behavior 5: Inspect or search execution parameters

Priority:
Important robustness gap

Expected business goal:
Clients should be able to see which parameters were used for an execution and search or audit executions by parameter keys or values.

Why it is unsupported:
Execution resources expose status-oriented fields but not the original request `properties` or converted Spring Batch job parameters.

Existing functions considered:
- `start job synchronously`: Accepts `properties` and converts them to job parameters.
- `start job asynchronously`: Accepts `properties` and converts them to job parameters.
- `get job execution by id`: Returns execution status fields but not parameters.
- `find job executions by job name`: Filters by job name only.
- `find job executions by exit code`: Filters by outcome only.

Missing capability:
REST representation of execution parameters and filters for parameter keys or values.

Proof that function composition is insufficient:
Once the POST response is returned, no REST read exposes the submitted `properties` map or converted job parameters. Execution id lookup and filters cannot recover those values.

Evidence from existing functions/source:
`JobParamUtil` converts raw properties to `JobParameter`; OpenAPI `JobExecution` schema lacks a parameters field.

Business impact:
Audit and troubleshooting are weakened because clients cannot prove which inputs produced a run.

### Missing Behavior 6: Purge or retain execution history

Priority:
API ergonomics gap

Expected business goal:
Operators may need to delete old execution records, purge test runs, archive history, or enforce retention policies.

Why it is unsupported:
All execution-history functions are read-only, and the only mutation starts a new execution.

Existing functions considered:
- `list job execution history`: Reads records.
- `get job execution by id`: Reads one record.
- `find job executions by job name`: Reads filtered records.
- `find job executions by exit code`: Reads filtered records.
- `find job executions by job name and exit code`: Reads filtered records.

Missing capability:
DELETE, purge, archive, or retention-management endpoints for Spring Batch metadata.

Proof that function composition is insufficient:
Starting more executions cannot remove old ones. Reading by id or filter cannot mutate repository metadata. Direct database cleanup is outside the REST API and is not equivalent to a governed business operation.

Evidence from existing functions/source:
OpenAPI has no DELETE path. `AdHocBatchConfig` configures repository behavior but exposes no REST cleanup function.

Business impact:
Execution history can grow, remain polluted, or require unsafe out-of-band cleanup.

### Missing Behavior 7: Authenticated or scoped batch administration

Priority:
Important robustness gap

Expected business goal:
Administrative job discovery, launch, and execution-history access should be scoped by authenticated user, tenant, role, or ownership where deployment requires it.

Why it is unsupported:
OpenAPI and extracted behavior contain no security schemes, authorization headers, ownership fields, or tenant parameters.

Existing functions considered:
- `list registered Spring Batch jobs`: Globally lists jobs.
- `get registered job by name`: Globally addresses job names.
- `start job synchronously`: Launches any resolvable job name.
- `start job asynchronously`: Launches any resolvable job name.
- `get job execution by id`: Globally retrieves by numeric id.

Missing capability:
Authentication requirements, authorization checks, tenant or owner scoping, and scoped identifiers.

Proof that function composition is insufficient:
No available function establishes identity or ownership state. No later function consumes an identity, tenant, token, or ownership binding. Global ids and names cannot be made scoped by chaining existing calls.

Evidence from existing functions/source:
OpenAPI declares no security schemes and job/execution schemas have no owner or tenant fields.

Business impact:
In multi-user or multi-tenant deployments, job launch and execution history access would be unsafe or ambiguous.

## Cross-Behavior Observations

- Job registration is outside the REST API; most workflows begin from pre-existing application-wired jobs such as `personJob`.
- The only exposed write transition is execution creation via `POST /jobExecutions`.
- Synchronous and asynchronous launch paths share the same endpoint but are separate behaviors because body `asynchronous` selects different launcher semantics and execution states.
- Generated execution ids are the central response-to-request binding for id lookup.
- Job name and exit code are the central query bindings for execution history.
- OpenAPI documents only `200` for core endpoints, while generated tests show `400`, `404`, and `500`.
- Response schemas are weaker than observed behavior: async timestamps can be null, and timestamp strings can violate the declared date-time format.
- Query validation is weak: regex-sensitive job names, negative limits, and null-provider paths can produce server errors.
- `addUniqueJobParameter=true` adds a generated `uuid`, making repeated launches distinct.
- Deprecated job-name-scoped property resolution is unsafe for concurrent async runs of the same job with different properties.
- There is no visible access-control, ownership, tenant scoping, audit-user binding, or schedule-management REST surface.

## Coverage Summary

Fully supported workflow/state areas:
- Listing exposed Spring Batch jobs.
- Reading a named job resource, with caveats around registry validation.
- Starting synchronous and asynchronous job executions.
- Reading global execution history.
- Retrieving an execution by generated id.
- Filtering execution history by job name, exit code, or both.

Partially supported workflow/state areas:
- Operational audit is available for status and exit code, but not for submitted parameters.
- Async monitoring is possible through later reads, but terminal-state timing is caller-managed.
- Query filters work for ordinary values but have weak validation and underdocumented failure modes.

Unsupported or unsafe workflow/state areas:
- REST job registration and job lifecycle management.
- Execution stop, restart, abandon, resume, or safe close.
- REST Quartz schedule lifecycle management.
- Validated literal search and documented error responses.
- Execution parameter audit/search.
- Execution-history purge or retention.
- Authenticated or scoped administration.