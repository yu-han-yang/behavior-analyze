Source/API note: the Swagger file exposes REST endpoints, but the allowed `src` tree does not contain the controller/resource classes named by the OpenAPI tags. The visible implementation mainly supports `POST /jobExecutions` through `AdHocStarter`, job parameter conversion, and Spring Batch job launching. For read endpoints, behavior is inferred from OpenAPI because exact controller logic is not present.

### Behavior 1: list jobs

Behavior name:
list registered Spring Batch jobs

Successful execution:
- Result:
  This behavior returns the collection of Spring Batch jobs known to the application.
- Endpoint sequence:
  Step 1: `GET /jobs`
- Constraints:
  No specific job resource must be created through the documented API. Jobs are registered outside these REST endpoints.

Failure or exceptional branches:
- No failure or exceptional branch is documented in the OpenAPI file or confirmed by the visible source.

Endpoint coverage:
- Covers:
  `GET /jobs`
- Distinct meaning:
  Global discovery/listing of available Spring Batch jobs.

### Behavior 2: retrieve job by name

Behavior name:
get job by name

Successful execution:
- Result:
  This behavior retrieves the job named `{jobName}`.
- Endpoint sequence:
  Step 1: `GET /jobs/{jobName}`
- Constraints:
  `{jobName}` must identify a registered Spring Batch job. No documented endpoint creates jobs, so the required job registration must already exist outside the REST API.

Failure or exceptional branches:
- No concrete failure response is documented or visible in source. A missing `{jobName}` is a likely lookup failure, but the controller implementation is absent from the allowed files.

Endpoint coverage:
- Covers:
  `GET /jobs/{jobName}`
- Distinct meaning:
  Resource-scoped lookup of one registered Spring Batch job.

### Behavior 3: start asynchronous job execution

Behavior name:
start job asynchronously

Successful execution:
- Result:
  This behavior starts a new execution of the registered Spring Batch job named in the request body and returns a job execution resource.
- Endpoint sequence:
  Step 1: `POST /jobExecutions`
- Constraints:
  Request body `name` must match a registered job. Request body `asynchronous` must be `true`. Request body `properties` is optional and is converted into Spring Batch job parameters. Source preserves `Date`, `Long`, and `Double` values as typed parameters and converts other values to strings. By default, the implementation adds a generated `uuid` job parameter, so repeated starts can create distinct executions.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The request body `name` does not match a registered Spring Batch job.
  - Endpoint group:
    Step 1: `POST /jobExecutions`
  - Failure endpoint:
    `POST /jobExecutions`
  - Why this fails:
    The implementation calls `JobLocator.getJob(name)` before launching. If no job is found, the start operation is wrapped in a runtime failure.
  - Intentionally violated constraints:
    The documented API has no prior endpoint that registers `{name}` as a job.

- Branch 2:
  - Unsatisfied condition:
    Two asynchronous executions of the same job rely on the deprecated singleton property resolver while using different properties.
  - Endpoint group:
    Step 1: `POST /jobExecutions`
    Step 2: `POST /jobExecutions`
  - Failure endpoint:
    `POST /jobExecutions`
  - Why this fails:
    The visible source warns that the singleton resolver is keyed by job name, not execution id, so concurrent async runs of the same job may read another execution’s properties.
  - Intentionally violated constraints:
    The two requests use the same body `name`, `asynchronous: true`, different `properties`, and overlap in time.

Endpoint coverage:
- Covers:
  `POST /jobExecutions`
- Distinct meaning:
  Starts a job using the async launcher branch selected by `JobConfig.asynchronous == true`.

### Behavior 4: start synchronous job execution

Behavior name:
start job synchronously

Successful execution:
- Result:
  This behavior starts the registered Spring Batch job named in the body and waits for the synchronous launcher to return a job execution resource.
- Endpoint sequence:
  Step 1: `POST /jobExecutions`
- Constraints:
  Request body `name` must match a registered job. Request body `asynchronous` must be `false` or omitted, because the Java boolean defaults to `false`. Optional `properties` become job parameters, and the default generated `uuid` parameter distinguishes repeated starts.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The request body `name` is missing, null, or not a registered job name.
  - Endpoint group:
    Step 1: `POST /jobExecutions`
  - Failure endpoint:
    `POST /jobExecutions`
  - Why this fails:
    The implementation must resolve the job before launching it; unresolved job names are wrapped as start failures.
  - Intentionally violated constraints:
    `name` does not identify an existing registered Spring Batch job.

- Branch 2:
  - Unsatisfied condition:
    Spring Batch rejects or fails the launch.
  - Endpoint group:
    Step 1: `POST /jobExecutions`
  - Failure endpoint:
    `POST /jobExecutions`
  - Why this fails:
    `JobLauncher.run(...)` can raise `JobExecutionException`, which the implementation wraps as a batch runtime failure. The OpenAPI file does not document the HTTP error shape.
  - Intentionally violated constraints:
    The job launch violates Spring Batch execution constraints or fails inside the job infrastructure.

Endpoint coverage:
- Covers:
  `POST /jobExecutions`
- Distinct meaning:
  Starts a job using the sync launcher branch selected by `JobConfig.asynchronous == false`.

### Behavior 5: list job executions

Behavior name:
list job execution history

Successful execution:
- Result:
  This behavior returns a collection of job execution resources.
- Endpoint sequence:
  Step 1: `GET /jobExecutions`
- Constraints:
  No specific existing execution is required for the endpoint to return a collection. Optional `limitPerJob` limits the number of executions returned per job and defaults to `3`.

Failure or exceptional branches:
- No failure or exceptional branch is documented in the OpenAPI file or confirmed by the visible source.

Endpoint coverage:
- Covers:
  `GET /jobExecutions`
- Distinct meaning:
  Global listing of job executions without requiring a specific job or execution id.

### Behavior 6: find executions for a job

Behavior name:
find job executions by job name

Successful execution:
- Result:
  This behavior returns executions belonging to the job named by query parameter `jobName`.
- Endpoint sequence:
  Step 1: `POST /jobExecutions`
  Step 2: `GET /jobExecutions`
- Constraints:
  Step 1 body `name` must be the same value used as Step 2 query parameter `jobName`. Step 1 also requires that this name already be a registered Spring Batch job. Step 2 may include `limitPerJob`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The filtered job execution state is intentionally not created first.
  - Endpoint group:
    Step 1: `GET /jobExecutions`
  - Failure endpoint:
    `GET /jobExecutions`
  - Why this fails:
    The endpoint can only return matching executions if executions for query `jobName` already exist. The omitted prerequisite is `POST /jobExecutions`.
  - Intentionally violated constraints:
    Query `jobName` names a job for which no prior documented execution was started.

Endpoint coverage:
- Covers:
  `GET /jobExecutions`
- Distinct meaning:
  Filtered lookup by `jobName`.

### Behavior 7: find executions by exit code

Behavior name:
find job executions by exit code

Successful execution:
- Result:
  This behavior returns executions whose exit code matches query parameter `exitCode`.
- Endpoint sequence:
  Step 1: `POST /jobExecutions`
  Step 2: `GET /jobExecutions`
- Constraints:
  Step 1 should use `asynchronous: false` or otherwise complete before Step 2. Step 2 query `exitCode` must reuse `jobExecution.exitCode` from the Step 1 response. Step 2 may include `limitPerJob`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The later query uses an exit code that was not produced by the created execution.
  - Endpoint group:
    Step 1: `POST /jobExecutions`
    Step 2: `GET /jobExecutions`
  - Failure endpoint:
    `GET /jobExecutions`
  - Why this fails:
    The filter does not match the execution created in Step 1, so that execution is excluded from the result.
  - Intentionally violated constraints:
    Step 2 query `exitCode` is different from Step 1 response `jobExecution.exitCode`.

Endpoint coverage:
- Covers:
  `GET /jobExecutions`
- Distinct meaning:
  Filtered lookup by execution exit code.

### Behavior 8: find executions for a job by exit code

Behavior name:
find job executions by job name and exit code

Successful execution:
- Result:
  This behavior returns executions for a specific job that also have a specific exit code.
- Endpoint sequence:
  Step 1: `POST /jobExecutions`
  Step 2: `GET /jobExecutions`
- Constraints:
  Step 1 body `name` must match Step 2 query `jobName`. Step 2 query `exitCode` must reuse Step 1 response `jobExecution.exitCode`. Use `asynchronous: false` or wait until an exit code exists before Step 2. Step 2 may include `limitPerJob`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The query combines a matching `jobName` with a mismatched `exitCode`, or a matching `exitCode` with a mismatched `jobName`.
  - Endpoint group:
    Step 1: `POST /jobExecutions`
    Step 2: `GET /jobExecutions`
  - Failure endpoint:
    `GET /jobExecutions`
  - Why this fails:
    Both filters must match the same execution for that execution to appear in the result.
  - Intentionally violated constraints:
    Step 2 does not reuse both Step 1 body `name` and Step 1 response `jobExecution.exitCode`.

Endpoint coverage:
- Covers:
  `GET /jobExecutions`
- Distinct meaning:
  Combined filtered lookup by job name and exit code.

### Behavior 9: retrieve job execution by id

Behavior name:
get job execution by id

Successful execution:
- Result:
  This behavior retrieves the job execution identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /jobExecutions`
  Step 2: `GET /jobExecutions/{id}`
- Constraints:
  Step 1 response `jobExecution.id` must be reused as Step 2 path `{id}`. Step 1 body `name` must identify a registered Spring Batch job.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The path `{id}` does not identify the execution created by the API.
  - Endpoint group:
    Step 1: `POST /jobExecutions`
    Step 2: `GET /jobExecutions/{id}`
  - Failure endpoint:
    `GET /jobExecutions/{id}`
  - Why this fails:
    The lookup is for a different or nonexistent execution id. The OpenAPI file does not document the error response, and the controller implementation is absent from the visible source.
  - Intentionally violated constraints:
    Step 2 path `{id}` is not the `jobExecution.id` produced by Step 1.

Endpoint coverage:
- Covers:
  `GET /jobExecutions/{id}`
- Distinct meaning:
  Resource-scoped lookup of one job execution by id.