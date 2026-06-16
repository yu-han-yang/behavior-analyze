# Business Behavior Coverage Report

## Executive Summary

- Project under analysis: `/Users/yangyuhan/behavior-analyze/spring-batch-rest`
- Business behavior specification: [business-behavior.md](/Users/yangyuhan/behavior-analyze/spring-batch-rest/business-behavior.md)
- Function behavior inventory: [full-behavior.md](/Users/yangyuhan/behavior-analyze/spring-batch-rest/full-behavior.md)
- Test root analyzed: [tests](/Users/yangyuhan/behavior-analyze/spring-batch-rest/tests)
- Total test files analyzed: `1`
- Total test cases analyzed: `26` active `@Test` methods. The EvoMaster file comment says `27`, but only 26 test methods are present.
- JaCoCo XML reports used: [reports/report.xml](/Users/yangyuhan/behavior-analyze/spring-batch-rest/reports/report.xml)
- JaCoCo CSV reports used: [reports/report.csv](/Users/yangyuhan/behavior-analyze/spring-batch-rest/reports/report.csv)
- Source roots analyzed: [src/main/java](/Users/yangyuhan/behavior-analyze/spring-batch-rest/src/main/java)
- Total documented business behaviors: `9`
- Covered: `2`
- Partially covered: `6`
- Not covered: `1`
- Unclear: `0`
- Business behavior coverage: `55.6%`
- Happy-path coverage: `55.6%` (`5/9`)
- Failure/exceptional-case coverage: `48.4%` (`15/31`)
- Behavior checklist coverage: `20/40`

JaCoCo coverage signal, from XML primary source:

| Metric | Covered / Total | Coverage |
|---|---:|---:|
| Line | `306/504` | `60.7%` |
| Branch | `46/307` | `15.0%` |
| Method | `142/238` | `59.7%` |
| Class | `32/42` | `76.2%` |

The CSV cross-check is close but not identical for line counters: `309/508` lines. Because there is one XML and one CSV, I used XML as primary and treated CSV as corroborating evidence.

Strongest coverage:
- `B1` job discovery is directly covered with assertions on the returned `personJob`.
- `B5` global execution history is covered, including negative provider/limit failures.
- Synchronous and asynchronous `POST /jobExecutions` are both exercised with meaningful execution-state assertions.
- `GET /jobExecutions/{id}` has a real create-then-read test through a returned `Location`.

Most important gaps:
- `B2` named job inspection is not behavior-covered because the test uses an arbitrary path name rather than a registered job returned by discovery.
- Filter behaviors `B7`, `B8`, and `B9` are not covered on their happy paths because tests do not reuse the created execution’s exact `jobName` and returned `exitCode`.
- Several documented failures are missing: malformed/non-numeric ids, malformed job path encoding, async property-concurrency leakage, regex-sensitive job names, and Spring Batch launch-rule failures.
- JaCoCo branch coverage is low, which agrees with the behavior-level finding that many exceptional branches are untested.

## Test Corpus Summary

| Area | Count / Summary |
|---|---|
| Test files analyzed | `1` |
| Test cases analyzed | `26` |
| Primary test framework | JUnit 4, REST Assured, EvoMaster-generated SUT controller |
| Main endpoints/functions exercised | `GET /jobs`, `GET /jobs/{jobName}`, `POST /jobExecutions`, `GET /jobExecutions`, `GET /jobExecutions/{id}`, `GET /v3/api-docs` |
| Positive-path tests | `18` tests include at least one successful 2xx operation |
| Negative/failure tests | `11` tests include at least one active 4xx/5xx assertion |
| Tests with business assertions | `18` tests assert job/execution fields or structured error details |
| Tests with only status/code/content-type assertions | `8` tests have weak or no business-state assertions |

| Test File | Test Cases | Main Behavior Area | Evidence Quality |
|---|---:|---|---|
| [EM_spring_batch_rest_True_25_false_false_SPECIFIED_false_0_Test.java](/Users/yangyuhan/behavior-analyze/spring-batch-rest/tests/EM_spring_batch_rest_True_25_false_false_SPECIFIED_false_0_Test.java) | 26 | EvoMaster REST API exploration across jobs and job executions | Medium |

## Function-To-Code Map

The visible `src/main/java` tree does not include REST controller source files, but JaCoCo covers API classes from the instrumented SUT. Controller mapping therefore uses `full-behavior.md`, `spring-batch-rest.json`, tests, and JaCoCo class/method names.

| Business Function | Endpoint | Main Code / Coverage Evidence |
|---|---|---|
| `list registered Spring Batch jobs` | `GET /jobs` | `JobController.all`, `JobService.jobs`; both 100% line/method coverage |
| `get registered job by name` | `GET /jobs/{jobName}` | `JobController.get`, `JobService.job`; both covered, but behavior validation is weak |
| `start job synchronously` | `POST /jobExecutions` | `JobExecutionController.put`, `JobExecutionService.launch`, [AdHocStarter.start(JobConfig)](/Users/yangyuhan/behavior-analyze/spring-batch-rest/src/main/java/com/github/chrisgleissner/springbatchrest/util/core/AdHocStarter.java), [JobParamUtil](/Users/yangyuhan/behavior-analyze/spring-batch-rest/src/main/java/com/github/chrisgleissner/springbatchrest/util/JobParamUtil.java) |
| `start job asynchronously` | `POST /jobExecutions` | Same endpoint; `JobConfig.isAsynchronous()` selects async launcher in `AdHocStarter` |
| `list job execution history` | `GET /jobExecutions` | `JobExecutionController.all`, `JobExecutionService.jobExecutions`, `CachedJobExecutionProvider`, `AllJobExecutionProvider` |
| `find job executions by job name` | `GET /jobExecutions?jobName=...` | Same collection endpoint/provider filter path |
| `find job executions by exit code` | `GET /jobExecutions?exitCode=...` | Same collection endpoint/provider filter path |
| `find job executions by job name and exit code` | `GET /jobExecutions?jobName=...&exitCode=...` | Combined provider filter path |
| `get job execution by id` | `GET /jobExecutions/{id}` | `JobExecutionController.get`, `JobExecutionService.jobExecution`, `JobExecution.fromSpring` |

## Coverage Matrix

| ID | Business Behavior | Happy Path | Failure Cases | Optional Verification | Status | Confidence | Main Gap |
|---|---|---|---|---|---|---|---|
| B1 | Discover registered batch jobs | Covered | N/A | N/A | Covered | High | None material |
| B2 | Inspect a named batch job | Not Covered | `0/3` | N/A | Not Covered | Medium | No test gets a registered name from discovery and then retrieves that name |
| B3 | Start a synchronous batch execution | Covered | `3/5` | Partly | Partially Covered | High | Missing launch-rule/repository failure and full discovery-to-launch binding |
| B4 | Submit an asynchronous batch execution | Covered | `2/5` | Partly | Partially Covered | High | Missing null-name async case, concurrency property leakage, and launch-rule failures |
| B5 | Review global execution history | Covered | `2/2` | N/A | Covered | High | Some related filter tests are weak, but behavior checklist is covered |
| B6 | Retrieve a specific execution record | Covered | `2/3` | Partly | Partially Covered | High | Missing non-numeric id failure |
| B7 | Review executions for one job | Not Covered | `2/4` | Not Covered | Partially Covered | Medium | No same-test sync launch followed by `jobName={same name}` assertions |
| B8 | Review executions by outcome | Not Covered | `2/4` | Not Covered | Partially Covered | Medium | No query using returned `jobExecution.exitCode` |
| B9 | Review one job's executions by outcome | Not Covered | `2/5` | Not Covered | Partially Covered | Medium | No successful combined filter using same `jobName` and returned `exitCode` |

## Behavior Details

### B1: Discover registered batch jobs

- Business goal: Find the batch jobs currently exposed by the service.
- Status: Covered
- Confidence: High

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | `GET /jobs`, `Accept=application/hal+json`, return job names | Yes | `test_6_getOnJobsReturnsObject` asserts one job named `personJob` | `JobController.all`, `JobService.jobs` 100% line/method |

Optional verification coverage: none documented.

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list registered Spring Batch jobs` | No confirmed domain-level failure branch | N/A | N/A | N/A |

Coverage item summary: Covered items `1/1`. Missing items: none.

Recommended tests: none required for documented coverage.

### B2: Inspect a named batch job

- Business goal: Retrieve the resource representation for a specific batch job name.
- Status: Not Covered
- Confidence: Medium

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | Get `job.name` from `GET /jobs` | Yes, separately | `test_6_getOnJobsReturnsObject` | `JobController.all` covered |
| 2 | `get registered job by name` | `GET /jobs/{jobName}` using returned `personJob` | No | `test_20_getOnJobReturnsObject` calls `/jobs/31SVHZ51PHVA`, not a discovered registered name | `JobController.get` covered |

Optional verification coverage: none documented.

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list registered Spring Batch jobs` | Registry has no jobs and caller does not know a valid name | No | No empty-registry test | Not isolated |
| `get registered job by name` | Malformed or unsafe path encoding | No | No malformed path test | Not isolated |
| `get registered job by name` | Name is not registered | No | `test_20` shows arbitrary name returns `200`, exposing discrepancy rather than covering expected failure | `JobController.get` covered |

Coverage item summary: Covered items `0/4`. Missing items: happy path and all failures.

Recommended tests: call `GET /jobs`, extract `personJob`, then call `GET /jobs/personJob` and assert returned `job.name=personJob`; add malformed path and truly missing-name tests with expected error behavior.

### B3: Start a synchronous batch execution

- Business goal: Run a registered job synchronously and receive the returned execution state.
- Status: Partially Covered
- Confidence: High

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | Obtain launchable `job.name` | Yes via shortcut | `personJob` is known and confirmed by `test_6` | Job registry path covered |
| 2 | `start job synchronously` | `POST /jobExecutions` with `name=personJob`, `asynchronous=false` or omitted | Yes | `test_12`, `test_21`, `test_23` assert `COMPLETED`, `jobName=personJob`, no exceptions | `JobExecutionController.put`; `AdHocStarter.start(JobConfig)` covered |

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get job execution by id` | Read generated execution id | Yes | `test_21` follows `Location` and asserts execution fields |
| 2 | `find job executions by job name` | Query history for same job | No | No sync launch followed by job-name-only filter assertion |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list registered Spring Batch jobs` | No launchable job name can be obtained | No | No empty registry / no known name test | Not isolated |
| `start job synchronously` | Body omits `name` or sets `name=null` | Yes | `test_4`, `test_5` assert `500` and null job message | Exception handler covered |
| `start job synchronously` | Body `name` is not registered | Yes | `test_9`, `test_14`, `test_19` assert `404` `NoSuchJobException` | Exception handler covered |
| `start job synchronously` | No body or malformed JSON body | Yes | `test_18`, `test_19` assert `400` empty body | Controller binding path covered |
| `start job synchronously` | Spring Batch parameter/repository/restart validation fails | No | No restart/already-complete/invalid parameter test | `AdHocStarter` branch coverage only 37.5% |

Coverage item summary: Covered items `4/6`. Missing items: no launchable-name setup failure and Spring Batch launch-rule failure.

Recommended tests: include an explicit discovery-to-launch test; add a controlled Spring Batch validation failure, such as duplicate parameters with unique parameter disabled or a job configured to fail validation.

### B4: Submit an asynchronous batch execution

- Business goal: Start a registered job without waiting for completion.
- Status: Partially Covered
- Confidence: High

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | Obtain launchable `job.name` | Yes via shortcut | `personJob` known from `test_6` | Job registry path covered |
| 2 | `start job asynchronously` | `POST /jobExecutions` with `name=personJob`, `asynchronous=true` | Yes | `test_1`, `test_2`, `test_15`, `test_17` assert `STARTING`, `UNKNOWN`, null timestamps | `AdHocStarter` async branch executed |

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get job execution by id` | Later inspect generated id | No | No async id polling test |
| 2 | `find job executions by job name` | Later inspect same-job history | Partly | `test_17` lists global history, not job-name scoped history |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list registered Spring Batch jobs` | No launchable job name can be obtained | No | No such test | Not isolated |
| `start job asynchronously` | Body omits `name` or sets `name=null` | No | Omitted-name tests use `asynchronous=false`; async empty string is not null | `AdHocStarter` exception path covered generally |
| `start job asynchronously` | Body `name` is not registered | Yes | `test_16` posts empty name with `asynchronous=true` and gets `404` | Exception handler covered |
| `start job asynchronously` | Request body absent or malformed | Yes | `test_18`, `test_19` assert `400` | Controller binding path covered |
| `start job asynchronously` | Concurrent async executions use different properties through job-name-scoped resolver | No | No concurrency test | `JobPropertyResolvers` partial branch coverage |

Coverage item summary: Covered items `3/6`. Missing items: async null-name, no launchable-name setup failure, and concurrent property isolation.

Recommended tests: start two async `personJob` executions with different properties and verify each execution reads its own parameters; add async `{ "asynchronous": true }` missing-name test.

### B5: Review global execution history

- Business goal: View stored job executions across jobs.
- Status: Covered
- Confidence: High

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list job execution history` | `GET /jobExecutions` with omitted or positive `limitPerJob` | Yes | `test_17` calls `GET /jobExecutions?limitPerJob=3` and asserts three `personJob` executions | `JobExecutionController.all`, providers covered |

Optional verification coverage: none documented.

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list job execution history` | Negative `limitPerJob` | Yes | `test_2` asserts `500` with `IllegalArgumentException` for `limitPerJob=-40` | Provider/exception path covered |
| `list job execution history` | Query combinations trigger null handling bugs | Yes | `test_0`, `test_1` assert `500` with `NullPointerException` | `CachedJobExecutionProvider` covered |

Coverage item summary: Covered items `3/3`. Missing items: none for documented B5 checklist.

Recommended tests: add cleaner global-only negative-limit test without unrelated filters to make intent clearer.

### B6: Retrieve a specific execution record

- Business goal: Inspect one generated execution by id.
- Status: Partially Covered
- Confidence: High

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | Obtain `job.name` | Yes via shortcut | `personJob` confirmed by `test_6` | Job API covered |
| 2 | `start job synchronously` | Create execution and capture generated id | Yes | `test_21` posts sync `personJob` and extracts `Location` | Launch path covered |
| 3 | `get job execution by id` | `GET /jobExecutions/{id}` using generated id | Yes | `test_21` follows `Location` and asserts returned execution fields | `JobExecutionController.get`, `JobExecutionService.jobExecution` covered |

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `list job execution history` | Verify generated execution appears in history | Partly | `test_17` verifies global history, but not the same generated id |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `start job synchronously` | Unknown, null, or unlaunchable job name | Yes | `test_4`, `test_5`, `test_9`, `test_19` | Exception handler covered |
| `get job execution by id` | No execution exists for path `id` | Yes | `test_26` asserts `404` for negative id | `NoSuchJobExecutionException` handler covered |
| `get job execution by id` | Path `id` is non-numeric | No | No non-numeric path test | Not isolated |

Coverage item summary: Covered items `3/4`. Missing items: non-numeric id rejection.

Recommended tests: call `GET /jobExecutions/not-a-number` and assert documented binding/error behavior.

### B7: Review executions for one job

- Business goal: View execution history scoped to one job name.
- Status: Partially Covered
- Confidence: Medium

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | Obtain `job.name` | Yes via shortcut | `test_6` confirms `personJob` | Job API covered |
| 2 | `start job synchronously` | Create execution for that job | Yes | `test_12`, `test_21`, `test_23` | Launch path covered |
| 3 | `find job executions by job name` | `GET /jobExecutions?jobName=personJob` and assert matching results | No | `test_7`/`test_13` use arbitrary names; no same-name result assertions | Provider filter covered but weakly |

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get job execution by id` | Inspect one matching execution | Partly | `test_21`, but not tied to a job-name filter result |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `start job synchronously` | Selected job cannot be launched | Yes | Missing/unknown-name POST tests | Launch/exception covered |
| `find job executions by job name` | Query `jobName` differs from stored job name | No | Arbitrary-name tests assert only status/content type | Provider filter covered |
| `find job executions by job name` | Invalid regex syntax | No | No `PatternSyntaxException` test found | Provider regex path likely covered partially |
| `find job executions by job name` | Negative `limitPerJob` | Yes | `test_2` asserts negative-limit failure | Provider/exception covered |

Coverage item summary: Covered items `2/5`. Missing items: happy path, mismatch assertion, regex failure.

Recommended tests: sync launch `personJob`, then `GET /jobExecutions?jobName=personJob&limitPerJob=3`; assert every returned `jobExecution.jobName` is `personJob` and includes the new execution.

### B8: Review executions by outcome

- Business goal: Find executions with a selected exit code.
- Status: Partially Covered
- Confidence: Medium

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | Obtain launchable job | Yes via shortcut | `test_6` | Job API covered |
| 2 | `start job synchronously` | Create execution and capture returned `exitCode` | Yes | `test_12`, `test_21`, `test_23` assert `COMPLETED` | Launch path covered |
| 3 | `find job executions by exit code` | Query `exitCode={returned exitCode}` and assert matching results | No | Tests use random/non-matching exit codes or no result assertions | Provider filter covered but weakly |

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get job execution by id` | Confirm selected execution exit code | Partly | `test_21` confirms id read, but not from exit-code filter |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `start job synchronously` | Launch fails before desired exit code exists | Yes | Missing/unknown-name POST tests | Exception path covered |
| `find job executions by exit code` | Query differs from stored exit code | No | `test_12` uses different exit code but does not assert exclusion | Provider filter covered |
| `find job executions by exit code` | Query `COMPLETED` immediately after async submission | No | No immediate `COMPLETED` async filter test | Async branch covered |
| `find job executions by exit code` | Query combinations trigger provider null handling defects | Yes | `test_0`, `test_1` assert `NullPointerException` failures | Provider path covered |

Coverage item summary: Covered items `2/5`. Missing items: happy path, mismatch-result assertion, async terminal-timing failure.

Recommended tests: sync launch `personJob`, read returned `exitCode=COMPLETED`, then `GET /jobExecutions?exitCode=COMPLETED` and assert returned executions have `COMPLETED`.

### B9: Review one job's executions by outcome

- Business goal: Find executions for a specific job that also match a specific exit code.
- Status: Partially Covered
- Confidence: Medium

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list registered Spring Batch jobs` | Obtain `job.name` | Yes via shortcut | `test_6` | Job API covered |
| 2 | `start job synchronously` | Create execution and capture `exitCode` | Yes | `test_12`, `test_21`, `test_23` | Launch path covered |
| 3 | `find job executions by job name and exit code` | Query same `jobName` and returned `exitCode` | No | `test_12`/`test_15` query `jobName=personJob` with random exit codes and no result assertions | Combined provider path covered but weakly |

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get job execution by id` | Inspect one matching execution | Partly | `test_21`, not tied to combined-filter result |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `start job synchronously` | Setup launch cannot create execution | Yes | Missing/unknown-name POST tests | Exception path covered |
| `find job executions by job name and exit code` | `jobName` does not equal stored job name | No | No asserted mismatch exclusion | Provider filter covered |
| `find job executions by job name and exit code` | `exitCode` does not equal stored exit code | No | Random exit-code tests assert only status/content type | Provider filter covered |
| `find job executions by job name and exit code` | Invalid regex syntax in `jobName` | No | No `PatternSyntaxException` test found | Not isolated |
| `find job executions by job name and exit code` | Non-matching exit code triggers cached-provider null path | Yes | `test_1` asserts `500` `NullPointerException` for combined filter | `CachedJobExecutionProvider` covered |

Coverage item summary: Covered items `2/6`. Missing items: happy path and most filter-failure assertions.

Recommended tests: sync launch `personJob`, capture `COMPLETED`, then query `GET /jobExecutions?jobName=personJob&exitCode=COMPLETED`; assert every result satisfies both fields and the new execution is present.

## Cross-Behavior Gaps

- Required value binding is often missing. Tests know `personJob` directly, which is allowed as a shortcut for launch, but filter workflows should still reuse returned `jobName`, id, and `exitCode`.
- Several tests call endpoints with filters but assert only status/content type, so they do not prove the business filter result.
- Direct endpoint execution overstates business coverage for `GET /jobs/{jobName}` because arbitrary names return `200`.
- Failure tests are useful, but they miss important documented branches: non-numeric id, malformed job path, regex job-name failure, async property-concurrency leakage, and Spring Batch restart/validation failures.
- JaCoCo-covered code lacks behavior-level evidence in several areas, especially provider filter branches and low-level launch parameter conversion.
- API source for controllers is not present under `src/main/java`; controller behavior is inferred from generated tests, `full-behavior.md`, OpenAPI, and JaCoCo class/method coverage.

## Suggested Additional Tests

| Priority | Behavior ID | Test Intent | Minimal Setup | Calls / Operations | Required Assertions | Coverage Type |
|---:|---|---|---|---|---|---|
| 1 | B9 | Successful combined job/outcome filter | Registered `personJob` | `GET /jobs`; sync `POST /jobExecutions`; `GET /jobExecutions?jobName=personJob&exitCode=COMPLETED` | All results have `jobName=personJob` and `exitCode=COMPLETED`; created execution included | Success |
| 2 | B7 | Successful job-name filter | Registered `personJob` | Sync launch; `GET /jobExecutions?jobName=personJob` | Results belong to `personJob`; created execution included | Success |
| 3 | B8 | Successful exit-code filter | Registered `personJob` | Sync launch; query returned `exitCode` | Results have returned exit code; created execution included | Success |
| 4 | B2 | Registered named job lookup | Existing registry with `personJob` | `GET /jobs`; `GET /jobs/personJob` | Returned `job.name` equals discovered name | Success |
| 5 | B6 | Non-numeric id rejection | None | `GET /jobExecutions/not-a-number` | Expected 4xx binding/error response | Failure |
| 6 | B4 | Async property isolation | Long-running or property-reading job | Start two async executions with different properties | Each execution reads its own parameters | Regression |
| 7 | B7/B9 | Regex-sensitive jobName failure | Existing execution | Query invalid regex-like `jobName` | Documented 4xx or known 5xx error asserted | Failure |
| 8 | B3/B4 | Spring Batch launch-rule failure | Job configured to fail validation/restart rule | `POST /jobExecutions` with violating parameters | Error type/status/message asserted | Failure |

## Appendix: Coverage Artifacts Used

### JaCoCo XML Files

- [reports/report.xml](/Users/yangyuhan/behavior-analyze/spring-batch-rest/reports/report.xml)
- Counters extracted: instruction `1872/3925`, branch `46/307`, line `306/504`, method `142/238`, class `32/42`.
- Notes: primary source. XML includes API controller/provider classes not present as source files under this workspace’s `src/main/java`.

### JaCoCo CSV Files

- [reports/report.csv](/Users/yangyuhan/behavior-analyze/spring-batch-rest/reports/report.csv)
- Counters extracted by summing rows: instruction `1872/3925`, branch `46/307`, line `309/508`, method `142/238`.
- Notes: used as aggregate cross-check only; line counters differ slightly from XML.

## Appendix: Test Inventory

| Test File | Test Case | Operations | Assertions | Related Behavior IDs |
|---|---|---|---|---|
| EM...Test.java | `test_0` | `GET /jobExecutions?jobName=&exitCode=OYB&limitPerJob=0` | `500`, `NullPointerException` | B5, B8, B9 |
| EM...Test.java | `test_1` | Async `POST /jobExecutions`; combined filtered `GET /jobExecutions` | Async `STARTING/UNKNOWN`; then `500` NPE | B4, B5, B9 |
| EM...Test.java | `test_2` | Async `POST`; `GET /jobExecutions?jobName=&limitPerJob=-40` | Async state; then `500` `IllegalArgumentException` | B4, B5, B7 |
| EM...Test.java | `test_4` | Sync `POST /jobExecutions` without `name` | `500`, null-job runtime message | B3, B6 |
| EM...Test.java | `test_5` | Sync `POST /jobExecutions` without `name` | `500`, null-job runtime message | B3, B6 |
| EM...Test.java | `test_6` | `GET /jobs` | one `personJob` returned | B1 |
| EM...Test.java | `test_7` | `GET /jobExecutions?jobName=random&limitPerJob=179` | status/content type only | B7 endpoint evidence |
| EM...Test.java | `test_8` | `GET /jobExecutions?jobName=random&exitCode=random` | status/content type only | B9 endpoint evidence |
| EM...Test.java | `test_9` | Unknown sync `POST`; global `GET /jobExecutions` | `404` unknown job; history fields | B3, B5 |
| EM...Test.java | `test_10` | `GET /jobExecutions?exitCode=random` | content type only; active status assertion absent | B8 endpoint evidence |
| EM...Test.java | `test_11` | `GET /jobExecutions` with negative limit and filters | `200`, content type only | B5/B7 weak evidence |
| EM...Test.java | `test_12` | Sync `POST`; combined filtered `GET` with random exit code | Sync `COMPLETED`; filter status only | B3, B8, B9 |
| EM...Test.java | `test_13` | `GET /jobExecutions?jobName=random` | status/content type only | B7 endpoint evidence |
| EM...Test.java | `test_14` | Unknown sync `POST`; combined filtered `GET` | `404` unknown job; filter status only | B3, B9 |
| EM...Test.java | `test_15` | Async `POST`; combined filtered `GET` with random exit code | Async `STARTING/UNKNOWN`; filter status only | B4, B9 |
| EM...Test.java | `test_16` | Async `POST` with empty name; exit-code `GET` | `404` unknown empty job; filter status only | B4, B8 |
| EM...Test.java | `test_17` | Async `POST`; global `GET /jobExecutions?limitPerJob=3` | Async state; three completed history records | B4, B5 |
| EM...Test.java | `test_18` | Empty-body `POST /jobExecutions` | `400`, empty body | B3, B4 |
| EM...Test.java | `test_19` | Empty-body `POST`; unknown-name `POST` | `400`; `404` `NoSuchJobException` | B3, B4 |
| EM...Test.java | `test_20` | `GET /jobs/{arbitraryName}` | returned same arbitrary `job.name` | B2 discrepancy evidence |
| EM...Test.java | `test_21` | Sync `POST`; follow `Location` to `GET /jobExecutions/{id}` | Sync `COMPLETED`; id lookup fields | B3, B6 |
| EM...Test.java | `test_22` | `GET /jobExecutions/0` | Existing fixture execution fields | B6 |
| EM...Test.java | `test_23` | Sync `POST`; attempted id lookup with fallback `/45` | Sync `COMPLETED`; lookup status assertion commented | B3, B6 weak evidence |
| EM...Test.java | `test_24` | `GET /v3/api-docs` | `200` only | Unrelated |
| EM...Test.java | `test_25` | `GET /jobExecutions/143` | Active status assertion commented; content type only | B6 weak evidence |
| EM...Test.java | `test_26` | `GET /jobExecutions/-16776668` | `404`, `NoSuchJobExecutionException` | B6 |