### Function 1: list registered Spring Batch jobs

Function name:
list registered Spring Batch jobs

Core endpoint(s):
- `GET /jobs`

Preconditions:
- The application may have zero or more Spring Batch jobs registered in the `JobRegistry`. No documented REST endpoint creates this state; it can be satisfied by application bean wiring, by calling `JobBuilder.registerJob(...)` or `JobBuilder.createJob(...)` inside the application, or by directly constructing the registry state in a test fixture. There is no database setup alternative for job registration in the visible source.

Successful execution:
- Result:
  Returns the collection of Spring Batch jobs known to the application.
- Invocation:
  Step 1: `GET /jobs` with an `Accept` header compatible with `application/hal+json`
- Constraints:
  The endpoint is a global discovery endpoint and does not require a specific job name. The OpenAPI file documents a `200` response with `CollectionModelJobResource`; the visible source contains registry helpers but not the REST controller implementation named by the OpenAPI tag.

Failure or exceptional branches:
- No failure or exceptional branch is documented in the OpenAPI file or confirmed by the visible controller source.

Endpoint coverage:
- Covers:
  `GET /jobs`
- Distinct meaning:
  Lists registered job resources without starting, filtering, or retrieving a specific execution.

### Function 2: retrieve a registered job by name

Function name:
get registered job by name

Core endpoint(s):
- `GET /jobs/{jobName}`

Preconditions:
- A Spring Batch job named `{jobName}` exists in the `JobRegistry`. No documented REST endpoint creates this state; it can be satisfied by application bean wiring, by calling `JobBuilder.registerJob(...)` or `JobBuilder.createJob(...)`, or by directly constructing the registry state in a test fixture. There is no database setup alternative for job registration in the visible source.

Successful execution:
- Result:
  Returns the job resource for the job named by `{jobName}`.
- Invocation:
  Step 1: `GET /jobs/{jobName}` with path value `jobName={jobName}` and an `Accept` header compatible with `application/hal+json`
- Constraints:
  `{jobName}` must identify a registered Spring Batch job. The OpenAPI file documents only a `200` response with `JobResource`; the visible source does not contain the REST controller logic for missing or malformed names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No Spring Batch job named `{jobName}` is registered in the `JobRegistry`. This can be produced by starting with an empty registry for that name, by not wiring a job bean with that name, and by not calling `JobBuilder.registerJob(...)` or `JobBuilder.createJob(...)` for `{jobName}`.
  - Failure endpoint:
    `GET /jobs/{jobName}`
  - Why this fails:
    The endpoint is a named resource lookup, so a missing registry entry cannot produce the requested job resource. The OpenAPI file does not document the error response, and the visible source does not include the controller implementation that maps this lookup failure to an HTTP response.
  - Intentionally violated constraints:
    The required job registry entry for `{jobName}` was omitted.

Endpoint coverage:
- Covers:
  `GET /jobs/{jobName}`
- Distinct meaning:
  Retrieves one registered job resource by its path-scoped name.

### Function 3: start an asynchronous job execution

Function name:
start job asynchronously

Core endpoint(s):
- `POST /jobExecutions`

Preconditions:
- A Spring Batch job named `{name}` exists in the `JobRegistry` and can be resolved by `JobLocator.getJob({name})`. No documented REST endpoint creates this state; it can be satisfied by application bean wiring, by calling `JobBuilder.registerJob(...)` or `JobBuilder.createJob(...)`, or by directly constructing the registry state in a test fixture. There is no database setup alternative for job registration in the visible source.

Successful execution:
- Result:
  Starts a new execution of the registered Spring Batch job named in the request body and returns the created job execution resource.
- Invocation:
  Step 1: `POST /jobExecutions` with `Content-Type: application/json`, an `Accept` header compatible with `application/hal+json`, and body fields `name={name}`, `asynchronous=true`, and optional `properties={...}`
- Constraints:
  Body field `name` must resolve to a registered job. Body field `asynchronous` must be `true`, which selects the `SimpleAsyncTaskExecutor` launcher in `AdHocStarter`. Optional `properties` are converted to Spring Batch job parameters: `Date`, `Long`, and `Double` values keep their typed parameter form, and other values are converted with string concatenation. When `com.github.chrisgleissner.springbatchrest.addUniqueJobParameter` is `true`, the implementation adds a generated `uuid` job parameter so repeated starts are distinct.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No Spring Batch job named `{name}` is registered in the `JobRegistry`. This can be produced by not wiring a job bean with that name and not calling `JobBuilder.registerJob(...)` or `JobBuilder.createJob(...)` for `{name}`.
  - Failure endpoint:
    `POST /jobExecutions`
  - Why this fails:
    `AdHocStarter.start(JobConfig)` calls `JobLocator.getJob(jobConfig.getName())` before launching. If `{name}` is unknown, the exception is wrapped in a runtime failure message such as `Failed to start job '{name}' ... No job configuration with the name [{name}] was registered`. The OpenAPI file documents only `200`, so the HTTP error shape is not specified there.
  - Intentionally violated constraints:
    The request body `name` does not identify a registered Spring Batch job.
- Branch 2:
  - Preconditions:
    - The request body omits `name` or sets `name=null`. This can be produced by posting `{}` or a JSON body without a usable `name` value.
  - Failure endpoint:
    `POST /jobExecutions`
  - Why this fails:
    `AdHocStarter.start(JobConfig)` still attempts `JobLocator.getJob(jobConfig.getName())`; with a missing name, the start operation is wrapped as a runtime failure for job `null`. The OpenAPI file does not document the HTTP error shape for this branch.
  - Intentionally violated constraints:
    The required job name value was omitted.
- Branch 3:
  - Preconditions:
    - A Spring Batch job named `{name}` is registered in the `JobRegistry`.
    - Another asynchronous execution of the same job name is still running and was started with different `properties`. This can be produced by a prior overlapping `POST /jobExecutions` request with body `name={name}`, `asynchronous=true`, and a different `properties` object.
  - Failure endpoint:
    `POST /jobExecutions`
  - Why this fails:
    The deprecated `JobPropertyResolvers.JobProperties` singleton stores property resolvers by job name rather than execution id. Concurrent asynchronous executions of the same job can therefore resolve properties from another still-running execution. This is an implementation-level correctness failure rather than a documented HTTP error.
  - Intentionally violated constraints:
    Concurrent asynchronous starts reuse the same job name while relying on different per-execution properties through the deprecated singleton resolver.
- Branch 4:
  - Preconditions:
    - A Spring Batch job named `{name}` is registered in the `JobRegistry`.
    - The supplied `properties` or the job infrastructure cause Spring Batch launch validation or execution startup to fail.
  - Failure endpoint:
    `POST /jobExecutions`
  - Why this fails:
    `JobLauncher.run(...)` can throw `JobExecutionException`; `AdHocStarter` catches it and wraps it in `BatchRuntimeException`. The OpenAPI file does not document the HTTP error shape for this branch.
  - Intentionally violated constraints:
    The launch request violates Spring Batch job parameter, restart, already-running, already-complete, or job-infrastructure constraints.

Endpoint coverage:
- Covers:
  `POST /jobExecutions`
- Distinct meaning:
  Starts a job using the asynchronous launcher branch selected by `JobConfig.asynchronous == true`.

### Function 4: start a synchronous job execution

Function name:
start job synchronously

Core endpoint(s):
- `POST /jobExecutions`

Preconditions:
- A Spring Batch job named `{name}` exists in the `JobRegistry` and can be resolved by `JobLocator.getJob({name})`. No documented REST endpoint creates this state; it can be satisfied by application bean wiring, by calling `JobBuilder.registerJob(...)` or `JobBuilder.createJob(...)`, or by directly constructing the registry state in a test fixture. There is no database setup alternative for job registration in the visible source.

Successful execution:
- Result:
  Starts the registered Spring Batch job named in the request body, waits for the synchronous launcher to return, and returns the resulting job execution resource.
- Invocation:
  Step 1: `POST /jobExecutions` with `Content-Type: application/json`, an `Accept` header compatible with `application/hal+json`, and body fields `name={name}`, optional `asynchronous=false`, and optional `properties={...}`
- Constraints:
  Body field `name` must resolve to a registered job. Body field `asynchronous` must be `false` or omitted; `JobConfig.asynchronous` is a Java primitive boolean and defaults to `false`. Optional `properties` are converted to Spring Batch job parameters, and the default generated `uuid` parameter distinguishes repeated starts when `addUniqueJobParameter` remains enabled.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No Spring Batch job named `{name}` is registered in the `JobRegistry`. This can be produced by not wiring a job bean with that name and not calling `JobBuilder.registerJob(...)` or `JobBuilder.createJob(...)` for `{name}`.
  - Failure endpoint:
    `POST /jobExecutions`
  - Why this fails:
    `AdHocStarter.start(JobConfig)` resolves the job before choosing the synchronous launcher. Unknown job names are wrapped as runtime start failures. The OpenAPI file documents only `200`, so the HTTP error shape is not specified there.
  - Intentionally violated constraints:
    The request body `name` does not identify a registered Spring Batch job.
- Branch 2:
  - Preconditions:
    - The request body omits `name` or sets `name=null`. This can be produced by posting `{}` or a JSON body without a usable `name` value.
  - Failure endpoint:
    `POST /jobExecutions`
  - Why this fails:
    The implementation attempts to resolve a null job name and wraps the failure as `Failed to start job 'null' with JobConfig(name=null, properties={}, asynchronous=false)`. The OpenAPI file does not document this error response.
  - Intentionally violated constraints:
    The required job name value was omitted.
- Branch 3:
  - Preconditions:
    - A Spring Batch job named `{name}` is registered in the `JobRegistry`.
    - The supplied `properties` or the job infrastructure cause Spring Batch launch validation or execution startup to fail.
  - Failure endpoint:
    `POST /jobExecutions`
  - Why this fails:
    The synchronous branch uses `JobLauncher.run(...)` with `SyncTaskExecutor`. Spring Batch launch exceptions are caught as `JobExecutionException` and wrapped in `BatchRuntimeException`.
  - Intentionally violated constraints:
    The launch request violates Spring Batch job parameter, restart, already-running, already-complete, or job-infrastructure constraints.

Endpoint coverage:
- Covers:
  `POST /jobExecutions`
- Distinct meaning:
  Starts a job using the synchronous launcher branch selected by `JobConfig.asynchronous == false`.

### Function 5: list job executions

Function name:
list job execution history

Core endpoint(s):
- `GET /jobExecutions`

Preconditions:
- The Spring Batch `JobRepository` may contain zero or more job executions. This state can be satisfied by directly seeding Spring Batch metadata tables when a persistent repository is used, by directly seeding the map-backed repository used by the visible `AdHocBatchConfig`, or by calling `POST /jobExecutions` beforehand with body `name={name}`, optional `asynchronous=false`, and optional `properties={...}` for a registered job named `{name}`.

Successful execution:
- Result:
  Returns a collection of job execution resources, limited per job when `limitPerJob` is supplied.
- Invocation:
  Step 1: `GET /jobExecutions` with optional query parameter `limitPerJob={limitPerJob}` and an `Accept` header compatible with `application/hal+json`
- Constraints:
  No specific execution is required for the endpoint to return a collection. OpenAPI documents optional `limitPerJob` with default `3`; the visible source does not include the controller implementation that enforces or normalizes this query parameter.

Failure or exceptional branches:
- No failure or exceptional branch is documented in the OpenAPI file or confirmed by the visible controller source.

Endpoint coverage:
- Covers:
  `GET /jobExecutions`
- Distinct meaning:
  Lists job executions globally without requiring a job-name or exit-code filter.

### Function 6: find executions for a job

Function name:
find job executions by job name

Core endpoint(s):
- `GET /jobExecutions`

Preconditions:
- A Spring Batch job named `{jobName}` exists in the `JobRegistry`. No documented REST endpoint creates this state; it can be satisfied by application bean wiring, by calling `JobBuilder.registerJob(...)` or `JobBuilder.createJob(...)`, or by directly constructing the registry state in a test fixture. There is no database setup alternative for job registration in the visible source.
- At least one execution for job `{jobName}` exists in the Spring Batch `JobRepository`. This can be satisfied by directly seeding Spring Batch metadata tables when a persistent repository is used, by directly seeding the map-backed repository used by the visible `AdHocBatchConfig`, or by calling `POST /jobExecutions` with body `name={jobName}`, optional `asynchronous=false`, and optional `properties={...}`.

Successful execution:
- Result:
  Returns job execution resources belonging to the job named by query parameter `jobName`.
- Invocation:
  Step 1: `GET /jobExecutions` with query `jobName={jobName}`, optional query `limitPerJob={limitPerJob}`, and an `Accept` header compatible with `application/hal+json`
- Constraints:
  Query parameter `jobName` must match the `jobExecution.jobName` stored for the desired executions. The `POST /jobExecutions` endpoint can establish matching repository state, but it is not part of this function's successful invocation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No execution for job `{jobName}` exists in the Spring Batch `JobRepository`. This can be produced by starting with an empty repository for that job, by clearing Spring Batch metadata, or by intentionally not calling `POST /jobExecutions` for body `name={jobName}`.
  - Failure endpoint:
    `GET /jobExecutions`
  - Why this fails:
    The filter can only return executions whose stored job name matches query `jobName`; without matching repository state, the created-resource expectation is unsatisfied and the result collection will not contain that job.
  - Intentionally violated constraints:
    The required matching job execution state was omitted.
- Branch 2:
  - Preconditions:
    - The repository contains executions for job `{actualJobName}` rather than `{jobName}`. This can be satisfied by directly seeding execution metadata under `{actualJobName}` or by calling `POST /jobExecutions` with body `name={actualJobName}`.
  - Failure endpoint:
    `GET /jobExecutions`
  - Why this fails:
    Query `jobName={jobName}` does not match the stored execution job name, so those executions are filtered out.
  - Intentionally violated constraints:
    The query job name and stored execution job name are mismatched.

Endpoint coverage:
- Covers:
  `GET /jobExecutions`
- Distinct meaning:
  Applies the job-name filter of the job execution collection endpoint.

### Function 7: find executions by exit code

Function name:
find job executions by exit code

Core endpoint(s):
- `GET /jobExecutions`

Preconditions:
- At least one completed or otherwise persisted job execution has `jobExecution.exitCode={exitCode}` in the Spring Batch `JobRepository`. This can be satisfied by directly seeding Spring Batch metadata tables when a persistent repository is used, by directly seeding the map-backed repository used by the visible `AdHocBatchConfig`, or by calling `POST /jobExecutions` with body `name={name}`, `asynchronous=false`, and optional `properties={...}` for a registered job and then reusing the returned or stored `jobExecution.exitCode`.
- If the API is used to establish the execution state, the `exitCode` query value must be taken from the resulting job execution after the execution has completed; asynchronous executions may initially return `UNKNOWN`.

Successful execution:
- Result:
  Returns job execution resources whose exit code matches query parameter `exitCode`.
- Invocation:
  Step 1: `GET /jobExecutions` with query `exitCode={exitCode}`, optional query `limitPerJob={limitPerJob}`, and an `Accept` header compatible with `application/hal+json`
- Constraints:
  Query parameter `exitCode` must match the stored `jobExecution.exitCode` value. The execution should be complete before expecting a terminal exit code such as `COMPLETED`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The repository contains an execution with `jobExecution.exitCode={actualExitCode}`. This can be satisfied by directly seeding execution metadata or by calling `POST /jobExecutions` for a registered job and waiting until the execution stores `{actualExitCode}`.
    - The request uses query `exitCode={differentExitCode}` instead of `{actualExitCode}`.
  - Failure endpoint:
    `GET /jobExecutions`
  - Why this fails:
    The exit-code filter excludes executions whose stored exit code does not equal the query value.
  - Intentionally violated constraints:
    The query exit code does not reuse the stored or returned `jobExecution.exitCode`.
- Branch 2:
  - Preconditions:
    - The only relevant execution was started asynchronously by `POST /jobExecutions` with body `name={name}` and `asynchronous=true`.
    - The request immediately uses query `exitCode=COMPLETED` before the asynchronous execution has completed and updated its stored exit code.
  - Failure endpoint:
    `GET /jobExecutions`
  - Why this fails:
    Asynchronous starts can return before the execution has a terminal exit code; a query for `COMPLETED` will not match an execution still reporting `UNKNOWN` or another non-terminal state.
  - Intentionally violated constraints:
    The query reuses an exit code before that value has been produced by the execution.

Endpoint coverage:
- Covers:
  `GET /jobExecutions`
- Distinct meaning:
  Applies the exit-code filter of the job execution collection endpoint.

### Function 8: find executions for a job by exit code

Function name:
find job executions by job name and exit code

Core endpoint(s):
- `GET /jobExecutions`

Preconditions:
- A Spring Batch job named `{jobName}` exists in the `JobRegistry`. No documented REST endpoint creates this state; it can be satisfied by application bean wiring, by calling `JobBuilder.registerJob(...)` or `JobBuilder.createJob(...)`, or by directly constructing the registry state in a test fixture. There is no database setup alternative for job registration in the visible source.
- At least one persisted job execution has both `jobExecution.jobName={jobName}` and `jobExecution.exitCode={exitCode}` in the Spring Batch `JobRepository`. This can be satisfied by directly seeding Spring Batch metadata tables when a persistent repository is used, by directly seeding the map-backed repository used by the visible `AdHocBatchConfig`, or by calling `POST /jobExecutions` with body `name={jobName}`, `asynchronous=false`, and optional `properties={...}` and then reusing the returned or stored `jobExecution.exitCode`.
- If the API is used to establish the execution state, the `jobName` query value must equal the request body `name`, and the `exitCode` query value must be obtained from the resulting job execution after it has completed.

Successful execution:
- Result:
  Returns job execution resources for the specified job name whose exit code also matches the specified exit code.
- Invocation:
  Step 1: `GET /jobExecutions` with query `jobName={jobName}`, query `exitCode={exitCode}`, optional query `limitPerJob={limitPerJob}`, and an `Accept` header compatible with `application/hal+json`
- Constraints:
  Both filters must match the same persisted execution. OpenAPI documents the filters as optional strings and does not document error responses for invalid or malformed query values; the visible source does not include the controller implementation that enforces or normalizes these query parameters.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The repository contains an execution with `jobExecution.jobName={jobName}` and `jobExecution.exitCode={actualExitCode}`. This can be satisfied by directly seeding execution metadata or by calling `POST /jobExecutions` with body `name={jobName}` and waiting for completion.
    - The request uses query `exitCode={differentExitCode}` instead of `{actualExitCode}`.
  - Failure endpoint:
    `GET /jobExecutions`
  - Why this fails:
    The job-name filter matches, but the exit-code filter excludes the execution.
  - Intentionally violated constraints:
    The query exit code is not the exit code stored for the matching job execution.
- Branch 2:
  - Preconditions:
    - The repository contains an execution with `jobExecution.jobName={actualJobName}` and `jobExecution.exitCode={exitCode}`. This can be satisfied by directly seeding execution metadata or by calling `POST /jobExecutions` with body `name={actualJobName}` and waiting for completion.
    - The request uses query `jobName={differentJobName}` instead of `{actualJobName}`.
  - Failure endpoint:
    `GET /jobExecutions`
  - Why this fails:
    The exit-code filter matches, but the job-name filter excludes the execution.
  - Intentionally violated constraints:
    The query job name is not the job name stored for the matching execution.

Endpoint coverage:
- Covers:
  `GET /jobExecutions`
- Distinct meaning:
  Applies the combined job-name and exit-code filters of the job execution collection endpoint.

### Function 9: retrieve a job execution by id

Function name:
get job execution by id

Core endpoint(s):
- `GET /jobExecutions/{id}`

Preconditions:
- A job execution with id `{id}` exists in the Spring Batch `JobRepository`. This can be satisfied by directly seeding Spring Batch metadata tables when a persistent repository is used, by directly seeding the map-backed repository used by the visible `AdHocBatchConfig`, or by calling `POST /jobExecutions` with body `name={name}`, optional `asynchronous=false`, and optional `properties={...}` for a registered job named `{name}`.
- If the API is used to establish the execution state, the `{id}` path value must be obtained from the returned `jobExecution.id` or from the created execution's stored metadata.

Successful execution:
- Result:
  Returns the job execution resource identified by `{id}`.
- Invocation:
  Step 1: `GET /jobExecutions/{id}` with path value `id={id}` and an `Accept` header compatible with `application/hal+json`
- Constraints:
  `{id}` must be a numeric Spring Batch job execution id. The OpenAPI file documents `id` as `int64` and documents only a `200` response; the visible source does not contain the controller implementation for missing ids.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No job execution with id `{id}` exists in the Spring Batch `JobRepository`. This can be produced by starting with an empty repository, clearing Spring Batch metadata, or intentionally not calling `POST /jobExecutions` and not directly inserting execution metadata for `{id}`.
  - Failure endpoint:
    `GET /jobExecutions/{id}`
  - Why this fails:
    The endpoint performs an id-scoped lookup; a nonexistent id cannot be mapped to a job execution resource. The OpenAPI file documents only `200`, and the visible source does not include the controller implementation that maps this lookup failure to an HTTP response.
  - Intentionally violated constraints:
    The path id does not identify a persisted job execution.
- Branch 2:
  - Preconditions:
    - A job execution exists with id `{actualId}` in the Spring Batch `JobRepository`. This can be satisfied by directly seeding execution metadata or by calling `POST /jobExecutions` and recording the returned `jobExecution.id`.
    - The request uses path `id={differentId}` instead of `{actualId}`.
  - Failure endpoint:
    `GET /jobExecutions/{id}`
  - Why this fails:
    The path id is the lookup key, so an id mismatch retrieves a different execution or no execution at all.
  - Intentionally violated constraints:
    The path id does not reuse the id generated or stored for the intended execution.

Endpoint coverage:
- Covers:
  `GET /jobExecutions/{id}`
- Distinct meaning:
  Retrieves one job execution resource by its path-scoped execution id.
