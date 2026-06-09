# Domain-Level Workflow and State Behavior Analysis

| No. | Behavior name | Business goal |
|---:|---|---|
| 1 | [Behavior 1: Search recurring agreements](#behavior-1) | Find agreements for an authenticated service account. |
| 2 | [Behavior 2: Create a recurring agreement](#behavior-2) | Create a reusable agreement for future recurring payments. |
| 3 | [Behavior 3: Retrieve one recurring agreement](#behavior-3) | Inspect the current details of a known agreement. |
| 4 | [Behavior 4: Initiate an agreement setup payment](#behavior-4) | Create a payment that can attach a payment instrument to an agreement. |
| 5 | [Behavior 5: Cancel an active recurring agreement](#behavior-5) | Move an active recurring agreement to cancelled. |
| 6 | [Behavior 6: Search payments](#behavior-6) | Find payments by lifecycle, reference, card, date, settlement, or agreement filters. |
| 7 | [Behavior 7: Create a web card payment](#behavior-7) | Start a standard hosted GOV.UK Pay card payment journey. |
| 8 | [Behavior 8: Replay idempotent payment creation](#behavior-8) | Return an existing payment instead of creating a duplicate. |
| 9 | [Behavior 9: Authorise a MOTO API payment](#behavior-9) | Create an API-authorised MOTO payment and submit card details. |
| 10 | [Behavior 10: Take a recurring payment from an active agreement](#behavior-10) | Charge a saved payment instrument linked to an active agreement. |
| 11 | [Behavior 11: Retrieve one payment](#behavior-11) | Inspect one payment by generated payment id. |
| 12 | [Behavior 12: Cancel an unfinished payment](#behavior-12) | Move an unfinished non-agreement payment to cancelled. |
| 13 | [Behavior 13: Capture a delayed MOTO API payment](#behavior-13) | Capture a delayed payment after successful authorisation. |
| 14 | [Behavior 14: Read payment event history](#behavior-14) | Inspect state-change events for one payment. |
| 15 | [Behavior 15: Create a refund for a successful payment](#behavior-15) | Submit a full or partial refund for a refundable payment. |
| 16 | [Behavior 16: List refunds for a payment](#behavior-16) | Read refund records belonging to one payment. |
| 17 | [Behavior 17: Retrieve one payment refund](#behavior-17) | Inspect one refund under its parent payment. |
| 18 | [Behavior 18: Search refunds](#behavior-18) | Find refunds across payments by date, settlement date, and pagination. |
| 19 | [Behavior 19: Search disputes](#behavior-19) | Find dispute records reported for the authenticated account. |

## Domain Summary

This service is the public GOV.UK Pay card-payment API. Its main aggregate roots are the authenticated gateway account, `paymentId`, `agreementId`, and `refundId`. Payments are the central lifecycle resource, with state transitions for creation, hosted or API authorisation, cancellation, delayed capture, refunding, and event-history reporting. Agreements model recurring-payment consent and payment-instrument reuse. Refunds are child transactions under payments and are also searchable across the account. Disputes are Ledger-backed reporting records tied to parent payments.

The implementation is mostly a façade. Connector owns mutation-heavy state transitions such as create, cancel, capture, authorise, agreement cancel, and refund creation. Ledger owns search, agreement reads, transaction/event views, and some fallback reads. Public API adds request parsing, validation, account scoping, id/link binding, idempotency header forwarding, response-link construction, and exception mapping.

## Supported Business Behaviors

<a id="behavior-1"></a>
### Behavior 1: Search recurring agreements

Business goal:
Find recurring-payment agreements belonging to the authenticated gateway account.

API group boundary:
This is atomic. The single function is a domain lookup over the agreement aggregate collection, scoped by gateway account and optional agreement search filters.

Domain context:
Services need to locate agreements by business reference or status before setup, cancellation, reconciliation, or recurring charging.

Starting point:
`No prior service state`

State transition summary:
- State before: Agreement records may or may not exist in Ledger.
- Transition trigger: A search request is submitted.
- Intermediate states: None.
- State after: Agreement state is unchanged.
- Invalid or blocked transitions: Invalid filters or unavailable Ledger state block the lookup.

Required execution workflow:
1. Use function `search agreements` (`GET /v1/agreements`) with authenticated Bearer caller context for `accountId={gateway account id}`, optional query `reference={exact reference}`, `status={created|active|cancelled|inactive}`, `page={integer >= 1}`, and `display_size={1..500}` to return matching agreements.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup step is required.
- Connector/Ledger backing-store seeding can make the response non-empty, but every returned agreement must still be scoped to the same gateway account.
- The lookup action itself cannot be skipped.

Parameter and value bindings:
- The authenticated account is converted to Ledger `gateway_account_id`.
- `reference` is sent with exact-match semantics.
- `status`, `page`, and `display_size` shape only the result set and pagination links.

Business result:
No state changes. The caller receives a paginated account-scoped list of agreements, possibly empty.

Constraints and invariants:
- Unknown nonblank agreement search query parameters are rejected.
- `status` must match the supported agreement statuses.
- `reference` is limited to 255 characters.
- Pagination is bounded to `page >= 1` and `display_size <= 500`.

Failure and exceptional cases:
- Failing function: `search agreements`
  - Failure condition: Unsupported status, invalid page, overlarge display size, overlong reference, or unsupported nonblank query parameter.
  - Why it fails: `AgreementSearchValidator` validates query names and values before Ledger is called.
  - Violated prerequisite or constraint: Agreement search filters must use supported names and valid values.
- Failing function: `search agreements`
  - Failure condition: Ledger returns a non-200 response or malformed agreement search payload.
  - Why it fails: `LedgerService.searchAgreements` maps downstream failure to the search-agreement exception path.
  - Violated prerequisite or constraint: Ledger must be available and able to return agreement search results.

Implementation notes:
Agreement search reads from Ledger only. Public API adds `gateway_account_id` and exact reference matching before calling Ledger.

<a id="behavior-2"></a>
### Behavior 2: Create a recurring agreement

Business goal:
Create a recurring-payment agreement that can later be activated and charged.

API group boundary:
This is atomic. The single function creates the agreement lifecycle resource and returns the generated agreement id.

Domain context:
An agreement records the service’s business reference, description, and optional user identifier for future recurring payments. It is not itself a charge.

Starting point:
`No prior service state`

State transition summary:
- State before: No agreement exists for the submitted business values.
- Transition trigger: A create-agreement request is accepted.
- Intermediate states: Connector creates the agreement, then Public API reads the new agreement from Ledger.
- State after: A new agreement exists, normally in an initial non-active state until setup completes.
- Invalid or blocked transitions: Invalid request body, disabled recurring-payment capability, or failed post-create Ledger read blocks a clean public result.

Required execution workflow:
1. Use function `create agreement` (`POST /v1/agreements`) with authenticated Bearer caller context for `accountId={gateway account id}` and JSON body `reference={nonblank string}`, `description={nonblank string}`, and optional `user_identifier={string}` to create the agreement and capture `agreement_id={generated agreement id}` from the response.

Optional verification workflow:
1. Use function `get agreement` (`GET /v1/agreements/{agreementId}`) with `agreementId={agreement_id from create agreement}` and the same authenticated account to inspect the created agreement.
2. Use function `search agreements` (`GET /v1/agreements`) with `reference={reference from create agreement}` and the same authenticated account to confirm it appears in search.

Existing-state shortcuts:
- For later behaviors, direct Connector/Ledger setup can replace this creation step if it produces an agreement in the same account scope.
- The generated `agreement_id` must remain bound to the same gateway account and agreement record.
- The create action is the core behavior and cannot be skipped for this behavior.

Parameter and value bindings:
- `reference`, `description`, and optional `user_identifier` are sent to Connector.
- Connector returns `agreement_id`; Public API immediately reuses that id to read the agreement from Ledger.
- The same `agreement_id` is later reusable as `{agreementId}`, `set_up_agreement`, or `agreement_id` in other behaviors.

Business result:
A new recurring agreement exists for the authenticated account. The response is `201` only after the Connector create and follow-up Ledger read both succeed.

Constraints and invariants:
- `reference` and `description` must be nonblank strings, despite the public OpenAPI schema not marking them as required.
- `user_identifier`, when present, must be a string of valid length.
- The account must be enabled in Connector for recurring card payments.
- Creation is not idempotent by public API contract.

Failure and exceptional cases:
- Failing function: `create agreement`
  - Failure condition: Missing, blank, malformed, or non-string `reference` or `description`.
  - Why it fails: `RequestJsonParser.parseAgreementRequest` requires nonblank string values.
  - Violated prerequisite or constraint: Agreement creation requires valid reference and description.
- Failing function: `create agreement`
  - Failure condition: Connector reports recurring card payments are not allowed for the account.
  - Why it fails: Connector capability errors are mapped by `CreateAgreementExceptionMapper`.
  - Violated prerequisite or constraint: The gateway account must support recurring card payments.
- Failing function: `create agreement`
  - Failure condition: Connector creates the agreement, but the immediate Ledger read fails.
  - Why it fails: The resource creates in Connector and then calls `getAgreement` from Ledger to build the response.
  - Violated prerequisite or constraint: Post-create Ledger consistency and availability are required for a clean public response.

Implementation notes:
This behavior has a partial-success risk: Connector may persist the agreement before Public API fails while reading it from Ledger.

<a id="behavior-3"></a>
### Behavior 3: Retrieve one recurring agreement

Business goal:
Inspect the current details and status of a known agreement.

API group boundary:
This behavior binds the generated `agreement_id` returned by agreement creation to a later read of the same agreement aggregate.

Domain context:
A service must often confirm whether an agreement exists and what status Ledger reports before charging or cancelling it.

Starting point:
`No prior service state`

State transition summary:
- State before: No agreement exists for the setup path.
- Transition trigger: Agreement creation generates an id, then the read endpoint retrieves it.
- Intermediate states: The agreement is created before being read.
- State after: Agreement state is unchanged by the read.
- Invalid or blocked transitions: Unknown, cross-account, or non-Ledger-visible ids cannot be retrieved.

Required execution workflow:
1. Use function `create agreement` (`POST /v1/agreements`) with authenticated Bearer caller context for `accountId={gateway account id}` and body `reference={nonblank string}` and `description={nonblank string}` to create an agreement and capture `agreement_id={generated agreement id}`.
2. Use function `get agreement` (`GET /v1/agreements/{agreementId}`) with the same authenticated account and path `agreementId={agreement_id from create agreement}` to retrieve the agreement.

Optional verification workflow:
None.

Existing-state shortcuts:
- Skip `create agreement` if an equivalent agreement already exists.
- The existing `agreement_id` must belong to the same gateway account and be visible in Ledger.
- Direct Ledger setup can seed the read state, but ownership and id binding must still hold.

Parameter and value bindings:
- The `agreement_id` generated by `create agreement` is reused exactly as `{agreementId}`.
- Account scoping is implicit through the Bearer API key and Ledger request.

Business result:
The caller receives one account-scoped agreement resource. No state is changed after setup creation.

Constraints and invariants:
- The agreement id must exist in Ledger for the authenticated account.
- There is no direct public read by `reference`; callers must use search for reference lookup.

Failure and exceptional cases:
- Failing function: `create agreement`
  - Failure condition: Invalid body or recurring-payment capability disabled.
  - Why it fails: Local request parsing or Connector capability checks reject setup.
  - Violated prerequisite or constraint: A retrievable agreement cannot be created.
- Failing function: `get agreement`
  - Failure condition: `agreementId` is unknown, not in Ledger, or belongs to another account.
  - Why it fails: Ledger returns not found and Public API maps it to the get-agreement not-found error.
  - Violated prerequisite or constraint: The agreement id must be real and account-scoped.

Implementation notes:
`get agreement` reads from Ledger with `X-Consistent=true`, while creation still depends on Connector first and Ledger second.

<a id="behavior-4"></a>
### Behavior 4: Initiate an agreement setup payment

Business goal:
Create a hosted payment that can save a payer’s payment instrument to an agreement after successful completion.

API group boundary:
This behavior uses response-to-request binding: `create agreement` generates `agreement_id`, and `create setup-agreement payment` consumes that id as `set_up_agreement`.

Domain context:
Recurring charging requires an active agreement with a saved payment instrument. Public API can initiate the setup payment; the payer’s hosted journey completes the activation outside this API.

Starting point:
`No prior service state`

State transition summary:
- State before: No agreement or setup payment exists.
- Transition trigger: Agreement creation followed by payment creation with `set_up_agreement`.
- Intermediate states: Agreement is created; payment is created and linked to the agreement with save-instrument intent.
- State after: A setup payment exists; the agreement is associated with that setup attempt but is not active until external payment completion.
- Invalid or blocked transitions: Unknown agreement id, invalid payment body, or disabled account capabilities block setup initiation.

Required execution workflow:
1. Use function `create agreement` (`POST /v1/agreements`) with authenticated Bearer caller context for `accountId={gateway account id}` and body `reference={agreement reference}` and `description={agreement description}` to create an agreement and capture `agreement_id={generated agreement id}`.
2. Use function `create setup-agreement payment` (`POST /v1/payments`) with the same authenticated account, body `amount={integer amount}`, `reference={payment reference}`, `description={payment description}`, `return_url={valid web return URL}`, and `set_up_agreement={agreement_id from create agreement}` to create the setup payment and capture `payment_id={generated payment id}`.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from create setup-agreement payment}` to inspect the setup payment.
2. Use function `get agreement` (`GET /v1/agreements/{agreementId}`) with `agreementId={agreement_id from create agreement}` to inspect agreement state.

Existing-state shortcuts:
- Skip `create agreement` if a same-account agreement already exists and its `agreement_id` is known.
- Direct Connector/Ledger setup can create an active agreement with a saved instrument for later charging, but that is stronger state than this setup-initiation behavior creates.
- The payment creation action with `set_up_agreement` cannot be skipped for this behavior.

Parameter and value bindings:
- `agreement_id` from `create agreement` is reused as `set_up_agreement`.
- Public API maps `set_up_agreement` to Connector `agreement_id` and `save_payment_instrument_to_agreement=true`.
- The created `payment_id` identifies the setup payment and is reused for optional payment reads.

Business result:
A web-journey setup payment exists and is linked to the agreement. The agreement is prepared for activation after the external hosted payment completes successfully.

Constraints and invariants:
- `set_up_agreement` must identify an agreement scoped to the same account.
- Payment `amount`, `reference`, and `description` are required by parser/validation.
- `return_url` is required for the hosted web setup journey.
- The API does not itself complete the payer journey or attach the instrument.

Failure and exceptional cases:
- Failing function: `create agreement`
  - Failure condition: Agreement request is invalid or recurring card payments are disabled.
  - Why it fails: Local parser and Connector capability checks reject agreement creation.
  - Violated prerequisite or constraint: Setup payment needs a valid agreement id.
- Failing function: `create setup-agreement payment`
  - Failure condition: `set_up_agreement` is unknown, malformed, cross-account, or not accepted by Connector.
  - Why it fails: Connector returns agreement-not-found or related validation error, mapped as an invalid `set_up_agreement`.
  - Violated prerequisite or constraint: Setup payment must reference an existing same-account agreement.
- Failing function: `create setup-agreement payment`
  - Failure condition: Payment body is invalid or the account cannot create payments.
  - Why it fails: Request parser, bean validation, or Connector policy rejects the payment.
  - Violated prerequisite or constraint: Payment creation requires valid amount, reference, description, return URL, and account capability.

Implementation notes:
This behavior initiates, but does not complete, agreement activation. External hosted-payment completion is required before `take recurring payment` or active-agreement cancellation can succeed.

<a id="behavior-5"></a>
### Behavior 5: Cancel an active recurring agreement

Business goal:
Stop future recurring charges by cancelling an active agreement.

API group boundary:
This is atomic at the public API level. The single function is the lifecycle transition for the agreement aggregate identified by `agreementId`.

Domain context:
A service must be able to stop using a saved payment instrument when a user cancels consent, ends a subscription, or no longer owes recurring payments.

Starting point:
`Pre-existing service/upstream state required`

State transition summary:
- State before: An agreement exists, belongs to the authenticated account, is active, and has completed setup upstream.
- Transition trigger: A cancel request is sent for that agreement id.
- Intermediate states: Connector processes the agreement cancellation.
- State after: The agreement is cancelled and should not be usable for new recurring payments.
- Invalid or blocked transitions: Unknown, cross-account, inactive, created, already-cancelled, or otherwise non-active agreements cannot be cancelled.

Required execution workflow:
1. Use function `cancel agreement` (`POST /v1/agreements/{agreementId}/cancel`) with authenticated Bearer caller context for `accountId={gateway account id}`, path `agreementId={pre-existing active agreement id}`, and upstream state `agreement status=active with saved payment instrument` to cancel the agreement.

Optional verification workflow:
1. Use function `get agreement` (`GET /v1/agreements/{agreementId}`) with `agreementId={agreement id used for cancellation}` to inspect the resulting status.

Existing-state shortcuts:
- The public API can create an agreement and initiate setup, but it cannot complete the hosted payment journey that makes the agreement active.
- Direct Connector/Ledger setup can provide the active agreement state.
- The `agreementId` must still belong to the authenticated account and refer to an active agreement; the cancellation action cannot be skipped.

Parameter and value bindings:
- `{agreementId}` is the agreement aggregate id.
- The Bearer API key scopes the cancellation to one gateway account.
- No body is required; the state transition is entirely identified by account plus `agreementId`.

Business result:
The active agreement becomes cancelled. Future agreement-mode payment creation for that agreement should be blocked by Connector.

Constraints and invariants:
- Only active agreements are cancellable.
- The implementation relies on Connector to enforce status and ownership.
- Public API does not pre-read the agreement before cancelling.

Failure and exceptional cases:
- Failing function: `cancel agreement`
  - Failure condition: `agreementId` is unknown or belongs to another account.
  - Why it fails: Connector returns not found and the mapper exposes a not-found cancellation error.
  - Violated prerequisite or constraint: The agreement must exist in the caller’s account scope.
- Failing function: `cancel agreement`
  - Failure condition: Agreement exists but is not active.
  - Why it fails: Connector rejects cancellation for non-active agreement state.
  - Violated prerequisite or constraint: Agreement cancellation requires active status.

Implementation notes:
The endpoint returns `204` on success. Invalid non-active transitions are mapped from Connector responses rather than independently evaluated in Public API.

<a id="behavior-6"></a>
### Behavior 6: Search payments

Business goal:
Find payments in the authenticated account by lifecycle state, business reference, card attributes, creation dates, settlement dates, or agreement id.

API group boundary:
This is atomic. The single function is a Ledger-backed payment collection lookup scoped by the gateway account.

Domain context:
Payment search supports operational reconciliation, customer-service lookup, recurring-payment tracking, and settlement reporting.

Starting point:
`No prior service state`

State transition summary:
- State before: Payment records may or may not exist in Ledger.
- Transition trigger: A payment search request is submitted.
- Intermediate states: Public API validates filters and calls Ledger.
- State after: Payment state is unchanged.
- Invalid or blocked transitions: Invalid filters or downstream search failures block lookup.

Required execution workflow:
1. Use function `search payments` (`GET /v1/payments`) with authenticated Bearer caller context for `accountId={gateway account id}` and query values `reference={exact reference}`, `email={email fragment}`, `state={created|started|submitted|success|failed|cancelled|error}`, `card_brand={card brand}`, `from_date={UTC ISO date-time}`, `to_date={UTC ISO date-time}`, `page={integer >= 1}`, `display_size={1..500}`, `cardholder_name={name}`, `first_digits_card_number={6 digits}`, `last_digits_card_number={4 digits}`, `from_settled_date={ISO date}`, `to_settled_date={ISO date}`, and `agreement_id={agreement id}` to retrieve matching payments.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup is required for an empty result.
- Connector/Ledger seeding or prior payment creation can make the search non-empty, but all matching payments must be in the same account scope.
- The search action itself cannot be skipped.

Parameter and value bindings:
- Public API adds `account_id`, `transaction_type=PAYMENT`, and `exact_reference_match=true` for Ledger.
- `agreement_id` binds search results to payments charged through or associated with a recurring agreement.
- Pagination links are rewritten to Public API URLs.

Business result:
No state changes. The caller receives a paginated payment result set with payment links.

Constraints and invariants:
- State values are limited to the supported public payment states.
- Date-time filters must use valid UTC date-time format; settled-date filters must be date-only.
- Card digit filters require exact numeric lengths.
- `reference`, `email`, and `card_brand` have implementation length limits.

Failure and exceptional cases:
- Failing function: `search payments`
  - Failure condition: Invalid state, date, pagination, display size, or card digit filter.
  - Why it fails: `PaymentSearchValidator` rejects invalid search values before Ledger search.
  - Violated prerequisite or constraint: Search filters must satisfy implementation validators.
- Failing function: `search payments`
  - Failure condition: Ledger returns page-not-found, downstream failure, or malformed response.
  - Why it fails: `LedgerService.searchPayments` maps non-200 or parse failures to search-payment errors.
  - Violated prerequisite or constraint: Ledger must support the requested result page and response shape.

Implementation notes:
Payment search is Ledger-only and exact-match by reference. Unknown query parameters outside the resource signature are not part of the search parameter object.

<a id="behavior-7"></a>
### Behavior 7: Create a web card payment

Business goal:
Start a standard hosted card payment journey and receive payment links for the payer flow.

API group boundary:
This is atomic. The single function creates the payment aggregate in web authorisation mode.

Domain context:
This is the normal public payment creation workflow for services that redirect users to GOV.UK Pay hosted pages.

Starting point:
`No prior service state`

State transition summary:
- State before: No payment exists for this create request.
- Transition trigger: A valid payment creation request is submitted.
- Intermediate states: Public API validates and forwards the request to Connector.
- State after: A new payment exists in an unfinished web-journey state with public links.
- Invalid or blocked transitions: Invalid body, account disablement, PSP misconfiguration, policy rejection, or idempotency conflict blocks creation.

Required execution workflow:
1. Use function `create web card payment` (`POST /v1/payments`) with authenticated Bearer caller context for `accountId={gateway account id}`, no reused `Idempotency-Key`, and body `amount={integer 0..10000000 subject to account policy}`, `reference={nonblank string}`, `description={nonblank string}`, `return_url={valid URL}`, optional `email={string}`, `language={en|cy}`, `metadata={valid metadata object}`, `prefilled_cardholder_details={valid object}`, `moto={boolean}`, and `delayed_capture={boolean}` to create the payment and capture `payment_id={generated payment id}`.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from create web card payment}` to inspect the payment.
2. Use function `search payments` (`GET /v1/payments`) with `reference={reference from create web card payment}` to find the payment in Ledger after it is indexed.

Existing-state shortcuts:
- No domain setup is required beyond an authenticated, payment-enabled account.
- Direct Connector seeding can create equivalent payment state for later read/cancel/capture/refund behaviors, but not for this creation behavior.
- The payment creation action cannot be skipped.

Parameter and value bindings:
- The generated `payment_id` is the aggregate id reused by payment read, event, cancel, capture, and refund behaviors.
- `return_url` binds the hosted payment journey back to the service.
- Optional `delayed_capture=true` changes the later capture lifecycle.
- If an `Idempotency-Key` is reused, the behavior becomes idempotent replay rather than ordinary new creation.

Business result:
A new card payment exists and is returned with payment links. The response is `201` for a brand-new Connector payment.

Constraints and invariants:
- `amount`, `reference`, and `description` are required by parser.
- OpenAPI marks `return_url` as required; implementation only parses it when present, but Connector can reject modes that require it.
- Amount zero passes local minimum validation but is allowed only for accounts/policies that Connector permits.
- Metadata is limited by key count, key length, and value constraints.

Failure and exceptional cases:
- Failing function: `create web card payment`
  - Failure condition: Missing or malformed `amount`, `reference`, `description`, invalid optional fields, malformed JSON, or invalid metadata.
  - Why it fails: `RequestJsonParser`, bean validation, and metadata validation reject the body.
  - Violated prerequisite or constraint: Payment creation requires valid structured request data.
- Failing function: `create web card payment`
  - Failure condition: Account disabled, PSP not linked, MOTO not allowed, authorisation API not allowed, zero amount not allowed, or card-number content in a payment-link reference.
  - Why it fails: Connector returns a policy/capability error mapped by `CreateChargeExceptionMapper`.
  - Violated prerequisite or constraint: The account and request mode must be allowed by Connector.
- Failing function: `create web card payment`
  - Failure condition: Reused idempotency key with a different body.
  - Why it fails: Connector reports the key has already been used.
  - Violated prerequisite or constraint: Idempotency keys must bind to equivalent create requests.

Implementation notes:
Public API forwards the idempotency header and returns `201` for new payments, `200` for existing idempotent payments. This behavior assumes a new or absent idempotency key.

<a id="behavior-8"></a>
### Behavior 8: Replay idempotent payment creation

Business goal:
Avoid duplicate payment creation when a caller retries the same create-payment request.

API group boundary:
This behavior binds the caller-supplied `Idempotency-Key` and equivalent payment body from the first create request to a later replay request.

Domain context:
Payment creation is financially sensitive. Idempotency lets client retries recover the existing payment instead of producing another charge attempt.

Starting point:
`No prior service state`

State transition summary:
- State before: No payment is associated with the chosen idempotency key.
- Transition trigger: The first create request stores the idempotency binding; the replay request reuses it.
- Intermediate states: Connector records the key/body/payment association.
- State after: Exactly one payment exists for the key; replay returns that existing payment.
- Invalid or blocked transitions: Reusing the same key for a different body is blocked.

Required execution workflow:
1. Use function `create web card payment` (`POST /v1/payments`) with authenticated Bearer caller context for `accountId={gateway account id}`, header `Idempotency-Key={1..255 chars using alphanumerics and hyphens}`, and body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, and `return_url={valid URL}` to create the original payment and capture `payment_id={generated payment id}`.
2. Use function `replay idempotent payment creation` (`POST /v1/payments`) with the same authenticated account, the same header `Idempotency-Key={same key from step 1}`, and the same body `amount={same amount}`, `reference={same reference}`, `description={same description}`, and `return_url={same URL}` to return the existing payment.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from step 1}` to inspect the single payment returned by both requests.

Existing-state shortcuts:
- Skip the first step if Connector already has idempotency state for the same account, key, and equivalent body.
- The existing idempotency binding must belong to the same gateway account.
- The replay action cannot be skipped.

Parameter and value bindings:
- The `Idempotency-Key` header is caller-generated and reused exactly.
- The request body must be equivalent to the body bound to that key.
- The replay response’s `payment_id` must match the original payment id.

Business result:
No second payment is created. The replay returns the existing payment, normally with `200`, while the first successful creation returns `201`.

Constraints and invariants:
- Header length is 1 to 255 characters.
- Header characters are limited to letters, digits, and hyphens.
- The key is scoped by account and request equivalence.

Failure and exceptional cases:
- Failing function: `create web card payment`
  - Failure condition: Initial create request is invalid or the account cannot create payments.
  - Why it fails: The setup payment cannot be established.
  - Violated prerequisite or constraint: Replay needs a prior successful create for the same key.
- Failing function: `replay idempotent payment creation`
  - Failure condition: Same key is reused with a different amount, reference, description, return URL, or payment mode.
  - Why it fails: Connector returns an idempotency-key-used conflict.
  - Violated prerequisite or constraint: A key cannot be rebound to a different payment creation request.

Implementation notes:
Public API does not persist idempotency state itself. It forwards the header to Connector and uses Connector’s `201` versus `200` status to choose created versus existing response semantics.

<a id="behavior-9"></a>
### Behavior 9: Authorise a MOTO API payment

Business goal:
Create a MOTO API payment and complete card authorisation through the public authorisation endpoint.

API group boundary:
This behavior has explicit generated-value binding: `create MOTO API payment` returns `auth_url_post.params.one_time_token`, which `authorise MOTO API payment` consumes.

Domain context:
MOTO API payments are not completed through a hosted return URL. The service submits card details directly using a one-time token.

Starting point:
`No prior service state`

State transition summary:
- State before: No MOTO API payment or one-time token exists.
- Transition trigger: A MOTO API payment is created, then card details are submitted with the token.
- Intermediate states: Payment exists in an API-authorisation-pending state; token is valid and unused.
- State after: Token is consumed and the payment is authorised or rejected according to Connector/PSP outcome.
- Invalid or blocked transitions: Disabled MOTO API capability, invalid token, reused token, or invalid card data blocks authorisation.

Required execution workflow:
1. Use function `create MOTO API payment` (`POST /v1/payments`) with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, `authorisation_mode=moto_api`, and no `return_url` to create the payment and capture `payment_id={generated payment id}` and `one_time_token={auth_url_post.params.one_time_token}`.
2. Use function `authorise MOTO API payment` (`POST /v1/auth`) with body `one_time_token={one_time_token from create MOTO API payment}`, `card_number={12..19 character card number}`, `cardholder_name={nonblank string <=255 chars}`, `cvc={3..4 chars}`, and `expiry_date={MM/YY}` to authorise the payment.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with authenticated Bearer caller context and `paymentId={payment_id from create MOTO API payment}` to inspect the resulting payment state.
2. Use function `get payment events` (`GET /v1/payments/{paymentId}/events`) with `paymentId={payment_id from create MOTO API payment}` to inspect authorisation events.

Existing-state shortcuts:
- Skip `create MOTO API payment` only if Connector already has a same-account MOTO API payment with a valid unused one-time token.
- The token must be current, unused, and bound to the intended payment.
- The authorisation action cannot be skipped.

Parameter and value bindings:
- `one_time_token` is generated by payment creation and consumed once by authorisation.
- `payment_id` is not sent to `/v1/auth`; the token provides the binding to the payment.
- `/v1/auth` is excluded from Bearer account authentication and relies on the token plus card details.

Business result:
The MOTO API payment leaves the pending-authorisation state. On successful authorisation, Public API returns `204`; the payment state is updated in Connector and later visible through payment reads/events.

Constraints and invariants:
- The gateway account must be enabled for MOTO API authorisation.
- `authorisation_mode=moto_api` must be used for token generation.
- The one-time token must be valid and unused.
- Card details must satisfy local validation and Connector/PSP rules.

Failure and exceptional cases:
- Failing function: `create MOTO API payment`
  - Failure condition: Account is not enabled for MOTO API authorisation.
  - Why it fails: Connector returns `AUTHORISATION_API_NOT_ALLOWED`.
  - Violated prerequisite or constraint: MOTO API creation requires account capability.
- Failing function: `authorise MOTO API payment`
  - Failure condition: Token is invalid, unknown, expired, or already used.
  - Why it fails: Connector returns token-specific authorisation errors.
  - Violated prerequisite or constraint: Authorisation requires the generated unused token.
- Failing function: `authorise MOTO API payment`
  - Failure condition: Card number, CVC, expiry date, cardholder name, or accepted card type is invalid.
  - Why it fails: Local validation or Connector authorisation rejection maps to public errors.
  - Violated prerequisite or constraint: Card details must be valid and acceptable.

Implementation notes:
Unlike most endpoints, `/v1/auth` has no Bearer account context because `AuthorizationValidationFilter` excludes it. The token is the binding and security mechanism.

<a id="behavior-10"></a>
### Behavior 10: Take a recurring payment from an active agreement

Business goal:
Charge a payer using the saved payment instrument attached to an active recurring agreement.

API group boundary:
This is atomic at the public API level. The function consumes an existing active `agreement_id` as the authorisation source for a new payment.

Domain context:
After a payer has activated an agreement, services can take recurring payments without sending the payer through the hosted card-entry flow again.

Starting point:
`Pre-existing service/upstream state required`

State transition summary:
- State before: An agreement exists, is active, belongs to the account, and has a saved payment instrument.
- Transition trigger: Payment creation is submitted with `authorisation_mode=agreement`.
- Intermediate states: Connector validates the agreement and creates a payment charged from the saved instrument.
- State after: A new payment exists and is linked to the agreement.
- Invalid or blocked transitions: Unknown, inactive, cross-account, or missing agreement id blocks recurring payment creation.

Required execution workflow:
1. Use function `take recurring payment` (`POST /v1/payments`) with authenticated Bearer caller context for `accountId={gateway account id}`, upstream state `agreement status=active with saved payment instrument`, body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, `authorisation_mode=agreement`, `agreement_id={pre-existing active agreement id}`, and no `return_url`, no `email`, and no `prefilled_cardholder_details` to create the recurring payment and capture `payment_id={generated payment id}`.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from take recurring payment}` to inspect the recurring payment.
2. Use function `search payments` (`GET /v1/payments`) with `agreement_id={agreement_id used by take recurring payment}` to find payments charged through that agreement.

Existing-state shortcuts:
- Public API can create an agreement and initiate setup, but activation requires the external hosted payment journey.
- Direct Connector/Ledger setup can provide an active agreement and saved instrument.
- The `agreement_id` must belong to the same account and remain active; the recurring charge action cannot be skipped.

Parameter and value bindings:
- The request body `agreement_id` binds the new payment to the active agreement.
- `authorisation_mode=agreement` is required for Public API to accept `agreement_id`.
- The generated `payment_id` is reused for later reads, events, refunds, or searches.

Business result:
A new payment is created using the saved payment instrument. It is linked to the recurring agreement and should not expose a hosted return journey.

Constraints and invariants:
- `agreement_id` is accepted only with `authorisation_mode=agreement`.
- `return_url`, `email`, and prefilled cardholder details must not be included for agreement-mode creation.
- Connector enforces active agreement state and payment-instrument availability.

Failure and exceptional cases:
- Failing function: `take recurring payment`
  - Failure condition: `agreement_id` is missing, unknown, inactive, cross-account, or not paired with `authorisation_mode=agreement`.
  - Why it fails: Parser rejects unexpected `agreement_id`, or Connector rejects missing/inactive agreement state.
  - Violated prerequisite or constraint: Recurring charging requires an active same-account agreement.
- Failing function: `take recurring payment`
  - Failure condition: The request includes fields prohibited for agreement mode.
  - Why it fails: Connector rejects the payment-mode/body combination.
  - Violated prerequisite or constraint: Agreement-mode payments do not use hosted return URL or payer-entry fields.

Implementation notes:
The active agreement state cannot be fully established through public API calls alone because setup completion is external to this API.

<a id="behavior-11"></a>
### Behavior 11: Retrieve one payment

Business goal:
Inspect one payment’s current public details and action links.

API group boundary:
This behavior binds the generated `payment_id` from creation to a later read of the same payment aggregate.

Domain context:
Services need to reconcile payment state, amounts, metadata, refund availability, and links after creation or later processing.

Starting point:
`No prior service state`

State transition summary:
- State before: No payment exists for the setup path.
- Transition trigger: Payment creation generates an id, then the read endpoint retrieves it.
- Intermediate states: Payment is created before being read.
- State after: Payment state is unchanged by the read.
- Invalid or blocked transitions: Unknown, cross-account, or unavailable payment ids cannot be retrieved.

Required execution workflow:
1. Use function `create web card payment` (`POST /v1/payments`) with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, and `return_url={valid URL}` to create a payment and capture `payment_id={generated payment id}`.
2. Use function `get payment` (`GET /v1/payments/{paymentId}`) with the same authenticated account, path `paymentId={payment_id from create web card payment}`, and header `X-Ledger` omitted to read the payment using the default strategy.

Optional verification workflow:
1. Use function `get payment events` (`GET /v1/payments/{paymentId}/events`) with `paymentId={payment_id from create web card payment}` to inspect state history.

Existing-state shortcuts:
- Skip creation if a same-account payment already exists and its `payment_id` is known.
- Direct Connector or Ledger setup can seed the payment, but the selected read strategy must be able to find it.
- The read action itself cannot be skipped.

Parameter and value bindings:
- `payment_id` generated by payment creation is reused as `{paymentId}`.
- The hidden `X-Ledger` header can force `ledger-only` or `connector-only`; omitted or invalid values use the default strategy.
- Account ownership is bound through the Bearer API key.

Business result:
The caller receives one payment representation with public links. No payment state is changed by the read.

Constraints and invariants:
- Payment id must be visible to Connector or Ledger for the authenticated account.
- Default strategy tries Connector first and falls back to Ledger.
- Finished and agreement-mode payments may not include cancel links; capture links depend on returned charge links.

Failure and exceptional cases:
- Failing function: `create web card payment`
  - Failure condition: Invalid payment request or account policy rejection.
  - Why it fails: Setup cannot produce a payment id.
  - Violated prerequisite or constraint: The read needs a valid payment aggregate.
- Failing function: `get payment`
  - Failure condition: `paymentId` is unknown or not scoped to the account in both Connector and Ledger.
  - Why it fails: Downstream not-found is mapped by the get-payment exception mapper.
  - Violated prerequisite or constraint: Payment id must exist in caller scope.

Implementation notes:
The default read strategy can mask Connector not-found by falling back to Ledger. Invalid `X-Ledger` values do not fail; they log a warning and use default behavior.

<a id="behavior-12"></a>
### Behavior 12: Cancel an unfinished payment

Business goal:
Cancel a payment that has not reached a finished state.

API group boundary:
This behavior binds a newly created unfinished `payment_id` to the payment cancellation state transition.

Domain context:
A service may need to abandon a payment that the payer has not completed, preventing further continuation of the hosted journey.

Starting point:
`No prior service state`

State transition summary:
- State before: A non-agreement payment exists in a cancellable unfinished state.
- Transition trigger: A cancel request is sent for that payment id.
- Intermediate states: Connector validates state and applies cancellation.
- State after: Payment is cancelled.
- Invalid or blocked transitions: Finished, already-cancelled, agreement-mode, unknown, or cross-account payments cannot be cancelled.

Required execution workflow:
1. Use function `create web card payment` (`POST /v1/payments`) with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, and `return_url={valid URL}` to create an unfinished payment and capture `payment_id={generated payment id}`.
2. Use function `cancel payment` (`POST /v1/payments/{paymentId}/cancel`) with the same authenticated account and path `paymentId={payment_id from create web card payment}` to cancel the unfinished payment.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from create web card payment}` to inspect cancelled state.
2. Use function `get payment events` (`GET /v1/payments/{paymentId}/events`) with `paymentId={payment_id from create web card payment}` to inspect the cancellation event.

Existing-state shortcuts:
- Skip creation if a same-account unfinished, cancellable payment already exists.
- Direct Connector setup can seed a cancellable payment.
- The cancellation action itself cannot be skipped.

Parameter and value bindings:
- `payment_id` from creation is reused as `{paymentId}` in cancellation.
- The authenticated account must match the account that owns the payment.
- No body is required for cancellation.

Business result:
The payment moves to cancelled and can no longer be completed by the payer.

Constraints and invariants:
- Connector enforces the actual cancellable states.
- Agreement-mode payments do not receive cancel links in the public response model.
- Repeating cancellation after success is an invalid state transition.

Failure and exceptional cases:
- Failing function: `create web card payment`
  - Failure condition: Invalid create body or account unable to create payments.
  - Why it fails: No cancellable payment id is produced.
  - Violated prerequisite or constraint: Cancellation needs an existing unfinished payment.
- Failing function: `cancel payment`
  - Failure condition: `paymentId` is unknown or cross-account.
  - Why it fails: Connector returns not found.
  - Violated prerequisite or constraint: Payment must exist in the caller’s account scope.
- Failing function: `cancel payment`
  - Failure condition: Payment is already cancelled, finished, or otherwise non-cancellable.
  - Why it fails: Connector rejects the state transition with bad request or conflict.
  - Violated prerequisite or constraint: Only unfinished cancellable payments can be cancelled.

Implementation notes:
Public API does not read the payment first; it delegates transition validity to Connector.

<a id="behavior-13"></a>
### Behavior 13: Capture a delayed MOTO API payment

Business goal:
Capture funds for a delayed-capture payment after successful card authorisation.

API group boundary:
This behavior binds `payment_id` and `one_time_token` from delayed MOTO API payment creation through authorisation and into the capture transition.

Domain context:
Delayed capture lets a service authorise a card first, perform checks, then capture funds later.

Starting point:
`No prior service state`

State transition summary:
- State before: No delayed payment exists.
- Transition trigger: Delayed MOTO API payment creation, authorisation, then capture.
- Intermediate states: Payment is created with `delayed_capture=true`; authorisation succeeds and leaves it awaiting capture.
- State after: Payment is captured and funds are taken.
- Invalid or blocked transitions: Non-delayed, unauthorised, already-captured, unknown, or cross-account payments cannot be captured.

Required execution workflow:
1. Use function `create MOTO API payment` (`POST /v1/payments`) with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, `authorisation_mode=moto_api`, `delayed_capture=true`, and no `return_url` to create the delayed payment and capture `payment_id={generated payment id}` and `one_time_token={generated token}`.
2. Use function `authorise MOTO API payment` (`POST /v1/auth`) with body `one_time_token={one_time_token from create MOTO API payment}`, `card_number={valid card number}`, `cardholder_name={nonblank string}`, `cvc={valid CVC}`, and `expiry_date={MM/YY}` to authorise the delayed payment.
3. Use function `capture delayed payment` (`POST /v1/payments/{paymentId}/capture`) with authenticated Bearer caller context for the same account and path `paymentId={payment_id from create MOTO API payment}` to capture the authorised delayed payment.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from create MOTO API payment}` to inspect captured state.
2. Use function `get payment events` (`GET /v1/payments/{paymentId}/events`) with `paymentId={payment_id from create MOTO API payment}` to inspect authorisation and capture events.

Existing-state shortcuts:
- Skip creation and authorisation only if a same-account delayed payment already exists in awaiting-capture state.
- Direct Connector setup can seed awaiting-capture state.
- The capture action itself cannot be skipped.

Parameter and value bindings:
- `payment_id` from creation is reused for capture and optional reads.
- `one_time_token` from creation is consumed by authorisation and is not reusable.
- `delayed_capture=true` changes the valid next transition from immediate capture to awaiting explicit capture.

Business result:
The delayed payment is captured. Public API returns `204` when Connector returns no content for capture.

Constraints and invariants:
- The account must be enabled for MOTO API authorisation.
- The payment must have `delayed_capture=true`.
- Capture is valid only after successful authorisation and before prior capture.
- Connector owns the exact capture-state check.

Failure and exceptional cases:
- Failing function: `create MOTO API payment`
  - Failure condition: Account cannot create MOTO API payments or body is invalid.
  - Why it fails: Parser or Connector rejects the delayed payment creation.
  - Violated prerequisite or constraint: Capture needs a delayed payment id.
- Failing function: `authorise MOTO API payment`
  - Failure condition: Token or card details are invalid.
  - Why it fails: Local validation or Connector authorisation rejects the request.
  - Violated prerequisite or constraint: Capture needs successful authorisation.
- Failing function: `capture delayed payment`
  - Failure condition: Payment is unknown, cross-account, not delayed, not authorised, already captured, or otherwise not awaiting capture.
  - Why it fails: Connector returns not found, bad request, or conflict.
  - Violated prerequisite or constraint: Capture requires an authorised delayed payment in the capture-ready state.

Implementation notes:
Public API posts an empty JSON object to Connector for capture and maps only Connector `204` to public success.

<a id="behavior-14"></a>
### Behavior 14: Read payment event history

Business goal:
Inspect the sequence of state-change events for one payment.

API group boundary:
This behavior binds a generated `payment_id` to the event-history lifecycle resource under that payment.

Domain context:
Event history helps services audit how a payment moved through created, started, submitted, success, failure, cancellation, or capture states.

Starting point:
`No prior service state`

State transition summary:
- State before: No payment exists for the setup path.
- Transition trigger: Payment creation produces at least initial event state, then event history is read.
- Intermediate states: Payment creation records initial lifecycle state.
- State after: Event state is unchanged by the read.
- Invalid or blocked transitions: Unknown or cross-account payment id blocks event retrieval.

Required execution workflow:
1. Use function `create web card payment` (`POST /v1/payments`) with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={integer}`, `reference={nonblank string}`, `description={nonblank string}`, and `return_url={valid URL}` to create a payment and capture `payment_id={generated payment id}`.
2. Use function `get payment events` (`GET /v1/payments/{paymentId}/events`) with the same authenticated account, path `paymentId={payment_id from create web card payment}`, and header `X-Ledger` omitted to retrieve event history.

Optional verification workflow:
1. Use function `get payment` (`GET /v1/payments/{paymentId}`) with `paymentId={payment_id from create web card payment}` to compare current payment state with event history.

Existing-state shortcuts:
- Skip payment creation if a same-account payment with event history already exists.
- Direct Connector or Ledger event seeding can support the read, but the chosen strategy must find the payment events.
- The event read cannot be skipped.

Parameter and value bindings:
- `payment_id` is reused as `{paymentId}`.
- Hidden `X-Ledger` can force `ledger-only` or `connector-only`; invalid values fall back to default.
- The same account scope applies to creation and event read.

Business result:
The caller receives an account-scoped event list for the payment. No state changes occur from reading events.

Constraints and invariants:
- The payment must exist and be visible to the selected read strategy.
- Default strategy tries Connector then Ledger.
- Event completeness can differ between Connector and Ledger during eventual-consistency windows.

Failure and exceptional cases:
- Failing function: `create web card payment`
  - Failure condition: Invalid payment creation request.
  - Why it fails: No payment id or initial event state is produced.
  - Violated prerequisite or constraint: Event read needs an existing payment.
- Failing function: `get payment events`
  - Failure condition: `paymentId` is unknown or cross-account.
  - Why it fails: Connector or Ledger not-found is mapped to get-events not-found.
  - Violated prerequisite or constraint: Payment event history must exist for the account-scoped payment id.

Implementation notes:
The event endpoint is read-only but can expose different results based on the read strategy and downstream consistency.

<a id="behavior-15"></a>
### Behavior 15: Create a refund for a successful payment

Business goal:
Submit a full or partial refund for a payment that has completed successfully and has refundable amount remaining.

API group boundary:
This behavior binds a successful `payment_id` to a child refund creation request under that payment.

Domain context:
Services need to return money to payers after successful payments, with optional concurrency protection through `refund_amount_available`.

Starting point:
`No prior service state`

State transition summary:
- State before: No payment or refund exists for the setup path.
- Transition trigger: A MOTO API payment is created and authorised successfully, then a refund is submitted.
- Intermediate states: Payment becomes successful and refundable; Connector accepts a refund request.
- State after: A refund transaction exists or is accepted for asynchronous processing; refundable amount is reduced according to Connector state.
- Invalid or blocked transitions: Unknown payment, non-successful payment, unavailable refund amount, disputed payment, disabled account, or amount-available mismatch blocks refunding.

Required execution workflow:
1. Use function `create MOTO API payment` (`POST /v1/payments`) with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={payment amount}`, `reference={nonblank string}`, `description={nonblank string}`, `authorisation_mode=moto_api`, and no `return_url` to create the payment and capture `payment_id={generated payment id}` and `one_time_token={generated token}`.
2. Use function `authorise MOTO API payment` (`POST /v1/auth`) with body `one_time_token={one_time_token from create MOTO API payment}`, `card_number={valid card number}`, `cardholder_name={nonblank string}`, `cvc={valid CVC}`, and `expiry_date={MM/YY}` to make the payment successful and refundable.
3. Use function `refund payment` (`POST /v1/payments/{paymentId}/refunds`) with authenticated Bearer caller context for the same account, path `paymentId={payment_id from create MOTO API payment}`, and body `amount={refund amount between 1 and current available amount}` and `refund_amount_available={current available amount before this refund}` to submit the refund and capture `refund_id={generated refund id}`.

Optional verification workflow:
1. Use function `get refund` (`GET /v1/payments/{paymentId}/refunds/{refundId}`) with `paymentId={payment_id from create MOTO API payment}` and `refundId={refund_id from refund payment}` to inspect the refund.
2. Use function `list payment refunds` (`GET /v1/payments/{paymentId}/refunds`) with `paymentId={payment_id from create MOTO API payment}` to inspect all refunds for the payment.

Existing-state shortcuts:
- Skip payment creation and authorisation if a same-account successful payment with refundable amount already exists.
- Direct Connector setup can seed a successful refundable payment.
- The refund submission action cannot be skipped.

Parameter and value bindings:
- `payment_id` generated by payment creation is reused as `{paymentId}`.
- `refund_amount_available`, when supplied, must match Connector’s current refundable amount before the refund.
- `refund_id` returned by refund creation is reused for refund reads.

Business result:
A refund is accepted and returned with public links. The implementation returns `202 Accepted` with a refund response entity.

Constraints and invariants:
- `amount` is required and must be within allowed refund bounds.
- `refund_amount_available` is optional in the model; if omitted, Public API reads the payment and derives the amount available before posting to Connector.
- Connector blocks refunds for unavailable amount, disputed payments, disabled accounts, or amount-available mismatch.
- There is no public rollback after a refund is accepted.

Failure and exceptional cases:
- Failing function: `create MOTO API payment`
  - Failure condition: Account cannot create MOTO API payments or body is invalid.
  - Why it fails: No successful payment can be established.
  - Violated prerequisite or constraint: Refund needs a payment id.
- Failing function: `authorise MOTO API payment`
  - Failure condition: Token or card details are invalid.
  - Why it fails: Payment does not become successful/refundable.
  - Violated prerequisite or constraint: Refund needs a successful payment.
- Failing function: `refund payment`
  - Failure condition: `paymentId` is unknown or cross-account.
  - Why it fails: Connector returns not found.
  - Violated prerequisite or constraint: Refund must be under an existing account-scoped payment.
- Failing function: `refund payment`
  - Failure condition: Invalid amount, stale `refund_amount_available`, payment not refundable, or payment disputed.
  - Why it fails: Parser or Connector refund validation rejects the request.
  - Violated prerequisite or constraint: Refund amount and payment refundability must match current Connector state.

Implementation notes:
The OpenAPI documents both `200` and `202` for refund submission, but the implementation always builds a `202 Accepted` response on success.

<a id="behavior-16"></a>
### Behavior 16: List refunds for a payment

Business goal:
Read all refund records under one payment.

API group boundary:
This behavior binds a parent `payment_id` and created refund state to the payment-scoped refund collection.

Domain context:
After refund submission, services need to inspect all refunds for a payment and reconcile remaining refund activity.

Starting point:
`No prior service state`

State transition summary:
- State before: No payment or refund exists for the setup path.
- Transition trigger: A successful payment is created, a refund is submitted, then the payment’s refund collection is read.
- Intermediate states: Payment becomes refundable; refund is accepted.
- State after: Refund state is unchanged by listing.
- Invalid or blocked transitions: Unknown payment id or inaccessible downstream refund collection blocks listing.

Required execution workflow:
1. Use function `create MOTO API payment` (`POST /v1/payments`) with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={payment amount}`, `reference={nonblank string}`, `description={nonblank string}`, `authorisation_mode=moto_api`, and no `return_url` to create the payment and capture `payment_id={generated payment id}` and `one_time_token={generated token}`.
2. Use function `authorise MOTO API payment` (`POST /v1/auth`) with body `one_time_token={one_time_token from create MOTO API payment}`, `card_number={valid card number}`, `cardholder_name={nonblank string}`, `cvc={valid CVC}`, and `expiry_date={MM/YY}` to make the payment refundable.
3. Use function `refund payment` (`POST /v1/payments/{paymentId}/refunds`) with authenticated Bearer caller context, path `paymentId={payment_id from create MOTO API payment}`, and body `amount={refund amount}` and `refund_amount_available={current available amount before refund}` to create a refund.
4. Use function `list payment refunds` (`GET /v1/payments/{paymentId}/refunds`) with the same authenticated account, path `paymentId={payment_id from create MOTO API payment}`, and header `X-Ledger` omitted to list refunds for the payment.

Optional verification workflow:
1. Use function `get refund` (`GET /v1/payments/{paymentId}/refunds/{refundId}`) with `paymentId={payment_id from create MOTO API payment}` and `refundId={refund_id from refund payment}` to inspect a specific refund.

Existing-state shortcuts:
- Skip setup steps if a same-account payment already exists and has zero or more refund records.
- Direct Connector or Ledger setup can seed the payment and refund records.
- The list action cannot be skipped.

Parameter and value bindings:
- Parent `payment_id` is reused across refund creation and refund listing.
- Header `X-Ledger` controls connector-only or ledger-only read strategy when supplied.
- Refund child records must belong to the parent payment id and account.

Business result:
The caller receives the payment-scoped refund collection. No refund state changes during listing.

Constraints and invariants:
- Payment id must exist and be account-scoped.
- The selected strategy must find the payment’s refund collection.
- The list may be empty for an existing payment with no refunds.

Failure and exceptional cases:
- Failing function: `create MOTO API payment`
  - Failure condition: Invalid setup payment creation.
  - Why it fails: No parent payment is available.
  - Violated prerequisite or constraint: Refund listing needs a payment id.
- Failing function: `authorise MOTO API payment`
  - Failure condition: Invalid token or card details.
  - Why it fails: Refund setup cannot create a successful refundable payment.
  - Violated prerequisite or constraint: The setup refund needs successful payment state.
- Failing function: `refund payment`
  - Failure condition: Refund setup is invalid or blocked.
  - Why it fails: No refund exists for a non-empty listing setup.
  - Violated prerequisite or constraint: The setup refund needs refundable amount.
- Failing function: `list payment refunds`
  - Failure condition: `paymentId` is unknown or cross-account.
  - Why it fails: Connector or Ledger returns not found.
  - Violated prerequisite or constraint: Refund collection must belong to an existing account-scoped payment.

Implementation notes:
Default strategy for refund listing is strategy-template based. Ledger and Connector views can differ while refund state is still propagating.

<a id="behavior-17"></a>
### Behavior 17: Retrieve one payment refund

Business goal:
Inspect one refund under its parent payment.

API group boundary:
This behavior binds parent `payment_id` and child `refund_id`; the child id is generated by refund creation and consumed by the refund read.

Domain context:
Refunds may process asynchronously, so services need to check a specific refund’s status and settlement details.

Starting point:
`No prior service state`

State transition summary:
- State before: No payment or refund exists for the setup path.
- Transition trigger: A successful payment is created, a refund is submitted, then the child refund is read.
- Intermediate states: Refund is accepted and identified by `refund_id`.
- State after: Refund state is unchanged by the read.
- Invalid or blocked transitions: Unknown refund id, cross-payment refund id, or cross-account id blocks retrieval.

Required execution workflow:
1. Use function `create MOTO API payment` (`POST /v1/payments`) with authenticated Bearer caller context for `accountId={gateway account id}`, body `amount={payment amount}`, `reference={nonblank string}`, `description={nonblank string}`, `authorisation_mode=moto_api`, and no `return_url` to create a payment and capture `payment_id={generated payment id}` and `one_time_token={generated token}`.
2. Use function `authorise MOTO API payment` (`POST /v1/auth`) with body `one_time_token={one_time_token from create MOTO API payment}`, `card_number={valid card number}`, `cardholder_name={nonblank string}`, `cvc={valid CVC}`, and `expiry_date={MM/YY}` to make the payment refundable.
3. Use function `refund payment` (`POST /v1/payments/{paymentId}/refunds`) with authenticated Bearer caller context, path `paymentId={payment_id from create MOTO API payment}`, and body `amount={refund amount}` and `refund_amount_available={current available amount before refund}` to create a refund and capture `refund_id={generated refund id}`.
4. Use function `get refund` (`GET /v1/payments/{paymentId}/refunds/{refundId}`) with the same authenticated account, path `paymentId={payment_id from create MOTO API payment}`, path `refundId={refund_id from refund payment}`, and header `X-Ledger` omitted to retrieve the refund.

Optional verification workflow:
1. Use function `list payment refunds` (`GET /v1/payments/{paymentId}/refunds`) with `paymentId={payment_id from create MOTO API payment}` to confirm the refund appears in the parent collection.

Existing-state shortcuts:
- Skip setup if a same-account payment and child refund already exist.
- Direct Connector or Ledger setup can seed the refund, but `refund_id` must belong to the supplied `paymentId`.
- The refund read cannot be skipped.

Parameter and value bindings:
- `payment_id` is the parent aggregate id.
- `refund_id` from `refund payment` is reused as `{refundId}`.
- Ledger lookup includes the parent external id, so a refund id under another payment is not equivalent.

Business result:
The caller receives one refund resource linked to its payment. No state changes occur from retrieval.

Constraints and invariants:
- Refund must exist, be account-scoped, and belong to the supplied payment id.
- The selected read strategy must find the refund.
- Parent and child id mismatch is not a valid lookup.

Failure and exceptional cases:
- Failing function: `create MOTO API payment`
  - Failure condition: Invalid payment setup.
  - Why it fails: No parent payment exists.
  - Violated prerequisite or constraint: Refund retrieval needs a payment id.
- Failing function: `authorise MOTO API payment`
  - Failure condition: Invalid token or card details.
  - Why it fails: No successful refundable payment is established.
  - Violated prerequisite or constraint: Refund setup depends on successful payment state.
- Failing function: `refund payment`
  - Failure condition: Invalid or blocked refund creation.
  - Why it fails: No refund id is generated.
  - Violated prerequisite or constraint: Refund retrieval needs a real child refund id.
- Failing function: `get refund`
  - Failure condition: `refundId` is unknown, belongs to another payment, or belongs to another account.
  - Why it fails: Connector or Ledger not-found is mapped to the get-refund error path.
  - Violated prerequisite or constraint: Refund id must be linked to the supplied parent payment.

Implementation notes:
Ledger read wraps transaction-not-found as refund-not-found, preserving the public refund error semantics.

<a id="behavior-18"></a>
### Behavior 18: Search refunds

Business goal:
Find refund transactions across payments for the authenticated account.

API group boundary:
This is atomic. The single function is a Ledger-backed refund collection lookup scoped by account.

Domain context:
Refund search supports operational reconciliation and settlement reporting without requiring a caller to know the parent payment id first.

Starting point:
`No prior service state`

State transition summary:
- State before: Refund records may or may not exist in Ledger.
- Transition trigger: A refund search request is submitted.
- Intermediate states: Public API validates filters and calls Ledger.
- State after: Refund state is unchanged.
- Invalid or blocked transitions: Invalid filters, unavailable page, or Ledger failure blocks lookup.

Required execution workflow:
1. Use function `search refunds` (`GET /v1/refunds`) with authenticated Bearer caller context for `accountId={gateway account id}` and query `from_date={UTC ISO date-time}`, `to_date={UTC ISO date-time}`, `from_settled_date={ISO date}`, `to_settled_date={ISO date}`, `page={integer >= 1}`, and `display_size={1..500}` to retrieve matching refunds.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup is required for an empty result.
- Existing refund records can be created through `refund payment` or seeded in Ledger for non-empty results.
- Returned refunds must still belong to payments in the authenticated account.

Parameter and value bindings:
- Public API adds `account_id={gateway account id}` and `transaction_type=REFUND`.
- Returned refund links bind each refund to its parent payment id.
- Pagination links are transformed to Public API URLs.

Business result:
No state changes. The caller receives paginated refund search results.

Constraints and invariants:
- Date-time and settled-date filters must use valid formats.
- Pagination must be in supported bounds.
- Search is account-scoped and Ledger-backed.

Failure and exceptional cases:
- Failing function: `search refunds`
  - Failure condition: Invalid date, settled-date, page, or display-size query value.
  - Why it fails: `RefundSearchValidator` rejects invalid parameters before Ledger search.
  - Violated prerequisite or constraint: Refund search filters must pass validation.
- Failing function: `search refunds`
  - Failure condition: Ledger returns page not found, downstream error, or malformed response.
  - Why it fails: `SearchRefundsService` maps Ledger search failures.
  - Violated prerequisite or constraint: Ledger must support the requested refund search.

Implementation notes:
Refund search is not payment-scoped. It returns parent payment links for each result.

<a id="behavior-19"></a>
### Behavior 19: Search disputes

Business goal:
Find dispute records associated with payments for the authenticated account.

API group boundary:
This is atomic. The single function is a Ledger-backed dispute reporting lookup scoped by account and dispute filters.

Domain context:
A dispute is created when a payer challenges a completed payment through their bank. Public API exposes dispute visibility but not dispute management.

Starting point:
`No prior service state`

State transition summary:
- State before: Dispute records may or may not exist in Ledger.
- Transition trigger: A dispute search request is submitted.
- Intermediate states: Public API validates filters, rewrites public status to Ledger state, and calls Ledger.
- State after: Dispute state is unchanged.
- Invalid or blocked transitions: Invalid filters or Ledger failure blocks lookup.

Required execution workflow:
1. Use function `search disputes` (`GET /v1/disputes`) with authenticated Bearer caller context for `accountId={gateway account id}` and query `from_date={UTC ISO date-time}`, `to_date={UTC ISO date-time}`, `from_settled_date={ISO date}`, `to_settled_date={ISO date}`, `status={needs_response|under_review|lost|won}`, `page={integer >= 1}`, and `display_size={1..500}` to retrieve matching disputes.

Optional verification workflow:
None.

Existing-state shortcuts:
- No setup is required for an empty result.
- Non-empty results require upstream dispute records in Ledger.
- The search action cannot be skipped, and returned disputes must belong to the authenticated account.

Parameter and value bindings:
- Public `status` is rewritten to Ledger `state`.
- Public API adds `account_id={gateway account id}` and `transaction_type=DISPUTE`.
- Returned dispute records include parent payment links.

Business result:
No state changes. The caller receives paginated dispute records.

Constraints and invariants:
- Public dispute statuses are limited to `needs_response`, `under_review`, `lost`, and `won`.
- Date and pagination filters must pass validators.
- The endpoint does not create, update, accept, contest, or close disputes.

Failure and exceptional cases:
- Failing function: `search disputes`
  - Failure condition: Unsupported status, invalid date, invalid settled date, invalid page, or invalid display size.
  - Why it fails: `DisputeSearchValidator` rejects invalid query values.
  - Violated prerequisite or constraint: Dispute search filters must match public validator rules.
- Failing function: `search disputes`
  - Failure condition: Ledger returns page not found, downstream failure, or malformed response.
  - Why it fails: `SearchDisputesService` and its mapper expose search-dispute errors.
  - Violated prerequisite or constraint: Ledger must support the requested dispute search.

Implementation notes:
The implementation rewrites response pagination links from `state` back to public `status`. Public API exposes dispute reporting only.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Respond to or progress a dispute lifecycle

Priority:
Critical domain gap

Expected business goal:
A service would reasonably expect to retrieve a dispute by id, submit evidence, accept liability, contest the dispute, or observe explicit dispute lifecycle transitions.

Why it is unsupported:
The public surface only supports dispute search. No available function creates a dispute response, submits evidence, changes dispute status, or retrieves one dispute by stable dispute id.

Existing functions considered:
- `search disputes`: returns filtered dispute records but does not mutate or target a single dispute lifecycle.
- `get payment`: can inspect the parent payment, not the dispute case.
- `refund payment`: may be blocked when a payment is disputed, but it does not resolve the dispute.

Missing capability:
A dispute detail endpoint and dispute action endpoints for evidence submission, acceptance, contesting, and lifecycle transition.

Proof that function composition is insufficient:
Chaining dispute search with payment reads only gives visibility. It cannot create evidence, move status, close the dispute, or prove that a particular dispute id was acted on.

Evidence from existing functions/source:
- `search disputes` maps public `status` to Ledger `state` and returns search results only.
- No function in the extracted inventory targets `/v1/disputes/{disputeId}` or a dispute action path.

Business impact:
Dispute handling remains outside this API. Services can detect disputes but cannot manage the operational response workflow through Public API.

### Missing Behavior 2: Activate a recurring agreement entirely through Public API

Priority:
Critical domain gap

Expected business goal:
A service using recurring payments would expect an API-complete path from agreement creation to active agreement with saved payment instrument.

Why it is unsupported:
Public API can create an agreement and create a setup payment, but it cannot complete the hosted payment journey or attach the payment instrument through an API function.

Existing functions considered:
- `create agreement`: creates the agreement shell.
- `create setup-agreement payment`: creates the setup payment and save-instrument intent.
- `get agreement`: reads the status after upstream processing.
- `take recurring payment`: requires the agreement to already be active.
- `cancel agreement`: requires active agreement state for the documented cancellation transition.

Missing capability:
An API-side setup completion, payment-instrument attachment, or explicit agreement activation endpoint.

Proof that function composition is insufficient:
`create agreement` plus `create setup-agreement payment` only initiates setup. Without external hosted payment completion, the agreement remains non-active and cannot satisfy `take recurring payment`.

Evidence from existing functions/source:
- `create setup-agreement payment` maps `set_up_agreement` to `save_payment_instrument_to_agreement=true`.
- `take recurring payment` depends on Connector accepting an active `agreement_id`.
- `cancel agreement` is documented and implemented for active agreements.

Business impact:
API-only integrations and tests cannot create a fully usable recurring agreement without external journey completion or backing-store setup.

### Missing Behavior 3: Amend existing payment or agreement business details

Priority:
Important robustness gap

Expected business goal:
A service may need to correct an agreement description, user identifier, payment reference, metadata, or description before the resource is used operationally.

Why it is unsupported:
The API exposes create, read, search, and cancel-style transitions, but no update or patch behavior for payments or agreements.

Existing functions considered:
- `create agreement`: can set initial agreement fields only.
- `get agreement`: can inspect, not change, agreement fields.
- `search agreements`: can locate candidates, not update them.
- `create web card payment`: can set initial payment fields only.
- `get payment`: can inspect, not change, payment fields.
- `cancel payment`: can cancel a payment, not correct it.

Missing capability:
PATCH/PUT endpoints or domain commands for correcting mutable business fields with ownership and state checks.

Proof that function composition is insufficient:
Cancel-and-recreate is not equivalent. It changes generated ids, links, event history, idempotency state, and audit trail, and it cannot preserve the original aggregate identity.

Evidence from existing functions/source:
All mutation functions create, cancel, capture, authorise, or refund. None accepts an existing id plus replacement business fields.

Business impact:
Mistakes require operational workaround, customer-visible cancellation, or duplicate resource creation.

### Missing Behavior 4: Search agreements by user identifier

Priority:
API ergonomics gap

Expected business goal:
A service that stores `user_identifier` on an agreement should be able to find agreements for that user.

Why it is unsupported:
Agreement search supports `reference`, `status`, `page`, and `display_size`, but not `user_identifier`.

Existing functions considered:
- `create agreement`: accepts optional `user_identifier`.
- `search agreements`: rejects unsupported nonblank search parameters.
- `get agreement`: requires the caller to already know `agreement_id`.

Missing capability:
A `user_identifier` agreement search filter or direct lookup endpoint.

Proof that function composition is insufficient:
Listing pages and filtering client-side is not equivalent because it is incomplete at scale, depends on pagination traversal, and has no stable exact lookup contract.

Evidence from existing functions/source:
`AgreementSearchValidator` defines supported search parameters as reference, status, page, and display size.

Business impact:
Services cannot reliably recover agreements by their own user-level identifier through Public API.

### Missing Behavior 5: Cancel, correct, or retry a submitted refund

Priority:
Important robustness gap

Expected business goal:
A service may need to cancel an accidental refund, retry a failed refund, or correct a refund amount before settlement.

Why it is unsupported:
Refund functions support create and read/search only. There is no refund state-transition command after submission.

Existing functions considered:
- `refund payment`: submits a refund.
- `get refund`: reads one refund status.
- `list payment refunds`: lists refunds under a payment.
- `search refunds`: finds refunds across payments.

Missing capability:
Refund cancellation, retry, adjustment, or explicit state-transition endpoint.

Proof that function composition is insufficient:
Creating another refund cannot cancel or correct the first refund. Reads can observe state but cannot change it, and delete-and-recreate does not exist for refunds.

Evidence from existing functions/source:
`refund payment` returns an accepted refund response; subsequent refund functions are read-only.

Business impact:
Refund mistakes or processing failures require external operational repair outside the Public API.

## Cross-Behavior Observations

- Mutations are Connector-owned; searches and many reads are Ledger-owned. Default single-payment, event, and refund reads may fall back or be forced by hidden `X-Ledger` strategy, so consistency can vary by path.
- Generated ids drive most workflows: `agreement_id`, `payment_id`, `refund_id`, caller-supplied `Idempotency-Key`, and generated `one_time_token`.
- `/v1/auth` is intentionally outside Bearer account authentication and relies on `one_time_token` as the payment binding.
- Validation is uneven. Agreement search rejects unsupported query names, while other resources largely bind known query params. `set_up_agreement` parsing is weaker for non-string JSON than most fields.
- OpenAPI discrepancies exist: `pay-publicapi.json` includes agreements, auth, and disputes, while `src/main/resources/assets/swagger.json` omits them. OpenAPI marks payment `return_url` required, but source parsing does not always require it. Agreement `reference` and `description` are required by implementation but not marked required in the schema. Refund submission documents `200` and `202`, while implementation returns `202`.
- Source registers additional endpoints such as `/v1/transactions` and `/v1/payment_notification`, but they are not represented by exact function names in `full-behavior.md` or the public OpenAPI file used here, so they are not modeled as supported behaviors.
- `create agreement` can partially persist state: Connector creation can succeed before the follow-up Ledger read fails.
- Dispute search rewrites public `status` to Ledger `state` and rewrites pagination links back to the public term.

## Coverage Summary

Fully supported workflow/state areas include agreement creation/search/read, web payment creation, idempotent payment creation replay, MOTO API authorisation, payment read/search/events, unfinished payment cancellation, delayed capture, refund creation/read/list/search, and dispute search.

Partially supported areas include recurring agreement activation and charging, because setup completion requires external/upstream state; web payment completion, because hosted payer interaction is outside the API; and read-after-write consistency, because Connector and Ledger can diverge temporarily.

Unsupported or unsafe areas include dispute lifecycle management, API-only recurring agreement activation, mutable correction of payment/agreement details, agreement lookup by `user_identifier`, and refund cancellation/correction/retry after submission.