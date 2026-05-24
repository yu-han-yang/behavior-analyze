# Domain-Level Behavior Analysis

## Domain Summary

The `ohsome-api` service is described by its OpenAPI contract as: This REST-based API aims to leverage the tools of the OSHDB through allowing to access some of its functionalities via HTTP requests. The official documentation can be found here .

The core business concepts are:

- Contributions Count: endpoint group for contributions count behavior.
- Elements Area: endpoint group for elements area behavior.
- Elements Count: endpoint group for elements count behavior.
- Elements Length: endpoint group for elements length behavior.
- Elements Perimeter: endpoint group for elements perimeter behavior.
- Users Count: endpoint group for users count behavior.

Contract-level limits: this document captures externally visible business behavior from the API contract. Implementation-only validation, persistence side effects, authorization rules, and asynchronous processing details may be stricter than what is visible here.

## Available Function Inventory

### Contributions Count

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `count of osm contributions` | `GET /contributions/count` | Count of OSM contributions. |
| `count of osm contributions` | `POST /contributions/count` | Count of OSM contributions. |
| `density of osm contributions number of contributions divided by the total area in square kilometers` | `GET /contributions/count/density` | Density of OSM contributions (number of contributions divided by the total area in square-kilometers). |
| `density of osm contributions number of contributions divided by the total area in square kilometers` | `POST /contributions/count/density` | Density of OSM contributions (number of contributions divided by the total area in square-kilometers). |
| `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys` | `GET /contributions/count/density/groupBy/boundary` | Count density of OSM contributions grouped by boundary (bboxes, bcirlces, or bpolys). |
| `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys` | `POST /contributions/count/density/groupBy/boundary` | Count density of OSM contributions grouped by boundary (bboxes, bcirlces, or bpolys). |
| `count of osm contributions grouped by boundary bboxes bcirlces or bpolys` | `GET /contributions/count/groupBy/boundary` | Count of OSM contributions grouped by boundary (bboxes, bcirlces, or bpolys). |
| `count of osm contributions grouped by boundary bboxes bcirlces or bpolys` | `POST /contributions/count/groupBy/boundary` | Count of OSM contributions grouped by boundary (bboxes, bcirlces, or bpolys). |
| `count of latest osm contributions` | `GET /contributions/latest/count` | Count of latest OSM contributions. |
| `count of latest osm contributions` | `POST /contributions/latest/count` | Count of latest OSM contributions. |
| `density of the latest osm contributions number of contributions divided by the total area in square kilometers` | `GET /contributions/latest/count/density` | Density of the latest OSM contributions (number of contributions divided by the total area in square-kilometers). |
| `density of the latest osm contributions number of contributions divided by the total area in square kilometers` | `POST /contributions/latest/count/density` | Density of the latest OSM contributions (number of contributions divided by the total area in square-kilometers). |

### Elements Area

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `area of osm elements` | `GET /elements/area` | Area of OSM elements. |
| `area of osm elements` | `POST /elements/area` | Area of OSM elements. |
| `density of osm elements area of elements divided by the total area in square kilometers` | `GET /elements/area/density` | Density of OSM elements (area of elements divided by the total area in square-kilometers). |
| `density of osm elements area of elements divided by the total area in square kilometers` | `POST /elements/area/density` | Density of OSM elements (area of elements divided by the total area in square-kilometers). |
| `density of osm elements grouped by the boundary bboxes bcircles or bpolys` | `GET /elements/area/density/groupBy/boundary` | Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `density of osm elements grouped by the boundary bboxes bcircles or bpolys` | `POST /elements/area/density/groupBy/boundary` | Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `density of osm elements grouped by the boundary and the tag` | `GET /elements/area/density/groupBy/boundary/groupBy/tag` | Density of OSM elements grouped by the boundary and the tag. |
| `density of osm elements grouped by the boundary and the tag` | `POST /elements/area/density/groupBy/boundary/groupBy/tag` | Density of OSM elements grouped by the boundary and the tag. |
| `density of osm elements grouped by the tag` | `GET /elements/area/density/groupBy/tag` | Density of OSM elements grouped by the tag. |
| `density of osm elements grouped by the tag` | `POST /elements/area/density/groupBy/tag` | Density of OSM elements grouped by the tag. |
| `density of osm elements grouped by the type` | `GET /elements/area/density/groupBy/type` | Density of OSM elements grouped by the type. |
| `density of osm elements grouped by the type` | `POST /elements/area/density/groupBy/type` | Density of OSM elements grouped by the type. |
| `area of osm elements grouped by the boundary bboxes bcircles or bpolys` | `GET /elements/area/groupBy/boundary` | Area of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `area of osm elements grouped by the boundary bboxes bcircles or bpolys` | `POST /elements/area/groupBy/boundary` | Area of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `area of osm elements grouped by the boundary and the tag` | `GET /elements/area/groupBy/boundary/groupBy/tag` | Area of OSM elements grouped by the boundary and the tag. |
| `area of osm elements grouped by the boundary and the tag` | `POST /elements/area/groupBy/boundary/groupBy/tag` | Area of OSM elements grouped by the boundary and the tag. |
| `area of osm elements grouped by the key` | `GET /elements/area/groupBy/key` | Area of OSM elements grouped by the key. |
| `area of osm elements grouped by the key` | `POST /elements/area/groupBy/key` | Area of OSM elements grouped by the key. |
| `area of osm elements grouped by the tag` | `GET /elements/area/groupBy/tag` | Area of OSM elements grouped by the tag. |
| `area of osm elements grouped by the tag` | `POST /elements/area/groupBy/tag` | Area of OSM elements grouped by the tag. |
| `area of osm elements grouped by the type` | `GET /elements/area/groupBy/type` | Area of OSM elements grouped by the type. |
| `area of osm elements grouped by the type` | `POST /elements/area/groupBy/type` | Area of OSM elements grouped by the type. |
| `ratio of the area of osm elements satisfying filter2 within items selected by filter` | `GET /elements/area/ratio` | Ratio of the area of OSM elements satisfying filter2 within items selected by filter. |
| `ratio of the area of osm elements satisfying filter2 within items selected by filter` | `POST /elements/area/ratio` | Ratio of the area of OSM elements satisfying filter2 within items selected by filter. |
| `ratio of the area of osm elements grouped by the boundary` | `GET /elements/area/ratio/groupBy/boundary` | Ratio of the area of OSM elements grouped by the boundary. |
| `ratio of the area of osm elements grouped by the boundary` | `POST /elements/area/ratio/groupBy/boundary` | Ratio of the area of OSM elements grouped by the boundary. |

### Elements Count

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `count of osm elements` | `GET /elements/count` | Count of OSM elements. |
| `count of osm elements` | `POST /elements/count` | Count of OSM elements. |
| `density of osm elements number of elements divided by the total area in square kilometers` | `GET /elements/count/density` | Density of OSM elements (number of elements divided by the total area in square-kilometers). |
| `density of osm elements number of elements divided by the total area in square kilometers` | `POST /elements/count/density` | Density of OSM elements (number of elements divided by the total area in square-kilometers). |
| `density of osm elements grouped by the boundary bboxes bcircles or bpolys` | `GET /elements/count/density/groupBy/boundary` | Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `density of osm elements grouped by the boundary bboxes bcircles or bpolys` | `POST /elements/count/density/groupBy/boundary` | Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `density of osm elements grouped by the boundary and the tag` | `GET /elements/count/density/groupBy/boundary/groupBy/tag` | Density of OSM elements grouped by the boundary and the tag. |
| `density of osm elements grouped by the boundary and the tag` | `POST /elements/count/density/groupBy/boundary/groupBy/tag` | Density of OSM elements grouped by the boundary and the tag. |
| `density of osm elements grouped by the tag` | `GET /elements/count/density/groupBy/tag` | Density of OSM elements grouped by the tag. |
| `density of osm elements grouped by the tag` | `POST /elements/count/density/groupBy/tag` | Density of OSM elements grouped by the tag. |
| `density of osm elements grouped by the type` | `GET /elements/count/density/groupBy/type` | Density of OSM elements grouped by the type. |
| `density of osm elements grouped by the type` | `POST /elements/count/density/groupBy/type` | Density of OSM elements grouped by the type. |
| `count of osm elements grouped by the boundary bboxes bcircles or bpolys` | `GET /elements/count/groupBy/boundary` | Count of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `count of osm elements grouped by the boundary bboxes bcircles or bpolys` | `POST /elements/count/groupBy/boundary` | Count of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `count of osm elements grouped by the boundary and the tag` | `GET /elements/count/groupBy/boundary/groupBy/tag` | Count of OSM elements grouped by the boundary and the tag. |
| `count of osm elements grouped by the boundary and the tag` | `POST /elements/count/groupBy/boundary/groupBy/tag` | Count of OSM elements grouped by the boundary and the tag. |
| `count of osm elements grouped by the key` | `GET /elements/count/groupBy/key` | Count of OSM elements grouped by the key. |
| `count of osm elements grouped by the key` | `POST /elements/count/groupBy/key` | Count of OSM elements grouped by the key. |
| `count of osm elements grouped by the tag` | `GET /elements/count/groupBy/tag` | Count of OSM elements grouped by the tag. |
| `count of osm elements grouped by the tag` | `POST /elements/count/groupBy/tag` | Count of OSM elements grouped by the tag. |
| `count of osm elements grouped by the type` | `GET /elements/count/groupBy/type` | Count of OSM elements grouped by the type. |
| `count of osm elements grouped by the type` | `POST /elements/count/groupBy/type` | Count of OSM elements grouped by the type. |
| `ratio of osm elements satisfying filter2 within items selected by filter` | `GET /elements/count/ratio` | Ratio of OSM elements satisfying filter2 within items selected by filter. |
| `ratio of osm elements satisfying filter2 within items selected by filter` | `POST /elements/count/ratio` | Ratio of OSM elements satisfying filter2 within items selected by filter. |
| `ratio of osm elements grouped by the boundary` | `GET /elements/count/ratio/groupBy/boundary` | Ratio of OSM elements grouped by the boundary. |
| `ratio of osm elements grouped by the boundary` | `POST /elements/count/ratio/groupBy/boundary` | Ratio of OSM elements grouped by the boundary. |

### Elements Length

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `length of osm elements` | `GET /elements/length` | Length of OSM elements. |
| `length of osm elements` | `POST /elements/length` | Length of OSM elements. |
| `density of osm elements length of elements divided by the total area in square kilometers` | `GET /elements/length/density` | Density of OSM elements (length of elements divided by the total area in square-kilometers). |
| `density of osm elements length of elements divided by the total area in square kilometers` | `POST /elements/length/density` | Density of OSM elements (length of elements divided by the total area in square-kilometers). |
| `density of osm elements grouped by the boundary bboxes bcircles or bpolys` | `GET /elements/length/density/groupBy/boundary` | Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `density of osm elements grouped by the boundary bboxes bcircles or bpolys` | `POST /elements/length/density/groupBy/boundary` | Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `density of osm elements grouped by the boundary and the tag` | `GET /elements/length/density/groupBy/boundary/groupBy/tag` | Density of OSM elements grouped by the boundary and the tag. |
| `density of osm elements grouped by the boundary and the tag` | `POST /elements/length/density/groupBy/boundary/groupBy/tag` | Density of OSM elements grouped by the boundary and the tag. |
| `density of osm elements grouped by the tag` | `GET /elements/length/density/groupBy/tag` | Density of OSM elements grouped by the tag. |
| `density of osm elements grouped by the tag` | `POST /elements/length/density/groupBy/tag` | Density of OSM elements grouped by the tag. |
| `density of osm elements grouped by the osm type` | `GET /elements/length/density/groupBy/type` | Density of OSM elements grouped by the OSM type. |
| `density of osm elements grouped by the osm type` | `POST /elements/length/density/groupBy/type` | Density of OSM elements grouped by the OSM type. |
| `length of osm elements grouped by the boundary bboxes bcircles or bpolys` | `GET /elements/length/groupBy/boundary` | Length of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `length of osm elements grouped by the boundary bboxes bcircles or bpolys` | `POST /elements/length/groupBy/boundary` | Length of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `length of osm elements grouped by the boundary and the tag` | `GET /elements/length/groupBy/boundary/groupBy/tag` | Length of OSM elements grouped by the boundary and the tag. |
| `length of osm elements grouped by the boundary and the tag` | `POST /elements/length/groupBy/boundary/groupBy/tag` | Length of OSM elements grouped by the boundary and the tag. |
| `length of osm elements grouped by the key` | `GET /elements/length/groupBy/key` | Length of OSM elements grouped by the key. |
| `length of osm elements grouped by the key` | `POST /elements/length/groupBy/key` | Length of OSM elements grouped by the key. |
| `length of osm elements grouped by the tag` | `GET /elements/length/groupBy/tag` | Length of OSM elements grouped by the tag. |
| `length of osm elements grouped by the tag` | `POST /elements/length/groupBy/tag` | Length of OSM elements grouped by the tag. |
| `length of osm elements grouped by the type` | `GET /elements/length/groupBy/type` | Length of OSM elements grouped by the type. |
| `length of osm elements grouped by the type` | `POST /elements/length/groupBy/type` | Length of OSM elements grouped by the type. |
| `ratio of osm elements satisfying filter2 within items selected by filter` | `GET /elements/length/ratio` | Ratio of OSM elements satisfying filter2 within items selected by filter. |
| `ratio of osm elements satisfying filter2 within items selected by filter` | `POST /elements/length/ratio` | Ratio of OSM elements satisfying filter2 within items selected by filter. |
| `ratio of the length of osm elements grouped by the boundary` | `GET /elements/length/ratio/groupBy/boundary` | Ratio of the length of OSM elements grouped by the boundary. |
| `ratio of the length of osm elements grouped by the boundary` | `POST /elements/length/ratio/groupBy/boundary` | Ratio of the length of OSM elements grouped by the boundary. |

### Elements Perimeter

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `perimeter of osm elements` | `GET /elements/perimeter` | Perimeter of OSM elements. |
| `perimeter of osm elements` | `POST /elements/perimeter` | Perimeter of OSM elements. |
| `density of osm elements perimeter of elements divided by the total area in square kilometers` | `GET /elements/perimeter/density` | Density of OSM elements (perimeter of elements divided by the total area in square-kilometers). |
| `density of osm elements perimeter of elements divided by the total area in square kilometers` | `POST /elements/perimeter/density` | Density of OSM elements (perimeter of elements divided by the total area in square-kilometers). |
| `density of osm elements grouped by the boundary bboxes bcircles or bpolys` | `GET /elements/perimeter/density/groupBy/boundary` | Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `density of osm elements grouped by the boundary bboxes bcircles or bpolys` | `POST /elements/perimeter/density/groupBy/boundary` | Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys). |
| `density of grouped by the boundary and the tag` | `GET /elements/perimeter/density/groupBy/boundary/groupBy/tag` | Density of grouped by the boundary and the tag. |
| `density of grouped by the boundary and the tag` | `POST /elements/perimeter/density/groupBy/boundary/groupBy/tag` | Density of grouped by the boundary and the tag. |
| `density of osm elements grouped by the tag` | `GET /elements/perimeter/density/groupBy/tag` | Density of OSM elements grouped by the tag. |
| `density of osm elements grouped by the tag` | `POST /elements/perimeter/density/groupBy/tag` | Density of OSM elements grouped by the tag. |
| `density of osm elements grouped by the type` | `GET /elements/perimeter/density/groupBy/type` | Density of OSM elements grouped by the type. |
| `density of osm elements grouped by the type` | `POST /elements/perimeter/density/groupBy/type` | Density of OSM elements grouped by the type. |
| `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys` | `GET /elements/perimeter/groupBy/boundary` | Perimeter of OSM elements in grouped by the boundary (bboxes, bcircles, or bpolys). |
| `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys` | `POST /elements/perimeter/groupBy/boundary` | Perimeter of OSM elements in grouped by the boundary (bboxes, bcircles, or bpolys). |
| `perimeter of osm elements grouped by the boundary and the tag` | `GET /elements/perimeter/groupBy/boundary/groupBy/tag` | Perimeter of OSM elements grouped by the boundary and the tag. |
| `perimeter of osm elements grouped by the boundary and the tag` | `POST /elements/perimeter/groupBy/boundary/groupBy/tag` | Perimeter of OSM elements grouped by the boundary and the tag. |
| `perimeter of osm elements grouped by the key` | `GET /elements/perimeter/groupBy/key` | Perimeter of OSM elements grouped by the key. |
| `perimeter of osm elements grouped by the key` | `POST /elements/perimeter/groupBy/key` | Perimeter of OSM elements grouped by the key. |
| `perimeter of osm elements grouped by the tag` | `GET /elements/perimeter/groupBy/tag` | Perimeter of OSM elements grouped by the tag. |
| `perimeter of osm elements grouped by the tag` | `POST /elements/perimeter/groupBy/tag` | Perimeter of OSM elements grouped by the tag. |
| `perimeter of osm elements grouped by the type` | `GET /elements/perimeter/groupBy/type` | Perimeter of OSM elements grouped by the type. |
| `perimeter of osm elements grouped by the type` | `POST /elements/perimeter/groupBy/type` | Perimeter of OSM elements grouped by the type. |
| `ratio of osm elements satisfying filter2 within items selected by filter` | `GET /elements/perimeter/ratio` | Ratio of OSM elements satisfying filter2 within items selected by filter. |
| `ratio of osm elements satisfying filter2 within items selected by filter` | `POST /elements/perimeter/ratio` | Ratio of OSM elements satisfying filter2 within items selected by filter. |
| `ratio of the perimeter of osm elements grouped by the boundary` | `GET /elements/perimeter/ratio/groupBy/boundary` | Ratio of the perimeter of OSM elements grouped by the boundary. |
| `ratio of the perimeter of osm elements grouped by the boundary` | `POST /elements/perimeter/ratio/groupBy/boundary` | Ratio of the perimeter of OSM elements grouped by the boundary. |

### Users Count

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `count of osm users` | `GET /users/count` | Count of OSM users. |
| `count of osm users` | `POST /users/count` | Count of OSM users. |
| `density of osm users number of users divided by the total area in square kilometers` | `GET /users/count/density` | Density of OSM users (number of users divided by the total area in square-kilometers). |
| `density of osm users number of users divided by the total area in square kilometers` | `POST /users/count/density` | Density of OSM users (number of users divided by the total area in square-kilometers). |
| `count of osm users grouped by boundary bboxes bcirlces or bpolys` | `GET /users/count/density/groupBy/boundary` | Count of OSM users grouped by boundary (bboxes, bcirlces, or bpolys). |
| `count of osm users grouped by boundary bboxes bcirlces or bpolys` | `POST /users/count/density/groupBy/boundary` | Count of OSM users grouped by boundary (bboxes, bcirlces, or bpolys). |
| `density of osm users grouped by the tag` | `GET /users/count/density/groupBy/tag` | Density of OSM users grouped by the tag. |
| `density of osm users grouped by the tag` | `POST /users/count/density/groupBy/tag` | Density of OSM users grouped by the tag. |
| `density of osm users grouped by the type` | `GET /users/count/density/groupBy/type` | Density of OSM users grouped by the type. |
| `density of osm users grouped by the type` | `POST /users/count/density/groupBy/type` | Density of OSM users grouped by the type. |
| `count of osm users grouped by boundary bboxes bcirlces or bpolys` | `GET /users/count/groupBy/boundary` | Count of OSM users grouped by boundary (bboxes, bcirlces, or bpolys). |
| `count of osm users grouped by boundary bboxes bcirlces or bpolys` | `POST /users/count/groupBy/boundary` | Count of OSM users grouped by boundary (bboxes, bcirlces, or bpolys). |
| `count of osm users grouped by the key` | `GET /users/count/groupBy/key` | Count of OSM users grouped by the key. |
| `count of osm users grouped by the key` | `POST /users/count/groupBy/key` | Count of OSM users grouped by the key. |
| `count of osm users grouped by the tag` | `GET /users/count/groupBy/tag` | Count of OSM users grouped by the tag. |
| `count of osm users grouped by the tag` | `POST /users/count/groupBy/tag` | Count of OSM users grouped by the tag. |
| `count of osm users grouped by the type` | `GET /users/count/groupBy/type` | Count of OSM users grouped by the type. |
| `count of osm users grouped by the type` | `POST /users/count/groupBy/type` | Count of OSM users grouped by the type. |

## Supported Business Behaviors

### Behavior 1: Count Of Osm Contributions

Business goal:
Count of OSM contributions.

Domain context:
This behavior belongs to the `Contributions Count` capability area and operates through `GET /contributions/count`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm contributions` (`GET /contributions/count`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 2: Count Of Osm Contributions

Business goal:
Count of OSM contributions.

Domain context:
This behavior belongs to the `Contributions Count` capability area and operates through `POST /contributions/count`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm contributions` (`POST /contributions/count`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 3: Density Of Osm Contributions Number Of Contributions Divided By The Total Area In Square Kilometers

Business goal:
Density of OSM contributions (number of contributions divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Contributions Count` capability area and operates through `GET /contributions/count/density`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm contributions number of contributions divided by the total area in square kilometers` (`GET /contributions/count/density`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 4: Density Of Osm Contributions Number Of Contributions Divided By The Total Area In Square Kilometers

Business goal:
Density of OSM contributions (number of contributions divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Contributions Count` capability area and operates through `POST /contributions/count/density`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm contributions number of contributions divided by the total area in square kilometers` (`POST /contributions/count/density`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 5: Count Density Of Osm Contributions Grouped By Boundary Bboxes Bcirlces Or Bpolys

Business goal:
Count density of OSM contributions grouped by boundary (bboxes, bcirlces, or bpolys).

Domain context:
This behavior belongs to the `Contributions Count` capability area and operates through `GET /contributions/count/density/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys` (`GET /contributions/count/density/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 6: Count Density Of Osm Contributions Grouped By Boundary Bboxes Bcirlces Or Bpolys

Business goal:
Count density of OSM contributions grouped by boundary (bboxes, bcirlces, or bpolys).

Domain context:
This behavior belongs to the `Contributions Count` capability area and operates through `POST /contributions/count/density/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys` (`POST /contributions/count/density/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count density of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 7: Count Of Osm Contributions Grouped By Boundary Bboxes Bcirlces Or Bpolys

Business goal:
Count of OSM contributions grouped by boundary (bboxes, bcirlces, or bpolys).

Domain context:
This behavior belongs to the `Contributions Count` capability area and operates through `GET /contributions/count/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm contributions grouped by boundary bboxes bcirlces or bpolys` (`GET /contributions/count/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 8: Count Of Osm Contributions Grouped By Boundary Bboxes Bcirlces Or Bpolys

Business goal:
Count of OSM contributions grouped by boundary (bboxes, bcirlces, or bpolys).

Domain context:
This behavior belongs to the `Contributions Count` capability area and operates through `POST /contributions/count/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm contributions grouped by boundary bboxes bcirlces or bpolys` (`POST /contributions/count/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm contributions grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 9: Count Of Latest Osm Contributions

Business goal:
Count of latest OSM contributions.

Domain context:
This behavior belongs to the `Contributions Count` capability area and operates through `GET /contributions/latest/count`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of latest osm contributions` (`GET /contributions/latest/count`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 10: Count Of Latest Osm Contributions

Business goal:
Count of latest OSM contributions.

Domain context:
This behavior belongs to the `Contributions Count` capability area and operates through `POST /contributions/latest/count`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of latest osm contributions` (`POST /contributions/latest/count`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of latest osm contributions`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 11: Density Of The Latest Osm Contributions Number Of Contributions Divided By The Total Area In Square Kilometers

Business goal:
Density of the latest OSM contributions (number of contributions divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Contributions Count` capability area and operates through `GET /contributions/latest/count/density`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of the latest osm contributions number of contributions divided by the total area in square kilometers` (`GET /contributions/latest/count/density`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 12: Density Of The Latest Osm Contributions Number Of Contributions Divided By The Total Area In Square Kilometers

Business goal:
Density of the latest OSM contributions (number of contributions divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Contributions Count` capability area and operates through `POST /contributions/latest/count/density`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of the latest osm contributions number of contributions divided by the total area in square kilometers` (`POST /contributions/latest/count/density`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of the latest osm contributions number of contributions divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 13: Area Of Osm Elements

Business goal:
Area of OSM elements.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `area of osm elements` (`GET /elements/area`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `area of osm elements`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 14: Area Of Osm Elements

Business goal:
Area of OSM elements.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `area of osm elements` (`POST /elements/area`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `area of osm elements`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 15: Density Of Osm Elements Area Of Elements Divided By The Total Area In Square Kilometers

Business goal:
Density of OSM elements (area of elements divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area/density`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements area of elements divided by the total area in square kilometers` (`GET /elements/area/density`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 16: Density Of Osm Elements Area Of Elements Divided By The Total Area In Square Kilometers

Business goal:
Density of OSM elements (area of elements divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area/density`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements area of elements divided by the total area in square kilometers` (`POST /elements/area/density`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements area of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 17: Density Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area/density/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary bboxes bcircles or bpolys` (`GET /elements/area/density/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 18: Density Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area/density/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary bboxes bcircles or bpolys` (`POST /elements/area/density/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 19: Density Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Density of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area/density/groupBy/boundary/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary and the tag` (`GET /elements/area/density/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 20: Density Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Density of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area/density/groupBy/boundary/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary and the tag` (`POST /elements/area/density/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 21: Density Of Osm Elements Grouped By The Tag

Business goal:
Density of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area/density/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the tag` (`GET /elements/area/density/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 22: Density Of Osm Elements Grouped By The Tag

Business goal:
Density of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area/density/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the tag` (`POST /elements/area/density/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 23: Density Of Osm Elements Grouped By The Type

Business goal:
Density of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area/density/groupBy/type`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the type` (`GET /elements/area/density/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 24: Density Of Osm Elements Grouped By The Type

Business goal:
Density of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area/density/groupBy/type`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the type` (`POST /elements/area/density/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 25: Area Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Area of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `area of osm elements grouped by the boundary bboxes bcircles or bpolys` (`GET /elements/area/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 26: Area Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Area of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `area of osm elements grouped by the boundary bboxes bcircles or bpolys` (`POST /elements/area/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 27: Area Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Area of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area/groupBy/boundary/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `area of osm elements grouped by the boundary and the tag` (`GET /elements/area/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 28: Area Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Area of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area/groupBy/boundary/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `area of osm elements grouped by the boundary and the tag` (`POST /elements/area/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 29: Area Of Osm Elements Grouped By The Key

Business goal:
Area of OSM elements grouped by the key.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area/groupBy/key`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `area of osm elements grouped by the key` (`GET /elements/area/groupBy/key`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKeys required, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKeys`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKeys`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 30: Area Of Osm Elements Grouped By The Key

Business goal:
Area of OSM elements grouped by the key.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area/groupBy/key`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `area of osm elements grouped by the key` (`POST /elements/area/groupBy/key`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKeys required, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKeys`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKeys`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the key`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 31: Area Of Osm Elements Grouped By The Tag

Business goal:
Area of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `area of osm elements grouped by the tag` (`GET /elements/area/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 32: Area Of Osm Elements Grouped By The Tag

Business goal:
Area of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `area of osm elements grouped by the tag` (`POST /elements/area/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 33: Area Of Osm Elements Grouped By The Type

Business goal:
Area of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area/groupBy/type`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `area of osm elements grouped by the type` (`GET /elements/area/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 34: Area Of Osm Elements Grouped By The Type

Business goal:
Area of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area/groupBy/type`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `area of osm elements grouped by the type` (`POST /elements/area/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `area of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 35: Ratio Of The Area Of Osm Elements Satisfying Filter2 Within Items Selected By Filter

Business goal:
Ratio of the area of OSM elements satisfying filter2 within items selected by filter.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area/ratio`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `ratio of the area of osm elements satisfying filter2 within items selected by filter` (`GET /elements/area/ratio`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 36: Ratio Of The Area Of Osm Elements Satisfying Filter2 Within Items Selected By Filter

Business goal:
Ratio of the area of OSM elements satisfying filter2 within items selected by filter.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area/ratio`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `ratio of the area of osm elements satisfying filter2 within items selected by filter` (`POST /elements/area/ratio`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 37: Ratio Of The Area Of Osm Elements Grouped By The Boundary

Business goal:
Ratio of the area of OSM elements grouped by the boundary.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `GET /elements/area/ratio/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `ratio of the area of osm elements grouped by the boundary` (`GET /elements/area/ratio/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 38: Ratio Of The Area Of Osm Elements Grouped By The Boundary

Business goal:
Ratio of the area of OSM elements grouped by the boundary.

Domain context:
This behavior belongs to the `Elements Area` capability area and operates through `POST /elements/area/ratio/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `ratio of the area of osm elements grouped by the boundary` (`POST /elements/area/ratio/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the area of osm elements grouped by the boundary`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 39: Count Of Osm Elements

Business goal:
Count of OSM elements.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm elements` (`GET /elements/count`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm elements`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 40: Count Of Osm Elements

Business goal:
Count of OSM elements.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm elements` (`POST /elements/count`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm elements`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 41: Density Of Osm Elements Number Of Elements Divided By The Total Area In Square Kilometers

Business goal:
Density of OSM elements (number of elements divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count/density`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements number of elements divided by the total area in square kilometers` (`GET /elements/count/density`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 42: Density Of Osm Elements Number Of Elements Divided By The Total Area In Square Kilometers

Business goal:
Density of OSM elements (number of elements divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count/density`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements number of elements divided by the total area in square kilometers` (`POST /elements/count/density`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements number of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 43: Density Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count/density/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary bboxes bcircles or bpolys` (`GET /elements/count/density/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 44: Density Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count/density/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary bboxes bcircles or bpolys` (`POST /elements/count/density/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 45: Density Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Density of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count/density/groupBy/boundary/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary and the tag` (`GET /elements/count/density/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 46: Density Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Density of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count/density/groupBy/boundary/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary and the tag` (`POST /elements/count/density/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 47: Density Of Osm Elements Grouped By The Tag

Business goal:
Density of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count/density/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the tag` (`GET /elements/count/density/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 48: Density Of Osm Elements Grouped By The Tag

Business goal:
Density of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count/density/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the tag` (`POST /elements/count/density/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 49: Density Of Osm Elements Grouped By The Type

Business goal:
Density of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count/density/groupBy/type`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the type` (`GET /elements/count/density/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 50: Density Of Osm Elements Grouped By The Type

Business goal:
Density of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count/density/groupBy/type`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the type` (`POST /elements/count/density/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 51: Count Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Count of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm elements grouped by the boundary bboxes bcircles or bpolys` (`GET /elements/count/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 52: Count Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Count of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm elements grouped by the boundary bboxes bcircles or bpolys` (`POST /elements/count/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 53: Count Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Count of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count/groupBy/boundary/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm elements grouped by the boundary and the tag` (`GET /elements/count/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 54: Count Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Count of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count/groupBy/boundary/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm elements grouped by the boundary and the tag` (`POST /elements/count/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 55: Count Of Osm Elements Grouped By The Key

Business goal:
Count of OSM elements grouped by the key.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count/groupBy/key`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm elements grouped by the key` (`GET /elements/count/groupBy/key`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKeys required, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKeys`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKeys`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 56: Count Of Osm Elements Grouped By The Key

Business goal:
Count of OSM elements grouped by the key.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count/groupBy/key`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm elements grouped by the key` (`POST /elements/count/groupBy/key`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKeys required, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKeys`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKeys`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the key`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 57: Count Of Osm Elements Grouped By The Tag

Business goal:
Count of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm elements grouped by the tag` (`GET /elements/count/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 58: Count Of Osm Elements Grouped By The Tag

Business goal:
Count of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm elements grouped by the tag` (`POST /elements/count/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 59: Count Of Osm Elements Grouped By The Type

Business goal:
Count of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count/groupBy/type`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm elements grouped by the type` (`GET /elements/count/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 60: Count Of Osm Elements Grouped By The Type

Business goal:
Count of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count/groupBy/type`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm elements grouped by the type` (`POST /elements/count/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 61: Ratio Of Osm Elements Satisfying Filter2 Within Items Selected By Filter

Business goal:
Ratio of OSM elements satisfying filter2 within items selected by filter.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count/ratio`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `ratio of osm elements satisfying filter2 within items selected by filter` (`GET /elements/count/ratio`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 62: Ratio Of Osm Elements Satisfying Filter2 Within Items Selected By Filter

Business goal:
Ratio of OSM elements satisfying filter2 within items selected by filter.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count/ratio`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `ratio of osm elements satisfying filter2 within items selected by filter` (`POST /elements/count/ratio`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 63: Ratio Of Osm Elements Grouped By The Boundary

Business goal:
Ratio of OSM elements grouped by the boundary.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `GET /elements/count/ratio/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `ratio of osm elements grouped by the boundary` (`GET /elements/count/ratio/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 64: Ratio Of Osm Elements Grouped By The Boundary

Business goal:
Ratio of OSM elements grouped by the boundary.

Domain context:
This behavior belongs to the `Elements Count` capability area and operates through `POST /elements/count/ratio/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `ratio of osm elements grouped by the boundary` (`POST /elements/count/ratio/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements grouped by the boundary`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 65: Length Of Osm Elements

Business goal:
Length of OSM elements.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `length of osm elements` (`GET /elements/length`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `length of osm elements`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 66: Length Of Osm Elements

Business goal:
Length of OSM elements.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `length of osm elements` (`POST /elements/length`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `length of osm elements`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 67: Density Of Osm Elements Length Of Elements Divided By The Total Area In Square Kilometers

Business goal:
Density of OSM elements (length of elements divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length/density`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements length of elements divided by the total area in square kilometers` (`GET /elements/length/density`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 68: Density Of Osm Elements Length Of Elements Divided By The Total Area In Square Kilometers

Business goal:
Density of OSM elements (length of elements divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length/density`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements length of elements divided by the total area in square kilometers` (`POST /elements/length/density`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements length of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 69: Density Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length/density/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary bboxes bcircles or bpolys` (`GET /elements/length/density/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 70: Density Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length/density/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary bboxes bcircles or bpolys` (`POST /elements/length/density/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 71: Density Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Density of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length/density/groupBy/boundary/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary and the tag` (`GET /elements/length/density/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 72: Density Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Density of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length/density/groupBy/boundary/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary and the tag` (`POST /elements/length/density/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 73: Density Of Osm Elements Grouped By The Tag

Business goal:
Density of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length/density/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the tag` (`GET /elements/length/density/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 74: Density Of Osm Elements Grouped By The Tag

Business goal:
Density of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length/density/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the tag` (`POST /elements/length/density/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 75: Density Of Osm Elements Grouped By The Osm Type

Business goal:
Density of OSM elements grouped by the OSM type.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length/density/groupBy/type`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the osm type` (`GET /elements/length/density/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 76: Density Of Osm Elements Grouped By The Osm Type

Business goal:
Density of OSM elements grouped by the OSM type.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length/density/groupBy/type`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the osm type` (`POST /elements/length/density/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the osm type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 77: Length Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Length of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `length of osm elements grouped by the boundary bboxes bcircles or bpolys` (`GET /elements/length/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 78: Length Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Length of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `length of osm elements grouped by the boundary bboxes bcircles or bpolys` (`POST /elements/length/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 79: Length Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Length of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length/groupBy/boundary/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `length of osm elements grouped by the boundary and the tag` (`GET /elements/length/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 80: Length Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Length of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length/groupBy/boundary/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `length of osm elements grouped by the boundary and the tag` (`POST /elements/length/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 81: Length Of Osm Elements Grouped By The Key

Business goal:
Length of OSM elements grouped by the key.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length/groupBy/key`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `length of osm elements grouped by the key` (`GET /elements/length/groupBy/key`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKeys required, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKeys`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKeys`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 82: Length Of Osm Elements Grouped By The Key

Business goal:
Length of OSM elements grouped by the key.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length/groupBy/key`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `length of osm elements grouped by the key` (`POST /elements/length/groupBy/key`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKeys required, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKeys`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKeys`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the key`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 83: Length Of Osm Elements Grouped By The Tag

Business goal:
Length of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `length of osm elements grouped by the tag` (`GET /elements/length/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 84: Length Of Osm Elements Grouped By The Tag

Business goal:
Length of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `length of osm elements grouped by the tag` (`POST /elements/length/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 85: Length Of Osm Elements Grouped By The Type

Business goal:
Length of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length/groupBy/type`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `length of osm elements grouped by the type` (`GET /elements/length/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 86: Length Of Osm Elements Grouped By The Type

Business goal:
Length of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length/groupBy/type`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `length of osm elements grouped by the type` (`POST /elements/length/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `length of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 87: Ratio Of Osm Elements Satisfying Filter2 Within Items Selected By Filter

Business goal:
Ratio of OSM elements satisfying filter2 within items selected by filter.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length/ratio`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `ratio of osm elements satisfying filter2 within items selected by filter` (`GET /elements/length/ratio`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 88: Ratio Of Osm Elements Satisfying Filter2 Within Items Selected By Filter

Business goal:
Ratio of OSM elements satisfying filter2 within items selected by filter.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length/ratio`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `ratio of osm elements satisfying filter2 within items selected by filter` (`POST /elements/length/ratio`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 89: Ratio Of The Length Of Osm Elements Grouped By The Boundary

Business goal:
Ratio of the length of OSM elements grouped by the boundary.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `GET /elements/length/ratio/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `ratio of the length of osm elements grouped by the boundary` (`GET /elements/length/ratio/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 90: Ratio Of The Length Of Osm Elements Grouped By The Boundary

Business goal:
Ratio of the length of OSM elements grouped by the boundary.

Domain context:
This behavior belongs to the `Elements Length` capability area and operates through `POST /elements/length/ratio/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `ratio of the length of osm elements grouped by the boundary` (`POST /elements/length/ratio/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the length of osm elements grouped by the boundary`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 91: Perimeter Of Osm Elements

Business goal:
Perimeter of OSM elements.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `perimeter of osm elements` (`GET /elements/perimeter`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 92: Perimeter Of Osm Elements

Business goal:
Perimeter of OSM elements.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `perimeter of osm elements` (`POST /elements/perimeter`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 93: Density Of Osm Elements Perimeter Of Elements Divided By The Total Area In Square Kilometers

Business goal:
Density of OSM elements (perimeter of elements divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter/density`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements perimeter of elements divided by the total area in square kilometers` (`GET /elements/perimeter/density`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 94: Density Of Osm Elements Perimeter Of Elements Divided By The Total Area In Square Kilometers

Business goal:
Density of OSM elements (perimeter of elements divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter/density`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements perimeter of elements divided by the total area in square kilometers` (`POST /elements/perimeter/density`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements perimeter of elements divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 95: Density Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter/density/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary bboxes bcircles or bpolys` (`GET /elements/perimeter/density/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 96: Density Of Osm Elements Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Density of OSM elements grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter/density/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the boundary bboxes bcircles or bpolys` (`POST /elements/perimeter/density/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 97: Density Of Grouped By The Boundary And The Tag

Business goal:
Density of grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter/density/groupBy/boundary/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of grouped by the boundary and the tag` (`GET /elements/perimeter/density/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 98: Density Of Grouped By The Boundary And The Tag

Business goal:
Density of grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter/density/groupBy/boundary/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of grouped by the boundary and the tag` (`POST /elements/perimeter/density/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 99: Density Of Osm Elements Grouped By The Tag

Business goal:
Density of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter/density/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the tag` (`GET /elements/perimeter/density/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 100: Density Of Osm Elements Grouped By The Tag

Business goal:
Density of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter/density/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the tag` (`POST /elements/perimeter/density/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 101: Density Of Osm Elements Grouped By The Type

Business goal:
Density of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter/density/groupBy/type`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm elements grouped by the type` (`GET /elements/perimeter/density/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 102: Density Of Osm Elements Grouped By The Type

Business goal:
Density of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter/density/groupBy/type`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm elements grouped by the type` (`POST /elements/perimeter/density/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 103: Perimeter Of Osm Elements In Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Perimeter of OSM elements in grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys` (`GET /elements/perimeter/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 104: Perimeter Of Osm Elements In Grouped By The Boundary Bboxes Bcircles Or Bpolys

Business goal:
Perimeter of OSM elements in grouped by the boundary (bboxes, bcircles, or bpolys).

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys` (`POST /elements/perimeter/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements in grouped by the boundary bboxes bcircles or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 105: Perimeter Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Perimeter of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter/groupBy/boundary/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `perimeter of osm elements grouped by the boundary and the tag` (`GET /elements/perimeter/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 106: Perimeter Of Osm Elements Grouped By The Boundary And The Tag

Business goal:
Perimeter of OSM elements grouped by the boundary and the tag.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter/groupBy/boundary/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `perimeter of osm elements grouped by the boundary and the tag` (`POST /elements/perimeter/groupBy/boundary/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the boundary and the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 107: Perimeter Of Osm Elements Grouped By The Key

Business goal:
Perimeter of OSM elements grouped by the key.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter/groupBy/key`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `perimeter of osm elements grouped by the key` (`GET /elements/perimeter/groupBy/key`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKeys required, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKeys`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKeys`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 108: Perimeter Of Osm Elements Grouped By The Key

Business goal:
Perimeter of OSM elements grouped by the key.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter/groupBy/key`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `perimeter of osm elements grouped by the key` (`POST /elements/perimeter/groupBy/key`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKeys required, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKeys`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKeys`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the key`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 109: Perimeter Of Osm Elements Grouped By The Tag

Business goal:
Perimeter of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `perimeter of osm elements grouped by the tag` (`GET /elements/perimeter/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 110: Perimeter Of Osm Elements Grouped By The Tag

Business goal:
Perimeter of OSM elements grouped by the tag.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `perimeter of osm elements grouped by the tag` (`POST /elements/perimeter/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 111: Perimeter Of Osm Elements Grouped By The Type

Business goal:
Perimeter of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter/groupBy/type`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `perimeter of osm elements grouped by the type` (`GET /elements/perimeter/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 112: Perimeter Of Osm Elements Grouped By The Type

Business goal:
Perimeter of OSM elements grouped by the type.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter/groupBy/type`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `perimeter of osm elements grouped by the type` (`POST /elements/perimeter/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `perimeter of osm elements grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 113: Ratio Of Osm Elements Satisfying Filter2 Within Items Selected By Filter

Business goal:
Ratio of OSM elements satisfying filter2 within items selected by filter.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter/ratio`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `ratio of osm elements satisfying filter2 within items selected by filter` (`GET /elements/perimeter/ratio`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 114: Ratio Of Osm Elements Satisfying Filter2 Within Items Selected By Filter

Business goal:
Ratio of OSM elements satisfying filter2 within items selected by filter.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter/ratio`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `ratio of osm elements satisfying filter2 within items selected by filter` (`POST /elements/perimeter/ratio`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of osm elements satisfying filter2 within items selected by filter`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 115: Ratio Of The Perimeter Of Osm Elements Grouped By The Boundary

Business goal:
Ratio of the perimeter of OSM elements grouped by the boundary.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `GET /elements/perimeter/ratio/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `ratio of the perimeter of osm elements grouped by the boundary` (`GET /elements/perimeter/ratio/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 116: Ratio Of The Perimeter Of Osm Elements Grouped By The Boundary

Business goal:
Ratio of the perimeter of OSM elements grouped by the boundary.

Domain context:
This behavior belongs to the `Elements Perimeter` capability area and operates through `POST /elements/perimeter/ratio/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `ratio of the perimeter of osm elements grouped by the boundary` (`POST /elements/perimeter/ratio/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, filter optional, filter2 optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `filter`, `filter2`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `ratio of the perimeter of osm elements grouped by the boundary`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 117: Count Of Osm Users

Business goal:
Count of OSM users.

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `GET /users/count`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm users` (`GET /users/count`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm users`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 118: Count Of Osm Users

Business goal:
Count of OSM users.

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `POST /users/count`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm users` (`POST /users/count`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm users`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 119: Density Of Osm Users Number Of Users Divided By The Total Area In Square Kilometers

Business goal:
Density of OSM users (number of users divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `GET /users/count/density`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm users number of users divided by the total area in square kilometers` (`GET /users/count/density`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 120: Density Of Osm Users Number Of Users Divided By The Total Area In Square Kilometers

Business goal:
Density of OSM users (number of users divided by the total area in square-kilometers).

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `POST /users/count/density`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm users number of users divided by the total area in square kilometers` (`POST /users/count/density`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users number of users divided by the total area in square kilometers`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 121: Count Of Osm Users Grouped By Boundary Bboxes Bcirlces Or Bpolys

Business goal:
Count of OSM users grouped by boundary (bboxes, bcirlces, or bpolys).

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `GET /users/count/density/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm users grouped by boundary bboxes bcirlces or bpolys` (`GET /users/count/density/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 122: Count Of Osm Users Grouped By Boundary Bboxes Bcirlces Or Bpolys

Business goal:
Count of OSM users grouped by boundary (bboxes, bcirlces, or bpolys).

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `POST /users/count/density/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm users grouped by boundary bboxes bcirlces or bpolys` (`POST /users/count/density/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 123: Density Of Osm Users Grouped By The Tag

Business goal:
Density of OSM users grouped by the tag.

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `GET /users/count/density/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm users grouped by the tag` (`GET /users/count/density/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 124: Density Of Osm Users Grouped By The Tag

Business goal:
Density of OSM users grouped by the tag.

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `POST /users/count/density/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm users grouped by the tag` (`POST /users/count/density/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 125: Density Of Osm Users Grouped By The Type

Business goal:
Density of OSM users grouped by the type.

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `GET /users/count/density/groupBy/type`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `density of osm users grouped by the type` (`GET /users/count/density/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 126: Density Of Osm Users Grouped By The Type

Business goal:
Density of OSM users grouped by the type.

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `POST /users/count/density/groupBy/type`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `density of osm users grouped by the type` (`POST /users/count/density/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `density of osm users grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 127: Count Of Osm Users Grouped By Boundary Bboxes Bcirlces Or Bpolys

Business goal:
Count of OSM users grouped by boundary (bboxes, bcirlces, or bpolys).

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `GET /users/count/groupBy/boundary`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm users grouped by boundary bboxes bcirlces or bpolys` (`GET /users/count/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 128: Count Of Osm Users Grouped By Boundary Bboxes Bcirlces Or Bpolys

Business goal:
Count of OSM users grouped by boundary (bboxes, bcirlces, or bpolys).

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `POST /users/count/groupBy/boundary`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm users grouped by boundary bboxes bcirlces or bpolys` (`POST /users/count/groupBy/boundary`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by boundary bboxes bcirlces or bpolys`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 129: Count Of Osm Users Grouped By The Key

Business goal:
Count of OSM users grouped by the key.

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `GET /users/count/groupBy/key`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm users grouped by the key` (`GET /users/count/groupBy/key`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, groupByKeys required, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `groupByKeys`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKeys`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 130: Count Of Osm Users Grouped By The Key

Business goal:
Count of OSM users grouped by the key.

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `POST /users/count/groupBy/key`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm users grouped by the key` (`POST /users/count/groupBy/key`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, groupByKeys required, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `groupByKeys`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKeys`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the key`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 131: Count Of Osm Users Grouped By The Tag

Business goal:
Count of OSM users grouped by the tag.

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `GET /users/count/groupBy/tag`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm users grouped by the tag` (`GET /users/count/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 132: Count Of Osm Users Grouped By The Tag

Business goal:
Count of OSM users grouped by the tag.

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `POST /users/count/groupBy/tag`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm users grouped by the tag` (`POST /users/count/groupBy/tag`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, groupByKey required, groupByValues optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `groupByKey`, `groupByValues`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `groupByKey`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the tag`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 133: Count Of Osm Users Grouped By The Type

Business goal:
Count of OSM users grouped by the type.

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `GET /users/count/groupBy/type`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of osm users grouped by the type` (`GET /users/count/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.

### Behavior 134: Count Of Osm Users Grouped By The Type

Business goal:
Count of OSM users grouped by the type.

Domain context:
This behavior belongs to the `Users Count` capability area and operates through `POST /users/count/groupBy/type`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of osm users grouped by the type` (`POST /users/count/groupBy/type`) with query: bboxes optional, bcircles optional, bpolys optional, contributionType optional, filter optional, format optional, showMetadata optional, time optional, timeout optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bboxes`, `bcircles`, `bpolys`, `contributionType`, `filter`, `format`, `showMetadata`, `time`, `timeout` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `400` response.
  - Why it fails: Bad request
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `405` response.
  - Why it fails: Method not allowed
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `413` response.
  - Why it fails: Payload too large
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `500` response.
  - Why it fails: Internal server error
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `501` response.
  - Why it fails: Not implemented
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of osm users grouped by the type`
  - Failure condition: HTTP `503` response.
  - Why it fails: Service Unavailable
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 400 Bad request; 401 Unauthorized; 404 Not found; 405 Method not allowed; 413 Payload too large; 500 Internal server error; 501 Not implemented; 503 Service Unavailable.
