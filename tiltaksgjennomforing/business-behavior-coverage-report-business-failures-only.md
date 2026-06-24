# Business Behavior Coverage Report - Business Failures Only

## Executive Summary

- Project under analysis: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing`
- Base coverage prompt: `/Users/yangyuhan/behavior-analyze/features-service/business-behavior-coverage-prompt.md`
- Business behavior specification: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/business-behavior.md`
- Function behavior inventory: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/full-behavior.md`
- Test root analyzed: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/tests`
- Total test files analyzed: `1`
- Total test cases analyzed: `195`
- JaCoCo XML reports used: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/reports/report.xml`
- JaCoCo CSV reports used: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/reports/report.csv`
- Source roots analyzed: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/src/main/java`, `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/src/main/resources`
- Total documented business behaviors: `81`
- Covered: `7`
- Partially covered: `1`
- Not covered: `73`
- Unclear: `0`
- Business behavior coverage: `9.3%`
- Happy-path coverage: `8.6%` (`7/81`)
- Business failure coverage: `1.7%` (`1/58`)
- Ignored technical/security failure items: `27`
- Behavior checklist coverage: `8/139` (`5.8%`)
- JaCoCo coverage signal:
  - line coverage: `12.5% (820/6541)`
  - branch coverage: `1.6% (127/8084)`
  - method coverage: `11.1% (409/3691)`
  - class coverage: `32.7% (129/394)`

This report reuses the original coverage-reporting approach, but changes the failure metric: framework-level parameter binding, generic authentication/authorization, pure endpoint security gates, and infrastructure/external-dependency failures are ignored rather than counted as business failure items.

Under this adjusted scope, the simple read-only lookup/probe behaviors B54-B60 become fully covered because their only previously missing failure cases were technical or infrastructure failures. Most previously partial admin/internal behaviors become not covered because their only evidence was an access-denied gate, not a business outcome.

The important conclusion is sharper than in the original report: the generated suite covers almost no real business failure branches. Only one retained business failure is covered: B3, invalid Arena cleanup agreement creation with blank participant id. The many 4xx/5xx generated tests mostly stop at request binding, missing cookies, missing endpoint access, invalid UUID parsing, or group checks.

## Adjusted Failure Scope

Ignored failure items are not counted in the failure denominator and do not prevent a behavior from being `Covered` when its happy path is covered.

Ignored categories:
- Generic `Authentication, authorization, parameter binding, or external dependency failure.` entries.
- Request-shape failures such as missing `innlogget-part` cookie or non-UUID request values.
- Unsupported token/role combinations that fail before the documented domain operation.
- Internal system-user, developer-admin, DVH patch group, and delete-marker admin access gates.
- Infrastructure availability failures such as health-check database failure.

Retained business failure items:
- Domain validation and invariant failures, such as invalid agreement creation, same participant and mentor identity, stale concurrency timestamp, invalid phone number, incomplete approval prerequisites, invalid subsidy-period state, unknown business resources, and ownership/readability constraints on notifications.
- Business-scoped access or eligibility rules that are part of the domain workflow, such as advisor write access to a participant or employer Altinn access for a selected company and measure type.

## Test Corpus Summary

| Area | Count / Summary |
|---|---|
| Test files analyzed | `1` |
| Test cases analyzed | `195` |
| Primary test framework | EvoMaster-generated JUnit 5 with RestAssured |
| Positive-path tests | `13` test cases with at least one 2xx operation |
| Negative/failure tests | `182` test cases with at least one 4xx/5xx operation |
| Tests with business assertions | `12`; all are read-only response-field assertions |
| Tests with only status/code assertions | `183`; includes empty-body and generic error JSON checks |

| Test File | Test Cases | Main Behavior Area | Evidence Quality |
|---|---:|---|---|
| `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/tests/EM_tiltaksgjennomforing_True_25_false_false_SPECIFIED_false_0_Test.java` | 195 | Broad generated API probing; strongest on lookup endpoints, weakest on stateful agreement workflows | Low |

## Function-To-Code Map

| Behavior IDs | Main functions / endpoints | Source classes | JaCoCo signal |
|---|---|---|---|
| B1-B51 | Agreement listing, creation, retrieval, update, approvals, post-approval changes, annulment, employer and decision-maker lists | AvtaleController, Avtale, Avtalepart subclasses, repositories and services | AvtaleController line `1.0% (3/301)`, branch `0.0% (0/40)` |
| B52 | Logged-in user context lookup | InnloggetBrukerController, InnloggingService | InnloggetBrukerController line `75.0% (3/4)` |
| B53 | Employer organization lookup | OrganisasjonController, EregService | OrganisasjonController line `50.0% (1/2)` |
| B54 | Altinn rights request URL lookup | BeOmAltinnRettighetUrlerController | line `100.0% (9/9)` |
| B55-B57 | Code-list lookup | KodeverkController, Status, Tiltakstype | KodeverkController line `100.0% (20/20)`, branch `100.0% (2/2)` |
| B58-B59 | Feature toggle and variant lookup | FeatureToggleController, FeatureToggleService, FakeFakeUnleash | FeatureToggleController line `100.0% (5/5)` |
| B60 | Internal health probe | HealthCheckController, JdbcTemplate | HealthCheckController line `100.0% (4/4)` |
| B61-B65 | Notification listing and read marking | VarselController, VarselRepository | VarselController line `6.2% (1/16)` |
| B66 | Journal export and completion marking | InternalAvtaleController, AvtaleTilJournalfoeringMapper | InternalAvtaleController line `26.3% (5/19)` |
| B67-B76 | Developer-admin wage/subsidy-period repair operations | AdminController, Avtale, TilskuddPeriode | AdminController line `1.8% (2/112)`, branch `0.0% (0/36)` |
| B77-B78 | Data warehouse patching | InternalDvhMeldingProdusentController, DvhAvtalePatchService | InternalDvhMeldingProdusentController line `21.1% (4/19)` |
| B79-B81 | Agreement event publication | AvtaleHendelseController, AvtaleHendelseService | AvtaleHendelseController line `23.5% (4/17)`, branch `0.0% (0/2)` |

## Coverage Matrix

| ID | Business Behavior | Happy Path | Business Failure Cases | Ignored Technical/Security Failures | Status | Confidence | Main Gap |
|---|---|---|---|---|---|---|---|
| B1 | Role-scoped agreement listing | Not covered | None retained | role cookie and token issuer unsupported | Not Covered | High | Missing successful list workflow. |
| B2 | Advisor-created agreement | Not covered | Not covered: advisor lacks write access to participant | None | Not Covered | High | Missing creation workflow and domain access failure. |
| B3 | Arena cleanup agreement creation | Not covered | Covered: invalid creation via `test_12_getOnAvtalerReturns400` | None | Partially Covered | Medium | Missing successful Arena cleanup creation. |
| B4 | Employer-created agreement | Not covered | Not covered: employer lacks Altinn access | None | Not Covered | High | Missing employer creation workflow and Altinn-denied branch. |
| B5 | Advisor-created mentor agreement | Not covered | Not covered: participant and mentor same identity | None | Not Covered | High | Missing mentor creation workflow and equality rejection. |
| B6 | Employer-created mentor agreement | Not covered | Not covered: unsupported `avtalerolle` | None | Not Covered | High | Missing employer mentor workflow and invalid-role branch. |
| B7 | Check participant overlap | Not covered | None retained | non-UUID `avtaleId` parameter parsing | Not Covered | High | Missing valid overlap lookup. |
| B8 | Saved agreement search registration and replay | Not covered | Not covered: unknown `sokId` replay | unsupported role/token combination | Not Covered | High | Missing save-and-replay workflow and unknown search id branch. |
| B9 | Agreement detail retrieval by id | Not covered | Not covered: agreement does not exist | None | Not Covered | High | Missing existing and unknown agreement-id retrieval. |
| B10 | Agreement detail retrieval by agreement number | Not covered | Not covered: unknown `avtaleNr` | None | Not Covered | High | Missing agreement-number lookup and unknown-number branch. |
| B11 | Agreement version history retrieval | Not covered | Not covered: agreement missing or inaccessible | None | Not Covered | High | Missing version-history workflow and missing/inaccessible branch. |
| B12 | Draft agreement update | Not covered | Not covered: stale or missing concurrency timestamp | None | Not Covered | High | Missing update workflow and concurrency failure. |
| B13 | Draft agreement update dry-run | Not covered | Not covered: agreement already approved | None | Not Covered | High | Missing dry-run workflow and approved-agreement rejection. |
| B14 | Agreement sharing with a party | Not covered | Not covered: missing or invalid phone number | None | Not Covered | High | Missing share workflow and phone validation branch. |
| B15 | Participant agreement approval | Not covered | Not covered: required agreement fields incomplete | None | Not Covered | High | Missing participant approval workflow and incomplete-fields rejection. |
| B16 | Employer agreement approval | Not covered | Not covered: employer already approved | None | Not Covered | High | Missing employer approval workflow and duplicate approval branch. |
| B17 | Mentor confidentiality signing | Not covered | Not covered: caller is not mentor | None | Not Covered | High | Missing mentor signing workflow and role rejection. |
| B18 | Advisor final approval | Not covered | Not covered: participant or employer approval missing | None | Not Covered | High | Missing advisor approval workflow and prerequisite rejection. |
| B19 | Advisor approval on behalf of participant | Not covered | Not covered: no on-behalf reason selected | None | Not Covered | High | Missing on-behalf participant workflow and reason validation. |
| B20 | Advisor approval on behalf of employer | Not covered | Not covered: measure type unsupported | None | Not Covered | High | Missing on-behalf employer workflow and unsupported-measure branch. |
| B21 | Advisor approval on behalf of participant and employer | Not covered | Not covered: participant or employer already approved | None | Not Covered | High | Missing combined on-behalf workflow and duplicate approval branch. |
| B22 | Approval revocation | Not covered | Not covered: no approvals exist | None | Not Covered | High | Missing revocation workflow and no-op rejection. |
| B23 | After-registration eligibility marking | Not covered | Not covered: agreement already entered | None | Not Covered | High | Missing after-registration mark workflow and entered-agreement branch. |
| B24 | After-registration eligibility removal | Not covered | Not covered: decision-maker access missing | None | Not Covered | High | Missing after-registration removal workflow and access branch. |
| B25 | Subsidy period approval | Not covered | Not covered: `enhet` is not four digits | None | Not Covered | High | Missing subsidy approval workflow and unit validation. |
| B26 | Subsidy period rejection | Not covered | Not covered: rejection explanation blank | None | Not Covered | High | Missing rejection workflow and explanation validation. |
| B27 | Rejected subsidy period return | Not covered | Not covered: agreement annulled or interrupted | None | Not Covered | High | Missing return workflow and invalid-state branch. |
| B28 | Agreement shortening | Not covered | Not covered: new end date is not before current end date | None | Not Covered | High | Missing shortening workflow and date-order branch. |
| B29 | Dry-run agreement shortening | Not covered | Not covered: agreement not advisor-approved | None | Not Covered | High | Missing dry-run shortening workflow and status branch. |
| B30 | Agreement extension | Not covered | Not covered: new end date is not after current end date | None | Not Covered | High | Missing extension workflow and date-order branch. |
| B31 | Dry-run agreement extension | Not covered | Not covered: agreement not advisor-approved | None | Not Covered | High | Missing dry-run extension workflow and status branch. |
| B32 | Subsidy calculation change | Not covered | Not covered: agreement not approved by advisor | None | Not Covered | High | Missing subsidy-calculation change and approval-state branch. |
| B33 | Dry-run subsidy calculation change | Not covered | Not covered: required calculation input missing | None | Not Covered | High | Missing dry-run calculation change and missing-input branch. |
| B34 | Contact information change | Not covered | Not covered: required contact field missing | None | Not Covered | High | Missing contact-change workflow and missing-field branch. |
| B35 | Job description change | Not covered | Not covered: agreement not advisor-approved | None | Not Covered | High | Missing job-description change and status branch. |
| B36 | Follow-up and adaptation text change | Not covered | Not covered: follow-up or adaptation text missing | None | Not Covered | High | Missing text-change workflow and missing-text branch. |
| B37 | Work-training goal replacement | Not covered | Not covered: agreement is not work training | None | Not Covered | High | Missing goal replacement and wrong-measure branch. |
| B38 | Inclusion subsidy expense replacement | Not covered | Not covered: total amount too high | None | Not Covered | High | Missing expense replacement and amount-limit branch. |
| B39 | Mentor details change | Not covered | Not covered: agreement is not mentor agreement | None | Not Covered | High | Missing mentor-details change and wrong-measure branch. |
| B40 | Cost center change | Not covered | Not covered: norg2 has no unit for `enhet` | None | Not Covered | High | Missing cost-center change and unknown-unit branch. |
| B41 | Arena migration date adjustment | Not covered | Not covered: agreement already entered | None | Not Covered | High | Missing migration-date adjustment and entered-state branch. |
| B42 | Dry-run Arena migration date adjustment | Not covered | Not covered: agreement missing | None | Not Covered | High | Missing dry-run adjustment and missing-agreement branch. |
| B43 | Employer account number lookup | Not covered | Not covered: account register has no company account | None | Not Covered | High | Missing account lookup and no-account branch. |
| B44 | Agreement PDF download | Not covered | Not covered: agreement not approved by advisor | None | Not Covered | High | Missing PDF download and not-approved branch. |
| B45 | Salesforce dialog visibility check | Not covered | Not covered: agreement missing or inaccessible | None | Not Covered | High | Missing visibility check and missing/inaccessible branch. |
| B46 | Follow-up unit refresh | Not covered | Not covered: protected address code 6 | None | Not Covered | High | Missing refresh workflow and protected-address branch. |
| B47 | Advisor takeover of agreement | Not covered | Not covered: logged-in advisor already owns agreement | None | Not Covered | High | Missing takeover workflow and same-advisor branch. |
| B48 | Agreement annulment | Not covered | Not covered: paid subsidy period exists | None | Not Covered | High | Missing annulment workflow and paid-period rejection. |
| B49 | Agreement soft deletion | Not covered | None retained | delete-marker admin access gate | Not Covered | High | Missing soft-delete workflow. |
| B50 | Employer Min Side agreement listing | Not covered | None retained | caller is not employer token/role | Not Covered | High | Missing employer list workflow. |
| B51 | Decision-maker work queue listing | Not covered | Not covered: decision-maker has no NAV units | None | Not Covered | High | Missing work-queue workflow and no-unit branch. |
| B52 | Logged-in user context lookup | Not covered | None retained | missing `innlogget-part` cookie | Not Covered | High | Missing successful user-context lookup. |
| B53 | Employer organization lookup | Not covered | Not covered: EREG does not find unit | None | Not Covered | High | Missing organization lookup and unknown-unit branch. |
| B54 | Altinn rights request URL lookup | Covered: `test_3_getOnBe_om_altinn_rettighet_urlerReturnsObject` | None retained | generic auth/binding/external failure | Covered | Medium | No retained business gap. |
| B55 | Combined code-list lookup | Covered: `test_1_getOnKodeverkReturnsObject` | None retained | generic auth/binding/external failure | Covered | Medium | No retained business gap. |
| B56 | Agreement status code-list lookup | Covered: `test_26_getOnStatuserReturns7Elements` | None retained | generic auth/binding/external failure | Covered | Medium | No retained business gap. |
| B57 | Measure type code-list lookup | Covered: `test_23`, `test_25`, `test_29`, `test_30` | None retained | generic auth/binding/external failure | Covered | Medium | No retained business gap. |
| B58 | Feature toggle evaluation | Covered: `test_2_getOnFeatureReturnsObject`, `test_4_getOnFeatureReturnsObject` | None retained | generic auth/binding/external failure | Covered | Medium | No retained business gap. |
| B59 | Feature variant lookup | Covered: `test_27`, `test_28` | None retained | generic auth/binding/external failure | Covered | Medium | No retained business gap. |
| B60 | Internal health probe | Covered: `test_24_getOnHealthcheckReturnsContent` | None retained | database query fails | Covered | Medium | No retained business gap. |
| B61 | Overview notification listing | Not covered | None retained | unsupported role/token combination | Not Covered | High | Missing successful notification overview. |
| B62 | Agreement modal notification listing | Not covered | None retained | generic auth/binding/external failure | Not Covered | High | Missing successful modal notification listing. |
| B63 | Agreement notification log listing | Not covered | Not covered: agreement id unknown | None | Not Covered | High | Missing notification log listing and unknown-agreement branch. |
| B64 | Single notification read marking | Not covered | Not covered: notification not owned/readable | unsupported role/token combination for overview listing | Not Covered | High | Missing notification read workflow and ownership rejection. |
| B65 | Bulk notification read marking | Not covered | Not covered: any body id not readable | unsupported role/token combination for overview listing | Not Covered | High | Missing bulk read workflow and mixed-ownership rejection. |
| B66 | Journal export and completion marking | Not covered | None retained | configured system-user access gate for both operations | Not Covered | High | Missing journal export/completion workflow. |
| B67 | Selected agreement wage-subsidy recalculation | Not covered | None retained | developer-admin access gate | Not Covered | High | Missing authorized recalculation workflow. |
| B68 | Missing reduced-percent date repair | Not covered | None retained | developer-admin access gate | Not Covered | High | Missing authorized repair workflow. |
| B69 | Dry-run missing reduced-percent date fix | Not covered | None retained | developer-admin access gate | Not Covered | High | Missing authorized dry-run workflow. |
| B70 | Admin subsidy-period generation for one agreement | Not covered | Not covered: agreement id unknown | None | Not Covered | High | Missing generation workflow and unknown-agreement branch. |
| B71 | Unhandled subsidy-period recalculation | Not covered | Not covered: agreement type not subsidy-backed | None | Not Covered | High | Missing recalculation workflow and wrong-measure branch. |
| B72 | Subsidy-period date-order diagnostic | Not covered | None retained | developer-admin access gate | Not Covered | High | Missing authorized diagnostic workflow. |
| B73 | Subsidy-period annulment | Not covered | Not covered: subsidy period id unknown | None | Not Covered | High | Missing annulment workflow and unknown-period branch. |
| B74 | Annul and resend approved subsidy period | Not covered | Not covered: subsidy period id unknown | None | Not Covered | High | Missing resend workflow and unknown-period branch. |
| B75 | Annul and generate unhandled subsidy period | Not covered | Not covered: agreement measure not subsidy-backed | None | Not Covered | High | Missing generate-unhandled workflow and wrong-measure branch. |
| B76 | Annul and generate Arena-treated periods | Not covered | Not covered: agreement id unknown | None | Not Covered | High | Missing Arena-treated generation and unknown-agreement branch. |
| B77 | Selected data warehouse patching | Not covered | None retained | DVH patch group access gate | Not Covered | High | Missing authorized selected patch workflow. |
| B78 | All-agreement data warehouse patching | Not covered | None retained | DVH patch group access gate | Not Covered | High | Missing authorized all-agreement patch workflow. |
| B79 | Single agreement event publication | Not covered | Not covered: agreement id unknown | None | Not Covered | High | Missing single-event publication and unknown-agreement branch. |
| B80 | All-agreement event publication | Not covered | None retained | developer-admin access gate | Not Covered | High | Missing authorized all-event publication workflow. |
| B81 | All-agreement event publication dry-run | Not covered | None retained | developer-admin access gate | Not Covered | High | Missing authorized dry-run event workflow. |

## Business Failure Coverage Details

Only one retained business failure item is covered:

| Behavior | Failing Function | Failure Condition | Covered Evidence | Notes |
|---|---|---|---|---|
| B3 | `create Arena cleanup agreement` | agreement creation itself is invalid | `test_12_getOnAvtalerReturns400` posts `/avtaler?ryddeavtale=true` with blank `deltakerFnr` and receives `400` | This is valid partial evidence for the invalid-creation branch, but no successful Arena cleanup agreement is created. |

Examples of retained business failures not covered:

| Area | Missing Failure Coverage |
|---|---|
| Agreement creation | advisor lacks participant write access; employer lacks Altinn access; participant and mentor same identity; unsupported mentor `avtalerolle` |
| Agreement retrieval and search | unknown `sokId`, unknown `avtaleId`, unknown `avtaleNr`, missing/inaccessible agreement versions |
| Agreement update and approval | stale timestamp, already-approved draft, incomplete agreement fields, duplicate approvals, missing prerequisite approvals |
| Post-approval changes | invalid duration changes, wrong measure type, missing required contact/text/calculation fields, amount limit exceeded |
| Subsidy period lifecycle | invalid unit, blank rejection explanation, annulled/interrupted agreement, unknown subsidy period id, unsupported measure |
| Notifications | unknown agreement id, notification not owned/readable, mixed unreadable ids in bulk marking |
| Operational jobs | unknown agreement id, unknown subsidy period id, unsupported measure type, missing candidate state |

## Ignored Technical/Security Failure Items

These `27` failure items were removed from the business-failure denominator:

| Behavior | Ignored Failure Condition | Reason |
|---|---|---|
| B1 | role cookie and token issuer are not a supported combination | Generic auth/request-context gate |
| B7 | `avtaleId` is supplied but is not a UUID | Parameter parsing/binding |
| B8 | unsupported role/token combination | Generic auth/request-context gate |
| B49 | advisor is not configured as a delete-marker admin | Operational admin access gate |
| B50 | caller is not an employer token/role | Endpoint auth gate |
| B52 | `innlogget-part` cookie is absent | Missing request cookie |
| B54 | generic auth/binding/external failure | No implementation-specific business failure identified |
| B55 | generic auth/binding/external failure | No implementation-specific business failure identified |
| B56 | generic auth/binding/external failure | No implementation-specific business failure identified |
| B57 | generic auth/binding/external failure | No implementation-specific business failure identified |
| B58 | generic auth/binding/external failure | No implementation-specific business failure identified |
| B59 | generic auth/binding/external failure | No implementation-specific business failure identified |
| B60 | database query fails | Infrastructure dependency failure |
| B61 | unsupported role/token combination | Generic auth/request-context gate |
| B62 | generic auth/binding/external failure | No implementation-specific business failure identified |
| B64 | unsupported role/token combination | Generic auth/request-context gate |
| B65 | unsupported role/token combination | Generic auth/request-context gate |
| B66 | caller is not the configured system user, for list | Internal system-user gate |
| B66 | caller is not the configured system user, for mark journaled | Internal system-user gate |
| B67 | caller lacks developer-admin access | Developer-admin gate |
| B68 | caller lacks developer-admin access | Developer-admin gate |
| B69 | caller lacks developer-admin access | Developer-admin gate |
| B72 | caller lacks developer-admin access | Developer-admin gate |
| B77 | caller lacks DVH patch group access | DVH admin gate |
| B78 | caller lacks DVH patch group access | DVH admin gate |
| B80 | caller lacks developer-admin access | Developer-admin gate |
| B81 | caller lacks developer-admin access | Developer-admin gate |

## Cross-Behavior Gaps

- The generated suite has many negative HTTP outcomes, but almost none reach retained domain branches.
- Most stateful workflows lack valid setup: no realistic agreements, subsidy periods, notification rows, saved searches, journal candidates, or admin repair candidates are created before invoking behavior endpoints.
- Agreement lifecycle behavior remains essentially uncovered: `AvtaleController` has only `1.0%` line coverage and `0/40` branch coverage.
- Admin and operational success behavior remains uncovered: `AdminController` has only `1.8%` line coverage and `0/36` branch coverage.
- Notification behavior remains uncovered beyond request-entry effects: `VarselController` has only `6.2%` line coverage.
- The lookup controllers are genuinely covered on happy path, and after technical failures are ignored they are the only fully covered behaviors.
- Some generated failures assert `500` for access-gate exceptions. These are intentionally ignored here because they are not business failure proof.

## Suggested Additional Tests

| Priority | Target Behaviors | Test Intent | Minimal Setup | Calls / Operations | Assertions | Coverage Type |
|---:|---|---|---|---|---|---|
| 1 | B2-B6 | Cover agreement and mentor creation business failures | Mock person, EREG, ABAC, Altinn; no existing agreement | Valid create calls plus denied advisor access, denied Altinn access, same participant/mentor, invalid `avtalerolle` | `201` for valid creates; no persisted row and domain error for invalid cases | Success and business failure |
| 2 | B12-B18 | Cover update and approval invariants | Create fully filled and partially filled agreements in required states | `PUT /avtaler/{id}`, dry-run, party/advisor approvals | Last-modified handling, approval timestamps, rejection for stale timestamp/incomplete prerequisites | Success and business failure |
| 3 | B25-B33 | Cover subsidy-period lifecycle and date/calculation failures | Advisor-approved subsidy-backed agreement with unhandled/rejected periods | approve, reject, send back, shorten/extend, change calculation | Status transitions, amount recalculation, invalid unit/date/status failures | Success and business failure |
| 4 | B61-B65 | Cover notification ownership failures | Persist notifications for current and foreign parties | overview/modal/log listings; single and bulk mark-read | Only owned notifications returned; foreign/unknown ids rejected without marking | Success and business failure |
| 5 | B66-B81 | Cover operational success and retained domain failures | Authorized system/admin/DVH callers plus candidate agreements/periods | journal export/update, admin generation/annulment, DVH/event publication | Persisted side effects, unknown id failures, wrong-measure failures | Success and business failure |
| 6 | B8-B11/B43-B45/B53 | Cover unknown-resource lookup failures | Known and unknown ids/numbers/orgs/accounts | saved search replay, agreement lookups, account/PDF/Salesforce/org lookup | Correct data for known resources; explicit domain not-found/no-account behavior for unknown resources | Success and business failure |

## Notes And Assumptions

- The original report at `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/business-behavior-coverage-report.md` was not modified.
- This report intentionally changes only the failure-counting policy. It does not reinterpret JaCoCo data or generated test bodies beyond that policy.
- The base behavior specification still contains technical failure entries. They are recorded here as ignored rather than deleted or rewritten.
- Business behavior scoring still uses the original formula: `Covered = 1.0`, `Partially Covered = 0.5`, `Not Covered = 0`, `Unclear = 0`.
- A behavior with a covered happy path and no retained business failure items is marked `Covered`.
- A behavior with only ignored technical/security failure evidence and no happy path is marked `Not Covered`.
- `GET /avtaler/sok` unknown `sokId` remains a documented implementation discrepancy: source returns an empty successful result object rather than a thrown failure. It is retained here because it is a business-level exceptional branch, but the generated tests do not cover it.
