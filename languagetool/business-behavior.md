# Domain-Level Behavior Analysis

## Domain Summary

The `languagetool` service is described by its OpenAPI contract as: Check texts for style and grammar issues with LanguageTool. See our wiki for access limitations.

The core business concepts are:

- Check: endpoint group for check behavior.
- Languages: endpoint group for languages behavior.

Contract-level limits: this document captures externally visible business behavior from the API contract. Implementation-only validation, persistence side effects, authorization rules, and asynchronous processing details may be stricter than what is visible here.

## Available Function Inventory

### Check

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `check a text` | `POST /check` | Check a text. |

### Languages

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `get a list of supported languages` | `GET /languages` | Get a list of supported languages. |

## Supported Business Behaviors

### Behavior 1: Check A Text

Business goal:
Check a text.

Domain context:
This behavior belongs to the `Check` capability area and operates through `POST /check`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `check a text` (`POST /check`) with formData: text optional, data optional, language required, altLanguages optional, motherTongue optional, preferredVariants optional, enabledRules optional, disabledRules optional, enabledCategories optional, disabledCategories optional, enabledOnly optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `language`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `check a text`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 the result of checking the text.

### Behavior 2: Get A List Of Supported Languages

Business goal:
Get a list of supported languages.

Domain context:
This behavior belongs to the `Languages` capability area and operates through `GET /languages`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `get a list of supported languages` (`GET /languages`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `get a list of supported languages`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 An array of language objects..
