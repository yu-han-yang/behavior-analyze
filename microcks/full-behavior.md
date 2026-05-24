Analyzed `microcks.json` and `src` only. I did not execute the API.

### Function 1: upload and import an artifact file

Function name:
upload artifact file

Core endpoint(s):
- `POST /api/artifact/upload`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function imports a service definition from an uploaded artifact file and returns the imported service name and version when at least one service is created or updated.
- Invocation:
  Step 1: `POST /api/artifact/upload` with required path/query/body/form/header values
- Constraints:
  The request must provide form field `file`. Query `mainArtifact` defaults to `true`. The uploaded file must be non-empty and parseable by the service-definition import logic. The response body is `{serviceName}:{serviceVersion}`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The uploaded file is empty. The required artifact content is empty. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/artifact/upload`
  - Why this fails:
    The controller skips import and returns `204 No Content`.
  - Intentionally violated constraints:
    The required artifact content is empty.
- Branch 2:
  - Preconditions:
    - The failure-causing state or request values are present: The uploaded artifact cannot be parsed as a supported Microcks service definition. The file content is not a supported contract or mock repository artifact. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/artifact/upload`
  - Why this fails:
    `MockRepositoryImportException` is returned as `400 Bad Request`.
  - Intentionally violated constraints:
    The file content is not a supported contract or mock repository artifact.

Endpoint coverage:
- Covers:
  `POST /api/artifact/upload`
- Distinct meaning:
  Imports a local uploaded service contract or examples artifact.

### Function 2: download and import a remote artifact

Function name:
download artifact

Core endpoint(s):
- `POST /api/artifact/download`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function downloads a remote artifact, imports service definitions from it, and returns the imported service name and version when at least one service is created or updated.
- Invocation:
  Step 1: `POST /api/artifact/download` with required path/query/body/form/header values
- Constraints:
  Query `url` must identify the remote artifact. Query `mainArtifact` defaults to `true`. Optional `secretName` is looked up with `GET`-equivalent repository logic and used for authenticated download if found.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The remote artifact cannot be retrieved. `url` points to an unreachable or unreadable remote artifact. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/artifact/download`
  - Why this fails:
    Download `IOException` returns `500 Internal Server Error`.
  - Intentionally violated constraints:
    `url` points to an unreachable or unreadable remote artifact.
- Branch 2:
  - Preconditions:
    - The failure-causing state or request values are present: The remote artifact is retrieved but cannot be imported. The artifact content is not a supported service definition. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/artifact/download`
  - Why this fails:
    `MockRepositoryImportException` returns `400 Bad Request`.
  - Intentionally violated constraints:
    The artifact content is not a supported service definition.

Endpoint coverage:
- Covers:
  `POST /api/artifact/download`
- Distinct meaning:
  Imports a service contract or examples artifact by URL.

### Function 3: import a repository snapshot

Function name:
import repository snapshot

Core endpoint(s):
- `POST /api/import`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function imports a JSON Microcks repository snapshot containing services, resources, requests, responses, and optional event messages.
- Invocation:
  Step 1: `POST /api/import` with required path/query/body/form/header values
- Constraints:
  Form field `file` must be provided. Implementation imports only when `file` is non-empty and has content type `application/json`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The file is empty or is not `application/json`. The file does not satisfy the implementation content checks. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/import`
  - Why this fails:
    The controller does not import anything, but still returns `201 Created`.
  - Intentionally violated constraints:
    The file does not satisfy the implementation content checks.

Endpoint coverage:
- Covers:
  `POST /api/import`
- Distinct meaning:
  Bulk-imports repository data from exported JSON.

### Function 4: export selected services

Function name:
export services

Core endpoint(s):
- `GET /api/export`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request query `serviceIds` must include the prerequisite service `id`. Multiple service ids may be supplied.

Successful execution:
- Result:
  This function exports selected services and their resources, requests, responses, and event messages as a JSON repository file.
- Invocation:
  Step 1: `GET /api/export` with required path/query/body/form/header values
- Constraints:
  The request query `serviceIds` must include the `id` returned by the prerequisite endpoint. Multiple service ids may be supplied.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/export`
- Distinct meaning:
  Exports repository content for a selected service id set.

### Function 5: create a generic REST service

Function name:
create generic REST service

Core endpoint(s):
- `POST /api/services/generic`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function creates a `GENERIC_REST` service with CRUD-like dynamic mock operations for one resource name.
- Invocation:
  Step 1: `POST /api/services/generic` with required path/query/body/form/header values
- Constraints:
  Body fields `name`, `version`, and `resource` define the service and dynamic resource path. Optional `referencePayload` must be JSON if it is intended to seed a reference generic resource. The created service includes operations `POST /{resource}`, `GET /{resource}`, `GET /{resource}/:id`, `PUT /{resource}/:id`, and `DELETE /{resource}/:id`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused when the failure endpoint requires `{id}` or `{serviceId}`. This prerequisite state is used before the failing request: A service with the same `name` and `version` already exists.
    - The failure-causing state or request values are present: A service with the same `name` and `version` already exists. The failing request repeats the same body `name` and `version` as an existing service. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/services/generic`
  - Why this fails:
    The service layer throws `EntityAlreadyExistsException`, and the controller returns `409 Conflict`.
  - Intentionally violated constraints:
    The failing request repeats the same body `name` and `version` as an existing service.

Endpoint coverage:
- Covers:
  `POST /api/services/generic`
- Distinct meaning:
  Creates a dynamic REST mock service.

### Function 6: create a generic event service

Function name:
create generic event service

Core endpoint(s):
- `POST /api/services/generic/event`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function creates a `GENERIC_EVENT` service with one `SUBSCRIBE {event}` operation and default Kafka/WebSocket bindings.
- Invocation:
  Step 1: `POST /api/services/generic/event` with required path/query/body/form/header values
- Constraints:
  Body fields `name`, `version`, and `resource` are used, where `resource` is treated as the event name. Optional `referencePayload` creates an AsyncAPI resource and a reference event message.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A `GENERIC_EVENT` service exists with the required `name`, `version`, and event `resource`. This can be satisfied by directly inserting the service and operation records, or by calling `POST /api/services/generic/event` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused consistently. This prerequisite state is used before the failing request: A service with the same `name` and `version` already exists.
    - The failure-causing state or request values are present: A service with the same `name` and `version` already exists. The failing request repeats the same body `name` and `version` as an existing service. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/services/generic/event`
  - Why this fails:
    The service layer throws `EntityAlreadyExistsException`, and the controller returns `409 Conflict`.
  - Intentionally violated constraints:
    The failing request repeats the same body `name` and `version` as an existing service.

Endpoint coverage:
- Covers:
  `POST /api/services/generic/event`
- Distinct meaning:
  Creates a dynamic event mock service.

### Function 7: list services

Function name:
list services

Core endpoint(s):
- `GET /api/services`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns a paginated list of services sorted by name and version.
- Invocation:
  Step 1: `GET /api/services` with required path/query/body/form/header values
- Constraints:
  Optional query `page` defaults to `0`; optional query `size` defaults to `20`.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/services`
- Distinct meaning:
  Browses the service catalog.

### Function 8: search services

Function name:
search services

Core endpoint(s):
- `GET /api/services/search`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function searches services by name, labels, or both.
- Invocation:
  Step 1: `GET /api/services/search` with required path/query/body/form/header values
- Constraints:
  Query `name` performs name-like search. Query parameters prefixed with `labels.` are converted into label filters. If only labels are present, search is label-only; if both are present, both must match.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/services/search`
- Distinct meaning:
  Searches the service catalog using query-map filters.

### Function 9: count services

Function name:
count services

Core endpoint(s):
- `GET /api/services/count`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns the total service count as `{ "counter": n }`.
- Invocation:
  Step 1: `GET /api/services/count` with required path/query/body/form/header values
- Constraints:
  No prerequisite resource state is required.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/services/count`
- Distinct meaning:
  Counts all services.

### Function 10: summarize service types

Function name:
summarize service types

Core endpoint(s):
- `GET /api/services/map`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns a map of service type to number of services of that type.
- Invocation:
  Step 1: `GET /api/services/map` with required path/query/body/form/header values
- Constraints:
  No prerequisite resource state is required.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/services/map`
- Distinct meaning:
  Aggregates services by type.

### Function 11: list service labels

Function name:
list service labels

Core endpoint(s):
- `GET /api/services/labels`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns available service label keys and their values.
- Invocation:
  Step 1: `GET /api/services/labels` with required path/query/body/form/header values
- Constraints:
  No prerequisite resource state is required.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/services/labels`
- Distinct meaning:
  Discovers labels used by services.

### Function 12: retrieve service without messages

Function name:
get service definition

Core endpoint(s):
- `GET /api/services/{id}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request path `{id}` must be the service `id` returned by the prerequisite endpoint. Query `messages=false` must be used to return the raw `Service` instead of a `ServiceView`.

Successful execution:
- Result:
  This function retrieves the raw service definition for an existing service.
- Invocation:
  Step 1: `GET /api/services/{id}` with required path/query/body/form/header values
- Constraints:
  The core request path `{id}` must be the service `id` returned by the prerequisite endpoint. Query `messages=false` must be used to return the raw `Service` instead of a `ServiceView`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The service id does not exist. The prerequisite service-creation endpoint is intentionally omitted or `{id}` is wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/services/{id}`
  - Why this fails:
    The controller returns `404 Not Found`.
  - Intentionally violated constraints:
    The prerequisite service-creation endpoint is intentionally omitted or `{id}` is wrong.

Endpoint coverage:
- Covers:
  `GET /api/services/{id}`
- Distinct meaning:
  Retrieves service metadata and operations without embedded examples.

### Function 13: retrieve service with messages

Function name:
get service with examples

Core endpoint(s):
- `GET /api/services/{id}`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite artifact import creates a contract/examples artifact. The prerequisite lookup uses the imported service name to obtain the service `id`. The core request path `{id}` must be that service id, and query `messages=true` or omitted returns `ServiceView`.

Successful execution:
- Result:
  This function retrieves a service view with request/response examples or event messages grouped by operation.
- Invocation:
  Step 1: `GET /api/services/{id}` with required path/query/body/form/header values
- Constraints:
  The prerequisite artifact import creates a contract/examples artifact. The prerequisite lookup uses the imported service name to obtain the service `id`. The core request path `{id}` must be that service id, and query `messages=true` or omitted returns `ServiceView`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The service id does not exist. The service import or creation endpoint is omitted or `{id}` is wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/services/{id}`
  - Why this fails:
    The controller returns `404 Not Found`.
  - Intentionally violated constraints:
    The service import or creation endpoint is omitted or `{id}` is wrong.

Endpoint coverage:
- Covers:
  `GET /api/services/{id}`
- Distinct meaning:
  Retrieves service plus operation message examples.

### Function 14: update service metadata

Function name:
update service metadata

Core endpoint(s):
- `PUT /api/services/{id}/metadata`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request path `{id}` must be the service `id` from the prerequisite endpoint. Body is `Metadata` with `labels` and `annotations`. The authenticated `UserInfo` must have manager access for the service.

Successful execution:
- Result:
  This function replaces a service’s labels and annotations and updates its last-update timestamp.
- Invocation:
  Step 1: `PUT /api/services/{id}/metadata` with required path/query/body/form/header values
- Constraints:
  The core request path `{id}` must be the service `id` from the prerequisite endpoint. Body is `Metadata` with `labels` and `annotations`. The authenticated `UserInfo` must have manager access for the service.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused when the failure endpoint requires `{id}` or `{serviceId}`. This prerequisite state is used before the failing request: The user does not have manager access or the service does not exist.
    - The failure-causing state or request values are present: The user does not have manager access or the service does not exist. The core request uses a `UserInfo` without the required manager role or uses an id that does not identify a manageable service. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `PUT /api/services/{id}/metadata`
  - Why this fails:
    `serviceService.updateMetadata` returns `false`, and the controller returns `403 Forbidden`.
  - Intentionally violated constraints:
    The core request uses a `UserInfo` without the required manager role or uses an id that does not identify a manageable service.

Endpoint coverage:
- Covers:
  `PUT /api/services/{id}/metadata`
- Distinct meaning:
  Updates service labels and annotations.

### Function 15: override service operation settings

Function name:
override operation

Core endpoint(s):
- `PUT /api/services/{id}/operation`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request path `{id}` must be the service `id` from the prerequisite endpoint. Query `operationName` must equal an operation on that service, such as `GET /{resource}` from the generic service. Body is `OperationOverrideDTO`. The authenticated `UserInfo` must have manager access.

Successful execution:
- Result:
  This function changes an operation’s dispatcher, dispatcher rules, default delay, and parameter constraints.
- Invocation:
  Step 1: `PUT /api/services/{id}/operation` with required path/query/body/form/header values
- Constraints:
  The core request path `{id}` must be the service `id` from the prerequisite endpoint. Query `operationName` must equal an operation on that service, such as `GET /{resource}` from the generic service. Body is `OperationOverrideDTO`. The authenticated `UserInfo` must have manager access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused when the failure endpoint requires `{id}` or `{serviceId}`. This prerequisite state is used before the failing request: The operation name is not found or the user lacks manager access.
    - The failure-causing state or request values are present: The operation name is not found or the user lacks manager access. The core request uses an `operationName` not present in the service, or a user without manager access. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `PUT /api/services/{id}/operation`
  - Why this fails:
    `serviceService.updateOperation` returns `false`, and the controller returns `403 Forbidden`.
  - Intentionally violated constraints:
    The core request uses an `operationName` not present in the service, or a user without manager access.

Endpoint coverage:
- Covers:
  `PUT /api/services/{id}/operation`
- Distinct meaning:
  Overrides operation-level mock dispatch settings.

### Function 16: delete service

Function name:
delete service

Core endpoint(s):
- `DELETE /api/services/{id}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request path `{id}` must be the service `id` returned by the prerequisite endpoint. The authenticated `UserInfo` must be admin or manager for the service.

Successful execution:
- Result:
  This function deletes an existing service and its related resources, tests, requests, responses, and event messages.
- Invocation:
  Step 1: `DELETE /api/services/{id}` with required path/query/body/form/header values
- Constraints:
  The core request path `{id}` must be the service `id` returned by the prerequisite endpoint. The authenticated `UserInfo` must be admin or manager for the service.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused when the failure endpoint requires `{id}` or `{serviceId}`. This prerequisite state is used before the failing request: The service exists but the user lacks delete permission.
    - The failure-causing state or request values are present: The service exists but the user lacks delete permission. The core request uses a user without admin or manager access. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `DELETE /api/services/{id}`
  - Why this fails:
    `serviceService.deleteService` returns `false`, and the controller returns `403 Forbidden`.
  - Intentionally violated constraints:
    The core request uses a user without admin or manager access.

Endpoint coverage:
- Covers:
  `DELETE /api/services/{id}`
- Distinct meaning:
  Removes a service and its dependent stored data.

### Function 17: create dynamic REST resource

Function name:
create dynamic resource

Core endpoint(s):
- `POST /dynarest/{service}/{version}/{resource}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request path `{service}` and `{version}` must match the prerequisite service body `name` and `version`. The core request path `{resource}` must match the prerequisite service body `resource`. The core request body must be valid JSON. The returned `id` is required by later dynamic read, update, and delete operations.

Successful execution:
- Result:
  This function stores a JSON resource instance for a generic REST service and returns the resource payload with generated `id`.
- Invocation:
  Step 1: `POST /dynarest/{service}/{version}/{resource}` with required path/query/body/form/header values
- Constraints:
  The core request path `{service}` and `{version}` must match the prerequisite service body `name` and `version`. The core request path `{resource}` must match the prerequisite service body `resource`. The core request body must be valid JSON. The returned `id` is required by later dynamic read, update, and delete operations.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The generic REST service or matching `POST /{resource}` operation does not exist. The prerequisite `POST /api/services/generic` is omitted or path values do not match it. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /dynarest/{service}/{version}/{resource}`
  - Why this fails:
    `getMockContext` returns null and the controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The prerequisite `POST /api/services/generic` is omitted or path values do not match it.
- Branch 2:
  - Preconditions:
    - A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused when the failure endpoint requires `{id}` or `{serviceId}`. This prerequisite state is used before the failing request: The core request body is not valid JSON.
    - The failure-causing state or request values are present: The core request body is not valid JSON. The core request body is not parseable JSON. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /dynarest/{service}/{version}/{resource}`
  - Why this fails:
    JSON parsing fails and the controller returns `422 Unprocessable Entity`.
  - Intentionally violated constraints:
    The core request body is not parseable JSON.

Endpoint coverage:
- Covers:
  `POST /dynarest/{service}/{version}/{resource}`
- Distinct meaning:
  Creates a persisted generic resource instance.

### Function 18: list dynamic REST resources

Function name:
list dynamic resources

Core endpoint(s):
- `GET /dynarest/{service}/{version}/{resource}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request path values must match the prerequisite service name, version, and resource. Optional `page` and `size` control pagination. If no body is supplied, the implementation lists by service id.

Successful execution:
- Result:
  This function returns stored generic resource instances for a generic REST service as a JSON array.
- Invocation:
  Step 1: `GET /dynarest/{service}/{version}/{resource}` with required path/query/body/form/header values
- Constraints:
  The core request path values must match the prerequisite service name, version, and resource. Optional `page` and `size` control pagination. If no body is supplied, the implementation lists by service id.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The service or matching `GET /{resource}` operation does not exist. The generic service creation endpoint is omitted or mismatched. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /dynarest/{service}/{version}/{resource}`
  - Why this fails:
    `getMockContext` returns null and the controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The generic service creation endpoint is omitted or mismatched.

Endpoint coverage:
- Covers:
  `GET /dynarest/{service}/{version}/{resource}`
- Distinct meaning:
  Lists generic resources without a JSON filter.

### Function 19: search dynamic REST resources

Function name:
filter dynamic resources

Core endpoint(s):
- `GET /dynarest/{service}/{version}/{resource}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The prerequisite creation request creates a resource and returns its `id`. The core request path values must match the prerequisite service state, and the body must be a JSON query whose keys are matched under stored `payload`.

Successful execution:
- Result:
  This function searches stored generic resources for a generic REST service using a JSON query body.
- Invocation:
  Step 1: `GET /dynarest/{service}/{version}/{resource}` with required path/query/body/form/header values
- Constraints:
  The prerequisite creation request creates a resource and returns its `id`. The core request path values must match the prerequisite service state, and the body must be a JSON query whose keys are matched under stored `payload`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused when the failure endpoint requires `{id}` or `{serviceId}`. This prerequisite state is used before the failing request: The JSON query body cannot be parsed by the repository query builder.
    - The failure-causing state or request values are present: The JSON query body cannot be parsed by the repository query builder. The core request body is not a valid JSON query document. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /dynarest/{service}/{version}/{resource}`
  - Why this fails:
    The implementation passes the body to `Document.parse`; invalid JSON raises an exception.
  - Intentionally violated constraints:
    The core request body is not a valid JSON query document.

Endpoint coverage:
- Covers:
  `GET /dynarest/{service}/{version}/{resource}`
- Distinct meaning:
  Searches generic resources using a JSON query body.

### Function 20: retrieve dynamic REST resource

Function name:
get dynamic resource

Core endpoint(s):
- `GET /dynarest/{service}/{version}/{resource}/{resourceId}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request path `{resourceId}` must be the `id` returned by the prerequisite endpoint. Service, version, and resource path values must match the prerequisite service state.

Successful execution:
- Result:
  This function retrieves one stored generic resource instance by generated resource id.
- Invocation:
  Step 1: `GET /dynarest/{service}/{version}/{resource}/{resourceId}` with required path/query/body/form/header values
- Constraints:
  The core request path `{resourceId}` must be the `id` returned by the prerequisite endpoint. Service, version, and resource path values must match the prerequisite service state.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused when the failure endpoint requires `{id}` or `{serviceId}`. This prerequisite state is used before the failing request: The resource id does not exist.
    - The failure-causing state or request values are present: The resource id does not exist. The core request uses a `{resourceId}` not produced by `POST /dynarest/{service}/{version}/{resource}`. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /dynarest/{service}/{version}/{resource}/{resourceId}`
  - Why this fails:
    The generic resource repository returns null and the controller returns `404 Not Found`.
  - Intentionally violated constraints:
    The core request uses a `{resourceId}` not produced by `POST /dynarest/{service}/{version}/{resource}`.
- Branch 2:
  - Preconditions:
    - The failure-causing state or request values are present: The service or matching `GET /{resource}/:id` operation does not exist. The generic service creation endpoint is omitted. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /dynarest/{service}/{version}/{resource}/{resourceId}`
  - Why this fails:
    `getMockContext` returns null and the controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The generic service creation endpoint is omitted.

Endpoint coverage:
- Covers:
  `GET /dynarest/{service}/{version}/{resource}/{resourceId}`
- Distinct meaning:
  Reads one dynamic generic resource.

### Function 21: update dynamic REST resource

Function name:
update dynamic resource

Core endpoint(s):
- `PUT /dynarest/{service}/{version}/{resource}/{resourceId}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request `{resourceId}` must be the `id` returned by the prerequisite endpoint. The core request body must be valid JSON; any `id` field in the body is removed before storing.

Successful execution:
- Result:
  This function replaces the payload of one stored generic resource and returns the updated resource with its id.
- Invocation:
  Step 1: `PUT /dynarest/{service}/{version}/{resource}/{resourceId}` with required path/query/body/form/header values
- Constraints:
  The core request `{resourceId}` must be the `id` returned by the prerequisite endpoint. The core request body must be valid JSON; any `id` field in the body is removed before storing.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused when the failure endpoint requires `{id}` or `{serviceId}`. This prerequisite state is used before the failing request: The resource id does not exist.
    - The failure-causing state or request values are present: The resource id does not exist. The prerequisite resource creation endpoint is omitted or `{resourceId}` is wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `PUT /dynarest/{service}/{version}/{resource}/{resourceId}`
  - Why this fails:
    The controller returns `404 Not Found`.
  - Intentionally violated constraints:
    The prerequisite resource creation endpoint is omitted or `{resourceId}` is wrong.
- Branch 2:
  - Preconditions:
    - A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused when the failure endpoint requires `{id}` or `{serviceId}`. This prerequisite state is used before the failing request: The update body is not valid JSON.
    - A generic resource instance exists for `{service}`, `{version}`, and `{resource}`. This can be satisfied by directly inserting a linked `GenericResource` row with a JSON payload, or by calling `POST /dynarest/{service}/{version}/{resource}` with a valid JSON body; the generated resource `id` must be reused as `{resourceId}`. This prerequisite state is used before the failing request: The update body is not valid JSON.
    - The failure-causing state or request values are present: The update body is not valid JSON. The core request body is not parseable JSON. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `PUT /dynarest/{service}/{version}/{resource}/{resourceId}`
  - Why this fails:
    JSON parsing fails and the controller returns `422 Unprocessable Entity`.
  - Intentionally violated constraints:
    The core request body is not parseable JSON.

Endpoint coverage:
- Covers:
  `PUT /dynarest/{service}/{version}/{resource}/{resourceId}`
- Distinct meaning:
  Replaces one dynamic generic resource payload.

### Function 22: delete dynamic REST resource

Function name:
delete dynamic resource

Core endpoint(s):
- `DELETE /dynarest/{service}/{version}/{resource}/{resourceId}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request `{resourceId}` must be the `id` returned by the prerequisite endpoint. Service, version, and resource must match the prerequisite service state.

Successful execution:
- Result:
  This function deletes one stored generic resource instance.
- Invocation:
  Step 1: `DELETE /dynarest/{service}/{version}/{resource}/{resourceId}` with required path/query/body/form/header values
- Constraints:
  The core request `{resourceId}` must be the `id` returned by the prerequisite endpoint. Service, version, and resource must match the prerequisite service state.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The service or matching `DELETE /{resource}/:id` operation does not exist. The generic service creation endpoint is omitted or mismatched. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `DELETE /dynarest/{service}/{version}/{resource}/{resourceId}`
  - Why this fails:
    `getMockContext` returns null and the controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The generic service creation endpoint is omitted or mismatched.

Endpoint coverage:
- Covers:
  `DELETE /dynarest/{service}/{version}/{resource}/{resourceId}`
- Distinct meaning:
  Deletes a dynamic generic resource.

### Function 23: list stored generic resource records

Function name:
list generic resource records

Core endpoint(s):
- `GET /api/genericresources/service/{serviceId}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request `{serviceId}` must be the service `id` returned by the prerequisite endpoint. Optional `page` defaults to `0`; optional `size` defaults to `10`.

Successful execution:
- Result:
  This function lists persisted `GenericResource` records for a generic REST service, adding each record id into its payload.
- Invocation:
  Step 1: `GET /api/genericresources/service/{serviceId}` with required path/query/body/form/header values
- Constraints:
  The core request `{serviceId}` must be the service `id` returned by the prerequisite endpoint. Optional `page` defaults to `0`; optional `size` defaults to `10`.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/genericresources/service/{serviceId}`
- Distinct meaning:
  Administrative browsing of generic resources by service id.

### Function 24: count stored generic resources

Function name:
count generic resources

Core endpoint(s):
- `GET /api/genericresources/service/{serviceId}/count`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request `{serviceId}` must be the service `id` returned by the prerequisite endpoint.

Successful execution:
- Result:
  This function counts persisted `GenericResource` records for a service.
- Invocation:
  Step 1: `GET /api/genericresources/service/{serviceId}/count` with required path/query/body/form/header values
- Constraints:
  The core request `{serviceId}` must be the service `id` returned by the prerequisite endpoint.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/genericresources/service/{serviceId}/count`
- Distinct meaning:
  Counts generic resources by service id.

### Function 25: generate generic REST OpenAPI or Swagger contract

Function name:
generate generic REST contract

Core endpoint(s):
- `GET /api/resources/{serviceId}/{resourceType}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request `{serviceId}` must be the service `id` returned by the prerequisite endpoint. `{resourceType}` must be `swagger_20` or `openapi_30`. The service must be type `GENERIC_REST`.

Successful execution:
- Result:
  This function generates a Swagger 2.0 or OpenAPI 3.0 YAML contract for a generic REST service.
- Invocation:
  Step 1: `GET /api/resources/{serviceId}/{resourceType}` with required path/query/body/form/header values
- Constraints:
  The core request `{serviceId}` must be the service `id` returned by the prerequisite endpoint. `{resourceType}` must be `swagger_20` or `openapi_30`. The service must be type `GENERIC_REST`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The service does not exist, is not `GENERIC_REST`, or the resource type is not supported. The generic REST service creation endpoint is omitted, or `{resourceType}` is not `swagger_20` or `openapi_30`. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/resources/{serviceId}/{resourceType}`
  - Why this fails:
    The controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The generic REST service creation endpoint is omitted, or `{resourceType}` is not `swagger_20` or `openapi_30`.

Endpoint coverage:
- Covers:
  `GET /api/resources/{serviceId}/{resourceType}`
- Distinct meaning:
  Generates a contract document for dynamic REST resources.

### Function 26: retrieve resource artifact by name

Function name:
get resource by name

Core endpoint(s):
- `GET /api/resources/{name}`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite artifact import must create a main artifact. The core request `{name}` must equal the stored artifact resource name. The controller returns content type according to file extension.

Successful execution:
- Result:
  This function retrieves the content of a stored main artifact resource by resource name.
- Invocation:
  Step 1: `GET /api/resources/{name}` with required path/query/body/form/header values
- Constraints:
  The request must import a main artifact. The core request `{name}` must equal the stored artifact resource name. The controller returns content type according to file extension.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: No resource with that name exists or no matching main artifact exists. The artifact import endpoint is omitted or `{name}` does not match the imported resource. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/resources/{name}`
  - Why this fails:
    The controller returns `404 Not Found`.
  - Intentionally violated constraints:
    The artifact import endpoint is omitted or `{name}` does not match the imported resource.

Endpoint coverage:
- Covers:
  `GET /api/resources/{name}`
- Distinct meaning:
  Downloads a stored resource by name.

### Function 27: retrieve resource artifact by id

Function name:
get resource by id

Core endpoint(s):
- `GET /api/resources/id/{id}`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The lookup request finds the imported service. The prerequisite lookup uses that service `id` and returns resource ids. The core request `{id}` must be a resource id returned by the prerequisite endpoint.
- The target resource id and resource metadata for `{serviceId}` are known. This can be satisfied by directly reading or inserting the linked `Resource` records in the database, or by calling `GET /api/resources/service/{serviceId}` with the service id and reusing the returned resource `id` or `name` in the core endpoint. The lookup request finds the imported service. The prerequisite lookup uses that service `id` and returns resource ids. The core request `{id}` must be a resource id returned by the prerequisite endpoint.

Successful execution:
- Result:
  This function retrieves the content of a stored resource by resource id.
- Invocation:
  Step 1: `GET /api/resources/id/{id}` with required path/query/body/form/header values
- Constraints:
  The lookup request finds the imported service. The prerequisite lookup uses that service `id` and returns resource ids. The core request `{id}` must be a resource id returned by the prerequisite endpoint.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The resource id does not exist. The resource-producing import and lookup sequence is omitted. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/resources/id/{id}`
  - Why this fails:
    The controller returns `404 Not Found`.
  - Intentionally violated constraints:
    The resource-producing import and lookup sequence is omitted.

Endpoint coverage:
- Covers:
  `GET /api/resources/id/{id}`
- Distinct meaning:
  Downloads a stored resource by id.

### Function 28: list resources for a service

Function name:
list service resources

Core endpoint(s):
- `GET /api/resources/service/{serviceId}`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The lookup request locates the imported service. The core request `{serviceId}` must be the service id returned by the prerequisite endpoint.

Successful execution:
- Result:
  This function lists stored resources associated with a service.
- Invocation:
  Step 1: `GET /api/resources/service/{serviceId}` with required path/query/body/form/header values
- Constraints:
  The lookup request locates the imported service. The core request `{serviceId}` must be the service id returned by the prerequisite endpoint.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/resources/service/{serviceId}`
- Distinct meaning:
  Lists artifacts attached to a service.

### Function 29: render API documentation

Function name:
render documentation

Core endpoint(s):
- `GET /api/documentation/{name}/{resourceType}`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite artifact import creates a main artifact. The core request `{name}` must match the resource name for meaningful rendering. `{resourceType}` must be `OPEN_API_SPEC`, `SWAGGER`, or `ASYNC_API_SPEC`. For AsyncAPI, the implementation requires the resource to exist and chooses the v2 or v3 template based on content.

Successful execution:
- Result:
  This function returns an HTML documentation page for a stored OpenAPI, Swagger, or AsyncAPI resource.
- Invocation:
  Step 1: `GET /api/documentation/{name}/{resourceType}` with required path/query/body/form/header values
- Constraints:
  The prerequisite artifact import creates a main artifact. The core request `{name}` must match the resource name for meaningful rendering. `{resourceType}` must be `OPEN_API_SPEC`, `SWAGGER`, or `ASYNC_API_SPEC`. For AsyncAPI, the implementation requires the resource to exist and chooses the v2 or v3 template based on content.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: `{resourceType}` is `ASYNC_API_SPEC` but the named resource is missing or not a main artifact. The AsyncAPI resource import is omitted or `{name}` does not identify a main artifact. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/documentation/{name}/{resourceType}`
  - Why this fails:
    The controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The AsyncAPI resource import is omitted or `{name}` does not identify a main artifact.
- Branch 2:
  - Preconditions:
    - The failure-causing state or request values are present: The resource type is unsupported. `{resourceType}` is not one of the supported documentation resource types. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/documentation/{name}/{resourceType}`
  - Why this fails:
    No template is selected and the controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    `{resourceType}` is not one of the supported documentation resource types.

Endpoint coverage:
- Covers:
  `GET /api/documentation/{name}/{resourceType}`
- Distinct meaning:
  Renders documentation by resource name.

### Function 30: render API documentation by resource id

Function name:
render documentation by id

Core endpoint(s):
- `GET /api/documentation/id/{id}/{resourceType}`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The core request `{id}` must be a resource id returned by the prerequisite endpoint. `{resourceType}` must be `OPEN_API_SPEC`, `SWAGGER`, or `ASYNC_API_SPEC`.
- The target resource id and resource metadata for `{serviceId}` are known. This can be satisfied by directly reading or inserting the linked `Resource` records in the database, or by calling `GET /api/resources/service/{serviceId}` with the service id and reusing the returned resource `id` or `name` in the core endpoint. The core request `{id}` must be a resource id returned by the prerequisite endpoint. `{resourceType}` must be `OPEN_API_SPEC`, `SWAGGER`, or `ASYNC_API_SPEC`.

Successful execution:
- Result:
  This function returns an HTML documentation page for a stored resource id.
- Invocation:
  Step 1: `GET /api/documentation/id/{id}/{resourceType}` with required path/query/body/form/header values
- Constraints:
  The core request `{id}` must be a resource id returned by the prerequisite endpoint. `{resourceType}` must be `OPEN_API_SPEC`, `SWAGGER`, or `ASYNC_API_SPEC`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: `{resourceType}` is `ASYNC_API_SPEC` but `{id}` does not identify an existing resource. The resource id lookup sequence is omitted or `{id}` is wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/documentation/id/{id}/{resourceType}`
  - Why this fails:
    The controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The resource id lookup sequence is omitted or `{id}` is wrong.

Endpoint coverage:
- Covers:
  `GET /api/documentation/id/{id}/{resourceType}`
- Distinct meaning:
  Renders documentation by resource id.

### Function 31: invoke REST mock without request validation

Function name:
invoke REST mock

Core endpoint(s):
- `GET /rest/{service}/{version}/**`
- `HEAD /rest/{service}/{version}/**`
- `POST /rest/{service}/{version}/**`
- `PUT /rest/{service}/{version}/**`
- `PATCH /rest/{service}/{version}/**`
- `DELETE /rest/{service}/{version}/**`
- `OPTIONS /rest/{service}/{version}/**`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite artifact import must create a REST service with an operation and response examples. The core request `{service}` and `{version}` must match the imported service. The trailing `/**` path and HTTP method must match an operation. Supported methods are `GET`, `HEAD`, `POST`, `PUT`, `PATCH`, `DELETE`, and `OPTIONS`. OpenAPI lists a required `body` query parameter for some GET variants, but implementation reads an optional request body.

Successful execution:
- Result:
  This function invokes a mocked REST operation and returns the matched mock response, including configured status, headers, templating, delay, fallback, or proxy handling.
- Invocation:
  Step 1: `{HTTP_METHOD} /rest/{service}/{version}/**` with the HTTP method, trailing path, request body, query values, and headers for the imported operation
- Constraints:
  The request must import a REST service with an operation and response examples. The core request `{service}` and `{version}` must match the imported service. The trailing `/**` path and HTTP method must match an operation. Supported methods are `GET`, `HEAD`, `POST`, `PUT`, `PATCH`, `DELETE`, and `OPTIONS`. OpenAPI lists a required `body` query parameter for some GET variants, but implementation reads an optional request body.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The service name and version do not exist. The artifact import endpoint is omitted or service/version values are wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `{HTTP_METHOD} /rest/{service}/{version}/**`
  - Why this fails:
    The controller returns `404 Not Found` with a service-not-exist message.
  - Intentionally violated constraints:
    The artifact import endpoint is omitted or service/version values are wrong.
- Branch 2:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: The service exists but no operation matches the method and path.
    - The failure-causing state or request values are present: The service exists but no operation matches the method and path. The core request uses a method/path not declared by the imported service. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `{HTTP_METHOD} /rest/{service}/{version}/**`
  - Why this fails:
    The controller returns `404 Not Found`, except an `OPTIONS` request can be handled as CORS when CORS is enabled.
  - Intentionally violated constraints:
    The core request uses a method/path not declared by the imported service.
- Branch 3:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: Parameter constraints configured on the operation are not satisfied.
    - The service operation has been overridden or configured with parameter constraints. This can be satisfied by directly updating the service operation document, or by calling `PUT /api/services/{id}/operation` with query `operationName`, an `OperationOverrideDTO` body, and manager authorization. This prerequisite state is used before the failing request: Parameter constraints configured on the operation are not satisfied.
    - The failure-causing state or request values are present: Parameter constraints configured on the operation are not satisfied. The core request omits a required constrained query/header parameter or sends a value not matching the configured regexp. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `{HTTP_METHOD} /rest/{service}/{version}/**`
  - Why this fails:
    Constraint validation returns `400 Bad Request`.
  - Intentionally violated constraints:
    The core request omits a required constrained query/header parameter or sends a value not matching the configured regexp.

Endpoint coverage:
- Covers:
  `GET /rest/{service}/{version}/**`
  `HEAD /rest/{service}/{version}/**`
  `POST /rest/{service}/{version}/**`
  `PUT /rest/{service}/{version}/**`
  `PATCH /rest/{service}/{version}/**`
  `DELETE /rest/{service}/{version}/**`
  `OPTIONS /rest/{service}/{version}/**`
- Distinct meaning:
  Executes REST mocks without validating JSON request bodies against the imported OpenAPI/Swagger schema.

### Function 32: invoke REST mock with request validation

Function name:
invoke validated REST mock

Core endpoint(s):
- `GET /rest-valid/{service}/{version}/**`
- `HEAD /rest-valid/{service}/{version}/**`
- `POST /rest-valid/{service}/{version}/**`
- `PUT /rest-valid/{service}/{version}/**`
- `PATCH /rest-valid/{service}/{version}/**`
- `DELETE /rest-valid/{service}/{version}/**`
- `OPTIONS /rest-valid/{service}/{version}/**`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite artifact import must create a REST service with an OpenAPI or Swagger resource and operation examples. The core request `{service}`, `{version}`, method, and trailing path must match the imported operation. Validation only runs when the request body is non-empty and content type is JSON.

Successful execution:
- Result:
  This function validates a JSON request body against the imported OpenAPI/Swagger schema before returning the matched mock response.
- Invocation:
  Step 1: `{HTTP_METHOD} /rest-valid/{service}/{version}/**` with the HTTP method, trailing path, JSON request body, query values, and headers for the imported operation
- Constraints:
  The request must import a REST service with an OpenAPI or Swagger resource and operation examples. The core request `{service}`, `{version}`, method, and trailing path must match the imported operation. Validation only runs when the request body is non-empty and content type is JSON.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused when the failure endpoint requires `{id}` or `{serviceId}`. This prerequisite state is used before the failing request: The service has no OpenAPI/Swagger resource for validation.
    - A generic resource instance exists for `{service}`, `{version}`, and `{resource}`. This can be satisfied by directly inserting a linked `GenericResource` row with a JSON payload, or by calling `POST /dynarest/{service}/{version}/{resource}` with a valid JSON body; the generated resource `id` must be reused as `{resourceId}`. This prerequisite state is used before the failing request: The service has no OpenAPI/Swagger resource for validation.
    - The failure-causing state or request values are present: The service has no OpenAPI/Swagger resource for validation. The service state lacks an OpenAPI/Swagger resource. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /rest-valid/{service}/{version}/**`
  - Why this fails:
    The controller returns `412 Precondition Failed`.
  - Intentionally violated constraints:
    The service state lacks an OpenAPI/Swagger resource.
- Branch 2:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: The JSON body does not conform to the request schema.
    - The failure-causing state or request values are present: The JSON body does not conform to the request schema. The core request body violates the schema for the matched operation. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /rest-valid/{service}/{version}/**`
  - Why this fails:
    Schema validation errors are returned with `400 Bad Request`.
  - Intentionally violated constraints:
    The core request body violates the schema for the matched operation.

Endpoint coverage:
- Covers:
  `GET /rest-valid/{service}/{version}/**`
  `HEAD /rest-valid/{service}/{version}/**`
  `POST /rest-valid/{service}/{version}/**`
  `PUT /rest-valid/{service}/{version}/**`
  `PATCH /rest-valid/{service}/{version}/**`
  `DELETE /rest-valid/{service}/{version}/**`
  `OPTIONS /rest-valid/{service}/{version}/**`
- Distinct meaning:
  Executes REST mocks with optional OpenAPI/Swagger body validation.

### Function 33: invoke SOAP mock

Function name:
invoke SOAP mock

Core endpoint(s):
- `POST /soap/{service}/{version}/**`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite artifact import must create a SOAP service with WSDL/SOAP examples. The core request `{service}` and `{version}` must match that service. Body must contain a SOAP envelope. Optional query `validate=true` requires a WSDL resource. Optional `delay` or `x-microcks-delay` controls response delay.

Successful execution:
- Result:
  This function invokes a mocked SOAP operation selected by `SOAPAction`, SOAP 1.2 `action`, or the SOAP body operation element, then returns a matched SOAP response or fault.
- Invocation:
  Step 1: `POST /soap/{service}/{version}/**` with required path/query/body/form/header values
- Constraints:
  The request must import a SOAP service with WSDL/SOAP examples. The core request `{service}` and `{version}` must match that service. Body must contain a SOAP envelope. Optional query `validate=true` requires a WSDL resource. Optional `delay` or `x-microcks-delay` controls response delay.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The SOAP service does not exist. The artifact import endpoint is omitted or service/version values are wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /soap/{service}/{version}/**`
  - Why this fails:
    The controller returns `404 Not Found`.
  - Intentionally violated constraints:
    The artifact import endpoint is omitted or service/version values are wrong.
- Branch 2:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: `validate=true` but the service has no WSDL resource.
    - The failure-causing state or request values are present: `validate=true` but the service has no WSDL resource. The imported or created service lacks a WSDL resource. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /soap/{service}/{version}/**`
  - Why this fails:
    The controller returns `412 Precondition Failed`.
  - Intentionally violated constraints:
    The imported or created service lacks a WSDL resource.
- Branch 3:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: No response matches the computed dispatch criteria.
    - The failure-causing state or request values are present: No response matches the computed dispatch criteria. The core request body or action does not match any stored SOAP response dispatch criteria. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /soap/{service}/{version}/**`
  - Why this fails:
    The controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The core request body or action does not match any stored SOAP response dispatch criteria.

Endpoint coverage:
- Covers:
  `POST /soap/{service}/{version}/**`
- Distinct meaning:
  Executes SOAP mocks and optional SOAP schema validation.

### Function 34: execute GraphQL mock operation

Function name:
execute GraphQL mock

Core endpoint(s):
- `GET /graphql/{service}/{version}/**`
- `POST /graphql/{service}/{version}/**`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite artifact import must create a GraphQL service with schema and examples. The core request may also be `GET /graphql/{service}/{version}/**`. `{service}` and `{version}` must match the imported service. The request must contain a parseable GraphQL query. Selected fields must correspond to service operations.

Successful execution:
- Result:
  This function executes a GraphQL query or mutation against a mocked GraphQL service and returns data filtered to the requested selection set.
- Invocation:
  Step 1: `GET /graphql/{service}/{version}/**` or `POST /graphql/{service}/{version}/**` with the GraphQL query, variables, operation name, and headers required for this function
- Constraints:
  The request must import a GraphQL service with schema and examples. The core request may also be `GET /graphql/{service}/{version}/**`. `{service}` and `{version}` must match the imported service. The request must contain a parseable GraphQL query. Selected fields must correspond to service operations.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The GraphQL service does not exist. The artifact import endpoint is omitted or service/version values are wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /graphql/{service}/{version}/**`
  - Why this fails:
    The controller returns `404 Not Found`.
  - Intentionally violated constraints:
    The artifact import endpoint is omitted or service/version values are wrong.
- Branch 2:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: The GraphQL request cannot be parsed.
    - The failure-causing state or request values are present: The GraphQL request cannot be parsed. The core request body or query string is not a valid GraphQL HTTP request. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /graphql/{service}/{version}/**`
  - Why this fails:
    The controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The core request body or query string is not a valid GraphQL HTTP request.
- Branch 3:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: A selected field does not map to a service operation.
    - The failure-causing state or request values are present: A selected field does not map to a service operation. The core request selects an operation name not imported as a GraphQL operation. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /graphql/{service}/{version}/**`
  - Why this fails:
    The controller returns `404 Not Found`.
  - Intentionally violated constraints:
    The core request selects an operation name not imported as a GraphQL operation.

Endpoint coverage:
- Covers:
  `GET /graphql/{service}/{version}/**`
  `POST /graphql/{service}/{version}/**`
- Distinct meaning:
  Executes regular GraphQL mock queries and mutations.

### Function 35: perform GraphQL schema introspection

Function name:
introspect GraphQL schema

Core endpoint(s):
- `GET /graphql/{service}/{version}/**`
- `POST /graphql/{service}/{version}/**`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite state must be a GraphQL `QUERY` whose first selection is `__schema`. The service must have a `GRAPHQL_SCHEMA` resource.

Successful execution:
- Result:
  This function returns GraphQL introspection output for a mocked GraphQL service.
- Invocation:
  Step 1: `GET /graphql/{service}/{version}/**` or `POST /graphql/{service}/{version}/**` with the GraphQL query, variables, operation name, and headers required for this function
- Constraints:
  The request must be a GraphQL `QUERY` whose first selection is `__schema`. The service must have a `GRAPHQL_SCHEMA` resource.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: The service lacks a GraphQL schema resource.
    - The failure-causing state or request values are present: The service lacks a GraphQL schema resource. The imported service lacks a GraphQL schema resource. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /graphql/{service}/{version}/**`
  - Why this fails:
    The implementation reads the first `GRAPHQL_SCHEMA` resource; if none exists, processing fails.
  - Intentionally violated constraints:
    The imported service lacks a GraphQL schema resource.

Endpoint coverage:
- Covers:
  `GET /graphql/{service}/{version}/**`
  `POST /graphql/{service}/{version}/**`
- Distinct meaning:
  Handles GraphQL introspection instead of matching a mocked operation.

### Function 36: answer GraphQL typename query

Function name:
answer GraphQL typename

Core endpoint(s):
- `GET /graphql/{service}/{version}/**`
- `POST /graphql/{service}/{version}/**`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The core request selected field must be `__typename`. The response value depends on whether the GraphQL operation type is query or mutation.

Successful execution:
- Result:
  This function returns `Query` or `Mutation` for a GraphQL `__typename` selection.
- Invocation:
  Step 1: `GET /graphql/{service}/{version}/**` or `POST /graphql/{service}/{version}/**` with the GraphQL query, variables, operation name, and headers required for this function
- Constraints:
  The core request selected field must be `__typename`. The response value depends on whether the GraphQL operation type is query or mutation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The service does not exist or the request is not parseable GraphQL. The imported GraphQL service or parseable request is omitted. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /graphql/{service}/{version}/**`
  - Why this fails:
    The controller returns `404 Not Found` for missing service or `400 Bad Request` for parse errors.
  - Intentionally violated constraints:
    The imported GraphQL service or parseable request is omitted.

Endpoint coverage:
- Covers:
  `GET /graphql/{service}/{version}/**`
  `POST /graphql/{service}/{version}/**`
- Distinct meaning:
  Special-cases the GraphQL meta field without operation lookup.

### Function 37: open MCP SSE session

Function name:
open MCP SSE session

Core endpoint(s):
- `GET /mcp/{service}/{version}/sse`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function opens an SSE channel and emits an endpoint event containing a generated `sessionId` and message URL.
- Invocation:
  Step 1: `GET /mcp/{service}/{version}/sse` with required path/query/body/form/header values
- Constraints:
  `{service}` has `+` converted to space. The implementation does not validate the service at this step. The emitted endpoint URL contains `sessionId`, which is required by `POST /mcp/{service}/{version}/message`.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /mcp/{service}/{version}/sse`
- Distinct meaning:
  Establishes SSE transport for MCP messages.

### Function 38: initialize MCP server

Function name:
initialize MCP

Core endpoint(s):
- `POST /mcp/{service}/{version}`
- `POST /mcp/{service}/{version}/message`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The core request body is a JSON-RPC request with method `initialize`. `{service}` and `{version}` must match an imported service. Protocol version must be one of the supported MCP protocol versions.

Successful execution:
- Result:
  This function returns MCP server capabilities and server information for a mocked service.
- Invocation:
  Step 1: `POST /mcp/{service}/{version}` with the JSON-RPC body, or `POST /mcp/{service}/{version}/message` with the same JSON-RPC body and a valid SSE `sessionId` when using the message transport
- Constraints:
  The core request body is a JSON-RPC request with method `initialize`. `{service}` and `{version}` must match an imported service. Protocol version must be one of the supported MCP protocol versions.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The service does not exist. The service-import endpoint is omitted or path values are wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /mcp/{service}/{version}`
  - Why this fails:
    `handleMcpRequest` throws `McpError`, returned as `400 Bad Request`.
  - Intentionally violated constraints:
    The service-import endpoint is omitted or path values are wrong.
- Branch 2:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: The protocol version is unsupported.
    - The failure-causing state or request values are present: The protocol version is unsupported. Request body `params.protocolVersion` is not supported. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /mcp/{service}/{version}`
  - Why this fails:
    The initialize handler returns an MCP error result for unsupported protocol.
  - Intentionally violated constraints:
    Request body `params.protocolVersion` is not supported.

Endpoint coverage:
- Covers:
  `POST /mcp/{service}/{version}`
  `POST /mcp/{service}/{version}/message`
- Distinct meaning:
  Handles MCP `initialize`; the `/message` variant requires a prior `GET /mcp/{service}/{version}/sse` and uses the emitted `sessionId`.

### Function 39: list MCP tools

Function name:
list MCP tools

Core endpoint(s):
- `POST /mcp/{service}/{version}`
- `POST /mcp/{service}/{version}/message`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The core request body is a JSON-RPC request with method `tools/list`. The service must exist. The contract resource, when present, is used to build tool names, descriptions, and input schemas.

Successful execution:
- Result:
  This function exposes service operations as MCP tools.
- Invocation:
  Step 1: `POST /mcp/{service}/{version}` with the JSON-RPC body, or `POST /mcp/{service}/{version}/message` with the same JSON-RPC body and a valid SSE `sessionId` when using the message transport
- Constraints:
  The core request body is a JSON-RPC request with method `tools/list`. The service must exist. The contract resource, when present, is used to build tool names, descriptions, and input schemas.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The service does not exist. The service import endpoint is omitted or path values are wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /mcp/{service}/{version}`
  - Why this fails:
    The endpoint returns `400 Bad Request` with an MCP error.
  - Intentionally violated constraints:
    The service import endpoint is omitted or path values are wrong.

Endpoint coverage:
- Covers:
  `POST /mcp/{service}/{version}`
  `POST /mcp/{service}/{version}/message`
- Distinct meaning:
  Handles MCP `tools/list`; the SSE `/message` variant requires a valid `sessionId`.

### Function 40: call MCP tool

Function name:
call MCP tool

Core endpoint(s):
- `POST /mcp/{service}/{version}`
- `POST /mcp/{service}/{version}/message`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The core request body is a JSON-RPC request with method `tools/call`. `params.name` must equal a tool name generated from one imported service operation. `params.arguments` must satisfy the converter for that service type.

Successful execution:
- Result:
  This function invokes a mocked operation through MCP `tools/call` and returns the selected mock response content as MCP text content.
- Invocation:
  Step 1: `POST /mcp/{service}/{version}` with the JSON-RPC body, or `POST /mcp/{service}/{version}/message` with the same JSON-RPC body and a valid SSE `sessionId` when using the message transport
- Constraints:
  The core request body is a JSON-RPC request with method `tools/call`. `params.name` must equal a tool name generated from one imported service operation. `params.arguments` must satisfy the converter for that service type.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: The requested tool name is unknown.
    - The failure-causing state or request values are present: The requested tool name is unknown. `params.name` does not match any generated tool name. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /mcp/{service}/{version}`
  - Why this fails:
    The handler returns an MCP error result for unknown tool name.
  - Intentionally violated constraints:
    `params.name` does not match any generated tool name.
- Branch 2:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: The SSE message session id is missing or unknown.
    - The failure-causing state or request values are present: The SSE message session id is missing or unknown. The prerequisite `GET /mcp/{service}/{version}/sse` is omitted or the core request uses the wrong `sessionId`. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /mcp/{service}/{version}/message`
  - Why this fails:
    The message endpoint returns `400 Bad Request` with `Session not found`.
  - Intentionally violated constraints:
    The prerequisite `GET /mcp/{service}/{version}/sse` is omitted or the core request uses the wrong `sessionId`.

Endpoint coverage:
- Covers:
  `POST /mcp/{service}/{version}`
  `POST /mcp/{service}/{version}/message`
- Distinct meaning:
  Handles MCP `tools/call`.

### Function 41: create secret

Function name:
create secret

Core endpoint(s):
- `POST /api/secrets`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function stores a secret containing credentials, token, token header, or CA certificate data.
- Invocation:
  Step 1: `POST /api/secrets` with required path/query/body/form/header values
- Constraints:
  Body is `Secret`; common fields include `name`, `description`, `username`, `password`, `token`, `tokenHeader`, and `caCertPem`.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `POST /api/secrets`
- Distinct meaning:
  Creates a reusable secret for imports or tests.

### Function 42: list secrets

Function name:
list secrets

Core endpoint(s):
- `GET /api/secrets`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns paginated secrets; non-admin users receive secrets with sensitive fields cleared.
- Invocation:
  Step 1: `GET /api/secrets` with required path/query/body/form/header values
- Constraints:
  Optional `page` defaults to `0`; optional `size` defaults to `20`. If the current user is not admin, `username`, `password`, `token`, and `caCertPem` are nulled in returned objects.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/secrets`
- Distinct meaning:
  Lists secrets with role-dependent redaction.

### Function 43: search secrets

Function name:
search secrets

Core endpoint(s):
- `GET /api/secrets/search`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function searches secrets by name-like match.
- Invocation:
  Step 1: `GET /api/secrets/search` with required path/query/body/form/header values
- Constraints:
  Query `name` is required by the OpenAPI contract and passed to `findByNameLike`.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/secrets/search`
- Distinct meaning:
  Searches secrets by name.

### Function 44: count secrets

Function name:
count secrets

Core endpoint(s):
- `GET /api/secrets/count`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns the total secret count as `{ "counter": n }`.
- Invocation:
  Step 1: `GET /api/secrets/count` with required path/query/body/form/header values
- Constraints:
  No prerequisite resource state is required.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/secrets/count`
- Distinct meaning:
  Counts all secrets.

### Function 45: retrieve secret

Function name:
get secret

Core endpoint(s):
- `GET /api/secrets/{id}`

Preconditions:
- A secret exists with the credentials, token, token header, or CA certificate data required by this function. This can be satisfied by directly inserting a `Secret` record, or by calling `POST /api/secrets` with the required `Secret` body. The generated secret `id` or `name` must be reused by the core endpoint when it references the secret. The core request `{id}` must be the secret `id` returned by the prerequisite endpoint. Admin users receive full data; non-admin users receive sensitive fields cleared.

Successful execution:
- Result:
  This function retrieves one secret by id; non-admin users receive a redacted secret.
- Invocation:
  Step 1: `GET /api/secrets/{id}` with required path/query/body/form/header values
- Constraints:
  The core request `{id}` must be the secret `id` returned by the prerequisite endpoint. Admin users receive full data; non-admin users receive sensitive fields cleared.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The secret id does not exist. The prerequisite `POST /api/secrets` is omitted or `{id}` is wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/secrets/{id}`
  - Why this fails:
    The implementation returns `200 OK` with null rather than `404`.
  - Intentionally violated constraints:
    The prerequisite `POST /api/secrets` is omitted or `{id}` is wrong.

Endpoint coverage:
- Covers:
  `GET /api/secrets/{id}`
- Distinct meaning:
  Reads a secret with role-dependent sensitive data filtering.

### Function 46: update secret

Function name:
update secret

Core endpoint(s):
- `PUT /api/secrets/{id}`

Preconditions:
- A secret exists with the credentials, token, token header, or CA certificate data required by this function. This can be satisfied by directly inserting a `Secret` record, or by calling `POST /api/secrets` with the required `Secret` body. The generated secret `id` or `name` must be reused by the core endpoint when it references the secret. The core request `{id}` should identify the secret returned by the prerequisite endpoint. The implementation saves the body `Secret`; it does not explicitly reconcile the path id with the body id.

Successful execution:
- Result:
  This function saves replacement secret content.
- Invocation:
  Step 1: `PUT /api/secrets/{id}` with required path/query/body/form/header values
- Constraints:
  The core request `{id}` should identify the secret returned by the prerequisite endpoint. The implementation saves the body `Secret`; it does not explicitly reconcile the path id with the body id.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `PUT /api/secrets/{id}`
- Distinct meaning:
  Updates stored secret data.

### Function 47: delete secret

Function name:
delete secret

Core endpoint(s):
- `DELETE /api/secrets/{id}`

Preconditions:
- A secret exists with the credentials, token, token header, or CA certificate data required by this function. This can be satisfied by directly inserting a `Secret` record, or by calling `POST /api/secrets` with the required `Secret` body. The generated secret `id` or `name` must be reused by the core endpoint when it references the secret. The core request `{id}` must be the secret `id` returned by the prerequisite request for an actual deletion.

Successful execution:
- Result:
  This function deletes a stored secret by id.
- Invocation:
  Step 1: `DELETE /api/secrets/{id}` with required path/query/body/form/header values
- Constraints:
  The core request `{id}` must be the secret `id` returned by the prerequisite request for an actual deletion.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `DELETE /api/secrets/{id}`
- Distinct meaning:
  Deletes a secret.

### Function 48: create import job

Function name:
create job

Core endpoint(s):
- `POST /api/jobs`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function creates an import job and initializes metadata and creation date.
- Invocation:
  Step 1: `POST /api/jobs` with required path/query/body/form/header values
- Constraints:
  Body is `ImportJob`. Labels from incoming metadata are preserved, but metadata is otherwise reinitialized. `createdDate` is set by the server.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `POST /api/jobs`
- Distinct meaning:
  Creates a reusable import job.

### Function 49: list jobs

Function name:
list jobs

Core endpoint(s):
- `GET /api/jobs`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns paginated jobs or, when `name` is supplied, jobs matching the name.
- Invocation:
  Step 1: `GET /api/jobs` with required path/query/body/form/header values
- Constraints:
  Optional `page` defaults to `0`; `size` defaults to `20`. Optional `name` switches implementation to name-like search.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/jobs`
- Distinct meaning:
  Lists or name-filters import jobs.

### Function 50: search jobs

Function name:
search jobs

Core endpoint(s):
- `GET /api/jobs/search`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function searches jobs by name, labels, or both.
- Invocation:
  Step 1: `GET /api/jobs/search` with required path/query/body/form/header values
- Constraints:
  Query `name` performs name-like search. Query parameters prefixed with `labels.` are converted into label filters.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/jobs/search`
- Distinct meaning:
  Searches import jobs using query-map filters.

### Function 51: count jobs

Function name:
count jobs

Core endpoint(s):
- `GET /api/jobs/count`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns the total job count as `{ "counter": n }`.
- Invocation:
  Step 1: `GET /api/jobs/count` with required path/query/body/form/header values
- Constraints:
  No prerequisite resource state is required.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/jobs/count`
- Distinct meaning:
  Counts import jobs.

### Function 52: retrieve job

Function name:
get job

Core endpoint(s):
- `GET /api/jobs/{id}`

Preconditions:
- An import job exists with the required name, repository URL, metadata, active flag, and optional secret reference. This can be satisfied by directly inserting an `ImportJob` record, or by calling `POST /api/jobs` with an `ImportJob` body. The generated job `id` must be reused as `{id}` in the core endpoint. The core request `{id}` must be the job `id` returned by the prerequisite endpoint.

Successful execution:
- Result:
  This function retrieves one import job by id.
- Invocation:
  Step 1: `GET /api/jobs/{id}` with required path/query/body/form/header values
- Constraints:
  The core request `{id}` must be the job `id` returned by the prerequisite endpoint.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The job id does not exist. The prerequisite `POST /api/jobs` is omitted or `{id}` is wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/jobs/{id}`
  - Why this fails:
    The controller returns `200 OK` with null rather than `404`.
  - Intentionally violated constraints:
    The prerequisite `POST /api/jobs` is omitted or `{id}` is wrong.

Endpoint coverage:
- Covers:
  `GET /api/jobs/{id}`
- Distinct meaning:
  Reads an import job.

### Function 53: update job

Function name:
update job

Core endpoint(s):
- `POST /api/jobs/{id}`

Preconditions:
- An import job exists with the required name, repository URL, metadata, active flag, and optional secret reference. This can be satisfied by directly inserting an `ImportJob` record, or by calling `POST /api/jobs` with an `ImportJob` body. The generated job `id` must be reused as `{id}` in the core endpoint. The core request should use the job id returned by the prerequisite endpoint. Body is the replacement `ImportJob`. The implementation ignores the path id and saves the body job. OpenAPI path includes `{id}`, but the generated operation parameters omit `id`; this matches the implementation not using the path id. User must be admin or manager for the job.

Successful execution:
- Result:
  This function saves an existing import job and updates its metadata timestamp.
- Invocation:
  Step 1: `POST /api/jobs/{id}` with required path/query/body/form/header values
- Constraints:
  The core request should use the job id returned by the prerequisite endpoint. Body is the replacement `ImportJob`. The implementation ignores the path id and saves the body job. OpenAPI path includes `{id}`, but the generated operation parameters omit `id`; this matches the implementation not using the path id. User must be admin or manager for the job.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An import job exists with the required repository URL, metadata, active flag, and optional secret reference. This can be satisfied by directly inserting an `ImportJob` record, or by calling `POST /api/jobs` with an `ImportJob` body and reusing the generated job `id`. This prerequisite state is used before the failing request: The user is not admin or manager for the job.
    - The failure-causing state or request values are present: The user is not admin or manager for the job. The core request uses a `UserInfo` without admin or manager access. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/jobs/{id}`
  - Why this fails:
    Authorization check fails and the controller returns `403 Forbidden`.
  - Intentionally violated constraints:
    The core request uses a `UserInfo` without admin or manager access.

Endpoint coverage:
- Covers:
  `POST /api/jobs/{id}`
- Distinct meaning:
  Updates an import job.

### Function 54: activate job

Function name:
activate job

Core endpoint(s):
- `PUT /api/jobs/{id}/activate`

Preconditions:
- An import job exists with the required name, repository URL, metadata, active flag, and optional secret reference. This can be satisfied by directly inserting an `ImportJob` record, or by calling `POST /api/jobs` with an `ImportJob` body. The generated job `id` must be reused as `{id}` in the core endpoint. The core request `{id}` must be the job `id` returned by the prerequisite endpoint. User must be admin or manager for the job.

Successful execution:
- Result:
  This function marks an import job as active and persists it.
- Invocation:
  Step 1: `PUT /api/jobs/{id}/activate` with required path/query/body/form/header values
- Constraints:
  The core request `{id}` must be the job `id` returned by the prerequisite endpoint. User must be admin or manager for the job.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The job does not exist or user lacks permission. The prerequisite job creation is omitted or the user is unauthorized. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `PUT /api/jobs/{id}/activate`
  - Why this fails:
    The controller returns `403 Forbidden`.
  - Intentionally violated constraints:
    The prerequisite job creation is omitted or the user is unauthorized.

Endpoint coverage:
- Covers:
  `PUT /api/jobs/{id}/activate`
- Distinct meaning:
  Enables scheduled imports for a job.

### Function 55: start job immediately

Function name:
start job

Core endpoint(s):
- `PUT /api/jobs/{id}/start`

Preconditions:
- An import job exists with the required name, repository URL, metadata, active flag, and optional secret reference. This can be satisfied by directly inserting an `ImportJob` record, or by calling `POST /api/jobs` with an `ImportJob` body. The generated job `id` must be reused as `{id}` in the core endpoint. The core request body must include a usable `repositoryUrl` for a meaningful import. If `secretRef` is set, it must refer to an existing secret. The core request `{id}` must be the job `id` from the prerequisite endpoint. User must be admin or manager for the job.

Successful execution:
- Result:
  This function marks a job active and immediately runs its import process from the configured repository URL.
- Invocation:
  Step 1: `PUT /api/jobs/{id}/start` with required path/query/body/form/header values
- Constraints:
  The core request body must include a usable `repositoryUrl` for a meaningful import. If `secretRef` is set, it must refer to an existing secret. The core request `{id}` must be the job `id` from the prerequisite endpoint. User must be admin or manager for the job.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An import job exists with the required repository URL, metadata, active flag, and optional secret reference. This can be satisfied by directly inserting an `ImportJob` record, or by calling `POST /api/jobs` with an `ImportJob` body and reusing the generated job `id`. This prerequisite state is used before the failing request: User lacks permission.
    - The failure-causing state or request values are present: User lacks permission. The core request uses a user without admin or manager access. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `PUT /api/jobs/{id}/start`
  - Why this fails:
    The controller returns `403 Forbidden`.
  - Intentionally violated constraints:
    The core request uses a user without admin or manager access.
- Branch 2:
  - Preconditions:
    - An import job exists with the required repository URL, metadata, active flag, and optional secret reference. This can be satisfied by directly inserting an `ImportJob` record, or by calling `POST /api/jobs` with an `ImportJob` body and reusing the generated job `id`. This prerequisite state is used before the failing request: The repository import fails.
    - The failure-causing state or request values are present: The repository import fails. The prerequisite job `repositoryUrl` points to an unsupported or unreachable artifact. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `PUT /api/jobs/{id}/start`
  - Why this fails:
    The HTTP response still returns `200 OK`, but `lastImportError` is recorded on the job.
  - Intentionally violated constraints:
    The prerequisite job `repositoryUrl` points to an unsupported or unreachable artifact.

Endpoint coverage:
- Covers:
  `PUT /api/jobs/{id}/start`
- Distinct meaning:
  Runs a configured import job now.

### Function 56: stop job

Function name:
stop job

Core endpoint(s):
- `PUT /api/jobs/{id}/stop`

Preconditions:
- An import job exists with the required name, repository URL, metadata, active flag, and optional secret reference. This can be satisfied by directly inserting an `ImportJob` record, or by calling `POST /api/jobs` with an `ImportJob` body. The generated job `id` must be reused as `{id}` in the core endpoint. The core request `{id}` must be the job `id` from the prerequisite endpoint. User must be admin or manager for the job.

Successful execution:
- Result:
  This function marks an import job inactive.
- Invocation:
  Step 1: `PUT /api/jobs/{id}/stop` with required path/query/body/form/header values
- Constraints:
  The core request `{id}` must be the job `id` from the prerequisite endpoint. User must be admin or manager for the job.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The job does not exist or the user lacks permission. The prerequisite job creation is omitted or user lacks access. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `PUT /api/jobs/{id}/stop`
  - Why this fails:
    The controller returns `403 Forbidden`.
  - Intentionally violated constraints:
    The prerequisite job creation is omitted or user lacks access.

Endpoint coverage:
- Covers:
  `PUT /api/jobs/{id}/stop`
- Distinct meaning:
  Disables an import job.

### Function 57: delete job

Function name:
delete job

Core endpoint(s):
- `DELETE /api/jobs/{id}`

Preconditions:
- An import job exists with the required name, repository URL, metadata, active flag, and optional secret reference. This can be satisfied by directly inserting an `ImportJob` record, or by calling `POST /api/jobs` with an `ImportJob` body. The generated job `id` must be reused as `{id}` in the core endpoint. The core request `{id}` must be the job `id` from the prerequisite endpoint. User must be admin or manager for the job.

Successful execution:
- Result:
  This function deletes an import job.
- Invocation:
  Step 1: `DELETE /api/jobs/{id}` with required path/query/body/form/header values
- Constraints:
  The core request `{id}` must be the job `id` from the prerequisite endpoint. User must be admin or manager for the job.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - An import job exists with the required repository URL, metadata, active flag, and optional secret reference. This can be satisfied by directly inserting an `ImportJob` record, or by calling `POST /api/jobs` with an `ImportJob` body and reusing the generated job `id`. This prerequisite state is used before the failing request: User lacks permission.
    - The failure-causing state or request values are present: User lacks permission. The core request uses a user without admin or manager access. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `DELETE /api/jobs/{id}`
  - Why this fails:
    The controller returns `403 Forbidden`.
  - Intentionally violated constraints:
    The core request uses a user without admin or manager access.

Endpoint coverage:
- Covers:
  `DELETE /api/jobs/{id}`
- Distinct meaning:
  Removes an import job.

### Function 58: launch service test

Function name:
launch test

Core endpoint(s):
- `POST /api/tests`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request body `serviceId` must be the service id returned by the prerequisite request or a supported service id form. Body `runnerType` must be a valid `TestRunnerType`; `testEndpoint` is the endpoint to test. Optional `secretName` is looked up and converted into `SecretRef` if found.

Successful execution:
- Result:
  This function creates a `TestResult` for a service and asynchronously launches test execution against a target endpoint.
- Invocation:
  Step 1: `POST /api/tests` with required path/query/body/form/header values
- Constraints:
  The core request body `serviceId` must be the service id returned by the prerequisite request or a supported service id form. Body `runnerType` must be a valid `TestRunnerType`; `testEndpoint` is the endpoint to test. Optional `secretName` is looked up and converted into `SecretRef` if found.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused when the failure endpoint requires `{id}` or `{serviceId}`. This prerequisite state is used before the failing request: `runnerType` is not a valid enum value.
    - The failure-causing state or request values are present: `runnerType` is not a valid enum value. The core request body `runnerType` is not a supported test runner type. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/tests`
  - Why this fails:
    `TestRunnerType.valueOf` throws an exception.
  - Intentionally violated constraints:
    The core request body `runnerType` is not a supported test runner type.
- Branch 2:
  - Preconditions:
    - The failure-causing state or request values are present: The service id does not resolve to a service. The service creation/import prerequisite is omitted or `serviceId` is wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/tests`
  - Why this fails:
    The service lookup returns null and later test initialization dereferences the service.
  - Intentionally violated constraints:
    The service creation/import prerequisite is omitted or `serviceId` is wrong.

Endpoint coverage:
- Covers:
  `POST /api/tests`
- Distinct meaning:
  Starts an asynchronous test run.

### Function 59: retrieve test result

Function name:
get test result

Core endpoint(s):
- `GET /api/tests/{id}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request `{id}` must be the test result `id` returned by the prerequisite endpoint.

Successful execution:
- Result:
  This function retrieves a previously created test result.
- Invocation:
  Step 1: `GET /api/tests/{id}` with required path/query/body/form/header values
- Constraints:
  The core request `{id}` must be the test result `id` returned by the prerequisite endpoint.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The test result id does not exist. The test creation endpoint is omitted or `{id}` is wrong. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/tests/{id}`
  - Why this fails:
    The controller returns `200 OK` with null rather than `404`.
  - Intentionally violated constraints:
    The test creation endpoint is omitted or `{id}` is wrong.

Endpoint coverage:
- Covers:
  `GET /api/tests/{id}`
- Distinct meaning:
  Reads one test result.

### Function 60: list tests for service

Function name:
list service tests

Core endpoint(s):
- `GET /api/tests/service/{serviceId}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request `{serviceId}` must be the service id returned by the prerequisite endpoint. Optional `page` defaults to `0`; optional `size` defaults to `20`.

Successful execution:
- Result:
  This function lists test results associated with a service.
- Invocation:
  Step 1: `GET /api/tests/service/{serviceId}` with required path/query/body/form/header values
- Constraints:
  The core request `{serviceId}` must be the service id returned by the prerequisite endpoint. Optional `page` defaults to `0`; optional `size` defaults to `20`.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/tests/service/{serviceId}`
- Distinct meaning:
  Lists test runs for a service.

### Function 61: count tests for service

Function name:
count service tests

Core endpoint(s):
- `GET /api/tests/service/{serviceId}/count`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request `{serviceId}` must be the service id returned by the prerequisite endpoint.

Successful execution:
- Result:
  This function counts test results associated with a service.
- Invocation:
  Step 1: `GET /api/tests/service/{serviceId}/count` with required path/query/body/form/header values
- Constraints:
  The core request `{serviceId}` must be the service id returned by the prerequisite endpoint.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/tests/service/{serviceId}/count`
- Distinct meaning:
  Counts test runs for a service.

### Function 62: report test case result

Function name:
report test case result

Core endpoint(s):
- `POST /api/tests/{id}/testCaseResult`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request `{id}` must be the test result `id` from the prerequisite endpoint. Body `operationName` must match a test case initialized from the service operation. Body `testReturns` determines success, elapsed time, and stored messages.

Successful execution:
- Result:
  This function records per-operation test returns, creates stored requests/responses or event messages, and updates the overall test result progress and success.
- Invocation:
  Step 1: `POST /api/tests/{id}/testCaseResult` with required path/query/body/form/header values
- Constraints:
  The core request `{id}` must be the test result `id` from the prerequisite endpoint. Body `operationName` must match a test case initialized from the service operation. Body `testReturns` determines success, elapsed time, and stored messages.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with body fields `name`, `version`, `resource`, and optional `referencePayload`; the generated service `id` must be reused when the failure endpoint requires `{id}` or `{serviceId}`. This prerequisite state is used before the failing request: `operationName` does not match any test case in the test result.
    - A test result exists for the target service. This can be satisfied by directly inserting a `TestResult` record, or by calling `POST /api/tests` with `serviceId`, `runnerType`, `testEndpoint`, and optional `secretName`; the generated test result `id` must be reused. This prerequisite state is used before the failing request: `operationName` does not match any test case in the test result.
    - The failure-causing state or request values are present: `operationName` does not match any test case in the test result. The core request body `operationName` is not one of the operations from the prerequisite service. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/tests/{id}/testCaseResult`
  - Why this fails:
    The service returns null and the controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The core request body `operationName` is not one of the operations from the prerequisite service.

Endpoint coverage:
- Covers:
  `POST /api/tests/{id}/testCaseResult`
- Distinct meaning:
  Reports a test case execution result.

### Function 63: retrieve request-response messages for a test case

Function name:
get test messages

Core endpoint(s):
- `GET /api/tests/{id}/messages/{testCaseId}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The prerequisite state must include request/response `testReturns`. The core request `{id}` is the test result id from the prerequisite endpoint. `{testCaseId}` is built as `{testResult.id}-{testResult.testNumber}-{operationName}`; it is URL decoded and `!` is converted to `/`.
- A test case result has been reported for the target test result, creating the request/response pairs or event messages that the core endpoint reads. This can be satisfied by directly inserting the matching `TestCaseResult`, request/response, or event-message records, or by calling `POST /api/tests/{id}/testCaseResult` with the test result `id`, matching `operationName`, and `testReturns`. The resulting `{testCaseId}` must match the stored test case id format. The prerequisite state must include request/response `testReturns`. The core request `{id}` is the test result id from the prerequisite endpoint. `{testCaseId}` is built as `{testResult.id}-{testResult.testNumber}-{operationName}`; it is URL decoded and `!` is converted to `/`.

Successful execution:
- Result:
  This function retrieves stored request/response pairs associated with a test case.
- Invocation:
  Step 1: `GET /api/tests/{id}/messages/{testCaseId}` with required path/query/body/form/header values
- Constraints:
  The request must include request/response `testReturns`. The core request `{id}` is the test result id from the prerequisite endpoint. `{testCaseId}` is built as `{testResult.id}-{testResult.testNumber}-{operationName}`; it is URL decoded and `!` is converted to `/`.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/tests/{id}/messages/{testCaseId}`
- Distinct meaning:
  Reads stored request/response messages for a test case.

### Function 64: retrieve event messages for a test case

Function name:
get test events

Core endpoint(s):
- `GET /api/tests/{id}/events/{testCaseId}`

Preconditions:
- A `GENERIC_EVENT` service exists with the required `name`, `version`, and event `resource`, including its `SUBSCRIBE {event}` operation and default bindings. This can be satisfied by directly inserting the `Service`, `Operation`, binding, resource, and event-message records, or by calling `POST /api/services/generic/event` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`. The prerequisite state must include event-message `testReturns`. The core request `{id}` is the test result id from the prerequisite endpoint. `{testCaseId}` is built as `{testResult.id}-{testResult.testNumber}-{operationName}`; it is URL decoded and `!` is converted to `/`.
- A test case result has been reported for the target test result, creating the request/response pairs or event messages that the core endpoint reads. This can be satisfied by directly inserting the matching `TestCaseResult`, request/response, or event-message records, or by calling `POST /api/tests/{id}/testCaseResult` with the test result `id`, matching `operationName`, and `testReturns`. The resulting `{testCaseId}` must match the stored test case id format. The prerequisite state must include event-message `testReturns`. The core request `{id}` is the test result id from the prerequisite endpoint. `{testCaseId}` is built as `{testResult.id}-{testResult.testNumber}-{operationName}`; it is URL decoded and `!` is converted to `/`.

Successful execution:
- Result:
  This function retrieves stored event messages associated with an asynchronous test case.
- Invocation:
  Step 1: `GET /api/tests/{id}/events/{testCaseId}` with required path/query/body/form/header values
- Constraints:
  The request must include event-message `testReturns`. The core request `{id}` is the test result id from the prerequisite endpoint. `{testCaseId}` is built as `{testResult.id}-{testResult.testNumber}-{operationName}`; it is URL decoded and `!` is converted to `/`.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/tests/{id}/events/{testCaseId}`
- Distinct meaning:
  Reads stored event messages for a test case.

### Function 65: get global invocation metrics

Function name:
get global invocation metrics

Core endpoint(s):
- `GET /api/metrics/invocations/global`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function flushes pending invocation statistics and returns aggregate invocation metrics for one day.
- Invocation:
  Step 1: `GET /api/metrics/invocations/global` with required path/query/body/form/header values
- Constraints:
  Optional query `day` is a date string in the repository’s daily-statistic format; when absent, the current day is used.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/metrics/invocations/global`
- Distinct meaning:
  Retrieves global invocation totals for a day.

### Function 66: get top invocation metrics

Function name:
get top invocation metrics

Core endpoint(s):
- `GET /api/metrics/invocations/top`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns the top invoked services for a day.
- Invocation:
  Step 1: `GET /api/metrics/invocations/top` with required path/query/body/form/header values
- Constraints:
  Optional `day` defaults to the current day. Optional `limit` defaults to `20`.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/metrics/invocations/top`
- Distinct meaning:
  Retrieves top invocation statistics.

### Function 67: get invocation metrics for service

Function name:
get service invocation metrics

Core endpoint(s):
- `GET /api/metrics/invocations/{service}/{version}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request `{service}` and `{version}` must match the prerequisite service state. The prerequisite creation request creates an invocation event only when invocation statistics are enabled. Optional `day` defaults to current day.

Successful execution:
- Result:
  This function retrieves invocation metrics for one service name and version.
- Invocation:
  Step 1: `GET /api/metrics/invocations/{service}/{version}` with required path/query/body/form/header values
- Constraints:
  The core request `{service}` and `{version}` must match the prerequisite service state. The prerequisite creation request creates an invocation event only when invocation statistics are enabled. Optional `day` defaults to current day.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/metrics/invocations/{service}/{version}`
- Distinct meaning:
  Retrieves invocation metrics scoped to service name and version.

### Function 68: get latest global invocation trend

Function name:
get latest invocation trend

Core endpoint(s):
- `GET /api/metrics/invocations/global/latest`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns a date-to-count map of global invocations for the latest days.
- Invocation:
  Step 1: `GET /api/metrics/invocations/global/latest` with required path/query/body/form/header values
- Constraints:
  Optional `limit` defaults to `20` and is used as the number of days back.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/metrics/invocations/global/latest`
- Distinct meaning:
  Retrieves recent global invocation counts.

### Function 69: get aggregate conformance metrics

Function name:
get aggregate conformance metrics

Core endpoint(s):
- `GET /api/metrics/conformance/aggregate`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns aggregate test conformance metrics across services.
- Invocation:
  Step 1: `GET /api/metrics/conformance/aggregate` with required path/query/body/form/header values
- Constraints:
  No prerequisite resource state is required.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/metrics/conformance/aggregate`
- Distinct meaning:
  Retrieves aggregated conformance metrics.

### Function 70: get service conformance metric

Function name:
get service conformance metric

Core endpoint(s):
- `GET /api/metrics/conformance/service/{serviceId}`

Preconditions:
- A `GENERIC_REST` service exists with the required `name`, `version`, and `resource` values, including the generated CRUD-like operations for that resource. This can be satisfied by directly inserting the `Service` and `Operation` records, or by calling `POST /api/services/generic` with a body containing `name`, `version`, `resource`, and optional `referencePayload`. The generated service `id` must be reused wherever the core endpoint requires `{id}` or `{serviceId}`, and the path `{service}`, `{version}`, and `{resource}` values must match this service. The core request `{serviceId}` must be the service id returned by the prerequisite endpoint. No documented endpoint directly creates the conformance metric record; the implementation returns the repository value or null.

Successful execution:
- Result:
  This function retrieves the test conformance metric record for one service.
- Invocation:
  Step 1: `GET /api/metrics/conformance/service/{serviceId}` with required path/query/body/form/header values
- Constraints:
  The core request `{serviceId}` must be the service id returned by the prerequisite endpoint. No documented endpoint directly creates the conformance metric record; the implementation returns the repository value or null.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/metrics/conformance/service/{serviceId}`
- Distinct meaning:
  Retrieves conformance metric by service id.

### Function 71: get latest test summaries

Function name:
get latest test summaries

Core endpoint(s):
- `GET /api/metrics/tests/latest`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns test result summaries after a computed past date.
- Invocation:
  Step 1: `GET /api/metrics/tests/latest` with required path/query/body/form/header values
- Constraints:
  Optional `limit` defaults to `7` and is interpreted as days back.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/metrics/tests/latest`
- Distinct meaning:
  Retrieves recent test trend summaries.

### Function 72: generate AI sample suggestions for one operation

Function name:
suggest operation samples

Core endpoint(s):
- `GET /api/copilot/samples/{id}`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The core request `{id}` may be the service id or `{serviceName}:{serviceVersion}` from the prerequisite endpoint. Query `operation` must equal an operation name on the service. The service type must have a corresponding contract resource: OpenAPI for REST, GraphQL schema for GraphQL, AsyncAPI for EVENT, or Protobuf schema for GRPC.

Successful execution:
- Result:
  This function uses the configured AI Copilot to generate sample exchanges for one service operation.
- Invocation:
  Step 1: `GET /api/copilot/samples/{id}` with required path/query/body/form/header values
- Constraints:
  The core request `{id}` may be the service id or `{serviceName}:{serviceVersion}` from the prerequisite endpoint. Query `operation` must equal an operation name on the service. The service type must have a corresponding contract resource: OpenAPI for REST, GraphQL schema for GraphQL, AsyncAPI for EVENT, or Protobuf schema for GRPC.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: Service, operation, contract resource, or AI Copilot is unavailable. The artifact import endpoint is omitted, `{id}` is wrong, `operation` is absent or wrong, or no contract resource exists. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/copilot/samples/{id}`
  - Why this fails:
    Missing required state returns `400 Bad Request`; exceptions during generation return `500 Internal Server Error`.
  - Intentionally violated constraints:
    The artifact import endpoint is omitted, `{id}` is wrong, `operation` is absent or wrong, or no contract resource exists.

Endpoint coverage:
- Covers:
  `GET /api/copilot/samples/{id}`
- Distinct meaning:
  Synchronously suggests examples for a single operation.

### Function 73: start AI sample generation for all operations

Function name:
start sample generation

Core endpoint(s):
- `GET /api/copilot/samples/{id}`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite sample generation request must omit query `operation`. `{id}` must identify the imported service. The service must have a supported contract resource. The response body contains `taskId`.

Successful execution:
- Result:
  This function starts asynchronous AI sample generation for all operations of a service and returns a task id.
- Invocation:
  Step 1: `GET /api/copilot/samples/{id}` with required path/query/body/form/header values
- Constraints:
  The request must omit query `operation`. `{id}` must identify the imported service. The service must have a supported contract resource. The response body contains `taskId`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: Service or contract resource is missing. The artifact import endpoint is omitted or `{id}` does not resolve to a service with a supported contract. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/copilot/samples/{id}`
  - Why this fails:
    The controller returns `400 Bad Request`.
  - Intentionally violated constraints:
    The artifact import endpoint is omitted or `{id}` does not resolve to a service with a supported contract.

Endpoint coverage:
- Covers:
  `GET /api/copilot/samples/{id}`
- Distinct meaning:
  Asynchronously generates samples for all service operations.

### Function 74: check AI sample generation task status

Function name:
check sample generation status

Core endpoint(s):
- `GET /api/copilot/samples/task/{id}/status`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite sample generation request must omit query `operation` and return `taskId`. The core request `{id}` must be that `taskId`. `PENDING` returns `202`, `SUCCESS` returns `201` and removes the task, and `FAILURE` returns `500` and removes the task.

Successful execution:
- Result:
  This function checks whether an asynchronous AI sample generation task is pending, successful, or failed.
- Invocation:
  Step 1: `GET /api/copilot/samples/task/{id}/status` with required path/query/body/form/header values
- Constraints:
  The request must omit query `operation` and return `taskId`. The core request `{id}` must be that `taskId`. `PENDING` returns `202`, `SUCCESS` returns `201` and removes the task, and `FAILURE` returns `500` and removes the task.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: The task id is unknown. The core request uses an id not produced by `GET /api/copilot/samples/{id}` without `operation`. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/copilot/samples/task/{id}/status`
  - Why this fails:
    The controller returns `404 Not Found` with status `NOT_FOUND`.
  - Intentionally violated constraints:
    The core request uses an id not produced by `GET /api/copilot/samples/{id}` without `operation`.

Endpoint coverage:
- Covers:
  `GET /api/copilot/samples/task/{id}/status`
- Distinct meaning:
  Polls asynchronous AI generation status.

### Function 75: add AI sample exchanges

Function name:
add AI samples

Core endpoint(s):
- `POST /api/copilot/samples/{id}`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite request uses query `operation` to generate sample exchanges. The core request `{id}` must identify the same service and query `operation` must match the same operation. The core request body is the exchange list returned or shaped like the sample generation output. User must have manager access.

Successful execution:
- Result:
  This function persists sample request/response pairs or event messages for one service operation and marks them as AI Copilot source artifacts.
- Invocation:
  Step 1: `POST /api/copilot/samples/{id}` with required path/query/body/form/header values
- Constraints:
  The request uses query `operation` to generate sample exchanges. The core request `{id}` must identify the same service and query `operation` must match the same operation. The core request body is the exchange list returned or shaped like the sample generation output. User must have manager access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: Service or operation is missing, or user lacks manager access.
    - The failure-causing state or request values are present: Service or operation is missing, or user lacks manager access. The core request uses wrong `{id}`, wrong `operation`, or unauthorized `UserInfo`. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/copilot/samples/{id}`
  - Why this fails:
    `addAICopilotExchangesToServiceOperation` returns `false`, and the controller returns `403 Forbidden`.
  - Intentionally violated constraints:
    The core request uses wrong `{id}`, wrong `operation`, or unauthorized `UserInfo`.

Endpoint coverage:
- Covers:
  `POST /api/copilot/samples/{id}`
- Distinct meaning:
  Adds selected AI-generated or user-supplied sample exchanges.

### Function 76: remove AI sample exchanges

Function name:
cleanup AI samples

Core endpoint(s):
- `POST /api/copilot/samples/{id}/cleanup`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite sample-add request adds AI samples. The core request body `ExchangeSelection.exchanges` maps operation names to exchange names to delete. The core request `{id}` must identify the same service. User must have manager access.

Successful execution:
- Result:
  This function removes selected AI Copilot exchanges from a service.
- Invocation:
  Step 1: `POST /api/copilot/samples/{id}/cleanup` with required path/query/body/form/header values
- Constraints:
  The prerequisite sample-add request adds AI samples. The core request body `ExchangeSelection.exchanges` maps operation names to exchange names to delete. The core request `{id}` must identify the same service. User must have manager access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: Service is missing or user lacks manager access. The service import and sample-add sequence is omitted, or user is unauthorized. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/copilot/samples/{id}/cleanup`
  - Why this fails:
    The controller returns `403 Forbidden`.
  - Intentionally violated constraints:
    The service import and sample-add sequence is omitted, or user is unauthorized.

Endpoint coverage:
- Covers:
  `POST /api/copilot/samples/{id}/cleanup`
- Distinct meaning:
  Deletes selected AI Copilot samples.

### Function 77: export selected AI sample exchanges

Function name:
export AI samples

Core endpoint(s):
- `POST /api/copilot/samples/{id}/export`

Preconditions:
- A supported artifact has already been imported and the resulting service, resource, request/response, or event-message records needed by this function exist in the database. This can be satisfied by directly inserting the corresponding `Service`, `Operation`, `Resource`, request/response, and event-message records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed. Any generated service id, resource id, resource name, service name, or service version used by the core endpoint must come from that imported state. The prerequisite creation request creates exchanges to select. The core request body `ExchangeSelection.exchanges` maps operation names to exchange names. Optional query `format` defaults to `APIExamples`. User must have manager access to receive non-empty export content.

Successful execution:
- Result:
  This function exports selected exchanges for a service in an examples export format.
- Invocation:
  Step 1: `POST /api/copilot/samples/{id}/export` with required path/query/body/form/header values
- Constraints:
  The prerequisite creation request creates exchanges to select. The core request body `ExchangeSelection.exchanges` maps operation names to exchange names. Optional query `format` defaults to `APIExamples`. User must have manager access to receive non-empty export content.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A supported artifact has been imported, creating the service, resource, operation, request/response, or event-message records referenced by the failure endpoint. This can be satisfied by directly inserting equivalent database records, or by calling `POST /api/artifact/upload` with form field `file` and query `mainArtifact` as needed; generated service and resource ids, names, and versions must be reused consistently. This prerequisite state is used before the failing request: Export processing throws an exception.
    - The failure-causing state or request values are present: Export processing throws an exception. Export format or selection cannot be processed. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `POST /api/copilot/samples/{id}/export`
  - Why this fails:
    The controller returns `500 Internal Server Error`.
  - Intentionally violated constraints:
    Export format or selection cannot be processed.

Endpoint coverage:
- Covers:
  `POST /api/copilot/samples/{id}/export`
- Distinct meaning:
  Exports selected sample exchanges.

### Function 78: get health status

Function name:
check health

Core endpoint(s):
- `GET /api/health`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function verifies repository/database access and returns `200 OK` when the check succeeds.
- Invocation:
  Step 1: `GET /api/health` with required path/query/body/form/header values
- Constraints:
  The implementation reads a page of import jobs to verify persistence access.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The failure-causing state or request values are present: Repository access throws an exception. Persistence backend is unavailable or fails during the health query. This can be produced by direct database/resource setup, by omitting the endpoint that would create required state, or by sending the described invalid, duplicate, mismatched, unauthorized, unsupported, or missing path/query/body/header values to the failure endpoint.
  - Failure endpoint:
    `GET /api/health`
  - Why this fails:
    The controller returns `503 Service Unavailable`.
  - Intentionally violated constraints:
    Persistence backend is unavailable or fails during the health query.

Endpoint coverage:
- Covers:
  `GET /api/health`
- Distinct meaning:
  Reports service health.

### Function 79: get version information

Function name:
get version info

Core endpoint(s):
- `GET /api/version/info`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns the application version id and build timestamp.
- Invocation:
  Step 1: `GET /api/version/info` with required path/query/body/form/header values
- Constraints:
  Values are read from `version.properties`.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/version/info`
- Distinct meaning:
  Exposes application version metadata.

### Function 80: get Keycloak frontend configuration

Function name:
get Keycloak config

Core endpoint(s):
- `GET /api/keycloak/config`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns frontend authentication configuration, including whether Keycloak is enabled, realm, auth server URL, SSL policy, public-client flag, and resource.
- Invocation:
  Step 1: `GET /api/keycloak/config` with required path/query/body/form/header values
- Constraints:
  Values come from application properties, with defaults for realm, auth server URL, SSL, public client, and resource.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/keycloak/config`
- Distinct meaning:
  Exposes frontend SSO configuration.

### Function 81: get optional feature configuration

Function name:
get feature config

Core endpoint(s):
- `GET /api/features/config`

Preconditions:
- No prerequisite resource state is required beyond providing the path, query, body, form, and header values accepted by the core endpoint.

Successful execution:
- Result:
  This function returns configured optional feature flags and properties.
- Invocation:
  Step 1: `GET /api/features/config` with required path/query/body/form/header values
- Constraints:
  Values are bound from `features.properties` and optional deployment override properties.

Failure or exceptional branches:
- No implementation-specific failure branch is visible.

Endpoint coverage:
- Covers:
  `GET /api/features/config`
- Distinct meaning:
  Exposes feature configuration to clients.

