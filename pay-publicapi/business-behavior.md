# Domain-Level Behavior Analysis

## Domain Summary

The `pay-publicapi` service is described by its OpenAPI contract as: The GOV.UK Pay REST API. Read [our documentation](https://docs.payments.service.gov.uk/) for more details.

The core business concepts are:

- Agreements: endpoint group for agreements behavior.
- Authorise card payments: endpoint group for authorise card payments behavior.
- Disputes: endpoint group for disputes behavior.
- Card payments: endpoint group for card payments behavior.
- Refunding card payments: endpoint group for refunding card payments behavior.

Contract-level limits: this document captures externally visible business behavior from the API contract. Implementation-only validation, persistence side effects, authorization rules, and asynchronous processing details may be stricter than what is visible here.

## Available Function Inventory

### Agreements

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search agreements for recurring payments` | `GET /v1/agreements` | Search agreements for recurring payments. |
| `create an agreement for recurring payments` | `POST /v1/agreements` | Create an agreement for recurring payments. |
| `get information about a single agreement for recurring payments` | `GET /v1/agreements/{agreementId}` | Get information about a single agreement for recurring payments. |
| `cancel an agreement for recurring payments` | `POST /v1/agreements/{agreementId}/cancel` | Cancel an agreement for recurring payments. |

### Authorise card payments

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `send card details to authorise a moto payment` | `POST /v1/auth` | Send card details to authorise a MOTO payment. |

### Card payments

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search payments` | `GET /v1/payments` | Search payments. |
| `create a payment` | `POST /v1/payments` | Create a payment. |
| `get information about a single payment` | `GET /v1/payments/{paymentId}` | Get information about a single payment. |
| `cancel payment` | `POST /v1/payments/{paymentId}/cancel` | Cancel payment. |
| `take a delayed payment` | `POST /v1/payments/{paymentId}/capture` | Take a delayed payment. |
| `get a payment s events` | `GET /v1/payments/{paymentId}/events` | Get a payment's events. |

### Disputes

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search disputes` | `GET /v1/disputes` | Search disputes. |

### Refunding card payments

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `get information about a payment s refunds` | `GET /v1/payments/{paymentId}/refunds` | Get information about a payment’s refunds. |
| `refund a payment` | `POST /v1/payments/{paymentId}/refunds` | Refund a payment. |
| `check the status of a refund` | `GET /v1/payments/{paymentId}/refunds/{refundId}` | Check the status of a refund. |
| `search refunds` | `GET /v1/refunds` | Search refunds. |

## Supported Business Behaviors

### Behavior 1: Search Agreements For Recurring Payments

Business goal:
Search agreements for recurring payments.

Domain context:
This behavior belongs to the `Agreements` capability area and operates through `GET /v1/agreements`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `search agreements for recurring payments` (`GET /v1/agreements`) with query: reference optional, status optional, page optional, display_size optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `reference`, `status`, `page`, `display_size` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search agreements for recurring payments`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search agreements for recurring payments`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search agreements for recurring payments`
  - Failure condition: HTTP `422` response.
  - Why it fails: Your request failed. Check the `code` and `description` in the response to find out why your request failed.
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search agreements for recurring payments`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search agreements for recurring payments`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK - your request was successful.. Failure responses: 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 404 Not found; 422 Your request failed. Check the `code` and `description` in the response to find out why your request failed.; 429 Too many requests; 500 Downstream system error.

### Behavior 2: Create An Agreement For Recurring Payments

Business goal:
Create an agreement for recurring payments.

Domain context:
This behavior belongs to the `Agreements` capability area and operates through `POST /v1/agreements`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `create an agreement for recurring payments` (`POST /v1/agreements`) with required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `create an agreement for recurring payments`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create an agreement for recurring payments`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create an agreement for recurring payments`
  - Failure condition: HTTP `422` response.
  - Why it fails: Your request failed. Check the `code` and `description` in the response to find out why your request failed.
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create an agreement for recurring payments`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create an agreement for recurring payments`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 201 Created. Failure responses: 400 Bad request; 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 422 Your request failed. Check the `code` and `description` in the response to find out why your request failed.; 429 Too many requests; 500 Downstream system error.

### Behavior 3: Get Information About A Single Agreement For Recurring Payments

Business goal:
Get information about a single agreement for recurring payments.

Domain context:
This behavior belongs to the `Agreements` capability area and operates through `GET /v1/agreements/{agreementId}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `get information about a single agreement for recurring payments` (`GET /v1/agreements/{agreementId}`) with path: agreementId required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `agreementId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `agreementId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `get information about a single agreement for recurring payments`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get information about a single agreement for recurring payments`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get information about a single agreement for recurring payments`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get information about a single agreement for recurring payments`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK - your request was successful.. Failure responses: 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 404 Not found; 429 Too many requests; 500 Downstream system error.

### Behavior 4: Cancel An Agreement For Recurring Payments

Business goal:
Cancel an agreement for recurring payments.

Domain context:
This behavior belongs to the `Agreements` capability area and operates through `POST /v1/agreements/{agreementId}/cancel`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `cancel an agreement for recurring payments` (`POST /v1/agreements/{agreementId}/cancel`) with path: agreementId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `agreementId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `agreementId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `cancel an agreement for recurring payments`
  - Failure condition: HTTP `400` response.
  - Why it fails: Cancellation of agreement failed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cancel an agreement for recurring payments`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cancel an agreement for recurring payments`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cancel an agreement for recurring payments`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cancel an agreement for recurring payments`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 204 No Content. Failure responses: 400 Cancellation of agreement failed; 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 404 Not found; 429 Too many requests; 500 Downstream system error.

### Behavior 5: Send Card Details To Authorise A Moto Payment

Business goal:
Send card details to authorise a MOTO payment.

Domain context:
This behavior belongs to the `Authorise card payments` capability area and operates through `POST /v1/auth`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `send card details to authorise a moto payment` (`POST /v1/auth`) with required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `send card details to authorise a moto payment`
  - Failure condition: HTTP `400` response.
  - Why it fails: Your request is invalid. Check the `code` and `description` in the response to find out why your request failed.
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `send card details to authorise a moto payment`
  - Failure condition: HTTP `402` response.
  - Why it fails: The `card_number` you sent is not a valid card number or you chose not to accept this card type. Check the `code` and `description` fields in the response to find out why your request failed.
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `send card details to authorise a moto payment`
  - Failure condition: HTTP `422` response.
  - Why it fails: A value you sent is invalid or missing. Check the `code` and `description` in the response to find out why your request failed.
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `send card details to authorise a moto payment`
  - Failure condition: HTTP `500` response.
  - Why it fails: There is something wrong with GOV.UK Pay. If there are no issues on our status page (https://payments.statuspage.io), you can contact us with your error code and we'll investigate.
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 204 Your authorisation request was successful.. Failure responses: 400 Your request is invalid. Check the `code` and `description` in the response to find out why your request failed.; 402 The `card_number` you sent is not a valid card number or you chose not to accept this card type. Check the `code` and `description` fields in the response to find out why your request failed.; 422 A value you sent is invalid or missing. Check the `code` and `description` in the response to find out why your request failed.; 500 There is something wrong with GOV.UK Pay. If there are no issues on our status page (https://payments.statuspage.io), you can contact us with your error code and we'll investigate..

### Behavior 6: Search Disputes

Business goal:
Search disputes.

Domain context:
This behavior belongs to the `Disputes` capability area and operates through `GET /v1/disputes`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `search disputes` (`GET /v1/disputes`) with query: from_date optional, to_date optional, from_settled_date optional, to_settled_date optional, status optional, page optional, display_size optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `from_date`, `to_date`, `from_settled_date`, `to_settled_date`, `status`, `page`, `display_size` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search disputes`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search disputes`
  - Failure condition: HTTP `422` response.
  - Why it fails: Invalid parameters: from_date, to_date, from_settled_date, to_settled_date, status, display_size. See Public API documentation for the correct data formats
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search disputes`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search disputes`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK - your request was successful.. Failure responses: 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 422 Invalid parameters: from_date, to_date, from_settled_date, to_settled_date, status, display_size. See Public API documentation for the correct data formats; 429 Too many requests; 500 Downstream system error.

### Behavior 7: Search Payments

Business goal:
Search payments.

Domain context:
This behavior belongs to the `Card payments` capability area and operates through `GET /v1/payments`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `search payments` (`GET /v1/payments`) with query: reference optional, email optional, state optional, card_brand optional, from_date optional, to_date optional, page optional, display_size optional, cardholder_name optional, first_digits_card_number optional, last_digits_card_number optional, from_settled_date optional, to_settled_date optional, agreement_id optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `reference`, `email`, `state`, `card_brand`, `from_date`, `to_date`, `page`, `display_size`, `cardholder_name`, `first_digits_card_number`, `last_digits_card_number`, `from_settled_date` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search payments`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search payments`
  - Failure condition: HTTP `422` response.
  - Why it fails: Invalid parameters: from_date, to_date, status, display_size. See Public API documentation for the correct data formats
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search payments`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search payments`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK - your request was successful.. Failure responses: 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 422 Invalid parameters: from_date, to_date, status, display_size. See Public API documentation for the correct data formats; 429 Too many requests; 500 Downstream system error.

### Behavior 8: Create A Payment

Business goal:
Create a payment.

Domain context:
This behavior belongs to the `Card payments` capability area and operates through `POST /v1/payments`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `create a payment` (`POST /v1/payments`) with header: Idempotency-Key optional; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `create a payment`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create a payment`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create a payment`
  - Failure condition: HTTP `422` response.
  - Why it fails: Your request failed. Check the `code` and `description` in the response to find out why your request failed.
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create a payment`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `create a payment`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 201 Created. Failure responses: 400 Bad request; 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 422 Your request failed. Check the `code` and `description` in the response to find out why your request failed.; 429 Too many requests; 500 Downstream system error.

### Behavior 9: Get Information About A Single Payment

Business goal:
Get information about a single payment.

Domain context:
This behavior belongs to the `Card payments` capability area and operates through `GET /v1/payments/{paymentId}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `get information about a single payment` (`GET /v1/payments/{paymentId}`) with path: paymentId required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `paymentId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `paymentId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `get information about a single payment`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get information about a single payment`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get information about a single payment`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get information about a single payment`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK - your request was successful.. Failure responses: 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 404 Not found; 429 Too many requests; 500 Downstream system error.

### Behavior 10: Cancel Payment

Business goal:
Cancel payment.

Domain context:
This behavior belongs to the `Card payments` capability area and operates through `POST /v1/payments/{paymentId}/cancel`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `cancel payment` (`POST /v1/payments/{paymentId}/cancel`) with path: paymentId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `paymentId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `paymentId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `cancel payment`
  - Failure condition: HTTP `400` response.
  - Why it fails: Cancellation of payment failed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cancel payment`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cancel payment`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cancel payment`
  - Failure condition: HTTP `409` response.
  - Why it fails: Conflict
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cancel payment`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cancel payment`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 204 No Content. Failure responses: 400 Cancellation of payment failed; 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 404 Not found; 409 Conflict; 429 Too many requests; 500 Downstream system error.

### Behavior 11: Take A Delayed Payment

Business goal:
Take a delayed payment.

Domain context:
This behavior belongs to the `Card payments` capability area and operates through `POST /v1/payments/{paymentId}/capture`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `take a delayed payment` (`POST /v1/payments/{paymentId}/capture`) with path: paymentId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `paymentId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `paymentId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `take a delayed payment`
  - Failure condition: HTTP `400` response.
  - Why it fails: Capture of payment failed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `take a delayed payment`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `take a delayed payment`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `take a delayed payment`
  - Failure condition: HTTP `409` response.
  - Why it fails: Conflict
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `take a delayed payment`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `take a delayed payment`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 204 No Content. Failure responses: 400 Capture of payment failed; 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 404 Not found; 409 Conflict; 429 Too many requests; 500 Downstream system error.

### Behavior 12: Get A Payment S Events

Business goal:
Get a payment's events.

Domain context:
This behavior belongs to the `Card payments` capability area and operates through `GET /v1/payments/{paymentId}/events`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `get a payment s events` (`GET /v1/payments/{paymentId}/events`) with path: paymentId required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `paymentId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `paymentId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `get a payment s events`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get a payment s events`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get a payment s events`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get a payment s events`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK - your request was successful.. Failure responses: 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 404 Not found; 429 Too many requests; 500 Downstream system error.

### Behavior 13: Get Information About A Payment S Refunds

Business goal:
Get information about a payment’s refunds.

Domain context:
This behavior belongs to the `Refunding card payments` capability area and operates through `GET /v1/payments/{paymentId}/refunds`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `get information about a payment s refunds` (`GET /v1/payments/{paymentId}/refunds`) with path: paymentId required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `paymentId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `paymentId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `get information about a payment s refunds`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get information about a payment s refunds`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get information about a payment s refunds`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `get information about a payment s refunds`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK - your request was successful.. Failure responses: 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 404 Not found; 429 Too many requests; 500 Downstream system error.

### Behavior 14: Refund A Payment

Business goal:
Refund a payment.

Domain context:
This behavior belongs to the `Refunding card payments` capability area and operates through `POST /v1/payments/{paymentId}/refunds`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `refund a payment` (`POST /v1/payments/{paymentId}/refunds`) with path: paymentId required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `paymentId` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `paymentId`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `refund a payment`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `refund a payment`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `refund a payment`
  - Failure condition: HTTP `412` response.
  - Why it fails: Refund amount available mismatch
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `refund a payment`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `refund a payment`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 successful operation; 202 ACCEPTED. Failure responses: 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 404 Not found; 412 Refund amount available mismatch; 429 Too many requests; 500 Downstream system error.

### Behavior 15: Check The Status Of A Refund

Business goal:
Check the status of a refund.

Domain context:
This behavior belongs to the `Refunding card payments` capability area and operates through `GET /v1/payments/{paymentId}/refunds/{refundId}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `check the status of a refund` (`GET /v1/payments/{paymentId}/refunds/{refundId}`) with path: paymentId required, refundId required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `paymentId`, `refundId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `paymentId`, `refundId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `check the status of a refund`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `check the status of a refund`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `check the status of a refund`
  - Failure condition: HTTP `429` response.
  - Why it fails: Too many requests
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `check the status of a refund`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK - your request was successful.. Failure responses: 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 404 Not found; 429 Too many requests; 500 Downstream system error.

### Behavior 16: Search Refunds

Business goal:
Search refunds.

Domain context:
This behavior belongs to the `Refunding card payments` capability area and operates through `GET /v1/refunds`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `search refunds` (`GET /v1/refunds`) with query: from_date optional, to_date optional, from_settled_date optional, to_settled_date optional, page optional, display_size optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `from_date`, `to_date`, `from_settled_date`, `to_settled_date`, `page`, `display_size` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search refunds`
  - Failure condition: HTTP `401` response.
  - Why it fails: Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication)
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search refunds`
  - Failure condition: HTTP `422` response.
  - Why it fails: Invalid parameters. See Public API documentation for the correct data formats
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search refunds`
  - Failure condition: HTTP `500` response.
  - Why it fails: Downstream system error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK - your request was successful.. Failure responses: 401 Your API key is missing or invalid. Read more about [authenticating GOV.UK Pay API requests](https://docs.payments.service.gov.uk/api_reference/#authentication); 422 Invalid parameters. See Public API documentation for the correct data formats; 500 Downstream system error.
