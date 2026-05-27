# Domain-Level Behavior Analysis

## Domain Summary

This service is the public GOV.UK Pay card-payment API. Its main domain resources are payments, recurring-payment agreements, refunds, and disputes. It also exposes reporting-style reads over payment events, agreement search, refund search, and dispute search.

The service mostly acts as a public façade over Connector and Ledger. Connector owns active payment, refund, agreement, capture, cancellation, and authorisation state transitions. Ledger owns search and some read/event/refund-history views. Public API adds request parsing, validation, public response links, authentication scoping by gateway account, and some fallback/read-strategy behavior.

## Available Function Inventory

### Agreements

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search agreements` | `GET /v1/agreements` | Search recurring-payment agreements for the authenticated account. |
| `create agreement` | `POST /v1/agreements` | Create a reusable recurring-payment agreement. |
| `get agreement` | `GET /v1/agreements/{agreementId}` | Read one agreement by generated agreement id. |
| `create setup-agreement payment` | `POST /v1/payments` | Create a payment that can attach a payment instrument to an agreement after successful completion. |
| `cancel agreement` | `POST /v1/agreements/{agreementId}/cancel` | Cancel an active recurring-payment agreement. |

### Payments

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search payments` | `GET /v1/payments` | Search payments for the authenticated account. |
| `create web card payment` | `POST /v1/payments` | Create a standard hosted web card payment. |
| `replay idempotent payment creation` | `POST /v1/payments` | Reuse an idempotency key to return an existing payment instead of creating another one. |
| `create MOTO API payment` | `POST /v1/payments` | Create an API-authorised MOTO payment and receive a one-time authorisation token. |
| `authorise MOTO API payment` | `POST /v1/auth` | Submit card details using the one-time token to authorise a MOTO API payment. |
| `take recurring payment` | `POST /v1/payments` | Create a payment charged against an active recurring agreement. |
| `get payment` | `GET /v1/payments/{paymentId}` | Read one payment by generated payment id. |
| `cancel payment` | `POST /v1/payments/{paymentId}/cancel` | Cancel an unfinished payment. |
| `capture delayed payment` | `POST /v1/payments/{paymentId}/capture` | Capture an already-authorised delayed-capture payment. |
| `get payment events` | `GET /v1/payments/{paymentId}/events` | Read state-change events for one payment. |

### Refunds

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list payment refunds` | `GET /v1/payments/{paymentId}/refunds` | List refunds belonging to one payment. |
| `refund payment` | `POST /v1/payments/{paymentId}/refunds` | Create a full or partial refund for a successful payment. |
| `get refund` | `GET /v1/payments/{paymentId}/refunds/{refundId}` | Read one refund under its parent payment. |
| `search refunds` | `GET /v1/refunds` | Search refunds across payments. |

### Disputes

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search disputes` | `GET /v1/disputes` | Search payment disputes reported through Ledger. |

## Supported Business Behaviors

### Behavior 1: Search recurring agreements

Business goal:
Find recurring-payment agreements for the authenticated service account.

Domain context:
Services need to reconcile agreement records by reference or status before charging or cancelling them.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `search agreements` (`GET /v1/agreements`) with optional query values such as `reference=REF-001`, `status=active`, `page=1`, and `display_size=100` to retrieve matching agreements.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup is required. If matching agreements already exist in Ledger, the response can be non-empty.
- Direct Ledger setup can be used to create agreement rows for search testing, but the query must still be scoped to the authenticated gateway account.

Parameter and value bindings:
- `reference` is matched exactly by Ledger because Public API sends `exact_reference_match=true`.
- `status` must be one of `created`, `active`, `cancelled`, or `inactive`.
- `page` and `display_size` control pagination only.

Business result:
No domain state changes. The caller receives a paged view of agreements for the authenticated account.

Constraints and invariants:
- Unknown nonblank agreement search parameters are rejected.
- `display_size` must be 1 to 500, and `page` must be at least 1.
- `reference` must not exceed the implementation’s 255-character limit.

Failure and exceptional cases:
- Failing function: `search agreements`
  - Failure condition: `status=expired`, `page=0`, `display_size=501`, overlong `reference`, or an unsupported nonblank query parameter.
  - Why it fails: `AgreementSearchValidator` validates query names and values before Ledger search.
  - Violated prerequisite or constraint: Search filters must use supported names and allowed value ranges.
- Failing function: `search agreements`
  - Failure condition: Ledger returns a non-200 response or malformed search response.
  - Why it fails: `LedgerService.searchAgreements` maps downstream failures to `SearchAgreementsException`.
  - Violated prerequisite or constraint: Ledger must be available and able to return agreement search results.

Implementation notes:
Search is read from Ledger, not Connector. The source enforces query-name validation for agreements, which is stricter than many other collection searches.

### Behavior 2: Create a recurring agreement

Business goal:
Create an agreement that can later be activated by a setup payment and used for recurring charges.

Domain context:
An agreement represents a customer’s consent and service reference for future recurring payments.

Starting point:
No prior agreement state, but the authenticated account must be configured by Connector to allow recurring card payments.

Required execution workflow:
1. Use function `create agreement` (`POST /v1/agreements`) with JSON body `reference=AGREE-001`, `description=Council tax subscription`, and optional `user_identifier=user-001` to create the agreement.
2. Capture the returned `agreement_id`.

Optional verification workflow:
1. Use function `get agreement` (`GET /v1/agreements/{agreementId}`) with `agreementId={agreement_id from create agreement}` to inspect the created agreement.
2. Use function `search agreements` (`GET /v1/agreements`) with `reference=AGREE-001` to verify it appears in agreement search.

Existing-state shortcuts:
- Direct Connector/Ledger setup can create an equivalent agreement, in which case step 1 can be skipped for later behaviors.
- The generated `agreement_id` must still belong to the same authenticated account.

Parameter and value bindings:
- `reference`, `description`, and optional `user_identifier` create the agreement business identity.
- The generated `agreement_id` returned by `create agreement` is reused as `{agreementId}` in `get agreement`, `cancel agreement`, and as `set_up_agreement` or `agreement_id` in payment creation.

Business result:
A new agreement exists for the authenticated account, initially not usable for recurring charges until a setup payment successfully activates it.

Constraints and invariants:
- Implementation requires nonblank string `reference` and `description`.
- `user_identifier`, if supplied, must be a string.
- The OpenAPI schema does not mark `reference` and `description` as required, but source parsing does.

Failure and exceptional cases:
- Failing function: `create agreement`
  - Failure condition: Missing, blank, malformed, or non-string `reference` or `description`.
  - Why it fails: `RequestJsonParser.parseAgreementRequest` requires nonblank string values.
  - Violated prerequisite or constraint: Agreement creation requires valid reference and description.
- Failing function: `create agreement`
  - Failure condition: Connector reports `RECURRING_CARD_PAYMENTS_NOT_ALLOWED`.
  - Why it fails: `CreateAgreementExceptionMapper` maps this to a 422 recurring-card-payments capability error.
  - Violated prerequisite or constraint: The gateway account must be enabled for recurring card payments.

Implementation notes:
The resource creates the agreement in Connector, then immediately reads it from Ledger to build the public response. This means creation success depends on a follow-up Ledger read being available and consistent enough to return the new agreement.

### Behavior 3: Retrieve one agreement

Business goal:
Inspect the current state and details of a known recurring agreement.

Domain context:
Services need to know whether an agreement exists and what state Ledger reports before charging or cancelling it.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create agreement` (`POST /v1/agreements`) with body `reference=AGREE-READ-001` and `description=Agreement to inspect` to create an agreement.
2. Capture `agreement_id` from the response.
3. Use function `get agreement` (`GET /v1/agreements/{agreementId}`) with `agreementId={agreement_id from step 1}` to retrieve it.

Optional verification workflow:
None.

Existing-state shortcuts:
- If an equivalent agreement already exists, skip `create agreement` and use its existing `agreement_id`.
- Direct Ledger setup can seed an agreement, but it must be scoped to the same gateway account.

Parameter and value bindings:
- The `{agreementId}` path value in `get agreement` must be the same `agreement_id` generated by `create agreement`.
- Account scoping is implicit through the authenticated account and Ledger query.

Business result:
No mutation beyond setup. The caller receives one agreement resource.

Constraints and invariants:
- The agreement id must exist in Ledger and be visible to the authenticated account.
- There is no public API to retrieve an agreement by `reference` directly; search must be used for reference lookup.

Failure and exceptional cases:
- Failing function: `create agreement`
  - Failure condition: Invalid creation body or recurring payments disabled.
  - Why it fails: Local parser or Connector capability checks reject the setup.
  - Violated prerequisite or constraint: A retrievable agreement cannot be created.
- Failing function: `get agreement`
  - Failure condition: `{agreementId}` is unknown or belongs to another account.
  - Why it fails: `AgreementsService.getAgreement` receives not found from Ledger.
  - Violated prerequisite or constraint: The agreement must exist and be account-scoped.

Implementation notes:
`get agreement` reads from Ledger with `X-Consistent=true`.

### Behavior 4: Initiate setup of a recurring agreement through a payment

Business goal:
Create a payment whose successful completion will save a payment instrument to an agreement.

Domain context:
An agreement cannot be charged until a payer completes a setup payment that attaches a reusable instrument.

Starting point:
No prior agreement state.

Required execution workflow:
1. Use function `create agreement` (`POST /v1/agreements`) with body `reference=AGREE-SETUP-001` and `description=Subscription agreement` to create the agreement.
2. Capture `agreement_id`.
3. Use function `create setup-agreement payment` (`POST /v1/payments`) with body `amount=1000`, `reference=SETUP-PAY-001`, `description=Initial setup payment`, `return_url=https://service.example/return/setup`, and `set_up_agreement={agreement_id from step 1}` to create the setup payment.
4. Capture the returned `payment_id` and hosted payment link for the external web journey.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from step 3}` to inspect the setup payment.
2. Use function `get agreement` (`GET /v1/agreements/{agreementId}`) with `agreementId={agreement_id from step 1}` to inspect the agreement state.

Existing-state shortcuts:
- If the agreement already exists, skip `create agreement` and use its `agreement_id` as `set_up_agreement`.
- Direct backing-store setup can create an active agreement with a saved payment instrument, but the `agreement_id`, account ownership, and active status must still match later recurring-payment calls.

Parameter and value bindings:
- `set_up_agreement` in payment creation must equal the `agreement_id` returned by `create agreement`.
- Public API maps `set_up_agreement` to Connector payload `agreement_id={agreement_id}` and `save_payment_instrument_to_agreement=true`.
- The setup `payment_id` is separate from the `agreement_id`; the payment represents the setup transaction, while the agreement represents future charging authority.

Business result:
A setup payment exists and is linked to the agreement. The agreement is not guaranteed active by this API workflow alone; activation depends on successful completion of the hosted payment journey outside these functions.

Constraints and invariants:
- `set_up_agreement` must identify an existing agreement for the account.
- Standard payment validation applies to `amount`, `reference`, `description`, and `return_url`.
- Connector may reject incompatible authorisation-mode combinations for saving a payment instrument.

Failure and exceptional cases:
- Failing function: `create agreement`
  - Failure condition: Invalid agreement body or recurring-card-payments disabled.
  - Why it fails: Local agreement validation or Connector capability enforcement rejects setup.
  - Violated prerequisite or constraint: A valid agreement id is required before setup payment creation.
- Failing function: `create setup-agreement payment`
  - Failure condition: `set_up_agreement` is unknown or not account-scoped.
  - Why it fails: Connector returns `AGREEMENT_NOT_FOUND`, mapped to an invalid `set_up_agreement` create-payment error.
  - Violated prerequisite or constraint: The setup payment must reference an existing agreement.
- Failing function: `create setup-agreement payment`
  - Failure condition: Missing payment fields, invalid URL, invalid amount, disabled account, missing PSP, or disallowed payment mode.
  - Why it fails: Local parser/bean validation or Connector policy rejects the request.
  - Violated prerequisite or constraint: The account and payment request must be valid for payment creation.

Implementation notes:
This API can initiate setup but cannot itself complete the hosted web journey. The setup payment creates the state needed for external completion.

### Behavior 5: Cancel an active recurring agreement

Business goal:
Stop future recurring charges against an active agreement.

Domain context:
A service must be able to end a customer’s recurring payment authority.

Starting point:
Pre-existing active agreement state.

Required execution workflow:
1. Use function `cancel agreement` (`POST /v1/agreements/{agreementId}/cancel`) with `agreementId={active agreement_id}` to cancel the agreement.

Optional verification workflow:
1. Use function `get agreement` (`GET /v1/agreements/{agreementId}`) with `agreementId={same agreement_id}` to inspect the resulting status.
2. Use function `search agreements` (`GET /v1/agreements`) with `status=cancelled` and `reference={agreement reference}` to find it in reporting.

Existing-state shortcuts:
- The main workflow starts from pre-existing active state because the public API has no function to complete the hosted setup payment journey.
- API setup can partially establish the state by using `create agreement` and `create setup-agreement payment`; the actual activation still requires external payment completion or direct backing-store setup.
- Direct database/Connector setup must create an agreement with the same account ownership and `active` status.

Parameter and value bindings:
- `{agreementId}` in `cancel agreement` must be the same generated `agreement_id` of the active agreement.
- Optional `get agreement` and `search agreements` reuse the same `agreementId` or agreement `reference`.

Business result:
The active agreement is cancelled. Future `take recurring payment` calls using that `agreement_id` should be rejected by Connector as inactive or not active.

Constraints and invariants:
- Only active agreements are cancellable.
- The agreement must be scoped to the authenticated account.
- Cancellation is delegated to Connector; Public API does not perform local state mutation.

Failure and exceptional cases:
- Failing function: `cancel agreement`
  - Failure condition: `{agreementId}` is unknown or belongs to another account.
  - Why it fails: Connector returns 404, mapped to cancel-agreement not found.
  - Violated prerequisite or constraint: Agreement must exist and be account-scoped.
- Failing function: `cancel agreement`
  - Failure condition: Agreement exists but is not `active`.
  - Why it fails: Connector rejects cancellation with a bad request.
  - Violated prerequisite or constraint: Only active agreements can be cancelled.

Implementation notes:
The public endpoint always returns 204 only when Connector returns 204. It does not re-read Ledger after cancellation.

### Behavior 6: Search payments

Business goal:
Find payments for reporting, reconciliation, support, or agreement-linked charging analysis.

Domain context:
Payments are the central transaction record and can be searched by business reference, state, dates, card attributes, settlement dates, or agreement id.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `search payments` (`GET /v1/payments`) with optional query values such as `reference=PAY-001`, `email=user@example.org`, `state=success`, `card_brand=visa`, `from_date=2026-01-01T00:00:00Z`, `to_date=2026-02-01T00:00:00Z`, `page=1`, `display_size=100`, `first_digits_card_number=424242`, `last_digits_card_number=4242`, `from_settled_date=2026-01-02`, `to_settled_date=2026-02-02`, and `agreement_id={agreement_id}`.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup is required. If matching payments already exist in Ledger, results can be non-empty.
- Direct Ledger setup can create searchable payment rows, but they must carry the authenticated account id.

Parameter and value bindings:
- `reference` is sent to Ledger with `exact_reference_match=true`.
- `agreement_id` filters payments authorised using a specific agreement.
- Card digit filters must match the stored card fragments.

Business result:
No state changes. The caller receives a paged Ledger view of payments.

Constraints and invariants:
- `state` must be one of `created`, `started`, `submitted`, `success`, `failed`, `cancelled`, or `error`.
- Date-time filters must use valid UTC ISO-8601 date-time strings; settled-date filters must use date-only ISO format.
- First card digits must be exactly 6 numeric digits; last card digits must be exactly 4 numeric digits.
- `display_size` must be 1 to 500; `page` must be at least 1.

Failure and exceptional cases:
- Failing function: `search payments`
  - Failure condition: Invalid state, malformed date, invalid pagination, overlong `reference` or `email`, or malformed card digits.
  - Why it fails: `PaymentSearchValidator` rejects invalid query values before calling Ledger.
  - Violated prerequisite or constraint: Search filters must satisfy local validators.
- Failing function: `search payments`
  - Failure condition: Ledger search fails.
  - Why it fails: `PaymentSearchService`/`LedgerService.searchPayments` maps non-200 or malformed responses.
  - Violated prerequisite or constraint: Ledger must serve the account-scoped payment search.

Implementation notes:
The OpenAPI documents payment search as public reporting. Implementation always adds `account_id`, `transaction_type=PAYMENT`, and `exact_reference_match=true` to the Ledger request.

### Behavior 7: Create a hosted web card payment

Business goal:
Start a standard card payment journey for a payer.

Domain context:
This is the normal GOV.UK Pay web flow: the API creates the payment and returns links for the payer to continue on hosted pages.

Starting point:
No prior payment state.

Required execution workflow:
1. Use function `create web card payment` (`POST /v1/payments`) with JSON body `amount=12000`, `reference=PAY-WEB-001`, `description=Passport application`, `return_url=https://service.example/return/PAY-WEB-001`, and optional `email`, `language=en`, `metadata`, `prefilled_cardholder_details`, `moto=false`, or `delayed_capture=false`.
2. Capture the returned `payment_id` and response links.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from step 1}` to inspect the created payment.
2. Use function `search payments` (`GET /v1/payments`) with `reference=PAY-WEB-001` to find it in reporting.

Existing-state shortcuts:
- None for creating a new payment. Reusing an existing payment is a different behavior handled by idempotency.
- Direct Connector/Ledger setup can seed a payment for read/search tests, but it is not equivalent to creating a new payment through Public API.

Parameter and value bindings:
- `amount`, `reference`, and `description` create the payment’s business amount and service-facing identity.
- `return_url` is used for the hosted web journey.
- The generated `payment_id` is reused by get, cancel, capture, event, and refund functions.
- Optional metadata is attached to the payment and later appears in payment views/search where supported.

Business result:
A new payment exists. It is usually unfinished until the payer completes the hosted journey.

Constraints and invariants:
- Implementation requires `amount`, nonblank `reference`, and nonblank `description`.
- `amount` is locally allowed from 0 to 10,000,000 pence, but zero amount requires Connector account capability; the public validation message says minimum 1.
- OpenAPI marks `return_url` as required, but the parser only requires it when the selected mode/downstream Connector requires it.
- `reference` is not unique.

Failure and exceptional cases:
- Failing function: `create web card payment`
  - Failure condition: Missing `amount`, `reference`, or `description`; invalid JSON types; invalid optional fields; invalid metadata.
  - Why it fails: `RequestJsonParser.parsePaymentRequest`, bean validation, and metadata validation reject the request.
  - Violated prerequisite or constraint: Payment creation requires a valid request body.
- Failing function: `create web card payment`
  - Failure condition: Account disabled, not linked to PSP, MOTO disabled for MOTO request, authorisation API disabled, zero amount not allowed, or card-number content in a payment-link reference.
  - Why it fails: Connector returns an error identifier mapped by `CreateChargeExceptionMapper`.
  - Violated prerequisite or constraint: Account capability and Connector policy must allow the requested payment.

Implementation notes:
For a new payment, Public API returns 201 and a `Location` header built from the generated `payment_id`. It also returns no-cache headers.

### Behavior 8: Reuse an idempotency key for payment creation

Business goal:
Prevent duplicate payment creation when a client retries the same create-payment request.

Domain context:
Payment creation is sensitive to network retries. Idempotency lets a service safely retry an equivalent request.

Starting point:
No prior payment state.

Required execution workflow:
1. Use function `create web card payment` (`POST /v1/payments`) with header `Idempotency-Key=pay-retry-001` and body `amount=12000`, `reference=PAY-IDEMP-001`, `description=Retry-safe payment`, and `return_url=https://service.example/return/PAY-IDEMP-001`.
2. Capture the returned `payment_id`.
3. Use function `replay idempotent payment creation` (`POST /v1/payments`) with the same header `Idempotency-Key=pay-retry-001` and an equivalent JSON body to return the existing payment.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from step 1}` to confirm the same payment is returned.
2. Use function `search payments` (`GET /v1/payments`) with `reference=PAY-IDEMP-001` to verify that only the expected payment appears for that reference in the test context.

Existing-state shortcuts:
- If Connector idempotency state already exists for `Idempotency-Key=pay-retry-001`, skip step 1 and invoke the replay directly.
- The stored idempotency key must belong to the same authenticated account and equivalent request body.

Parameter and value bindings:
- `Idempotency-Key` in step 3 must exactly match step 1.
- The replay body must be equivalent to the original body.
- The returned `payment_id` should be the same existing payment id, not a newly generated one.

Business result:
No second payment is created. The replay returns the existing payment, typically with HTTP 200 instead of the original 201.

Constraints and invariants:
- `Idempotency-Key` must be 1 to 255 characters and contain only alphanumeric characters and hyphens.
- Reusing a key for a different body is treated as a conflict.

Failure and exceptional cases:
- Failing function: `create web card payment`
  - Failure condition: Initial create request is invalid or not allowed by Connector.
  - Why it fails: Local parser/validator or Connector rejects the setup request.
  - Violated prerequisite or constraint: Idempotent state cannot be established.
- Failing function: `replay idempotent payment creation`
  - Failure condition: Same key is reused with changed `amount`, `reference`, `description`, mode, or other material body value.
  - Why it fails: Connector returns `IDEMPOTENCY_KEY_USED`, mapped to 409.
  - Violated prerequisite or constraint: Idempotency key must be tied to an equivalent request.

Implementation notes:
Public API determines new versus existing from Connector status: 201 means brand new, 200 means existing.

### Behavior 9: Complete a MOTO API payment

Business goal:
Take a card payment by creating a MOTO API payment and submitting card details through the public authorisation endpoint.

Domain context:
MOTO API mode supports payment authorisation without redirecting the payer through the hosted web journey.

Starting point:
No prior payment state, but the authenticated account must be enabled for MOTO API authorisation.

Required execution workflow:
1. Use function `create MOTO API payment` (`POST /v1/payments`) with body `amount=12000`, `reference=PAY-MOTO-001`, `description=MOTO API payment`, and `authorisation_mode=moto_api`; omit `return_url`.
2. Capture `payment_id` and `auth_url_post.params.one_time_token` from the response.
3. Use function `authorise MOTO API payment` (`POST /v1/auth`) with body `one_time_token={one_time_token from step 2}`, `card_number=4242424242424242`, `cardholder_name=J Citizen`, `cvc=123`, and `expiry_date=12/30`.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from step 2}` to inspect the authorised payment.
2. Use function `get payment events` (`GET /v1/payments/{paymentId}/events`) with the same `paymentId` to inspect state transitions.

Existing-state shortcuts:
- If a MOTO API payment and valid unused token already exist, skip step 1 and use the existing `one_time_token`.
- Direct Connector setup must preserve the token-to-payment binding and the token must be unused.

Parameter and value bindings:
- `one_time_token` generated by `create MOTO API payment` is consumed by `authorise MOTO API payment`.
- `payment_id` identifies the payment for later reads, refunds, events, or delayed capture.
- The authorisation body card data is not reused by later Public API functions.

Business result:
The MOTO API payment is submitted for authorisation. On success, `POST /v1/auth` returns 204 and Connector records the payment’s authorised outcome.

Constraints and invariants:
- `authorisation_mode` must be `moto_api`.
- `return_url` should be omitted for MOTO API mode.
- Account must allow authorisation API.
- Token is single-use and must match the payment.

Failure and exceptional cases:
- Failing function: `create MOTO API payment`
  - Failure condition: Account does not allow authorisation API.
  - Why it fails: Connector returns `AUTHORISATION_API_NOT_ALLOWED`, mapped to 422.
  - Violated prerequisite or constraint: Account must be enabled for MOTO API authorisation.
- Failing function: `authorise MOTO API payment`
  - Failure condition: Token is invalid or already used.
  - Why it fails: Connector returns `ONE_TIME_TOKEN_INVALID` or `ONE_TIME_TOKEN_ALREADY_USED`, mapped to 400.
  - Violated prerequisite or constraint: The token must be valid, current, and unused.
- Failing function: `authorise MOTO API payment`
  - Failure condition: Invalid card number, expiry, CVC, unsupported card, or declined authorisation.
  - Why it fails: Local bean validation or Connector authorisation errors produce 402 or 422.
  - Violated prerequisite or constraint: Card data and card acceptance rules must pass validation.

Implementation notes:
`POST /v1/auth` does not use the standard authenticated `Account` parameter; the one-time token is the binding credential.

### Behavior 10: Take a recurring payment from an active agreement

Business goal:
Charge a saved payment instrument associated with an active recurring agreement.

Domain context:
After a customer has activated an agreement, the service can take subsequent payments without a new hosted card-entry journey.

Starting point:
Pre-existing active agreement with saved payment instrument.

Required execution workflow:
1. Use function `take recurring payment` (`POST /v1/payments`) with body `amount=5000`, `reference=RECUR-PAY-001`, `description=Monthly subscription charge`, `authorisation_mode=agreement`, and `agreement_id={active agreement_id}`.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from step 1}` to inspect the recurring payment.
2. Use function `search payments` (`GET /v1/payments`) with `agreement_id={same agreement_id}` to list payments charged against the agreement.

Existing-state shortcuts:
- The main workflow starts from pre-existing active agreement state because Public API cannot complete the hosted setup journey by itself.
- API setup can create an agreement and setup payment by using `create agreement` and `create setup-agreement payment`; external completion or direct backing-store setup is still required to make the agreement active with an instrument.

Parameter and value bindings:
- `agreement_id` in `take recurring payment` must equal an active agreement’s generated id.
- `authorisation_mode=agreement` is required for `agreement_id` to be accepted.
- `return_url`, `email`, and `prefilled_cardholder_details` must not be sent for agreement-mode payments.

Business result:
A new payment exists and is authorised against the agreement’s saved instrument, subject to Connector outcome.

Constraints and invariants:
- Agreement must exist, be account-scoped, active, and have a saved instrument.
- `agreement_id` is only accepted when `authorisation_mode=agreement`.
- The implementation rejects `agreement_id` as an unexpected field if `authorisation_mode` is absent or not `agreement`.

Failure and exceptional cases:
- Failing function: `take recurring payment`
  - Failure condition: `agreement_id` is missing, unknown, inactive, or not scoped to the account.
  - Why it fails: Parser enforces mode/field consistency; Connector returns `AGREEMENT_NOT_FOUND` or `AGREEMENT_NOT_ACTIVE`.
  - Violated prerequisite or constraint: Agreement-mode payment requires an active agreement.
- Failing function: `take recurring payment`
  - Failure condition: Request includes web-only fields such as `return_url`, `email`, or `prefilled_cardholder_details`.
  - Why it fails: Connector rejects unexpected attributes for agreement mode.
  - Violated prerequisite or constraint: Agreement-mode payments have a restricted request shape.

Implementation notes:
Public API forwards the agreement mode to Connector; it does not locally verify that the agreement is active.

### Behavior 11: Retrieve one payment

Business goal:
Inspect the current details, state, links, refunds summary, card details, and agreement binding for a known payment.

Domain context:
Payment retrieval is needed for customer support, reconciliation, refund decisions, cancellation, or capture decisions.

Starting point:
No prior payment state.

Required execution workflow:
1. Use function `create web card payment` (`POST /v1/payments`) with body `amount=12000`, `reference=PAY-READ-001`, `description=Payment to inspect`, and `return_url=https://service.example/return/PAY-READ-001`.
2. Capture `payment_id`.
3. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from step 1}` and optional hidden header `X-Ledger=ledger-only` or `X-Ledger=connector-only` to retrieve the payment.

Optional verification workflow:
None.

Existing-state shortcuts:
- If an equivalent payment already exists, skip creation and use its `payment_id`.
- Direct Connector or Ledger setup can seed payment state, but the selected read strategy must be able to find it.

Parameter and value bindings:
- `{paymentId}` must equal the generated `payment_id`.
- `X-Ledger=connector-only` forces Connector read; `X-Ledger=ledger-only` forces Ledger read; invalid values fall back to default.

Business result:
No mutation beyond setup. The caller receives one payment resource and public links.

Constraints and invariants:
- Payment must exist and be account-scoped.
- Default strategy tries Connector and falls back to Ledger after `GetChargeException`.

Failure and exceptional cases:
- Failing function: `create web card payment`
  - Failure condition: Invalid setup request or Connector account policy rejection.
  - Why it fails: Local or downstream validation rejects payment creation.
  - Violated prerequisite or constraint: A payment id cannot be created for retrieval.
- Failing function: `get payment`
  - Failure condition: Payment id is unknown or not account-scoped in the selected backing service.
  - Why it fails: Connector or Ledger returns not found and `GetChargeExceptionMapper` handles the error.
  - Violated prerequisite or constraint: The payment must exist in the selected read source.

Implementation notes:
The response includes no-cache headers. Payment links are built conditionally: cancel links are omitted for finished payments and agreement-mode payments; capture links depend on Connector-provided capture rels.

### Behavior 12: Cancel an unfinished payment

Business goal:
Stop a payment that has been created but not completed.

Domain context:
A service may need to cancel an in-progress payment when the payer abandons, starts over, or the service-side order expires.

Starting point:
No prior payment state.

Required execution workflow:
1. Use function `create web card payment` (`POST /v1/payments`) with body `amount=12000`, `reference=PAY-CANCEL-001`, `description=Payment to cancel`, and `return_url=https://service.example/return/PAY-CANCEL-001`.
2. Capture `payment_id`.
3. Use function `cancel payment` (`POST /v1/payments/{paymentId}/cancel`) with `paymentId={payment_id from step 1}` to cancel it.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={same payment_id}` to inspect the cancelled state.
2. Use function `get payment events` (`GET /v1/payments/{paymentId}/events`) with the same `paymentId` to inspect the cancellation event.

Existing-state shortcuts:
- If an unfinished non-agreement payment already exists, skip creation and cancel that `payment_id`.
- Direct Connector setup can create a cancellable payment, but it must be unfinished and account-scoped.

Parameter and value bindings:
- `{paymentId}` in `cancel payment` must be the `payment_id` created in step 1.
- Optional verification reuses the same id.

Business result:
The payment is transitioned to cancelled and cannot be completed or cancelled again.

Constraints and invariants:
- Payment must be unfinished and cancellable.
- Agreement-mode payments do not receive cancel links in the response model.
- Connector owns the state-transition rules.

Failure and exceptional cases:
- Failing function: `create web card payment`
  - Failure condition: Invalid setup request.
  - Why it fails: Local parser/validator or Connector rejects payment creation.
  - Violated prerequisite or constraint: A cancellable payment cannot be established.
- Failing function: `cancel payment`
  - Failure condition: Unknown or cross-account `paymentId`.
  - Why it fails: Connector returns 404, mapped to cancel-payment not found.
  - Violated prerequisite or constraint: Payment must exist and be account-scoped.
- Failing function: `cancel payment`
  - Failure condition: Payment is already cancelled, finished, or otherwise not cancellable.
  - Why it fails: Connector returns 400 or 409, mapped by `CancelChargeExceptionMapper`.
  - Violated prerequisite or constraint: Payment must be in a cancellable state.

Implementation notes:
The resource returns the Connector response for cancellation directly.

### Behavior 13: Capture a delayed MOTO API payment

Business goal:
Authorise a payment first, then capture funds later.

Domain context:
Delayed capture supports service-side checks before money is actually taken.

Starting point:
No prior payment state, with MOTO API authorisation enabled.

Required execution workflow:
1. Use function `create MOTO API payment` (`POST /v1/payments`) with body `amount=12000`, `reference=PAY-DELAY-001`, `description=Delayed capture payment`, `authorisation_mode=moto_api`, and `delayed_capture=true`.
2. Capture `payment_id` and `auth_url_post.params.one_time_token`.
3. Use function `authorise MOTO API payment` (`POST /v1/auth`) with body `one_time_token={token from step 2}`, `card_number=4242424242424242`, `cardholder_name=J Citizen`, `cvc=123`, and `expiry_date=12/30`.
4. Use function `capture delayed payment` (`POST /v1/payments/{paymentId}/capture`) with `paymentId={payment_id from step 2}`.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={same payment_id}` to inspect capture state.
2. Use function `get payment events` (`GET /v1/payments/{paymentId}/events`) with the same `paymentId` to inspect authorisation and capture events.

Existing-state shortcuts:
- If a delayed-capture payment already exists and is authorised awaiting capture, skip steps 1 to 3.
- Direct Connector setup must create the payment with `delayed_capture=true`, account ownership, and capture-eligible state.

Parameter and value bindings:
- `one_time_token` from step 1 is consumed by step 3.
- `payment_id` from step 1 is consumed by capture and verification.
- `delayed_capture=true` creates the deferred capture semantics; without it, capture is not a valid later action.

Business result:
Funds are captured for the authorised delayed payment. On success, capture returns 204.

Constraints and invariants:
- Payment must be delayed-capture and in a Connector state that allows capture.
- Capturing a non-delayed, unauthorised, already-captured, or finished payment is rejected.

Failure and exceptional cases:
- Failing function: `create MOTO API payment`
  - Failure condition: Authorisation API disabled or invalid payment request.
  - Why it fails: Connector capability checks or local validation reject creation.
  - Violated prerequisite or constraint: A delayed MOTO API payment cannot be established.
- Failing function: `authorise MOTO API payment`
  - Failure condition: Invalid/reused token or rejected card data.
  - Why it fails: Local validation or Connector authorisation errors reject authorisation.
  - Violated prerequisite or constraint: Capture requires a successfully authorised payment.
- Failing function: `capture delayed payment`
  - Failure condition: Unknown payment id.
  - Why it fails: Connector returns 404, mapped to capture-payment not found.
  - Violated prerequisite or constraint: Payment must exist and be account-scoped.
- Failing function: `capture delayed payment`
  - Failure condition: Payment is not awaiting capture.
  - Why it fails: Connector returns 400 or 409, mapped by `CaptureChargeExceptionMapper`.
  - Violated prerequisite or constraint: Payment must be capture-eligible.

Implementation notes:
Public API only checks the Connector status. Any non-204 Connector response becomes `CaptureChargeException`.

### Behavior 14: Retrieve payment event history

Business goal:
Inspect the timeline of state changes for a payment.

Domain context:
Events explain how a payment moved through created, submitted, authorised, failed, cancelled, captured, or other lifecycle states.

Starting point:
No prior payment state.

Required execution workflow:
1. Use function `create web card payment` (`POST /v1/payments`) with body `amount=12000`, `reference=PAY-EVENT-001`, `description=Payment events test`, and `return_url=https://service.example/return/PAY-EVENT-001`.
2. Capture `payment_id`.
3. Use function `get payment events` (`GET /v1/payments/{paymentId}/events`) with `paymentId={payment_id from step 1}` and optional hidden header `X-Ledger=ledger-only` or `X-Ledger=connector-only`.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with the same `paymentId` to compare current state with the latest event.

Existing-state shortcuts:
- If a payment with event history already exists, skip creation and use its `payment_id`.
- Direct event setup in Ledger or Connector must match the selected read strategy.

Parameter and value bindings:
- `{paymentId}` binds the event list to one payment.
- Hidden `X-Ledger` strategy selects Ledger-only, Connector-only, or default behavior.

Business result:
No mutation beyond setup. The caller receives a list of payment state-change events.

Constraints and invariants:
- Payment must exist and be account-scoped.
- Invalid `X-Ledger` values are ignored and default strategy is used.

Failure and exceptional cases:
- Failing function: `create web card payment`
  - Failure condition: Invalid setup request.
  - Why it fails: Local or downstream validation rejects payment creation.
  - Violated prerequisite or constraint: No payment exists for event retrieval.
- Failing function: `get payment events`
  - Failure condition: Unknown or cross-account payment id.
  - Why it fails: Connector or Ledger returns not found.
  - Violated prerequisite or constraint: Event history requires an existing payment.

Implementation notes:
Default event retrieval can use Connector or Ledger depending on strategy implementation. Strategy selection is a hidden header, not public OpenAPI surface.

### Behavior 15: List refunds for a payment

Business goal:
Inspect all refunds associated with a payment.

Domain context:
Refund listing helps determine whether a payment has been partially, fully, or not refunded.

Starting point:
No prior payment state.

Required execution workflow:
1. Use function `create web card payment` (`POST /v1/payments`) with body `amount=12000`, `reference=PAY-REFLIST-001`, `description=Payment refund listing`, and `return_url=https://service.example/return/PAY-REFLIST-001`.
2. Capture `payment_id`.
3. Use function `list payment refunds` (`GET /v1/payments/{paymentId}/refunds`) with `paymentId={payment_id from step 1}` and optional hidden header `X-Ledger=ledger-only` or `X-Ledger=connector-only`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If a payment already exists, skip payment creation and list refunds for that `payment_id`.
- Direct Ledger or Connector setup can create refund rows under the payment, but the parent-child relationship and account scope must match.

Parameter and value bindings:
- `{paymentId}` scopes refunds to one parent payment.
- Hidden `X-Ledger` strategy controls whether refunds are read from Ledger or Connector; default is Ledger-only.

Business result:
No mutation beyond setup. The response returns zero or more refunds for the parent payment.

Constraints and invariants:
- Parent payment must exist and be account-scoped.
- Refunds are child resources; a refund from another payment must not appear under this payment id.

Failure and exceptional cases:
- Failing function: `create web card payment`
  - Failure condition: Invalid setup request.
  - Why it fails: Local or Connector validation rejects creation.
  - Violated prerequisite or constraint: A parent payment is required.
- Failing function: `list payment refunds`
  - Failure condition: Unknown or cross-account parent payment id.
  - Why it fails: Ledger or Connector returns not found.
  - Violated prerequisite or constraint: Refund listing requires an existing parent payment.

Implementation notes:
The default strategy for refund listing is Ledger-only, unlike payment retrieval, which defaults to Connector with Ledger fallback.

### Behavior 16: Refund a successful MOTO API payment

Business goal:
Return all or part of a successful payment amount to the payer.

Domain context:
Refunding is a core post-payment operation for corrections, cancellations, or service decisions.

Starting point:
No prior payment state, with MOTO API authorisation enabled.

Required execution workflow:
1. Use function `create MOTO API payment` (`POST /v1/payments`) with body `amount=12000`, `reference=PAY-REFUND-001`, `description=Refundable payment`, and `authorisation_mode=moto_api`.
2. Capture `payment_id` and `auth_url_post.params.one_time_token`.
3. Use function `authorise MOTO API payment` (`POST /v1/auth`) with body `one_time_token={token from step 2}`, `card_number=4242424242424242`, `cardholder_name=J Citizen`, `cvc=123`, and `expiry_date=12/30`.
4. Use function `refund payment` (`POST /v1/payments/{paymentId}/refunds`) with `paymentId={payment_id from step 2}` and body `amount=5000`; optionally include `refund_amount_available=12000` if the caller wants optimistic concurrency protection.
5. Capture `refund_id` from the refund response.

Optional verification workflow:
1. Use function `get refund` (`GET /v1/payments/{paymentId}/refunds/{refundId}`) with `paymentId={payment_id from step 2}` and `refundId={refund_id from step 4}`.
2. Use function `list payment refunds` (`GET /v1/payments/{paymentId}/refunds`) with `paymentId={same payment_id}`.
3. Use function `search refunds` (`GET /v1/refunds`) with date filters covering the refund creation time.

Existing-state shortcuts:
- If a successful refundable payment already exists, skip steps 1 to 3 and refund that `payment_id`.
- Direct Connector setup must create a successful payment with sufficient refundable amount and no dispute block.
- If `refund_amount_available` is omitted, Public API reads the payment first and sends the current available amount it sees to Connector.

Parameter and value bindings:
- `payment_id` from payment creation is reused as `{paymentId}` in refund creation.
- `amount` in refund creation is the amount to refund, not the original payment amount.
- Optional `refund_amount_available` must match Connector’s current available amount for that payment.
- Generated `refund_id` is reused by `get refund`.

Business result:
A refund transaction is accepted for the payment. The payment’s available refundable amount is reduced by the refund amount if Connector accepts the refund.

Constraints and invariants:
- Refund `amount` is required and must be 1 to 10,000,000.
- The payment must be successful and have enough refundable amount.
- Refunds can be blocked by disputes.
- The refund is accepted with HTTP 202, not necessarily completed synchronously.

Failure and exceptional cases:
- Failing function: `create MOTO API payment`
  - Failure condition: Invalid setup request or account not enabled for MOTO API.
  - Why it fails: Local/Connector validation rejects creation.
  - Violated prerequisite or constraint: A successful refundable payment cannot be created.
- Failing function: `authorise MOTO API payment`
  - Failure condition: Invalid token or declined card.
  - Why it fails: Connector rejects authorisation.
  - Violated prerequisite or constraint: Refund requires successful payment state.
- Failing function: `refund payment`
  - Failure condition: Unknown payment id.
  - Why it fails: Connector returns 404, mapped to refund not found.
  - Violated prerequisite or constraint: Refund must target an existing account-scoped payment.
- Failing function: `refund payment`
  - Failure condition: Invalid amount, no refundable balance, `refund_amount_available` mismatch, or dispute block.
  - Why it fails: Local parsing rejects missing/nonnumeric amount, or Connector returns refund availability errors.
  - Violated prerequisite or constraint: Refund amount and payment state must be valid.

Implementation notes:
When `refund_amount_available` is omitted, `CreateRefundService` reads the payment to derive it and then sends both `amount` and `refund_amount_available` to Connector. This is useful but not fully atomic against concurrent refunds.

### Behavior 17: Retrieve one refund

Business goal:
Check the status and details of a specific refund under a payment.

Domain context:
A service needs to reconcile an accepted refund with later refund processing state.

Starting point:
No prior payment or refund state.

Required execution workflow:
1. Use function `create MOTO API payment` (`POST /v1/payments`) with body `amount=12000`, `reference=PAY-GETREF-001`, `description=Payment for refund lookup`, and `authorisation_mode=moto_api`.
2. Capture `payment_id` and `auth_url_post.params.one_time_token`.
3. Use function `authorise MOTO API payment` (`POST /v1/auth`) with body `one_time_token={token from step 2}`, `card_number=4242424242424242`, `cardholder_name=J Citizen`, `cvc=123`, and `expiry_date=12/30`.
4. Use function `refund payment` (`POST /v1/payments/{paymentId}/refunds`) with `paymentId={payment_id from step 2}` and body `amount=5000`.
5. Capture `refund_id`.
6. Use function `get refund` (`GET /v1/payments/{paymentId}/refunds/{refundId}`) with `paymentId={payment_id from step 2}` and `refundId={refund_id from step 5}`.

Optional verification workflow:
1. Use function `list payment refunds` (`GET /v1/payments/{paymentId}/refunds`) with `paymentId={same payment_id}` to verify the refund appears in the parent list.

Existing-state shortcuts:
- If a payment and linked refund already exist, skip setup and retrieve using their ids.
- Direct setup must preserve the parent-child relationship: `refund_id` must belong to the specified `payment_id` and account.

Parameter and value bindings:
- `payment_id` scopes the refund lookup.
- `refund_id` generated by `refund payment` is consumed by `get refund`.
- A refund id from another payment is not equivalent, even if it exists.

Business result:
No mutation beyond setup. The caller receives one refund resource for the parent payment.

Constraints and invariants:
- Refund must exist, belong to the specified payment, and be account-scoped.
- Hidden `X-Ledger` strategy may select Ledger or Connector reads.

Failure and exceptional cases:
- Failing function: `refund payment`
  - Failure condition: Payment is not refundable or refund amount invalid.
  - Why it fails: Connector rejects refund creation.
  - Violated prerequisite or constraint: A refund id cannot be established.
- Failing function: `get refund`
  - Failure condition: Unknown refund id, refund belongs to another payment, or cross-account id.
  - Why it fails: Connector or Ledger returns not found.
  - Violated prerequisite or constraint: Refund id must be linked to the supplied parent payment id.

Implementation notes:
Default strategy tries `GetPaymentRefundService.getPaymentRefund`, while hidden strategies can force Ledger or Connector.

### Behavior 18: Search refunds

Business goal:
Find refunds across payments for reporting and reconciliation.

Domain context:
Finance and support workflows often need refund lists independent of a single payment.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `search refunds` (`GET /v1/refunds`) with optional query values `from_date=2026-01-01T00:00:00Z`, `to_date=2026-02-01T00:00:00Z`, `from_settled_date=2026-01-02`, `to_settled_date=2026-02-02`, `page=1`, and `display_size=100`.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup is required. Direct Ledger setup can create refund rows if a non-empty search result is needed.
- Seeded refunds must belong to the authenticated account.

Parameter and value bindings:
- Date filters constrain refund creation or settlement dates.
- Pagination values shape the result page only.

Business result:
No state changes. The caller receives a paged Ledger view of refunds.

Constraints and invariants:
- Date-time filters must be valid ISO-8601 UTC date-times.
- Settled-date filters must be valid ISO local dates.
- `page` must be at least 1 and `display_size` must be 1 to 500.

Failure and exceptional cases:
- Failing function: `search refunds`
  - Failure condition: Malformed dates, invalid pagination, or `display_size=501`.
  - Why it fails: `RefundSearchValidator` rejects invalid query values.
  - Violated prerequisite or constraint: Refund search filters must satisfy local validators.
- Failing function: `search refunds`
  - Failure condition: Ledger search fails or returns malformed data.
  - Why it fails: `SearchRefundsService`/`LedgerService.searchRefunds` maps downstream failure.
  - Violated prerequisite or constraint: Ledger must serve refund search.

Implementation notes:
Refund search supports date and pagination filters only. It does not support direct filtering by `payment_id`, `refund_id`, refund status, or payment reference.

### Behavior 19: Search disputes

Business goal:
Find payment disputes for operational follow-up and reporting.

Domain context:
A dispute represents a payer challenge raised through a bank or payment provider.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `search disputes` (`GET /v1/disputes`) with optional query values `from_date=2026-01-01T00:00:00Z`, `to_date=2026-02-01T00:00:00Z`, `from_settled_date=2026-01-02`, `to_settled_date=2026-02-02`, `status=needs_response`, `page=1`, and `display_size=100`.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup is required. Direct Ledger setup can create dispute rows if non-empty results are needed.
- Seeded disputes must belong to the authenticated account.

Parameter and value bindings:
- Public `status` is mapped to Ledger `state`.
- Date and pagination filters constrain the Ledger dispute search.

Business result:
No state changes. The caller receives a paged view of disputes.

Constraints and invariants:
- `status` must be `needs_response`, `under_review`, `lost`, or `won`.
- Date-time and settled-date formats are validated locally.
- `page` and `display_size` follow the same pagination constraints as other searches.

Failure and exceptional cases:
- Failing function: `search disputes`
  - Failure condition: Unsupported status, malformed date, invalid page, or overlarge display size.
  - Why it fails: `DisputeSearchValidator` rejects invalid query values.
  - Violated prerequisite or constraint: Dispute search filters must satisfy local validators.
- Failing function: `search disputes`
  - Failure condition: Ledger search fails or returns malformed data.
  - Why it fails: `LedgerService.searchDisputes` maps downstream failure.
  - Violated prerequisite or constraint: Ledger must serve dispute search.

Implementation notes:
Implementation rewrites `status` to Ledger `state`. There is no public function in `full-behavior.md` for creating, retrieving, updating, or responding to an individual dispute.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Complete a hosted web payment entirely through Public API

Priority:
Critical domain gap

Expected business goal:
Create a standard web payment and complete it by submitting payer card details or a hosted-payment completion decision through API calls.

Why it is unsupported:
The service can create a hosted web payment but has no public function for entering card details or completing the hosted web journey.

Existing functions considered:
- `create web card payment`: creates the payment and hosted journey links, but does not complete the payment.
- `authorise MOTO API payment`: only works with `one_time_token` from `authorisation_mode=moto_api`, not standard web payments.
- `get payment`: can inspect state but cannot transition it.

Missing capability:
A web-payment authorisation/completion endpoint or API-supported hosted journey simulation.

Proof that function composition is insufficient:
Chaining create, get, search, and events can observe a web payment but cannot submit card details for web mode or advance the hosted payment state. `POST /v1/auth` requires a MOTO API one-time token, which a web payment does not provide.

Evidence from existing functions/source:
`create web card payment` returns links for a web journey. `authorise MOTO API payment` is implemented separately and posts only token-bound card details to Connector.

Business impact:
A complete API-only workflow cannot demonstrate successful web payment, web setup-agreement activation, refunding a web payment, or cancelling after hosted completion without external journey execution or direct backing-store setup.

### Missing Behavior 2: Activate a recurring agreement entirely through Public API

Priority:
Critical domain gap

Expected business goal:
Create an agreement, attach a payer instrument, activate the agreement, then take recurring payments using only Public API functions.

Why it is unsupported:
The API can create an agreement and create a setup payment, but it cannot complete the hosted setup payment journey that saves the instrument and activates the agreement.

Existing functions considered:
- `create agreement`: creates the agreement shell.
- `create setup-agreement payment`: links a payment to the agreement for future instrument saving.
- `take recurring payment`: requires the agreement to already be active.
- `cancel agreement`: also requires active state.

Missing capability:
An API function to complete setup payment authorisation for agreement activation, or a direct instrument attachment/activation endpoint.

Proof that function composition is insufficient:
`create setup-agreement payment` creates only the setup payment. Without external completion, there is no available function that changes agreement status to active or creates the saved payment instrument. `take recurring payment` consumes active agreement state but cannot create it.

Evidence from existing functions/source:
The setup function maps `set_up_agreement` to Connector `agreement_id` plus `save_payment_instrument_to_agreement=true`; activation is downstream of payment completion, not a Public API function.

Business impact:
Recurring-payment lifecycle tests and integrations need external hosted payment completion or direct database/Connector setup before recurring charge and cancellation behaviors can be exercised.

### Missing Behavior 3: Manage dispute lifecycle or retrieve one dispute

Priority:
Critical domain gap

Expected business goal:
Retrieve a dispute by id, inspect evidence requirements, submit evidence, accept a dispute, or track a single dispute’s lifecycle.

Why it is unsupported:
The only dispute function is collection search.

Existing functions considered:
- `search disputes`: can find disputes by date, settlement date, status, and pagination.
- `get payment`: may show payment state but does not expose dispute detail or actions.
- `refund payment`: can be blocked by a dispute but cannot manage it.

Missing capability:
Dispute create/import, retrieve-by-id, evidence submission, status transition, or payment-linked dispute lookup endpoints.

Proof that function composition is insufficient:
Search can return a list, but there is no path containing a dispute id and no function that mutates dispute state. Chaining payment and refund functions cannot create evidence, accept liability, or move a dispute through provider workflow.

Evidence from existing functions/source:
`SearchDisputesResource` only implements `GET /v1/disputes`. No dispute detail or mutation resource appears in `full-behavior.md` or the public OpenAPI path list.

Business impact:
Dispute operations are read-only and coarse-grained. Services cannot build a complete dispute management workflow through this API.

### Missing Behavior 4: Update agreement or payment business details after creation

Priority:
Important robustness gap

Expected business goal:
Correct or update agreement/payment `reference`, `description`, metadata, user identifier, email, or return URL after creation.

Why it is unsupported:
The API exposes create, read, search, cancel, capture, refund, and authorise operations, but no update or patch operations for agreements or payments.

Existing functions considered:
- `create agreement`: creates initial agreement values only.
- `create web card payment`: creates initial payment values only.
- `get agreement` and `get payment`: read values but cannot change them.
- `search agreements` and `search payments`: can find records but cannot update them.

Missing capability:
PATCH/PUT endpoints for agreement and payment mutable business fields.

Proof that function composition is insufficient:
Delete-and-recreate is not equivalent. A recreated payment or agreement receives a new generated id, loses existing links/events/refunds/agreement activation state, and cannot preserve downstream processor relationships.

Evidence from existing functions/source:
No public resource class exposes PUT/PATCH for payments or agreements. Request models are create-only.

Business impact:
Mistyped references/descriptions and metadata cannot be corrected through Public API, weakening reconciliation and customer-support workflows.

### Missing Behavior 5: Safely refund with fully atomic available-amount protection

Priority:
Important robustness gap

Expected business goal:
Create a refund only if the available refundable amount has not changed, with the read and refund decision protected as one atomic operation.

Why it is unsupported:
The refund function supports `refund_amount_available`, but when the caller omits it, Public API reads the payment first and then sends the derived value to Connector in a separate operation.

Existing functions considered:
- `refund payment`: can send explicit or derived `refund_amount_available`.
- `get payment`: can read refund summary before refunding.
- `list payment refunds`: can inspect existing refunds.

Missing capability:
A single public operation that atomically reads current availability and creates the refund, or a caller-visible version/ETag/compare-and-set token for refundability.

Proof that function composition is insufficient:
A composition of `get payment` then `refund payment` has a race window. Another refund can change available amount between the read and refund request. Explicit `refund_amount_available` detects mismatch but does not remove the race; omitted value hides the read but still performs two downstream steps.

Evidence from existing functions/source:
`CreateRefundService` calls `GetOnePaymentStrategy` to derive `refund_amount_available` when omitted, then posts to Connector.

Business impact:
Concurrent refund workflows can fail unexpectedly with precondition mismatch or require client-side retry logic.

### Missing Behavior 6: Search refunds by business identifiers

Priority:
API ergonomics gap

Expected business goal:
Find refunds by `payment_id`, `refund_id`, payment reference, refund status, or amount.

Why it is unsupported:
Refund search only accepts date, settled-date, page, and display-size filters.

Existing functions considered:
- `search refunds`: searches by dates and pagination only.
- `list payment refunds`: lists refunds for a known `payment_id`, but does not help discover that id.
- `get refund`: requires both `payment_id` and `refund_id`.

Missing capability:
Additional refund search filters or a direct refund lookup endpoint independent of parent payment id.

Proof that function composition is insufficient:
If the caller does not already know `payment_id`, listing by payment cannot be used. If the caller knows only `refund_id`, `get refund` cannot be called because it also requires the parent payment id.

Evidence from existing functions/source:
`SearchRefundsResource` builds `RefundsParams` from only date, settled-date, page, and display-size query values.

Business impact:
Refund reconciliation by external reference or refund id requires out-of-band storage or broad date searches.

### Missing Behavior 7: Retrieve or search agreements by user identifier

Priority:
API ergonomics gap

Expected business goal:
Find all agreements associated with a service-side user identifier.

Why it is unsupported:
Agreement creation accepts `user_identifier`, but agreement search does not support it.

Existing functions considered:
- `create agreement`: stores optional `user_identifier`.
- `search agreements`: supports only `reference`, `status`, `page`, and `display_size`.
- `get agreement`: requires generated `agreement_id`.

Missing capability:
`user_identifier` filter on agreement search or a dedicated user-agreement lookup endpoint.

Proof that function composition is insufficient:
If the service has a user id but not the generated agreement id or exact reference, existing functions cannot directly query agreements for that user. Searching all agreements and filtering client-side is incomplete and inefficient at scale.

Evidence from existing functions/source:
`AgreementSearchValidator` rejects unsupported nonblank query parameters and defines supported search params as `reference`, `status`, `page`, and `display_size`.

Business impact:
Services must maintain their own mapping from user identifiers to agreement ids.

### Missing Behavior 8: Publicly documented telephone-payment notification and transaction search behavior

Priority:
API ergonomics gap

Expected business goal:
Use all deployed resource capabilities through the public API contract and function-level behavior document.

Why it is unsupported:
Source registers `/v1/payment_notification` and `/v1/transactions`, but they are absent from `full-behavior.md` and the public `pay-publicapi.json` path inventory used for this analysis.

Existing functions considered:
- Functions in `full-behavior.md`: none cover telephone payment notification or generic transaction search.
- `search payments`: covers payment transaction search through `/v1/payments`, but not the separate `/v1/transactions` resource.
- `create web card payment` and `create MOTO API payment`: do not create telephone payment notifications.

Missing capability:
Publicly documented function definitions and OpenAPI paths for these implementation resources, or intentional removal if they are not public API surface.

Proof that function composition is insufficient:
No listed function has the `/v1/payment_notification` endpoint or telephone-specific request fields such as `processor_id`, `provider_id`, and `payment_outcome`. Payment creation functions create card payments through Connector’s charge endpoint, not telephone-charge notifications.

Evidence from existing functions/source:
`TelephonePaymentNotificationResource` implements `POST /v1/payment_notification`, and `TransactionsResource` implements `GET /v1/transactions`; neither appears in `full-behavior.md` or `pay-publicapi.json`.

Business impact:
There is ambiguity about whether these resources are supported public business behavior, internal compatibility surface, or undocumented API.

## Cross-Behavior Observations

- Public API is primarily a façade: Connector performs most mutations, while Ledger powers searches and several read views.
- Account ownership is enforced through downstream account-scoped Connector/Ledger URLs and authenticated account ids rather than local ownership checks.
- Agreement and payment references are not unique. Idempotency exists for payment creation only, not agreement creation or refund creation.
- Several complete business lifecycles require state outside the listed Public API functions, especially hosted web payment completion and agreement activation.
- Hidden `X-Ledger` read strategy affects payment, refund, and event reads; invalid values silently fall back to default.
- OpenAPI and implementation disagree on required fields: implementation requires payment `amount`, `reference`, and `description`, and agreement `reference` and `description`; the schema does not consistently mark them required. OpenAPI marks payment `return_url` required, but source parsing does not require it for all modes.
- `src/main/resources/assets/swagger.json` is narrower than `pay-publicapi.json`; it omits agreements, authorisation, and disputes.
- Source includes additional resources for telephone payment notification and generic transactions that are not included in `full-behavior.md` or the public OpenAPI path list used here.
- Search validators are strict about date, pagination, status, and card digit formats, but search capabilities are uneven across domain resources.
- Refund creation without caller-supplied `refund_amount_available` performs a read-before-write convenience flow, but that does not make refund availability checks atomic.

## Coverage Summary

Supported domain areas:
Payment creation, MOTO API authorisation, payment retrieval, payment search, cancellation, delayed capture, payment events, agreement creation/search/read/cancel, setup-payment initiation for agreements, recurring payment creation from active agreements, refund creation/read/list/search, and dispute search.

Partially supported domain areas:
Recurring-payment lifecycle, hosted web payment lifecycle, refund concurrency protection, and operational reporting. These work for important slices but depend on external hosted journeys, direct backing-store setup, downstream state, or limited search filters.

Unsupported domain areas:
API-only hosted payment completion, API-only agreement activation, dispute lifecycle management, post-creation updates to payment/agreement business data, direct refund lookup without parent payment id, agreement lookup by user identifier, and publicly documented use of implementation-only telephone/transaction resources.