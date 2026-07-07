# Business Behavior Coverage Report

## Executive Summary

- Project under analysis: `/Users/yangyuhan/behavior-analyze/pay-publicapi`
- Business specification: [business-behavior.md](/Users/yangyuhan/behavior-analyze/pay-publicapi/business-behavior.md)
- Test suites analyzed: [EM_pay_publicapi_True_25_false_false_SPECIFIED_false_0_Test.java](/Users/yangyuhan/behavior-analyze/pay-publicapi/tests/EM_pay_publicapi_True_25_false_false_SPECIFIED_false_0_Test.java:36), `36` tests
- Application calls analyzed: `39` calls and `11` distinct normalized routes, including one non-business `/assets/swagger.json` call
- Coverage reports analyzed: `reports/report.xml` and `reports/report.csv`; `coverage/evomaster_10310_pay-publicapi__10311/report.xml` and `coverage/evomaster_10320_pay-publicapi__10321/report.xml` were inspected for endpoint-method corroboration but not arithmetically added because they overlap the same generated execution and include a broader runtime/dependency universe
- Source roots analyzed: `src/main/java`, `src/main/resources`
- Total documented behaviors: `19`
- Total documented failure entries: `95`
- Covered / Partially Covered / Not Covered / Unclear: `0 / 0 / 19 / 0`
- Business behavior coverage: `0/19 (0.0%)`
- Function/API invocation coverage: `10/20 (50.0%)`, plus `0` ambiguous shared-route matches
- Required-step attempt coverage: `16/36 (44.4%)`
- Required-step application-reach coverage: `0/36 (0.0%)`
- Required-step context-valid success coverage: `0/36 (0.0%)`
- Happy-path behavior coverage: `0/19 (0.0%)`
- Documented business-failure coverage: `0/95 (0.0%)`
- Unique source business-branch coverage: `0/94 (0.0%)`
- Behavior outcome checklist coverage: `0/114 (0.0%)`
- Optional verification execution coverage: `0/22 (0.0%)`
- Combined JaCoCo signal: lines `458/4198 (10.9%)`, branches `13/641 (2.0%)`, methods `171/1507 (11.3%)`, classes `86/243 (35.4%)` from `reports/report.xml`

The execution funnel is internally consistent: context-valid success `0` <= application reached `0` <= attempted required steps `16`. The generated suite probes several payment/refund routes, but the calls either omit authentication and receive `401` from [AuthorizationValidationFilter](/Users/yangyuhan/behavior-analyze/pay-publicapi/src/main/java/uk/gov/pay/api/filter/AuthorizationValidationFilter.java:52) or use dummy Bearer credentials that fail in [AccountAuthenticator.authenticate](/Users/yangyuhan/behavior-analyze/pay-publicapi/src/main/java/uk/gov/pay/api/auth/AccountAuthenticator.java:37) and return `500`. JaCoCo corroborates this: behavior resource/service methods are not covered beyond constructors and class initializers. Covered code therefore proves route/admission activity, not business behavior.

## Inventory Validation

| Item | Result |
|---|---:|
| Parsed behavior count | `19` |
| Parsed failure-entry count | `95` |
| Prompt expected count | `81` behaviors / `461` failures |
| Count discrepancy | Actual specification is `62` fewer behaviors and `366` fewer failure entries than the prompt expectation |
| Behaviors with `Failure and exceptional cases: None.` | `0` |
| Malformed or unparsed behavior/failure entries | `0` |
| Exact-function-name mapping failures through `full-behavior.md` | `0` |
| Required workflow steps | `36` |
| Optional verification steps | `22` |
| Documented functions owned by behaviors | `20` |
| Unique source business branches | `94` |
| Behavior outcome denominator | `19 + 95 = 114` |

The duplicate unique-source branch is the post-create Ledger-read failure for `create agreement`, documented under both B2 and B4. It remains two documented failure occurrences, but one unique source branch.

## Test Evidence Summary

- Generated suite declares `36` tests and resets state before each method with `controller.resetStateOfSUT()` in [the generated suite](/Users/yangyuhan/behavior-analyze/pay-publicapi/tests/EM_pay_publicapi_True_25_false_false_SPECIFIED_false_0_Test.java:74).
- No generated test captures a response value such as `payment_id`, `agreement_id`, `refund_id`, `one_time_token`, or an idempotency response and reuses it in a later request.
- No generated test configures a valid business account, Connector/Ledger success fixture, persisted state assertion, event assertion, or side-effect assertion.
- The generated comments preceding tests are not used as evidence because they do not always match the actual REST-assured call in the method body; this report uses the actual calls and `statusCode` assertions.

| Normalized Route | Calls | Status Classes | Representative Tests |
|---|---:|---|---|
| `GET /assets/swagger.json` | `1` | 1x `200` | `test_16_getOnSwagger_jsonReturnsObject` (200) |
| `GET /v1/payments` | `10` | 2x `401`, 8x `500` | `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500), `test_4_getOnPaymentsShowsFaults_100_101` (500) |
| `GET /v1/payments/{paymentId}` | `2` | 1x `401`, 1x `500` | `test_22_getOnPaymentShowsFaults_100_101` (500), `test_23_getOnPaymentReturnsMismatchResponseWithSchema` (401) |
| `GET /v1/payments/{paymentId}/events` | `2` | 1x `401`, 1x `500` | `test_24_getOnEventsShowsFaults_100_101` (500), `test_29_getOnEventsReturnsMismatchResponseWithSchema` (401) |
| `GET /v1/payments/{paymentId}/refunds` | `2` | 1x `401`, 1x `500` | `test_25_getOnPaymentRefundsShowsFaults_100_101` (500), `test_30_getOnPaymentRefundsReturnsMismatchResponseWithSchema` (401) |
| `GET /v1/payments/{paymentId}/refunds/{refundId}` | `2` | 1x `401`, 1x `500` | `test_34_getOnRefundShowsFaults_100_101` (500), `test_35_getOnRefundReturnsMismatchResponseWithSchema` (401) |
| `GET /v1/refunds` | `4` | 1x `401`, 3x `500` | `test_5_getOnRefundsShowsFaults_100_101` (500), `test_8_getOnRefundsShowsFaults_100_101` (500), `test_9_getOnRefundsShowsFaults_100_101` (500), `test_18_getOnV1RefundsWithQueryParamsReturnsMismatchResponseWithSchema` (401) |
| `POST /v1/payments` | `10` | 3x `401`, 7x `500` | `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500), `test_14_postOnPaymentsShowsFaults_100_101` (500) |
| `POST /v1/payments/{paymentId}/cancel` | `2` | 1x `401`, 1x `500` | `test_26_postOnCancelShowsFaults_100_101` (500), `test_31_postOnCancelReturnsMismatchResponseWithSchema` (401) |
| `POST /v1/payments/{paymentId}/capture` | `2` | 1x `401`, 1x `500` | `test_27_postOnCaptureShowsFaults_100_101` (500), `test_32_postOnCaptureReturnsMismatchResponseWithSchema` (401) |
| `POST /v1/payments/{paymentId}/refunds` | `2` | 1x `401`, 1x `500` | `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401) |

## Coverage Matrix

| ID | Business Behavior | Required Steps Attempted | Application Reached | Context-Valid Steps | Happy Path | Failure Coverage | Optional Verification | Status | Confidence |
|---|---|---:|---:|---:|---|---:|---:|---|---|
| B1 | Search recurring agreements | `0/1` | `0/1` | `0/1` | Not Covered | `0/5` | `0/0` | Not Covered | High |
| B2 | Create a recurring agreement | `0/1` | `0/1` | `0/1` | Not Covered | `0/2` | `0/2` | Not Covered | High |
| B3 | Retrieve one recurring agreement | `0/2` | `0/2` | `0/2` | Not Covered | `0/1` | `0/0` | Not Covered | High |
| B4 | Initiate an agreement setup payment | `0/2` | `0/2` | `0/2` | Not Covered | `0/9` | `0/2` | Not Covered | High |
| B5 | Cancel an active recurring agreement | `0/1` | `0/1` | `0/1` | Not Covered | `0/2` | `0/1` | Not Covered | High |
| B6 | Search payments | `1/1` | `0/1` | `0/1` | Not Covered | `0/13` | `0/0` | Not Covered | High |
| B7 | Create a web card payment | `1/1` | `0/1` | `0/1` | Not Covered | `0/7` | `0/2` | Not Covered | High |
| B8 | Replay idempotent payment creation | `1/2` | `0/2` | `0/2` | Not Covered | `0/1` | `0/1` | Not Covered | High |
| B9 | Authorise a MOTO API payment | `0/2` | `0/2` | `0/2` | Not Covered | `0/12` | `0/2` | Not Covered | High |
| B10 | Take a recurring payment from an active agreement | `0/1` | `0/1` | `0/1` | Not Covered | `0/9` | `0/2` | Not Covered | High |
| B11 | Retrieve one payment | `2/2` | `0/2` | `0/2` | Not Covered | `0/1` | `0/1` | Not Covered | High |
| B12 | Cancel an unfinished payment | `2/2` | `0/2` | `0/2` | Not Covered | `0/3` | `0/2` | Not Covered | High |
| B13 | Capture a delayed MOTO API payment | `1/3` | `0/3` | `0/3` | Not Covered | `0/3` | `0/2` | Not Covered | High |
| B14 | Read payment event history | `2/2` | `0/2` | `0/2` | Not Covered | `0/1` | `0/1` | Not Covered | High |
| B15 | Create a refund for a successful payment | `1/3` | `0/3` | `0/3` | Not Covered | `0/8` | `0/2` | Not Covered | High |
| B16 | List refunds for a payment | `2/4` | `0/4` | `0/4` | Not Covered | `0/1` | `0/1` | Not Covered | High |
| B17 | Retrieve one payment refund | `2/4` | `0/4` | `0/4` | Not Covered | `0/2` | `0/1` | Not Covered | High |
| B18 | Search refunds | `1/1` | `0/1` | `0/1` | Not Covered | `0/7` | `0/0` | Not Covered | High |
| B19 | Search disputes | `0/1` | `0/1` | `0/1` | Not Covered | `0/8` | `0/0` | Not Covered | High |

## Function/API Invocation Checklist

| Exact Function Name | Method/Route | Attempted? | Distinguishable? | Representative Tests | Result Classes |
|---|---|---|---|---|---|
| `search agreements` | `GET /v1/agreements` | No | N/A | None | No matching generated route call |
| `create agreement` | `POST /v1/agreements` | No | N/A | None | No matching generated route call |
| `get agreement` | `GET /v1/agreements/{agreementId}` | No | N/A | None | No matching generated route call |
| `create setup-agreement payment` | `POST /v1/payments` | No | No exact shared-route discriminator | None | No exact attempt: no `set_up_agreement` body value appears in generated tests. |
| `cancel agreement` | `POST /v1/agreements/{agreementId}/cancel` | No | N/A | None | No matching generated route call |
| `search payments` | `GET /v1/payments` | Yes | Yes | `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500) | 2x `401`, 8x `500` |
| `create web card payment` | `POST /v1/payments` | Yes | Yes - shared route body lacks `set_up_agreement`, `agreement_id`, `authorisation_mode`, and `Idempotency-Key`, so these probes match the web-card variant only | `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500) | 3x `401`, 7x `500` |
| `replay idempotent payment creation` | `POST /v1/payments` | No | No exact shared-route discriminator | None | No exact attempt: no `Idempotency-Key` header appears in generated tests. |
| `create MOTO API payment` | `POST /v1/payments` | No | No exact shared-route discriminator | None | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. |
| `authorise MOTO API payment` | `POST /v1/auth` | No | N/A | None | No matching generated route call |
| `take recurring payment` | `POST /v1/payments` | No | No exact shared-route discriminator | None | No exact attempt: no `authorisation_mode=agreement` and `agreement_id` body appears in generated tests. |
| `get payment` | `GET /v1/payments/{paymentId}` | Yes | Yes | `test_22_getOnPaymentShowsFaults_100_101` (500), `test_23_getOnPaymentReturnsMismatchResponseWithSchema` (401) | 1x `401`, 1x `500` |
| `cancel payment` | `POST /v1/payments/{paymentId}/cancel` | Yes | Yes | `test_26_postOnCancelShowsFaults_100_101` (500), `test_31_postOnCancelReturnsMismatchResponseWithSchema` (401) | 1x `401`, 1x `500` |
| `capture delayed payment` | `POST /v1/payments/{paymentId}/capture` | Yes | Yes | `test_27_postOnCaptureShowsFaults_100_101` (500), `test_32_postOnCaptureReturnsMismatchResponseWithSchema` (401) | 1x `401`, 1x `500` |
| `get payment events` | `GET /v1/payments/{paymentId}/events` | Yes | Yes | `test_24_getOnEventsShowsFaults_100_101` (500), `test_29_getOnEventsReturnsMismatchResponseWithSchema` (401) | 1x `401`, 1x `500` |
| `list payment refunds` | `GET /v1/payments/{paymentId}/refunds` | Yes | Yes | `test_25_getOnPaymentRefundsShowsFaults_100_101` (500), `test_30_getOnPaymentRefundsReturnsMismatchResponseWithSchema` (401) | 1x `401`, 1x `500` |
| `refund payment` | `POST /v1/payments/{paymentId}/refunds` | Yes | Yes | `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401) | 1x `401`, 1x `500` |
| `get refund` | `GET /v1/payments/{paymentId}/refunds/{refundId}` | Yes | Yes | `test_34_getOnRefundShowsFaults_100_101` (500), `test_35_getOnRefundReturnsMismatchResponseWithSchema` (401) | 1x `401`, 1x `500` |
| `search refunds` | `GET /v1/refunds` | Yes | Yes | `test_5_getOnRefundsShowsFaults_100_101` (500), `test_8_getOnRefundsShowsFaults_100_101` (500), `test_9_getOnRefundsShowsFaults_100_101` (500), `test_18_getOnV1RefundsWithQueryParamsReturnsMismatchResponseWithSchema` (401) | 1x `401`, 3x `500` |
| `search disputes` | `GET /v1/disputes` | No | N/A | None | No matching generated route call |

## Behavior Details

### `B1`: `Search recurring agreements`

- Business goal: Find recurring-payment agreements belonging to the authenticated gateway account.
- Starting point: `No prior service state`
- Expected business result: No state changes. The caller receives a paginated account-scoped list of agreements, possibly empty.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `search agreements` | `GET /v1/agreements` with authenticated Bearer caller context for `accountId={gateway account id}`, optional query `reference={exact reference}`, `status={created\\|active\\|cancelled\\|inactive}`, `page={integer >= 1}`, and `display_size={1..500}` to return matching agreements. | No | No | No | No generated call to `GET /v1/agreements`. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

None documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `search agreements` | `AgreementSearchValidator.validateStatus -> RequestError.Code.SEARCH_AGREEMENTS_VALIDATION_ERROR` | `status` is nonblank and is not one of `created`, `active`, `cancelled`, or `inactive`. | No | High | No generated call to `GET /v1/agreements`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search agreements` | `AgreementSearchValidator.validateReference -> RequestError.Code.SEARCH_AGREEMENTS_VALIDATION_ERROR` | `reference` is longer than 255 characters. | No | High | No generated call to `GET /v1/agreements`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search agreements` | `SearchValidator.validatePageIfNotNull -> RequestError.Code.SEARCH_AGREEMENTS_VALIDATION_ERROR` | `page` is nonblank and is not numeric or is less than 1. | No | High | No generated call to `GET /v1/agreements`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search agreements` | `SearchValidator.validateDisplaySizeIfNotNull -> RequestError.Code.SEARCH_AGREEMENTS_VALIDATION_ERROR` | `display_size` is nonblank and is not numeric, is less than 1, or is greater than 500. | No | High | No generated call to `GET /v1/agreements`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search agreements` | `SearchAgreementsExceptionMapper -> RequestError.Code.SEARCH_AGREEMENTS_NOT_FOUND` | Ledger returns 404 for the requested agreement search page. | No | High | No generated call to `GET /v1/agreements`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `0/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/5`
- Behavior outcome checklist summary: `0/6`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T2, T7`

### `B2`: `Create a recurring agreement`

- Business goal: Create a recurring-payment agreement that can later be activated and charged.
- Starting point: `No prior service state`
- Expected business result: A new recurring agreement exists for the authenticated account. The response is `201` only after the Connector create and follow-up Ledger read both succeed.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create agreement` | `POST /v1/agreements` with authenticated Bearer caller context for `accountId={gateway account id}` and JSON body `reference={nonblank string}`, `description={nonblank string}`, and optional `user_identifier={string}` to create the agreement and capture `agreement_id={generated agreement id}` from the response. | No | No | No | No generated call to `POST /v1/agreements`. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get agreement` | `GET /v1/agreements/{agreementId}` with `agreementId={agreement_id from create agreement}` and the same authenticated account to inspect the created agreement. | No | No generated verification call with required binding for `GET /v1/agreements/{agreementId}`. |
| 2 | `search agreements` | `GET /v1/agreements` with `reference={reference from create agreement}` and the same authenticated account to confirm it appears in search. | No | No generated verification call with required binding for `GET /v1/agreements`. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `create agreement` | `ErrorIdentifier.RECURRING_CARD_PAYMENTS_NOT_ALLOWED -> RequestError.Code.RECURRING_CARD_PAYMENTS_NOT_ALLOWED_ERROR` | Connector reports that recurring card payments are not enabled for the gateway account. | No | High | No generated call to `POST /v1/agreements`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create agreement` | `AgreementsApiResource.createAgreement post-create Ledger read -> RequestError.Code.GET_AGREEMENT_NOT_FOUND_ERROR` | Connector creates the agreement, but the immediate Ledger read for the generated `agreement_id` returns 404. | No | High | No generated call to `POST /v1/agreements`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `0/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T2`

### `B3`: `Retrieve one recurring agreement`

- Business goal: Inspect the current details and status of a known agreement.
- Starting point: `No prior service state`
- Expected business result: The caller receives one account-scoped agreement resource. No state is changed after setup creation.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create agreement` | `POST /v1/agreements` with authenticated Bearer caller context for `accountId={gateway account id}` and body `reference={nonblank string}` and `description={nonblank string}` to create an agreement and capture `agreement_id={generated agreement id}`. | No | No | No | No generated call to `POST /v1/agreements`. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 2 | `get agreement` | `GET /v1/agreements/{agreementId}` with the same authenticated account and path `agreementId={agreement_id from create agreement}` to retrieve the agreement. | No | No | No | No generated call to `GET /v1/agreements/{agreementId}`. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

None documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `get agreement` | `GetAgreementExceptionMapper -> RequestError.Code.GET_AGREEMENT_NOT_FOUND_ERROR` | `agreementId` does not identify a Ledger agreement visible to the authenticated account. | No | High | No generated call to `GET /v1/agreements/{agreementId}`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `0/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T2`

### `B4`: `Initiate an agreement setup payment`

- Business goal: Create a hosted payment that can save a payer’s payment instrument to an agreement after successful completion.
- Starting point: `No prior service state`
- Expected business result: A web-journey setup payment exists and is linked to the agreement. The agreement is prepared for activation after the external hosted payment completes successfully.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create agreement` | `POST /v1/agreements` with authenticated Bearer caller context for `accountId={gateway account id}` and body `reference={agreement reference}` and `description={agreement description}` to create an agreement and capture `agreement_id={generated agreement id}`. | No | No | No | No generated call to `POST /v1/agreements`. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 2 | `create setup-agreement payment` | `POST /v1/payments` with the same authenticated account, body `amount={integer amount}`, `reference={payment reference}`, `description={payment description}`, `return_url={valid web return URL}`, and `set_up_agreement={agreement_id from create agreement}` to create the setup payment and capture `payment_id={generated payment id}`. | No | No | No | No exact attempt: no `set_up_agreement` body value appears in generated tests. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get payment` | `GET /v1/payments/{paymentId}` with `paymentId={payment_id from create setup-agreement payment}` to inspect the setup payment. | No | Attempted by 2 call(s): `test_22_getOnPaymentShowsFaults_100_101` (500), `test_23_getOnPaymentReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |
| 2 | `get agreement` | `GET /v1/agreements/{agreementId}` with `agreementId={agreement_id from create agreement}` to inspect agreement state. | No | No generated verification call with required binding for `GET /v1/agreements/{agreementId}`. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `create agreement` | `ErrorIdentifier.RECURRING_CARD_PAYMENTS_NOT_ALLOWED -> RequestError.Code.RECURRING_CARD_PAYMENTS_NOT_ALLOWED_ERROR` | Connector reports that recurring card payments are not enabled for the gateway account while creating the agreement that will be set up. | No | High | No generated call to `POST /v1/agreements`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create agreement` | `AgreementsApiResource.createAgreement post-create Ledger read -> RequestError.Code.GET_AGREEMENT_NOT_FOUND_ERROR` | Connector creates the agreement, but the immediate Ledger read for the generated `agreement_id` returns 404. | No | High | No generated call to `POST /v1/agreements`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create setup-agreement payment` | `ErrorIdentifier.AGREEMENT_NOT_FOUND with 404 -> RequestError.Code.CREATE_PAYMENT_AGREEMENT_ID_ERROR` | `set_up_agreement` is an agreement id that Connector cannot find for the account when saving a payment instrument to the agreement. | No | High | No exact attempt: no `set_up_agreement` body value appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create setup-agreement payment` | `ErrorIdentifier.INCORRECT_AUTHORISATION_MODE_FOR_SAVE_PAYMENT_INSTRUMENT_TO_AGREEMENT -> RequestError.Code.CREATE_PAYMENT_UNEXPECTED_FIELD_ERROR` | The request asks Connector to save a payment instrument to an agreement using an incompatible authorisation mode. | No | High | No exact attempt: no `set_up_agreement` body value appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create setup-agreement payment` | `ErrorIdentifier.RECURRING_CARD_PAYMENTS_NOT_ALLOWED -> RequestError.Code.RECURRING_CARD_PAYMENTS_NOT_ALLOWED_ERROR` | The account is not allowed to create payments that set up recurring-card agreements. | No | High | No exact attempt: no `set_up_agreement` body value appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create setup-agreement payment` | `ErrorIdentifier.MISSING_MANDATORY_ATTRIBUTE -> RequestError.Code.GENERIC_MISSING_FIELD_ERROR_MESSAGE_FROM_CONNECTOR` | The hosted setup payment request omits a Connector-required payment attribute such as the web journey `return_url`. | No | High | No exact attempt: no `set_up_agreement` body value appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create setup-agreement payment` | `ErrorIdentifier.ZERO_AMOUNT_NOT_ALLOWED -> RequestError.Code.CREATE_PAYMENT_VALIDATION_ERROR` | The setup payment amount is zero and the account is not permitted to create zero-amount payments. | No | High | No exact attempt: no `set_up_agreement` body value appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create setup-agreement payment` | `ErrorIdentifier.ACCOUNT_DISABLED -> RequestError.Code.ACCOUNT_DISABLED` | GOV.UK Pay has disabled payment creation on the account. | No | High | No exact attempt: no `set_up_agreement` body value appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create setup-agreement payment` | `ErrorIdentifier.ACCOUNT_NOT_LINKED_WITH_PSP -> RequestError.Code.ACCOUNT_NOT_LINKED_WITH_PSP` | The account is not fully linked to a payment service provider for creating the setup payment. | No | High | No exact attempt: no `set_up_agreement` body value appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `0/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/9`
- Behavior outcome checklist summary: `0/10`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T2`

### `B5`: `Cancel an active recurring agreement`

- Business goal: Stop future recurring charges by cancelling an active agreement.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: The active agreement becomes cancelled. Future agreement-mode payment creation for that agreement should be blocked by Connector.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `cancel agreement` | `POST /v1/agreements/{agreementId}/cancel` with authenticated Bearer caller context for `accountId={gateway account id}`, path `agreementId={pre-existing active agreement id}`, and upstream state `agreement status=active with saved payment instrument` to cancel the agreement. | No | No | No | No generated call to `POST /v1/agreements/{agreementId}/cancel`. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get agreement` | `GET /v1/agreements/{agreementId}` with `agreementId={agreement id used for cancellation}` to inspect the resulting status. | No | No generated verification call with required binding for `GET /v1/agreements/{agreementId}`. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `cancel agreement` | `CancelAgreementExceptionMapper -> RequestError.Code.CANCEL_AGREEMENT_NOT_FOUND_ERROR` | `agreementId` is unknown to Connector for the authenticated account. | No | High | No generated call to `POST /v1/agreements/{agreementId}/cancel`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `cancel agreement` | `CancelAgreementExceptionMapper -> RequestError.Code.CANCEL_AGREEMENT_CONNECTOR_BAD_REQUEST_ERROR` | Connector rejects cancellation because the agreement is not in an active cancellable state. | No | High | No generated call to `POST /v1/agreements/{agreementId}/cancel`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `0/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T3`

### `B6`: `Search payments`

- Business goal: Find payments in the authenticated account by lifecycle state, business reference, card attributes, creation dates, settlement dates, or agreement id.
- Starting point: `No prior service state`
- Expected business result: No state changes. The caller receives a paginated payment result set with payment links.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `search payments` | `GET /v1/payments` with authenticated Bearer caller context for `accountId={gateway account id}` and query values `reference={exact reference}`, `email={email fragment}`, `state={created\\|started\\|submitted\\|success\\|failed\\|cancelled\\|error}`, `card_brand={card brand}`, `from_date={UTC ISO date-time}`, `to_date={UTC ISO date-time}`, `page={integer >= 1}`, `display_size={1..500}`, `cardholder_name={name}`, `first_digits_card_number={6 digits}`, `last_digits_card_number={4 digits}`, `from_settled_date={ISO date}`, `to_settled_date={ISO date}`, and `agreement_id={agreement id}` to retrieve matching payments. | Yes | No | No | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

None documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `search payments` | `PaymentSearchValidator.validateState -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR` | `state` is nonblank and is not one of `created`, `started`, `submitted`, `success`, `failed`, `cancelled`, or `error`. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search payments` | `PaymentSearchValidator.validateReference -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR` | `reference` is longer than 255 characters. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search payments` | `PaymentSearchValidator.validateEmail -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR` | `email` is longer than 254 characters. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search payments` | `PaymentSearchValidator.validateCardBrand -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR` | `card_brand` is longer than 20 characters. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search payments` | `SearchValidator.validateFromDate -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR` | `from_date` is not a valid UTC ISO-8601 date-time accepted by `DateValidator`. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search payments` | `SearchValidator.validateToDate -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR` | `to_date` is not a valid UTC ISO-8601 date-time accepted by `DateValidator`. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search payments` | `SearchValidator.validatePageIfNotNull -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR` | `page` is nonblank and is not numeric or is less than 1. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search payments` | `SearchValidator.validateDisplaySizeIfNotNull -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR` | `display_size` is nonblank and is not numeric, is less than 1, or is greater than 500. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search payments` | `PaymentSearchValidator.validateFirstDigitsCardNumber -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR` | `first_digits_card_number` is nonblank and is not exactly 6 numeric digits. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search payments` | `PaymentSearchValidator.validateLastDigitsCardNumber -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR` | `last_digits_card_number` is nonblank and is not exactly 4 numeric digits. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search payments` | `SearchValidator.validateFromSettledDate -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR` | `from_settled_date` is nonblank and is not a valid ISO-8601 date-only value. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search payments` | `SearchValidator.validateToSettledDate -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR` | `to_settled_date` is nonblank and is not a valid ISO-8601 date-only value. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search payments` | `SearchChargesExceptionMapper -> RequestError.Code.SEARCH_PAYMENTS_NOT_FOUND` | Ledger returns 404 for the requested payment search page. | No | High | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/13`
- Behavior outcome checklist summary: `0/14`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T1, T8`

### `B7`: `Create a web card payment`

- Business goal: Start a standard hosted card payment journey and receive payment links for the payer flow.
- Starting point: `No prior service state`
- Expected business result: A new card payment exists and is returned with payment links. The response is `201` for a brand-new Connector payment.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create web card payment` | `POST /v1/payments` with authenticated Bearer caller context for `accountId={gateway account id}`, no reused `Idempotency-Key`, and body `amount={integer 0..10000000 subject to account policy}`, `reference={nonblank string}`, `description={nonblank string}`, `return_url={valid URL}`, optional `email={string}`, `language={en\\|cy}`, `metadata={valid metadata object}`, `prefilled_cardholder_details={valid object}`, `moto={boolean}`, and `delayed_capture={boolean}` to create the payment and capture `payment_id={generated payment id}`. | Yes | No | No | Attempted by 10 call(s): `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 3, 500x 7; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get payment` | `GET /v1/payments/{paymentId}` with `paymentId={payment_id from create web card payment}` to inspect the payment. | No | Attempted by 2 call(s): `test_22_getOnPaymentShowsFaults_100_101` (500), `test_23_getOnPaymentReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |
| 2 | `search payments` | `GET /v1/payments` with `reference={reference from create web card payment}` to find the payment in Ledger after it is indexed. | No | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `create web card payment` | `CreateChargeExceptionMapper 404 without ErrorIdentifier.AGREEMENT_NOT_FOUND -> RequestError.Code.CREATE_PAYMENT_ACCOUNT_ERROR` | Connector cannot find the gateway account for charge creation. | No | High | Attempted by 10 call(s): `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 3, 500x 7; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create web card payment` | `ErrorIdentifier.MISSING_MANDATORY_ATTRIBUTE -> RequestError.Code.GENERIC_MISSING_FIELD_ERROR_MESSAGE_FROM_CONNECTOR` | Connector requires a create-payment attribute that Public API forwards as absent, such as the hosted-payment `return_url` for web mode. | No | High | Attempted by 10 call(s): `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 3, 500x 7; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create web card payment` | `ErrorIdentifier.ZERO_AMOUNT_NOT_ALLOWED -> RequestError.Code.CREATE_PAYMENT_VALIDATION_ERROR` | The payment amount is zero and the account is not allowed to create zero-amount payments. | No | High | Attempted by 10 call(s): `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 3, 500x 7; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create web card payment` | `ErrorIdentifier.MOTO_NOT_ALLOWED -> RequestError.Code.CREATE_PAYMENT_MOTO_NOT_ENABLED` | The payment request asks for MOTO handling on an account that is not enabled for MOTO payments. | No | High | Attempted by 10 call(s): `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 3, 500x 7; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create web card payment` | `ErrorIdentifier.ACCOUNT_DISABLED -> RequestError.Code.ACCOUNT_DISABLED` | GOV.UK Pay has disabled payment creation on the account. | No | High | Attempted by 10 call(s): `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 3, 500x 7; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create web card payment` | `ErrorIdentifier.ACCOUNT_NOT_LINKED_WITH_PSP -> RequestError.Code.ACCOUNT_NOT_LINKED_WITH_PSP` | The account is not fully linked to a payment service provider. | No | High | Attempted by 10 call(s): `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 3, 500x 7; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create web card payment` | `ErrorIdentifier.CARD_NUMBER_IN_PAYMENT_LINK_REFERENCE_REJECTED -> RequestError.Code.CREATE_PAYMENT_CARD_NUMBER_IN_PAYMENT_LINK_REFERENCE_ERROR` | A payment-link sourced request uses a `reference` that Connector identifies as containing card-number content. | No | High | Attempted by 10 call(s): `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 3, 500x 7; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/7`
- Behavior outcome checklist summary: `0/8`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T1`

### `B8`: `Replay idempotent payment creation`

- Business goal: Avoid duplicate payment creation when a caller retries the same create-payment request.
- Starting point: `No prior service state`
- Expected business result: No second payment is created. The replay returns the existing payment, normally with `200`, while the first successful creation returns `201`.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create web card payment` | `POST /v1/payments` with authenticated Bearer caller context for `accountId={gateway account id}`, header `Idempotency-Key={1..255 chars using alphanumerics and hyphens}`, and body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, and `return_url={valid URL}` to create the original payment and capture `payment_id={generated payment id}`. | Yes | No | No | Attempted by 10 call(s): `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 3, 500x 7; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 2 | `replay idempotent payment creation` | `POST /v1/payments` with the same authenticated account, the same header `Idempotency-Key={same key from step 1}`, and the same body `amount={same amount}`, `reference={same reference}`, `description={same description}`, and `return_url={same URL}` to return the existing payment. | No | No | No | No exact attempt: no `Idempotency-Key` header appears in generated tests. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get payment` | `GET /v1/payments/{paymentId}` with `paymentId={payment_id from step 1}` to inspect the single payment returned by both requests. | No | Attempted by 2 call(s): `test_22_getOnPaymentShowsFaults_100_101` (500), `test_23_getOnPaymentReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `replay idempotent payment creation` | `ErrorIdentifier.IDEMPOTENCY_KEY_USED -> RequestError.Code.CREATE_PAYMENT_IDEMPOTENCY_KEY_ALREADY_USED` | A previous create-payment request used the same `Idempotency-Key`, but the replay request is not equivalent to the original payment creation request. | No | High | No exact attempt: no `Idempotency-Key` header appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `1/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T6`

### `B9`: `Authorise a MOTO API payment`

- Business goal: Create a MOTO API payment and complete card authorisation through the public authorisation endpoint.
- Starting point: `No prior service state`
- Expected business result: The MOTO API payment leaves the pending-authorisation state. On successful authorisation, Public API returns `204`; the payment state is updated in Connector and later visible through payment reads/events.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create MOTO API payment` | `POST /v1/payments` with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, `authorisation_mode=moto_api`, and no `return_url` to create the payment and capture `payment_id={generated payment id}` and `one_time_token={auth_url_post.params.one_time_token}`. | No | No | No | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 2 | `authorise MOTO API payment` | `POST /v1/auth` with body `one_time_token={one_time_token from create MOTO API payment}`, `card_number={12..19 character card number}`, `cardholder_name={nonblank string <=255 chars}`, `cvc={3..4 chars}`, and `expiry_date={MM/YY}` to authorise the payment. | No | No | No | No generated call to `POST /v1/auth`. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get payment` | `GET /v1/payments/{paymentId}` with authenticated Bearer caller context and `paymentId={payment_id from create MOTO API payment}` to inspect the resulting payment state. | No | Attempted by 2 call(s): `test_22_getOnPaymentShowsFaults_100_101` (500), `test_23_getOnPaymentReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |
| 2 | `get payment events` | `GET /v1/payments/{paymentId}/events` with `paymentId={payment_id from create MOTO API payment}` to inspect authorisation events. | No | Attempted by 2 call(s): `test_24_getOnEventsShowsFaults_100_101` (500), `test_29_getOnEventsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `create MOTO API payment` | `CreateChargeExceptionMapper 404 without ErrorIdentifier.AGREEMENT_NOT_FOUND -> RequestError.Code.CREATE_PAYMENT_ACCOUNT_ERROR` | Connector cannot find the gateway account for MOTO API charge creation. | No | High | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create MOTO API payment` | `ErrorIdentifier.ZERO_AMOUNT_NOT_ALLOWED -> RequestError.Code.CREATE_PAYMENT_VALIDATION_ERROR` | The MOTO API payment amount is zero and the account is not allowed to create zero-amount payments. | No | High | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create MOTO API payment` | `ErrorIdentifier.MOTO_NOT_ALLOWED -> RequestError.Code.CREATE_PAYMENT_MOTO_NOT_ENABLED` | The account is not enabled for MOTO payment creation. | No | High | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create MOTO API payment` | `ErrorIdentifier.AUTHORISATION_API_NOT_ALLOWED -> RequestError.Code.CREATE_PAYMENT_AUTHORISATION_API_NOT_ENABLED` | The account is not enabled for API authorisation using `authorisation_mode=moto_api`. | No | High | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create MOTO API payment` | `ErrorIdentifier.UNEXPECTED_ATTRIBUTE -> RequestError.Code.GENERIC_UNEXPECTED_FIELD_ERROR_MESSAGE_FROM_CONNECTOR` | The MOTO API create request includes a Connector-prohibited hosted-journey attribute such as `return_url`. | No | High | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create MOTO API payment` | `ErrorIdentifier.ACCOUNT_DISABLED -> RequestError.Code.ACCOUNT_DISABLED` | GOV.UK Pay has disabled payment creation on the account. | No | High | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `create MOTO API payment` | `ErrorIdentifier.ACCOUNT_NOT_LINKED_WITH_PSP -> RequestError.Code.ACCOUNT_NOT_LINKED_WITH_PSP` | The account is not fully linked to a payment service provider. | No | High | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `authorise MOTO API payment` | `ErrorIdentifier.ONE_TIME_TOKEN_ALREADY_USED -> RequestError.Code.AUTHORISATION_ONE_TIME_TOKEN_ALREADY_USED_ERROR` | The one-time token has already been consumed by an earlier authorisation attempt. | No | High | No generated call to `POST /v1/auth`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `authorise MOTO API payment` | `ErrorIdentifier.ONE_TIME_TOKEN_INVALID -> RequestError.Code.AUTHORISATION_ONE_TIME_TOKEN_INVALID_ERROR` | The one-time token is unknown, invalid, or not current for a pending MOTO API payment. | No | High | No generated call to `POST /v1/auth`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `authorise MOTO API payment` | `ErrorIdentifier.CARD_NUMBER_REJECTED -> RequestError.Code.AUTHORISATION_CARD_NUMBER_REJECTED_ERROR` | Connector rejects the supplied card number or card type for authorisation. | No | High | No generated call to `POST /v1/auth`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `authorise MOTO API payment` | `ErrorIdentifier.AUTHORISATION_REJECTED -> RequestError.Code.AUTHORISATION_REJECTED_ERROR` | Connector or the payment provider rejects the card authorisation attempt. | No | High | No generated call to `POST /v1/auth`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `authorise MOTO API payment` | `ErrorIdentifier.INVALID_ATTRIBUTE_VALUE -> RequestError.Code.GENERIC_VALIDATION_EXCEPTION_MESSAGE_FROM_CONNECTOR` | Connector rejects an authorisation attribute value that passed Public API's structural request validation. | No | High | No generated call to `POST /v1/auth`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `0/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/12`
- Behavior outcome checklist summary: `0/13`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T4`

### `B10`: `Take a recurring payment from an active agreement`

- Business goal: Charge a payer using the saved payment instrument attached to an active recurring agreement.
- Starting point: `Pre-existing service/upstream state required`
- Expected business result: A new payment is created using the saved payment instrument. It is linked to the recurring agreement and should not expose a hosted return journey.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `take recurring payment` | `POST /v1/payments` with authenticated Bearer caller context for `accountId={gateway account id}`, upstream state `agreement status=active with saved payment instrument`, body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, `authorisation_mode=agreement`, `agreement_id={pre-existing active agreement id}`, and no `return_url`, no `email`, and no `prefilled_cardholder_details` to create the recurring payment and capture `payment_id={generated payment id}`. | No | No | No | No exact attempt: no `authorisation_mode=agreement` and `agreement_id` body appears in generated tests. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get payment` | `GET /v1/payments/{paymentId}` with `paymentId={payment_id from take recurring payment}` to inspect the recurring payment. | No | Attempted by 2 call(s): `test_22_getOnPaymentShowsFaults_100_101` (500), `test_23_getOnPaymentReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |
| 2 | `search payments` | `GET /v1/payments` with `agreement_id={agreement_id used by take recurring payment}` to find payments charged through that agreement. | No | Attempted by 10 call(s): `test_0_getOnV1PaymentsWithQueryParamsEmptyEmailShowsFaults_100_101` (500), `test_1_getOnPaymentsShowsFaults_100_101` (500), `test_2_getOnPaymentsShowsFaults_100_101` (500), `test_3_getOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 2, 500x 8; all stop at generic auth/admission. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `take recurring payment` | `RequestJsonParser agreement_id branch -> RequestError.Code.CREATE_PAYMENT_UNEXPECTED_FIELD_ERROR` | The request sends `agreement_id` without setting `authorisation_mode=agreement`. | No | High | No exact attempt: no `authorisation_mode=agreement` and `agreement_id` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `take recurring payment` | `ErrorIdentifier.MISSING_MANDATORY_ATTRIBUTE -> RequestError.Code.GENERIC_MISSING_FIELD_ERROR_MESSAGE_FROM_CONNECTOR` | The request uses `authorisation_mode=agreement` but omits the Connector-required `agreement_id`. | No | High | No exact attempt: no `authorisation_mode=agreement` and `agreement_id` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `take recurring payment` | `ErrorIdentifier.AGREEMENT_NOT_FOUND -> RequestError.Code.CREATE_PAYMENT_VALIDATION_ERROR` | `agreement_id` does not identify an agreement Connector can find for the account. | No | High | No exact attempt: no `authorisation_mode=agreement` and `agreement_id` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `take recurring payment` | `ErrorIdentifier.AGREEMENT_NOT_ACTIVE -> RequestError.Code.CREATE_PAYMENT_VALIDATION_ERROR` | `agreement_id` identifies an agreement that is not active. | No | High | No exact attempt: no `authorisation_mode=agreement` and `agreement_id` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `take recurring payment` | `ErrorIdentifier.UNEXPECTED_ATTRIBUTE -> RequestError.Code.GENERIC_UNEXPECTED_FIELD_ERROR_MESSAGE_FROM_CONNECTOR` | The agreement-mode request includes Connector-prohibited hosted or payer-entry fields such as `return_url`, `email`, or prefilled cardholder details. | No | High | No exact attempt: no `authorisation_mode=agreement` and `agreement_id` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `take recurring payment` | `ErrorIdentifier.RECURRING_CARD_PAYMENTS_NOT_ALLOWED -> RequestError.Code.RECURRING_CARD_PAYMENTS_NOT_ALLOWED_ERROR` | The account is not allowed to create recurring-card payments. | No | High | No exact attempt: no `authorisation_mode=agreement` and `agreement_id` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `take recurring payment` | `ErrorIdentifier.ZERO_AMOUNT_NOT_ALLOWED -> RequestError.Code.CREATE_PAYMENT_VALIDATION_ERROR` | The recurring payment amount is zero and the account is not allowed to create zero-amount payments. | No | High | No exact attempt: no `authorisation_mode=agreement` and `agreement_id` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `take recurring payment` | `ErrorIdentifier.ACCOUNT_DISABLED -> RequestError.Code.ACCOUNT_DISABLED` | GOV.UK Pay has disabled payment creation on the account. | No | High | No exact attempt: no `authorisation_mode=agreement` and `agreement_id` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `take recurring payment` | `ErrorIdentifier.ACCOUNT_NOT_LINKED_WITH_PSP -> RequestError.Code.ACCOUNT_NOT_LINKED_WITH_PSP` | The account is not fully linked to a payment service provider. | No | High | No exact attempt: no `authorisation_mode=agreement` and `agreement_id` body appears in generated tests. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `0/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/9`
- Behavior outcome checklist summary: `0/10`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T5`

### `B11`: `Retrieve one payment`

- Business goal: Inspect one payment’s current public details and action links.
- Starting point: `No prior service state`
- Expected business result: The caller receives one payment representation with public links. No payment state is changed by the read.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create web card payment` | `POST /v1/payments` with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, and `return_url={valid URL}` to create a payment and capture `payment_id={generated payment id}`. | Yes | No | No | Attempted by 10 call(s): `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 3, 500x 7; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 2 | `get payment` | `GET /v1/payments/{paymentId}` with the same authenticated account, path `paymentId={payment_id from create web card payment}`, and header `X-Ledger` omitted to read the payment using the default strategy. | Yes | No | No | Attempted by 2 call(s): `test_22_getOnPaymentShowsFaults_100_101` (500), `test_23_getOnPaymentReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get payment events` | `GET /v1/payments/{paymentId}/events` with `paymentId={payment_id from create web card payment}` to inspect state history. | No | Attempted by 2 call(s): `test_24_getOnEventsShowsFaults_100_101` (500), `test_29_getOnEventsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `get payment` | `GetChargeExceptionMapper -> RequestError.Code.GET_PAYMENT_NOT_FOUND_ERROR` | `paymentId` is not found for the authenticated account by the selected strategy, or by either Connector or Ledger in the default strategy. | No | High | Attempted by 2 call(s): `test_22_getOnPaymentShowsFaults_100_101` (500), `test_23_getOnPaymentReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T1`

### `B12`: `Cancel an unfinished payment`

- Business goal: Cancel a payment that has not reached a finished state.
- Starting point: `No prior service state`
- Expected business result: The payment moves to cancelled and can no longer be completed by the payer.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create web card payment` | `POST /v1/payments` with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, and `return_url={valid URL}` to create an unfinished payment and capture `payment_id={generated payment id}`. | Yes | No | No | Attempted by 10 call(s): `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 3, 500x 7; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 2 | `cancel payment` | `POST /v1/payments/{paymentId}/cancel` with the same authenticated account and path `paymentId={payment_id from create web card payment}` to cancel the unfinished payment. | Yes | No | No | Attempted by 2 call(s): `test_26_postOnCancelShowsFaults_100_101` (500), `test_31_postOnCancelReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get payment` | `GET /v1/payments/{paymentId}` with `paymentId={payment_id from create web card payment}` to inspect cancelled state. | No | Attempted by 2 call(s): `test_22_getOnPaymentShowsFaults_100_101` (500), `test_23_getOnPaymentReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |
| 2 | `get payment events` | `GET /v1/payments/{paymentId}/events` with `paymentId={payment_id from create web card payment}` to inspect the cancellation event. | No | Attempted by 2 call(s): `test_24_getOnEventsShowsFaults_100_101` (500), `test_29_getOnEventsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `cancel payment` | `CancelChargeExceptionMapper -> RequestError.Code.CANCEL_PAYMENT_NOT_FOUND_ERROR` | Connector cannot find `paymentId` for the authenticated account. | No | High | Attempted by 2 call(s): `test_26_postOnCancelShowsFaults_100_101` (500), `test_31_postOnCancelReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `cancel payment` | `CancelChargeExceptionMapper -> RequestError.Code.CANCEL_PAYMENT_CONNECTOR_BAD_REQUEST_ERROR` | Connector rejects cancellation because the payment's current state is not cancellable. | No | High | Attempted by 2 call(s): `test_26_postOnCancelShowsFaults_100_101` (500), `test_31_postOnCancelReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `cancel payment` | `CancelChargeExceptionMapper -> RequestError.Code.CANCEL_PAYMENT_CONNECTOR_CONFLICT_ERROR` | Connector reports a cancellation conflict for the payment state transition. | No | High | Attempted by 2 call(s): `test_26_postOnCancelShowsFaults_100_101` (500), `test_31_postOnCancelReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T1`

### `B13`: `Capture a delayed MOTO API payment`

- Business goal: Capture funds for a delayed-capture payment after successful card authorisation.
- Starting point: `No prior service state`
- Expected business result: The delayed payment is captured. Public API returns `204` when Connector returns no content for capture.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create MOTO API payment` | `POST /v1/payments` with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, `authorisation_mode=moto_api`, `delayed_capture=true`, and no `return_url` to create the delayed payment and capture `payment_id={generated payment id}` and `one_time_token={generated token}`. | No | No | No | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 2 | `authorise MOTO API payment` | `POST /v1/auth` with body `one_time_token={one_time_token from create MOTO API payment}`, `card_number={valid card number}`, `cardholder_name={nonblank string}`, `cvc={valid CVC}`, and `expiry_date={MM/YY}` to authorise the delayed payment. | No | No | No | No generated call to `POST /v1/auth`. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 3 | `capture delayed payment` | `POST /v1/payments/{paymentId}/capture` with authenticated Bearer caller context for the same account and path `paymentId={payment_id from create MOTO API payment}` to capture the authorised delayed payment. | Yes | No | No | Attempted by 2 call(s): `test_27_postOnCaptureShowsFaults_100_101` (500), `test_32_postOnCaptureReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get payment` | `GET /v1/payments/{paymentId}` with `paymentId={payment_id from create MOTO API payment}` to inspect captured state. | No | Attempted by 2 call(s): `test_22_getOnPaymentShowsFaults_100_101` (500), `test_23_getOnPaymentReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |
| 2 | `get payment events` | `GET /v1/payments/{paymentId}/events` with `paymentId={payment_id from create MOTO API payment}` to inspect authorisation and capture events. | No | Attempted by 2 call(s): `test_24_getOnEventsShowsFaults_100_101` (500), `test_29_getOnEventsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `capture delayed payment` | `CaptureChargeExceptionMapper -> RequestError.Code.CAPTURE_PAYMENT_NOT_FOUND_ERROR` | Connector cannot find `paymentId` for the authenticated account. | No | High | Attempted by 2 call(s): `test_27_postOnCaptureShowsFaults_100_101` (500), `test_32_postOnCaptureReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `capture delayed payment` | `CaptureChargeExceptionMapper -> RequestError.Code.CAPTURE_PAYMENT_CONNECTOR_BAD_REQUEST_ERROR` | Connector rejects capture because the payment is not in a capturable delayed-payment state. | No | High | Attempted by 2 call(s): `test_27_postOnCaptureShowsFaults_100_101` (500), `test_32_postOnCaptureReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `capture delayed payment` | `CaptureChargeExceptionMapper -> RequestError.Code.CAPTURE_PAYMENT_CONNECTOR_CONFLICT_ERROR` | Connector reports a capture conflict for the payment lifecycle state. | No | High | Attempted by 2 call(s): `test_27_postOnCaptureShowsFaults_100_101` (500), `test_32_postOnCaptureReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `1/3`, application reached `0/3`, context-valid success `0/3`
- Happy-path summary: `0/1`
- Failure summary: `0/3`
- Behavior outcome checklist summary: `0/4`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T4`

### `B14`: `Read payment event history`

- Business goal: Inspect the sequence of state-change events for one payment.
- Starting point: `No prior service state`
- Expected business result: The caller receives an account-scoped event list for the payment. No state changes occur from reading events.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create web card payment` | `POST /v1/payments` with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, and `return_url={valid URL}` to create a payment and capture `payment_id={generated payment id}`. | Yes | No | No | Attempted by 10 call(s): `test_6_getOnV1PaymentsWithQueryParamsEmptyReferenceAndEmptyEmailShowsFaults_100_101` (500), `test_11_postOnPaymentsShowsFaults_100_101` (500), `test_12_postOnPaymentsShowsFaults_100_101` (500), `test_13_postOnPaymentsShowsFaults_100_101` (500). Result classes: 401x 3, 500x 7; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 2 | `get payment events` | `GET /v1/payments/{paymentId}/events` with the same authenticated account, path `paymentId={payment_id from create web card payment}`, and header `X-Ledger` omitted to retrieve event history. | Yes | No | No | Attempted by 2 call(s): `test_24_getOnEventsShowsFaults_100_101` (500), `test_29_getOnEventsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get payment` | `GET /v1/payments/{paymentId}` with `paymentId={payment_id from create web card payment}` to compare current payment state with event history. | No | Attempted by 2 call(s): `test_22_getOnPaymentShowsFaults_100_101` (500), `test_23_getOnPaymentReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `get payment events` | `GetEventsExceptionMapper -> RequestError.Code.GET_PAYMENT_EVENTS_NOT_FOUND_ERROR` | `paymentId` has no event history visible to the authenticated account in the selected strategy, or in either Connector or Ledger under the default strategy. | No | High | Attempted by 2 call(s): `test_24_getOnEventsShowsFaults_100_101` (500), `test_29_getOnEventsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `2/2`, application reached `0/2`, context-valid success `0/2`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T1`

### `B15`: `Create a refund for a successful payment`

- Business goal: Submit a full or partial refund for a payment that has completed successfully and has refundable amount remaining.
- Starting point: `No prior service state`
- Expected business result: A refund is accepted and returned with public links. The implementation returns `202 Accepted` with a refund response entity.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create MOTO API payment` | `POST /v1/payments` with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={payment amount}`, `reference={nonblank string}`, `description={nonblank string}`, `authorisation_mode=moto_api`, and no `return_url` to create the payment and capture `payment_id={generated payment id}` and `one_time_token={generated token}`. | No | No | No | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 2 | `authorise MOTO API payment` | `POST /v1/auth` with body `one_time_token={one_time_token from create MOTO API payment}`, `card_number={valid card number}`, `cardholder_name={nonblank string}`, `cvc={valid CVC}`, and `expiry_date={MM/YY}` to make the payment successful and refundable. | No | No | No | No generated call to `POST /v1/auth`. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 3 | `refund payment` | `POST /v1/payments/{paymentId}/refunds` with authenticated Bearer caller context for the same account, path `paymentId={payment_id from create MOTO API payment}`, and body `amount={refund amount between 1 and current available amount}` and `refund_amount_available={current available amount before this refund}` to submit the refund and capture `refund_id={generated refund id}`. | Yes | No | No | Attempted by 2 call(s): `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get refund` | `GET /v1/payments/{paymentId}/refunds/{refundId}` with `paymentId={payment_id from create MOTO API payment}` and `refundId={refund_id from refund payment}` to inspect the refund. | No | Attempted by 2 call(s): `test_34_getOnRefundShowsFaults_100_101` (500), `test_35_getOnRefundReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |
| 2 | `list payment refunds` | `GET /v1/payments/{paymentId}/refunds` with `paymentId={payment_id from create MOTO API payment}` to inspect all refunds for the payment. | No | Attempted by 2 call(s): `test_25_getOnPaymentRefundsShowsFaults_100_101` (500), `test_30_getOnPaymentRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `refund payment` | `PaymentRefundRequestValidator.validateAmount -> RequestError.Code.CREATE_PAYMENT_REFUND_VALIDATION_ERROR` | Refund `amount` is less than 1. | No | High | Attempted by 2 call(s): `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `refund payment` | `PaymentRefundRequestValidator.validateAmount -> RequestError.Code.CREATE_PAYMENT_REFUND_VALIDATION_ERROR` | Refund `amount` is greater than 10000000. | No | High | Attempted by 2 call(s): `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `refund payment` | `CreateRefundService.getRefundAmountAvailableFromPayment -> RequestError.Code.GET_PAYMENT_NOT_FOUND_ERROR` | `refund_amount_available` is omitted and `paymentId` cannot be found when Public API tries to read the payment to derive available refund amount. | No | High | Attempted by 2 call(s): `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `refund payment` | `CreateRefundExceptionMapper -> RequestError.Code.CREATE_PAYMENT_REFUND_NOT_FOUND_ERROR` | Connector cannot find `paymentId` for refund creation. | No | High | Attempted by 2 call(s): `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `refund payment` | `ErrorIdentifier.ACCOUNT_DISABLED -> RequestError.Code.ACCOUNT_DISABLED` | GOV.UK Pay has disabled refund creation on the account. | No | High | Attempted by 2 call(s): `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `refund payment` | `ErrorIdentifier.REFUND_NOT_AVAILABLE with reason -> RequestError.Code.CREATE_PAYMENT_REFUND_NOT_AVAILABLE` | Connector reports that the payment is not currently available for refund and supplies the payment refund status as the reason. | No | High | Attempted by 2 call(s): `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `refund payment` | `ErrorIdentifier.REFUND_NOT_AVAILABLE_DUE_TO_DISPUTE -> RequestError.Code.CREATE_PAYMENT_REFUND_NOT_AVAILABLE_DUE_TO_DISPUTE` | The payment is disputed and Connector blocks refund creation. | No | High | Attempted by 2 call(s): `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `refund payment` | `ErrorIdentifier.REFUND_AMOUNT_AVAILABLE_MISMATCH -> RequestError.Code.CREATE_PAYMENT_REFUND_AMOUNT_AVAILABLE_MISMATCH` | The supplied or derived `refund_amount_available` does not match Connector's current refundable amount for the payment. | No | High | Attempted by 2 call(s): `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `1/3`, application reached `0/3`, context-valid success `0/3`
- Happy-path summary: `0/1`
- Failure summary: `0/8`
- Behavior outcome checklist summary: `0/9`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T4`

### `B16`: `List refunds for a payment`

- Business goal: Read all refund records under one payment.
- Starting point: `No prior service state`
- Expected business result: The caller receives the payment-scoped refund collection. No refund state changes during listing.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create MOTO API payment` | `POST /v1/payments` with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={payment amount}`, `reference={nonblank string}`, `description={nonblank string}`, `authorisation_mode=moto_api`, and no `return_url` to create the payment and capture `payment_id={generated payment id}` and `one_time_token={generated token}`. | No | No | No | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 2 | `authorise MOTO API payment` | `POST /v1/auth` with body `one_time_token={one_time_token from create MOTO API payment}`, `card_number={valid card number}`, `cardholder_name={nonblank string}`, `cvc={valid CVC}`, and `expiry_date={MM/YY}` to make the payment refundable. | No | No | No | No generated call to `POST /v1/auth`. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 3 | `refund payment` | `POST /v1/payments/{paymentId}/refunds` with authenticated Bearer caller context, path `paymentId={payment_id from create MOTO API payment}`, and body `amount={refund amount}` and `refund_amount_available={current available amount before refund}` to create a refund. | Yes | No | No | Attempted by 2 call(s): `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 4 | `list payment refunds` | `GET /v1/payments/{paymentId}/refunds` with the same authenticated account, path `paymentId={payment_id from create MOTO API payment}`, and header `X-Ledger` omitted to list refunds for the payment. | Yes | No | No | Attempted by 2 call(s): `test_25_getOnPaymentRefundsShowsFaults_100_101` (500), `test_30_getOnPaymentRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `get refund` | `GET /v1/payments/{paymentId}/refunds/{refundId}` with `paymentId={payment_id from create MOTO API payment}` and `refundId={refund_id from refund payment}` to inspect a specific refund. | No | Attempted by 2 call(s): `test_34_getOnRefundShowsFaults_100_101` (500), `test_35_getOnRefundReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `list payment refunds` | `GetRefundsExceptionMapper -> RequestError.Code.GET_PAYMENT_REFUNDS_NOT_FOUND_ERROR` | `paymentId` has no refund collection visible to the authenticated account in Ledger by default, or in Connector when `connector-only` is selected. | No | High | Attempted by 2 call(s): `test_25_getOnPaymentRefundsShowsFaults_100_101` (500), `test_30_getOnPaymentRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `2/4`, application reached `0/4`, context-valid success `0/4`
- Happy-path summary: `0/1`
- Failure summary: `0/1`
- Behavior outcome checklist summary: `0/2`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T4`

### `B17`: `Retrieve one payment refund`

- Business goal: Inspect one refund under its parent payment.
- Starting point: `No prior service state`
- Expected business result: The caller receives one refund resource linked to its payment. No state changes occur from retrieval.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `create MOTO API payment` | `POST /v1/payments` with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={payment amount}`, `reference={nonblank string}`, `description={nonblank string}`, `authorisation_mode=moto_api`, and no `return_url` to create a payment and capture `payment_id={generated payment id}` and `one_time_token={generated token}`. | No | No | No | No exact attempt: no `authorisation_mode=moto_api` body appears in generated tests. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 2 | `authorise MOTO API payment` | `POST /v1/auth` with body `one_time_token={one_time_token from create MOTO API payment}`, `card_number={valid card number}`, `cardholder_name={nonblank string}`, `cvc={valid CVC}`, and `expiry_date={MM/YY}` to make the payment refundable. | No | No | No | No generated call to `POST /v1/auth`. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 3 | `refund payment` | `POST /v1/payments/{paymentId}/refunds` with authenticated Bearer caller context, path `paymentId={payment_id from create MOTO API payment}`, and body `amount={refund amount}` and `refund_amount_available={current available amount before refund}` to create a refund and capture `refund_id={generated refund id}`. | Yes | No | No | Attempted by 2 call(s): `test_28_postOnRefundsShowsFaults_100_101` (500), `test_33_postOnRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |
| 4 | `get refund` | `GET /v1/payments/{paymentId}/refunds/{refundId}` with the same authenticated account, path `paymentId={payment_id from create MOTO API payment}`, path `refundId={refund_id from refund payment}`, and header `X-Ledger` omitted to retrieve the refund. | Yes | No | No | Attempted by 2 call(s): `test_34_getOnRefundShowsFaults_100_101` (500), `test_35_getOnRefundReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

| Step | Exact Function Name | Operation | Executed? | Evidence |
|---:|---|---|---|---|
| 1 | `list payment refunds` | `GET /v1/payments/{paymentId}/refunds` with `paymentId={payment_id from create MOTO API payment}` to confirm the refund appears in the parent collection. | No | Attempted by 2 call(s): `test_25_getOnPaymentRefundsShowsFaults_100_101` (500), `test_30_getOnPaymentRefundsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. |

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `get refund` | `GetRefundExceptionMapper -> RequestError.Code.GET_PAYMENT_REFUND_NOT_FOUND_ERROR` | `paymentId` is unknown or not scoped to the authenticated account for the selected refund-read strategy. | No | High | Attempted by 2 call(s): `test_34_getOnRefundShowsFaults_100_101` (500), `test_35_getOnRefundReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `get refund` | `GetRefundExceptionMapper -> RequestError.Code.GET_PAYMENT_REFUND_NOT_FOUND_ERROR` | `refundId` is unknown, belongs to another payment, or belongs to another account. | No | High | Attempted by 2 call(s): `test_34_getOnRefundShowsFaults_100_101` (500), `test_35_getOnRefundReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 1; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `2/4`, application reached `0/4`, context-valid success `0/4`
- Happy-path summary: `0/1`
- Failure summary: `0/2`
- Behavior outcome checklist summary: `0/3`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T4`

### `B18`: `Search refunds`

- Business goal: Find refund transactions across payments for the authenticated account.
- Starting point: `No prior service state`
- Expected business result: No state changes. The caller receives paginated refund search results.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `search refunds` | `GET /v1/refunds` with authenticated Bearer caller context for `accountId={gateway account id}` and query `from_date={UTC ISO date-time}`, `to_date={UTC ISO date-time}`, `from_settled_date={ISO date}`, `to_settled_date={ISO date}`, `page={integer >= 1}`, and `display_size={1..500}` to retrieve matching refunds. | Yes | No | No | Attempted by 4 call(s): `test_5_getOnRefundsShowsFaults_100_101` (500), `test_8_getOnRefundsShowsFaults_100_101` (500), `test_9_getOnRefundsShowsFaults_100_101` (500), `test_18_getOnV1RefundsWithQueryParamsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 3; all stop at generic auth/admission. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

None documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `search refunds` | `SearchValidator.validateFromDate -> RequestError.Code.SEARCH_REFUNDS_VALIDATION_ERROR` | `from_date` is not a valid UTC ISO-8601 date-time accepted by `DateValidator`. | No | High | Attempted by 4 call(s): `test_5_getOnRefundsShowsFaults_100_101` (500), `test_8_getOnRefundsShowsFaults_100_101` (500), `test_9_getOnRefundsShowsFaults_100_101` (500), `test_18_getOnV1RefundsWithQueryParamsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 3; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search refunds` | `SearchValidator.validateToDate -> RequestError.Code.SEARCH_REFUNDS_VALIDATION_ERROR` | `to_date` is not a valid UTC ISO-8601 date-time accepted by `DateValidator`. | No | High | Attempted by 4 call(s): `test_5_getOnRefundsShowsFaults_100_101` (500), `test_8_getOnRefundsShowsFaults_100_101` (500), `test_9_getOnRefundsShowsFaults_100_101` (500), `test_18_getOnV1RefundsWithQueryParamsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 3; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search refunds` | `SearchValidator.validateFromSettledDate -> RequestError.Code.SEARCH_REFUNDS_VALIDATION_ERROR` | `from_settled_date` is nonblank and is not a valid ISO-8601 date-only value. | No | High | Attempted by 4 call(s): `test_5_getOnRefundsShowsFaults_100_101` (500), `test_8_getOnRefundsShowsFaults_100_101` (500), `test_9_getOnRefundsShowsFaults_100_101` (500), `test_18_getOnV1RefundsWithQueryParamsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 3; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search refunds` | `SearchValidator.validateToSettledDate -> RequestError.Code.SEARCH_REFUNDS_VALIDATION_ERROR` | `to_settled_date` is nonblank and is not a valid ISO-8601 date-only value. | No | High | Attempted by 4 call(s): `test_5_getOnRefundsShowsFaults_100_101` (500), `test_8_getOnRefundsShowsFaults_100_101` (500), `test_9_getOnRefundsShowsFaults_100_101` (500), `test_18_getOnV1RefundsWithQueryParamsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 3; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search refunds` | `SearchValidator.validatePageIfNotNull -> RequestError.Code.SEARCH_REFUNDS_VALIDATION_ERROR` | `page` is nonblank and is not numeric or is less than 1. | No | High | Attempted by 4 call(s): `test_5_getOnRefundsShowsFaults_100_101` (500), `test_8_getOnRefundsShowsFaults_100_101` (500), `test_9_getOnRefundsShowsFaults_100_101` (500), `test_18_getOnV1RefundsWithQueryParamsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 3; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search refunds` | `SearchValidator.validateDisplaySizeIfNotNull -> RequestError.Code.SEARCH_REFUNDS_VALIDATION_ERROR` | `display_size` is nonblank and is not numeric, is less than 1, or is greater than 500. | No | High | Attempted by 4 call(s): `test_5_getOnRefundsShowsFaults_100_101` (500), `test_8_getOnRefundsShowsFaults_100_101` (500), `test_9_getOnRefundsShowsFaults_100_101` (500), `test_18_getOnV1RefundsWithQueryParamsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 3; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search refunds` | `SearchRefundsExceptionMapper -> RequestError.Code.SEARCH_REFUNDS_NOT_FOUND` | Ledger returns 404 for the requested refund search page. | No | High | Attempted by 4 call(s): `test_5_getOnRefundsShowsFaults_100_101` (500), `test_8_getOnRefundsShowsFaults_100_101` (500), `test_9_getOnRefundsShowsFaults_100_101` (500), `test_18_getOnV1RefundsWithQueryParamsReturnsMismatchResponseWithSchema` (401). Result classes: 401x 1, 500x 3; all stop at generic auth/admission. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `1/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/7`
- Behavior outcome checklist summary: `0/8`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T9`

### `B19`: `Search disputes`

- Business goal: Find dispute records associated with payments for the authenticated account.
- Starting point: `No prior service state`
- Expected business result: No state changes. The caller receives paginated dispute records.

#### Required execution workflow coverage

| Step | Exact Function Name | Operation And Required Bindings | Attempted? | Application Reached? | Context-Valid Success? | Test Evidence | JaCoCo Evidence |
|---:|---|---|---|---|---|---|---|
| 1 | `search disputes` | `GET /v1/disputes` with authenticated Bearer caller context for `accountId={gateway account id}` and query `from_date={UTC ISO date-time}`, `to_date={UTC ISO date-time}`, `from_settled_date={ISO date}`, `to_settled_date={ISO date}`, `status={needs_response\\|under_review\\|lost\\|won}`, `page={integer >= 1}`, and `display_size={1..500}` to retrieve matching disputes. | No | No | No | No generated call to `GET /v1/disputes`. | No covered mapped business method in JaCoCo; only constructor/class-initializer coverage for relevant resource/service. |

- Happy-path item: `Not Covered`. No test executes all required steps in documented order with valid authentication, state, response-to-request bindings, and terminal business result.

#### Optional verification coverage

None documented.

#### Concrete business-failure coverage

| Failing Function | Source Discriminator | Failure Condition | Covered? | Confidence | Test Evidence | State/Side-Effect Evidence | JaCoCo Evidence |
|---|---|---|---|---|---|---|---|
| `search disputes` | `SearchValidator.validateFromDate -> RequestError.Code.SEARCH_DISPUTES_VALIDATION_ERROR` | `from_date` is not a valid UTC ISO-8601 date-time accepted by `DateValidator`. | No | High | No generated call to `GET /v1/disputes`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search disputes` | `SearchValidator.validateToDate -> RequestError.Code.SEARCH_DISPUTES_VALIDATION_ERROR` | `to_date` is not a valid UTC ISO-8601 date-time accepted by `DateValidator`. | No | High | No generated call to `GET /v1/disputes`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search disputes` | `SearchValidator.validateFromSettledDate -> RequestError.Code.SEARCH_DISPUTES_VALIDATION_ERROR` | `from_settled_date` is nonblank and is not a valid ISO-8601 date-only value. | No | High | No generated call to `GET /v1/disputes`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search disputes` | `SearchValidator.validateToSettledDate -> RequestError.Code.SEARCH_DISPUTES_VALIDATION_ERROR` | `to_settled_date` is nonblank and is not a valid ISO-8601 date-only value. | No | High | No generated call to `GET /v1/disputes`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search disputes` | `SearchValidator.validatePageIfNotNull -> RequestError.Code.SEARCH_DISPUTES_VALIDATION_ERROR` | `page` is nonblank and is not numeric or is less than 1. | No | High | No generated call to `GET /v1/disputes`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search disputes` | `SearchValidator.validateDisplaySizeIfNotNull -> RequestError.Code.SEARCH_DISPUTES_VALIDATION_ERROR` | `display_size` is nonblank and is not numeric, is less than 1, or is greater than 500. | No | High | No generated call to `GET /v1/disputes`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search disputes` | `DisputeSearchValidator.validateState -> RequestError.Code.SEARCH_DISPUTES_VALIDATION_ERROR` | `status` is nonblank and is not one of `needs_response`, `under_review`, `lost`, or `won`. | No | High | No generated call to `GET /v1/disputes`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |
| `search disputes` | `SearchDisputesExceptionMapper -> RequestError.Code.SEARCH_DISPUTES_NOT_FOUND` | Ledger returns 404 for the requested dispute search page. | No | High | No generated call to `GET /v1/disputes`. | None. No test establishes valid unrelated prerequisites, exact target-invalid condition, persisted outcome, or absence of side effects. | Not corroborated. No covered discriminator branch or mapped business method evidence for this generated corpus. |

- Required-step summary: attempted `0/1`, application reached `0/1`, context-valid success `0/1`
- Happy-path summary: `0/1`
- Failure summary: `0/8`
- Behavior outcome checklist summary: `0/9`, counting one happy path plus all documented failures
- Status and confidence: `Not Covered`, High
- Exact gap: generated evidence does not pass generic authentication/admission into a mapped business function and does not satisfy documented state/value bindings.
- Recommended test IDs that close the gap: `T10`

## Cross-Behavior Gaps

- Route probes never establish a valid authenticated service account; `401` and `500` generic auth/admission outcomes dominate the generated corpus.
- Database/SUT state resets before every test, and no generated test builds a continuous stateful scenario with captured IDs.
- Shared `/v1/payments` behaviors are mostly unattempted: no generated body contains `set_up_agreement`, `authorisation_mode=moto_api`, `authorisation_mode=agreement`, `agreement_id`, or an `Idempotency-Key`.
- Assertions are status/schema/error-shape oriented and do not verify persisted payment, agreement, refund, event, Ledger, Connector, or side-effect state.
- Validator branches for agreements, payments, refunds, and disputes are not business-covered because invalid requests do not pass authentication and mapped endpoint admission first.
- JaCoCo coverage is concentrated in startup, filters, constructors, and generic error behavior; resource/service business methods are not reached in the generated suite.
- Asynchronous or eventually consistent operations such as payment indexing, refund creation, and event visibility have no polling or completion verification.

## Suggested Additional Tests

The following test specifications are intentionally concrete and use the local test HMAC secret `qwer9yuhgf`. The example API key `coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo` is `ApiKeyGenerator.apiKeyValueOf("coverage-token", "qwer9yuhgf")`; map that token to gateway account `12345` through `PublicAuthMockClient.mapBearerTokenToAccountId`.

### Test `T1`: `web payment lifecycle covers create, search, read, events, and cancel`

- Priority: `P0`
- Target behavior ID and name: B7 Create a web card payment; also closes B6, B11, B12, and B14 happy paths
- Target checklist item: happy path; exact functions `create web card payment`, `search payments`, `get payment`, `get payment events`, `cancel payment`
- Test category: success, state transition
- Why needed: this is the smallest continuous stateful card-payment scenario that proves generated `payment_id` binding and a real cancellation transition.
- Coverage delta if passing: function invocation context-valid for five functions; required-step context-valid success for B6, B7, B11, B12, B14; happy-path coverage for B6, B7, B11, B12, B14; optional verification for B7, B11, B12, B14.

#### Initial state and fixture plan

State:
- database/SUT reset occurs once before the test; all calls run in one method after that reset.
- Public Auth maps `coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo` to gateway account `12345`, token type `CARD`.
- Connector stubs create charge for account `12345` and returns `payment_id=pay_web_000000000000000001`, state `created`, amount `1200`, reference `coverage-web-001`, description `Coverage web payment`, return URL `https://service.example.test/return/coverage-web-001`, and cancel link.
- Ledger stubs search, get payment, and event history for the same `payment_id`; after the cancel call, Connector and Ledger read fixtures expose state `cancelled`.
- fixed clock: `2026-07-07T09:00:00Z`.
- external-domain stubs: Public Auth, Connector, Ledger reset before the test; no asynchronous wait beyond polling Ledger search up to 5 seconds for the created payment.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B7 step 1 / `create web card payment` | service account `12345`, Bearer token `coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo` | `POST /v1/payments` | `Authorization: Bearer coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo`; `Accept: application/json`; `Content-Type: application/json` | none | `{"amount":1200,"reference":"coverage-web-001","description":"Coverage web payment","return_url":"https://service.example.test/return/coverage-web-001","language":"en","email":"payer@example.test","metadata":{"case":"T1"},"prefilled_cardholder_details":{"cardholder_name":"Ada Lovelace","billing_address":{"line1":"1 Test Street","city":"London","postcode":"SW1A 1AA","country":"GB"}},"moto":false,"delayed_capture":false}` | response `payment_id -> pay_web_000000000000000001`; `Location -> /v1/payments/pay_web_000000000000000001` | `201`; body `payment_id=pay_web_000000000000000001`, `state.status=created`, `_links.self`, `_links.next_url`, `_links.cancel` | Connector has one created payment for account `12345`; no duplicate payment |
| 2 | B11 step 2 / `get payment` | same service account | `GET /v1/payments/pay_web_000000000000000001` | same `Authorization`; `Accept: application/json` | `paymentId=pay_web_000000000000000001` | none | `payment_id` from call 1 | `200`; body repeats amount, reference, description, state `created` | no state change |
| 3 | B6 step 1 / `search payments` | same service account | `GET /v1/payments?reference=coverage-web-001&page=1&display_size=1` | same `Authorization`; `Accept: application/json` | `reference=coverage-web-001`; `page=1`; `display_size=1` | none | reference from call 1 | `200`; one result with `payment_id=pay_web_000000000000000001` | no state change |
| 4 | B14 step 2 / `get payment events` | same service account | `GET /v1/payments/pay_web_000000000000000001/events` | same `Authorization`; `Accept: application/json` | `paymentId=pay_web_000000000000000001` | none | `payment_id` from call 1 | `200`; event list contains `created` event at `2026-07-07T09:00:00Z` | no state change |
| 5 | B12 step 2 / `cancel payment` | same service account | `POST /v1/payments/pay_web_000000000000000001/cancel` | same `Authorization`; `Accept: application/json` | `paymentId=pay_web_000000000000000001` | none | `payment_id` from call 1 | `204`; empty body | Connector state becomes `cancelled`; cancel link no longer usable |
| 6 | optional verification / `get payment` | same service account | `GET /v1/payments/pay_web_000000000000000001` | same `Authorization`; `Accept: application/json` | `paymentId=pay_web_000000000000000001` | none | `payment_id` from call 1 | `200`; body `state.status=cancelled`, `state.finished=true` | cancelled state visible through read |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `amount` | JSON body | `1200` | integer pence | yes | `0..10000000`, subject to Connector policy | account must allow nonzero card payments | nonzero ordinary payment amount |
| 1 | `reference` | JSON body | `coverage-web-001` | string | yes | nonblank, max 255 | used again in search | proves response-to-search binding |
| 1 | `return_url` | JSON body | `https://service.example.test/return/coverage-web-001` | absolute HTTPS URL | yes for web journey | valid URL | must be absent only for MOTO API/agreement mode | proves hosted web mode |
| 5 | payment lifecycle | Connector fixture | `created -> cancelled` | state transition | yes | cancellable unfinished payment | cancellation is invalid after finished/success/cancelled | target B12 transition |

#### Assertions

Assert exact HTTP statuses, `Location` header on creation, response `payment_id`, state, links, search result membership, event list content, cancellation `204`, final cancelled state, one Connector create call, one Connector cancel call, one Ledger search call, no second payment creation, and covered source methods `PaymentsResource.createNewPayment`, `searchPayments`, `getPayment`, `getPaymentEvents`, and `cancelPayment`.

#### Isolation and variants

Reset WireMock stubs and SUT state before the method; use deterministic IDs above; poll Ledger search for at most 5 seconds; add separate variants for zero amount, MOTO disabled, not-found cancel, and non-cancellable state.

### Test `T2`: `agreement creation, retrieval, search, and setup payment`

- Priority: `P0`
- Target behavior ID and name: B2 Create a recurring agreement; also closes B1, B3, and B4 happy paths
- Target checklist item: happy path; exact functions `create agreement`, `get agreement`, `search agreements`, `create setup-agreement payment`, optional `get payment`
- Test category: success, state transition
- Why needed: no agreement route is currently exercised, and B4 requires `agreement_id -> set_up_agreement` binding.
- Coverage delta if passing: function invocation and context-valid success for agreement create/read/search and setup-payment creation; happy-path coverage for B1, B2, B3, B4; optional verification for B2 and B4.

#### Initial state and fixture plan

State:
- reset SUT once before the test; all calls run in one method.
- Public Auth maps the coverage token to account `12345`.
- Connector create-agreement stub returns `agreement_id=12345678901234567890123456` for reference `coverage-agreement-001`.
- Ledger get/search agreement stubs return the same agreement with status `created`, service id `service-coverage-001`, and account `12345`.
- Connector create-payment stub accepts `set_up_agreement=12345678901234567890123456` and returns `payment_id=pay_setup_0000000000000001` with save-instrument intent.
- fixed clock: `2026-07-07T09:10:00Z`; no direct database setup replaces the create calls.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B2 step 1 / `create agreement` | service account `12345`, Bearer coverage token | `POST /v1/agreements` | `Authorization: Bearer coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo`; `Accept: application/json`; `Content-Type: application/json` | none | `{"reference":"coverage-agreement-001","description":"Coverage recurring agreement","user_identifier":"user-coverage-001"}` | response `agreement_id -> 12345678901234567890123456` | `201`; body `agreement_id=12345678901234567890123456`, `reference=coverage-agreement-001`, `description=Coverage recurring agreement` | Connector and Ledger expose created agreement |
| 2 | B3 step 2 / `get agreement` | same account | `GET /v1/agreements/12345678901234567890123456` | same `Authorization`; `Accept: application/json` | `agreementId=12345678901234567890123456` | none | agreement id from call 1 | `200`; body status `created`, reference and description match | no state change |
| 3 | B1 step 1 / `search agreements` | same account | `GET /v1/agreements?reference=coverage-agreement-001&page=1&display_size=1` | same `Authorization`; `Accept: application/json` | `reference=coverage-agreement-001`; `page=1`; `display_size=1` | none | reference from call 1 | `200`; one result with agreement id from call 1 | no state change |
| 4 | B4 step 2 / `create setup-agreement payment` | same account | `POST /v1/payments` | same `Authorization`; `Accept: application/json`; `Content-Type: application/json` | none | `{"amount":500,"reference":"coverage-setup-payment-001","description":"Coverage setup payment","return_url":"https://service.example.test/return/setup-001","set_up_agreement":"12345678901234567890123456"}` | `agreement_id` from call 1 -> `set_up_agreement` | `201`; body `payment_id=pay_setup_0000000000000001`, agreement link present | setup payment exists and is linked to agreement |
| 5 | optional verification / `get payment` | same account | `GET /v1/payments/pay_setup_0000000000000001` | same `Authorization`; `Accept: application/json` | `paymentId=pay_setup_0000000000000001` | none | `payment_id` from call 4 | `200`; body reference `coverage-setup-payment-001`, agreement id `12345678901234567890123456` | no state change |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | recurring capability | Connector fixture | enabled for account `12345` | boolean capability | yes | enabled | create agreement rejects disabled accounts | reaches success path |
| 1 | `reference` | JSON body | `coverage-agreement-001` | string | yes | nonblank, max 255 | reused in search | proves B1/B2 binding |
| 4 | `set_up_agreement` | JSON body | `12345678901234567890123456` | agreement id string | yes for setup payment | existing same-account agreement | maps to Connector `agreement_id` and save-instrument intent | target B4 binding |
| 4 | `return_url` | JSON body | `https://service.example.test/return/setup-001` | URL | yes | valid hosted return URL | setup payment is hosted web mode | avoids missing mandatory attribute branch |

#### Assertions

Assert `201` agreement creation, generated id reuse, Ledger post-create read, search result scope, `201` setup payment, setup payment body links/id, Connector request includes `agreement_id=12345678901234567890123456` and `save_payment_instrument_to_agreement=true`, no `authorisation_mode=moto_api`, no unrelated Connector error, and covered methods `AgreementsApiResource.createAgreement`, `getAgreement`, `getAgreements`, and `PaymentsResource.createNewPayment`.

#### Isolation and variants

Reset all stubs before the test; use deterministic IDs; add separate failure tests for recurring capability disabled, post-create Ledger 404, unknown `set_up_agreement`, incompatible authorisation mode, and missing `return_url`.

### Test `T3`: `cancel active recurring agreement`

- Priority: `P0`
- Target behavior ID and name: B5 Cancel an active recurring agreement
- Target checklist item: happy path; exact function `cancel agreement`
- Test category: success, state transition
- Why needed: Public API cannot complete the hosted setup journey, so B5 needs an explicit active-agreement upstream fixture.
- Coverage delta if passing: context-valid required step for B5, happy-path coverage for B5, optional `get agreement` verification.

#### Initial state and fixture plan

State:
- reset SUT once before the test.
- Public Auth maps the coverage token to account `12345`.
- Direct Connector/Ledger fixture creates agreement `12345678901234567890123456` with status `active`, saved payment instrument id `pi_coverage_001`, service id `service-coverage-001`, and account `12345`; this is prerequisite state only and does not cover B2 or B4.
- Connector cancel-agreement stub returns `204`; Ledger get-agreement stub after cancel returns status `cancelled`.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B5 step 1 / `cancel agreement` | service account `12345`, Bearer coverage token | `POST /v1/agreements/12345678901234567890123456/cancel` | `Authorization: Bearer coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo`; `Accept: application/json` | `agreementId=12345678901234567890123456` | none | pre-existing active agreement fixture | `204`; empty body | Connector marks agreement cancelled; no new payment created |
| 2 | optional verification / `get agreement` | same account | `GET /v1/agreements/12345678901234567890123456` | same `Authorization`; `Accept: application/json` | `agreementId=12345678901234567890123456` | none | agreement id from fixture | `200`; body status `cancelled` | cancelled state visible |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | agreement status | Connector fixture | `active` | enum | yes | `active` only for success | created/cancelled/inactive should fail | proves cancellable transition |
| 1 | saved payment instrument | Connector fixture | `pi_coverage_001` | string id | yes | existing instrument | active agreement must be set up upstream | makes B5 context valid |

#### Assertions

Assert `204`, Connector cancel request path includes account `12345` and agreement id, final Ledger read status `cancelled`, no payment creation/refund side effect, and covered source methods `AgreementsApiResource.cancelAgreement` and `AgreementsService.cancelAgreement`.

#### Isolation and variants

Reset stubs before test; add separate business-failure variants for unknown agreement id and non-active agreement state.

### Test `T4`: `moto delayed capture and refund lifecycle`

- Priority: `P0`
- Target behavior ID and name: B9 Authorise a MOTO API payment; also closes B13, B15, B16, and B17 happy paths
- Target checklist item: happy path; exact functions `create MOTO API payment`, `authorise MOTO API payment`, `capture delayed payment`, `refund payment`, `list payment refunds`, `get refund`
- Test category: success, state transition
- Why needed: refund behavior requires a successful refundable payment and currently no `/v1/auth` call exists.
- Coverage delta if passing: context-valid success and happy paths for B9, B13, B15, B16, B17; optional verification for those behaviors.

#### Initial state and fixture plan

State:
- reset SUT once before the test.
- Public Auth maps coverage token to account `12345`; `/v1/auth` uses token security, not Bearer auth.
- Connector is configured for MOTO and authorisation API on account `12345`.
- Connector create MOTO payment returns `payment_id=pay_moto_000000000000000001`, `one_time_token=ott_coverage_token_001`, `delayed_capture=true`, state `created` or `submitted` with awaiting authorisation.
- Connector authorisation accepts card details and moves payment to `submitted`/`awaiting_capture_request`.
- Connector capture returns `204` and final payment state `success` with `refund_summary.amount_available=5000`.
- Connector refund returns `refund_id=refund_000000000000000001`, amount `1200`, status `submitted`; Ledger/Connector refund reads return the same child refund under the parent payment.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B9 step 1 / `create MOTO API payment` | service account `12345`, Bearer coverage token | `POST /v1/payments` | `Authorization: Bearer coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo`; `Accept: application/json`; `Content-Type: application/json` | none | `{"amount":5000,"reference":"coverage-moto-001","description":"Coverage MOTO delayed capture","authorisation_mode":"moto_api","delayed_capture":true}` | response `payment_id -> pay_moto_000000000000000001`; response `_links.auth_url_post.params.one_time_token -> ott_coverage_token_001` | `201`; body has MOTO API auth URL post params and no hosted `next_url` | MOTO delayed payment exists awaiting authorisation |
| 2 | B9 step 2 / `authorise MOTO API payment` | one-time token context, no Bearer auth | `POST /v1/auth` | `Accept: application/json`; `Content-Type: application/json` | none | `{"one_time_token":"ott_coverage_token_001","card_number":"4242424242424242","cardholder_name":"Ada Lovelace","cvc":"123","expiry_date":"12/30"}` | token from call 1 | `204`; empty body | token consumed; payment awaiting capture |
| 3 | B13 step 3 / `capture delayed payment` | service account `12345`, Bearer coverage token | `POST /v1/payments/pay_moto_000000000000000001/capture` | `Authorization: Bearer coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo`; `Accept: application/json` | `paymentId=pay_moto_000000000000000001` | none | `payment_id` from call 1 | `204`; empty body | payment captured and successful |
| 4 | B15 step 3 / `refund payment` | same service account | `POST /v1/payments/pay_moto_000000000000000001/refunds` | same `Authorization`; `Accept: application/json`; `Content-Type: application/json` | `paymentId=pay_moto_000000000000000001` | `{"amount":1200,"refund_amount_available":5000}` | `payment_id` from call 1; available amount from captured payment fixture | `202`; body `refund_id=refund_000000000000000001`, `amount=1200`, `refund_amount_available=3800` | refund child exists; available amount reduced |
| 5 | B16 step 4 / `list payment refunds` | same service account | `GET /v1/payments/pay_moto_000000000000000001/refunds` | same `Authorization`; `Accept: application/json` | `paymentId=pay_moto_000000000000000001` | none | `payment_id` from call 1 | `200`; refunds list contains `refund_000000000000000001` | no state change |
| 6 | B17 step 4 / `get refund` | same service account | `GET /v1/payments/pay_moto_000000000000000001/refunds/refund_000000000000000001` | same `Authorization`; `Accept: application/json` | `paymentId=pay_moto_000000000000000001`; `refundId=refund_000000000000000001` | none | `payment_id` from call 1; `refund_id` from call 4 | `200`; body refund id, amount, status, parent payment link all match | no state change |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `authorisation_mode` | JSON body | `moto_api` | enum string | yes | `moto_api` | no `return_url` for MOTO API | generates one-time token |
| 1 | `delayed_capture` | JSON body | `true` | boolean | yes for B13 | true/false | capture endpoint valid only after delayed authorisation | enables B13 |
| 2 | `one_time_token` | JSON body | `ott_coverage_token_001` | token string | yes | valid unused token | consumed exactly once | binds auth to payment |
| 2 | card details | JSON body | `4242424242424242`, `Ada Lovelace`, `123`, `12/30` | card fields | yes | valid card number, CVC, MM/YY | must pass local and Connector validation | reaches success branch |
| 4 | `refund_amount_available` | JSON body | `5000` | integer pence | yes for concurrency assertion | equals current available amount | mismatch should produce `P0604` | proves refund concurrency binding |

#### Assertions

Assert statuses `201`, `204`, `204`, `202`, `200`, `200`; token consumed exactly once; Connector authorisation called with card details and token; capture called only after authorisation; refund response id and available amount; list/get refund both point to same parent payment; no forbidden hosted `return_url` in MOTO create; source corroboration for `PaymentsResource.createNewPayment`, `AuthorisationResource.authorisePayment`, `PaymentsResource.capturePayment`, `PaymentRefundsResource.submitRefund`, `getRefunds`, and `getRefundById`.

#### Isolation and variants

Reset Public Auth, Connector, and Ledger stubs; deterministic IDs; poll refund visibility for up to 5 seconds; add separate variants for invalid token, reused token, invalid card, non-capturable payment, refund amount below 1, refund unavailable, disputed payment, and amount-available mismatch.

### Test `T5`: `take recurring payment from active agreement`

- Priority: `P0`
- Target behavior ID and name: B10 Take a recurring payment from an active agreement
- Target checklist item: happy path; exact function `take recurring payment`
- Test category: success, ownership/eligibility
- Why needed: this shared `/v1/payments` variant is completely absent from generated tests.
- Coverage delta if passing: function invocation and context-valid success for `take recurring payment`; happy-path coverage for B10; optional payment read/search verification.

#### Initial state and fixture plan

State:
- reset SUT once before the test.
- Public Auth maps coverage token to account `12345`.
- Direct Connector/Ledger fixture creates active agreement `12345678901234567890123456` with saved payment instrument `pi_coverage_001`; this fixture is a prerequisite only and does not count as B2 or B4 coverage.
- Connector create charge accepts agreement mode and returns `payment_id=pay_recurring_00000000000001`, `agreement_id=12345678901234567890123456`, amount `2500`, state `success`.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B10 step 1 / `take recurring payment` | service account `12345`, Bearer coverage token | `POST /v1/payments` | `Authorization: Bearer coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo`; `Accept: application/json`; `Content-Type: application/json` | none | `{"amount":2500,"reference":"coverage-recurring-001","description":"Coverage recurring payment","authorisation_mode":"agreement","agreement_id":"12345678901234567890123456"}` | pre-existing active agreement fixture -> `agreement_id` | `201`; body `payment_id=pay_recurring_00000000000001`, `agreement_id=12345678901234567890123456`, no hosted next URL | new recurring payment exists linked to active agreement |
| 2 | optional verification / `get payment` | same account | `GET /v1/payments/pay_recurring_00000000000001` | same `Authorization`; `Accept: application/json` | `paymentId=pay_recurring_00000000000001` | none | `payment_id` from call 1 | `200`; body agreement id and reference match | no state change |
| 3 | optional verification / `search payments` | same account | `GET /v1/payments?agreement_id=12345678901234567890123456&page=1&display_size=1` | same `Authorization`; `Accept: application/json` | `agreement_id=12345678901234567890123456`; `page=1`; `display_size=1` | none | agreement id from fixture | `200`; one result with recurring payment id | no state change |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `authorisation_mode` | JSON body | `agreement` | enum string | yes | `agreement` | required when `agreement_id` is present | selects recurring-payment branch |
| 1 | `agreement_id` | JSON body | `12345678901234567890123456` | agreement id | yes | active same-account agreement | must not include `return_url`, `email`, or prefilled cardholder details | proves recurring mode |
| 1 | agreement state | Connector fixture | `active` with saved instrument | lifecycle state | yes | active | inactive/created should fail | required business pre-state |

#### Assertions

Assert `201`, no hosted `next_url`, payment links/read/search include agreement id, Connector request omits `return_url`, `email`, and prefilled cardholder details, one payment created, no agreement state mutation, and source coverage for `CreateCardPaymentRequest.toConnectorPayload` agreement-mode branch and `PaymentsResource.createNewPayment`.

#### Isolation and variants

Reset stubs and deterministic IDs; add failure variants for missing `agreement_id`, `agreement_id` without `authorisation_mode=agreement`, inactive agreement, unknown agreement, and prohibited hosted fields.

### Test `T6`: `idempotency replay and conflict`

- Priority: `P0`
- Target behavior ID and name: B8 Replay idempotent payment creation
- Target checklist item: happy path plus concrete failure `ErrorIdentifier.IDEMPOTENCY_KEY_USED -> RequestError.Code.CREATE_PAYMENT_IDEMPOTENCY_KEY_ALREADY_USED`
- Test category: idempotency, regression
- Why needed: no generated test sends an `Idempotency-Key` header or proves duplicate prevention.
- Coverage delta if passing: context-valid required steps for B8; happy-path coverage for B8; documented failure coverage for B8 idempotency conflict; unique source branch coverage for the idempotency mapper.

#### Initial state and fixture plan

State:
- reset SUT once before the test.
- Public Auth maps coverage token to account `12345`.
- Connector idempotency fixture binds key `coverage-key-2026-07-07-1` and the first body to `payment_id=pay_idem_000000000000000001`.
- Connector returns `201` for the first request, `200` with the same payment for equivalent replay, and `409` with error identifier `IDEMPOTENCY_KEY_USED` for the changed body.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B8 step 1 / `create web card payment` | service account `12345`, Bearer coverage token | `POST /v1/payments` | `Authorization: Bearer coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo`; `Idempotency-Key: coverage-key-2026-07-07-1`; `Accept: application/json`; `Content-Type: application/json` | none | `{"amount":1300,"reference":"coverage-idem-001","description":"Coverage idempotent payment","return_url":"https://service.example.test/return/idem-001"}` | caller-generated idempotency key | `201`; body `payment_id=pay_idem_000000000000000001`; `Location` points to same id | exactly one payment associated with key |
| 2 | B8 step 2 / `replay idempotent payment creation` | same account | `POST /v1/payments` | same headers and same `Idempotency-Key` | none | `{"amount":1300,"reference":"coverage-idem-001","description":"Coverage idempotent payment","return_url":"https://service.example.test/return/idem-001"}` | same key and equivalent body from call 1 | `200`; body `payment_id=pay_idem_000000000000000001`; no new `Location` creation | no second payment created |
| 3 | B8 failure / `replay idempotent payment creation` | same account | `POST /v1/payments` | same headers and same `Idempotency-Key` | none | `{"amount":1400,"reference":"coverage-idem-001-changed","description":"Coverage idempotent payment","return_url":"https://service.example.test/return/idem-001"}` | same key, intentionally changed amount/reference | `409`; body `code=P0191`, description contains ``Idempotency-Key`` already used | still only one original payment; no mutation from failed replay |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1-3 | `Idempotency-Key` | HTTP header | `coverage-key-2026-07-07-1` | alphanumeric/hyphen string | yes for B8 | 1..255 chars, letters/digits/hyphens | scoped to account and equivalent request body | target idempotency behavior |
| 3 | changed body | JSON body | amount `1400`, reference `coverage-idem-001-changed` | JSON | yes for failure branch | otherwise valid payment body | violates only equivalence for same key | triggers exact conflict branch |

#### Assertions

Assert status sequence `201`, `200`, `409`; same payment id for first two calls; Connector receives idempotency key each time; third response `code=P0191`; no second payment exists; no state mutation after conflict; source branch `CreateChargeExceptionMapper` maps `IDEMPOTENCY_KEY_USED` to `CREATE_PAYMENT_IDEMPOTENCY_KEY_ALREADY_USED`.

#### Isolation and variants

Reset idempotency fixture before the method; add separate validation variants for blank key, key longer than 255, and invalid key characters.

### Test `T7`: `agreement search rejects unsupported status`

- Priority: `P1`
- Target behavior ID and name: B1 Search recurring agreements
- Target checklist item: concrete failure; exact function `search agreements`; source discriminator `AgreementSearchValidator.validateStatus -> RequestError.Code.SEARCH_AGREEMENTS_VALIDATION_ERROR`; condition `status` is nonblank and unsupported
- Test category: business failure, boundary
- Why needed: current generated suite has no `/v1/agreements` call and no validator branch coverage.
- Coverage delta if passing: documented failure coverage for B1 status validation; unique source branch coverage for agreement status validation.

#### Initial state and fixture plan

State:
- reset SUT once before test; Public Auth maps coverage token to account `12345`.
- Ledger is not expected to be called; configure WireMock to fail the test if `/v1/agreement` or `/v1/agreement/search` is requested.
- All unrelated parameters are valid: `page=1`, `display_size=25`, `reference=coverage-agreement-001`.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B1 failure / `search agreements` | service account `12345`, Bearer coverage token | `GET /v1/agreements?reference=coverage-agreement-001&status=paused&page=1&display_size=25` | `Authorization: Bearer coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo`; `Accept: application/json` | `reference=coverage-agreement-001`; `status=paused`; `page=1`; `display_size=25` | none | target invalid value is `status=paused` | `422`; body `code=P2401`, description lists `status` | no Ledger call; no state change |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `status` | query | `paused` | string | optional | allowed: `created`, `active`, `cancelled`, `inactive` | no other invalid parameter present | exact failing condition |
| 1 | `page` | query | `1` | integer | optional | `>=1` | valid unrelated parameter | isolates status branch |
| 1 | `display_size` | query | `25` | integer | optional | `1..500` | valid unrelated parameter | isolates status branch |

#### Assertions

Assert `422`, `code=P2401`, description includes `status`, no Ledger call, source coverage reaches `AgreementSearchValidator.validateStatus`, and no agreement/payment/refund side effects.

#### Isolation and variants

Reset stubs; add separate tests for overlong `reference`, invalid `page`, invalid `display_size`, and Ledger page-not-found.

### Test `T8`: `payment search rejects unsupported state`

- Priority: `P1`
- Target behavior ID and name: B6 Search payments
- Target checklist item: concrete failure; exact function `search payments`; source discriminator `PaymentSearchValidator.validateState -> RequestError.Code.SEARCH_PAYMENTS_VALIDATION_ERROR`; condition unsupported `state`
- Test category: business failure, boundary
- Why needed: current payment search calls fail before validator/application entry.
- Coverage delta if passing: documented failure coverage for B6 state validation; unique source branch coverage for payment state validation.

#### Initial state and fixture plan

State:
- reset SUT once; Public Auth maps coverage token to account `12345`.
- Ledger must not be called because validation fails before search.
- Valid unrelated parameters: `from_date=2026-07-07T09:00:00Z`, `to_date=2026-07-08T09:00:00Z`, `page=1`, `display_size=25`.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B6 failure / `search payments` | service account `12345`, Bearer coverage token | `GET /v1/payments?state=refunded&from_date=2026-07-07T09:00:00Z&to_date=2026-07-08T09:00:00Z&page=1&display_size=25` | `Authorization: Bearer coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo`; `Accept: application/json` | `state=refunded`; `from_date=2026-07-07T09:00:00Z`; `to_date=2026-07-08T09:00:00Z`; `page=1`; `display_size=25` | none | target invalid value is `state=refunded` | `422`; body `code=P0401`, description lists `state` | no Ledger call; no state change |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `state` | query | `refunded` | string | optional | `created`, `started`, `submitted`, `success`, `failed`, `cancelled`, `error` | no other invalid parameter present | exact failing condition |
| 1 | date range | query | `2026-07-07T09:00:00Z` to `2026-07-08T09:00:00Z` | UTC ISO date-time | optional | valid date-time strings | ordered lower to upper | isolates state branch |

#### Assertions

Assert `422`, `code=P0401`, description includes `state`, no Ledger search call, source coverage reaches `PaymentSearchValidator.validateState`, and no persisted mutations.

#### Isolation and variants

Reset stubs; add separate tests for invalid card digits, invalid settled dates, invalid pagination, and Ledger page-not-found.

### Test `T9`: `refund search rejects invalid settled date`

- Priority: `P1`
- Target behavior ID and name: B18 Search refunds
- Target checklist item: concrete failure; exact function `search refunds`; source discriminator `SearchValidator.validateFromSettledDate -> RequestError.Code.SEARCH_REFUNDS_VALIDATION_ERROR`; condition invalid ISO date-only value
- Test category: business failure, boundary
- Why needed: current `/v1/refunds` calls fail before application entry.
- Coverage delta if passing: documented failure coverage for B18 settled-date validation; unique source branch coverage for refund search date validation.

#### Initial state and fixture plan

State:
- reset SUT once; Public Auth maps coverage token to account `12345`.
- Ledger must not be called.
- Valid unrelated parameters: `to_settled_date=2026-07-31`, `page=1`, `display_size=25`.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B18 failure / `search refunds` | service account `12345`, Bearer coverage token | `GET /v1/refunds?from_settled_date=2026-02-30&to_settled_date=2026-07-31&page=1&display_size=25` | `Authorization: Bearer coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo`; `Accept: application/json` | `from_settled_date=2026-02-30`; `to_settled_date=2026-07-31`; `page=1`; `display_size=25` | none | target invalid value is impossible calendar date `2026-02-30` | `422`; body `code=P1101`, description lists `from_settled_date` | no Ledger call; no state change |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `from_settled_date` | query | `2026-02-30` | ISO date-only candidate | optional | valid `YYYY-MM-DD` calendar date | no other invalid parameter present | exact failing condition |
| 1 | `display_size` | query | `25` | integer | optional | `1..500` | valid unrelated parameter | isolates settled-date branch |

#### Assertions

Assert `422`, `code=P1101`, description includes `from_settled_date`, no Ledger call, source coverage reaches `RefundSearchValidator.validateSearchParameters` and `SearchValidator.validateFromSettledDate`, and no side effects.

#### Isolation and variants

Reset stubs; add separate tests for invalid `from_date`, invalid `to_date`, invalid pagination, and Ledger page-not-found.

### Test `T10`: `dispute search rejects unsupported status`

- Priority: `P1`
- Target behavior ID and name: B19 Search disputes
- Target checklist item: concrete failure; exact function `search disputes`; source discriminator `DisputeSearchValidator.validateState -> RequestError.Code.SEARCH_DISPUTES_VALIDATION_ERROR`; condition unsupported public dispute `status`
- Test category: business failure, boundary
- Why needed: no generated test calls `/v1/disputes`.
- Coverage delta if passing: documented failure coverage for B19 status validation; unique source branch coverage for dispute status validation.

#### Initial state and fixture plan

State:
- reset SUT once; Public Auth maps coverage token to account `12345`.
- Ledger must not be called because validation fails before search.
- Valid unrelated parameters: `from_date=2026-07-07T09:00:00Z`, `to_date=2026-07-08T09:00:00Z`, `page=1`, `display_size=25`.

#### Complete API call sequence

| Order | Behavior Step/Exact Function | Actor And Auth Context | Method And Resolved Path | Headers/Cookies | Path/Query/Form Parameters | Complete Request Body | Value Source/Binding | Expected Response | Expected State After Call |
|---:|---|---|---|---|---|---|---|---|---|
| 1 | B19 failure / `search disputes` | service account `12345`, Bearer coverage token | `GET /v1/disputes?status=appealed&from_date=2026-07-07T09:00:00Z&to_date=2026-07-08T09:00:00Z&page=1&display_size=25` | `Authorization: Bearer coverage-tokenihkdslgtoea0eqvah7572jldsaa8ungo`; `Accept: application/json` | `status=appealed`; `from_date=2026-07-07T09:00:00Z`; `to_date=2026-07-08T09:00:00Z`; `page=1`; `display_size=25` | none | target invalid public dispute status | `422`; body `code=P0401`, description lists `state` | no Ledger call; no state change |

#### Parameter and state constraints

| Call Order | Parameter/State | Location | Concrete Value | Type/Format | Required? | Allowed Values/Range | Cross-Field Or Lifecycle Constraint | Why This Value |
|---:|---|---|---|---|---|---|---|---|
| 1 | `status` | query | `appealed` | string | optional | `needs_response`, `under_review`, `lost`, `won` | validator records invalid value as `state` internally | exact failing condition |
| 1 | date range | query | `2026-07-07T09:00:00Z` to `2026-07-08T09:00:00Z` | UTC ISO date-time | optional | valid date-time strings | ordered lower to upper | isolates status branch |

#### Assertions

Assert `422`, `code=P0401`, description includes `state`, no Ledger call, source coverage reaches `DisputeSearchValidator.validateState`, pagination links are absent, and no side effects.

#### Isolation and variants

Reset stubs; add separate tests for invalid settled dates, invalid pagination, valid status rewrite to Ledger `state`, and Ledger page-not-found.

## Notes And Assumptions

- No API or generated test execution was performed for this audit; evidence is extracted from generated test source, source code, and JaCoCo artifacts only.
- The authoritative behavior specification in this checkout has 19 behaviors and 95 failure entries, not the prompt-expected 81 behaviors and 461 failure entries.
- `src/test` contains hand-written/unit/integration tests, but the request scopes generated-suite analysis to `/tests`; they were used only to understand local fixture patterns for recommendations.
- The two `coverage/evomaster_*` XML files include overlapping execution and many non-project runtime classes. They were inspected for endpoint-method evidence but not added to project aggregate counters.
- Route-level generated-test comments are unreliable in this suite; actual REST-assured calls and status assertions were used instead.
- No source/document discrepancy changed scoring. Unsupported or missing business behaviors listed after the 19 documented behaviors are not counted as behavior inventory items.
