# Business Behavior Coverage Report

## Executive Summary

- Project under analysis: `/Users/yangyuhan/behavior-analyze/spring-batch-rest`
- Business specification: [business-behavior.md](/Users/yangyuhan/behavior-analyze/spring-batch-rest/business-behavior.md)
- Function inventory: [full-behavior.md](/Users/yangyuhan/behavior-analyze/spring-batch-rest/full-behavior.md)
- Test suites analyzed: [tests/EM_spring_batch_rest_True_25_false_false_SPECIFIED_false_0_Test.java](/Users/yangyuhan/behavior-analyze/spring-batch-rest/tests/EM_spring_batch_rest_True_25_false_false_SPECIFIED_false_0_Test.java), `26` active `@Test` methods. The generated file comment says `27`, but only 26 test methods are present.
- Application calls analyzed: `37` REST Assured calls across `6` normalized routes, including the non-business `/v3/api-docs`; `36` calls target documented business routes across `5` normalized routes.
- Coverage reports analyzed: [reports/report.xml](/Users/yangyuhan/behavior-analyze/spring-batch-rest/reports/report.xml), [coverage/evomaster_10550_spring-batch-rest__10551/report.xml](/Users/yangyuhan/behavior-analyze/spring-batch-rest/coverage/evomaster_10550_spring-batch-rest__10551/report.xml), [coverage/evomaster_10560_spring-batch-rest__10561/report.xml](/Users/yangyuhan/behavior-analyze/spring-batch-rest/coverage/evomaster_10560_spring-batch-rest__10561/report.xml), with [reports/report.csv](/Users/yangyuhan/behavior-analyze/spring-batch-rest/reports/report.csv) used as a counter cross-check.
- Source roots analyzed: [src/main/java](/Users/yangyuhan/behavior-analyze/spring-batch-rest/src/main/java), [tests](/Users/yangyuhan/behavior-analyze/spring-batch-rest/tests)
- Total documented behaviors: `9`
- Total documented failure entries: `25`
- Covered / Partially Covered / Not Covered / Unclear: `3 / 5 / 1 / 0`
- Business behavior coverage: `5.5/9 (61.1%)`
- Function/API invocation coverage: `9/9 (100.0%)`, plus `0` ambiguous shared-route attempts credited
- Required-step attempt coverage: `20/20 (100.0%)`
- Required-step application-reach coverage: `20/20 (100.0%)`
- Required-step context-valid success coverage: `16/20 (80.0%)`
- Happy-path behavior coverage: `5/9 (55.6%)`
- Documented business-failure coverage: `7/25 (28.0%)`
- Unique source business-branch coverage: `7/25 (28.0%)`
- Behavior outcome checklist coverage: `12/34 (35.3%)`
- Optional verification execution coverage: `1/8 (12.5%)`
- Combined JaCoCo signal by element-level union: lines `306/504 (60.7%)`, branches `46/307 (15.0%)`, methods `142/238 (59.7%)`, classes `32/43 (74.4%)`. The primary XML aggregate class counter is `32/42`; the `coverage/` XML files are narrower example-app reports and do not increase line, branch, or method union coverage.

The execution funnel is strong at the transport layer but weak at the business-outcome layer. Every documented function has at least one generated invocation, and JaCoCo shows the mapped controller/provider methods were reached. Four required filter steps are not context-valid successes because tests use random or non-reused `jobName` and `exitCode` values. Happy paths are covered for discovery, synchronous launch, asynchronous launch, global history, and id lookup. The main gap is concrete failure coverage: only 7 of 25 documented failure entries are uniquely proven by tests, source discriminators, and observable responses.

## Inventory Validation

| Item | Result |
|---|---:|
| Parsed behavior count | `9` |
| Parsed failure-entry count | `25` |
| Behaviors with `Failure and exceptional cases: None.` | `B1`, `B2` |
| Malformed or unparsed behavior entries | `0` |
| Malformed or unparsed failure entries | `0` |
| Exact-function-name mapping failures through `full-behavior.md` | `0` |
| Distinct documented functions owned by behaviors | `9` |
| Required execution workflow steps | `20` |
| Optional verification workflow steps | `8` |
| Behavior-outcome denominator | `9` happy paths + `25` failures = `34` |

The prompt's expected `81` behaviors, `461` failures, and `542` behavior-outcome denominator do not match this repository's actual [business-behavior.md](/Users/yangyuhan/behavior-analyze/spring-batch-rest/business-behavior.md). The report is complete against the parsed local inventory and does not silently carry over the larger template denominator.

## Coverage Matrix

| ID | Business Behavior | Required Steps Attempted | Application Reached | Context-Valid Steps | Happy Path | Failure Coverage | Optional Verification | Status | Confidence |
|---|---|---:|---:|---:|---|---:|---|---|---|
| B1 | Discover registered batch jobs | `1/1` | `1/1` | `1/1` | Covered | N/A | N/A | Covered | High |
| B2 | Inspect a named batch job | `2/2` | `2/2` | `1/2` | Not Covered | N/A | N/A | Not Covered | Medium |
| B3 | Start a synchronous batch execution | `2/2` | `2/2` | `2/2` | Covered | `2/7` | `1/2` | Partially Covered | High |
| B4 | Submit an asynchronous batch execution | `2/2` | `2/2` | `2/2` | Covered | `1/7` | `0/2` | Partially Covered | High |
| B5 | Review global execution history | `1/1` | `1/1` | `1/1` | Covered | `1/1` | N/A | Covered | High |
| B6 | Retrieve a specific execution record | `3/3` | `3/3` | `3/3` | Covered | `1/1` | `0/1` | Covered | High |
| B7 | Review executions for one job | `3/3` | `3/3` | `2/3` | Not Covered | `0/3` | `0/1` | Partially Covered | Medium |
| B8 | Review executions by outcome | `3/3` | `3/3` | `2/3` | Not Covered | `1/2` | `0/1` | Partially Covered | Medium |
| B9 | Review one job's executions by outcome | `3/3` | `3/3` | `2/3` | Not Covered | `1/4` | `0/1` | Partially Covered | Medium |

The execution funnel is internally consistent: context-valid success `16` <= application reached `20` <= attempted `20`.

## Function/API Invocation Checklist

| Exact Function Name | Method/Route | Attempted? | Distinguishable? | Representative Tests | Result Classes |
|---|---|---|---|---|---|
| `list registered Spring Batch jobs` | `GET /jobs` | Yes | Yes | `test_6_getOnJobsReturnsObject` | Successful `200` with `personJob` |
| `get registered job by name` | `GET /jobs/{jobName}` | Yes | Yes | `test_20_getOnJobReturnsObject` | Successful-looking `200`; business validity weak because the path name is arbitrary |
| `start job asynchronously` | `POST /jobExecutions` with `asynchronous=true` | Yes | Yes, by request body | `test_1`, `test_2`, `test_15`, `test_16`, `test_17` | Successful `200 STARTING/UNKNOWN`; business failure `404 NoSuchJobException` |
| `start job synchronously` | `POST /jobExecutions` with `asynchronous=false` or omitted | Yes | Yes, by request body/default | `test_4`, `test_5`, `test_9`, `test_12`, `test_19`, `test_21`, `test_23` | Successful `200 COMPLETED`; business failures `500 RuntimeException`, `404 NoSuchJobException`; binding `400` |
| `list job execution history` | `GET /jobExecutions` with no business filters, optional `limitPerJob` | Yes | Yes | `test_9`, `test_17` | Successful `200`; negative-limit failure through empty/global filter path |
| `find job executions by job name` | `GET /jobExecutions?jobName=...` | Yes | Yes when only `jobName` is present | `test_7`, `test_13` | Endpoint success/probe only; no context-valid matching-result assertion |
| `find job executions by exit code` | `GET /jobExecutions?exitCode=...` | Yes | Yes when only `exitCode` is present | `test_0`, `test_10`, `test_16` | Endpoint success/probe; `500 NullPointerException` for missing cached exit-code bucket |
| `find job executions by job name and exit code` | `GET /jobExecutions?jobName=...&exitCode=...` | Yes | Yes when both filters are non-empty | `test_1`, `test_8`, `test_12`, `test_14`, `test_15` | Endpoint success/probe; `500 NullPointerException` for matched job with missing exit-code bucket |
| `get job execution by id` | `GET /jobExecutions/{id}` | Yes | Yes | `test_21`, `test_22`, `test_23`, `test_25`, `test_26` | Successful `200`; business failure `404 NoSuchJobExecutionException` |

No shared-route attempt was silently credited to multiple exact functions. Empty `jobName=` calls are treated as global or exit-code filter evidence only when the asserted response and source discriminator make that route meaning traceable.

## Behavior Details

### `B1`: `Discover registered batch jobs`

- Business goal: Find the batch jobs currently exposed by the service.
- Starting point: Registered jobs already exist through application wiring or fixture setup.
- Expected business result: Return exposed job resources and preserve registry state.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | `GET /jobs`, `Accept=application/hal+json`, no path/query/body; return `job.name` values | Yes | Yes | Yes | `test_6_getOnJobsReturnsObject` asserts one returned job named `personJob` | `JobController.all` and `JobService.jobs` are covered |

- Happy-path item: `Covered`. The test directly proves registry discovery for the sample service.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| N/A | N/A | None documented | N/A | N/A |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| N/A | N/A | None documented | N/A | High | N/A | N/A | N/A |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `1/1`
- Status and confidence: `Covered`, High
- Exact gap: None.
- Recommended test IDs that close the gap: None.

### `B2`: `Inspect a named batch job`

- Business goal: Retrieve the resource representation for a specific batch job name.
- Starting point: A usable job name exists in the same registry scope.
- Expected business result: Return a job resource for the registered path-scoped name without changing state.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | `GET /jobs`; capture returned `job.name` | Yes | Yes | Yes | `test_6_getOnJobsReturnsObject` returns `personJob` | `JobController.all` covered |
| 2 | `get registered job by name` | `GET /jobs/{jobName}` with `jobName={job.name from step 1}` | Yes | Yes | No | `test_20_getOnJobReturnsObject` calls `/jobs/31SVHZ51PHVA`, not the discovered `personJob` | `JobController.get` covered |

- Happy-path item: `Not Covered`. No continuous test binds discovery output to `GET /jobs/personJob`.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| N/A | N/A | None documented | N/A | N/A |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| N/A | N/A | None documented | N/A | High | N/A | N/A | N/A |

- Required-step summary: attempted `2/2`, application reached `2/2`, context-valid success `1/2`
- Happy-path summary: `0/1`
- Failure summary: `0/0`
- Behavior outcome checklist summary: `0/1`
- Status and confidence: `Not Covered`, Medium
- Exact gap: The named lookup is invoked, but not with a registered name returned by discovery or an equivalent documented shortcut.
- Recommended test IDs that close the gap: `T1`.

### `B3`: `Start a synchronous batch execution`

- Business goal: Run a registered batch job through the synchronous launcher and receive the returned execution state.
- Starting point: A launchable job exists in the registry; generated tests use the documented shortcut of known sample job `personJob`.
- Expected business result: Create a new execution record for the selected job, with sample tests observing `COMPLETED`.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | `GET /jobs`; obtain launchable `job.name` | Yes | Yes | Yes | `test_6_getOnJobsReturnsObject` confirms `personJob`; launch tests use that known fixture value | `JobController.all` covered |
| 2 | `start job synchronously` | `POST /jobExecutions`, JSON body `name=personJob`, `asynchronous=false` or omitted, properties object | Yes | Yes | Yes | `test_12`, `test_21`, and `test_23` assert `jobName=personJob`, `exitCode=COMPLETED`, `status=COMPLETED`, empty exceptions | `JobExecutionController.put` covered; [AdHocStarter.start(JobConfig)](/Users/yangyuhan/behavior-analyze/spring-batch-rest/src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java) covered `15/15` lines, `3/4` branches |

- Happy-path item: `Covered`. Existing-state shortcut is satisfied by the registered sample job, and synchronous launch reaches the documented terminal result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get job execution by id` | `GET /jobExecutions/{id}` with id from synchronous launch | Yes | `test_21` extracts `Location` from the POST and follows it with a successful `GET` |
| 2 | `find job executions by job name` | `GET /jobExecutions?jobName={body name}` after launch | No | No sync launch test performs a job-name-only history query with assertions |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `start job synchronously` | `RuntimeException` wrapping `JobLocator.getJob(jobConfig.getName())` null-name branch | Parsed `JobConfig.name=null` | Yes | High | `test_4` and `test_5` post valid JSON without `name`, assert `500`, null-job message, `RuntimeException` | No execution success is asserted; failure happens before launch | `AdHocStarter.start` exception path reached |
| `start job synchronously` | `NoSuchJobException` from `JobLocator.getJob(jobConfig.getName())` | Non-null unknown job name | Yes | High | `test_9`, `test_14`, and `test_19` assert `404`, `NoSuchJobException`, and unknown job message | No execution for the unknown job is asserted | Exception handler and `AdHocStarter.start` reached |
| `start job synchronously` | `JobParametersInvalidException` | Registered job rejects converted parameters | No | High | No test configures a validator-rejecting registered job | Not observed | `JobParamUtil` covered, but no validator failure branch proven |
| `start job synchronously` | `JobExecutionAlreadyRunningException` | Same identifying parameters already running | No | High | No concurrent same-instance synchronous launch | Not observed | No precise branch evidence |
| `start job synchronously` | `JobInstanceAlreadyCompleteException` | Relaunch completed instance with same identifying parameters | No | High | Default unique `uuid` parameter prevents this scenario in generated tests | Not observed | No precise branch evidence |
| `start job synchronously` | `JobRestartException` | Existing instance restart state is not restartable | No | High | No restart-state fixture | Not observed | No precise branch evidence |
| `start job synchronously` | `JobExecutionController.put` failed-exit-code branch | Synchronous launch returns `ExitStatus.FAILED` | No | High | No registered job finishes with `FAILED` and then maps to controller error | Persisted failed execution not asserted | `JobExecutionController.put` branch `1/2`; failed branch not proven |

- Required-step summary: attempted `2/2`, application reached `2/2`, context-valid success `2/2`
- Happy-path summary: `1/1`
- Failure summary: `2/7`
- Behavior outcome checklist summary: `3/8`
- Status and confidence: `Partially Covered`, High
- Exact gap: Launch-rule and failed-exit branches are not intentionally established.
- Recommended test IDs that close the gap: `T1`, `T3`, `T6`.

### `B4`: `Submit an asynchronous batch execution`

- Business goal: Start a registered job without waiting for completion.
- Starting point: A launchable job exists in the registry; generated tests use known sample job `personJob`.
- Expected business result: Create an execution handle that may initially report `STARTING` and `UNKNOWN`.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | `GET /jobs`; obtain launchable `job.name` | Yes | Yes | Yes | `test_6_getOnJobsReturnsObject` confirms `personJob`; async tests use that known value | `JobController.all` covered |
| 2 | `start job asynchronously` | `POST /jobExecutions`, JSON body `name=personJob`, `asynchronous=true`, properties object | Yes | Yes | Yes | `test_1`, `test_2`, `test_15`, and `test_17` assert `STARTING`, `UNKNOWN`, null timestamps, and empty exceptions | `AdHocStarter.start` selects async branch through `JobConfig.isAsynchronous()`; `JobPropertyResolvers.started` covered |

- Happy-path item: `Covered`. Async submission returns a documented execution handle for the registered job.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get job execution by id` | Later inspect generated async execution id | No | No async test captures and follows the generated id |
| 2 | `find job executions by job name` | Later inspect history for same async job | No | `test_17` performs global history only, not `jobName=personJob` |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `start job asynchronously` | `RuntimeException` wrapping null-name `JobLocator.getJob(...)` branch | Parsed `JobConfig.name=null` with async request | No | High | Missing-name tests use the synchronous default; async test uses empty string, not null | Not observed | General exception path covered, exact async null branch not proven |
| `start job asynchronously` | `NoSuchJobException` from `JobLocator.getJob(...)` | Non-null unknown job name, including empty string | Yes | High | `test_16` posts `name=""`, `asynchronous=true`, asserts `404`, `NoSuchJobException` | No async execution is accepted | `AdHocStarter.start` exception path reached |
| `start job asynchronously` | `JobParametersInvalidException` | Registered job rejects converted parameters | No | High | No validator-rejecting async launch | Not observed | No precise branch evidence |
| `start job asynchronously` | `JobExecutionAlreadyRunningException` | Same identifying parameters already running | No | High | No same-instance concurrent async launch | Not observed | No precise branch evidence |
| `start job asynchronously` | `JobInstanceAlreadyCompleteException` | Relaunch completed instance with same identifying parameters | No | High | Default unique `uuid` prevents generated tests from proving this | Not observed | No precise branch evidence |
| `start job asynchronously` | `JobRestartException` | Existing instance restart state is not restartable | No | High | No restart-state fixture | Not observed | No precise branch evidence |
| `start job asynchronously` | `JobPropertyResolvers.started(JobConfig)` job-name-keyed resolver overwrite | Concurrent same-job async executions use different properties via deprecated resolver | No | High | Generated tests start async jobs but do not run overlapping property-sensitive assertions | No incorrect property use is asserted | [JobPropertyResolvers.started](/Users/yangyuhan/behavior-analyze/spring-batch-rest/src/main/java/com/github/chrisgleissner/springbatchrest/util/core/property/JobPropertyResolvers.java) covered, but concurrency branch not proven |

- Required-step summary: attempted `2/2`, application reached `2/2`, context-valid success `2/2`
- Happy-path summary: `1/1`
- Failure summary: `1/7`
- Behavior outcome checklist summary: `2/8`
- Status and confidence: `Partially Covered`, High
- Exact gap: Async failure tests cover unknown name only; concurrency and launch-rule failures remain uncovered.
- Recommended test IDs that close the gap: `T2`, `T7`.

### `B5`: `Review global execution history`

- Business goal: View stored job executions across jobs.
- Starting point: Repository may contain zero or more executions; tests observe sample history after reset and after launches.
- Expected business result: Return a HAL collection without mutating executions.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `list job execution history` | `GET /jobExecutions`, optional non-negative `limitPerJob`, no business filters required | Yes | Yes | Yes | `test_17` calls `GET /jobExecutions?limitPerJob=3` and asserts three `personJob` executions with `COMPLETED` status and exit code | `JobExecutionController.all`, `JobExecutionService.jobExecutions`, `CachedJobExecutionProvider.getJobExecutions` covered |

- Happy-path item: `Covered`. The collection endpoint returns stored execution records with asserted business fields.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| N/A | N/A | None documented | N/A | N/A |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `list job execution history` | `IllegalArgumentException` from `Stream.limit(limitPerJob)` in cached-provider path | Negative `limitPerJob`, cached path selected, at least one cached execution scanned | Yes | High | `test_2` creates async `personJob`, then `GET /jobExecutions?jobName=&limitPerJob=-40` asserts `500`, message `-40`, `IllegalArgumentException` | Prior async POST establishes cached execution state | `CachedJobExecutionProvider.lambda$getJobExecutions$2` and provider method covered |

- Required-step summary: attempted `1/1`, application reached `1/1`, context-valid success `1/1`
- Happy-path summary: `1/1`
- Failure summary: `1/1`
- Behavior outcome checklist summary: `2/2`
- Status and confidence: `Covered`, High
- Exact gap: None against the documented B5 inventory.
- Recommended test IDs that close the gap: None.

### `B6`: `Retrieve a specific execution record`

- Business goal: Inspect one generated execution by id.
- Starting point: A registered launchable job exists, or an equivalent execution exists in the same repository.
- Expected business result: Return the selected execution record without changing it.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | Obtain launchable `job.name` | Yes | Yes | Yes | `test_6` confirms `personJob`; B6 launch uses known fixture shortcut | `JobController.all` covered |
| 2 | `start job synchronously` | Create execution and capture generated id/Location | Yes | Yes | Yes | `test_21` posts `personJob` synchronously and extracts the `Location` header | `JobExecutionController.put` and `AdHocStarter.start` covered |
| 3 | `get job execution by id` | `GET /jobExecutions/{id}` using the generated id | Yes | Yes | Yes | `test_21` follows the extracted `Location` and asserts `jobName=personJob`, `exitCode=COMPLETED`, `status=COMPLETED` | `JobExecutionController.get` and `JobExecutionService.jobExecution` covered |

- Happy-path item: `Covered`. The test preserves the response-to-request `Location -> id` binding.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `list job execution history` | Verify the generated execution appears in history | No | History tests do not assert the specific id generated by `test_21` |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `get job execution by id` | `javax.batch.operations.NoSuchJobExecutionException` | Parsed numeric path id does not identify persisted execution | Yes | High | `test_26` calls `/jobExecutions/-16776668` and asserts `404`, `NoSuchJobExecutionException`, and missing-id message | No mutation is expected for read failure | `JobExecutionService.jobExecution` branch `2/2`; exception handler covered |

- Required-step summary: attempted `3/3`, application reached `3/3`, context-valid success `3/3`
- Happy-path summary: `1/1`
- Failure summary: `1/1`
- Behavior outcome checklist summary: `2/2`
- Status and confidence: `Covered`, High
- Exact gap: Optional history verification of the same id is missing, but optional verification is not required for behavior coverage.
- Recommended test IDs that close the gap: `T1`.

### `B7`: `Review executions for one job`

- Business goal: View execution history scoped to one job name.
- Starting point: A registered launchable job exists, and matching execution history is created or already present.
- Expected business result: Return execution records whose stored `jobExecution.jobName` matches the query.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | Obtain returned `job.name` | Yes | Yes | Yes | `test_6` confirms `personJob` | `JobController.all` covered |
| 2 | `start job synchronously` | Create at least one execution for that job | Yes | Yes | Yes | `test_12`, `test_21`, and `test_23` create `personJob` executions | Launch path covered |
| 3 | `find job executions by job name` | `GET /jobExecutions?jobName={body name}` with non-negative limit and matching-result assertions | Yes | Yes | No | `test_7` and `test_13` use arbitrary job names; no test queries `jobName=personJob` alone after setup and asserts matching rows | Provider methods covered, but evidence is probe-level |

- Happy-path item: `Not Covered`. No generated test proves a job-name-filtered result set for the same job created or selected by the scenario.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get job execution by id` | Inspect one matching execution id from setup | No | Id lookup exists in `test_21`, but not as part of a job-name filter scenario |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `find job executions by job name` | `PatternSyntaxException` from `Pattern.compile(jobNameRegexp)` in cached-provider path | Invalid regex jobName with cached provider path | No | No invalid-regex jobName test | Not observed | Provider regex code covered generally, not failure branch |
| `find job executions by job name` | `PatternSyntaxException` from `Pattern.compile(jobNameRegexp)` in all-provider fallback path | Invalid regex jobName with large `limitPerJob` fallback | No | No invalid-regex large-limit test | Not observed | All-provider regex path covered generally, not failure branch |
| `find job executions by job name` | `IllegalArgumentException` from `Stream.limit(limitPerJob)` in cached-provider path | Negative limit, cached path, job-name filter matches cached bucket | No | `test_2` has `jobName=` and is credited to global history, not a present matching job-name filter; `test_11` has negative limit but returns `200` | Not observed | Negative-limit provider branch covered for B5, not this exact function/condition |

- Required-step summary: attempted `3/3`, application reached `3/3`, context-valid success `2/3`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`
- Status and confidence: `Partially Covered`, Medium
- Exact gap: Tests reach the shared collection endpoint but do not prove correct job-name filtering or documented regex/limit failure branches.
- Recommended test IDs that close the gap: `T1`, `T4`, `T5`.

### `B8`: `Review executions by outcome`

- Business goal: Find job executions with a selected exit code.
- Starting point: At least one execution with the selected `jobExecution.exitCode` exists.
- Expected business result: Return execution records whose stored exit code matches the query.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | Obtain launchable `job.name` | Yes | Yes | Yes | `test_6` confirms `personJob` | `JobController.all` covered |
| 2 | `start job synchronously` | Create execution and capture returned `jobExecution.exitCode` | Yes | Yes | Yes | `test_12`, `test_21`, and `test_23` assert `COMPLETED` | Launch path covered |
| 3 | `find job executions by exit code` | `GET /jobExecutions?exitCode={jobExecution.exitCode from step 2}` | Yes | Yes | No | `test_10` and `test_16` use arbitrary exit codes; no test reuses returned `COMPLETED` as the filter with result assertions | Cached/all providers covered, but not context-valid outcome query |

- Happy-path item: `Not Covered`. No test binds the launch response `exitCode` to a subsequent outcome filter.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get job execution by id` | Confirm selected execution's exit code | No | `test_21` confirms id lookup, but not from an exit-code filter scenario |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `find job executions by exit code` | `NullPointerException` from `copyOf(jobExecutionsByExitCode.get(exitCode))` | Cached path scans a bucket without the requested exit-code queue | Yes | High | `test_0` calls `GET /jobExecutions?jobName=&exitCode=OYB&limitPerJob=0` and asserts `500`, `NullPointerException` | Read failure only; no mutation expected | `CachedJobExecutionProvider.JobExecutions.getJobExecutions` covered `3/3` lines, `2/2` branches |
| `find job executions by exit code` | `IllegalArgumentException` from `Stream.limit(limitPerJob)` in cached-provider path | Negative limit with requested exit-code queue present | No | High | No test uses existing `exitCode=COMPLETED` with negative `limitPerJob` | Not observed | Negative-limit branch covered elsewhere, not this exact condition |

- Required-step summary: attempted `3/3`, application reached `3/3`, context-valid success `2/3`
- Happy-path summary: `0/1`
- Failure summary: `1/2`
- Behavior outcome checklist summary: `1/3`
- Status and confidence: `Partially Covered`, Medium
- Exact gap: Outcome filter success and negative-limit-with-existing-exit-code failure remain unproven.
- Recommended test IDs that close the gap: `T1`, `T5`.

### `B9`: `Review one job's executions by outcome`

- Business goal: Find executions for a specific job that also match a specific exit code.
- Starting point: A persisted execution exists with both the selected job name and selected exit code.
- Expected business result: Return only records satisfying both filters.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | Obtain returned `job.name` | Yes | Yes | Yes | `test_6` confirms `personJob` | `JobController.all` covered |
| 2 | `start job synchronously` | Create execution and capture returned `exitCode` | Yes | Yes | Yes | `test_12`, `test_21`, and `test_23` assert `COMPLETED` | Launch path covered |
| 3 | `find job executions by job name and exit code` | `GET /jobExecutions?jobName={body name}&exitCode={returned exitCode}` | Yes | Yes | No | `test_12` and `test_15` query `jobName=personJob` but use random/non-matching exit codes and do not assert matching result rows | Combined provider path covered, but not context-valid success |

- Happy-path item: `Not Covered`. No test reuses both `jobName=personJob` and returned `exitCode=COMPLETED` in the combined filter.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get job execution by id` | Inspect one matching execution | No | Id lookup exists outside a combined-filter scenario |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `find job executions by job name and exit code` | `PatternSyntaxException` from `Pattern.compile(jobNameRegexp)` in cached-provider path | Invalid regex jobName, cached combined filter path | No | No invalid-regex combined-filter test | Not observed | Provider regex path covered generally |
| `find job executions by job name and exit code` | `PatternSyntaxException` from `Pattern.compile(jobNameRegexp)` in all-provider fallback path | Invalid regex jobName, large-limit fallback | No | No invalid-regex large-limit combined test | Not observed | All-provider regex path covered generally |
| `find job executions by job name and exit code` | `NullPointerException` from `copyOf(jobExecutionsByExitCode.get(exitCode))` | Matched job bucket lacks requested exit-code queue | Yes | High | `test_1` posts async `personJob`, then `GET /jobExecutions?jobName=personJob&exitCode=7peOmys%3Cy` asserts `500`, `NullPointerException` | Prior POST establishes same job bucket; read failure only | `CachedJobExecutionProvider.JobExecutions.getJobExecutions` covered |
| `find job executions by job name and exit code` | `IllegalArgumentException` from `Stream.limit(limitPerJob)` in cached-provider path | Negative limit with matched job and existing requested exit-code bucket | No | High | No combined query uses `jobName=personJob`, existing `exitCode=COMPLETED`, and negative limit | Not observed | Negative-limit branch covered elsewhere, not this exact condition |

- Required-step summary: attempted `3/3`, application reached `3/3`, context-valid success `2/3`
- Happy-path summary: `0/1`
- Failure summary: `1/4`
- Behavior outcome checklist summary: `1/5`
- Status and confidence: `Partially Covered`, Medium
- Exact gap: Combined filter success and most combined filter failures remain uncovered.
- Recommended test IDs that close the gap: `T1`, `T4`, `T5`.

## Cross-Behavior Gaps

- Generated tests call every documented route, but filter workflows rarely preserve response-to-request value bindings such as returned `job.name`, `Location -> id`, and returned `exitCode`.
- `controller.resetStateOfSUT()` runs before every test. State-dependent workflows are covered only when all calls occur inside one method, such as `test_21`; separate tests are not stitched together for happy-path credit.
- Several shared `GET /jobExecutions` variants assert only status and content type. Those calls prove invocation/application reach, not business filter correctness.
- Status-only random 4xx/5xx probes do not prove documented business failures unless the exact source discriminator is asserted or uniquely implied by setup, response, and JaCoCo branch evidence.
- The named job lookup endpoint returns `200` for an arbitrary path name in `test_20`, which disagrees with the documented expectation that the name should identify a registered job.
- JaCoCo branch coverage is low at `46/307 (15.0%)`; many Spring Batch lifecycle branches, controller error branches, and provider edge cases remain untested.
- Async behavior is only checked at immediate response time. No generated test polls a returned async id or proves per-execution property isolation.
- Optional verification workflows are mostly absent. Only the synchronous launch-to-id lookup binding in `test_21` is fully executed as documented.

## Suggested Additional Tests

### Test `T1`: `complete registered-job lookup and execution filter workflow`

- Priority: `P0`
- Target behavior ID and name: `B2 Inspect a named batch job`, `B7 Review executions for one job`, `B8 Review executions by outcome`, `B9 Review one job's executions by outcome`, with optional support for `B6`
- Target checklist item:
  - happy path and required step
  - exact function names: `get registered job by name`, `find job executions by job name`, `find job executions by exit code`, `find job executions by job name and exit code`
  - exact source discriminator and condition for a failure: N/A
- Test category: success
- Why needed: It closes the largest success-path gap by preserving `job.name`, generated execution id, and returned `exitCode` inside one stateful method.
- Coverage delta if passing: increases B2 happy path, B2 step 2 context-valid success, B7 step 3 context-valid success, B7 happy path, B8 step 3 context-valid success, B8 happy path, B9 step 3 context-valid success, B9 happy path, optional verification for B6/B7/B8/B9 when the generated id is followed and asserted.

#### Initial state and fixture plan

State:
- database/SUT reset occurs before the test through `controller.resetStateOfSUT()`
- registered job: `personJob` from application wiring
- lifecycle state: no required prior execution; the test creates a synchronous execution
- actor identities, roles, token issuers, ownership: none documented by OpenAPI or behavior spec
- fixed clock/date assumptions: none; assert timestamp presence or schema only if stable
- feature/config values: `com.github.chrisgleissner.springbatchrest.addUniqueJobParameter=true`
- external-domain stub results: none
- transaction and asynchronous waiting strategy: synchronous launch only, no async wait required

No direct database setup is required. The test uses API-realizable setup through `POST /jobExecutions`.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B2 step 1, `list registered Spring Batch jobs` | Anonymous ordinary HTTP context | `GET /jobs` | `Accept: application/hal+json`; no cookies | none | none | Capture `_embedded.jobResourceList[0].job.name = personJob` | `200`; body contains one job resource with `job.name="personJob"` | Registry unchanged |
| 2 | B2 step 2, `get registered job by name` | Same context | `GET /jobs/personJob` | `Accept: application/hal+json`; no cookies | path `jobName=personJob` | none | `personJob` from call 1 | `200`; body `job.name="personJob"` | Registry unchanged |
| 3 | B7/B8/B9 setup, `start job synchronously` | Same context | `POST /jobExecutions` | `Content-Type: application/json`; `Accept: application/hal+json`; no cookies | none | `{"name":"personJob","asynchronous":false,"properties":{}}` | `name` from call 1 | `200`; header `Location=/jobExecutions/{generatedId}`; body `jobExecution.jobName="personJob"`, `exitCode="COMPLETED"`, `status="COMPLETED"`, `exceptions=[]` | New execution exists for `personJob` with returned id and `COMPLETED` exit code |
| 4 | B7 step 3, `find job executions by job name` | Same context | `GET /jobExecutions?jobName=personJob&limitPerJob=3` | `Accept: application/hal+json`; no cookies | query `jobName=personJob`, `limitPerJob=3` | none | `personJob` from call 3 body | `200`; every returned item has `jobExecution.jobName="personJob"`; one item has the generated id from call 3 | Repository unchanged |
| 5 | B8 step 3, `find job executions by exit code` | Same context | `GET /jobExecutions?exitCode=COMPLETED&limitPerJob=3` | `Accept: application/hal+json`; no cookies | query `exitCode=COMPLETED`, `limitPerJob=3` | none | `COMPLETED` from call 3 body | `200`; every returned item has `jobExecution.exitCode="COMPLETED"`; one item has the generated id from call 3 | Repository unchanged |
| 6 | B9 step 3, `find job executions by job name and exit code` | Same context | `GET /jobExecutions?jobName=personJob&exitCode=COMPLETED&limitPerJob=3` | `Accept: application/hal+json`; no cookies | query `jobName=personJob`, `exitCode=COMPLETED`, `limitPerJob=3` | none | `personJob` and `COMPLETED` from call 3 | `200`; every returned item has both `jobName="personJob"` and `exitCode="COMPLETED"`; one item has generated id from call 3 | Repository unchanged |
| 7 | Optional verification, `get job execution by id` | Same context | `GET /jobExecutions/{generatedId}`; concrete example `/jobExecutions/0` when Location supplies `0` | `Accept: application/hal+json`; no cookies | path `id={generatedId}` | none | `Location -> generatedId` from call 3 | `200`; body matches call 3 `jobName`, `status`, `exitCode`, and id | Repository unchanged |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `Accept` | header | `application/hal+json` | media type | Yes for documented representation | HAL JSON compatible | none | Matches documented workflows |
| 2 | `jobName` | path | `personJob` | string, path-safe | Yes | registered job name | Must equal `job.name` from call 1 | Proves named lookup binding |
| 3 | `name` | JSON body | `personJob` | string | Yes | registered job name | Must equal call 1 `job.name` | Selects launchable job |
| 3 | `asynchronous` | JSON body | `false` | boolean | Yes for explicit sync branch | `false` for sync | Selects synchronous launcher | Produces terminal response |
| 3 | `properties` | JSON body | `{}` | object | No | object or omitted | Empty map plus generated `uuid` still valid | Minimal valid launch |
| 4 | `jobName` | query | `personJob` | string/regex-safe | Yes for B7 | regex-safe value | Must equal call 3 body name | Proves job-name filter |
| 4-6 | `limitPerJob` | query | `3` | integer | No | non-negative | Must not trigger negative-limit failure | Stable bounded result |
| 5 | `exitCode` | query | `COMPLETED` | string | Yes for B8 | stored exit code | Must equal call 3 returned exit code | Proves outcome filter |
| 6 | `jobName` + `exitCode` | query | `personJob`, `COMPLETED` | string, string | Yes for B9 | both must match same execution | Both values must come from call 3 | Proves combined filter |
| 7 | `id` | path | dynamic generated id; example `0` | int64 | Yes | existing execution id | Must come from call 3 `Location` | Proves id binding |

#### Assertions

- HTTP status: `200` for all calls.
- Response headers: call 3 has non-empty `Location` ending in `/jobExecutions/{generatedId}`.
- Response body fields: `job.name`, `jobExecution.jobName`, `jobExecution.exitCode`, `jobExecution.status`, `jobExecution.exceptions`.
- Error code/source discriminator: none.
- Persisted entity state: one execution for `personJob` with generated id and `COMPLETED` exit code is returned by id and by all filters.
- Derived status and business invariants: all B7 results match `jobName`, all B8 results match `exitCode`, all B9 results match both values.
- Events, notifications, journal, DVH/message, recalculation, integration side effects: none documented.
- Explicit absence of forbidden mutations: GET calls do not change execution fields.
- Final read/verification call: call 7.
- Expected source method/line/branch corroboration: `JobController.all`, `JobController.get`, `JobExecutionController.put`, `JobExecutionController.all`, `JobExecutionController.get`, `CachedJobExecutionProvider.getJobExecutions`.

#### Isolation and variants

- Cleanup/reset requirements: rely on generated test `@Before controller.resetStateOfSUT()`.
- Fixed clock and deterministic-id handling: do not assert exact timestamps or exact generated id; assert binding through `Location`.
- External stub reset: none.
- Transaction handling: synchronous POST must complete before filter calls.
- Async polling/timeout strategy: none.
- Nearby boundary variants requiring separate tests: empty history, `limitPerJob=0`, and `limitPerJob` larger than cache size.

### Test `T2`: `asynchronous launch with null name fails before submission`

- Priority: `P1`
- Target behavior ID and name: `B4 Submit an asynchronous batch execution`
- Target checklist item:
  - concrete failure
  - exact function name: `start job asynchronously`
  - exact source discriminator and condition: `RuntimeException` wrapping `JobLocator.getJob(jobConfig.getName())` null-name branch; parsed `JobConfig.name=null`
- Test category: business failure
- Why needed: Existing async failure coverage uses `name=""`, which covers unknown non-null name, not null name.
- Coverage delta if passing: increases B4 documented business-failure coverage by 1 and B4 behavior outcome checklist by 1.

#### Initial state and fixture plan

State:
- database/SUT reset occurs before the test
- registered job: `personJob` may exist but is intentionally not referenced
- lifecycle and approval state: no execution required
- actor identities, roles, token issuers, ownership: none documented
- fixed clock/date assumptions: none
- feature/config values: default `addUniqueJobParameter=true`
- external-domain stub results: none
- transaction and asynchronous waiting strategy: no async execution should be accepted

For this failure test, every unrelated parameter is valid: JSON is parseable, `asynchronous=true`, and `properties={}`. Only `name` is omitted.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B4 failure, `start job asynchronously` | Anonymous ordinary HTTP context | `POST /jobExecutions` | `Content-Type: application/json`; `Accept: application/hal+json`; no cookies | none | `{"asynchronous":true,"properties":{}}` | `name` intentionally absent, parsed as `null` | `500`; body `status` contains `500 INTERNAL_SERVER_ERROR`; `exception` contains `RuntimeException`; `message` contains `Failed to start job 'null'` | No execution is created for a null job |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `Content-Type` | header | `application/json` | media type | Yes | JSON | Allows controller binding | Keeps failure after binding |
| 1 | `Accept` | header | `application/hal+json` | media type | Yes | HAL JSON compatible | none | Matches generated tests |
| 1 | `name` | JSON body | absent | null after binding | Yes for valid launch | registered job name | Target-invalid value | Isolates null-name branch |
| 1 | `asynchronous` | JSON body | `true` | boolean | Yes for async failure | `true` | Selects async documented function after binding | Distinguishes B4 from B3 |
| 1 | `properties` | JSON body | `{}` | object | No | object | Valid unrelated parameter | Avoids parameter conversion failure |

#### Assertions

- HTTP status: `500`.
- Response headers: `Content-Type` compatible with `application/hal+json`.
- Response body fields: `status` contains `500 INTERNAL_SERVER_ERROR`, `exception` contains `RuntimeException`, `message` contains `Failed to start job 'null'`.
- Error code/source discriminator: null-name `RuntimeException` from `AdHocStarter.start(JobConfig)`.
- Persisted entity and child-row state: no new execution for null job; optional follow-up `GET /jobExecutions?jobName=null` must not show a new execution.
- Derived status and business invariants: launch request without a job identity cannot create an execution handle.
- Side effects: no job submission to async launcher.
- Explicit absence of forbidden mutations: repository execution count for registered jobs remains unchanged except fixture state.
- Final read/verification call when needed: optional `GET /jobExecutions?jobName=null&limitPerJob=3` expecting no matching rows.
- Expected source method/line/branch corroboration: `AdHocStarter.start(JobConfig)` reaches `jobLocator.getJob(null)` and exception wrapping path.

#### Isolation and variants

- Cleanup/reset requirements: generated reset before test.
- Fixed clock and deterministic-id handling: none.
- External stub reset: none.
- Transaction handling: request fails before launch.
- Async polling/timeout strategy: none because no async work should start.
- Nearby boundary variants requiring separate tests: explicit JSON `"name":null`, empty string `""`, unknown non-empty name.

### Test `T3`: `synchronous launch parameter validation failure`

- Priority: `P1`
- Target behavior ID and name: `B3 Start a synchronous batch execution`
- Target checklist item:
  - concrete failure
  - exact function name: `start job synchronously`
  - exact source discriminator and condition: `JobParametersInvalidException`; registered job's parameter validator rejects converted request properties
- Test category: business failure
- Why needed: Generated tests exercise unknown/missing job names but not Spring Batch parameter validation after a registered job is resolved.
- Coverage delta if passing: increases B3 documented business-failure coverage by 1 and unique source business-branch coverage by 1.

#### Initial state and fixture plan

State:
- database/SUT reset occurs before the test
- registered job: `validatedJob` registered in the test SUT with a Spring Batch validator requiring property `runDate` to match `yyyy-MM-dd`
- lifecycle and approval state: no prior execution required
- actor identities, roles, token issuers, ownership: none documented
- fixed clock/date assumptions: none
- feature/config values: `addUniqueJobParameter=true`
- external-domain stub results: none
- transaction and asynchronous waiting strategy: synchronous launch fails before successful execution

Direct application fixture setup is necessary because the public REST API has no job-registration endpoint. The fixture registers one job bean named `validatedJob` with a validator; this setup is only a prerequisite and does not replace the `POST /jobExecutions` behavior under test.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B3 failure setup, `list registered Spring Batch jobs` | Anonymous ordinary HTTP context | `GET /jobs` | `Accept: application/hal+json`; no cookies | none | none | Fixture registration | `200`; body contains `job.name="validatedJob"` | Registry unchanged |
| 2 | B3 failure, `start job synchronously` | Same context | `POST /jobExecutions` | `Content-Type: application/json`; `Accept: application/hal+json`; no cookies | none | `{"name":"validatedJob","asynchronous":false,"properties":{"runDate":"not-a-date"}}` | `validatedJob` from call 1; invalid `runDate` intentional | Error response mapped from Spring Batch launch failure; body exception chain includes `JobParametersInvalidException` | No successful execution is created for invalid parameters |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `validatedJob` | registry | `validatedJob` | job name string | Yes | registered job | Must be resolvable by `JobLocator` | Ensures failure occurs after job resolution |
| 2 | `name` | JSON body | `validatedJob` | string | Yes | registered job name | Must equal call 1 job name | Avoids unknown-job branch |
| 2 | `asynchronous` | JSON body | `false` | boolean | Yes | `false` | Selects synchronous launcher | Targets B3 |
| 2 | `properties.runDate` | JSON body | `not-a-date` | string | Yes for fixture validator | Expected `yyyy-MM-dd`; supplied value violates format | Only target-invalid constraint | Triggers `JobParametersInvalidException` |
| 2 | `properties` | JSON body | `{"runDate":"not-a-date"}` | object | Yes for target | object | Converted by `JobParamUtil` to string `JobParameter` | Keeps conversion valid, validator invalid |

#### Assertions

- HTTP status: the mapped error status produced by the application for `BatchRuntimeException`; assert the exact observed status after fixture implementation, expected non-2xx.
- Response headers: `Content-Type` compatible with application error representation.
- Response body fields: message contains `Failed to start job 'validatedJob'`; exception or cause contains `JobParametersInvalidException`.
- Error code/source discriminator: `JobParametersInvalidException`.
- Persisted entity and child-row state: no successful `validatedJob` execution with `runDate=not-a-date` appears in `GET /jobExecutions?jobName=validatedJob`.
- Derived status and invariants: registered job resolution succeeds; validation alone blocks launch.
- Side effects: no completed execution; no writer output from the job.
- Explicit absence of forbidden mutations: no execution marked `COMPLETED`.
- Final read/verification call: optional `GET /jobExecutions?jobName=validatedJob&limitPerJob=3`.
- Expected source method/line/branch corroboration: `AdHocStarter.start(JobConfig)` after `JobParamUtil.convertRawToParamMap(...)`, then `JobLauncher.run(...)` throws `JobParametersInvalidException`.

#### Isolation and variants

- Cleanup/reset requirements: unregister or isolate `validatedJob` after test class if fixture is not reset by SUT controller.
- Fixed clock and deterministic-id handling: none.
- External stub reset: none.
- Transaction handling: launch transaction must roll back or persist only Spring Batch failed metadata according to actual Spring Batch behavior; assert the documented service outcome.
- Async polling/timeout strategy: none.
- Nearby boundary variants requiring separate tests: missing required `runDate`, valid boundary date `2026-07-07`, empty string.

### Test `T4`: `regex-unsafe jobName filter failures`

- Priority: `P1`
- Target behavior ID and name: `B7 Review executions for one job`, `B9 Review one job's executions by outcome`
- Target checklist item:
  - concrete failure
  - exact function names: `find job executions by job name`, `find job executions by job name and exit code`
  - exact source discriminator and condition: `PatternSyntaxException` from `Pattern.compile(jobNameRegexp)` in cached-provider path; invalid Java regex jobName
- Test category: business failure
- Why needed: The behavior spec documents regex-sensitive job-name failures, but generated tests never use invalid regex syntax.
- Coverage delta if passing: increases B7 failure coverage by 1, B9 failure coverage by 1, and unique source business-branch coverage by 2.

#### Initial state and fixture plan

State:
- database/SUT reset occurs before the test
- registered job: `personJob`
- lifecycle state: create one cached execution for `personJob` through a synchronous launch
- actor identities, roles, token issuers, ownership: none documented
- fixed clock/date assumptions: none
- feature/config values: cached provider enabled with cache size greater than or equal to `3`
- external-domain stub results: none
- transaction and asynchronous waiting strategy: synchronous setup completes before failure calls

Direct database setup is unnecessary. The setup launch establishes a cached execution bucket before the invalid regex filters are sent.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | Setup, `start job synchronously` | Anonymous ordinary HTTP context | `POST /jobExecutions` | `Content-Type: application/json`; `Accept: application/hal+json`; no cookies | none | `{"name":"personJob","asynchronous":false,"properties":{}}` | known registered sample job | `200`; body `jobExecution.jobName="personJob"`, `exitCode="COMPLETED"` | Cached execution bucket for `personJob` exists |
| 2 | B7 failure, `find job executions by job name` | Same context | `GET /jobExecutions?jobName=%5B&limitPerJob=3` | `Accept: application/hal+json`; no cookies | query `jobName=[`, `limitPerJob=3` | none | Invalid regex `[` is intentional | Non-2xx error; body exception contains `PatternSyntaxException` | Repository unchanged |
| 3 | B9 failure, `find job executions by job name and exit code` | Same context | `GET /jobExecutions?jobName=%5B&exitCode=COMPLETED&limitPerJob=3` | `Accept: application/hal+json`; no cookies | query `jobName=[`, `exitCode=COMPLETED`, `limitPerJob=3` | none | Invalid regex `[` plus existing exit code from call 1 | Non-2xx error; body exception contains `PatternSyntaxException` | Repository unchanged |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `name` | JSON body | `personJob` | string | Yes | registered job | none | Creates cached bucket |
| 1 | `asynchronous` | JSON body | `false` | boolean | Yes | `false` | synchronous setup | Stable completed exit code |
| 2 | `jobName` | query | `[` encoded as `%5B` | string, invalid Java regex | Yes | any string accepted by OpenAPI, but invalid for implementation regex | Target-invalid value | Triggers cached regex compile failure |
| 2 | `limitPerJob` | query | `3` | integer | No | non-negative and within cached path | Avoids all-provider fallback | Targets cached-provider path |
| 3 | `exitCode` | query | `COMPLETED` | string | Yes for B9 | existing stored exit code | Must match setup execution | Isolates jobName regex failure |

#### Assertions

- HTTP status: exact observed non-2xx status for `PatternSyntaxException`; current generated faults suggest `500` for provider failures.
- Response headers: error representation content type.
- Response body fields: exception contains `PatternSyntaxException`; detail or message references invalid regex syntax.
- Error code/source discriminator: `Pattern.compile(jobNameRegexp)` cached-provider path.
- Persisted entity state: setup execution remains readable by id or by valid `jobName=personJob`.
- Side effects: GET failures do not create, modify, or delete executions.
- Explicit absence of forbidden mutations: no new execution count change after calls 2 and 3.
- Final read/verification call when needed: optional `GET /jobExecutions?jobName=personJob&exitCode=COMPLETED&limitPerJob=3` returns the setup execution.
- Expected source method/line/branch corroboration: `CachedJobExecutionProvider.getJobExecutions(...)`, lambda compiling job-name pattern.

#### Isolation and variants

- Cleanup/reset requirements: generated reset before test.
- Fixed clock and deterministic-id handling: do not assert exact id.
- External stub reset: none.
- Transaction handling: setup POST completes before GET failures.
- Async polling/timeout strategy: none.
- Nearby boundary variants requiring separate tests: invalid regex with `limitPerJob` greater than cache size to cover all-provider fallback.

### Test `T5`: `negative limit with existing exit-code bucket`

- Priority: `P1`
- Target behavior ID and name: `B8 Review executions by outcome`, `B9 Review one job's executions by outcome`, with B7 negative-limit variant if jobName-only call is included
- Target checklist item:
  - concrete failure
  - exact function names: `find job executions by exit code`, `find job executions by job name and exit code`, optionally `find job executions by job name`
  - exact source discriminator and condition: `IllegalArgumentException` from `Stream.limit(limitPerJob)` in cached-provider path; negative `limitPerJob` after matching cached bucket/queue exists
- Test category: boundary
- Why needed: Existing negative-limit evidence covers global history only. It does not prove negative limit after a present job-name or exit-code filter.
- Coverage delta if passing: increases B8 failure coverage by 1, B9 failure coverage by 1, optionally B7 failure coverage by 1, and unique source business-branch coverage by 2 or 3.

#### Initial state and fixture plan

State:
- database/SUT reset occurs before the test
- registered job: `personJob`
- lifecycle state: one synchronous completed execution exists before filter failures
- actor identities, roles, token issuers, ownership: none documented
- fixed clock/date assumptions: none
- feature/config values: cached provider path selected for small absolute limit value before `Stream.limit` rejects negative input
- external-domain stub results: none
- transaction and asynchronous waiting strategy: synchronous setup completes before failure queries

Direct database setup is unnecessary.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | Setup, `start job synchronously` | Anonymous ordinary HTTP context | `POST /jobExecutions` | `Content-Type: application/json`; `Accept: application/hal+json`; no cookies | none | `{"name":"personJob","asynchronous":false,"properties":{}}` | known registered sample job | `200`; body `jobExecution.jobName="personJob"`, `exitCode="COMPLETED"` | Cached job and exit-code queues exist |
| 2 | B8 failure, `find job executions by exit code` | Same context | `GET /jobExecutions?exitCode=COMPLETED&limitPerJob=-1` | `Accept: application/hal+json`; no cookies | query `exitCode=COMPLETED`, `limitPerJob=-1` | none | `COMPLETED` from call 1 | `500`; body exception contains `IllegalArgumentException`; message contains `-1` | Repository unchanged |
| 3 | B9 failure, `find job executions by job name and exit code` | Same context | `GET /jobExecutions?jobName=personJob&exitCode=COMPLETED&limitPerJob=-1` | `Accept: application/hal+json`; no cookies | query `jobName=personJob`, `exitCode=COMPLETED`, `limitPerJob=-1` | none | values from call 1 | `500`; body exception contains `IllegalArgumentException`; message contains `-1` | Repository unchanged |
| 4 | Optional B7 failure, `find job executions by job name` | Same context | `GET /jobExecutions?jobName=personJob&limitPerJob=-1` | `Accept: application/hal+json`; no cookies | query `jobName=personJob`, `limitPerJob=-1` | none | `personJob` from call 1 | `500`; body exception contains `IllegalArgumentException`; message contains `-1` | Repository unchanged |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `name` | body | `personJob` | string | Yes | registered job name | none | Creates matching buckets |
| 1 | `asynchronous` | body | `false` | boolean | Yes | `false` | sync setup | Produces `COMPLETED` |
| 2 | `exitCode` | query | `COMPLETED` | string | Yes | existing exit-code bucket | Must match call 1 | Avoids NPE missing-bucket branch |
| 2-4 | `limitPerJob` | query | `-1` | integer | Yes for target | invalid negative | Only target-invalid value | Triggers `Stream.limit` failure |
| 3-4 | `jobName` | query | `personJob` | regex-safe string | Yes for B7/B9 | registered/stored job name | Must match call 1 | Avoids mismatch and regex failures |

#### Assertions

- HTTP status: `500` for calls 2-4 if current exception mapping is retained.
- Response headers: HAL/error content type.
- Response body fields: `exception` contains `IllegalArgumentException`; `message` contains `-1`.
- Error code/source discriminator: `Stream.limit(limitPerJob)` in cached-provider path.
- Persisted state: setup execution remains present and unchanged.
- Derived invariants: negative limit alone causes failure after matching bucket/queue selection.
- Side effects: no write side effects from GET failures.
- Explicit absence of forbidden mutations: execution count and fields unchanged after failures.
- Final read/verification call when needed: optional valid combined filter from T1's call 6.
- Expected source method/line/branch corroboration: `CachedJobExecutionProvider.getJobExecutions(...)`, lambda applying `limit(limitPerJob)`.

#### Isolation and variants

- Cleanup/reset requirements: generated reset before test.
- Fixed clock and deterministic-id handling: none.
- External stub reset: none.
- Transaction handling: setup launch committed before failure queries.
- Async polling/timeout strategy: none.
- Nearby boundary variants requiring separate tests: `limitPerJob=0`, `limitPerJob=1`, large positive limit that delegates to all-provider fallback.

### Test `T6`: `synchronous failed exit code maps to error and keeps failed execution`

- Priority: `P1`
- Target behavior ID and name: `B3 Start a synchronous batch execution`
- Target checklist item:
  - concrete failure
  - exact function name: `start job synchronously`
  - exact source discriminator and condition: `JobExecutionController.put` failed-exit-code branch; synchronous launch returns `jobExecution.exitCode=FAILED`
- Test category: state transition
- Why needed: The controller's failed-exit-code branch has no generated proof even though the behavior document states the execution is persisted despite the error response.
- Coverage delta if passing: increases B3 failure coverage by 1, unique source business-branch coverage by 1, and behavior outcome checklist by 1.

#### Initial state and fixture plan

State:
- database/SUT reset occurs before the test
- registered job: `failingJob`, a test fixture job whose tasklet throws `IllegalStateException("planned failure")`
- lifecycle state: no prior execution for the fixture job
- actor identities, roles, token issuers, ownership: none documented
- fixed clock/date assumptions: none
- feature/config values: `addUniqueJobParameter=true`
- external-domain stub results: none
- transaction and asynchronous waiting strategy: synchronous launcher waits until the failed execution is returned

Direct application fixture setup is necessary because there is no REST job-registration endpoint. The fixture registers `failingJob`; the API call under test still performs the documented launch behavior.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | Setup check, `list registered Spring Batch jobs` | Anonymous ordinary HTTP context | `GET /jobs` | `Accept: application/hal+json`; no cookies | none | none | Fixture registration | `200`; body contains `job.name="failingJob"` | Registry unchanged |
| 2 | B3 failure, `start job synchronously` | Same context | `POST /jobExecutions` | `Content-Type: application/json`; `Accept: application/hal+json`; no cookies | none | `{"name":"failingJob","asynchronous":false,"properties":{}}` | `failingJob` from call 1 | Non-2xx error from `JobExecutionController.put`; body/error identifies failed execution outcome | A Spring Batch execution exists with `jobName=failingJob`, `exitCode=FAILED`, `status=FAILED`, exception `planned failure` |
| 3 | Persisted outcome check, `find job executions by job name` | Same context | `GET /jobExecutions?jobName=failingJob&limitPerJob=3` | `Accept: application/hal+json`; no cookies | query `jobName=failingJob`, `limitPerJob=3` | none | `failingJob` from call 2 body | `200`; at least one returned execution has `exitCode=FAILED`, `status=FAILED`, and exception detail | Failed execution remains persisted |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | fixture job | registry | `failingJob` | string | Yes | registered job | Must be resolvable | Avoids unknown-job failure |
| 2 | `name` | JSON body | `failingJob` | string | Yes | registered job name | Must equal fixture | Targets failed execution branch |
| 2 | `asynchronous` | JSON body | `false` | boolean | Yes | `false` | Synchronous branch required | Controller checks returned exit code |
| 2 | `properties` | JSON body | `{}` | object | No | valid object | No parameter failure | Isolates job execution failure |
| 3 | `jobName` | query | `failingJob` | regex-safe string | Yes | stored job name | Must match call 2 body | Verifies persisted failed outcome |

#### Assertions

- HTTP status: exact non-2xx status mapped by `JobExecutionController.put` for failed exit code.
- Response headers: error content type.
- Response body fields: error details identify failed launch outcome; if body includes `jobExecution`, assert `exitCode=FAILED`.
- Error code/source discriminator: `JobExecutionController.put` failed-exit-code branch.
- Persisted entity state: failed execution exists in history with `jobName=failingJob`, `status=FAILED`, `exitCode=FAILED`, and planned exception.
- Derived status and business invariants: a failed job result is not returned as normal `200` success.
- Events/side effects: fixture writer side effects should not report success.
- Explicit absence of forbidden mutations: no `COMPLETED` execution for `failingJob`.
- Final read/verification call: call 3.
- Expected source method/line/branch corroboration: `JobExecutionController.put` covered branch for failed exit code, `JobExecutionService.launch`, `AdHocStarter.start`.

#### Isolation and variants

- Cleanup/reset requirements: unregister fixture job or isolate Spring context per test class.
- Fixed clock and deterministic-id handling: do not assert exact id/timestamps.
- External stub reset: none.
- Transaction handling: Spring Batch may persist failed execution metadata; assert persisted outcome as behavior document requires.
- Async polling/timeout strategy: none.
- Nearby boundary variants requiring separate tests: async failing job, tasklet throwing before step starts, tasklet returning `ExitStatus.FAILED` without exception.

### Test `T7`: `concurrent asynchronous executions keep property values isolated`

- Priority: `P2`
- Target behavior ID and name: `B4 Submit an asynchronous batch execution`
- Target checklist item:
  - concrete failure/regression
  - exact function name: `start job asynchronously`
  - exact source discriminator and condition: `JobPropertyResolvers.started(JobConfig)` job-name-keyed resolver overwrite; concurrent same-job executions use different `properties`
- Test category: concurrency
- Why needed: The behavior document calls out a source-level concurrency defect that generated tests do not attempt.
- Coverage delta if passing or exposing defect: increases B4 concrete failure coverage by 1 if the defect is reproduced and asserted, or documents a fixed branch if implementation changes.

#### Initial state and fixture plan

State:
- database/SUT reset occurs before the test
- registered job: `propertyEchoJob`, a fixture job that waits on a controllable latch, reads `JobPropertyResolvers.JobProperties.of("propertyEchoJob").getProperty("marker")`, and persists or exposes the read marker per execution
- lifecycle state: no prior executions required
- actor identities, roles, token issuers, ownership: none documented
- fixed clock/date assumptions: none
- feature/config values: `addUniqueJobParameter=true`
- external-domain stub results: none
- transaction and asynchronous waiting strategy: use two latches so execution A starts and waits, execution B starts with different marker, then both complete; poll by generated ids until terminal state

Direct application fixture setup is necessary because the public API cannot register a latch-controlled job. This setup creates the condition; the two `POST /jobExecutions` calls are the behavior under test.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | First B4 submission, `start job asynchronously` | Anonymous ordinary HTTP context | `POST /jobExecutions` | `Content-Type: application/json`; `Accept: application/hal+json`; no cookies | none | `{"name":"propertyEchoJob","asynchronous":true,"properties":{"marker":"A"}}` | fixture job | `200`; capture `Location=/jobExecutions/{idA}`; immediate body `jobName=propertyEchoJob` | Execution A is running or starting and waiting on latch |
| 2 | Second B4 submission, `start job asynchronously` | Same context | `POST /jobExecutions` | `Content-Type: application/json`; `Accept: application/hal+json`; no cookies | none | `{"name":"propertyEchoJob","asynchronous":true,"properties":{"marker":"B"}}` | same fixture job, different properties | `200`; capture `Location=/jobExecutions/{idB}` | Execution B is running or starting; resolver may overwrite job-name key |
| 3 | Release fixture latches | Test harness, not API | fixture operation `releaseBoth()` | none | none | none | ids from calls 1 and 2 | both executions allowed to finish | Echoed markers are persisted or exposed by fixture |
| 4 | Verify execution A, `get job execution by id` | Same context | `GET /jobExecutions/{idA}`; concrete example `/jobExecutions/10` | `Accept: application/hal+json`; no cookies | path `id={idA}` | none | `Location -> idA` from call 1 | terminal `200`; execution A details available | Execution A terminal |
| 5 | Verify execution B, `get job execution by id` | Same context | `GET /jobExecutions/{idB}`; concrete example `/jobExecutions/11` | `Accept: application/hal+json`; no cookies | path `id={idB}` | none | `Location -> idB` from call 2 | terminal `200`; execution B details available | Execution B terminal |
| 6 | Fixture marker read | Test harness or fixture endpoint if exposed | fixture operation `readMarkers()` | none | none | none | ids from calls 1 and 2 | expected isolated values `idA -> A`, `idB -> B`; documented defect reproduces as `idA -> B` | Property isolation result known |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1-2 | `name` | JSON body | `propertyEchoJob` | string | Yes | registered job | Same job name for both calls | Required to exercise job-name-keyed resolver overwrite |
| 1 | `properties.marker` | JSON body | `A` | string | Yes for fixture | any non-empty string | Must differ from call 2 | First execution marker |
| 2 | `properties.marker` | JSON body | `B` | string | Yes for fixture | any non-empty string | Must differ from call 1 | Second execution marker |
| 1-2 | `asynchronous` | JSON body | `true` | boolean | Yes | `true` | Both executions overlap | Targets async concurrency |
| 3 | latches | fixture state | both executions blocked before marker read | synchronization primitive | Yes | blocked/running | Ensures overlap | Makes overwrite deterministic |

#### Assertions

- HTTP status: `200` for both async submissions and id reads after completion.
- Response headers: both POST calls return distinct `Location` headers.
- Response body fields: both submissions have `jobName=propertyEchoJob`; terminal reads show expected statuses.
- Error code/source discriminator: if defect is reproduced, marker mismatch is attributed to `JobPropertyResolvers.started(JobConfig)` job-name-keyed resolver overwrite.
- Persisted entity state: two distinct executions exist.
- Derived status/invariants: execution A should read marker `A` and execution B should read marker `B`; a mismatch proves documented failure.
- Side effects: fixture marker store records per-execution marker values.
- Explicit absence of forbidden mutations: no cross-execution property contamination in a fixed implementation.
- Final read/verification call: fixture marker read plus id reads.
- Expected source method/line/branch corroboration: `JobPropertyResolvers.started(JobConfig)` called twice for same job name before first execution completes; `JobPropertyResolvers.of(String)` returns latest resolver.

#### Isolation and variants

- Cleanup/reset requirements: clear fixture marker store and latches after test.
- Fixed clock and deterministic-id handling: ids are dynamic; bind via `Location`.
- External stub reset: none.
- Transaction handling: wait for both executions to reach terminal status before assertions.
- Async polling/timeout strategy: poll ids every 100 ms up to 10 seconds after latch release.
- Nearby boundary variants requiring separate tests: same marker value for both executions, different job names, using `StepExecution#getJobParameters()` instead of deprecated resolver.

## Notes And Assumptions

- No API was executed during this review; evidence comes from generated test code, behavior files, source files, and JaCoCo reports.
- The visible `src/main/java` tree lacks the API controller/provider source files named by JaCoCo, so controller/provider implementation evidence is taken from JaCoCo class/method names, behavior documents, OpenAPI-derived function inventory, and generated test responses.
- `business-behavior.md` is authoritative. `full-behavior.md` is used for exact function-name and route normalization only.
- The generated test suite resets SUT state before each test. Cross-test composition is not used for happy-path credit.
- Some generated tests contain commented-out status assertions. They are counted as invocations when the request is present, but not as covered business outcomes unless active assertions and source evidence prove the branch.
- The `coverage/evomaster_*` XML reports overlap with and are narrower than `reports/report.xml`; counters are unioned by element where possible and are not summed.
- Exact HTTP statuses for new recommended failure tests should be asserted from the current application mapping once implemented; the source discriminator and state condition are the business-critical coverage target.
