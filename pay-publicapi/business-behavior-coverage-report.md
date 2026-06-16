# Business Behavior Coverage Report

## Executive Summary

Project under analysis: `/Users/yangyuhan/behavior-analyze/pay-publicapi`

| Item | Result |
|---|---:|
| Business spec | [business-behavior.md](/Users/yangyuhan/behavior-analyze/pay-publicapi/business-behavior.md) |
| Function map | [full-behavior.md](/Users/yangyuhan/behavior-analyze/pay-publicapi/full-behavior.md) |
| Test root analyzed | `/Users/yangyuhan/behavior-analyze/pay-publicapi/tests` |
| Test files / cases | `1` / `36` |
| Source roots analyzed | `src/main/java`, `src/main/resources` |
| JaCoCo XML / CSV | `reports/report.xml`, `reports/report.csv` |
| Documented business behaviors | `19` |
| Covered / Partially / Not / Unclear | `0 / 0 / 19 / 0` |
| Business behavior coverage | `0.0%` |
| Happy-path coverage | `0/19`, `0.0%` |
| Failure-case coverage | `0/50`, `0.0%` |
| Checklist coverage | `0/69` |

JaCoCo XML signal: line `458/4198` (`10.9%`), branch `13/641` (`2.0%`), method `171/1507` (`11.3%`), class `86/243` (`35.4%`). CSV is a close cross-check at line `459/4202`; XML was used as primary.

The generated tests touch payment and refund endpoints, but they do not establish authenticated business context or downstream state. Most assertions are `401` unauthorized or `500` from `AccountAuthenticator`; the only `200` is `/assets/swagger.json`. No test proves a documented workflow, generated ID binding, state transition, persisted outcome, or documented business failure condition.

## Test Corpus Summary

| Area | Count / Summary |
|---|---|
| Test files analyzed | `1` |
| Test cases analyzed | `36` |
| Primary framework | EvoMaster-generated JUnit 5 + REST-assured |
| HTTP operations | `39` |
| Main endpoints exercised | `GET/POST /v1/payments`, payment subresources, `GET /v1/refunds`, `/assets/swagger.json` |
| Positive-path tests | `1` non-business static Swagger request; `0` business positive tests |
| Negative/failure tests | `35` auth/error-path tests; `0` documented business negative tests |
| Tests with business assertions | `0` |
| Tests limited to status/error/static assertions | `36` |

| Test File | Test Cases | Main Behavior Area | Evidence Quality |
|---|---:|---|---|
| [EM_pay_publicapi_True_25_false_false_SPECIFIED_false_0_Test.java](/Users/yangyuhan/behavior-analyze/pay-publicapi/tests/EM_pay_publicapi_True_25_false_false_SPECIFIED_false_0_Test.java) | 36 | Unauthenticated or dummy-auth payment/refund endpoint calls; static Swagger asset | Low |

Endpoint/status distribution: `10 GET /v1/payments`, `10 POST /v1/payments`, `4 GET /v1/refunds`, `14` payment subresource calls, `1 GET /assets/swagger.json`; statuses are `25x 500`, `13x 401`, `1x 200`.

## Function-To-Code Map

| Function Area | Endpoint(s) | Source Evidence |
|---|---|---|
| Agreement search/create/get/cancel | `/v1/agreements`, `/v1/agreements/{agreementId}`, `/cancel` | [AgreementsApiResource.java](/Users/yangyuhan/behavior-analyze/pay-publicapi/src/main/java/uk/gov/pay/api/agreement/resource/AgreementsApiResource.java:60), `AgreementsService` |
| Payment search/read/events/create/cancel/capture | `/v1/payments`, `/v1/payments/{paymentId}`, `/events`, `/cancel`, `/capture` | [PaymentsResource.java](/Users/yangyuhan/behavior-analyze/pay-publicapi/src/main/java/uk/gov/pay/api/resources/PaymentsResource.java:96), `CreatePaymentService`, `LedgerService`, `ConnectorService` |
| MOTO API authorisation | `/v1/auth` | [AuthorisationResource.java](/Users/yangyuhan/behavior-analyze/pay-publicapi/src/main/java/uk/gov/pay/api/resources/AuthorisationResource.java:40), `AuthorisationService` |
| Payment refund create/list/get | `/v1/payments/{paymentId}/refunds`, `/{refundId}` | [PaymentRefundsResource.java](/Users/yangyuhan/behavior-analyze/pay-publicapi/src/main/java/uk/gov/pay/api/resources/PaymentRefundsResource.java:68), `CreateRefundService` |
| Refund search | `/v1/refunds` | [SearchRefundsResource.java](/Users/yangyuhan/behavior-analyze/pay-publicapi/src/main/java/uk/gov/pay/api/resources/SearchRefundsResource.java:47) |
| Dispute search | `/v1/disputes` | [SearchDisputesResource.java](/Users/yangyuhan/behavior-analyze/pay-publicapi/src/main/java/uk/gov/pay/api/resources/SearchDisputesResource.java:47) |

## Coverage Matrix

| ID | Business Behavior | Happy Path | Failure Cases | Optional Verification | Status | Confidence | Main Gap |
|---|---|---|---|---|---|---|---|
| B1 | Search recurring agreements | Not Covered | `0/2` | N/A | Not Covered | High | No `/v1/agreements` tests |
| B2 | Create a recurring agreement | Not Covered | `0/3` | Not executed | Not Covered | High | No agreement create/read workflow |
| B3 | Retrieve one recurring agreement | Not Covered | `0/2` | N/A | Not Covered | High | No created or seeded agreement ID |
| B4 | Initiate agreement setup payment | Not Covered | `0/3` | Not executed | Not Covered | High | No `set_up_agreement` binding |
| B5 | Cancel active recurring agreement | Not Covered | `0/2` | Not executed | Not Covered | High | No active agreement state |
| B6 | Search payments | Not Covered | `0/2` | N/A | Not Covered | High | Calls fail at auth, not search validation/Ledger |
| B7 | Create web card payment | Not Covered | `0/3` | Not executed | Not Covered | High | No authenticated `201` create |
| B8 | Replay idempotent payment creation | Not Covered | `0/2` | Not executed | Not Covered | High | No `Idempotency-Key` replay pair |
| B9 | Authorise MOTO API payment | Not Covered | `0/3` | Not executed | Not Covered | High | No MOTO create/token/auth flow |
| B10 | Take recurring payment | Not Covered | `0/2` | Not executed | Not Covered | High | No active agreement fixture or agreement mode |
| B11 | Retrieve one payment | Not Covered | `0/2` | Not executed | Not Covered | High | No valid payment ID from create/fixture |
| B12 | Cancel unfinished payment | Not Covered | `0/3` | Not executed | Not Covered | High | Cancel calls are unauthenticated/dummy-auth |
| B13 | Capture delayed MOTO payment | Not Covered | `0/3` | Not executed | Not Covered | High | No delayed authorised payment state |
| B14 | Read payment events | Not Covered | `0/2` | Not executed | Not Covered | High | No created payment or event history |
| B15 | Create refund | Not Covered | `0/4` | Not executed | Not Covered | High | No successful refundable payment |
| B16 | List payment refunds | Not Covered | `0/4` | Not executed | Not Covered | High | No parent payment/refund state |
| B17 | Retrieve one refund | Not Covered | `0/4` | Not executed | Not Covered | High | No parent/child ID binding |
| B18 | Search refunds | Not Covered | `0/2` | N/A | Not Covered | High | Calls fail at auth, not refund validator/Ledger |
| B19 | Search disputes | Not Covered | `0/2` | N/A | Not Covered | High | No `/v1/disputes` tests |

## Cross-Behavior Gaps

- No generated test supplies a valid authenticated service account, so business resources are not exercised past auth.
- No test binds generated `agreement_id`, `payment_id`, `refund_id`, `one_time_token`, or `Idempotency-Key` across workflow steps.
- Direct endpoint execution is not enough: the suite never asserts payment/agreement/refund/dispute business state.
- Documented validators are effectively unproven by the generated corpus: `PaymentSearchValidator`, `AgreementSearchValidator`, `RefundSearchValidator`, `DisputeSearchValidator`, and `RequestJsonParser` all show `0` covered lines in XML.
- JaCoCo coverage is concentrated in bootstrap/auth/filter/error plumbing, not completed domain workflows.

## Suggested Additional Tests

| Priority | Behavior ID | Test Intent | Minimal Setup | Calls / Operations | Required Assertions | Type |
|---:|---|---|---|---|---|---|
| 1 | B7, B11, B12, B14 | Web payment lifecycle | Valid account + connector stubs | `POST /v1/payments`, `GET`, `GET events`, `POST cancel` | `201`, same `payment_id`, links/state, `204` cancel, event includes cancellation | Success |
| 2 | B6, B18, B19, B1 | Search success + invalid filters | Valid auth; Ledger empty/non-empty stubs | `GET /v1/payments`, `/refunds`, `/disputes`, `/agreements` | `200` scoped results; invalid filters return documented `422` | Success/Failure |
| 3 | B2, B3 | Agreement create/read/search | Recurring-enabled account | `POST /v1/agreements`, `GET /{id}`, search by reference | `201`, created fields, same `agreement_id`, appears in search | Success |
| 4 | B4, B10, B5 | Recurring setup/charge/cancel | Active agreement fixture for B10/B5 | Setup payment, agreement-mode payment, cancel agreement | correct ID binding, mode constraints, `204` cancel | Success |
| 5 | B8 | Idempotency replay | Valid account + connector idempotency support | Two same `POST /v1/payments` with same key; third changed body | `201`, then `200` same `payment_id`; changed body `409` | Regression |
| 6 | B9, B13 | MOTO auth and delayed capture | MOTO API-enabled account | MOTO create, `/v1/auth`, delayed create/auth/capture | token consumed, `204` auth/capture, invalid token/card failures | Success/Failure |
| 7 | B15-B17 | Refund lifecycle | Successful refundable payment | `POST refunds`, list, get refund | `202`, same `refund_id`, list contains child, invalid amount/not-found failures | Success/Failure |

## Appendix: Coverage Artifacts Used

XML: [reports/report.xml](/Users/yangyuhan/behavior-analyze/pay-publicapi/reports/report.xml) with full JaCoCo counters. Exact combined coverage was computable because only one XML report exists.

CSV: [reports/report.csv](/Users/yangyuhan/behavior-analyze/pay-publicapi/reports/report.csv), used as aggregate cross-check only.

I did not include `src/test` because the prompt explicitly scoped generated-test analysis to `/Users/yangyuhan/behavior-analyze/pay-publicapi/tests`.