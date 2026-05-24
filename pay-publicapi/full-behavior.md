Scope note: I analyzed the root `pay-publicapi.json` and implementation under `src`. Behavior-specific failures are listed below. The documented authenticated endpoints also share common 401/429/500 branches for invalid API key, rate limiting, or downstream failure.

### Behavior 1: search agreements

Behavior name:
search agreements

Successful execution:
- Result:
  Returns recurring payment agreements matching optional search filters.
- Endpoint sequence:
  Step 1: `GET /v1/agreements`
- Constraints:
  Optional query values are `reference`, `status`, `page`, and `display_size`. `status` must be `created`, `active`, `cancelled`, or `inactive`; `page` must be at least 1; `display_size` must be 1 to 500.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Search query parameters are invalid.
  - Endpoint group:
    Step 1: `GET /v1/agreements`
  - Failure endpoint:
    `GET /v1/agreements`
  - Why this fails:
    Implementation validates status, reference length, page, and display size before calling ledger.
  - Intentionally violated constraints:
    Use an unsupported `status` or invalid `display_size`.

Endpoint coverage:
- Covers:
  `GET /v1/agreements`
- Distinct meaning:
  Collection search; no specific agreement must exist.

### Behavior 2: create agreement

Behavior name:
create agreement

Successful execution:
- Result:
  Creates a recurring payments agreement and returns the agreement details.
- Endpoint sequence:
  Step 1: `POST /v1/agreements`
- Constraints:
  Body must include valid `reference` and `description`; `user_identifier` is optional. Source requires `reference` and `description`, although the OpenAPI schema does not mark them as required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Required agreement fields are missing or invalid.
  - Endpoint group:
    Step 1: `POST /v1/agreements`
  - Failure endpoint:
    `POST /v1/agreements`
  - Why this fails:
    The request parser requires nonblank `reference` and `description`.
  - Intentionally violated constraints:
    Omit `reference` or `description`.
- Branch 2:
  - Unsatisfied condition:
    The account cannot use recurring card payments.
  - Endpoint group:
    Step 1: `POST /v1/agreements`
  - Failure endpoint:
    `POST /v1/agreements`
  - Why this fails:
    Connector error `RECURRING_CARD_PAYMENTS_NOT_ALLOWED` is mapped to 422.
  - Intentionally violated constraints:
    Use an account without recurring card payment support.

Endpoint coverage:
- Covers:
  `POST /v1/agreements`
- Distinct meaning:
  Creates a reusable recurring payment agreement.

### Behavior 3: get agreement

Behavior name:
get agreement

Successful execution:
- Result:
  Retrieves one recurring payments agreement.
- Endpoint sequence:
  Step 1: `POST /v1/agreements`
  Step 2: `GET /v1/agreements/{agreementId}`
- Constraints:
  Step 1 returns `agreement_id`; Step 2 must use that value as `{agreementId}`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The agreement does not exist.
  - Endpoint group:
    Step 1: `GET /v1/agreements/{agreementId}`
  - Failure endpoint:
    `GET /v1/agreements/{agreementId}`
  - Why this fails:
    Ledger returns not found and the mapper returns the get-agreement not-found error.
  - Intentionally violated constraints:
    Omit `POST /v1/agreements` and use an unknown `{agreementId}`.

Endpoint coverage:
- Covers:
  `GET /v1/agreements/{agreementId}`
- Distinct meaning:
  Resource-scoped read of one agreement.

### Behavior 4: set up an agreement with a payment

Behavior name:
set up agreement payment

Successful execution:
- Result:
  Creates a payment that, after successful payment completion, saves a payment instrument to an existing agreement.
- Endpoint sequence:
  Step 1: `POST /v1/agreements`
  Step 2: `POST /v1/payments`
- Constraints:
  Step 1 returns `agreement_id`; Step 2 body must set `set_up_agreement` to that value. Source maps this to connector `agreement_id` plus `save_payment_instrument_to_agreement=true`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The setup agreement does not exist.
  - Endpoint group:
    Step 1: `POST /v1/payments`
  - Failure endpoint:
    `POST /v1/payments`
  - Why this fails:
    Connector agreement-not-found is mapped to a create-payment agreement error.
  - Intentionally violated constraints:
    Omit `POST /v1/agreements` and send an unknown `set_up_agreement`.

Endpoint coverage:
- Covers:
  `POST /v1/payments`
- Distinct meaning:
  Same endpoint as payment creation, but `set_up_agreement` makes it an agreement-setup workflow.

### Behavior 5: cancel agreement

Behavior name:
cancel agreement

Successful execution:
- Result:
  Cancels an active recurring payments agreement.
- Endpoint sequence:
  Step 1: `POST /v1/agreements`
  Step 2: `POST /v1/payments`
  Step 3: `POST /v1/agreements/{agreementId}/cancel`
- Constraints:
  Step 1 returns `agreement_id`; Step 2 must use it as `set_up_agreement` and the setup payment must complete so the agreement becomes `active`; Step 3 uses the same `agreement_id`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Agreement is missing.
  - Endpoint group:
    Step 1: `POST /v1/agreements/{agreementId}/cancel`
  - Failure endpoint:
    `POST /v1/agreements/{agreementId}/cancel`
  - Why this fails:
    Connector 404 maps to cancel-agreement not found.
  - Intentionally violated constraints:
    Omit agreement creation and use an unknown `{agreementId}`.
- Branch 2:
  - Unsatisfied condition:
    Agreement exists but is not active.
  - Endpoint group:
    Step 1: `POST /v1/agreements`
    Step 2: `POST /v1/agreements/{agreementId}/cancel`
  - Failure endpoint:
    `POST /v1/agreements/{agreementId}/cancel`
  - Why this fails:
    The endpoint is documented for active agreements; connector rejects invalid agreement state.
  - Intentionally violated constraints:
    Do not complete the setup payment that activates the agreement.

Endpoint coverage:
- Covers:
  `POST /v1/agreements/{agreementId}/cancel`
- Distinct meaning:
  State transition from active agreement to cancelled agreement.

### Behavior 6: search payments

Behavior name:
search payments

Successful execution:
- Result:
  Returns payments matching optional filters.
- Endpoint sequence:
  Step 1: `GET /v1/payments`
- Constraints:
  Optional filters include `reference`, `email`, `state`, `card_brand`, date ranges, pagination, cardholder/card-number fragments, settled dates, and `agreement_id`. `state` must be one of `created`, `started`, `submitted`, `success`, `failed`, `cancelled`, or `error`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Search parameters are invalid.
  - Endpoint group:
    Step 1: `GET /v1/payments`
  - Failure endpoint:
    `GET /v1/payments`
  - Why this fails:
    Implementation validates dates, page, display size, state, card number lengths, and max field lengths.
  - Intentionally violated constraints:
    Use invalid `from_date`, unsupported `state`, nonnumeric `page`, or malformed card digits.

Endpoint coverage:
- Covers:
  `GET /v1/payments`
- Distinct meaning:
  Collection search; no specific payment must exist.

### Behavior 7: create web card payment

Behavior name:
create web card payment

Successful execution:
- Result:
  Creates a card payment for the standard GOV.UK Pay web journey and returns payment links.
- Endpoint sequence:
  Step 1: `POST /v1/payments`
- Constraints:
  Body must include `amount`, `reference`, `description`, and for normal web payments a `return_url`. Optional values include `email`, `language`, `metadata`, `prefilled_cardholder_details`, `moto`, and `delayed_capture`. Source allows `return_url` to be omitted for non-web modes even though OpenAPI marks it required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Required fields are missing or malformed.
  - Endpoint group:
    Step 1: `POST /v1/payments`
  - Failure endpoint:
    `POST /v1/payments`
  - Why this fails:
    The parser and bean validation reject missing `amount`, `reference`, or `description`, invalid URL/string/boolean formats, or invalid metadata.
  - Intentionally violated constraints:
    Omit `amount` or send nonstring `reference`.
- Branch 2:
  - Unsatisfied condition:
    Connector rejects payment creation.
  - Endpoint group:
    Step 1: `POST /v1/payments`
  - Failure endpoint:
    `POST /v1/payments`
  - Why this fails:
    Implementation maps connector errors such as disabled account, zero amount, MOTO not enabled, authorisation API not enabled, or PSP account problems.
  - Intentionally violated constraints:
    Use an account or feature state not allowed for the chosen request fields.

Endpoint coverage:
- Covers:
  `POST /v1/payments`
- Distinct meaning:
  Creates a normal web card payment.

### Behavior 8: replay idempotent payment creation

Behavior name:
replay idempotent payment creation

Successful execution:
- Result:
  Returns an existing payment instead of creating a new one when the same idempotency key and equivalent request are repeated.
- Endpoint sequence:
  Step 1: `POST /v1/payments`
  Step 2: `POST /v1/payments`
- Constraints:
  Both calls must use the same `Idempotency-Key` header and equivalent payment body. Step 1 creates the payment; Step 2 returns the existing payment with 200.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The same idempotency key is reused for a different request.
  - Endpoint group:
    Step 1: `POST /v1/payments`
    Step 2: `POST /v1/payments`
  - Failure endpoint:
    `POST /v1/payments`
  - Why this fails:
    Connector error `IDEMPOTENCY_KEY_USED` is mapped to 409.
  - Intentionally violated constraints:
    Reuse Step 1 `Idempotency-Key` but change body fields in Step 2.

Endpoint coverage:
- Covers:
  `POST /v1/payments`
- Distinct meaning:
  Existing-payment replay path, separate from brand-new creation.

### Behavior 9: create MOTO API payment

Behavior name:
create MOTO API payment

Successful execution:
- Result:
  Creates a payment that must be authorised by submitting card details to the API.
- Endpoint sequence:
  Step 1: `POST /v1/payments`
- Constraints:
  Body must set `authorisation_mode` to `moto_api` and must not send `return_url`. The response must include `payment_id` and an `auth_url_post` link with a one-time token for `POST /v1/auth`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    MOTO API authorisation is not enabled for the account.
  - Endpoint group:
    Step 1: `POST /v1/payments`
  - Failure endpoint:
    `POST /v1/payments`
  - Why this fails:
    Connector `AUTHORISATION_API_NOT_ALLOWED` maps to 422.
  - Intentionally violated constraints:
    Use `authorisation_mode=moto_api` on an account without that capability.

Endpoint coverage:
- Covers:
  `POST /v1/payments`
- Distinct meaning:
  Creates an API-authorised MOTO payment rather than a web-journey payment.

### Behavior 10: authorise MOTO API payment

Behavior name:
authorise MOTO API payment

Successful execution:
- Result:
  Sends card details for a previously created MOTO API payment and completes authorisation.
- Endpoint sequence:
  Step 1: `POST /v1/payments`
  Step 2: `POST /v1/auth`
- Constraints:
  Step 1 must use `authorisation_mode=moto_api`; Step 2 body must include `one_time_token` from Step 1 `auth_url_post.params`, plus `card_number`, `cardholder_name`, `cvc`, and `expiry_date`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The token is invalid or already used.
  - Endpoint group:
    Step 1: `POST /v1/payments`
    Step 2: `POST /v1/auth`
    Step 3: `POST /v1/auth`
  - Failure endpoint:
    `POST /v1/auth`
  - Why this fails:
    The one-time token is single use; connector errors map to 400.
  - Intentionally violated constraints:
    Reuse the same `one_time_token` from Step 1 in Step 3.
- Branch 2:
  - Unsatisfied condition:
    Card details are rejected.
  - Endpoint group:
    Step 1: `POST /v1/payments`
    Step 2: `POST /v1/auth`
  - Failure endpoint:
    `POST /v1/auth`
  - Why this fails:
    Connector rejection maps to 402 or validation maps to 422.
  - Intentionally violated constraints:
    Send invalid card number, expiry date, CVC, or unsupported card type.

Endpoint coverage:
- Covers:
  `POST /v1/auth`
- Distinct meaning:
  Completes authorisation for a MOTO API payment.

### Behavior 11: take recurring payment with agreement

Behavior name:
take recurring payment

Successful execution:
- Result:
  Creates a payment charged against an active recurring payments agreement.
- Endpoint sequence:
  Step 1: `POST /v1/agreements`
  Step 2: `POST /v1/payments`
  Step 3: `POST /v1/payments`
- Constraints:
  Step 1 returns `agreement_id`; Step 2 must use it as `set_up_agreement` and the setup payment must complete so the agreement becomes active. Step 3 must use `authorisation_mode=agreement` and `agreement_id` equal to Step 1 `agreement_id`; it must not send `return_url`, `email`, or `prefilled_cardholder_details`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `agreement_id` is absent, unknown, or not active.
  - Endpoint group:
    Step 1: `POST /v1/payments`
  - Failure endpoint:
    `POST /v1/payments`
  - Why this fails:
    Source only accepts `agreement_id` with `authorisation_mode=agreement`; connector rejects unknown or inactive agreements.
  - Intentionally violated constraints:
    Omit the agreement setup sequence or use a non-active agreement.

Endpoint coverage:
- Covers:
  `POST /v1/payments`
- Distinct meaning:
  Same endpoint as payment creation, but `authorisation_mode=agreement` charges a stored agreement.

### Behavior 12: get payment

Behavior name:
get payment

Successful execution:
- Result:
  Retrieves details and links for a single payment.
- Endpoint sequence:
  Step 1: `POST /v1/payments`
  Step 2: `GET /v1/payments/{paymentId}`
- Constraints:
  Step 1 returns `payment_id`; Step 2 must use it as `{paymentId}`. Implementation tries connector first, then ledger unless hidden `X-Ledger` selects a strategy.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Payment does not exist.
  - Endpoint group:
    Step 1: `GET /v1/payments/{paymentId}`
  - Failure endpoint:
    `GET /v1/payments/{paymentId}`
  - Why this fails:
    Connector/ledger not-found is mapped to get-payment not found.
  - Intentionally violated constraints:
    Omit `POST /v1/payments` and use an unknown `{paymentId}`.

Endpoint coverage:
- Covers:
  `GET /v1/payments/{paymentId}`
- Distinct meaning:
  Resource-scoped read of one payment.

### Behavior 13: cancel payment

Behavior name:
cancel payment

Successful execution:
- Result:
  Cancels an unfinished non-agreement payment.
- Endpoint sequence:
  Step 1: `POST /v1/payments`
  Step 2: `POST /v1/payments/{paymentId}/cancel`
- Constraints:
  Step 1 returns `payment_id`; Step 2 must use it as `{paymentId}`. Payment must not be finished, and agreement-mode payments do not receive a cancel link in the response model.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Payment does not exist.
  - Endpoint group:
    Step 1: `POST /v1/payments/{paymentId}/cancel`
  - Failure endpoint:
    `POST /v1/payments/{paymentId}/cancel`
  - Why this fails:
    Connector 404 maps to cancel-payment not found.
  - Intentionally violated constraints:
    Omit `POST /v1/payments` and use an unknown `{paymentId}`.
- Branch 2:
  - Unsatisfied condition:
    Payment is already cancelled or otherwise not cancellable.
  - Endpoint group:
    Step 1: `POST /v1/payments`
    Step 2: `POST /v1/payments/{paymentId}/cancel`
    Step 3: `POST /v1/payments/{paymentId}/cancel`
  - Failure endpoint:
    `POST /v1/payments/{paymentId}/cancel`
  - Why this fails:
    Connector conflict/bad request is mapped to 409 or 400.
  - Intentionally violated constraints:
    Reuse the same `{paymentId}` after it has already been cancelled.

Endpoint coverage:
- Covers:
  `POST /v1/payments/{paymentId}/cancel`
- Distinct meaning:
  State transition from unfinished payment to cancelled payment.

### Behavior 14: capture delayed payment

Behavior name:
capture delayed payment

Successful execution:
- Result:
  Captures funds for a delayed-capture payment.
- Endpoint sequence:
  Step 1: `POST /v1/payments`
  Step 2: `POST /v1/auth`
  Step 3: `POST /v1/payments/{paymentId}/capture`
- Constraints:
  One supported documented setup is Step 1 with `authorisation_mode=moto_api` and `delayed_capture=true`; Step 2 must use the one-time token from Step 1 and successfully authorise the payment; Step 3 uses Step 1 `payment_id`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Payment does not exist.
  - Endpoint group:
    Step 1: `POST /v1/payments/{paymentId}/capture`
  - Failure endpoint:
    `POST /v1/payments/{paymentId}/capture`
  - Why this fails:
    Connector 404 maps to capture-payment not found.
  - Intentionally violated constraints:
    Omit payment creation and use an unknown `{paymentId}`.
- Branch 2:
  - Unsatisfied condition:
    Payment is not in delayed-capture awaiting-capture state.
  - Endpoint group:
    Step 1: `POST /v1/payments`
    Step 2: `POST /v1/payments/{paymentId}/capture`
  - Failure endpoint:
    `POST /v1/payments/{paymentId}/capture`
  - Why this fails:
    Connector returns bad request or conflict, mapped by `CaptureChargeExceptionMapper`.
  - Intentionally violated constraints:
    Create a normal payment without `delayed_capture=true` or capture before successful authorisation.

Endpoint coverage:
- Covers:
  `POST /v1/payments/{paymentId}/capture`
- Distinct meaning:
  Captures a delayed payment after authorisation.

### Behavior 15: get payment events

Behavior name:
get payment events

Successful execution:
- Result:
  Retrieves the state-change event history for a payment.
- Endpoint sequence:
  Step 1: `POST /v1/payments`
  Step 2: `GET /v1/payments/{paymentId}/events`
- Constraints:
  Step 1 returns `payment_id`; Step 2 must use it as `{paymentId}`. Implementation can read from connector or ledger.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Payment does not exist.
  - Endpoint group:
    Step 1: `GET /v1/payments/{paymentId}/events`
  - Failure endpoint:
    `GET /v1/payments/{paymentId}/events`
  - Why this fails:
    Connector/ledger not-found is mapped to get-events not found.
  - Intentionally violated constraints:
    Omit `POST /v1/payments` and use an unknown `{paymentId}`.

Endpoint coverage:
- Covers:
  `GET /v1/payments/{paymentId}/events`
- Distinct meaning:
  Reads a payment’s event history.

### Behavior 16: list refunds for payment

Behavior name:
list payment refunds

Successful execution:
- Result:
  Returns the refund list for a payment, possibly empty.
- Endpoint sequence:
  Step 1: `POST /v1/payments`
  Step 2: `GET /v1/payments/{paymentId}/refunds`
- Constraints:
  Step 1 returns `payment_id`; Step 2 must use it as `{paymentId}`. Default implementation uses ledger for this list.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Payment does not exist.
  - Endpoint group:
    Step 1: `GET /v1/payments/{paymentId}/refunds`
  - Failure endpoint:
    `GET /v1/payments/{paymentId}/refunds`
  - Why this fails:
    Ledger/connector not-found is mapped to get-refunds not found.
  - Intentionally violated constraints:
    Omit `POST /v1/payments` and use an unknown `{paymentId}`.

Endpoint coverage:
- Covers:
  `GET /v1/payments/{paymentId}/refunds`
- Distinct meaning:
  Resource-scoped list of refunds for one payment.

### Behavior 17: refund payment

Behavior name:
refund payment

Successful execution:
- Result:
  Creates a full or partial refund for a successful payment.
- Endpoint sequence:
  Step 1: `POST /v1/payments`
  Step 2: `POST /v1/auth`
  Step 3: `POST /v1/payments/{paymentId}/refunds`
- Constraints:
  One supported documented setup is a successful MOTO API payment: Step 1 returns `payment_id` and one-time token; Step 2 authorises it; Step 3 uses Step 1 `payment_id`. Refund body must include `amount`; optional `refund_amount_available`, if provided, must match connector’s available amount.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Payment does not exist.
  - Endpoint group:
    Step 1: `POST /v1/payments/{paymentId}/refunds`
  - Failure endpoint:
    `POST /v1/payments/{paymentId}/refunds`
  - Why this fails:
    Connector 404 maps to refund not found.
  - Intentionally violated constraints:
    Omit payment creation and use an unknown `{paymentId}`.
- Branch 2:
  - Unsatisfied condition:
    Refund amount is invalid or unavailable.
  - Endpoint group:
    Step 1: `POST /v1/payments`
    Step 2: `POST /v1/auth`
    Step 3: `POST /v1/payments/{paymentId}/refunds`
  - Failure endpoint:
    `POST /v1/payments/{paymentId}/refunds`
  - Why this fails:
    Local validation rejects amount outside 1 to 10000000; connector rejects unavailable refunds, dispute-blocked refunds, or amount-available mismatch.
  - Intentionally violated constraints:
    Send invalid `amount`, over-refund, or wrong `refund_amount_available`.

Endpoint coverage:
- Covers:
  `POST /v1/payments/{paymentId}/refunds`
- Distinct meaning:
  Creates a refund transaction for a payment.

### Behavior 18: get refund

Behavior name:
get refund

Successful execution:
- Result:
  Retrieves one refund for a payment.
- Endpoint sequence:
  Step 1: `POST /v1/payments`
  Step 2: `POST /v1/auth`
  Step 3: `POST /v1/payments/{paymentId}/refunds`
  Step 4: `GET /v1/payments/{paymentId}/refunds/{refundId}`
- Constraints:
  Step 1 produces `payment_id`; Step 3 creates the refund and returns `refund_id`; Step 4 must use both values in the path.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Refund or parent payment does not exist.
  - Endpoint group:
    Step 1: `POST /v1/payments`
    Step 2: `GET /v1/payments/{paymentId}/refunds/{refundId}`
  - Failure endpoint:
    `GET /v1/payments/{paymentId}/refunds/{refundId}`
  - Why this fails:
    A refund-specific read needs a real refund id; connector/ledger not-found maps to get-refund not found.
  - Intentionally violated constraints:
    Use Step 1 `payment_id` but omit `POST /v1/payments/{paymentId}/refunds`, or use an unknown `{refundId}`.

Endpoint coverage:
- Covers:
  `GET /v1/payments/{paymentId}/refunds/{refundId}`
- Distinct meaning:
  Resource-scoped read of one refund.

### Behavior 19: search refunds

Behavior name:
search refunds

Successful execution:
- Result:
  Returns refunds matching optional date, settled-date, and pagination filters.
- Endpoint sequence:
  Step 1: `GET /v1/refunds`
- Constraints:
  Optional `from_date` and `to_date` must be UTC ISO-8601 datetimes; settled dates must be date-only ISO-8601; `page` must be at least 1; `display_size` must be 1 to 500.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Search parameters are invalid.
  - Endpoint group:
    Step 1: `GET /v1/refunds`
  - Failure endpoint:
    `GET /v1/refunds`
  - Why this fails:
    Refund search validator rejects invalid dates, page, or display size.
  - Intentionally violated constraints:
    Use malformed `from_date` or `display_size=501`.

Endpoint coverage:
- Covers:
  `GET /v1/refunds`
- Distinct meaning:
  Collection search across refunds.

### Behavior 20: search disputes

Behavior name:
search disputes

Successful execution:
- Result:
  Returns payment disputes matching optional filters.
- Endpoint sequence:
  Step 1: `GET /v1/disputes`
- Constraints:
  Optional `status` must be `needs_response`, `under_review`, `lost`, or `won`; date and pagination constraints match the implementation validators. Source rewrites public `status` to ledger `state`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Search parameters are invalid.
  - Endpoint group:
    Step 1: `GET /v1/disputes`
  - Failure endpoint:
    `GET /v1/disputes`
  - Why this fails:
    Dispute search validator rejects invalid dates, status, page, or display size.
  - Intentionally violated constraints:
    Use unsupported `status` or malformed settled date.

Endpoint coverage:
- Covers:
  `GET /v1/disputes`
- Distinct meaning:
  Collection search across disputes.

### Unclear or auxiliary endpoints

The root OpenAPI file does not document these implementation endpoints, so I did not count them as documented behaviors: `GET /v1/transactions`, `POST /v1/payment_notification`, healthcheck/ping endpoints, `GET /security.txt`, `GET /.well-known/security.txt`, and `request-denied` handlers. This is a source/OpenAPI discrepancy; the behavior list above covers every endpoint in `pay-publicapi.json`.