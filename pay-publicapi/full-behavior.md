### Function 1: search agreements

Function name:
search agreements

Core endpoint(s):
- `GET /v1/agreements`

Preconditions:
- A valid authenticated account is available for the search. No agreement resource has to exist; the ledger can return an empty result set from its backing store.

Successful execution:
- Result:
  Returns recurring payment agreements matching the supplied optional filters.
- Invocation:
  Step 1: `GET /v1/agreements` with optional query parameters `reference`, `status`, `page`, and `display_size`.
- Constraints:
  `status` must be `created`, `active`, `cancelled`, or `inactive`; `page` must be at least 1; `display_size` must be between 1 and 500; `reference` must satisfy the implementation length validation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No resource state is required; the failing request supplies invalid agreement search query values such as an unsupported `status`, an over-large `display_size`, or an invalid `page`.
  - Failure endpoint:
    `GET /v1/agreements`
  - Why this fails:
    `AgreementSearchValidator` validates allowed query parameters and their values before the service calls ledger.
  - Intentionally violated constraints:
    The search request violates the allowed query parameter formats or value ranges.

Endpoint coverage:
- Covers:
  `GET /v1/agreements`
- Distinct meaning:
  Searches the agreement collection without requiring a specific agreement id.

### Function 2: create agreement

Function name:
create agreement

Core endpoint(s):
- `POST /v1/agreements`

Preconditions:
- A valid authenticated account exists. The account must be configured in connector to allow recurring card payments; this can be satisfied by direct configuration of the connector backing store or service fixtures.

Successful execution:
- Result:
  Creates a recurring payment agreement and returns the created agreement details after the service reads the new agreement from ledger.
- Invocation:
  Step 1: `POST /v1/agreements` with JSON body fields `reference` and `description`, and optional `user_identifier`.
- Constraints:
  `reference` and `description` must be nonblank strings. `user_identifier`, when supplied, must be a string. The implementation requires `reference` and `description` even though the OpenAPI schema does not mark them as required.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No prior agreement state is required; the request body omits `reference` or `description`, supplies them as non-string values, or sends malformed JSON.
  - Failure endpoint:
    `POST /v1/agreements`
  - Why this fails:
    `RequestJsonParser.parseAgreementRequest` requires nonblank string values for `reference` and `description`; malformed JSON is mapped by the agreement request deserializer.
  - Intentionally violated constraints:
    Required agreement body fields are missing, blank, malformed, or of the wrong JSON type.
- Branch 2:
  - Preconditions:
    - The authenticated account exists but connector has recurring card payments disabled for that account. This can be satisfied by direct connector backing-store configuration or by using a service fixture with recurring card payments disabled.
  - Failure endpoint:
    `POST /v1/agreements`
  - Why this fails:
    Connector returns `RECURRING_CARD_PAYMENTS_NOT_ALLOWED`, which `CreateAgreementExceptionMapper` maps to a 422 recurring-card-payments error.
  - Intentionally violated constraints:
    The account capability required for creating agreements is absent.

Endpoint coverage:
- Covers:
  `POST /v1/agreements`
- Distinct meaning:
  Creates a reusable recurring payment agreement.

### Function 3: get agreement

Function name:
get agreement

Core endpoint(s):
- `GET /v1/agreements/{agreementId}`

Preconditions:
- An agreement exists for the authenticated account. This can be satisfied by directly seeding the ledger/connector backing store with an agreement row for that account or by calling `POST /v1/agreements` with valid `reference` and `description`.
- The `{agreementId}` path value must identify that agreement. If the API creates the agreement, `{agreementId}` must be taken from the `agreement_id` value returned by `POST /v1/agreements`.

Successful execution:
- Result:
  Retrieves the agreement identified by `{agreementId}`.
- Invocation:
  Step 1: `GET /v1/agreements/{agreementId}` with the agreement id in the path.
- Constraints:
  `{agreementId}` must exist in ledger for the authenticated account.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No agreement with `{agreementId}` exists for the authenticated account. This can be produced by starting from empty agreement state, deleting the agreement from the backing store, or intentionally not inserting it directly and not calling `POST /v1/agreements`.
  - Failure endpoint:
    `GET /v1/agreements/{agreementId}`
  - Why this fails:
    `AgreementsService.getAgreement` receives a not-found response from ledger and the mapper returns the get-agreement not-found error.
  - Intentionally violated constraints:
    The required agreement state for `{agreementId}` is omitted or the id is not scoped to the account.

Endpoint coverage:
- Covers:
  `GET /v1/agreements/{agreementId}`
- Distinct meaning:
  Reads one agreement resource by id.

### Function 4: create payment to set up an agreement

Function name:
create setup-agreement payment

Core endpoint(s):
- `POST /v1/payments`

Preconditions:
- An agreement exists for the authenticated account. This can be satisfied by directly seeding the ledger/connector backing store with an agreement for that account or by calling `POST /v1/agreements` with valid `reference` and `description`.
- The `set_up_agreement` body value must be the `agreement_id` of that agreement. If the API creates the agreement, this value must be taken from the `agreement_id` returned by `POST /v1/agreements`.

Successful execution:
- Result:
  Creates a payment that will save a payment instrument to the specified agreement when the payment completes successfully.
- Invocation:
  Step 1: `POST /v1/payments` with required payment fields `amount`, `reference`, and `description`, with a valid `return_url` for the web journey, and with `set_up_agreement={agreementId}`.
- Constraints:
  `amount` must be an integer within the allowed range, `reference` and `description` must be nonblank strings, and `set_up_agreement` must reference an existing agreement. The implementation maps `set_up_agreement` to connector `agreement_id` and `save_payment_instrument_to_agreement=true`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No agreement exists for the `set_up_agreement` value. This can be produced by using an unknown id, deleting the agreement from the backing store, or intentionally not inserting it directly and not calling `POST /v1/agreements`.
  - Failure endpoint:
    `POST /v1/payments`
  - Why this fails:
    Connector reports `AGREEMENT_NOT_FOUND`, and `CreateChargeExceptionMapper` maps that to an invalid `set_up_agreement` create-payment error.
  - Intentionally violated constraints:
    The setup agreement id in the request does not identify an existing agreement for the account.

Endpoint coverage:
- Covers:
  `POST /v1/payments`
- Distinct meaning:
  Uses payment creation for the specific endpoint-level effect of setting up a recurring agreement.

### Function 5: cancel agreement

Function name:
cancel agreement

Core endpoint(s):
- `POST /v1/agreements/{agreementId}/cancel`

Preconditions:
- An agreement exists for the authenticated account. This can be satisfied by directly seeding the connector/ledger backing store or by calling `POST /v1/agreements` with valid `reference` and `description`.
- A setup payment has been created for that agreement and completed successfully so connector considers the agreement `active`. This can be satisfied by direct backing-store state or by calling `POST /v1/payments` with `set_up_agreement={agreementId}` and completing the external payment journey.
- The `{agreementId}` path value must identify the active agreement. If the API creates the agreement, the value must be taken from the `agreement_id` returned by `POST /v1/agreements`.

Successful execution:
- Result:
  Cancels the active recurring payment agreement.
- Invocation:
  Step 1: `POST /v1/agreements/{agreementId}/cancel` with the active agreement id in the path.
- Constraints:
  `{agreementId}` must exist, be scoped to the authenticated account, and be in the `active` status.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No agreement exists for `{agreementId}` for the authenticated account. This can be produced by starting from empty agreement state, deleting the agreement from the backing store, or intentionally not inserting it directly and not calling `POST /v1/agreements`.
  - Failure endpoint:
    `POST /v1/agreements/{agreementId}/cancel`
  - Why this fails:
    Connector returns 404 and `CancelAgreementExceptionMapper` maps it to the cancel-agreement not-found error.
  - Intentionally violated constraints:
    The agreement id required by the cancel endpoint is missing or not account-scoped.
- Branch 2:
  - Preconditions:
    - An agreement exists for `{agreementId}`. This can be satisfied by directly inserting agreement state or by calling `POST /v1/agreements`.
    - No completed setup payment has activated that agreement. This can be produced by omitting the setup payment, leaving the setup payment incomplete, or directly setting the agreement status to a non-`active` value in the backing store.
  - Failure endpoint:
    `POST /v1/agreements/{agreementId}/cancel`
  - Why this fails:
    The endpoint is implemented for active agreements; connector rejects cancellation when the agreement state is not active.
  - Intentionally violated constraints:
    The agreement exists but does not satisfy the required `active` status.

Endpoint coverage:
- Covers:
  `POST /v1/agreements/{agreementId}/cancel`
- Distinct meaning:
  Performs the agreement state transition from active to cancelled.

### Function 6: search payments

Function name:
search payments

Core endpoint(s):
- `GET /v1/payments`

Preconditions:
- A valid authenticated account is available for the search. No payment resource has to exist; ledger can return an empty result set from its backing store.

Successful execution:
- Result:
  Returns payments matching optional search filters.
- Invocation:
  Step 1: `GET /v1/payments` with optional query parameters including `reference`, `email`, `state`, `card_brand`, `from_date`, `to_date`, `page`, `display_size`, `cardholder_name`, `first_digits_card_number`, `last_digits_card_number`, `from_settled_date`, `to_settled_date`, and `agreement_id`.
- Constraints:
  `state` must be `created`, `started`, `submitted`, `success`, `failed`, `cancelled`, or `error`; date-time filters must use the expected UTC ISO-8601 format; settled-date filters must use date-only ISO-8601 format; pagination and card digit filters must pass implementation validation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No resource state is required; the failing request supplies invalid payment search query values such as malformed `from_date`, unsupported `state`, invalid `page`, over-large `display_size`, or malformed card digits.
  - Failure endpoint:
    `GET /v1/payments`
  - Why this fails:
    `PaymentSearchValidator` validates dates, pagination, state, field lengths, and card number fragments before ledger search.
  - Intentionally violated constraints:
    The payment search query values do not satisfy the implementation validators.

Endpoint coverage:
- Covers:
  `GET /v1/payments`
- Distinct meaning:
  Searches the payment collection without requiring a specific payment id.

### Function 7: create web card payment

Function name:
create web card payment

Core endpoint(s):
- `POST /v1/payments`

Preconditions:
- A valid authenticated account exists and is enabled for normal payment creation. This can be satisfied by direct connector backing-store configuration or by using an enabled account fixture.

Successful execution:
- Result:
  Creates a card payment for the standard GOV.UK Pay web payment journey and returns the payment details and links.
- Invocation:
  Step 1: `POST /v1/payments` with JSON body fields `amount`, `reference`, `description`, and normally `return_url`, plus optional fields such as `email`, `language`, `metadata`, `prefilled_cardholder_details`, `moto`, and `delayed_capture`.
- Constraints:
  `amount` must be an integer accepted by local validation and connector policy; `reference` and `description` must be nonblank strings; `return_url` must be a valid URL when supplied. OpenAPI marks `return_url` as required, but source parsing only requires it when connector rejects the selected payment mode.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No prior payment state is required; the request body omits `amount`, `reference`, or `description`, supplies invalid JSON types, sends malformed optional values, or violates metadata constraints.
  - Failure endpoint:
    `POST /v1/payments`
  - Why this fails:
    `RequestJsonParser.parsePaymentRequest`, bean validation, and metadata validation reject missing mandatory fields, wrong types, invalid URLs, invalid booleans, unsupported language codes, and invalid metadata.
  - Intentionally violated constraints:
    Required or optional payment request fields do not satisfy local parser and validator requirements.
- Branch 2:
  - Preconditions:
    - The authenticated account exists but connector state does not allow the chosen payment creation request, such as a disabled account, missing PSP link, MOTO disabled, authorisation API disabled, zero amount not allowed, or card-number content in a payment-link reference. This can be satisfied by direct connector backing-store configuration or by using account fixtures with those capabilities disabled.
  - Failure endpoint:
    `POST /v1/payments`
  - Why this fails:
    Connector returns an error identifier such as `ACCOUNT_DISABLED`, `ACCOUNT_NOT_LINKED_WITH_PSP`, `MOTO_NOT_ALLOWED`, `AUTHORISATION_API_NOT_ALLOWED`, `ZERO_AMOUNT_NOT_ALLOWED`, or `CARD_NUMBER_IN_PAYMENT_LINK_REFERENCE_REJECTED`, and `CreateChargeExceptionMapper` maps it to the public API error.
  - Intentionally violated constraints:
    Account capability or connector policy required for the selected payment creation request is absent.

Endpoint coverage:
- Covers:
  `POST /v1/payments`
- Distinct meaning:
  Creates a new web-journey card payment rather than replaying an existing idempotent request or using agreement/MOTO-specific modes.

### Function 8: replay idempotent payment creation

Function name:
replay idempotent payment creation

Core endpoint(s):
- `POST /v1/payments`

Preconditions:
- A previous payment creation exists for the authenticated account with a specific `Idempotency-Key` header and request body. This can be satisfied by directly seeding connector idempotency state or by calling `POST /v1/payments` once with that `Idempotency-Key` and body values such as `amount`, `reference`, `description`, and `return_url`; the first response produces the existing `payment_id`.
- The replay request must reuse the same `Idempotency-Key` value and an equivalent payment body. If the API establishes the prior state, the idempotency key is the caller-supplied header from the first `POST /v1/payments` request.

Successful execution:
- Result:
  Returns the existing payment instead of creating a new payment.
- Invocation:
  Step 1: `POST /v1/payments` with the same `Idempotency-Key` header and equivalent JSON body as the existing idempotent creation.
- Constraints:
  `Idempotency-Key` must be 1 to 255 characters and contain only alphanumeric characters and hyphens. The request body must match the one associated with that key.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A previous payment creation exists for the authenticated account using a specific `Idempotency-Key`. This can be satisfied by direct connector idempotency state or by calling `POST /v1/payments` once with that header and concrete body values such as `amount`, `reference`, `description`, and `return_url`; the first response produces the existing `payment_id`.
    - The failing request reuses that `Idempotency-Key` but changes one or more body fields such as `amount`, `reference`, `description`, or payment mode.
  - Failure endpoint:
    `POST /v1/payments`
  - Why this fails:
    Connector returns `IDEMPOTENCY_KEY_USED`, which `CreateChargeExceptionMapper` maps to a 409 idempotency-key conflict.
  - Intentionally violated constraints:
    The idempotency key is reused for a non-equivalent request.

Endpoint coverage:
- Covers:
  `POST /v1/payments`
- Distinct meaning:
  Exercises the existing-payment replay result of payment creation rather than the brand-new creation result.

### Function 9: create MOTO API payment

Function name:
create MOTO API payment

Core endpoint(s):
- `POST /v1/payments`

Preconditions:
- A valid authenticated account exists and connector has MOTO API authorisation enabled for it. This can be satisfied by direct connector backing-store configuration or by using an account fixture with authorisation API capability enabled.

Successful execution:
- Result:
  Creates a payment that must be authorised by submitting card details to the public authorisation endpoint.
- Invocation:
  Step 1: `POST /v1/payments` with required payment fields and `authorisation_mode=moto_api`; do not send `return_url` for this API-authorised mode.
- Constraints:
  The response includes `payment_id` and an `auth_url_post` link whose parameters contain a generated `one_time_token` for `POST /v1/auth`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The authenticated account exists but connector has MOTO API authorisation disabled. This can be satisfied by direct connector backing-store configuration or by using an account fixture without authorisation API capability.
  - Failure endpoint:
    `POST /v1/payments`
  - Why this fails:
    Connector returns `AUTHORISATION_API_NOT_ALLOWED`, which `CreateChargeExceptionMapper` maps to a 422 create-payment authorisation API error.
  - Intentionally violated constraints:
    `authorisation_mode=moto_api` is requested for an account that is not allowed to use API authorisation.

Endpoint coverage:
- Covers:
  `POST /v1/payments`
- Distinct meaning:
  Creates an API-authorised MOTO payment and produces the one-time authorisation token.

### Function 10: authorise MOTO API payment

Function name:
authorise MOTO API payment

Core endpoint(s):
- `POST /v1/auth`

Preconditions:
- A MOTO API payment exists with `authorisation_mode=moto_api`. This can be satisfied by directly seeding connector payment and token state or by calling `POST /v1/payments` with body values `amount`, `reference`, `description`, and `authorisation_mode=moto_api`; the response produces `payment_id` and `auth_url_post.params.one_time_token`.
- A valid unused `one_time_token` exists for that payment. If the API creates the payment, the token must be taken from the `auth_url_post.params.one_time_token` value returned by `POST /v1/payments`.

Successful execution:
- Result:
  Sends card details to connector and completes authorisation for the MOTO API payment.
- Invocation:
  Step 1: `POST /v1/auth` with body fields `one_time_token`, `card_number`, `cardholder_name`, `cvc`, and `expiry_date`.
- Constraints:
  `one_time_token` must be valid and unused; `card_number` must be 12 to 19 characters; `cvc` must be 3 to 4 characters; `expiry_date` must be in `MM/YY` format; `cardholder_name` must be nonblank and at most 255 characters.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A MOTO API payment and one-time token exist. This can be satisfied by direct connector token state or by calling `POST /v1/payments` with `authorisation_mode=moto_api`.
    - The token has already been consumed by a successful or attempted `POST /v1/auth`, or the failing request uses a token value that connector does not recognise.
  - Failure endpoint:
    `POST /v1/auth`
  - Why this fails:
    Connector returns `ONE_TIME_TOKEN_ALREADY_USED` or `ONE_TIME_TOKEN_INVALID`, which `AuthorisationRequestExceptionMapper` maps to a 400 error.
  - Intentionally violated constraints:
    The request uses an invalid, reused, or non-current generated token.
- Branch 2:
  - Preconditions:
    - A MOTO API payment exists and has a valid unused `one_time_token`. This can be satisfied by direct connector state or by calling `POST /v1/payments` with `authorisation_mode=moto_api` and using the returned token.
    - The card data in the failing request is invalid or rejected, such as an invalid card number, invalid expiry date, invalid CVC, or unsupported card type.
  - Failure endpoint:
    `POST /v1/auth`
  - Why this fails:
    Local bean validation rejects malformed authorisation fields, or connector returns `CARD_NUMBER_REJECTED`, `AUTHORISATION_REJECTED`, or `INVALID_ATTRIBUTE_VALUE`, which the mapper exposes as 402 or 422.
  - Intentionally violated constraints:
    Required card-detail validation or connector authorisation rules are violated.

Endpoint coverage:
- Covers:
  `POST /v1/auth`
- Distinct meaning:
  Completes card authorisation for a payment created in MOTO API mode.

### Function 11: create recurring payment from an agreement

Function name:
take recurring payment

Core endpoint(s):
- `POST /v1/payments`

Preconditions:
- An agreement exists for the authenticated account. This can be satisfied by directly seeding connector/ledger agreement state or by calling `POST /v1/agreements` with valid `reference` and `description`.
- A setup payment has been created for that agreement and completed successfully so the agreement is active and has a saved payment instrument. This can be satisfied by direct backing-store state or by calling `POST /v1/payments` with `set_up_agreement={agreementId}` and completing the external payment journey.
- The `agreement_id` body value must identify the active agreement. If the API created the agreement, this value must be taken from the `agreement_id` returned by `POST /v1/agreements`.

Successful execution:
- Result:
  Creates a payment charged against the saved payment instrument for an active agreement.
- Invocation:
  Step 1: `POST /v1/payments` with required payment fields, `authorisation_mode=agreement`, and `agreement_id={agreementId}`.
- Constraints:
  `authorisation_mode` must be `agreement`; `agreement_id` must be supplied only with that mode; `return_url`, `email`, and `prefilled_cardholder_details` must not be used for agreement-mode payment creation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No active agreement exists for the `agreement_id` value. This can be produced by omitting agreement creation, using an unknown id, using an agreement before the setup payment has activated it, or directly setting the agreement state to a non-`active` value in the backing store.
  - Failure endpoint:
    `POST /v1/payments`
  - Why this fails:
    `RequestJsonParser` only accepts `agreement_id` when `authorisation_mode=agreement`; connector rejects missing, unknown, or inactive agreements with `AGREEMENT_NOT_FOUND` or `AGREEMENT_NOT_ACTIVE`.
  - Intentionally violated constraints:
    The recurring payment request does not reference an active agreement scoped to the account.

Endpoint coverage:
- Covers:
  `POST /v1/payments`
- Distinct meaning:
  Uses payment creation for the agreement charging mode rather than a web or MOTO API payment.

### Function 12: get payment

Function name:
get payment

Core endpoint(s):
- `GET /v1/payments/{paymentId}`

Preconditions:
- A payment exists for the authenticated account. This can be satisfied by directly seeding connector/ledger payment state or by calling `POST /v1/payments` with body values such as `amount`, `reference`, `description`, and `return_url`; the response produces the `payment_id`.
- The `{paymentId}` path value must identify that payment. If the API creates the payment, `{paymentId}` must be taken from the `payment_id` returned by `POST /v1/payments`.

Successful execution:
- Result:
  Retrieves details and links for the payment identified by `{paymentId}`.
- Invocation:
  Step 1: `GET /v1/payments/{paymentId}` with the payment id in the path and optional hidden `X-Ledger` strategy header.
- Constraints:
  `{paymentId}` must exist and be scoped to the authenticated account. The implementation tries the configured connector/ledger strategy and returns no-cache headers.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No payment exists for `{paymentId}` for the authenticated account. This can be produced by starting from empty payment state, deleting the payment from the backing store, or intentionally not inserting it directly and not calling `POST /v1/payments`.
  - Failure endpoint:
    `GET /v1/payments/{paymentId}`
  - Why this fails:
    Connector or ledger returns not found and the get-payment exception mapper returns the public not-found error.
  - Intentionally violated constraints:
    The required payment id is unknown or not scoped to the account.

Endpoint coverage:
- Covers:
  `GET /v1/payments/{paymentId}`
- Distinct meaning:
  Reads one payment resource by id.

### Function 13: cancel payment

Function name:
cancel payment

Core endpoint(s):
- `POST /v1/payments/{paymentId}/cancel`

Preconditions:
- An unfinished non-agreement payment exists for the authenticated account. This can be satisfied by directly seeding connector payment state or by calling `POST /v1/payments` with body values such as `amount`, `reference`, `description`, and `return_url`, then leaving the payment unfinished; the response produces the `payment_id`.
- The `{paymentId}` path value must identify that unfinished payment. If the API creates the payment, `{paymentId}` must be taken from the `payment_id` returned by `POST /v1/payments`.

Successful execution:
- Result:
  Cancels the unfinished payment and returns no content.
- Invocation:
  Step 1: `POST /v1/payments/{paymentId}/cancel` with the payment id in the path.
- Constraints:
  The payment must exist, be scoped to the account, and be in a cancellable unfinished state. Agreement-mode payments do not receive a cancel link in the response model.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No payment exists for `{paymentId}` for the authenticated account. This can be produced by starting from empty payment state, deleting the payment from the backing store, or intentionally not inserting it directly and not calling `POST /v1/payments`.
  - Failure endpoint:
    `POST /v1/payments/{paymentId}/cancel`
  - Why this fails:
    Connector returns 404 and `CancelChargeExceptionMapper` maps it to the cancel-payment not-found error.
  - Intentionally violated constraints:
    The payment id required by the cancel endpoint is missing or not account-scoped.
- Branch 2:
  - Preconditions:
    - A payment exists for `{paymentId}`. This can be satisfied by direct connector state or by calling `POST /v1/payments` with body values such as `amount`, `reference`, `description`, and `return_url`; the response produces the `payment_id` used as `{paymentId}`.
    - The payment is already cancelled or otherwise not cancellable. This can be satisfied by directly setting a non-cancellable state in connector or by calling `POST /v1/payments/{paymentId}/cancel` once before the failing request.
  - Failure endpoint:
    `POST /v1/payments/{paymentId}/cancel`
  - Why this fails:
    Connector returns a bad request or conflict for an invalid state transition, and `CancelChargeExceptionMapper` maps it to 400 or 409.
  - Intentionally violated constraints:
    The same payment id is reused after cancellation or while in a state that cannot be cancelled.

Endpoint coverage:
- Covers:
  `POST /v1/payments/{paymentId}/cancel`
- Distinct meaning:
  Performs the payment state transition from unfinished to cancelled.

### Function 14: capture delayed payment

Function name:
capture delayed payment

Core endpoint(s):
- `POST /v1/payments/{paymentId}/capture`

Preconditions:
- A delayed-capture payment exists for the authenticated account. This can be satisfied by directly seeding connector payment state or by calling `POST /v1/payments` with body values `amount`, `reference`, `description`, `authorisation_mode=moto_api`, and `delayed_capture=true`; the response produces `payment_id` and `auth_url_post.params.one_time_token`.
- The delayed-capture payment has been successfully authorised and is awaiting capture. This can be satisfied by direct connector state or by calling `POST /v1/auth` with the payment's `one_time_token` plus `card_number`, `cardholder_name`, `cvc`, and `expiry_date`.
- The `{paymentId}` path value must identify that awaiting-capture payment. If the API creates the payment, `{paymentId}` must be taken from the `payment_id` returned by `POST /v1/payments`.

Successful execution:
- Result:
  Captures funds for the delayed-capture payment and returns no content.
- Invocation:
  Step 1: `POST /v1/payments/{paymentId}/capture` with the payment id in the path.
- Constraints:
  The payment must exist, be scoped to the account, have `delayed_capture=true`, and be in the connector state that allows capture.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No payment exists for `{paymentId}` for the authenticated account. This can be produced by starting from empty payment state, deleting the payment from the backing store, or intentionally not inserting it directly and not calling `POST /v1/payments`.
  - Failure endpoint:
    `POST /v1/payments/{paymentId}/capture`
  - Why this fails:
    Connector returns 404 and `CaptureChargeExceptionMapper` maps it to the capture-payment not-found error.
  - Intentionally violated constraints:
    The payment id required by the capture endpoint is missing or not account-scoped.
- Branch 2:
  - Preconditions:
    - A payment exists for `{paymentId}`. This can be satisfied by direct connector payment state or by calling `POST /v1/payments` with body values such as `amount`, `reference`, `description`, and `return_url`; the response produces the `payment_id` used as `{paymentId}`.
    - The payment is not delayed-capture awaiting-capture state, because it was created without `delayed_capture=true`, has not been authorised, has already been captured, or is otherwise in a state connector rejects for capture.
  - Failure endpoint:
    `POST /v1/payments/{paymentId}/capture`
  - Why this fails:
    Connector returns a bad request or conflict for the invalid capture state, and `CaptureChargeExceptionMapper` maps that response.
  - Intentionally violated constraints:
    The payment state does not satisfy the delayed-capture awaiting-capture requirement.

Endpoint coverage:
- Covers:
  `POST /v1/payments/{paymentId}/capture`
- Distinct meaning:
  Captures an already-authorised delayed payment.

### Function 15: get payment events

Function name:
get payment events

Core endpoint(s):
- `GET /v1/payments/{paymentId}/events`

Preconditions:
- A payment exists for the authenticated account and has event history. This can be satisfied by directly seeding connector/ledger payment event state or by calling `POST /v1/payments` with body values such as `amount`, `reference`, `description`, and `return_url`, which returns `payment_id` and creates at least an initial payment event.
- The `{paymentId}` path value must identify that payment. If the API creates the payment, `{paymentId}` must be taken from the `payment_id` returned by `POST /v1/payments`.

Successful execution:
- Result:
  Retrieves the state-change event history for the payment.
- Invocation:
  Step 1: `GET /v1/payments/{paymentId}/events` with the payment id in the path and optional hidden `X-Ledger` strategy header.
- Constraints:
  `{paymentId}` must exist and be scoped to the authenticated account. The implementation can read from connector or ledger depending on strategy.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No payment exists for `{paymentId}` for the authenticated account. This can be produced by starting from empty payment state, deleting the payment from the backing store, or intentionally not inserting it directly and not calling `POST /v1/payments`.
  - Failure endpoint:
    `GET /v1/payments/{paymentId}/events`
  - Why this fails:
    Connector or ledger returns not found and the get-events exception mapper returns the public not-found error.
  - Intentionally violated constraints:
    The payment id required to read events is missing or not account-scoped.

Endpoint coverage:
- Covers:
  `GET /v1/payments/{paymentId}/events`
- Distinct meaning:
  Reads the event history for one payment.

### Function 16: list refunds for payment

Function name:
list payment refunds

Core endpoint(s):
- `GET /v1/payments/{paymentId}/refunds`

Preconditions:
- A payment exists for the authenticated account. This can be satisfied by directly seeding connector/ledger payment state or by calling `POST /v1/payments` with body values such as `amount`, `reference`, `description`, and `return_url`; the response produces the `payment_id`.
- The `{paymentId}` path value must identify that payment. If the API creates the payment, `{paymentId}` must be taken from the `payment_id` returned by `POST /v1/payments`.

Successful execution:
- Result:
  Returns the refund list for the payment, possibly empty.
- Invocation:
  Step 1: `GET /v1/payments/{paymentId}/refunds` with the payment id in the path and optional hidden `X-Ledger` strategy header.
- Constraints:
  `{paymentId}` must exist and be scoped to the authenticated account. The default strategy reads this list from ledger.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No payment exists for `{paymentId}` for the authenticated account. This can be produced by starting from empty payment state, deleting the payment from the backing store, or intentionally not inserting it directly and not calling `POST /v1/payments`.
  - Failure endpoint:
    `GET /v1/payments/{paymentId}/refunds`
  - Why this fails:
    Ledger or connector returns not found and the get-refunds exception mapper returns the public not-found error.
  - Intentionally violated constraints:
    The parent payment id required to list refunds is missing or not account-scoped.

Endpoint coverage:
- Covers:
  `GET /v1/payments/{paymentId}/refunds`
- Distinct meaning:
  Lists refund resources belonging to one payment.

### Function 17: refund payment

Function name:
refund payment

Core endpoint(s):
- `POST /v1/payments/{paymentId}/refunds`

Preconditions:
- A payment exists for the authenticated account. This can be satisfied by directly seeding connector payment state or by calling `POST /v1/payments` with body values `amount`, `reference`, `description`, and `authorisation_mode=moto_api`; the response produces `payment_id` and `auth_url_post.params.one_time_token`.
- The payment has completed successfully and has refundable amount available. This can be satisfied by direct connector state or, for a documented API setup path, by calling `POST /v1/auth` with the MOTO API payment's `one_time_token` plus `card_number`, `cardholder_name`, `cvc`, and `expiry_date`.
- The `{paymentId}` path value must identify that successful refundable payment. If the API creates the payment, `{paymentId}` must be taken from the `payment_id` returned by `POST /v1/payments`.

Successful execution:
- Result:
  Creates a full or partial refund for the payment and returns the accepted refund response.
- Invocation:
  Step 1: `POST /v1/payments/{paymentId}/refunds` with body field `amount` and optional `refund_amount_available`.
- Constraints:
  `amount` is required and must be an integer between 1 and 10000000. `refund_amount_available`, when supplied, must match connector's current available amount for that payment.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No payment exists for `{paymentId}` for the authenticated account. This can be produced by starting from empty payment state, deleting the payment from the backing store, or intentionally not inserting it directly and not calling `POST /v1/payments`.
  - Failure endpoint:
    `POST /v1/payments/{paymentId}/refunds`
  - Why this fails:
    Connector returns 404 and `CreateRefundExceptionMapper` maps it to the create-refund not-found error.
  - Intentionally violated constraints:
    The payment id required to create a refund is missing or not account-scoped.
- Branch 2:
  - Preconditions:
    - A payment exists for `{paymentId}` for the authenticated account. This can be satisfied by direct connector state or by calling `POST /v1/payments` with body values `amount`, `reference`, `description`, and `authorisation_mode=moto_api`, then calling `POST /v1/auth` with the returned `one_time_token`, `card_number`, `cardholder_name`, `cvc`, and `expiry_date`.
    - The refund request asks for an invalid or unavailable amount, supplies a `refund_amount_available` that does not match connector state, or the payment is blocked from refunding by a dispute.
  - Failure endpoint:
    `POST /v1/payments/{paymentId}/refunds`
  - Why this fails:
    Local parsing rejects missing or nonnumeric `amount`; connector rejects unavailable refunds, dispute-blocked refunds, and amount-available mismatches, which `CreateRefundExceptionMapper` maps to 400 or 412.
  - Intentionally violated constraints:
    Refund amount, available amount, or payment refundability constraints are violated.

Endpoint coverage:
- Covers:
  `POST /v1/payments/{paymentId}/refunds`
- Distinct meaning:
  Creates a refund transaction for one payment.

### Function 18: get refund

Function name:
get refund

Core endpoint(s):
- `GET /v1/payments/{paymentId}/refunds/{refundId}`

Preconditions:
- A payment exists for the authenticated account. This can be satisfied by directly seeding connector/ledger payment state or by calling `POST /v1/payments` with body values `amount`, `reference`, `description`, and `authorisation_mode=moto_api`; the response produces `payment_id` and `auth_url_post.params.one_time_token`.
- The payment has completed successfully and is refundable. This can be satisfied by direct connector state or, for a documented API setup path, by calling `POST /v1/auth` with the MOTO API payment's `one_time_token` plus `card_number`, `cardholder_name`, `cvc`, and `expiry_date`.
- A refund exists for that payment. This can be satisfied by directly seeding connector/ledger refund state linked to `{paymentId}` or by calling `POST /v1/payments/{paymentId}/refunds` with body field `amount` and optional `refund_amount_available`; the response produces `refund_id`.
- The `{paymentId}` and `{refundId}` path values must identify the linked payment and refund. If the API creates the refund, `{refundId}` must be taken from the `refund_id` returned by `POST /v1/payments/{paymentId}/refunds`.

Successful execution:
- Result:
  Retrieves the refund identified by `{refundId}` for the parent payment identified by `{paymentId}`.
- Invocation:
  Step 1: `GET /v1/payments/{paymentId}/refunds/{refundId}` with both ids in the path and optional hidden `X-Ledger` strategy header.
- Constraints:
  The refund must exist, belong to the specified payment, and be scoped to the authenticated account.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A payment exists for `{paymentId}`. This can be satisfied by direct connector/ledger payment state or by calling `POST /v1/payments` with body values such as `amount`, `reference`, `description`, and `return_url`; the response produces the `payment_id` used as `{paymentId}`.
    - No refund with `{refundId}` exists for that payment, or the refund id belongs to another payment/account. This can be produced by omitting direct refund insertion and not calling `POST /v1/payments/{paymentId}/refunds`, by using an unknown refund id, or by using a cross-scoped refund id.
  - Failure endpoint:
    `GET /v1/payments/{paymentId}/refunds/{refundId}`
  - Why this fails:
    A refund-specific read requires a real refund id linked to the parent payment; connector or ledger not-found is mapped to the get-refund not-found error.
  - Intentionally violated constraints:
    The refund id is missing, unknown, or not linked to the supplied payment id/account.

Endpoint coverage:
- Covers:
  `GET /v1/payments/{paymentId}/refunds/{refundId}`
- Distinct meaning:
  Reads one refund resource under one payment.

### Function 19: search refunds

Function name:
search refunds

Core endpoint(s):
- `GET /v1/refunds`

Preconditions:
- A valid authenticated account is available for the search. No refund resource has to exist; ledger can return an empty result set from its backing store.

Successful execution:
- Result:
  Returns refunds matching optional date, settled-date, and pagination filters.
- Invocation:
  Step 1: `GET /v1/refunds` with optional query parameters `from_date`, `to_date`, `from_settled_date`, `to_settled_date`, `page`, and `display_size`.
- Constraints:
  `from_date` and `to_date` must be UTC ISO-8601 date-times; settled-date filters must be ISO-8601 date-only values; `page` must be at least 1; `display_size` must be between 1 and 500.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No resource state is required; the failing request supplies invalid refund search values such as malformed `from_date`, malformed settled dates, invalid `page`, or `display_size=501`.
  - Failure endpoint:
    `GET /v1/refunds`
  - Why this fails:
    `RefundSearchValidator` rejects invalid dates, page values, and display-size values before ledger search.
  - Intentionally violated constraints:
    Refund search query values do not match the implementation validators.

Endpoint coverage:
- Covers:
  `GET /v1/refunds`
- Distinct meaning:
  Searches refunds across payments without requiring a parent payment id.

### Function 20: search disputes

Function name:
search disputes

Core endpoint(s):
- `GET /v1/disputes`

Preconditions:
- A valid authenticated account is available for the search. No dispute resource has to exist; ledger can return an empty result set from its backing store.

Successful execution:
- Result:
  Returns payment disputes matching optional filters.
- Invocation:
  Step 1: `GET /v1/disputes` with optional query parameters including `from_date`, `to_date`, `from_settled_date`, `to_settled_date`, `status`, `page`, and `display_size`.
- Constraints:
  `status` must be `needs_response`, `under_review`, `lost`, or `won`; date-time filters and settled-date filters must match the expected formats; pagination must pass implementation validation. The implementation rewrites public `status` values to ledger state values.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No resource state is required; the failing request supplies invalid dispute search values such as unsupported `status`, malformed date filters, invalid `page`, or over-large `display_size`.
  - Failure endpoint:
    `GET /v1/disputes`
  - Why this fails:
    `DisputeSearchValidator` rejects invalid dates, status, page, or display-size values before ledger search.
  - Intentionally violated constraints:
    Dispute search query values do not match the implementation validators.

Endpoint coverage:
- Covers:
  `GET /v1/disputes`
- Distinct meaning:
  Searches dispute records independently of a specific payment endpoint.
