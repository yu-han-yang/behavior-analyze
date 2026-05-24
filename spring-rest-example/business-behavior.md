# Domain-Level Behavior Analysis

## Domain Summary

The `spring-rest-example` service is described by its OpenAPI contract as: project-api

The core business concepts are:

- problem-controller: endpoint group for problem controller behavior.
- project-controller: endpoint group for project controller behavior.
- sub-problem-controller: endpoint group for sub problem controller behavior.

Contract-level limits: this document captures externally visible business behavior from the API contract. Implementation-only validation, persistence side effects, authorization rules, and asynchronous processing details may be stricter than what is visible here.

## Available Function Inventory

### problem-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `code` | `GET /api/problem/{code}` | code로 문제 조회. |
| `` | `POST /api/problem/{code}` | 문제 생성. |
| `code` | `DELETE /api/problem/{code}` | code로 최근 문제 삭제. |
| `code` | `DELETE /api/problem/{code}/all` | code로 모든 문제 삭제,. |

### project-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `` | `POST /api/project` | 프로젝트 생성. |
| `code` | `GET /api/project/{code}` | code로 프로젝트 조회. |
| `project` | `PUT /api/project/{code}` | project 업데이트. |
| `code` | `DELETE /api/project/{code}` | code로 프로젝트 삭제. |

### sub-problem-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `save sub problem` | `POST /api/subproblem` | Creates or executes subproblem business state. |

## Supported Business Behaviors

### Behavior 1: Code

Business goal:
code로 문제 조회.

Domain context:
This behavior belongs to the `problem-controller` capability area and operates through `GET /api/problem/{code}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `code` (`GET /api/problem/{code}`) with path: code required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `code` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `code`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `code`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 2: 

Business goal:
문제 생성.

Domain context:
This behavior belongs to the `problem-controller` capability area and operates through `POST /api/problem/{code}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `` (`POST /api/problem/{code}`) with path: code required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `code` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `code`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: ``
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 3: Code

Business goal:
code로 최근 문제 삭제.

Domain context:
This behavior belongs to the `problem-controller` capability area and operates through `DELETE /api/problem/{code}`.

Starting point:
The target resource identified by the path should exist and be eligible for removal.

Required execution workflow:
1. Use function `code` (`DELETE /api/problem/{code}`) with path: code required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the resource is absent or no longer active.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the delete operation.

Parameter and value bindings:
- Path values `code` identify the business resource scope for the operation.

Business result:
The addressed resource is removed, cancelled, or detached from its previous scope.

Constraints and invariants:
- Required request values: `code`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `code`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 4: Code

Business goal:
code로 모든 문제 삭제,.

Domain context:
This behavior belongs to the `problem-controller` capability area and operates through `DELETE /api/problem/{code}/all`.

Starting point:
The target resource identified by the path should exist and be eligible for removal.

Required execution workflow:
1. Use function `code` (`DELETE /api/problem/{code}/all`) with path: code required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the resource is absent or no longer active.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the delete operation.

Parameter and value bindings:
- Path values `code` identify the business resource scope for the operation.

Business result:
The addressed resource is removed, cancelled, or detached from its previous scope.

Constraints and invariants:
- Required request values: `code`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `code`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 5: 

Business goal:
프로젝트 생성.

Domain context:
This behavior belongs to the `project-controller` capability area and operates through `POST /api/project`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `` (`POST /api/project`) with required request body (application/json).

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
- Failing function: ``
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 6: Code

Business goal:
code로 프로젝트 조회.

Domain context:
This behavior belongs to the `project-controller` capability area and operates through `GET /api/project/{code}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `code` (`GET /api/project/{code}`) with path: code required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `code` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `code`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `code`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 7: Project

Business goal:
project 업데이트.

Domain context:
This behavior belongs to the `project-controller` capability area and operates through `PUT /api/project/{code}`.

Starting point:
The target resource identified by the path should exist unless the implementation treats the request as an upsert.

Required execution workflow:
1. Use function `project` (`PUT /api/project/{code}`) with path: code required; required request body (application/json).

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the update operation.

Parameter and value bindings:
- Path values `code` identify the business resource scope for the operation.
- The required request body (application/json) supplies the business payload for the operation.

Business result:
The addressed resource is replaced with the submitted representation or command result.

Constraints and invariants:
- Required request values: `code`.
- Request-body validation follows the schema declared for the accepted media type.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `project`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 8: Code

Business goal:
code로 프로젝트 삭제.

Domain context:
This behavior belongs to the `project-controller` capability area and operates through `DELETE /api/project/{code}`.

Starting point:
The target resource identified by the path should exist and be eligible for removal.

Required execution workflow:
1. Use function `code` (`DELETE /api/project/{code}`) with path: code required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the resource is absent or no longer active.

Existing-state shortcuts:
- Direct setup can create the target resource before invoking the delete operation.

Parameter and value bindings:
- Path values `code` identify the business resource scope for the operation.

Business result:
The addressed resource is removed, cancelled, or detached from its previous scope.

Constraints and invariants:
- Required request values: `code`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `code`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.

### Behavior 9: Save Sub Problem

Business goal:
Creates or executes subproblem business state.

Domain context:
This behavior belongs to the `sub-problem-controller` capability area and operates through `POST /api/subproblem`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `save sub problem` (`POST /api/subproblem`) with required request body (application/json).

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
- Failing function: `save sub problem`
  - Failure condition: No explicit error response is declared in the OpenAPI contract.
  - Why it fails: Domain-specific failures are not visible from the contract alone.
  - Violated prerequisite or constraint: Not specified.

Implementation notes:
Success responses: 200 OK.
