# Business Behavior Coverage Report

## Executive Summary

| Item | Result |
|---|---|
| Project under analysis | `/Users/yangyuhan/behavior-analyze/quartz-manager` |
| Business behavior specification | `/Users/yangyuhan/behavior-analyze/quartz-manager/business-behavior.md` |
| Function behavior inventory | `/Users/yangyuhan/behavior-analyze/quartz-manager/full-behavior.md` |
| Test root analyzed | `/Users/yangyuhan/behavior-analyze/quartz-manager/tests` |
| Total test files analyzed | 1 |
| Total test cases analyzed | 45 |
| JaCoCo XML reports used | `/Users/yangyuhan/behavior-analyze/quartz-manager/reports/report.xml` |
| JaCoCo CSV reports used | `/Users/yangyuhan/behavior-analyze/quartz-manager/reports/report.csv` |
| Source roots analyzed | `/Users/yangyuhan/behavior-analyze/quartz-manager/src/main/java` |
| Total documented business behaviors | 11 |
| Covered | 1 |
| Partially covered | 10 |
| Not covered | 0 |
| Unclear | 0 |
| Business behavior coverage | 54.5% |
| Happy-path coverage | 45.5% (5/11) |
| Failure/exceptional-case coverage | 72.1% (31/43) |
| Behavior checklist coverage | 36/54 (66.7%) |
| JaCoCo coverage signal | Line 53.8%, branch 29.5%, method 61.3%, class 79.3% |

There is one XML and one CSV report, so exact combined JaCoCo coverage can be taken from the XML top-level counters. The CSV was used as a per-class cross-check, not summed separately.

- Strongest behavior evidence is for authentication, eligible job discovery, scheduler details, stop scheduler, and pause scheduler.
- Authentication failure and missing-bearer checks are broadly exercised across protected endpoints.
- Major happy-path gaps remain for start scheduler, resume scheduler, list triggers, schedule simple trigger, retrieve simple trigger, and reschedule simple trigger.
- Several documented 404/not-found cases are not covered, or are left flaky/ambiguous. Some tests assert 500 where the API contract expects 404 or 204.
- Most generated tests assert HTTP status or error envelopes only. Only 5 tests assert business response content.
- The current `src/main/java` checkout contains only shared constants/properties/utilities. Controller and service mapping therefore relies on `full-behavior.md` plus JaCoCo class names from the runtime SUT report.

## Test Corpus Summary

| Area | Count / Summary |
|---|---|
| Test files analyzed | 1 |
| Test cases analyzed | 45 |
| Primary test framework | EvoMaster generated JUnit 5 tests using REST-assured |
| Main endpoints/functions exercised | `POST /quartz-manager/auth/login`, `GET /quartz-manager/jobs`, `GET /quartz-manager/scheduler`, scheduler run/stop/pause/resume, `GET /quartz-manager/triggers`, simple-trigger GET/POST/PUT, and `/v3/api-docs` |
| Positive-path tests | 11 executable 2xx tests: 10 business endpoint tests plus 1 `/v3/api-docs` test |
| Negative/failure tests | 33 executable negative tests, plus 1 flaky/ambiguous simple-trigger lookup test with no executable status assertion |
| Tests with business assertions | 5 |
| Tests with only status/code/error assertions | 40 |

All generated tests call `controller.resetStateOfSUT()` before each test. Authenticated tests usually perform `POST /quartz-manager/auth/login` with form credentials `foo/bar` or `foo2/bar`, extract `accessToken`, and send `Authorization: Bearer {token}`. No direct database inserts or business fixtures appear in the generated test code. Cleanup is `controller.stopSut()` in `@AfterAll`.

| Test File | Test Cases | Main Behavior Area | Evidence Quality |
|---|---:|---|---|
| `/Users/yangyuhan/behavior-analyze/quartz-manager/tests/EM_quartz_manager_True_25_false_false_SPECIFIED_false_0_Test.java` | 45 | Generated REST tests across all documented endpoint groups | Medium. Broad endpoint exercise, but limited state assertions and no successful trigger create/update/read workflow |

## Function-to-Code Map

| Business Function | Endpoint / Operation | Code Evidence | Failure Branch Evidence |
|---|---|---|---|
| `authenticate user` | `POST /quartz-manager/auth/login` | `QuartzManagerPaths` defines login path; `OpenAPIConfigConsts` defines bearer scheme; JaCoCo covers security config/helpers and `UserController` | 401 invalid/missing credentials in `test_36_postOnLoginReturns401` |
| `list eligible job classes` | `GET /quartz-manager/jobs` | `JobController`, `JobService` in JaCoCo; endpoint behavior in `full-behavior.md` | 401 unauthenticated in `test_10_getOnJobsReturns401`; generic 404 not covered |
| `retrieve scheduler details` | `GET /quartz-manager/scheduler` | `SchedulerController`, `SchedulerService`, `SchedulerToSchedulerDTO` in JaCoCo | 401 unauthenticated in `test_9` and `test_11`; generic 404 not covered |
| `start scheduler` | `GET /quartz-manager/scheduler/run` | `SchedulerController`, `SchedulerService` in JaCoCo | 401 in `test_31`; internal 500 in `test_13`, `test_14`, `test_22`; 404 not covered |
| `stop scheduler` | `GET /quartz-manager/scheduler/stop` | `SchedulerController`, `SchedulerService` in JaCoCo | 401 in `test_30`, `test_34`; 404 not covered |
| `pause scheduler` | `GET /quartz-manager/scheduler/pause` | `SchedulerController`, `SchedulerService` in JaCoCo | 401 in `test_33`; 404 not covered |
| `resume scheduler` | `GET /quartz-manager/scheduler/resume` | `SchedulerController`, `SchedulerService` in JaCoCo | 401 in `test_32`; internal 500 in `test_15`, `test_20`, `test_21`; 404 not covered |
| `list triggers` | `GET /quartz-manager/triggers` | `TriggerController`, `TriggerService` in JaCoCo | 401 in `test_8`; internal 500 in `test_0`, `test_1`; 404 not covered |
| `schedule simple trigger` | `POST /quartz-manager/simple-triggers/{name}` | `SimpleTriggerController`, `SimpleTriggerService`, validators/converters in JaCoCo | 401, 400 invalid body, 415 missing JSON media type covered; 404 not covered |
| `retrieve simple trigger by name` | `GET /quartz-manager/simple-triggers/{name}` | `SimpleTriggerController`, `AbstractSchedulerService`, converters in JaCoCo | 401 covered; missing trigger exercised as 500; executable 404 and name-mismatch cases missing |
| `reschedule simple trigger` | `PUT /quartz-manager/simple-triggers/{name}` | `SimpleTriggerController`, `SimpleTriggerService`, validators/converters in JaCoCo | 401, 400 invalid replacement body, 415 missing JSON media type covered; successful update, 404, name-mismatch, and internal PUT 500 missing |

## Coverage Matrix

| ID | Business Behavior | Happy Path | Failure Cases | Optional Verification | Status | Confidence | Main Gap |
|---|---|---|---|---|---|---|---|
| B1 | Obtain API access token | Covered | Covered | None | Covered | High | No standalone explicit `accessToken` nonblank assertion |
| B2 | Inspect eligible job classes | Covered | Partially Covered | None | Partially Covered | High | Generic 404 case missing |
| B3 | Inspect scheduler status and configuration | Covered | Partially Covered | None | Partially Covered | High | Generic 404 case missing |
| B4 | Start scheduler execution | Not Covered | Partially Covered | Not executed | Partially Covered | High | No 204 start success or started-state verification |
| B5 | Stop scheduler execution | Covered | Partially Covered | Not executed | Partially Covered | Medium | No post-stop scheduler-status verification; 404 missing |
| B6 | Pause scheduler execution | Covered | Partially Covered | Not executed | Partially Covered | Medium | No post-pause scheduler-status verification; 404 missing |
| B7 | Resume scheduler execution | Not Covered | Partially Covered | Not executed | Partially Covered | High | No 204 resume success or resumed-state verification |
| B8 | List scheduler triggers | Not Covered | Partially Covered | None | Partially Covered | High | Authenticated list returns asserted 500, not documented list result |
| B9 | Schedule a named simple trigger | Not Covered | Partially Covered | Not executed | Partially Covered | High | No valid `SimpleTriggerInputDTO` POST producing 201 |
| B10 | Retrieve a named simple trigger | Not Covered | Partially Covered | None | Partially Covered | Medium | No successful create/read same-name workflow; 404 is flaky/unasserted |
| B11 | Reschedule a named simple trigger | Not Covered | Partially Covered | Not executed | Partially Covered | Medium | No successful create/PUT same-name workflow |

## Behavior Details

### B1: Obtain API access token

- Business goal: Authenticate credentials and obtain the bearer token used by protected APIs.
- Status: Covered.
- Confidence: High.

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `authenticate user` | POST form credentials to `/quartz-manager/auth/login` and obtain `{token}` | Yes | Many authenticated tests extract `accessToken`; later 200/204 protected calls prove token acceptance | Security config/helpers covered; `UserController` method partly covered |

Happy-path item: Covered. Valid login is exercised as setup and the token authorizes subsequent protected calls.

Optional verification coverage: None documented.

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `authenticate user` | `username` or `password` missing, invalid, or not configured | Yes | `test_36_postOnLoginReturns401` posts an empty form and asserts 401/error/path | Authentication failure/security helper coverage present |

Coverage item summary: Covered items 2/2.

Gap: Add an explicit success assertion for HTTP 200 and nonblank `accessToken` to make the setup evidence self-contained.

Recommended tests: Valid-login test with `Content-Type=application/x-www-form-urlencoded`, known credentials, assert 200, JSON body contains nonblank `accessToken`, then use it on one protected endpoint.

### B2: Inspect eligible job classes

- Business goal: Discover the Java job classes available to the scheduler.
- Status: Partially Covered.
- Confidence: High.

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `authenticate user` | Obtain `{token}` | Yes | `test_3`, `test_4`, `test_6` log in first | Auth/security coverage present |
| 2 | `list eligible job classes` | GET `/quartz-manager/jobs` with bearer token | Yes | `test_3`, `test_4`, `test_6` assert 200, JSON content, and `size() == 0` | `JobController` 4/4 lines; `JobService` 18/20 lines |

Happy-path item: Covered. The required login-then-list workflow is directly tested.

Optional verification coverage: None documented.

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `authenticate user` | Invalid credentials | Yes | `test_36` | Auth failure coverage |
| `list eligible job classes` | Missing or invalid bearer token | Yes | `test_10_getOnJobsReturns401` | Security filter/entry point coverage |
| `list eligible job classes` | OpenAPI-declared generic not-found condition | No | No test produces a 404 for `/jobs` | No source condition visible |

Coverage item summary: Covered items 3/4.

Gap: The documented 404 path is not exercised and has no visible setup condition.

Recommended tests: Add a contract/implementation clarification test only after a concrete `/jobs` not-found condition is defined.

### B3: Inspect scheduler status and configuration

- Business goal: Read the singleton scheduler identity, instance id, status, and trigger-key view.
- Status: Partially Covered.
- Confidence: High.

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `authenticate user` | Obtain `{token}` | Yes | `test_2`, `test_5` log in first | Auth/security coverage present |
| 2 | `retrieve scheduler details` | GET `/quartz-manager/scheduler` with bearer token | Yes | `test_2`, `test_5` assert name `example`, instance `NON_CLUSTERED`, status `STOPPED`, `triggerKeys` null | `SchedulerController` 14/18 lines; `SchedulerService` 7/9 lines |

Happy-path item: Covered. Response fields are asserted.

Optional verification coverage: None documented.

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `authenticate user` | Invalid credentials | Yes | `test_36` | Auth failure coverage |
| `retrieve scheduler details` | Missing or invalid bearer token | Yes | `test_9`, `test_11` assert 401 | Security filter/entry point coverage |
| `retrieve scheduler details` | OpenAPI-declared not-found condition | No | No scheduler 404 test | No source condition visible |

Coverage item summary: Covered items 3/4.

Gap: Singleton scheduler 404 behavior is not covered.

Recommended tests: Define a realizable missing-scheduler condition or remove the contract response; then test it.

### B4: Start scheduler execution

- Business goal: Move the scheduler into active execution.
- Status: Partially Covered.
- Confidence: High.

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `authenticate user` | Obtain `{token}` | Yes | `test_13`, `test_14`, `test_22` log in first | Auth/security coverage present |
| 2 | `start scheduler` | GET `/quartz-manager/scheduler/run` and receive 204 | No | Authenticated start tests assert 500, not 204 | `SchedulerController` and `SchedulerService` executed |

Happy-path item: Not Covered. The documented 204 start transition is never demonstrated.

Optional verification coverage:

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve scheduler details` | Inspect resulting scheduler status | No | No same-test read after successful start |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `authenticate user` | Invalid credentials | Yes | `test_36` | Auth failure coverage |
| `start scheduler` | Missing or invalid bearer token | Yes | `test_31_getOnRunReturns401` | Security coverage |
| `start scheduler` | Scheduler start routine fails internally | Yes | `test_13`, `test_14`, `test_22` assert 500 and path `/scheduler/run` | `SchedulerService` executed |
| `start scheduler` | OpenAPI-declared not-found condition | No | No start 404 test | No source condition visible |

Coverage item summary: Covered items 3/5.

Gap: Success is missing, and internal failure is mapped to 500 rather than a documented domain error.

Recommended tests: Start from a stopped scheduler, call login, call `/scheduler/run`, assert 204, then GET `/scheduler` and assert running/started status.

### B5: Stop scheduler execution

- Business goal: Stop the scheduler from executing scheduled work.
- Status: Partially Covered.
- Confidence: Medium.

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `authenticate user` | Obtain `{token}` | Yes | `test_23`, `test_26` log in first | Auth/security coverage present |
| 2 | `stop scheduler` | GET `/quartz-manager/scheduler/stop` and receive 204 | Yes | `test_23`, `test_26` assert 204 and empty body | Scheduler controller/service coverage present |

Happy-path item: Covered. The required call sequence is executed, though no post-stop state read is made.

Optional verification coverage:

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve scheduler details` | Inspect resulting scheduler status | No | No same-test scheduler detail read after stop |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `authenticate user` | Invalid credentials | Yes | `test_36` | Auth failure coverage |
| `stop scheduler` | Missing or invalid bearer token | Yes | `test_30`, `test_34` assert 401 | Security coverage |
| `stop scheduler` | OpenAPI-declared not-found condition | No | No stop 404 test | No source condition visible |

Coverage item summary: Covered items 3/4.

Gap: 404 and post-stop status are unverified.

Recommended tests: Stop scheduler, assert 204, then retrieve scheduler details and assert the documented stopped state; add a concrete 404 test if the implementation can expose one.

### B6: Pause scheduler execution

- Business goal: Temporarily pause scheduler activity while retaining configuration.
- Status: Partially Covered.
- Confidence: Medium.

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `authenticate user` | Obtain `{token}` | Yes | `test_24`, `test_25`, `test_27` log in first | Auth/security coverage present |
| 2 | `pause scheduler` | GET `/quartz-manager/scheduler/pause` and receive 204 | Yes | `test_24`, `test_25`, `test_27` assert 204 and empty body | Scheduler controller/service coverage present |

Happy-path item: Covered. The required call sequence is executed.

Optional verification coverage:

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve scheduler details` | Inspect resulting scheduler status | No | No same-test scheduler detail read after pause |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `authenticate user` | Invalid credentials | Yes | `test_36` | Auth failure coverage |
| `pause scheduler` | Missing or invalid bearer token | Yes | `test_33_getOnPauseReturns401` | Security coverage |
| `pause scheduler` | OpenAPI-declared not-found condition | No | No pause 404 test | No source condition visible |

Coverage item summary: Covered items 3/4.

Gap: 404 and post-pause status are unverified.

Recommended tests: Pause scheduler, assert 204, then retrieve scheduler details and assert a paused/inactive status if the API exposes it.

### B7: Resume scheduler execution

- Business goal: Resume scheduler activity after pause or inactive state.
- Status: Partially Covered.
- Confidence: High.

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `authenticate user` | Obtain `{token}` | Yes | `test_15`, `test_20`, `test_21` log in first | Auth/security coverage present |
| 2 | `resume scheduler` | GET `/quartz-manager/scheduler/resume` and receive 204 | No | Authenticated resume tests assert 500 | Scheduler controller/service coverage present |

Happy-path item: Not Covered. No test proves successful resume.

Optional verification coverage:

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve scheduler details` | Inspect resulting scheduler status | No | No same-test read after successful resume |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `authenticate user` | Invalid credentials | Yes | `test_36` | Auth failure coverage |
| `resume scheduler` | Missing or invalid bearer token | Yes | `test_32_getOnResumeReturns401` | Security coverage |
| `resume scheduler` | Scheduler resume routine fails internally | Yes | `test_15`, `test_20`, `test_21` assert 500 | `SchedulerService` executed |
| `resume scheduler` | OpenAPI-declared not-found condition | No | No resume 404 test | No source condition visible |

Coverage item summary: Covered items 3/5.

Gap: The documented 204 resume path and resumed state are absent.

Recommended tests: Pause or otherwise prepare the scheduler, call resume, assert 204, then retrieve scheduler details and assert resumed/running status.

### B8: List scheduler triggers

- Business goal: Read the global trigger inventory.
- Status: Partially Covered.
- Confidence: High.

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `authenticate user` | Obtain `{token}` | Yes | `test_0`, `test_1` log in first | Auth/security coverage present |
| 2 | `list triggers` | GET `/quartz-manager/triggers` and receive trigger list | No | Authenticated list tests assert 500 | `TriggerController` and `TriggerService` executed |

Happy-path item: Not Covered. No 200 list response is asserted.

Optional verification coverage: None documented.

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `authenticate user` | Invalid credentials | Yes | `test_36` | Auth failure coverage |
| `list triggers` | Missing or invalid bearer token | Yes | `test_8_getOnTriggersReturns401` | Security coverage |
| `list triggers` | Trigger inventory read fails internally | Yes | `test_0`, `test_1` assert 500 and path `/triggers` | `TriggerService` covered 4/8 lines |
| `list triggers` | OpenAPI-declared not-found condition | No | No trigger-list 404 test | No source condition visible |

Coverage item summary: Covered items 3/5.

Gap: The operational inventory success path is not proven.

Recommended tests: With an empty or seeded trigger store, call `/triggers`, assert 200 and the expected trigger-key array/object shape.

### B9: Schedule a named simple trigger

- Business goal: Create a named simple trigger in the scheduler store.
- Status: Partially Covered.
- Confidence: High.

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `authenticate user` | Obtain `{token}` | Yes | Authenticated POST tests log in first | Auth/security coverage present |
| 2 | `schedule simple trigger` | POST valid JSON `SimpleTriggerInputDTO` to `/simple-triggers/{name}` and receive 201 | No | No successful POST. Tests cover 400, 401, and 415 only | `SimpleTriggerController` low coverage; validators executed |

Happy-path item: Not Covered. No valid create payload is used.

Optional verification coverage:

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve simple trigger by name` | Inspect created trigger | No | No successful POST followed by same-name GET |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `authenticate user` | Invalid credentials | Yes | `test_36` | Auth failure coverage |
| `schedule simple trigger` | Missing or invalid bearer token | Yes | `test_35`, `test_37`, `test_39` assert 401 | Security coverage |
| `schedule simple trigger` | Missing `Content-Type=application/json` | Yes | `test_41`, `test_42`, plus `test_16`, `test_19` assert 415 | Spring/media-type handling covered |
| `schedule simple trigger` | Invalid `SimpleTriggerInputDTO` body | Yes | `test_17` asserts 400 for `{}` | Validator/converter coverage partial |
| `schedule simple trigger` | OpenAPI-declared not-found condition | No | No POST 404 test | No source condition visible |

Coverage item summary: Covered items 4/6.

Gap: No documented success path because a valid DTO payload is absent from tests and the OpenAPI schema is missing.

Recommended tests: Provide a valid `SimpleTriggerInputDTO`, POST to a unique `{name}`, assert 201 and response DTO fields, then GET the same `{name}`.

### B10: Retrieve a named simple trigger

- Business goal: Read the stored configuration for a specific named simple trigger.
- Status: Partially Covered.
- Confidence: Medium.

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `authenticate user` | Obtain `{token}` | Yes | Authenticated simple-trigger GET tests log in first | Auth/security coverage present |
| 2 | `schedule simple trigger` | Establish trigger under `name={name}` | No | POST setup attempts fail with 400 or 415 | `SimpleTriggerService` only 2/16 lines |
| 3 | `retrieve simple trigger by name` | GET same `{name}` and receive trigger DTO | No | GET unknown/uncreated names returns asserted 500 or flaky unasserted 404/500 | `AbstractSchedulerService` and controller partially covered |

Happy-path item: Not Covered. No test creates or seeds a trigger and then retrieves the same name successfully.

Optional verification coverage: None documented.

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `authenticate user` | Invalid credentials | Yes | `test_36` | Auth failure coverage |
| `schedule simple trigger` | Setup body missing/wrong/invalid | Yes | `test_16`, `test_17`, `test_19` assert setup 415 or 400 | Media/validation coverage partial |
| `retrieve simple trigger by name` | Missing or invalid bearer token | Yes | `test_29_getOnSimple_triggReturns401` | Security coverage |
| `retrieve simple trigger by name` | No trigger exists under `{name}` | Yes, with discrepancy | `test_12`, `test_16`, `test_17`, `test_19` assert 500; `test_40` comments 404 vs 500 but does not execute status assertion | `AbstractSchedulerService` covered; `TriggerNotFoundException` not covered |
| `retrieve simple trigger by name` | GET uses `{otherName}` after setup created `{name}` | No | No successful setup plus mismatched GET path | Not corroborated |

Coverage item summary: Covered items 4/6.

Gap: The actual documented 404 behavior and successful same-name retrieval are not covered.

Recommended tests: Create a valid simple trigger named `n`, GET `/simple-triggers/n`, assert 200 and persisted fields. Separately, GET a known-missing name and assert the documented 404 error DTO.

### B11: Reschedule a named simple trigger

- Business goal: Replace the timing or configuration of an existing named simple trigger.
- Status: Partially Covered.
- Confidence: Medium.

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `authenticate user` | Obtain `{token}` | Yes | Authenticated PUT tests log in first | Auth/security coverage present |
| 2 | `schedule simple trigger` | Establish existing trigger under `name={name}` | No | No valid setup POST exists | `SimpleTriggerService` low coverage |
| 3 | `reschedule simple trigger` | PUT valid replacement DTO to same `{name}` and receive 200 | No | PUT tests assert 400, 401, or 415 only | `SimpleTriggerController` 2/5 methods; `SimpleTriggerService` 1/4 methods |

Happy-path item: Not Covered. No successful create-then-update workflow is present.

Optional verification coverage:

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve simple trigger by name` | Inspect updated trigger | No | No successful PUT followed by same-name GET |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `authenticate user` | Invalid credentials | Yes | `test_36` | Auth failure coverage |
| `schedule simple trigger` | Setup body invalid or request not JSON | Yes | POST 400/415 setup failures in `test_16`, `test_17`, `test_19` | Media/validation coverage partial |
| `reschedule simple trigger` | Missing or invalid bearer token | Yes | `test_38`, `test_39` assert 401 | Security coverage |
| `reschedule simple trigger` | No simple trigger exists under `{name}` | No | No valid replacement body isolates missing-target behavior | `TriggerNotFoundException` not covered |
| `reschedule simple trigger` | Missing `Content-Type=application/json` | Yes | `test_43`, `test_44` assert 415 | Media handling covered |
| `reschedule simple trigger` | Invalid replacement DTO | Yes | `test_18`, `test_28` assert 400 for `{}` | Validator/converter coverage partial |
| `reschedule simple trigger` | PUT uses `{otherName}` after setup created `{name}` | No | No successful setup plus mismatched PUT | Not corroborated |
| `reschedule simple trigger` | Internal processing fails during PUT | No | No executable PUT 500 found in the generated tests | Not corroborated |

Coverage item summary: Covered items 5/9.

Gap: Positive update behavior and several identity/not-found branches are missing.

Recommended tests: Create trigger `n` with valid initial DTO, PUT a valid replacement DTO to `/simple-triggers/n`, assert 200 and changed schedule fields, then GET `n` to verify persistence. Add separate tests for missing target and name mismatch.

## Cross-Behavior Gaps

- Successful trigger lifecycle coverage is absent. There is no valid POST, no successful GET of an existing trigger, and no successful PUT update.
- Scheduler start and resume are executed only as 500 failures, not as documented 204 transitions.
- Generic 404 cases are broadly missing. The simple-trigger lookup 404 is explicitly flaky in `test_40` because the executable status assertion is commented out.
- Tests do not stitch workflows across isolated cases, correctly, because every test resets SUT state. This means POST failures in one test cannot establish preconditions for later GET or PUT behavior.
- Direct database setup is not used. That is fine for preconditions, but no equivalent fixture establishes existing trigger state for retrieve/reschedule.
- Many endpoint lines are covered without behavior-level confidence. For example, `SimpleTriggerController`, converters, and validators are touched by 400/415 paths but not by successful business creation/update.
- Optional read-after-write/read-after-transition checks are missing for stop, pause, start, resume, schedule, and reschedule.
- The missing `SimpleTriggerInputDTO` schema prevents generated tests from discovering a valid create/update payload, which directly blocks behavior-level trigger coverage.

## Suggested Additional Tests

| Priority | Behavior ID | Test Intent | Minimal Setup | Calls / Operations | Required Assertions | Coverage Type |
|---:|---|---|---|---|---|---|
| 1 | B9, B10, B11 | Full simple-trigger lifecycle | Valid user and valid `SimpleTriggerInputDTO` | Login, POST `/simple-triggers/{name}`, GET same name, PUT replacement, GET same name | 201 create, 200 read/update, fields match initial then replacement state | Success |
| 2 | B4 | Successful scheduler start | Scheduler configured in stopped/inactive state | Login, GET `/scheduler/run`, GET `/scheduler` | 204 start, scheduler status running/started | Success |
| 3 | B7 | Successful scheduler resume | Scheduler paused or inactive | Login, optionally pause, GET `/scheduler/resume`, GET `/scheduler` | 204 resume, scheduler status active/running | Success |
| 4 | B8 | Successful trigger inventory read | Empty or seeded trigger store | Login, GET `/triggers` | 200 and expected empty/listed trigger-key response | Success |
| 5 | B10 | Reliable missing-trigger response | Authenticated caller; ensure no trigger with name | Login, GET `/simple-triggers/{missing}` | Documented 404 and error DTO, not 500 | Failure |
| 6 | B11 | Reschedule missing trigger | Authenticated caller; valid replacement DTO; no target trigger | Login, PUT `/simple-triggers/{missing}` | Documented 404, no trigger created | Failure |
| 7 | B2-B8 | Clarify singleton/generic 404 cases | Defined way to simulate missing singleton/resource | Call each documented endpoint under missing-resource state | Contract-aligned 404 or remove unreachable contract response | Regression |
| 8 | B5, B6 | Verify lifecycle side effects | Running scheduler where possible | Login, stop/pause, then GET `/scheduler` | Status reflects stopped/paused state | Success |
| 9 | B1 | Explicit login success contract | Valid credentials | POST `/auth/login` | 200, nonblank `accessToken`, usable bearer header | Regression |

## Appendix: Coverage Artifacts Used

### JaCoCo XML Files

- `/Users/yangyuhan/behavior-analyze/quartz-manager/reports/report.xml`
  - Extracted counters: line 322/598 (53.8%), branch 36/122 (29.5%), method 119/194 (61.3%), class 46/58 (79.3%).
  - Relevant class signals: `JobController` 100% line/method; `SchedulerController` 77.8% line and 100% method; `TriggerController` 80.0% line and 66.7% method; `SimpleTriggerController` 19.0% line and 40.0% method; `SimpleTriggerService` 12.5% line and 25.0% method.
  - Limitation: XML references controller/service/source files not present under the current local `src/main/java` checkout. It is valid as JaCoCo evidence for the executed SUT, but source-level interpretation is limited by the missing local implementation files.

### JaCoCo CSV Files

- `/Users/yangyuhan/behavior-analyze/quartz-manager/reports/report.csv`
  - Used as a per-class cross-check for line, branch, method, and class coverage.
  - Not summed separately because the XML already provides the aggregate report for the same SUT run.

## Appendix: Test Inventory

Most authenticated tests include setup login via `POST /quartz-manager/auth/login` and reuse `Authorization: Bearer {token}`. The operations below list the business operation under test.

| Test File | Test Case | Operations | Assertions | Related Behavior IDs |
|---|---|---|---|---|
| `EM_quartz_manager_True_25_false_false_SPECIFIED_false_0_Test.java` | `test_0_getOnQuartz_managerTriggersCauses500_internalServerError` | GET `/quartz-manager/triggers` | 500, JSON error/path | B8 |
| same | `test_1_getOnQuartz_managerTriggersWithQueryParamsCauses500_internalServerError` | GET `/quartz-manager/triggers` with extra query params | 500, JSON error/path | B8 |
| same | `test_2_getOnQuartz_managerSchedulerWithQueryParamReturnsObject` | GET `/quartz-manager/scheduler` | 200, scheduler name/instance/status/triggerKeys | B3 |
| same | `test_3_getOnQuartz_managerJobsReturnsEmptyList` | GET `/quartz-manager/jobs` | 200, JSON array size 0 | B2 |
| same | `test_4_getOnQuartz_managerJobsWithQueryParamsReturnsEmptyList` | GET `/quartz-manager/jobs` with extra query params | 200, JSON array size 0 | B2 |
| same | `test_5_getOnQuartz_managerSchedulerWithQueryParamsReturnsObject` | GET `/quartz-manager/scheduler` with extra query params | 200, scheduler fields | B3 |
| same | `test_6_getOnQuartz_managerJobsWithQueryParamReturnsEmptyList` | GET `/quartz-manager/jobs` with extra query param | 200, JSON array size 0 | B2 |
| same | `test_7_getOnApi_docsReturnsObject` | GET `/v3/api-docs` | 200 only | None |
| same | `test_8_getOnTriggersReturns401` | GET `/quartz-manager/triggers` without bearer | 401, empty body | B8 |
| same | `test_9_getOnQuartz_managerSchedulerReturns401` | GET `/quartz-manager/scheduler` without bearer | 401, empty body | B3 |
| same | `test_10_getOnJobsReturns401` | GET `/quartz-manager/jobs` without bearer | 401, empty body | B2 |
| same | `test_11_getOnQuartz_managerSchedulerWithQueryParamReturns401` | GET `/quartz-manager/scheduler` without bearer | 401, empty body | B3 |
| same | `test_12_getOnSimple_triggCauses500_internalServerError` | GET missing `/quartz-manager/simple-triggers/{name}` | 500, JSON error/path | B10 |
| same | `test_13_getOnSchedulerRunWithQueryParamCauses500_internalServerError` | GET `/quartz-manager/scheduler/run` | 500, JSON error/path | B4 |
| same | `test_14_getOnSchedulerRunCauses500_internalServerError` | GET `/quartz-manager/scheduler/run` | 500, JSON error/path | B4 |
| same | `test_15_getOnSchedulerResumeWithQueryParamCauses500_internalServerError` | GET `/quartz-manager/scheduler/resume` | 500, JSON error/path | B7 |
| same | `test_16_getOnQuartz_managerSimple_triggWithQueryParamsCauses500_internalServerError` | POST simple trigger without JSON type, then GET same name | 415 then 500 | B9, B10 |
| same | `test_17_getOnQuartz_managerSimple_triggCauses500_internalServerError` | POST simple trigger with `{}`, then GET same name | 400 then 500 | B9, B10 |
| same | `test_18_getOnSimple_triggCauses500_internalServerError` | PUT simple trigger with `{}`, then GET same name | 400 then 500 | B11, B10 |
| same | `test_19_getOnSimple_triggCauses500_internalServerError` | POST simple trigger without JSON type, then GET same name | 415 then 500 | B9, B10 |
| same | `test_20_getOnResumeCauses500_internalServerError` | GET `/quartz-manager/scheduler/resume` | 500, JSON error/path | B7 |
| same | `test_21_getOnResumeCauses500_internalServerError` | GET `/quartz-manager/scheduler/resume` | 500, JSON error/path | B7 |
| same | `test_22_getOnSchedulerRunWithQueryParamsCauses500_internalServerError` | GET `/quartz-manager/scheduler/run` | 500, JSON error/path | B4 |
| same | `test_23_getOnSchedulerStopWithQueryParamReturns204` | GET `/quartz-manager/scheduler/stop` | 204, empty body | B5 |
| same | `test_24_getOnSchedulerPauseReturns204` | GET `/quartz-manager/scheduler/pause` | 204, empty body | B6 |
| same | `test_25_getOnPauseReturns204` | GET `/quartz-manager/scheduler/pause` | 204, empty body | B6 |
| same | `test_26_getOnSchedulerStopWithQueryParamsReturns204` | GET `/quartz-manager/scheduler/stop` | 204, empty body | B5 |
| same | `test_27_getOnPauseReturns204` | GET `/quartz-manager/scheduler/pause` | 204, empty body | B6 |
| same | `test_28_putOnSimple_triggReturns400` | PUT simple trigger with `{}` | 400, JSON error/path | B11 |
| same | `test_29_getOnSimple_triggReturns401` | GET simple trigger without bearer | 401, empty body | B10 |
| same | `test_30_getOnStopReturns401` | GET `/quartz-manager/scheduler/stop` without bearer | 401, empty body | B5 |
| same | `test_31_getOnRunReturns401` | GET `/quartz-manager/scheduler/run` without bearer | 401, empty body | B4 |
| same | `test_32_getOnResumeReturns401` | GET `/quartz-manager/scheduler/resume` without bearer | 401, empty body | B7 |
| same | `test_33_getOnPauseReturns401` | GET `/quartz-manager/scheduler/pause` without bearer | 401, empty body | B6 |
| same | `test_34_getOnStopReturns401` | GET `/quartz-manager/scheduler/stop` without bearer | 401, empty body | B5 |
| same | `test_35_postOnSimple_triggReturns401` | POST simple trigger without bearer | 401, empty body | B9 |
| same | `test_36_postOnLoginReturns401` | POST login with empty form | 401, JSON error/path | B1 |
| same | `test_37_postOnSimple_triggReturns401` | POST simple trigger without bearer | 401, empty body | B9 |
| same | `test_38_putOnSimple_triggReturns401` | PUT simple trigger without bearer | 401, empty body | B11 |
| same | `test_39_putOnSimple_triggReturns401` | POST then PUT simple trigger without bearer | 401 responses | B9, B11 |
| same | `test_40_getOnSimple_triggReturns404` | GET missing simple trigger | No executable status assertion; comments note flaky 404 vs 500 | B10 |
| same | `test_41_postOnSimple_triggReturns415` | POST simple trigger without JSON type | 415, JSON error/path | B9 |
| same | `test_42_postOnSimple_triggReturns415` | POST simple trigger without JSON type | 415, JSON error/path | B9 |
| same | `test_43_putOnQuartz_managerSimple_triggWithQueryParamReturns415` | PUT simple trigger without JSON type | 415, JSON error/path | B11 |
| same | `test_44_putOnQuartz_managerSimple_triggReturns415` | PUT simple trigger without JSON type | 415, JSON error/path | B11 |
