# Business Behavior Coverage Report

## Executive Summary

- Project under analysis: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing`
- Business behavior specification: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/business-behavior.md`
- Function behavior inventory: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/full-behavior.md`
- Test root analyzed: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/tests`
- Total test files analyzed: `1`
- Total test cases analyzed: `195`
- JaCoCo XML reports used: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/reports/report.xml`
- JaCoCo CSV reports used: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/reports/report.csv`
- Source roots analyzed: `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/src/main/java`, `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/src/main/resources`
- Total documented business behaviors: `81`
- Covered: `0`
- Partially covered: `19`
- Not covered: `62`
- Unclear: `0`
- Business behavior coverage: `11.7%`
- Happy-path coverage: `8.6%` (`7/81`)
- Failure/exceptional-case coverage: `14.1%` (`12/85`)
- Behavior checklist coverage: `19/166` (`11.4%`)
- JaCoCo coverage signal:
  - line coverage: `12.5% (820/6541)`
  - branch coverage: `1.6% (127/8084)`
  - method coverage: `11.1% (409/3691)`
  - class coverage: `32.7% (129/394)`

The generated tests cover a small set of read-only lookup behaviors well enough to prove their successful responses: code lists, feature toggles, Altinn rights URLs, and the internal health probe. They do not cover any agreement lifecycle success workflow end to end.

Important gaps:
- No documented behavior is fully covered because every happy-path behavior that is exercised is missing its documented failure case, and every covered failure-only behavior is missing its happy path.
- Agreement creation, approval, versioning, subsidy-period lifecycle, notification mutation, journal completion, and admin repair workflows are not covered as business workflows.
- Most generated negative tests assert only HTTP status, empty body, or generic error fields; they rarely create the preconditions required to reach the documented business branch.
- Many agreement endpoint tests fail during Spring parameter binding, missing `innlogget-part`, invalid UUID conversion, or authorization setup, before the documented domain rule is reached.
- JaCoCo corroborates the thin behavior coverage: `AvtaleController` has only `1.0%` line coverage and `0/40` covered branches, while lookup controllers are fully or mostly covered.
- Implementation discrepancy: documented `GET /avtaler/sok` unknown-`sokId` failure is not implemented as a failure in source; the controller returns an empty result object when no saved search is found.

## Test Corpus Summary

| Area | Count / Summary |
|---|---|
| Test files analyzed | `1` |
| Test cases analyzed | `195` |
| Primary test framework | EvoMaster-generated JUnit 5 with RestAssured |
| Main endpoints/functions exercised | Agreement endpoints, code lists, feature toggles, notifications, internal journal endpoint, developer-admin endpoints, organization lookup |
| Positive-path tests | `13` test cases with at least one 2xx operation |
| Negative/failure tests | `182` test cases with at least one 4xx/5xx operation |
| Tests with business assertions | `12`; all are read-only response-field assertions, with no persisted-state verification |
| Tests with only status/code assertions | `183`; includes empty-body and generic error JSON checks |

| Test File | Test Cases | Main Behavior Area | Evidence Quality |
|---|---:|---|---|
| `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/tests/EM_tiltaksgjennomforing_True_25_false_false_SPECIFIED_false_0_Test.java` | 195 | Broad generated API probing; strongest on lookup endpoints, weakest on stateful agreement workflows | Low |

## Function-To-Code Map

| Behavior IDs | Main functions / endpoints | Source classes | JaCoCo signal |
|---|---|---|---|
| B1-B51 | Agreement listing, creation, retrieval, update, approvals, post-approval changes, annulment, employer and decision-maker lists | AvtaleController, Avtale, Avtalepart subclasses, repositories and services | AvtaleController line 1.0% (3/301), branch 0.0% (0/40) |
| B52 | Logged-in user context lookup | InnloggetBrukerController, InnloggingService | InnloggetBrukerController line 75.0% (3/4) |
| B53 | Employer organization lookup | OrganisasjonController, EregService | OrganisasjonController line 50.0% (1/2) |
| B54 | Altinn rights request URL lookup | BeOmAltinnRettighetUrlerController | line 100.0% (9/9) |
| B55-B57 | Code-list lookup | KodeverkController, Status, Tiltakstype | KodeverkController line 100.0% (20/20), branch 100.0% (2/2) |
| B58-B59 | Feature toggle and variant lookup | FeatureToggleController, FeatureToggleService, FakeFakeUnleash | FeatureToggleController line 100.0% (5/5) |
| B60 | Internal health probe | HealthCheckController, JdbcTemplate | HealthCheckController line 100.0% (4/4) |
| B61-B65 | Notification listing and read marking | VarselController, VarselRepository | VarselController line 6.2% (1/16) |
| B66 | Journal export and completion marking | InternalAvtaleController, AvtaleTilJournalfoeringMapper | InternalAvtaleController line 26.3% (5/19) |
| B67-B76 | Developer-admin wage/subsidy-period repair operations | AdminController, Avtale, TilskuddPeriode | AdminController line 1.8% (2/112), branch 0.0% (0/36) |
| B77-B78 | Data warehouse patching | InternalDvhMeldingProdusentController, DvhAvtalePatchService | InternalDvhMeldingProdusentController line 21.1% (4/19) |
| B79-B81 | Agreement event publication | AvtaleHendelseController, AvtaleHendelseService | AvtaleHendelseController not materially covered in XML class signal |

## Coverage Matrix

| ID | Business Behavior | Happy Path | Failure Cases | Optional Verification | Status | Confidence | Main Gap |
|---|---|---|---|---|---|---|---|
| B1 | Role-scoped agreement listing | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B2 | Advisor-created agreement | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B3 | Arena cleanup agreement creation | Not covered | create Arena cleanup agreement: agreement creation itself is invalid (test_12_getOnAvtalerReturns400 uses POST /avtaler?ryddeavtale=true with blank deltakerFnr and gets 400) | None documented | Partially Covered | Medium | Missing happy path. |
| B4 | Employer-created agreement | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B5 | Advisor-created mentor agreement | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B6 | Employer-created mentor agreement | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B7 | Check participant overlap | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B8 | Saved agreement search registration and replay | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B9 | Agreement detail retrieval by id | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B10 | Agreement detail retrieval by agreement number | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B11 | Agreement version history retrieval | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B12 | Draft agreement update | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B13 | Draft agreement update dry-run | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B14 | Agreement sharing with a party | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B15 | Participant agreement approval | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B16 | Employer agreement approval | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B17 | Mentor confidentiality signing | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B18 | Advisor final approval | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B19 | Advisor approval on behalf of participant | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B20 | Advisor approval on behalf of employer | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B21 | Advisor approval on behalf of participant and employer | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B22 | Approval revocation | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B23 | After-registration eligibility marking | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B24 | After-registration eligibility removal | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B25 | Subsidy period approval | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B26 | Subsidy period rejection | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B27 | Rejected subsidy period return | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B28 | Agreement shortening | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B29 | Dry-run agreement shortening | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B30 | Agreement extension | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B31 | Dry-run agreement extension | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B32 | Subsidy calculation change | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B33 | Dry-run subsidy calculation change | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B34 | Contact information change | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B35 | Job description change | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B36 | Follow-up and adaptation text change | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B37 | Work-training goal replacement | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B38 | Inclusion subsidy expense replacement | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B39 | Mentor details change | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B40 | Cost center change | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B41 | Arena migration date adjustment | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B42 | Dry-run Arena migration date adjustment | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B43 | Employer account number lookup | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B44 | Agreement PDF download | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B45 | Salesforce dialog visibility check | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B46 | Follow-up unit refresh | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B47 | Advisor takeover of agreement | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B48 | Agreement annulment | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B49 | Agreement soft deletion | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B50 | Employer Min Side agreement listing | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B51 | Decision-maker work queue listing | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B52 | Logged-in user context lookup | Not covered | get logged-in user: `innlogget-part` cookie is absent (test_10_getOnInnlogget_brukerReturns400 calls GET /innlogget-bruker without innlogget-part and gets 400) | None documented | Partially Covered | Medium | Missing happy path. |
| B53 | Employer organization lookup | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B54 | Altinn rights request URL lookup | Covered: test_3_getOnBe_om_altinn_rettighet_urlerReturnsObject | Not covered | None documented | Partially Covered | Medium | Missing failure cases: Authentication, authorization, parameter binding, or external dependency failure.. |
| B55 | Combined code-list lookup | Covered: test_1_getOnKodeverkReturnsObject | Not covered | None documented | Partially Covered | Medium | Missing failure cases: Authentication, authorization, parameter binding, or external dependency failure.. |
| B56 | Agreement status code-list lookup | Covered: test_26_getOnStatuserReturns7Elements | Not covered | None documented | Partially Covered | Medium | Missing failure cases: Authentication, authorization, parameter binding, or external dependency failure.. |
| B57 | Measure type code-list lookup | Covered: test_23/test_25/test_29/test_30_getOnTiltakstyperReturns6Elements | Not covered | None documented | Partially Covered | Medium | Missing failure cases: Authentication, authorization, parameter binding, or external dependency failure.. |
| B58 | Feature toggle evaluation | Covered: test_2_getOnFeatureReturnsObject; test_4_getOnFeatureReturnsObject | Not covered | None documented | Partially Covered | Medium | Missing failure cases: Authentication, authorization, parameter binding, or external dependency failure.. |
| B59 | Feature variant lookup | Covered: test_27_getOnFeatureVariantWithQueryParamsReturnsObject; test_28_getOnFeatureVariantWithQueryParamReturnsObject | Not covered | None documented | Partially Covered | Medium | Missing failure cases: Authentication, authorization, parameter binding, or external dependency failure.. |
| B60 | Internal health probe | Covered: test_24_getOnHealthcheckReturnsContent | Not covered | None documented | Partially Covered | Medium | Missing failure cases: database query fails. |
| B61 | Overview notification listing | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B62 | Agreement modal notification listing | Not covered | list agreement modal notifications: Authentication, authorization, parameter binding, or external dependency failure. (test_41_getOnAvtale_modalReturns400 sends non-UUID avtaleId and gets 400) | None documented | Partially Covered | Low | Missing happy path. |
| B63 | Agreement notification log listing | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B64 | Single notification read marking | Not covered | Not covered | list overview notifications: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B65 | Bulk notification read marking | Not covered | Not covered | list overview notifications: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B66 | Journal export and completion marking | Not covered | list unjournaled agreements: caller is not the configured system user (test_83_getOnAvtalerReturns403 calls GET /internal/avtaler with system token but not configured system user); 1 failure item(s) missing | list unjournaled agreements: not executed | Partially Covered | Medium | Missing happy path and failure cases: caller is not the configured system user. |
| B67 | Selected agreement wage-subsidy recalculation | Not covered | recalculate wage subsidy: caller lacks developer-admin access (test_20_postOnReberegnCauses500_internalServerError reaches developer-admin access check with AAD token lacking the group) | None documented | Partially Covered | Medium | Missing happy path. |
| B68 | Missing reduced-percent date repair | Not covered | fix missing reduced-percent date: caller lacks developer-admin access (test_85_postOnReberegn_mangler_dato_for_redusert_prosCauses500_internalServerError reaches developer-admin access check) | None documented | Partially Covered | Medium | Missing happy path. |
| B69 | Dry-run missing reduced-percent date fix | Not covered | dry-run missing reduced-percent date fix: caller lacks developer-admin access (test_86_postOnReberegn_mangler_dato_for_redusert_prosent_dry_runCauses500_internalServerError reaches developer-admin access check) | None documented | Partially Covered | Medium | Missing happy path. |
| B70 | Admin subsidy-period generation for one agreement | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B71 | Unhandled subsidy-period recalculation | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B72 | Subsidy-period date-order diagnostic | Not covered | find subsidy period date-order problems: caller lacks developer-admin access (test_21_postOnFinn_avtaler_med_tilskuddsperioder_feil_datoerCauses500_internalServerError reaches developer-admin access check) | None documented | Partially Covered | Medium | Missing happy path. |
| B73 | Subsidy-period annulment | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B74 | Annul and resend approved subsidy period | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B75 | Annul and generate unhandled subsidy period | Not covered | Not covered | None documented | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B76 | Annul and generate Arena-treated periods | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B77 | Selected data warehouse patching | Not covered | patch selected data warehouse messages: caller lacks DVH patch group access (test_88_postOnPatchCauses500_internalServerError reaches DVH patch access check) | None documented | Partially Covered | Medium | Missing happy path. |
| B78 | All-agreement data warehouse patching | Not covered | patch all data warehouse messages: caller lacks DVH patch group access (test_87_postOnPatchalleavtalerCauses500_internalServerError reaches DVH patch access check) | None documented | Partially Covered | Medium | Missing happy path. |
| B79 | Single agreement event publication | Not covered | Not covered | retrieve agreement by id: not executed | Not Covered | High | No matching happy-path workflow or documented failure case is credibly exercised. |
| B80 | All-agreement event publication | Not covered | send event messages for all agreements: caller lacks developer-admin access (test_89_postOnSend_melding_alle_avtalerCauses500_internalServerError reaches developer-admin access check) | None documented | Partially Covered | Medium | Missing happy path. |
| B81 | All-agreement event publication dry-run | Not covered | dry-run event messages for all agreements: caller lacks developer-admin access (test_90_postOnDry_send_melding_alle_avtalerCauses500_internalServerError reaches developer-admin access check) | None documented | Partially Covered | Medium | Missing happy path. |

## Behavior Details

### `B1`: `Role-scoped agreement listing`

- Business goal: Returns a paginated list of agreements visible to the logged-in party, filtered by the query fields.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list accessible agreements` | `GET /avtaler` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list accessible agreements` | the role cookie and token issuer are not a supported combination | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, list accessible agreements: the role cookie and token issuer are not a supported combination.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /avtaler` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for the role cookie and token issuer are not a supported combination.

### `B2`: `Advisor-created agreement`

- Business goal: Creates a new agreement for a participant and employer as a NAV advisor and returns a `Location` pointing to `/avtaler/{avtaleId}`.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `create advisor agreement` | `POST /avtaler` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `create advisor agreement` | advisor lacks write access to the participant | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, create advisor agreement: advisor lacks write access to the participant.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for advisor lacks write access to the participant.

### `B3`: `Arena cleanup agreement creation`

- Business goal: Creates a new advisor agreement and also marks it as an Arena cleanup agreement.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `create Arena cleanup agreement` | `POST /avtaler` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `create Arena cleanup agreement` | agreement creation itself is invalid | Yes | test_12_getOnAvtalerReturns400 uses POST /avtaler?ryddeavtale=true with blank deltakerFnr and gets 400 | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `1/2` covered.
- Missing items: happy path.
- Gap: Missing happy path.
- Recommended tests: add a valid workflow test for `POST /avtaler` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement creation itself is invalid.

### `B4`: `Employer-created agreement`

- Business goal: Creates a new agreement as an employer and returns a `Location` for `/avtaler/{avtaleId}`.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `create employer agreement` | `POST /avtaler/opprett-som-arbeidsgiver` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `create employer agreement` | employer lacks Altinn access for `bedriftNr` and `tiltakstype` | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, create employer agreement: employer lacks Altinn access for `bedriftNr` and `tiltakstype`.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/opprett-som-arbeidsgiver` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for employer lacks Altinn access for `bedriftNr` and `tiltakstype`.

### `B5`: `Advisor-created mentor agreement`

- Business goal: Creates a mentor agreement where the advisor is the creator.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `create mentor agreement as advisor` | `POST /avtaler/opprett-mentor-avtale` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `create mentor agreement as advisor` | participant and mentor have the same national identity number | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, create mentor agreement as advisor: participant and mentor have the same national identity number.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/opprett-mentor-avtale` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for participant and mentor have the same national identity number.

### `B6`: `Employer-created mentor agreement`

- Business goal: Creates a mentor agreement where the employer is the creator.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `create mentor agreement as employer` | `POST /avtaler/opprett-mentor-avtale` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `create mentor agreement as employer` | `avtalerolle` is neither `VEILEDER` nor `ARBEIDSGIVER` | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, create mentor agreement as employer: `avtalerolle` is neither `VEILEDER` nor `ARBEIDSGIVER`.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/opprett-mentor-avtale` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for `avtalerolle` is neither `VEILEDER` nor `ARBEIDSGIVER`.

### `B7`: `Check participant overlap`

- Business goal: Returns existing agreements that overlap the participant, measure type, and optional period.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `check participant overlap` | `GET /avtaler/deltaker-allerede-paa-tiltak` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `check participant overlap` | `avtaleId` is supplied but is not a UUID | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, check participant overlap: `avtaleId` is supplied but is not a UUID.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /avtaler/deltaker-allerede-paa-tiltak` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for `avtaleId` is supplied but is not a UUID.

### `B8`: `Saved agreement search registration and replay`

- Business goal: Persist a search filter and replay it later through its generated search id.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `search agreements and save search` | `POST /avtaler/sok` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |
| 2 | `replay saved agreement search` | `GET /avtaler/sok` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `search agreements and save search` | unsupported role/token combination | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |
| `replay saved agreement search` | `sokId` is unknown | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/3` covered.
- Missing items: happy path, search agreements and save search: unsupported role/token combination, replay saved agreement search: `sokId` is unknown.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/sok`; `GET /avtaler/sok` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for unsupported role/token combination; `sokId` is unknown.

### `B9`: `Agreement detail retrieval by id`

- Business goal: Retrieves the agreement identified by `{avtaleId}` for the selected logged-in party.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `retrieve agreement by id` | agreement does not exist | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, retrieve agreement by id: agreement does not exist.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /avtaler/{avtaleId}` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement does not exist.

### `B10`: `Agreement detail retrieval by agreement number`

- Business goal: Retrieves an agreement by its generated agreement number.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `retrieve agreement by agreement number` | `GET /avtaler/avtaleNr/{avtaleNr}` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `retrieve agreement by agreement number` | `avtaleNr` is unknown | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, retrieve agreement by agreement number: `avtaleNr` is unknown.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /avtaler/avtaleNr/{avtaleNr}` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for `avtaleNr` is unknown.

### `B11`: `Agreement version history retrieval`

- Business goal: Returns all stored content versions for an agreement.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list agreement versions` | `GET /avtaler/{avtaleId}/versjoner` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list agreement versions` | agreement is missing or inaccessible | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, list agreement versions: agreement is missing or inaccessible.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /avtaler/{avtaleId}/versjoner` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is missing or inaccessible.

### `B12`: `Draft agreement update`

- Business goal: Updates mutable agreement content and, for subsidy-backed measures, recalculates subsidy periods.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `update agreement` | `PUT /avtaler/{avtaleId}` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `update agreement` | stale or missing concurrency timestamp | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, update agreement: stale or missing concurrency timestamp.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `PUT /avtaler/{avtaleId}` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for stale or missing concurrency timestamp.

### `B13`: `Draft agreement update dry-run`

- Business goal: Validates and returns the would-be updated agreement without saving it.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `dry-run agreement update` | `PUT /avtaler/{avtaleId}/dry-run` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `dry-run agreement update` | agreement has already been approved | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, dry-run agreement update: agreement has already been approved.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `PUT /avtaler/{avtaleId}/dry-run` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement has already been approved.

### `B14`: `Agreement sharing with a party`

- Business goal: Registers a share event for the selected agreement party and creates corresponding notifications.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `share agreement with party` | `POST /avtaler/{avtaleId}/del-med-avtalepart` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `share agreement with party` | phone number for the selected party is missing or invalid | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, share agreement with party: phone number for the selected party is missing or invalid.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/del-med-avtalepart` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for phone number for the selected party is missing or invalid.

### `B15`: `Participant agreement approval`

- Business goal: Records participant approval on a fully filled agreement.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `approve agreement as participant` | `POST /avtaler/{avtaleId}/godkjenn` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `approve agreement as participant` | required agreement fields are incomplete | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, approve agreement as participant: required agreement fields are incomplete.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/godkjenn` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for required agreement fields are incomplete.

### `B16`: `Employer agreement approval`

- Business goal: Records employer approval on a fully filled agreement.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `approve agreement as employer` | `POST /avtaler/{avtaleId}/godkjenn` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `approve agreement as employer` | employer has already approved | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, approve agreement as employer: employer has already approved.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/godkjenn` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for employer has already approved.

### `B17`: `Mentor confidentiality signing`

- Business goal: Records that the mentor has signed the confidentiality declaration.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `sign mentor confidentiality declaration` | `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `sign mentor confidentiality declaration` | caller is not logged in as mentor | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, sign mentor confidentiality declaration: caller is not logged in as mentor.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for caller is not logged in as mentor.

### `B18`: `Advisor final approval`

- Business goal: Records advisor approval after participant and employer have approved. For measures not requiring decision-maker approval, this also makes the agreement entered.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `approve agreement as advisor` | `POST /avtaler/{avtaleId}/godkjenn` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `approve agreement as advisor` | participant or employer approval is missing | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, approve agreement as advisor: participant or employer approval is missing.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/godkjenn` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for participant or employer approval is missing.

### `B19`: `Advisor approval on behalf of participant`

- Business goal: Advisor records both advisor approval and participant approval on behalf of the participant.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `approve on behalf of participant` | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `approve on behalf of participant` | no on-behalf reason is selected | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, approve on behalf of participant: no on-behalf reason is selected.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for no on-behalf reason is selected.

### `B20`: `Advisor approval on behalf of employer`

- Business goal: Advisor records both advisor approval and employer approval on behalf of the employer.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `approve on behalf of employer` | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `approve on behalf of employer` | measure type is not supported for employer on-behalf approval | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, approve on behalf of employer: measure type is not supported for employer on-behalf approval.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for measure type is not supported for employer on-behalf approval.

### `B21`: `Advisor approval on behalf of participant and employer`

- Business goal: Advisor records advisor, participant, and employer approvals in one operation.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `approve on behalf of participant and employer` | `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `approve on behalf of participant and employer` | participant or employer has already approved | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, approve on behalf of participant and employer: participant or employer has already approved.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for participant or employer has already approved.

### `B22`: `Approval revocation`

- Business goal: Clears existing participant, employer, and advisor approvals on an agreement that has not yet been entered.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `revoke approvals` | `POST /avtaler/{avtaleId}/opphev-godkjenninger` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `revoke approvals` | no approvals exist | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, revoke approvals: no approvals exist.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/opphev-godkjenninger` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for no approvals exist.

### `B23`: `After-registration eligibility marking`

- Business goal: Toggles an unentered agreement so it is approved for after-registration.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `mark agreement eligible for after-registration` | `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `mark agreement eligible for after-registration` | agreement is already entered | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, mark agreement eligible for after-registration: agreement is already entered.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is already entered.

### `B24`: `After-registration eligibility removal`

- Business goal: Toggles an unentered agreement so it is no longer approved for after-registration.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `remove after-registration eligibility` | `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `remove after-registration eligibility` | decision-maker access is missing | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, remove after-registration eligibility: decision-maker access is missing.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for decision-maker access is missing.

### `B25`: `Subsidy period approval`

- Business goal: Decision-maker approves the current subsidy period; for the first decision-maker-controlled period, this also enters the agreement.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `approve subsidy period` | `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `approve subsidy period` | `enhet` is not four digits | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, approve subsidy period: `enhet` is not four digits.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for `enhet` is not four digits.

### `B26`: `Subsidy period rejection`

- Business goal: Decision-maker rejects the current subsidy period with rejection causes and explanation.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `reject subsidy period` | `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `reject subsidy period` | rejection explanation is blank | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, reject subsidy period: rejection explanation is blank.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for rejection explanation is blank.

### `B27`: `Rejected subsidy period return`

- Business goal: Deactivates active rejected subsidy periods and creates new unhandled periods for correction.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `send rejected subsidy period back` | `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `send rejected subsidy period back` | agreement is annulled or interrupted | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, send rejected subsidy period back: agreement is annulled or interrupted.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is annulled or interrupted.

### `B28`: `Agreement shortening`

- Business goal: Creates a new approved version with an earlier end date and adjusts subsidy periods.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `shorten agreement` | `POST /avtaler/{avtaleId}/forkort` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `shorten agreement` | new end date is not before current end date | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, shorten agreement: new end date is not before current end date.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/forkort` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for new end date is not before current end date.

### `B29`: `Dry-run agreement shortening`

- Business goal: Returns the would-be shortened agreement without saving it.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `dry-run agreement shortening` | `POST /avtaler/{avtaleId}/forkort-dry-run` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `dry-run agreement shortening` | agreement is not advisor-approved | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, dry-run agreement shortening: agreement is not advisor-approved.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/forkort-dry-run` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is not advisor-approved.

### `B30`: `Agreement extension`

- Business goal: Creates a new approved version with a later end date and adds or recalculates subsidy periods.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `extend agreement` | `POST /avtaler/{avtaleId}/forleng` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `extend agreement` | new end date is not after current end date | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, extend agreement: new end date is not after current end date.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/forleng` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for new end date is not after current end date.

### `B31`: `Dry-run agreement extension`

- Business goal: Returns the would-be extended agreement without saving it.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `dry-run agreement extension` | `POST /avtaler/{avtaleId}/forleng-dry-run` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `dry-run agreement extension` | agreement is not advisor-approved | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, dry-run agreement extension: agreement is not advisor-approved.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/forleng-dry-run` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is not advisor-approved.

### `B32`: `Subsidy calculation change`

- Business goal: Creates a new approved version with changed monthly salary, holiday pay rate, employer tax, and pension rate, then recalculates unhandled subsidy amounts.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `change subsidy calculation` | `POST /avtaler/{avtaleId}/endre-tilskuddsberegning` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `change subsidy calculation` | agreement is not approved by advisor | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, change subsidy calculation: agreement is not approved by advisor.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/endre-tilskuddsberegning` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is not approved by advisor.

### `B33`: `Dry-run subsidy calculation change`

- Business goal: Returns the would-be updated agreement after subsidy calculation changes without saving.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `dry-run subsidy calculation change` | `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `dry-run subsidy calculation change` | required calculation input is missing | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, dry-run subsidy calculation change: required calculation input is missing.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for required calculation input is missing.

### `B34`: `Contact information change`

- Business goal: Creates a new approved version with changed participant, advisor, employer, and refund contact information.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `change contact information` | `POST /avtaler/{avtaleId}/endre-kontaktinfo` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `change contact information` | required contact field is missing | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, change contact information: required contact field is missing.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/endre-kontaktinfo` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for required contact field is missing.

### `B35`: `Job description change`

- Business goal: Creates a new approved version with changed title, work tasks, occupation code, position percent, and days per week.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `change job description` | `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `change job description` | agreement is not advisor-approved | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, change job description: agreement is not advisor-approved.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is not advisor-approved.

### `B36`: `Follow-up and adaptation text change`

- Business goal: Creates a new approved version with changed follow-up and adaptation descriptions.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `change follow-up and adaptation text` | `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `change follow-up and adaptation text` | follow-up or adaptation text is missing | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, change follow-up and adaptation text: follow-up or adaptation text is missing.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for follow-up or adaptation text is missing.

### `B37`: `Work-training goal replacement`

- Business goal: Replaces goals on an approved work-training agreement.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `change work-training goals` | `POST /avtaler/{avtaleId}/endre-maal` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `change work-training goals` | agreement is not work training | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, change work-training goals: agreement is not work training.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/endre-maal` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is not work training.

### `B38`: `Inclusion subsidy expense replacement`

- Business goal: Replaces inclusion subsidy expense lines on an approved inclusion subsidy agreement.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `change inclusion subsidy expenses` | `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `change inclusion subsidy expenses` | total inclusion subsidy amount is too high | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, change inclusion subsidy expenses: total inclusion subsidy amount is too high.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for total inclusion subsidy amount is too high.

### `B39`: `Mentor details change`

- Business goal: Creates a new approved version with changed mentor name, phone, tasks, hours, and hourly wage.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `change mentor details` | `POST /avtaler/{avtaleId}/endre-om-mentor` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `change mentor details` | agreement is not a mentor agreement | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, change mentor details: agreement is not a mentor agreement.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/endre-om-mentor` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is not a mentor agreement.

### `B40`: `Cost center change`

- Business goal: Sets the cost-center unit and unit name on unhandled or rejected subsidy periods.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `change cost center` | `POST /avtaler/{avtaleId}/endre-kostnadssted` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `change cost center` | norg2 does not return a unit for `enhet` | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, change cost center: norg2 does not return a unit for `enhet`.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/endre-kostnadssted` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for norg2 does not return a unit for `enhet`.

### `B41`: `Arena migration date adjustment`

- Business goal: Recomputes subsidy periods around a migration date and saves or updates the agreement’s Arena cleanup marker.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `adjust Arena migration date` | `POST /avtaler/{avtaleId}/juster-arena-migreringsdato` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `adjust Arena migration date` | agreement is already entered | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, adjust Arena migration date: agreement is already entered.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/juster-arena-migreringsdato` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is already entered.

### `B42`: `Dry-run Arena migration date adjustment`

- Business goal: Returns the would-be agreement after recalculating periods around the migration date without saving it.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `dry-run Arena migration date adjustment` | `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `dry-run Arena migration date adjustment` | agreement is missing | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, dry-run Arena migration date adjustment: agreement is missing.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is missing.

### `B43`: `Employer account number lookup`

- Business goal: Returns the employer’s bank account number for the agreement’s company.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `get employer account number` | `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `get employer account number` | account register has no company account | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, get employer account number: account register has no company account.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for account register has no company account.

### `B44`: `Agreement PDF download`

- Business goal: Returns a PDF representation of an advisor-approved agreement.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `download agreement PDF` | `GET /avtaler/{avtaleId}/pdf` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `download agreement PDF` | agreement is not approved by advisor | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, download agreement PDF: agreement is not approved by advisor.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /avtaler/{avtaleId}/pdf` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is not approved by advisor.

### `B45`: `Salesforce dialog visibility check`

- Business goal: Returns `true` when the agreement belongs to a configured Salesforce office, has `MIDLERTIDIG_LONNSTILSKUDD`, and is `GJENNOMFØRES` or `AVSLUTTET`; otherwise returns `false`.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `check Salesforce dialog visibility` | `GET /avtaler/{avtaleId}/vis-salesforce-dialog` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `check Salesforce dialog visibility` | agreement is missing or inaccessible | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, check Salesforce dialog visibility: agreement is missing or inaccessible.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /avtaler/{avtaleId}/vis-salesforce-dialog` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement is missing or inaccessible.

### `B46`: `Follow-up unit refresh`

- Business goal: Refreshes participant data, follow-up status, geographic unit, and follow-up unit name, then saves the agreement.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `refresh follow-up unit` | `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `refresh follow-up unit` | participant has protected address code 6 when person data is refreshed | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, refresh follow-up unit: participant has protected address code 6 when person data is refreshed.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for participant has protected address code 6 when person data is refreshed.

### `B47`: `Advisor takeover of agreement`

- Business goal: Assigns the logged-in advisor as the agreement’s advisor and refreshes follow-up data.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `take over agreement as advisor` | `PUT /avtaler/{avtaleId}/overta` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `take over agreement as advisor` | logged-in advisor is already the agreement advisor | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, take over agreement as advisor: logged-in advisor is already the agreement advisor.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `PUT /avtaler/{avtaleId}/overta` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for logged-in advisor is already the agreement advisor.

### `B48`: `Agreement annulment`

- Business goal: Annuls an agreement and annuls/removes eligible subsidy periods; if reason is `Feilregistrering`, the agreement is also marked as incorrectly registered.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `annul agreement` | `POST /avtaler/{avtaleId}/annuller` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `annul agreement` | agreement contains a paid subsidy period | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, annul agreement: agreement contains a paid subsidy period.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/annuller` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement contains a paid subsidy period.

### `B49`: `Agreement soft deletion`

- Business goal: Marks the agreement as deleted/hidden.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `soft-delete agreement` | `POST /avtaler/{avtaleId}/slettemerk` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `soft-delete agreement` | advisor is not configured as a delete-marker admin | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, soft-delete agreement: advisor is not configured as a delete-marker admin.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /avtaler/{avtaleId}/slettemerk` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for advisor is not configured as a delete-marker admin.

### `B50`: `Employer Min Side agreement listing`

- Business goal: Returns all agreements for a company that the logged-in employer can view.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list employer agreements` | `GET /avtaler/min-side-arbeidsgiver` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list employer agreements` | caller is not an employer token/role | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, list employer agreements: caller is not an employer token/role.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /avtaler/min-side-arbeidsgiver` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for caller is not an employer token/role.

### `B51`: `Decision-maker work queue listing`

- Business goal: Returns paginated agreements whose subsidy periods match decision-maker filters and the decision-maker’s NAV units.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list decision-maker agreements` | `GET /avtaler/beslutter-liste` | No | No credible success workflow evidence | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list decision-maker agreements` | decision-maker has no NAV units | No | No direct test reaches this documented failure condition | AvtaleController line 1.0% (3/301); branch 0.0% (0/40) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, list decision-maker agreements: decision-maker has no NAV units.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /avtaler/beslutter-liste` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for decision-maker has no NAV units.

### `B52`: `Logged-in user context lookup`

- Business goal: Returns role-specific information for the logged-in user.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `get logged-in user` | `GET /innlogget-bruker` | No | No credible success workflow evidence | InnloggetBrukerController line 75.0% (3/4); branch n/a |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `get logged-in user` | `innlogget-part` cookie is absent | Yes | test_10_getOnInnlogget_brukerReturns400 calls GET /innlogget-bruker without innlogget-part and gets 400 | InnloggetBrukerController line 75.0% (3/4); branch n/a |

- Coverage item summary: `1/2` covered.
- Missing items: happy path.
- Gap: Missing happy path.
- Recommended tests: add a valid workflow test for `GET /innlogget-bruker` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for `innlogget-part` cookie is absent.

### `B53`: `Employer organization lookup`

- Business goal: Returns organization data for an employer unit.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `look up organization` | `GET /organisasjoner` | No | No credible success workflow evidence | OrganisasjonController line 50.0% (1/2); branch n/a |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `look up organization` | ereg does not find the unit | No | No direct test reaches this documented failure condition | OrganisasjonController line 50.0% (1/2); branch n/a |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, look up organization: ereg does not find the unit.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /organisasjoner` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for ereg does not find the unit.

### `B54`: `Altinn rights request URL lookup`

- Business goal: Returns URLs that let an employer request Altinn rights for each supported measure type.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `get Altinn rights request URLs` | `GET /be-om-altinn-rettighet-urler` | Yes | test_3_getOnBe_om_altinn_rettighet_urlerReturnsObject | BeOmAltinnRettighetUrlerController line 100.0% (9/9); branch n/a |

- Happy-path item: `Covered`; direct 2xx response assertions exercise the documented lookup/probe behavior, but no failure branch is covered for this behavior.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `get Altinn rights request URLs` | Authentication, authorization, parameter binding, or external dependency failure. | No | No direct test reaches this documented failure condition | BeOmAltinnRettighetUrlerController line 100.0% (9/9); branch n/a |

- Coverage item summary: `1/2` covered.
- Missing items: get Altinn rights request URLs: Authentication, authorization, parameter binding, or external dependency failure..
- Gap: Missing failure cases: Authentication, authorization, parameter binding, or external dependency failure..
- Recommended tests: add a valid workflow test for `GET /be-om-altinn-rettighet-urler` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for Authentication, authorization, parameter binding, or external dependency failure..

### `B55`: `Combined code-list lookup`

- Business goal: Returns both measure types and agreement statuses.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `get all code lists` | `GET /kodeverk` | Yes | test_1_getOnKodeverkReturnsObject | KodeverkController line 100.0% (20/20); branch 100.0% (2/2) |

- Happy-path item: `Covered`; direct 2xx response assertions exercise the documented lookup/probe behavior, but no failure branch is covered for this behavior.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `get all code lists` | Authentication, authorization, parameter binding, or external dependency failure. | No | No direct test reaches this documented failure condition | KodeverkController line 100.0% (20/20); branch 100.0% (2/2) |

- Coverage item summary: `1/2` covered.
- Missing items: get all code lists: Authentication, authorization, parameter binding, or external dependency failure..
- Gap: Missing failure cases: Authentication, authorization, parameter binding, or external dependency failure..
- Recommended tests: add a valid workflow test for `GET /kodeverk` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for Authentication, authorization, parameter binding, or external dependency failure..

### `B56`: `Agreement status code-list lookup`

- Business goal: Returns all `Status` enum names and descriptions.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `get status code list` | `GET /kodeverk/statuser` | Yes | test_26_getOnStatuserReturns7Elements | KodeverkController line 100.0% (20/20); branch 100.0% (2/2) |

- Happy-path item: `Covered`; direct 2xx response assertions exercise the documented lookup/probe behavior, but no failure branch is covered for this behavior.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `get status code list` | Authentication, authorization, parameter binding, or external dependency failure. | No | No direct test reaches this documented failure condition | KodeverkController line 100.0% (20/20); branch 100.0% (2/2) |

- Coverage item summary: `1/2` covered.
- Missing items: get status code list: Authentication, authorization, parameter binding, or external dependency failure..
- Gap: Missing failure cases: Authentication, authorization, parameter binding, or external dependency failure..
- Recommended tests: add a valid workflow test for `GET /kodeverk/statuser` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for Authentication, authorization, parameter binding, or external dependency failure..

### `B57`: `Measure type code-list lookup`

- Business goal: Returns all `Tiltakstype` enum names, descriptions, treatment themes, and optional Arena codes.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `get measure type code list` | `GET /kodeverk/tiltakstyper` | Yes | test_23/test_25/test_29/test_30_getOnTiltakstyperReturns6Elements | KodeverkController line 100.0% (20/20); branch 100.0% (2/2) |

- Happy-path item: `Covered`; direct 2xx response assertions exercise the documented lookup/probe behavior, but no failure branch is covered for this behavior.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `get measure type code list` | Authentication, authorization, parameter binding, or external dependency failure. | No | No direct test reaches this documented failure condition | KodeverkController line 100.0% (20/20); branch 100.0% (2/2) |

- Coverage item summary: `1/2` covered.
- Missing items: get measure type code list: Authentication, authorization, parameter binding, or external dependency failure..
- Gap: Missing failure cases: Authentication, authorization, parameter binding, or external dependency failure..
- Recommended tests: add a valid workflow test for `GET /kodeverk/tiltakstyper` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for Authentication, authorization, parameter binding, or external dependency failure..

### `B58`: `Feature toggle evaluation`

- Business goal: Returns enabled/disabled values for requested feature names.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `evaluate feature toggles` | `GET /feature` | Yes | test_2_getOnFeatureReturnsObject; test_4_getOnFeatureReturnsObject | FeatureToggleController line 100.0% (5/5); branch n/a |

- Happy-path item: `Covered`; direct 2xx response assertions exercise the documented lookup/probe behavior, but no failure branch is covered for this behavior.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `evaluate feature toggles` | Authentication, authorization, parameter binding, or external dependency failure. | No | No direct test reaches this documented failure condition | FeatureToggleController line 100.0% (5/5); branch n/a |

- Coverage item summary: `1/2` covered.
- Missing items: evaluate feature toggles: Authentication, authorization, parameter binding, or external dependency failure..
- Gap: Missing failure cases: Authentication, authorization, parameter binding, or external dependency failure..
- Recommended tests: add a valid workflow test for `GET /feature` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for Authentication, authorization, parameter binding, or external dependency failure..

### `B59`: `Feature variant lookup`

- Business goal: Returns Unleash variant objects for requested feature names.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `get feature variants` | `GET /feature/variant` | Yes | test_27_getOnFeatureVariantWithQueryParamsReturnsObject; test_28_getOnFeatureVariantWithQueryParamReturnsObject | FeatureToggleController line 100.0% (5/5); branch n/a |

- Happy-path item: `Covered`; direct 2xx response assertions exercise the documented lookup/probe behavior, but no failure branch is covered for this behavior.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `get feature variants` | Authentication, authorization, parameter binding, or external dependency failure. | No | No direct test reaches this documented failure condition | FeatureToggleController line 100.0% (5/5); branch n/a |

- Coverage item summary: `1/2` covered.
- Missing items: get feature variants: Authentication, authorization, parameter binding, or external dependency failure..
- Gap: Missing failure cases: Authentication, authorization, parameter binding, or external dependency failure..
- Recommended tests: add a valid workflow test for `GET /feature/variant` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for Authentication, authorization, parameter binding, or external dependency failure..

### `B60`: `Internal health probe`

- Business goal: Returns `ok` if the database query succeeds.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `run health check` | `GET /internal/healthcheck` | Yes | test_24_getOnHealthcheckReturnsContent | HealthCheckController line 100.0% (4/4); branch n/a |

- Happy-path item: `Covered`; direct 2xx response assertions exercise the documented lookup/probe behavior, but no failure branch is covered for this behavior.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `run health check` | database query fails | No | No direct test reaches this documented failure condition | HealthCheckController line 100.0% (4/4); branch n/a |

- Coverage item summary: `1/2` covered.
- Missing items: run health check: database query fails.
- Gap: Missing failure cases: database query fails.
- Recommended tests: add a valid workflow test for `GET /internal/healthcheck` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for database query fails.

### `B61`: `Overview notification listing`

- Business goal: Returns unread bell notifications for the logged-in party’s identifiers.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list overview notifications` | `GET /varsler/oversikt` | No | No credible success workflow evidence | VarselController line 6.2% (1/16); branch n/a |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list overview notifications` | unsupported role/token combination | No | No direct test reaches this documented failure condition | VarselController line 6.2% (1/16); branch n/a |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, list overview notifications: unsupported role/token combination.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /varsler/oversikt` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for unsupported role/token combination.

### `B62`: `Agreement modal notification listing`

- Business goal: Returns unread bell notifications for a specific agreement and logged-in party.
- Status: `Partially Covered`
- Confidence: `Low`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list agreement modal notifications` | `GET /varsler/avtale-modal` | No | No credible success workflow evidence | VarselController line 6.2% (1/16); branch n/a |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list agreement modal notifications` | Authentication, authorization, parameter binding, or external dependency failure. | Yes | test_41_getOnAvtale_modalReturns400 sends non-UUID avtaleId and gets 400 | VarselController line 6.2% (1/16); branch n/a |

- Coverage item summary: `1/2` covered.
- Missing items: happy path.
- Gap: Missing happy path.
- Recommended tests: add a valid workflow test for `GET /varsler/avtale-modal` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for Authentication, authorization, parameter binding, or external dependency failure..

### `B63`: `Agreement notification log listing`

- Business goal: Returns all notifications for a specific agreement and receiver role.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list agreement notification log` | `GET /varsler/avtale-logg` | No | No credible success workflow evidence | VarselController line 6.2% (1/16); branch n/a |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list agreement notification log` | agreement id is unknown | No | No direct test reaches this documented failure condition | VarselController line 6.2% (1/16); branch n/a |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, list agreement notification log: agreement id is unknown.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /varsler/avtale-logg` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement id is unknown.

### `B64`: `Single notification read marking`

- Business goal: Mark one notification as read for the logged-in party.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list overview notifications` | `GET /varsler/oversikt` | No | No credible success workflow evidence | VarselController line 6.2% (1/16); branch n/a |
| 2 | `mark notification as read` | `POST /varsler/{varselId}/sett-til-lest` | No | No credible success workflow evidence | VarselController line 6.2% (1/16); branch n/a |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `list overview notifications` | `GET /varsler/oversikt` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list overview notifications` | unsupported role/token combination | No | No direct test reaches this documented failure condition | VarselController line 6.2% (1/16); branch n/a |
| `mark notification as read` | notification does not belong to logged-in party | No | No direct test reaches this documented failure condition | VarselController line 6.2% (1/16); branch n/a |

- Coverage item summary: `0/3` covered.
- Missing items: happy path, list overview notifications: unsupported role/token combination, mark notification as read: notification does not belong to logged-in party.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /varsler/oversikt`; `POST /varsler/{varselId}/sett-til-lest` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for unsupported role/token combination; notification does not belong to logged-in party.

### `B65`: `Bulk notification read marking`

- Business goal: Mark several caller-owned notifications as read in one request.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list overview notifications` | `GET /varsler/oversikt` | No | No credible success workflow evidence | VarselController line 6.2% (1/16); branch n/a |
| 2 | `mark multiple notifications as read` | `POST /varsler/sett-alle-til-lest` | No | No credible success workflow evidence | VarselController line 6.2% (1/16); branch n/a |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `list overview notifications` | `GET /varsler/oversikt` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list overview notifications` | unsupported role/token combination | No | No direct test reaches this documented failure condition | VarselController line 6.2% (1/16); branch n/a |
| `mark multiple notifications as read` | any body id is not readable by the logged-in party | No | No direct test reaches this documented failure condition | VarselController line 6.2% (1/16); branch n/a |

- Coverage item summary: `0/3` covered.
- Missing items: happy path, list overview notifications: unsupported role/token combination, mark multiple notifications as read: any body id is not readable by the logged-in party.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `GET /varsler/oversikt`; `POST /varsler/sett-alle-til-lest` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for unsupported role/token combination; any body id is not readable by the logged-in party.

### `B66`: `Journal export and completion marking`

- Business goal: Export unjournaled agreement versions and mark the consumed versions as journaled.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `list unjournaled agreements` | `GET /internal/avtaler` | No | No credible success workflow evidence | InternalAvtaleController line 26.3% (5/19); branch n/a |
| 2 | `mark agreement versions as journaled` | `PUT /internal/avtaler` | No | No credible success workflow evidence | InternalAvtaleController line 26.3% (5/19); branch n/a |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `list unjournaled agreements` | `GET /internal/avtaler` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `list unjournaled agreements` | caller is not the configured system user | Yes | test_83_getOnAvtalerReturns403 calls GET /internal/avtaler with system token but not configured system user | InternalAvtaleController line 26.3% (5/19); branch n/a |
| `mark agreement versions as journaled` | caller is not the configured system user | No | No direct test reaches this documented failure condition | InternalAvtaleController line 26.3% (5/19); branch n/a |

- Coverage item summary: `1/3` covered.
- Missing items: happy path, mark agreement versions as journaled: caller is not the configured system user.
- Gap: Missing happy path and failure cases: caller is not the configured system user.
- Recommended tests: add a valid workflow test for `GET /internal/avtaler`; `PUT /internal/avtaler` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for caller is not the configured system user; caller is not the configured system user.

### `B67`: `Selected agreement wage-subsidy recalculation`

- Business goal: Recalculates missing wage subsidy totals for each selected agreement.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `recalculate wage subsidy` | `POST /utvikler-admin/reberegn` | No | No credible success workflow evidence | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `recalculate wage subsidy` | caller lacks developer-admin access | Yes | test_20_postOnReberegnCauses500_internalServerError reaches developer-admin access check with AAD token lacking the group | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Coverage item summary: `1/2` covered.
- Missing items: happy path.
- Gap: Missing happy path.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/reberegn` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for caller lacks developer-admin access.

### `B68`: `Missing reduced-percent date repair`

- Business goal: Finds entered permanent wage-subsidy agreements missing reduced-percent data and recalculates reduced values and migrated periods.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `fix missing reduced-percent date` | `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}` | No | No credible success workflow evidence | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `fix missing reduced-percent date` | caller lacks developer-admin access | Yes | test_85_postOnReberegn_mangler_dato_for_redusert_prosCauses500_internalServerError reaches developer-admin access check | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Coverage item summary: `1/2` covered.
- Missing items: happy path.
- Gap: Missing happy path.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for caller lacks developer-admin access.

### `B69`: `Dry-run missing reduced-percent date fix`

- Business goal: Counts/logs permanent wage-subsidy agreements that would be repaired, without saving changes.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `dry-run missing reduced-percent date fix` | `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}` | No | No credible success workflow evidence | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `dry-run missing reduced-percent date fix` | caller lacks developer-admin access | Yes | test_86_postOnReberegn_mangler_dato_for_redusert_prosent_dry_runCauses500_internalServerError reaches developer-admin access check | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Coverage item summary: `1/2` covered.
- Missing items: happy path.
- Gap: Missing happy path.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for caller lacks developer-admin access.

### `B70`: `Admin subsidy-period generation for one agreement`

- Business goal: Generates subsidy periods for one agreement after an Arena migration date.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `generate subsidy periods for agreement` | `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}` | No | No credible success workflow evidence | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `generate subsidy periods for agreement` | agreement id is unknown | No | No direct test reaches this documented failure condition | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, generate subsidy periods for agreement: agreement id is unknown.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement id is unknown.

### `B71`: `Unhandled subsidy-period recalculation`

- Business goal: Removes unhandled periods and recreates them from the first unhandled point through agreement end.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `recalculate unhandled subsidy periods` | `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}` | No | No credible success workflow evidence | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `recalculate unhandled subsidy periods` | agreement type is not subsidy-backed | No | No direct test reaches this documented failure condition | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, recalculate unhandled subsidy periods: agreement type is not subsidy-backed.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement type is not subsidy-backed.

### `B72`: `Subsidy-period date-order diagnostic`

- Business goal: Scans temporary wage-subsidy agreements and logs periods whose start date is earlier than the previous sequence number’s start date.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `find subsidy period date-order problems` | `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer` | No | No credible success workflow evidence | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `find subsidy period date-order problems` | caller lacks developer-admin access | Yes | test_21_postOnFinn_avtaler_med_tilskuddsperioder_feil_datoerCauses500_internalServerError reaches developer-admin access check | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Coverage item summary: `1/2` covered.
- Missing items: happy path.
- Gap: Missing happy path.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for caller lacks developer-admin access.

### `B73`: `Subsidy-period annulment`

- Business goal: Marks a subsidy period as annulled and emits an annulment event unless its refund status is expired.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `annul subsidy period` | `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}` | No | No credible success workflow evidence | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `annul subsidy period` | subsidy period id is unknown | No | No direct test reaches this documented failure condition | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, annul subsidy period: subsidy period id is unknown.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for subsidy period id is unknown.

### `B74`: `Annul and resend approved subsidy period`

- Business goal: Annuls an existing subsidy period and creates a replacement period with approved status, reusing approval metadata.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `annul and resend approved subsidy period` | `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}` | No | No credible success workflow evidence | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `annul and resend approved subsidy period` | subsidy period id is unknown | No | No direct test reaches this documented failure condition | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, annul and resend approved subsidy period: subsidy period id is unknown.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for subsidy period id is unknown.

### `B75`: `Annul and generate unhandled subsidy period`

- Business goal: Annuls an existing subsidy period and creates a replacement with unhandled status.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `annul and generate unhandled subsidy period` | `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}` | No | No credible success workflow evidence | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `annul and generate unhandled subsidy period` | agreement measure is not subsidy-backed | No | No direct test reaches this documented failure condition | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, annul and generate unhandled subsidy period: agreement measure is not subsidy-backed.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement measure is not subsidy-backed.

### `B76`: `Annul and generate Arena-treated periods`

- Business goal: Annuls all subsidy periods on an agreement ending before `{dato}` and creates replacements with `BEHANDLET_I_ARENA` status.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `annul and generate Arena-treated periods` | `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}` | No | No credible success workflow evidence | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `annul and generate Arena-treated periods` | agreement id is unknown | No | No direct test reaches this documented failure condition | AdminController line 1.8% (2/112); branch 0.0% (0/36) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, annul and generate Arena-treated periods: agreement id is unknown.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement id is unknown.

### `B77`: `Selected data warehouse patching`

- Business goal: Creates DVH patch message entities for selected agreement ids found in the repository.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `patch selected data warehouse messages` | `POST /utvikler-admin/dvh-melding/patch` | No | No credible success workflow evidence | InternalDvhMeldingProdusentController line 21.1% (4/19); branch 0.0% (0/2) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `patch selected data warehouse messages` | caller lacks DVH patch group access | Yes | test_88_postOnPatchCauses500_internalServerError reaches DVH patch access check | InternalDvhMeldingProdusentController line 21.1% (4/19); branch 0.0% (0/2) |

- Coverage item summary: `1/2` covered.
- Missing items: happy path.
- Gap: Missing happy path.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/dvh-melding/patch` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for caller lacks DVH patch group access.

### `B78`: `All-agreement data warehouse patching`

- Business goal: Creates DVH patch messages for all agreements.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `patch all data warehouse messages` | `POST /utvikler-admin/dvh-melding/patchalleavtaler` | No | No credible success workflow evidence | InternalDvhMeldingProdusentController line 21.1% (4/19); branch 0.0% (0/2) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `patch all data warehouse messages` | caller lacks DVH patch group access | Yes | test_87_postOnPatchalleavtalerCauses500_internalServerError reaches DVH patch access check | InternalDvhMeldingProdusentController line 21.1% (4/19); branch 0.0% (0/2) |

- Coverage item summary: `1/2` covered.
- Missing items: happy path.
- Gap: Missing happy path.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/dvh-melding/patchalleavtaler` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for caller lacks DVH patch group access.

### `B79`: `Single agreement event publication`

- Business goal: Sends an agreement event message for one existing agreement.
- Status: `Not Covered`
- Confidence: `High`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `send event message for one agreement` | `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}` | No | No credible success workflow evidence | AvtaleHendelseController line 23.5% (4/17); branch 0.0% (0/2) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `retrieve agreement by id` | `GET /avtaler/{avtaleId}` | No | No optional verification test |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `send event message for one agreement` | agreement id is unknown | No | No direct test reaches this documented failure condition | AvtaleHendelseController line 23.5% (4/17); branch 0.0% (0/2) |

- Coverage item summary: `0/2` covered.
- Missing items: happy path, send event message for one agreement: agreement id is unknown.
- Gap: No matching happy-path workflow or documented failure case is credibly exercised.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for agreement id is unknown.

### `B80`: `All-agreement event publication`

- Business goal: Sends agreement event messages for all agreements.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `send event messages for all agreements` | `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler` | No | No credible success workflow evidence | AvtaleHendelseController line 23.5% (4/17); branch 0.0% (0/2) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `send event messages for all agreements` | caller lacks developer-admin access | Yes | test_89_postOnSend_melding_alle_avtalerCauses500_internalServerError reaches developer-admin access check | AvtaleHendelseController line 23.5% (4/17); branch 0.0% (0/2) |

- Coverage item summary: `1/2` covered.
- Missing items: happy path.
- Gap: Missing happy path.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for caller lacks developer-admin access.

### `B81`: `All-agreement event publication dry-run`

- Business goal: Performs the all-agreement event-message operation in dry-run mode.
- Status: `Partially Covered`
- Confidence: `Medium`

| Step | Function Name | Expected Operation | Covered? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|
| 1 | `dry-run event messages for all agreements` | `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler` | No | No credible success workflow evidence | AvtaleHendelseController line 23.5% (4/17); branch 0.0% (0/2) |

- Happy-path item: `Not Covered`; no test performs the documented successful workflow with valid preconditions and value bindings.

| Step | Function Name | Expected Verification | Executed? | Evidence |
|---:|---|---|---|---|
| - | - | None documented | No | Not applicable |

| Failing Function | Failure Condition | Covered? | Test Evidence | JaCoCo Evidence |
|---|---|---|---|---|
| `dry-run event messages for all agreements` | caller lacks developer-admin access | Yes | test_90_postOnDry_send_melding_alle_avtalerCauses500_internalServerError reaches developer-admin access check | AvtaleHendelseController line 23.5% (4/17); branch 0.0% (0/2) |

- Coverage item summary: `1/2` covered.
- Missing items: happy path.
- Gap: Missing happy path.
- Recommended tests: add a valid workflow test for `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler` with domain setup through documented APIs or fixtures, assert response and persisted/read-back business state, and add focused failure tests for caller lacks developer-admin access.

## Cross-Behavior Gaps

- Missing required workflow steps: most stateful agreement behaviors are never exercised on valid aggregates; test inputs typically use invalid IDs or omit required cookies.
- Missing negative paths: most documented domain failures are not intentionally triggered. Negative tests mostly cover framework binding, authorization absence, or malformed request data.
- Tests call endpoints without verifying business outcomes: generated failures usually assert only status, empty body, or generic `status/error/path` JSON fields.
- Direct database setup is not used in the generated suite; no test creates realistic existing agreements, subsidy periods, notification rows, saved searches, or journal candidates before invoking lifecycle behavior.
- Missing read-after-write verification: no creation, mutation, approval, notification read, journal update, or admin repair test follows the operation with a retrieval/state assertion.
- Code covered without business evidence: lookup controllers have strong line coverage, but `AvtaleController`, `AdminController`, and `VarselController` show minimal line and branch coverage and no behavior-level proof.
- Business behaviors with no corresponding success test include all agreement lifecycle behaviors B1-B53, all notification mutation/listing behaviors B61-B65, journal completion B66, and admin repair/publication success workflows B67-B81.
- JaCoCo-covered code lacks behavior-level evidence where access filters and request binding are touched without reaching documented domain transitions.

## Suggested Additional Tests

| Priority | Behavior ID | Test Intent | Minimal Setup | Calls / Operations | Required Assertions | Coverage Type |
|---:|---|---|---|---|---|---|
| 1 | B2/B3/B4 | Create agreements through advisor, Arena cleanup, and employer paths | Mock person, EREG, Altinn and ABAC access; no existing agreement | `POST /avtaler`, `POST /avtaler?ryddeavtale=true`, `POST /avtaler/opprett-som-arbeidsgiver` | 201 Location, persisted agreement fields, creator role, Arena cleanup marker when requested | Success |
| 2 | B15-B18 | Exercise participant, employer, mentor, and advisor approval sequence | Create fully filled agreement using documented creation/update functions | `POST /avtaler/{id}/godkjenn`, mentor signing endpoint, final advisor approval | Approval timestamps, final status, event/notification side effects, failure when prerequisite approval missing | Success |
| 3 | B25-B27 | Cover subsidy-period approve/reject/return lifecycle | Advisor-approved subsidy-backed agreement with active unhandled/rejected period | `godkjenn-tilskuddsperiode`, `avslag-tilskuddsperiode`, `send-tilbake-til-beslutter` | Period status transitions, rejection cause/explanation, first-period agreement entry behavior | Success |
| 4 | B28-B33 | Cover duration and subsidy-calculation changes including dry runs | Approved wage-subsidy agreement with periods | `forkort`, `forkort-dry-run`, `forleng`, `forleng-dry-run`, `endre-tilskuddsberegning*` | Saved vs not-saved behavior, new version, recalculated unhandled periods | Success |
| 5 | B61-B65 | Cover notification listings and read marking | Persist notifications for participant/employer/mentor identifiers and unrelated identifiers | `GET /varsler/*`, `POST /varsler/{id}/sett-til-lest`, `POST /varsler/sett-alle-til-lest` | Only owned unread notifications returned; read flags updated; unrelated notification rejected | Success |
| 6 | B66 | Cover journal export and completion marking | Persist unjournaled agreement versions and a configured system token | `GET /internal/avtaler`, then `PUT /internal/avtaler` with returned ids | Export contains expected versions; journalpostId persisted; repeated export excludes completed versions | Success |
| 7 | B67-B81 | Cover developer-admin and DVH/event success and access-denied branches | Configured admin/DVH groups plus candidate agreements and periods | Each `/utvikler-admin/*` endpoint with authorized and unauthorized callers | Authorized side effects, unauthorized 403, no mutation on denied calls | Regression |
| 8 | B1/B8/B50/B51 | Cover scoped search/listing workflows | Agreements across roles, NAV units, employers, statuses, saved search rows | `GET /avtaler`, `POST /avtaler/sok`, `GET /avtaler/sok`, employer and beslutter lists | Returned rows match caller scope and filters; saved search increments replay metadata | Success |
| 9 | B12-B14/B34-B41/B46-B49 | Cover all mutable agreement edit endpoints | Valid aggregate in the required state for each edit | Each edit endpoint plus follow-up `GET /avtaler/{id}` | Versioning, recalculation, notification/event side effects, and documented invalid-state failures | Success |

## Appendix: Coverage Artifacts Used

### JaCoCo XML Files

- `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/reports/report.xml`
  - Counters extracted from XML root:
    - INSTRUCTION: `8.0% (5120/63843)`
    - BRANCH: `1.6% (127/8084)`
    - LINE: `12.5% (820/6541)`
    - COMPLEXITY: `5.5% (427/7757)`
    - METHOD: `11.1% (409/3691)`
    - CLASS: `32.7% (129/394)`
  - Notes: XML was the primary source for class-level and controller-level evidence. Only one XML report was present, so no overlapping-report union was required.

### JaCoCo CSV Files

- `/Users/yangyuhan/behavior-analyze/tiltaksgjennomforing/reports/report.csv`
  - Rows parsed: `394`
  - INSTRUCTION: `8.0% (5120/63843)`
  - BRANCH: `1.6% (127/8084)`
  - LINE: `12.5% (820/6557)`
  - COMPLEXITY: `5.5% (427/7757)`
  - METHOD: `11.1% (409/3691)`
  - Notes: CSV broadly agrees with XML. CSV line totals differ slightly in missed lines (`5737` CSV missed vs `5721` XML missed), so the report uses XML root totals as primary and CSV as a cross-check.

## Appendix: Test Inventory

The generated suite was parsed test-by-test. To keep the appendix readable, the table groups the 203 HTTP operations by endpoint/status while preserving the test cases that produced each group.

| Endpoint / Operation | Status Counts | Test Cases | Related Behavior IDs | Evidence Quality |
|---|---|---|---|---|
| `GET /avtaler` | 400: 6, 401: 2 | test_11_getOnAvtalerReturns400, test_12_getOnAvtalerReturns400, test_15_getOnAvtalerReturns401, test_18_getOnAvtalerReturns401, test_5_getOnAvtalerReturns400, test_6_getOnAvtalerReturns400, test_7_getOnAvtalerReturns400, test_8_getOnTiltaksgjennomforing_apiAvtalerWithQueryParamReturns400 | B1 | Low |
| `POST /avtaler` | 400: 2, 401: 2, 403: 1, 500: 1 | test_0_postOnAvtalerCauses500_internalServerError, test_11_getOnAvtalerReturns400, test_12_getOnAvtalerReturns400, test_14_postOnAvtalerReturns400, test_18_getOnAvtalerReturns401, test_19_postOnAvtalerReturns401 | B2, B3 | Low |
| `GET /avtaler/avtaleNr/{avtaleNr}` | 400: 1, 401: 1 | test_144_getOnAvtaleNrReturns401, test_95_getOnAvtaleNrReturns400 | B10 | Low |
| `GET /avtaler/beslutter-liste` | 400: 3, 401: 1, 403: 1 | test_35_getOnBeslutter_listeReturns400, test_36_getOnBeslutter_listeReturns400, test_37_getOnBeslutter_listeReturns400, test_71_getOnBeslutter_listeReturns401, test_82_getOnBeslutter_listeReturns403 | B51 | Low |
| `GET /avtaler/deltaker-allerede-paa-tiltak` | 400: 4, 401: 1 | test_33_getOnDeltaker_allerede_paa_tiltakReturns400, test_34_getOnDeltaker_allerede_paa_tiltakReturns400, test_45_getOnDeltaker_allerede_paa_tiltakReturns400, test_46_getOnDeltaker_allerede_paa_tiltakReturns400, test_70_getOnDeltaker_allerede_paa_tiltakReturns401 | B7 | Low |
| `GET /avtaler/min-side-arbeidsgiver` | 400: 2, 401: 1, 403: 1 | test_32_getOnMin_side_arbeidsgiverReturns400, test_43_getOnMin_side_arbeidsgiverReturns400, test_69_getOnMin_side_arbeidsgiverReturns401, test_81_getOnMin_side_arbeidsgiverReturns403 | B50 | Low |
| `POST /avtaler/opprett-mentor-avtale` | 400: 2, 401: 1, 500: 1 | test_22_postOnOpprett_mentor_avtaleCauses500_internalServerError, test_48_postOnOpprett_mentor_avtaleReturns400, test_78_postOnOpprett_mentor_avtaleReturns401 | B5, B6 | Low |
| `POST /avtaler/opprett-som-arbeidsgiver` | 400: 3, 401: 1, 403: 1 | test_54_postOnOpprett_som_arbeidsgiverReturns400, test_55_postOnOpprett_som_arbeidsgiverReturns400, test_77_postOnOpprett_som_arbeidsgiverReturns401, test_84_postOnOpprett_som_arbeidsgiverReturns403 | B4 | Low |
| `GET /avtaler/sok` | 400: 2, 401: 1 | test_38_getOnSokReturns400, test_44_getOnSokReturns400, test_63_getOnSokReturns401 | B8 | Low |
| `POST /avtaler/sok` | 400: 5, 401: 1 | test_47_postOnSokReturns400, test_49_postOnSokReturns400, test_50_postOnSokReturns400, test_51_postOnSokReturns400, test_76_postOnSokReturns401 | B8 | Low |
| `GET /avtaler/{avtaleId}` | 400: 1, 401: 1 | test_39_getOnAvtalReturns400, test_65_getOnAvtalReturns401 | B9 | Low |
| `PUT /avtaler/{avtaleId}` | 400: 6, 401: 1 | test_56_putOnAvtalReturns400, test_57_putOnAvtalReturns400, test_58_putOnAvtalReturns400, test_59_putOnAvtalReturns400, test_60_putOnAvtalReturns400, test_61_putOnAvtalReturns400, test_79_putOnAvtalReturns401 | B12 | Low |
| `POST /avtaler/{avtaleId}/annuller` | 400: 2, 401: 1 | test_118_postOnAnnullerReturns400, test_133_postOnAnnullerReturns400, test_184_postOnAnnullerReturns401 | B48 | Low |
| `POST /avtaler/{avtaleId}/avslag-tilskuddsperiode` | 400: 1, 401: 1 | test_117_postOnAvslag_tilskuddsperiodeReturns400, test_183_postOnAvslag_tilskuddsperiodeReturns401 | B26 | Low |
| `POST /avtaler/{avtaleId}/del-med-avtalepart` | 400: 1, 401: 1 | test_116_postOnDel_med_avtalepartReturns400, test_182_postOnDel_med_avtalepartReturns401 | B14 | Low |
| `PUT /avtaler/{avtaleId}/dry-run` | 400: 1, 401: 1 | test_139_putOnDry_runReturns400, test_186_putOnDry_runReturns401 | B13 | Low |
| `POST /avtaler/{avtaleId}/endre-inkluderingstilskudd` | 400: 1, 401: 1 | test_115_postOnEndre_inkluderingstilskuddReturns400, test_181_postOnEndre_inkluderingstilskuddReturns401 | B38 | Low |
| `POST /avtaler/{avtaleId}/endre-kontaktinfo` | 400: 1, 401: 1 | test_114_postOnEndre_kontaktinfoReturns400, test_180_postOnEndre_kontaktinfoReturns401 | B34 | Low |
| `POST /avtaler/{avtaleId}/endre-kostnadssted` | 400: 1, 401: 1 | test_131_postOnEndre_kostnadsstedReturns400, test_179_postOnEndre_kostnadsstedReturns401 | B40 | Low |
| `POST /avtaler/{avtaleId}/endre-maal` | 400: 1, 401: 1 | test_130_postOnEndre_maalReturns400, test_178_postOnEndre_maalReturns401 | B37 | Low |
| `POST /avtaler/{avtaleId}/endre-om-mentor` | 400: 1, 401: 1 | test_113_postOnEndre_om_mentorReturns400, test_177_postOnEndre_om_mentorReturns401 | B39 | Low |
| `POST /avtaler/{avtaleId}/endre-oppfolging-og-tilrettelegging` | 400: 1, 401: 1 | test_112_postOnEndre_oppfolging_og_tilretteleggingReturns400, test_176_postOnEndre_oppfolging_og_tilretteleggingReturns401 | B36 | Low |
| `POST /avtaler/{avtaleId}/endre-stillingbeskrivelse` | 400: 1, 401: 1 | test_111_postOnEndre_stillingbeskrivelseReturns400, test_175_postOnEndre_stillingbeskrivelseReturns401 | B35 | Low |
| `POST /avtaler/{avtaleId}/endre-tilskuddsberegning` | 400: 1, 401: 1 | test_110_postOnEndre_tilskuddsberegningReturns400, test_173_postOnEndre_tilskuddsberegningReturns401 | B32 | Low |
| `POST /avtaler/{avtaleId}/endre-tilskuddsberegning-dry-run` | 400: 1, 401: 1 | test_129_postOnEndre_tilskuddsberegning_dry_runReturns400, test_174_postOnEndre_tilskuddsberegning_dry_runReturns401 | B33 | Low |
| `POST /avtaler/{avtaleId}/forkort` | 400: 2, 401: 1 | test_108_postOnForkortReturns400, test_137_postOnForkortReturns400, test_171_postOnForkortReturns401 | B28 | Low |
| `POST /avtaler/{avtaleId}/forkort-dry-run` | 400: 2, 401: 1 | test_109_postOnForkort_dry_runReturns400, test_135_postOnForkort_dry_runReturns400, test_172_postOnForkort_dry_runReturns401 | B29 | Low |
| `POST /avtaler/{avtaleId}/forleng` | 400: 2, 401: 1 | test_128_postOnForlengReturns400, test_136_postOnForlengReturns400, test_169_postOnForlengReturns401 | B30 | Low |
| `POST /avtaler/{avtaleId}/forleng-dry-run` | 400: 1, 401: 1 | test_107_postOnForleng_dry_runReturns400, test_170_postOnForleng_dry_runReturns401 | B31 | Low |
| `POST /avtaler/{avtaleId}/godkjenn` | 400: 2, 401: 1 | test_104_postOnGodkjennReturns400, test_105_postOnGodkjennReturns400, test_163_postOnGodkjennReturns401 | B15, B16, B18 | Low |
| `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av` | 400: 1, 401: 1 | test_126_postOnGodkjenn_paa_vegne_avReturns400, test_167_postOnGodkjenn_paa_vegne_avReturns401 | B19 | Low |
| `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-arbeidsgiver` | 400: 1, 401: 1 | test_127_postOnGodkjenn_paa_vegne_av_arbeidsgiverReturns400, test_168_postOnGodkjenn_paa_vegne_av_arbeidsgiverReturns401 | B20 | Low |
| `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker` | 400: 1, 401: 1 | test_125_postOnGodkjenn_paa_vegne_av_deltakerReturns400, test_166_postOnGodkjenn_paa_vegne_av_deltakerReturns401 | No documented business behavior | Low |
| `POST /avtaler/{avtaleId}/godkjenn-paa-vegne-av-deltaker-og-arbeidsgiver` | 400: 1, 401: 1 | test_124_postOnGodkjenn_paa_vegne_av_deltaker_og_arbeidsgiverReturns400, test_165_postOnGodkjenn_paa_vegne_av_deltaker_og_arbeidsgiverReturns401 | B21 | Low |
| `POST /avtaler/{avtaleId}/godkjenn-tilskuddsperiode` | 400: 1, 401: 1 | test_106_postOnGodkjenn_tilskuddsperiodeReturns400, test_164_postOnGodkjenn_tilskuddsperiodeReturns401 | B25 | Low |
| `POST /avtaler/{avtaleId}/juster-arena-migreringsdato` | 400: 1, 401: 1 | test_123_postOnJuster_arena_migreringsdatoReturns400, test_162_postOnJuster_arena_migreringsdatoReturns401 | B41 | Low |
| `POST /avtaler/{avtaleId}/juster-arena-migreringsdato/dry-run` | 400: 1, 401: 1 | test_187_postOnDry_runReturns400, test_194_postOnDry_runReturns401 | B42 | Low |
| `GET /avtaler/{avtaleId}/kontonummer-arbeidsgiver` | 400: 2, 401: 1 | test_143_getOnKontonummer_arbeidsgiverReturns401, test_94_getOnKontonummer_arbeidsgiverReturns400, test_96_getOnKontonummer_arbeidsgiverReturns400 | B43 | Low |
| `POST /avtaler/{avtaleId}/mentorGodkjennTaushetserklæring` | 400: 2, 401: 1 | test_103_postOnMentorGodkjennTaushetserkl_ringReturns400, test_134_postOnMentorGodkjennTaushetserkl_ringReturns400, test_161_postOnMentorGodkjennTaushetserkl_ringReturns401 | B17 | Low |
| `POST /avtaler/{avtaleId}/oppdaterOppfølgingsEnhet` | 400: 1, 401: 1 | test_102_postOnOppdaterOppf_lgingsEnhetReturns400, test_160_postOnOppdaterOppf_lgingsEnhetReturns401 | B46 | Low |
| `POST /avtaler/{avtaleId}/opphev-godkjenninger` | 400: 1, 401: 1 | test_101_postOnOpphev_godkjenningerReturns400, test_159_postOnOpphev_godkjenningerReturns401 | B22 | Low |
| `PUT /avtaler/{avtaleId}/overta` | 400: 1, 401: 1 | test_138_putOnOvertaReturns400, test_185_putOnOvertaReturns401 | B47 | Low |
| `GET /avtaler/{avtaleId}/pdf` | 400: 1, 401: 1 | test_142_getOnPdfReturns401, test_93_getOnPdfReturns400 | B44 | Low |
| `POST /avtaler/{avtaleId}/send-tilbake-til-beslutter` | 400: 1, 401: 1 | test_100_postOnSend_tilbake_til_beslutterReturns400, test_158_postOnSend_tilbake_til_beslutterReturns401 | B27 | Low |
| `POST /avtaler/{avtaleId}/set-om-avtalen-kan-etterregistreres` | 400: 1, 401: 1 | test_157_postOnSet_om_avtalen_kan_etterregistreresReturns401, test_99_postOnSet_om_avtalen_kan_etterregistreresReturns400 | B23, B24 | Low |
| `POST /avtaler/{avtaleId}/slettemerk` | 400: 1, 401: 1 | test_156_postOnSlettemerkReturns401, test_98_postOnSlettemerkReturns400 | B49 | Low |
| `GET /avtaler/{avtaleId}/versjoner` | 400: 1, 401: 1 | test_141_getOnVersjonerReturns401, test_92_getOnVersjonerReturns400 | B11 | Low |
| `GET /avtaler/{avtaleId}/vis-salesforce-dialog` | 400: 1, 401: 1 | test_140_getOnVis_salesforce_dialogReturns401, test_91_getOnVis_salesforce_dialogReturns400 | B45 | Low |
| `GET /be-om-altinn-rettighet-urler` | 200: 1 | test_3_getOnBe_om_altinn_rettighet_urlerReturnsObject | B54 | Medium |
| `GET /feature` | 200: 2 | test_2_getOnFeatureReturnsObject, test_4_getOnFeatureReturnsObject | B58 | Medium |
| `GET /feature/variant` | 200: 2 | test_27_getOnFeatureVariantWithQueryParamsReturnsObject, test_28_getOnFeatureVariantWithQueryParamReturnsObject | B59 | Medium |
| `GET /innlogget-bruker` | 400: 1, 401: 1 | test_10_getOnInnlogget_brukerReturns400, test_17_getOnInnlogget_brukerReturns401 | B52 | Low |
| `GET /internal/avtaler` | 401: 2, 403: 1 | test_64_getOnInternalAvtalerWithQueryParamReturns401, test_72_getOnInternalAvtalerReturns401, test_83_getOnAvtalerReturns403 | B66 | Low |
| `PUT /internal/avtaler` | 400: 1, 401: 1 | test_62_putOnAvtalerReturns400, test_80_putOnAvtalerReturns401 | B66 | Low |
| `GET /internal/healthcheck` | 200: 1 | test_24_getOnHealthcheckReturnsContent | B60 | Medium |
| `GET /kodeverk` | 200: 1 | test_1_getOnKodeverkReturnsObject | B55 | Medium |
| `GET /kodeverk/statuser` | 200: 1 | test_26_getOnStatuserReturns7Elements | B56 | Medium |
| `GET /kodeverk/tiltakstyper` | 200: 4 | test_23_getOnTiltakstyperReturns6Elements, test_25_getOnTiltakstyperReturns6Elements, test_29_getOnTiltakstyperReturns6Elements, test_30_getOnTiltakstyperReturns6Elements | B57 | Medium |
| `GET /organisasjoner` | 400: 2, 401: 1 | test_13_getOnOrganisasjonerReturns400, test_16_getOnOrganisasjonerReturns401, test_9_getOnOrganisasjonerReturns400 | B53 | Low |
| `POST /utvikler-admin/annuller-og-generer-behandlet-i-arena-perioder/{avtaleId}/{dato}` | 400: 1, 401: 1 | test_190_postOnAnnuller_og_generer_behandlet_i_arena_periodReturns400, test_193_postOnAnnuller_og_generer_behandlet_i_arena_periodReturns401 | B76 | Low |
| `POST /utvikler-admin/annuller-og-generer-tilskuddsperiode/{tilskuddsperiodeId}` | 400: 1, 401: 1 | test_122_postOnAnnuller_og_generer_tilskuddsperiodReturns400, test_155_postOnAnnuller_og_generer_tilskuddsperiodReturns401 | B75 | Low |
| `POST /utvikler-admin/annuller-og-resend-tilskuddsperiode/{tilskuddsperiodeId}` | 400: 1, 401: 1 | test_154_postOnAnnuller_og_resend_tilskuddsperiodReturns401, test_97_postOnAnnuller_og_resend_tilskuddsperiodReturns400 | B74 | Low |
| `POST /utvikler-admin/annuller-tilskuddsperiode/{tilskuddsperiodeId}` | 400: 1, 401: 1 | test_121_postOnAnnuller_tilskuddsperiodReturns400, test_153_postOnAnnuller_tilskuddsperiodReturns401 | B73 | Low |
| `POST /utvikler-admin/avtale-hendelse/dry-send-melding-alle-avtaler` | 401: 1, 500: 1 | test_152_postOnDry_send_melding_alle_avtalerReturns401, test_90_postOnDry_send_melding_alle_avtalerCauses500_internalServerError | B81 | Low |
| `POST /utvikler-admin/avtale-hendelse/send-melding-alle-avtaler` | 401: 1, 500: 1 | test_151_postOnSend_melding_alle_avtalerReturns401, test_89_postOnSend_melding_alle_avtalerCauses500_internalServerError | B80 | Low |
| `POST /utvikler-admin/avtale-hendelse/send-melding-en-avtale/{avtaleId}` | 400: 1, 401: 1 | test_189_postOnSend_melding_en_avtalReturns400, test_192_postOnSend_melding_en_avtalReturns401 | B79 | Low |
| `POST /utvikler-admin/dvh-melding/patch` | 400: 2, 401: 1, 500: 1 | test_132_postOnPatchReturns400, test_150_postOnPatchReturns401, test_88_postOnPatchCauses500_internalServerError | B77 | Low |
| `POST /utvikler-admin/dvh-melding/patchalleavtaler` | 401: 1, 500: 1 | test_149_postOnPatchalleavtalerReturns401, test_87_postOnPatchalleavtalerCauses500_internalServerError | B78 | Low |
| `POST /utvikler-admin/finn-avtaler-med-tilskuddsperioder-feil-datoer` | 401: 1, 500: 1 | test_21_postOnFinn_avtaler_med_tilskuddsperioder_feil_datoerCauses500_internalServerError, test_75_postOnFinn_avtaler_med_tilskuddsperioder_feil_datoerReturns401 | B72 | Low |
| `POST /utvikler-admin/lag-tilskuddsperioder-for-en-avtale/{avtaleId}/{migreringsDato}` | 400: 1, 401: 1 | test_188_postOnLag_tilskuddsperioder_for_en_avtalReturns400, test_191_postOnLag_tilskuddsperioder_for_en_avtalReturns401 | B70 | Low |
| `POST /utvikler-admin/reberegn` | 400: 2, 401: 1, 500: 1 | test_20_postOnReberegnCauses500_internalServerError, test_53_postOnReberegnReturns400, test_74_postOnReberegnReturns401 | B67 | Low |
| `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent-dry-run/{migreringsDato}` | 401: 1, 500: 1 | test_148_postOnReberegn_mangler_dato_for_redusert_prosent_dry_runReturns401, test_86_postOnReberegn_mangler_dato_for_redusert_prosent_dry_runCauses500_internalServerError | B69 | Low |
| `POST /utvikler-admin/reberegn-mangler-dato-for-redusert-prosent/{migreringsDato}` | 401: 1, 500: 1 | test_147_postOnReberegn_mangler_dato_for_redusert_prosReturns401, test_85_postOnReberegn_mangler_dato_for_redusert_prosCauses500_internalServerError | B68 | Low |
| `POST /utvikler-admin/reberegn-ubehandlede-tilskuddsperioder/{avtaleId}` | 400: 1, 401: 1 | test_120_postOnReberegn_ubehandlede_tilskuddsperiodReturns400, test_146_postOnReberegn_ubehandlede_tilskuddsperiodReturns401 | B71 | Low |
| `GET /v3/api-docs` | 200: 1 | test_31_getOnApi_docsReturnsObject | No documented business behavior | Low |
| `GET /varsler/avtale-logg` | 400: 1, 401: 1 | test_42_getOnAvtale_loggReturns400, test_68_getOnAvtale_loggReturns401 | B63 | Low |
| `GET /varsler/avtale-modal` | 400: 1, 401: 1 | test_41_getOnAvtale_modalReturns400, test_67_getOnAvtale_modalReturns401 | B62 | Low |
| `GET /varsler/oversikt` | 400: 1, 401: 1 | test_40_getOnOversiktReturns400, test_66_getOnOversiktReturns401 | B61, B64, B65 | Low |
| `POST /varsler/sett-alle-til-lest` | 400: 1, 401: 1 | test_52_postOnSett_alle_til_lestReturns400, test_73_postOnSett_alle_til_lestReturns401 | B65 | Low |
| `POST /varsler/{varselId}/sett-til-lest` | 400: 1, 401: 1 | test_119_postOnSett_til_lestReturns400, test_145_postOnSett_til_lestReturns401 | B64 | Low |

### Read-only notes

- No direct SQL setup or insert statements were present in the generated test file beyond EvoMaster controller database reset/setup hooks.
- The suite calls `controller.resetDatabase(Arrays.asList())` and `controller.resetStateOfSUT()` before each test, so separate tests cannot be stitched into one stateful workflow.
- The OpenAPI docs test (`GET /v3/api-docs`) is included in the corpus summary but not credited to any documented business behavior.
