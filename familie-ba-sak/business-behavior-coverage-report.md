# Business Behavior Coverage Report

## Executive Summary

- Project under analysis: `/Users/qingchen/Downloads/microservice_subset/behavior-analyze/familie-ba-sak`
- Business behavior specification: `/Users/qingchen/Downloads/microservice_subset/behavior-analyze/familie-ba-sak/business-behavior.md`
- Function behavior inventory: `/Users/qingchen/Downloads/microservice_subset/behavior-analyze/familie-ba-sak/full-behavior.md`
- Test root analyzed: `/Users/qingchen/Downloads/microservice_subset/behavior-analyze/familie-ba-sak/tests`
- Total test files analyzed: `3`
- Total test cases analyzed: `485`
- JaCoCo XML reports used: `/Users/qingchen/Downloads/microservice_subset/behavior-analyze/familie-ba-sak/reports/report.xml`
- JaCoCo CSV reports used: `/Users/qingchen/Downloads/microservice_subset/behavior-analyze/familie-ba-sak/reports/report.csv`
- Source roots analyzed: `/Users/qingchen/Downloads/microservice_subset/behavior-analyze/familie-ba-sak/src/main/kotlin`
- Total documented business behaviors: `90`
- Covered: `0`
- Partially covered: `22`
- Not covered: `68`
- Unclear: `0`
- Business behavior coverage: `12.2%`
- Happy-path coverage: `4.4%` (`4/90`)
- Failure/exceptional-case coverage: `14.9%` (`18/121`)
- Behavior checklist coverage: `22/211` (`10.4%`)
- JaCoCo coverage signal from XML: line `5234/29081` (`18.0%`), branch `178/11102` (`1.6%`), method `1797/8965` (`20.0%`), class `932/2242` (`41.6%`). CSV cross-check gives line `5756/29888` (`19.3%`), branch `178/11102` (`1.6%`), method `1797/8965` (`20.0%`); CSV has no class counters.

- The generated suite is broad at endpoint discovery level, but it rarely builds documented domain state or executes multi-step workflows with reused identifiers.
- No documented behavior is fully covered because every behavior has at least one documented failure case and no behavior has complete happy-path plus complete failure coverage.
- Strongest evidence is for feature-toggle lookup, two statistics reads, background call-id job triggers, empty collaborator search validation, invalid long-deadline date validation, and several missing-state failure branches.
- The biggest gap is the core case/treatment lifecycle: case creation/idempotency, treatment creation/restart, step flow through decision, wait/resume, condition assessment, EEA records, letters, complaint flows, journal/task workflows, and administrative repair jobs are not exercised as business workflows.
- JaCoCo corroborates shallow exploration: branch coverage is only `1.6%`, and major workflow packages such as fagsak, behandling, brev, EEA, vedtak, and journalføring have low branch coverage.

## Test Corpus Summary

| Area | Count / Summary |
|---|---|
| Test files analyzed | `3` |
| Test cases analyzed | `485` |
| Primary test framework | JUnit 5, RestAssured, EvoMaster-generated black-box HTTP tests |
| Main endpoints/functions exercised | 371 unique HTTP method/path strings, mostly single-call probes across fagsak, behandling, person, task, statistics, forvalter, satsendring, document, EEA, Infotrygd, Skatt, and feature endpoints |
| Positive-path tests | `46` test cases with at least one 2xx status |
| Negative/failure tests | `439` test cases with at least one 4xx/5xx status |
| Tests with business assertions | `4` strong business-output assertions: feature toggles, internal statistics, application statistics, and static condition explanations. Persisted state/read-after-write assertions: `0`. |
| Tests with only status/code assertions | `121`; `364` include some response-body assertions, usually envelope/error fields rather than domain state. |

| Test File | Test Cases | Main Behavior Area | Evidence Quality |
|---|---:|---|---|
| `tests/EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java` | 200 | Broad endpoint probes; some statistics, fagsak read failures, document/log/timeline failures, invalid long-deadline date | Low |
| `tests/EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java` | 200 | Feature toggle, statistics publishing endpoints, task/search/admin endpoints, collaborator validation, Infotrygd failures | Low |
| `tests/EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java` | 85 | API docs and authorization/security denial probes | Low |

## Coverage Matrix

| ID | Business Behavior | Happy Path | Failure Cases | Optional Verification | Status | Confidence | Main Gap |
|---|---|---|---|---|---|---|---|
| B1 | Establish a case and reuse the case identity | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B2 | Inspect case views and locate cases by person | Not Covered | 0/2 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B3 | Resolve case participation and ongoing benefit relationships | Not Covered | 1/2 covered | Not executed | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B4 | Create and check manual repayment treatment on a case | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B5 | Create a treatment and restart an early active treatment | Not Covered | 0/3 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B6 | Queue automated birth-event treatment processing | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B7 | Change treatment theme | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B8 | Execute the caseworker treatment step flow through decision | Not Covered | 0/3 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B9 | Dismiss an active treatment | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B10 | Register institution and guardian information on treatment | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B11 | Add a child to treatment basis and reset later treatment steps | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B12 | Put treatment on wait, update wait, and resume | Not Covered | 0/4 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B13 | Read person information for case handling | Not Covered | 1/1 covered | None | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B14 | Refresh treatment register basis and manually record death | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B15 | Maintain condition assessment records | Not Covered | 0/3 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B16 | Maintain EEA competence intervals | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B17 | Maintain foreign period amounts | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B18 | Update existing currency rate from ECB | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B19 | Set historical ISK currency rate manually and delete currency rate | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B20 | Maintain changed payment shares and reset treatment result | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B21 | Inspect EEA timelines | Not Covered | 1/1 covered | None | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B22 | Maintain EEA refund periods | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B23 | Maintain overpaid currency periods | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B24 | Activate and deactivate corrected decision metadata | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B25 | Activate, list, and deactivate corrected after-payment metadata | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B26 | Add and remove small child supplement correction | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B27 | Preview repayment warning letter | Not Covered | 1/1 covered | None | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B28 | Generate and retrieve decision letter | Not Covered | 1/2 covered | Not executed | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B29 | Preview and send manual treatment letter | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B30 | Preview and send manual case letter | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B31 | Maintain manual letter recipients | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B32 | Edit decision periods and regenerate letter explanations | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B33 | Retrieve treatment log | Not Covered | 1/1 covered | None | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B34 | Retrieve external benefit data for BISYS | Not Covered | 0/2 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B35 | Retrieve pension child benefit | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B36 | Order pension yearly export | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B37 | Production tax data export | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B38 | Tax test endpoint data retrieval | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B39 | Retrieve Infotrygd case and benefit context | Not Covered | 1/1 covered | None | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B40 | Discover collaborators by search and organization number | Not Covered | 1/2 covered | None | Partially Covered | Medium | Happy workflow missing; only failure evidence exists. |
| B41 | Create and list complaint treatments for a case | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B42 | Let complaint system create a revision after precheck | Not Covered | 0/2 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B43 | Retrieve decisions for complaint system | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B44 | Search external tasks | Not Covered | 1/1 covered | None | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B45 | Assign external task | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B46 | Reset external task assignment | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B47 | Retrieve journaling task data | Not Covered | 1/1 covered | None | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B48 | Complete external task | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B49 | Complete task while linking a journalpost | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B50 | Retrieve open extended-benefit deadlines | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B51 | Clear application task ownership | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B52 | Inspect journalpost and retrieve documents | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B53 | Journal an incoming journalpost | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B54 | Retrieve feature toggles | Covered | 0/1 covered | None | Partially Covered | Medium | Documented failure branch missing. |
| B55 | Check person access | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B56 | Queue identity event handling | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B57 | Queue transitional-benefit event handling | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B58 | Check rate-change eligibility for one case | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B59 | Queue rate change for one case | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B60 | Queue rate change for multiple cases | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B61 | Run synchronous rate change for one case | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B62 | Queue rate change from identities | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B63 | Queue technical dismissal for long-deadline treatments | Not Covered | 1/1 covered | Not executed | Partially Covered | Medium | Happy workflow missing; only failure evidence exists. |
| B64 | Identify ongoing cases without latest rate | Covered | 0/1 covered | None | Partially Covered | Medium | Documented failure branch missing. |
| B65 | Run consistency reconciliation dry run | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B66 | Run real consistency reconciliation | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B67 | Retrieve internal and application statistics | Covered | 0/1 covered | None | Partially Covered | Medium | Documented failure branch missing. |
| B68 | Retrieve treatment statistics payload | Not Covered | 1/1 covered | None | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B69 | Retrieve case statistics payload | Not Covered | 1/1 covered | None | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B70 | Register statistics message as sent | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B71 | Retrieve benefit statistics decisions | Not Covered | 1/1 covered | None | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B72 | Queue unsent benefit statistics | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B73 | Manually queue benefit statistics | Not Covered | 1/1 covered | Not executed | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B74 | Resend manual migration statistics | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B75 | Complete an administrative task list with partial success | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B76 | Restart small child supplement job | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B77 | Send payment orders administratively | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B78 | Bulk corrected payment-order resend | Not Covered | 1/1 covered | Not executed | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B79 | Single-version corrected payment-order resend | Not Covered | 1/1 covered | Not executed | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B80 | Run unvalidated rate change administratively | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B81 | Identify payments over 100 percent | Covered | 0/1 covered | None | Partially Covered | Medium | Documented failure branch missing. |
| B82 | Find payment-order issue candidates | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B83 | Check incorrect cessation dates for selected treatments | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B84 | Populate support dates for one treatment | Not Covered | 1/1 covered | Not executed | Partially Covered | Low | Happy workflow missing; only failure evidence exists. |
| B85 | Populate support dates in bulk | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B86 | Find cases to close | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B87 | Update case ongoing status | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B88 | Find migration duplicates with ongoing Infotrygd case | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B89 | Find migration duplicates | Not Covered | 0/1 covered | None | Not Covered | Medium | No complete workflow or documented failure coverage. |
| B90 | Fill empty condition start dates in preprod | Not Covered | 0/1 covered | Not executed | Not Covered | Medium | No complete workflow or documented failure coverage. |

## Behavior Details

Each behavior below is scored against the documented business workflow, not against endpoint execution alone. Direct database reset before each generated test prevents stitching stateful workflow steps from separate tests.

### B1: Establish a case and reuse the case identity
- Business goal: Create the domain parent case for later treatments, letters, complaints, repayment checks, and statistics.
- Required workflow: `create case`; `return existing case`
- Optional verification: `retrieve full case`; `retrieve minimal case`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B2: Inspect case views and locate cases by person
- Business goal: Read case state by known `fagsakId` and by person/type lookup.
- Required workflow: `retrieve full case`; `retrieve minimal case`; `find minimal case for person`; `find all minimal cases for person`
- Optional verification: None
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B3: Resolve case participation and ongoing benefit relationships
- Business goal: Determine where a person participates in child-benefit cases and whether the person has ongoing benefit.
- Required workflow: `search case participants`; `resolve case participants`; `search cases where person participates`; `search cases with ongoing benefit for person`
- Optional verification: `retrieve full case`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `resolve case participants`: Test_1.test_123 calls `/api/fagsaker/sok/fagsakdeltagere` and asserts PDL lookup failure.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B4: Create and check manual repayment treatment on a case
- Business goal: Create a repayment treatment for a case and inspect whether open repayment state exists.
- Required workflow: `create repayment treatment`
- Optional verification: `check open repayment case`; `retrieve full case`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B5: Create a treatment and restart an early active treatment
- Business goal: Start a treatment on a case, then reuse and reset it when another creation request arrives before decision stage.
- Required workflow: `create treatment`; `restart active early treatment`
- Optional verification: `retrieve treatment`
- Failure cases documented: `3`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B6: Queue automated birth-event treatment processing
- Business goal: Accept a birth event and queue asynchronous treatment processing.
- Required workflow: `queue treatment from birth event`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B7: Change treatment theme
- Business goal: Change treatment category and subcategory while treatment is editable.
- Required workflow: `change treatment theme`
- Optional verification: `retrieve treatment`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B8: Execute the caseworker treatment step flow through decision
- Business goal: Move a treatment from application registration through result, repayment assessment, decision-maker handoff, and decision implementation.
- Required workflow: `register application`; `validate conditions`; `derive treatment result`; `assess repayment`; `send to decision maker`; `decide treatment`
- Optional verification: `validate treatment result`; `retrieve treatment`; `list decision periods`
- Failure cases documented: `3`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B9: Dismiss an active treatment
- Business goal: Close a treatment by dismissal rather than full decision implementation.
- Required workflow: `dismiss treatment`
- Optional verification: `retrieve treatment`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B10: Register institution and guardian information on treatment
- Business goal: Store institution and/or guardian information required by the treatment flow.
- Required workflow: `register institution and guardian`
- Optional verification: `retrieve treatment`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B11: Add a child to treatment basis and reset later treatment steps
- Business goal: Add a child into the treatment person basis after treatment creation.
- Required workflow: `add child to basis`
- Optional verification: `retrieve treatment`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B12: Put treatment on wait, update wait, and resume
- Business goal: Pause a treatment with a deadline/reason, change the wait metadata, then resume the treatment.
- Required workflow: `set treatment on wait`; `update wait`; `resume treatment`
- Optional verification: `retrieve treatment`
- Failure cases documented: `4`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B13: Read person information for case handling
- Business goal: Retrieve detailed, simple, and address-focused person information for casework.
- Required workflow: `retrieve full person information`; `retrieve simple person information`; `retrieve person address`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `retrieve full person information`: Test_0.test_4 calls `/api/person` and asserts upstream PDL lookup failure.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B14: Refresh treatment register basis and manually record death
- Business goal: Update treatment person basis from register information and record manual death details for a person in the treatment.
- Required workflow: `refresh register information`; `register manual death`
- Optional verification: `retrieve treatment`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B15: Maintain condition assessment records
- Business goal: Add, update, delete, and supplement condition assessment data on an editable treatment.
- Required workflow: `add condition`; `update condition`; `delete condition period`; `add condition`; `delete condition`; `update other assessment`
- Optional verification: `retrieve treatment`; `list condition explanation texts`
- Failure cases documented: `3`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B16: Maintain EEA competence intervals
- Business goal: Create/replace competence periods and delete an existing competence interval.
- Required workflow: `upsert competence interval`; `delete competence interval`
- Optional verification: `retrieve treatment`; `retrieve EØS timelines`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B17: Maintain foreign period amounts
- Business goal: Update and delete an existing foreign benefit amount period.
- Required workflow: `update foreign period amount`; `delete foreign period amount`
- Optional verification: `retrieve treatment`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B18: Update existing currency rate from ECB
- Business goal: Update an existing currency-rate period by fetching a rate from ECB when currency/date changes.
- Required workflow: `update currency rate from ECB`
- Optional verification: `retrieve treatment`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B19: Set historical ISK currency rate manually and delete currency rate
- Business goal: Store a manually supplied historical ISK rate, then remove the currency-rate row.
- Required workflow: `set historical ISK rate manually`; `delete currency rate`
- Optional verification: `retrieve treatment`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B20: Maintain changed payment shares and reset treatment result
- Business goal: Create, fill, remove, and explicitly reset changed-payment share state in an editable treatment.
- Required workflow: `create changed payment share`; `update changed payment share`; `delete changed payment share`; `reset treatment to treatment result`
- Optional verification: `retrieve treatment`; `find invalid after-payment periods`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B21: Inspect EEA timelines
- Business goal: Read calculated EEA timelines for a treatment.
- Required workflow: `retrieve EØS timelines`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `retrieve EØS timelines`: Test_0.test_15 calls `/api/tidslinjer/304` and asserts missing treatment.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B22: Maintain EEA refund periods
- Business goal: Add, list, update, and delete refund periods for EEA handling.
- Required workflow: `add EØS refund period`; `list EØS refund periods`; `update EØS refund period`; `delete EØS refund period`
- Optional verification: `retrieve treatment`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B23: Maintain overpaid currency periods
- Business goal: Add, list, update, and delete periods with overpaid currency amount.
- Required workflow: `add overpaid currency period`; `list overpaid currency periods`; `update overpaid currency period`; `delete overpaid currency period`
- Optional verification: `retrieve treatment`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B24: Activate and deactivate corrected decision metadata
- Business goal: Mark a treatment as having corrected decision metadata, then deactivate that metadata.
- Required workflow: `create corrected decision metadata`; `deactivate corrected decision metadata`
- Optional verification: `retrieve treatment`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B25: Activate, list, and deactivate corrected after-payment metadata
- Business goal: Mark corrected after-payment metadata active, inspect all records, and deactivate the active correction.
- Required workflow: `create corrected after-payment metadata`; `list corrected after-payment metadata`; `deactivate corrected after-payment metadata`
- Optional verification: `retrieve treatment`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B26: Add and remove small child supplement correction
- Business goal: Add a small-child supplement correction for a month and remove it later.
- Required workflow: `add small child supplement correction`; `remove small child supplement correction`
- Optional verification: `retrieve treatment`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B27: Preview repayment warning letter
- Business goal: Generate a repayment warning letter preview without sending it.
- Required workflow: `preview repayment warning letter`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `preview repayment warning letter`: Test_0.test_65 calls `/api/tilbakekreving/27/forhandsvis-varselbrev` and asserts missing treatment.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B28: Generate and retrieve decision letter
- Business goal: Generate the persisted decision letter for an active decision and retrieve it.
- Required workflow: `generate decision letter`; `retrieve decision letter`
- Optional verification: `retrieve treatment`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `generate decision letter`: Test_0.test_53 calls `/api/dokument/vedtaksbrev/528` and asserts missing Vedtak.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B29: Preview and send manual treatment letter
- Business goal: Preview and send a manual letter tied to a treatment.
- Required workflow: `preview treatment letter`; `send treatment letter`
- Optional verification: `retrieve treatment log`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B30: Preview and send manual case letter
- Business goal: Preview and send a manual letter tied directly to a case.
- Required workflow: `preview case letter`; `send case letter`
- Optional verification: `retrieve full case`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B31: Maintain manual letter recipients
- Business goal: Add, list, and remove manual letter recipients for a treatment.
- Required workflow: `add letter recipient`; `list letter recipients`; `delete letter recipient`
- Optional verification: `retrieve treatment log`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B32: Edit decision periods and regenerate letter explanations
- Business goal: Modify decision-period explanations, override change date, and generate final letter explanation texts.
- Required workflow: `list decision periods`; `update standard explanations`; `update decision free texts`; `regenerate decision periods`; `list decision periods`; `generate letter explanation texts`
- Optional verification: `get change date`; `generate decision letter`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B33: Retrieve treatment log
- Business goal: Read audit/log entries for a treatment.
- Required workflow: `retrieve treatment log`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `retrieve treatment log`: Test_0.test_21 calls `/api/logg/295` and asserts invalid treatment id.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B34: Retrieve external benefit data for BISYS
- Business goal: Provide BISYS with extended child benefit and small child supplement periods for a person.
- Required workflow: `retrieve BISYS extended benefit`
- Optional verification: None
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B35: Retrieve pension child benefit
- Business goal: Provide Pension with child-benefit case and period data for one person.
- Required workflow: `retrieve pension child benefit`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B36: Order pension yearly export
- Business goal: Queue export of persons with child benefit for a Pension tax/reporting year.
- Required workflow: `order pension yearly export`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B37: Production tax data export
- Business goal: Return Skatteetaten production person and period data for one tax year.
- Required workflow: `list tax persons`; `retrieve tax periods`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B38: Tax test endpoint data retrieval
- Business goal: Return Skatteetaten test-path person and period data for one tax year.
- Required workflow: `list tax persons test`; `retrieve tax periods test`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B39: Retrieve Infotrygd case and benefit context
- Business goal: Read legacy Infotrygd case, benefit, and ongoing-state information for an applicant.
- Required workflow: `retrieve Infotrygd cases`; `retrieve Infotrygd benefits`; `check ongoing Infotrygd case`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `retrieve Infotrygd cases`: Test_1.test_29 calls `/api/infotrygd/hent-infotrygdsaker-for-soker` and asserts upstream failure.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B40: Discover collaborators by search and organization number
- Business goal: Find collaborator/institution information and retrieve details by organization number.
- Required workflow: `search collaborator`; `retrieve collaborator by organization`
- Optional verification: None
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `search collaborator`: Test_1.test_50 posts `{}` to `/api/samhandler/navn` and asserts the required-search-variable functional error.
- Status: `Partially Covered`. Confidence: `Medium`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B41: Create and list complaint treatments for a case
- Business goal: Start a complaint treatment for a child-benefit case and list complaint treatments.
- Required workflow: `create complaint treatment`; `list complaint treatments`
- Optional verification: `retrieve full case`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B42: Let complaint system create a revision after precheck
- Business goal: Let the external complaint system check and create a complaint-triggered revision on a case.
- Required workflow: `check complaint revision creation`; `create complaint revision`
- Optional verification: `retrieve complaint decisions`
- Failure cases documented: `2`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B43: Retrieve decisions for complaint system
- Business goal: Provide fagsystem decisions for a case to the complaint system.
- Required workflow: `retrieve complaint decisions`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B44: Search external tasks
- Business goal: Find external task ids that can be acted on by later task workflows.
- Required workflow: `search tasks`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `search tasks`: Test_1.test_25 calls `/api/oppgave/hent-oppgaver` and asserts task-service failure.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B45: Assign external task
- Business goal: Assign a known task to a caseworker.
- Required workflow: `search tasks`; `assign task`
- Optional verification: `retrieve journaling task data`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B46: Reset external task assignment
- Business goal: Clear assignment on a known external task.
- Required workflow: `search tasks`; `reset task assignment`
- Optional verification: `retrieve journaling task data`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B47: Retrieve journaling task data
- Business goal: Gather task, person, minimal case, and optional journalpost context for manual journaling.
- Required workflow: `retrieve journaling task data`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `retrieve journaling task data`: Test_0.test_20 calls `/api/oppgave/925` and asserts upstream load failure.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B48: Complete external task
- Business goal: Close a known external task without linking a journalpost.
- Required workflow: `search tasks`; `complete task`
- Optional verification: `search tasks`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B49: Complete task while linking a journalpost
- Business goal: Link a journalpost to a case/treatment context and complete the related task.
- Required workflow: `complete task and link journalpost`
- Optional verification: `retrieve journaling task data`; `retrieve journalpost`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B50: Retrieve open extended-benefit deadlines
- Business goal: Report deadlines for open extended child-benefit treatments.
- Required workflow: `retrieve open treatment deadlines`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B51: Clear application task ownership
- Business goal: Remove `behandlesAvApplikasjon` ownership markers from selected tasks.
- Required workflow: `clear application task ownership`
- Optional verification: `retrieve journaling task data`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B52: Inspect journalpost and retrieve documents
- Business goal: Read journalpost metadata and fetch documents in resource/PDF form.
- Required workflow: `retrieve journalpost`; `retrieve journal document resource`; `retrieve journal document PDF`
- Optional verification: `list user journalposts`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B53: Journal an incoming journalpost
- Business goal: Journal an incoming journalpost to the correct unit/task context.
- Required workflow: `journal journalpost`
- Optional verification: `retrieve journalpost`; `retrieve journaling task data`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B54: Retrieve feature toggles
- Business goal: Return enabled/disabled state for requested feature toggles.
- Required workflow: `retrieve feature toggles`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: `POST /api/feature` in Test_1.test_4 returns `SUKSESS` and maps requested toggle ids to booleans.
- Failure evidence: No documented failure case covered.
- Status: `Partially Covered`. Confidence: `Medium`.
- Gap: add targeted tests for all remaining documented failure/exceptional branches and, where applicable, state verification.

### B55: Check person access
- Business goal: Determine whether the current caller may access a person and see the person’s discretion code.
- Required workflow: `check person access`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B56: Queue identity event handling
- Business goal: Create asynchronous work for a new identity/PDL identity event.
- Required workflow: `handle identity event`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B57: Queue transitional-benefit event handling
- Business goal: Create asynchronous work for a transitional-benefit decision event.
- Required workflow: `handle transitional benefit event`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B58: Check rate-change eligibility for one case
- Business goal: Determine whether a case can undergo manual rate change.
- Required workflow: `check rate change eligibility`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B59: Queue rate change for one case
- Business goal: Queue rate-change processing for a single case.
- Required workflow: `trigger rate change for case`
- Optional verification: `retrieve full case`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B60: Queue rate change for multiple cases
- Business goal: Queue rate-change processing for a supplied set of cases.
- Required workflow: `trigger rate change for cases`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B61: Run synchronous rate change for one case
- Business goal: Execute rate change immediately for one eligible case.
- Required workflow: `check rate change eligibility`; `run synchronous rate change`
- Optional verification: `retrieve full case`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B62: Queue rate change from identities
- Business goal: Discover relevant cases from supplied identities and queue rate-change tasks.
- Required workflow: `trigger rate change from identities`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B63: Queue technical dismissal for long-deadline treatments
- Business goal: Create dismissal tasks for treatments with deadlines beyond a validation date.
- Required workflow: `queue long-deadline dismissals`
- Optional verification: `search tasks`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `queue long-deadline dismissals`: Test_0.test_97 posts invalid `valideringsdato` and asserts `Ugyldig dato`.
- Status: `Partially Covered`. Confidence: `Medium`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B64: Identify ongoing cases without latest rate
- Business goal: Start background discovery of ongoing cases missing the latest rate.
- Required workflow: `find cases without latest rate`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: `POST /api/satsendring/saker-uten-sats` in Test_1.test_40 returns a `callId` pair.
- Failure evidence: No documented failure case covered.
- Status: `Partially Covered`. Confidence: `Medium`.
- Gap: add targeted tests for all remaining documented failure/exceptional branches and, where applicable, state verification.

### B65: Run consistency reconciliation dry run
- Business goal: Queue economy consistency reconciliation without sending to the economy system.
- Required workflow: `run consistency dry run`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B66: Run real consistency reconciliation
- Business goal: Queue economy consistency reconciliation that sends to the economy system.
- Required workflow: `run consistency reconciliation`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B67: Retrieve internal and application statistics
- Business goal: Read aggregate service statistics and application counts.
- Required workflow: `retrieve internal statistics`; `retrieve application statistics`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: `GET /api/internstatistikk` in Test_0.test_5 and `GET /api/internstatistikk/antallSoknader` in Test_0.test_27 return `SUKSESS`; credited as a read-only workflow with no state binding.
- Failure evidence: No documented failure case covered.
- Status: `Partially Covered`. Confidence: `Medium`.
- Gap: add targeted tests for all remaining documented failure/exceptional branches and, where applicable, state verification.

### B68: Retrieve treatment statistics payload
- Business goal: Map one treatment to a DVH treatment statistics payload.
- Required workflow: `retrieve treatment statistics`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `retrieve treatment statistics`: Test_0.test_90 calls `/api/saksstatistikk/behandling/731` and asserts missing treatment.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B69: Retrieve case statistics payload
- Business goal: Map one case to a DVH case statistics payload.
- Required workflow: `retrieve case statistics`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `retrieve case statistics`: Test_0.test_89 calls `/api/saksstatistikk/sak/561` and asserts missing fagsak.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B70: Register statistics message as sent
- Business goal: Persist that an externally sent statistics message has been sent and should not be resent.
- Required workflow: `register statistics sent`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B71: Retrieve benefit statistics decisions
- Business goal: Map treatment ids to DVH V2 benefit-statistics decision payloads.
- Required workflow: `retrieve benefit statistics decisions`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `retrieve benefit statistics decisions`: Test_1.test_37 calls `/api/stonadsstatistikk/vedtakV2` and asserts missing treatment.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B72: Queue unsent benefit statistics
- Business goal: Queue publication tasks for supplied treatments that have not already been sent.
- Required workflow: `queue unsent benefit statistics`
- Optional verification: `retrieve benefit statistics decisions`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B73: Manually queue benefit statistics
- Business goal: Queue benefit-statistics publication for supplied treatments without the normal sent-state filter.
- Required workflow: `queue benefit statistics manually`
- Optional verification: `retrieve benefit statistics decisions`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `queue benefit statistics manually`: Test_1.test_39 calls `/api/stonadsstatistikk/send-til-dvh-manuell` and asserts missing treatment.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B74: Resend manual migration statistics
- Business goal: Backfill benefit statistics for eligible manual migration treatments.
- Required workflow: `resend migration statistics`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B75: Complete an administrative task list with partial success
- Business goal: Attempt to complete a list of tasks administratively and report failures.
- Required workflow: `finish admin task list`
- Optional verification: `search tasks`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B76: Restart small child supplement job
- Business goal: Trigger manual restart logic for small child supplement processing.
- Required workflow: `restart small child supplement job`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B77: Send payment orders administratively
- Business goal: Generate and send payment orders to the economy system for supplied treatments.
- Required workflow: `send payment orders administratively`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B78: Bulk corrected payment-order resend
- Business goal: Generate and implement corrected payment orders for a list of treatments.
- Required workflow: `resend corrected payment orders`
- Optional verification: `find payment-order issues`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `resend corrected payment orders`: Test_1.test_42 exercises bulk resend and asserts one `harFeil` item.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B79: Single-version corrected payment-order resend
- Business goal: Generate and implement a corrected payment order for one treatment and version.
- Required workflow: `resend corrected payment order version`
- Optional verification: `check incorrect cessation dates`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `resend corrected payment order version`: Test_0.test_164 exercises single resend and asserts one `harFeil` item.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B80: Run unvalidated rate change administratively
- Business goal: Run simplified rate change for supplied cases without normal validation.
- Required workflow: `run rate change without validation`
- Optional verification: `retrieve full case`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B81: Identify payments over 100 percent
- Business goal: Start background analysis for payments exceeding 100 percent.
- Required workflow: `identify payments over 100 percent`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: `POST /api/forvalter/identifiser-utbetalinger-over-100-prosent` in Test_1.test_46 returns a `callId` pair.
- Failure evidence: No documented failure case covered.
- Status: `Partially Covered`. Confidence: `Medium`.
- Gap: add targeted tests for all remaining documented failure/exceptional branches and, where applicable, state verification.

### B82: Find payment-order issue candidates
- Business goal: Identify treatments with potentially incorrect payment orders.
- Required workflow: `find payment-order issues`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B83: Check incorrect cessation dates for selected treatments
- Business goal: Validate payment-order cessation dates for supplied treatment ids.
- Required workflow: `check incorrect cessation dates`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B84: Populate support dates for one treatment
- Business goal: Populate support-from/support-to dates for one treatment.
- Required workflow: `populate support dates for treatment`
- Optional verification: `retrieve treatment`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: `populate support dates for treatment`: Test_0.test_71 calls `/api/forvalter/populer-stonad-fom-tom/800` and receives failure for unresolved state.
- Status: `Partially Covered`. Confidence: `Low`.
- Gap: add the successful business workflow with valid setup and state assertions; keep the failure test but make its intent explicit.

### B85: Populate support dates in bulk
- Business goal: Populate support end dates for multiple active treatments up to a limit.
- Required workflow: `populate support dates in bulk`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B86: Find cases to close
- Business goal: Discover cases that should be closed because they have no ongoing entitlement.
- Required workflow: `find cases to close`
- Optional verification: `retrieve full case`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B87: Update case ongoing status
- Business goal: Bulk update ongoing/closed status on cases according to service rules.
- Required workflow: `update case ongoing status`
- Optional verification: `find cases to close`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B88: Find migration duplicates with ongoing Infotrygd case
- Business goal: Identify open cases with multiple migration treatments and an ongoing Infotrygd case.
- Required workflow: `find migration duplicates with Infotrygd`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B89: Find migration duplicates
- Business goal: Identify open cases with multiple migration treatments.
- Required workflow: `find migration duplicates`
- Optional verification: None
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

### B90: Fill empty condition start dates in preprod
- Business goal: Mutate preprod/local test data by filling missing condition start dates from birth dates.
- Required workflow: `fill condition dates in preprod`
- Optional verification: `retrieve treatment`
- Failure cases documented: `1`
- Happy-path evidence: Not covered as a documented business workflow.
- Failure evidence: No documented failure case covered.
- Status: `Not Covered`. Confidence: `Medium`.
- Gap: add a test that establishes documented preconditions, invokes each required function in order, and asserts the expected business result plus documented failures.

## Cross-Behavior Gaps

- Required workflows are usually absent. Tests generally invoke one endpoint with generated values rather than carrying IDs from case creation to treatment, letter, task, complaint, statistics, or payment workflows.
- Direct database setup is not used to create coherent domain preconditions. Most missing-state tests prove error handling, not business success.
- Failure coverage is accidental and narrow. Many 401/403/500 tests exercise authentication, unsupported content types, missing fake infrastructure, or JSON deserialization rather than documented business constraints.
- Assertions focus on HTTP status and `Ressurs` envelope fields. There are no persisted-state assertions and no read-after-write verification for mutations.
- JaCoCo line coverage is non-trivial in some controllers, but branch coverage remains very low, so execution evidence does not support behavior-level confidence for state-machine logic.

## Suggested Additional Tests

| Priority | Behavior ID | Test Intent | Minimal Setup | Calls / Operations | Required Assertions | Coverage Type |
|---:|---|---|---|---|---|---|
| 1 | B1 | Case creation idempotency | Mock/seed accessible person `P1`; no existing case | `POST /api/fagsaker` twice with same `personIdent`/`fagsakType` | both return HTTP 201, same `RestMinimalFagsak.id`, only one case exists | Success |
| 2 | B5+B8 | Treatment creation and decision step flow | Existing normal case with applicant/child/person basis | create treatment, register application, validate conditions, derive result, assess repayment, send to decision maker, decide | step state advances in order; decision implemented; expected result persisted | Success |
| 3 | B12 | Wait/update/resume state machine | Editable treatment not already on wait | set wait, update wait, resume | active wait persisted, metadata changed, then cleared | Success |
| 4 | B15-B20 | Editable EEA/condition/payment side records | Editable treatment with person basis | add/update/delete condition, competence, foreign amount, currency rate, changed payment share | rows created/updated/deleted and later treatment result reset where required | Success |
| 5 | B27-B32 | Letter generation and recipients | Treatment/case with valid decision/person/recipient data | preview/send letters, add/delete recipients, edit decision periods, generate explanations | preview bytes/content present; send side effect recorded; recipient and explanation state correct | Success |
| 6 | B40 | Collaborator lookup happy path | Fake collaborator service returns `ORG1` for search | `POST /api/samhandler/navn`, then `GET /api/samhandler/orgnr/{ORG1}` | search result org number is reused; detail response matches collaborator | Success |
| 7 | B63 | Long-deadline dismissal success | Treatments/tasks with deadlines after validation date | `POST /api/satsendring/henleggBehandlingerMedLangFristSenereEnn/{futureDate}` | dismissal tasks created for each treatment id | Success |
| 8 | B68-B73 | Statistics payload and sent-state behavior | Mappable treatment/case and unsent statistics state | read treatment/case/benefit stats; register sent; queue unsent/manual | mapped payload fields correct; sent marker prevents duplicate where documented | Success/Regression |
| 9 | All behaviors | Documented negative branches | Same setup as happy path but violate one documented prerequisite at a time | invoke failing function directly | exact business error/status/resource state asserted | Failure |

## Appendix: Coverage Artifacts Used

### JaCoCo XML Files

- `/Users/qingchen/Downloads/microservice_subset/behavior-analyze/familie-ba-sak/reports/report.xml`: primary coverage source. Aggregate counters: instruction `30523/174959` (`17.4%`), branch `178/11102` (`1.6%`), line `5234/29081` (`18.0%`), complexity `1819/14702` (`12.4%`), method `1797/8965` (`20.0%`), class `932/2242` (`41.6%`).

### JaCoCo CSV Files

- `/Users/qingchen/Downloads/microservice_subset/behavior-analyze/familie-ba-sak/reports/report.csv`: cross-check/aggregate file with 2242 rows. Totals from CSV: line `5756/29888` (`19.3%`), branch `178/11102` (`1.6%`), method `1797/8965` (`20.0%`). CSV does not expose class counters, so XML is used for class coverage.

## Appendix: Test Inventory

| Test File | Test Case Count | Operations / Assertions | Related Behavior IDs |
|---|---:|---|---|
| `tests/EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_0.java` | 200 | 29 positive, 171 negative; includes `GET /api/internstatistikk`, `GET /api/internstatistikk/antallSoknader`, missing treatment/fagsak/doc/log failures, invalid long-deadline date, case statistics failures | B13, B21, B27, B28, B33, B63, B67, B68, B69, B79, B84 |
| `tests/EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_1.java` | 200 | 15 positive, 185 negative; includes feature toggles, call-id async jobs, collaborator empty search, Infotrygd/task/statistics publishing failures, bulk corrected resend failure | B3, B39, B40, B44, B47, B54, B64, B71, B73, B78, B81 |
| `tests/EM_familie_ba_sak_True_25_false_false_SPECIFIED_false_0_Test_2.java` | 85 | 2 positive, 83 negative; mostly API-doc and authorization denial probes | No direct business behavior coverage credited |
