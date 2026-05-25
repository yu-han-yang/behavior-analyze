Scope note: the implementation source also contains mappings such as `/metadata`, `/elements/geometry`, `/elementsFullHistory/...`, and data-extraction `/contributions/...` endpoints that are not present in `ohsome-api.json`. Because the OpenAPI file in this directory does not expose them, they are excluded from the Swagger-supported function list below.

### Function 1: calculate OSM element metric

Function name:
calculate OSM element metric

Core endpoint(s):
- `GET /elements/count`
- `POST /elements/count`
- `GET /elements/length`
- `POST /elements/length`
- `GET /elements/area`
- `POST /elements/area`
- `GET /elements/perimeter`
- `POST /elements/perimeter`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns a time series for one selected OSM element metric: count, length, area, or perimeter.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count`, `POST /elements/count`, `GET /elements/length`, `POST /elements/length`, `GET /elements/area`, `POST /elements/area`, `GET /elements/perimeter`, or `POST /elements/perimeter` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Provide at least one request parameter. For predictable success, provide exactly one spatial boundary parameter: `bboxes`, `bcircles`, or `bpolys`. `time`, when supplied, must be ISO-8601. `format` may be `json` or `csv`. `showMetadata` must be boolean-like if supplied. The selected path determines the metric; no response value is reused by another endpoint.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No query or form parameter is supplied to the failure endpoint, so the boundary state and all other request-scoped inputs are intentionally omitted.
  - Failure endpoint:
    `GET /elements/count`
  - Why this fails:
    The implementation rejects requests whose parameter map is empty.
  - Intentionally violated constraints:
    Omitted all request parameters, including boundary input.
- Branch 2:
  - Preconditions:
    - The core request supplies more than one boundary parameter type, such as both `bboxes` and `bpolys` or both `bboxes` and `bcircles`, creating an ambiguous area-of-interest state.
  - Failure endpoint:
    `GET /elements/count`
  - Why this fails:
    The implementation accepts only one of `bboxes`, `bcircles`, or `bpolys`.
  - Intentionally violated constraints:
    Supplied both `bboxes` and `bpolys`.

Endpoint coverage:
- Covers:
  `GET /elements/count`
  `POST /elements/count`
- Distinct meaning:
  Count matching OSM elements.
- Covers:
  `GET /elements/length`
  `POST /elements/length`
- Distinct meaning:
  Sum matching element geometry length.
- Covers:
  `GET /elements/area`
  `POST /elements/area`
- Distinct meaning:
  Sum matching element polygon area.
- Covers:
  `GET /elements/perimeter`
  `POST /elements/perimeter`
- Distinct meaning:
  Sum matching polygon perimeter.

### Function 2: calculate OSM element metric density

Function name:
calculate OSM element metric density

Core endpoint(s):
- `GET /elements/count/density`
- `POST /elements/count/density`
- `GET /elements/length/density`
- `POST /elements/length/density`
- `GET /elements/area/density`
- `POST /elements/area/density`
- `GET /elements/perimeter/density`
- `POST /elements/perimeter/density`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns the selected element metric divided by the requested boundary area in square kilometers.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count/density`, `POST /elements/count/density`, `GET /elements/length/density`, `POST /elements/length/density`, `GET /elements/area/density`, `POST /elements/area/density`, `GET /elements/perimeter/density`, or `POST /elements/perimeter/density` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Provide at least one request parameter and exactly one spatial boundary parameter for predictable success. The implementation computes density from the selected metric value and the spatial boundary geometry area. `time`, `format`, `showMetadata`, and `timeout` follow the same validation as non-density element metrics.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits `bboxes`, `bcircles`, and `bpolys`, and no usable extract polygon state is available to substitute for the missing boundary.
  - Failure endpoint:
    `GET /elements/count/density`
  - Why this fails:
    Density requires a geometry area; the implementation cannot establish one without a usable boundary.
  - Intentionally violated constraints:
    Omitted `bboxes`, `bcircles`, and `bpolys`.
- Branch 2:
  - Preconditions:
    - The core request supplies a `timeout` value that is non-numeric or larger than the configured maximum request timeout.
  - Failure endpoint:
    `GET /elements/count/density`
  - Why this fails:
    The implementation parses `timeout` as seconds and rejects invalid or too-large values.
  - Intentionally violated constraints:
    Supplied an invalid `timeout`.

Endpoint coverage:
- Covers:
  `GET /elements/count/density`
  `POST /elements/count/density`
- Distinct meaning:
  Count elements per square kilometer.
- Covers:
  `GET /elements/length/density`
  `POST /elements/length/density`
- Distinct meaning:
  Element length per square kilometer.
- Covers:
  `GET /elements/area/density`
  `POST /elements/area/density`
- Distinct meaning:
  Element area per square kilometer.
- Covers:
  `GET /elements/perimeter/density`
  `POST /elements/perimeter/density`
- Distinct meaning:
  Element perimeter per square kilometer.

### Function 3: group OSM element metric by type

Function name:
group OSM element metric by type

Core endpoint(s):
- `GET /elements/count/groupBy/type`
- `POST /elements/count/groupBy/type`
- `GET /elements/length/groupBy/type`
- `POST /elements/length/groupBy/type`
- `GET /elements/area/groupBy/type`
- `POST /elements/area/groupBy/type`
- `GET /elements/perimeter/groupBy/type`
- `POST /elements/perimeter/groupBy/type`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns the selected element metric split by OSM type, such as node, way, or relation.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count/groupBy/type`, `POST /elements/count/groupBy/type`, `GET /elements/length/groupBy/type`, `POST /elements/length/groupBy/type`, `GET /elements/area/groupBy/type`, `POST /elements/area/groupBy/type`, `GET /elements/perimeter/groupBy/type`, or `POST /elements/perimeter/groupBy/type` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Provide at least one request parameter and exactly one spatial boundary parameter. `filter`, if supplied, must parse successfully and must not use changeset-only filters on these snapshot-based element endpoints.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request supplies a malformed `filter` value that cannot be parsed by the OSHDB filter parser.
  - Failure endpoint:
    `GET /elements/count/groupBy/type`
  - Why this fails:
    The implementation parses `filter` with the OSHDB filter parser and rejects parser errors.
  - Intentionally violated constraints:
    Supplied a malformed `filter`.
- Branch 2:
  - Preconditions:
    - The core request targets a snapshot-based element endpoint while supplying a changeset-only filter expression.
  - Failure endpoint:
    `GET /elements/count/groupBy/type`
  - Why this fails:
    Changeset filters are accepted only on contribution-based endpoints.
  - Intentionally violated constraints:
    Used a contribution-only filter on an element snapshot query.

Endpoint coverage:
- Covers:
  `GET /elements/count/groupBy/type`
  `POST /elements/count/groupBy/type`
- Distinct meaning:
  Count elements grouped by OSM type.
- Covers:
  `GET /elements/length/groupBy/type`
  `POST /elements/length/groupBy/type`
- Distinct meaning:
  Sum element length grouped by OSM type.
- Covers:
  `GET /elements/area/groupBy/type`
  `POST /elements/area/groupBy/type`
- Distinct meaning:
  Sum element area grouped by OSM type.
- Covers:
  `GET /elements/perimeter/groupBy/type`
  `POST /elements/perimeter/groupBy/type`
- Distinct meaning:
  Sum element perimeter grouped by OSM type.

### Function 4: group OSM element metric density by type

Function name:
group OSM element metric density by type

Core endpoint(s):
- `GET /elements/count/density/groupBy/type`
- `POST /elements/count/density/groupBy/type`
- `GET /elements/length/density/groupBy/type`
- `POST /elements/length/density/groupBy/type`
- `GET /elements/area/density/groupBy/type`
- `POST /elements/area/density/groupBy/type`
- `GET /elements/perimeter/density/groupBy/type`
- `POST /elements/perimeter/density/groupBy/type`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns the selected element metric density split by OSM type.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count/density/groupBy/type`, `POST /elements/count/density/groupBy/type`, `GET /elements/length/density/groupBy/type`, `POST /elements/length/density/groupBy/type`, `GET /elements/area/density/groupBy/type`, `POST /elements/area/density/groupBy/type`, `GET /elements/perimeter/density/groupBy/type`, or `POST /elements/perimeter/density/groupBy/type` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  The selected endpoint uses snapshot data, groups by OSM type, and divides each metric value by boundary area. Exactly one usable boundary parameter is needed for predictable density calculation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits `bboxes`, `bcircles`, and `bpolys`, and no usable extract polygon state is available to substitute for the missing boundary.
  - Failure endpoint:
    `GET /elements/count/density/groupBy/type`
  - Why this fails:
    Density and input processing need a spatial area of interest.
  - Intentionally violated constraints:
    Omitted the boundary parameters.
- Branch 2:
  - Preconditions:
    - The core request supplies `showMetadata` with a value other than `true`, `yes`, `false`, `no`, or blank.
  - Failure endpoint:
    `GET /elements/count/density/groupBy/type`
  - Why this fails:
    The implementation accepts only `true`, `yes`, `false`, `no`, or blank for `showMetadata`.
  - Intentionally violated constraints:
    Supplied an unsupported `showMetadata` value.

Endpoint coverage:
- Covers:
  `GET /elements/count/density/groupBy/type`
  `POST /elements/count/density/groupBy/type`
- Distinct meaning:
  Count density grouped by OSM type.
- Covers:
  `GET /elements/length/density/groupBy/type`
  `POST /elements/length/density/groupBy/type`
- Distinct meaning:
  Length density grouped by OSM type.
- Covers:
  `GET /elements/area/density/groupBy/type`
  `POST /elements/area/density/groupBy/type`
- Distinct meaning:
  Area density grouped by OSM type.
- Covers:
  `GET /elements/perimeter/density/groupBy/type`
  `POST /elements/perimeter/density/groupBy/type`
- Distinct meaning:
  Perimeter density grouped by OSM type.

### Function 5: group OSM element metric by boundary

Function name:
group OSM element metric by boundary

Core endpoint(s):
- `GET /elements/count/groupBy/boundary`
- `POST /elements/count/groupBy/boundary`
- `GET /elements/length/groupBy/boundary`
- `POST /elements/length/groupBy/boundary`
- `GET /elements/area/groupBy/boundary`
- `POST /elements/area/groupBy/boundary`
- `GET /elements/perimeter/groupBy/boundary`
- `POST /elements/perimeter/groupBy/boundary`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns the selected element metric separately for each requested boundary object.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count/groupBy/boundary`, `POST /elements/count/groupBy/boundary`, `GET /elements/length/groupBy/boundary`, `POST /elements/length/groupBy/boundary`, `GET /elements/area/groupBy/boundary`, `POST /elements/area/groupBy/boundary`, `GET /elements/perimeter/groupBy/boundary`, or `POST /elements/perimeter/groupBy/boundary` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Exactly one boundary parameter must be supplied, and it may contain multiple boundary objects. Boundary IDs are taken from supplied IDs or generated automatically. `format=geojson` is meaningful here and returns a GeoJSON feature collection; `csv` and `json` are also supported.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request supplies malformed boundary coordinates in `bboxes`, `bcircles`, or `bpolys`, so the boundary geometry cannot be built.
  - Failure endpoint:
    `GET /elements/count/groupBy/boundary`
  - Why this fails:
    Boundary parsing rejects wrong coordinate counts or non-numeric coordinate values.
  - Intentionally violated constraints:
    Supplied an invalid `bboxes`, `bcircles`, or `bpolys` value.
- Branch 2:
  - Preconditions:
    - The core request supplies a boundary geometry that lies outside the configured data-extract polygon.
  - Failure endpoint:
    `GET /elements/count/groupBy/boundary`
  - Why this fails:
    Geometry validation checks that the requested boundary is within the data polygon when one is configured.
  - Intentionally violated constraints:
    Supplied a boundary outside the available dataset.

Endpoint coverage:
- Covers:
  `GET /elements/count/groupBy/boundary`
  `POST /elements/count/groupBy/boundary`
- Distinct meaning:
  Count elements per boundary.
- Covers:
  `GET /elements/length/groupBy/boundary`
  `POST /elements/length/groupBy/boundary`
- Distinct meaning:
  Sum element length per boundary.
- Covers:
  `GET /elements/area/groupBy/boundary`
  `POST /elements/area/groupBy/boundary`
- Distinct meaning:
  Sum element area per boundary.
- Covers:
  `GET /elements/perimeter/groupBy/boundary`
  `POST /elements/perimeter/groupBy/boundary`
- Distinct meaning:
  Sum element perimeter per boundary.

### Function 6: group OSM element metric density by boundary

Function name:
group OSM element metric density by boundary

Core endpoint(s):
- `GET /elements/count/density/groupBy/boundary`
- `POST /elements/count/density/groupBy/boundary`
- `GET /elements/length/density/groupBy/boundary`
- `POST /elements/length/density/groupBy/boundary`
- `GET /elements/area/density/groupBy/boundary`
- `POST /elements/area/density/groupBy/boundary`
- `GET /elements/perimeter/density/groupBy/boundary`
- `POST /elements/perimeter/density/groupBy/boundary`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns the selected element metric density separately for each requested boundary object.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count/density/groupBy/boundary`, `POST /elements/count/density/groupBy/boundary`, `GET /elements/length/density/groupBy/boundary`, `POST /elements/length/density/groupBy/boundary`, `GET /elements/area/density/groupBy/boundary`, `POST /elements/area/density/groupBy/boundary`, `GET /elements/perimeter/density/groupBy/boundary`, or `POST /elements/perimeter/density/groupBy/boundary` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Exactly one boundary parameter must be supplied. Each group’s metric is divided by that boundary group’s area. `format=geojson`, `json`, and `csv` are supported for these boundary-grouped responses.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request supplies more than one boundary parameter type, such as both `bboxes` and `bpolys` or both `bboxes` and `bcircles`, creating an ambiguous area-of-interest state.
  - Failure endpoint:
    `GET /elements/count/density/groupBy/boundary`
  - Why this fails:
    The implementation accepts one boundary parameter type per request.
  - Intentionally violated constraints:
    Supplied both `bboxes` and `bcircles`.
- Branch 2:
  - Preconditions:
    - The core request supplies `format` with a value other than the supported `json`, `csv`, or `geojson` values.
  - Failure endpoint:
    `GET /elements/count/density/groupBy/boundary`
  - Why this fails:
    Format validation accepts only `json`, `csv`, or `geojson`.
  - Intentionally violated constraints:
    Supplied an unsupported `format`.

Endpoint coverage:
- Covers:
  `GET /elements/count/density/groupBy/boundary`
  `POST /elements/count/density/groupBy/boundary`
- Distinct meaning:
  Count density per boundary.
- Covers:
  `GET /elements/length/density/groupBy/boundary`
  `POST /elements/length/density/groupBy/boundary`
- Distinct meaning:
  Length density per boundary.
- Covers:
  `GET /elements/area/density/groupBy/boundary`
  `POST /elements/area/density/groupBy/boundary`
- Distinct meaning:
  Area density per boundary.
- Covers:
  `GET /elements/perimeter/density/groupBy/boundary`
  `POST /elements/perimeter/density/groupBy/boundary`
- Distinct meaning:
  Perimeter density per boundary.

### Function 7: group OSM element metric by tag

Function name:
group OSM element metric by tag

Core endpoint(s):
- `GET /elements/count/groupBy/tag`
- `POST /elements/count/groupBy/tag`
- `GET /elements/length/groupBy/tag`
- `POST /elements/length/groupBy/tag`
- `GET /elements/area/groupBy/tag`
- `POST /elements/area/groupBy/tag`
- `GET /elements/perimeter/groupBy/tag`
- `POST /elements/perimeter/groupBy/tag`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.
- For tag grouping, the grouping key is supplied as `groupByKey` on the core request, and optional `groupByValues` are supplied on the same request; these values are not established by a setup endpoint.

Successful execution:
- Result:
  This function returns the selected element metric grouped by values of one requested OSM tag key.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count/groupBy/tag`, `POST /elements/count/groupBy/tag`, `GET /elements/length/groupBy/tag`, `POST /elements/length/groupBy/tag`, `GET /elements/area/groupBy/tag`, `POST /elements/area/groupBy/tag`, `GET /elements/perimeter/groupBy/tag`, or `POST /elements/perimeter/groupBy/tag` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  `groupByKey` must contain exactly one key. `groupByValues` is optional and limits the returned values; unmatched elements go into a remainder group. Provide a usable boundary and valid time/filter parameters.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits `groupByKey` or supplies more than one key for an endpoint that requires exactly one grouping key.
  - Failure endpoint:
    `GET /elements/count/groupBy/tag`
  - Why this fails:
    The implementation requires exactly one `groupByKey` for `/groupBy/tag`.
  - Intentionally violated constraints:
    Omitted `groupByKey` or supplied a comma-separated multi-key value.
- Branch 2:
  - Preconditions:
    - The core request repeats one query or form parameter instead of providing a single value for that parameter.
  - Failure endpoint:
    `GET /elements/count/groupBy/tag`
  - Why this fails:
    The implementation rejects any parameter with more than one submitted value.
  - Intentionally violated constraints:
    Repeated a query/form parameter instead of using one value.

Endpoint coverage:
- Covers:
  `GET /elements/count/groupBy/tag`
  `POST /elements/count/groupBy/tag`
- Distinct meaning:
  Count elements by tag value.
- Covers:
  `GET /elements/length/groupBy/tag`
  `POST /elements/length/groupBy/tag`
- Distinct meaning:
  Sum element length by tag value.
- Covers:
  `GET /elements/area/groupBy/tag`
  `POST /elements/area/groupBy/tag`
- Distinct meaning:
  Sum element area by tag value.
- Covers:
  `GET /elements/perimeter/groupBy/tag`
  `POST /elements/perimeter/groupBy/tag`
- Distinct meaning:
  Sum element perimeter by tag value.

### Function 8: group OSM element metric density by tag

Function name:
group OSM element metric density by tag

Core endpoint(s):
- `GET /elements/count/density/groupBy/tag`
- `POST /elements/count/density/groupBy/tag`
- `GET /elements/length/density/groupBy/tag`
- `POST /elements/length/density/groupBy/tag`
- `GET /elements/area/density/groupBy/tag`
- `POST /elements/area/density/groupBy/tag`
- `GET /elements/perimeter/density/groupBy/tag`
- `POST /elements/perimeter/density/groupBy/tag`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.
- For tag grouping, the grouping key is supplied as `groupByKey` on the core request, and optional `groupByValues` are supplied on the same request; these values are not established by a setup endpoint.

Successful execution:
- Result:
  This function returns the selected element metric density grouped by values of one requested OSM tag key.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count/density/groupBy/tag`, `POST /elements/count/density/groupBy/tag`, `GET /elements/length/density/groupBy/tag`, `POST /elements/length/density/groupBy/tag`, `GET /elements/area/density/groupBy/tag`, `POST /elements/area/density/groupBy/tag`, `GET /elements/perimeter/density/groupBy/tag`, or `POST /elements/perimeter/density/groupBy/tag` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  `groupByKey` must contain exactly one key. `groupByValues` is optional. A usable boundary is required because the grouped values are density values.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits the required `groupByKey` parameter for a tag-grouped endpoint.
  - Failure endpoint:
    `GET /elements/count/density/groupBy/tag`
  - Why this fails:
    Tag grouping cannot be computed without the tag key to group by.
  - Intentionally violated constraints:
    Omitted `groupByKey`.
- Branch 2:
  - Preconditions:
    - The core request supplies malformed boundary coordinates in `bboxes`, `bcircles`, or `bpolys`, so the boundary geometry cannot be built.
  - Failure endpoint:
    `GET /elements/count/density/groupBy/tag`
  - Why this fails:
    Input processing cannot build a geometry for density calculation.
  - Intentionally violated constraints:
    Supplied invalid boundary coordinates.

Endpoint coverage:
- Covers:
  `GET /elements/count/density/groupBy/tag`
  `POST /elements/count/density/groupBy/tag`
- Distinct meaning:
  Count density by tag value.
- Covers:
  `GET /elements/length/density/groupBy/tag`
  `POST /elements/length/density/groupBy/tag`
- Distinct meaning:
  Length density by tag value.
- Covers:
  `GET /elements/area/density/groupBy/tag`
  `POST /elements/area/density/groupBy/tag`
- Distinct meaning:
  Area density by tag value.
- Covers:
  `GET /elements/perimeter/density/groupBy/tag`
  `POST /elements/perimeter/density/groupBy/tag`
- Distinct meaning:
  Perimeter density by tag value.

### Function 9: group OSM element metric by key

Function name:
group OSM element metric by key

Core endpoint(s):
- `GET /elements/count/groupBy/key`
- `POST /elements/count/groupBy/key`
- `GET /elements/length/groupBy/key`
- `POST /elements/length/groupBy/key`
- `GET /elements/area/groupBy/key`
- `POST /elements/area/groupBy/key`
- `GET /elements/perimeter/groupBy/key`
- `POST /elements/perimeter/groupBy/key`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.
- For key grouping, the grouping keys are supplied as `groupByKeys` on the core request; these values are not established by a setup endpoint.

Successful execution:
- Result:
  This function returns the selected element metric grouped by one or more requested OSM tag keys.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count/groupBy/key`, `POST /elements/count/groupBy/key`, `GET /elements/length/groupBy/key`, `POST /elements/length/groupBy/key`, `GET /elements/area/groupBy/key`, `POST /elements/area/groupBy/key`, `GET /elements/perimeter/groupBy/key`, or `POST /elements/perimeter/groupBy/key` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  `groupByKeys` must contain at least one key. Multiple keys may be comma-separated in the single parameter value. Elements without the requested keys are placed in a remainder group.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits the required `groupByKeys` parameter for a key-grouped endpoint.
  - Failure endpoint:
    `GET /elements/count/groupBy/key`
  - Why this fails:
    The implementation requires `groupByKeys` for `/groupBy/key`.
  - Intentionally violated constraints:
    Omitted `groupByKeys`.
- Branch 2:
  - Preconditions:
    - The core request includes a parameter name that is not allowed for this resource-specific endpoint.
  - Failure endpoint:
    `GET /elements/count/groupBy/key`
  - Why this fails:
    The implementation checks resource-specific allowed parameters and rejects unknown names.
  - Intentionally violated constraints:
    Supplied a parameter not allowed for this resource.

Endpoint coverage:
- Covers:
  `GET /elements/count/groupBy/key`
  `POST /elements/count/groupBy/key`
- Distinct meaning:
  Count elements by tag key.
- Covers:
  `GET /elements/length/groupBy/key`
  `POST /elements/length/groupBy/key`
- Distinct meaning:
  Sum element length by tag key.
- Covers:
  `GET /elements/area/groupBy/key`
  `POST /elements/area/groupBy/key`
- Distinct meaning:
  Sum element area by tag key.
- Covers:
  `GET /elements/perimeter/groupBy/key`
  `POST /elements/perimeter/groupBy/key`
- Distinct meaning:
  Sum element perimeter by tag key.

### Function 10: group OSM element metric by boundary and tag

Function name:
group OSM element metric by boundary and tag

Core endpoint(s):
- `GET /elements/count/groupBy/boundary/groupBy/tag`
- `POST /elements/count/groupBy/boundary/groupBy/tag`
- `GET /elements/length/groupBy/boundary/groupBy/tag`
- `POST /elements/length/groupBy/boundary/groupBy/tag`
- `GET /elements/area/groupBy/boundary/groupBy/tag`
- `POST /elements/area/groupBy/boundary/groupBy/tag`
- `GET /elements/perimeter/groupBy/boundary/groupBy/tag`
- `POST /elements/perimeter/groupBy/boundary/groupBy/tag`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.
- For tag grouping, the grouping key is supplied as `groupByKey` on the core request, and optional `groupByValues` are supplied on the same request; these values are not established by a setup endpoint.

Successful execution:
- Result:
  This function returns the selected element metric split first by boundary and then by values of one requested tag key.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count/groupBy/boundary/groupBy/tag`, `POST /elements/count/groupBy/boundary/groupBy/tag`, `GET /elements/length/groupBy/boundary/groupBy/tag`, `POST /elements/length/groupBy/boundary/groupBy/tag`, `GET /elements/area/groupBy/boundary/groupBy/tag`, `POST /elements/area/groupBy/boundary/groupBy/tag`, `GET /elements/perimeter/groupBy/boundary/groupBy/tag`, or `POST /elements/perimeter/groupBy/boundary/groupBy/tag` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Provide exactly one boundary parameter and exactly one `groupByKey`. `groupByValues` is optional. Boundary IDs and tag identifiers together define each result group.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits the required `groupByKey` parameter for a tag-grouped endpoint.
  - Failure endpoint:
    `GET /elements/count/groupBy/boundary/groupBy/tag`
  - Why this fails:
    The endpoint cannot form tag groups without a tag key.
  - Intentionally violated constraints:
    Omitted `groupByKey`.
- Branch 2:
  - Preconditions:
    - The core request supplies more than one boundary parameter type, such as both `bboxes` and `bpolys` or both `bboxes` and `bcircles`, creating an ambiguous area-of-interest state.
  - Failure endpoint:
    `GET /elements/count/groupBy/boundary/groupBy/tag`
  - Why this fails:
    Boundary grouping requires a single boundary source.
  - Intentionally violated constraints:
    Supplied two boundary parameter types.

Endpoint coverage:
- Covers:
  `GET /elements/count/groupBy/boundary/groupBy/tag`
  `POST /elements/count/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Count elements by boundary and tag value.
- Covers:
  `GET /elements/length/groupBy/boundary/groupBy/tag`
  `POST /elements/length/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Sum element length by boundary and tag value.
- Covers:
  `GET /elements/area/groupBy/boundary/groupBy/tag`
  `POST /elements/area/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Sum element area by boundary and tag value.
- Covers:
  `GET /elements/perimeter/groupBy/boundary/groupBy/tag`
  `POST /elements/perimeter/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Sum element perimeter by boundary and tag value.

### Function 11: group OSM element metric density by boundary and tag

Function name:
group OSM element metric density by boundary and tag

Core endpoint(s):
- `GET /elements/count/density/groupBy/boundary/groupBy/tag`
- `POST /elements/count/density/groupBy/boundary/groupBy/tag`
- `GET /elements/length/density/groupBy/boundary/groupBy/tag`
- `POST /elements/length/density/groupBy/boundary/groupBy/tag`
- `GET /elements/area/density/groupBy/boundary/groupBy/tag`
- `POST /elements/area/density/groupBy/boundary/groupBy/tag`
- `GET /elements/perimeter/density/groupBy/boundary/groupBy/tag`
- `POST /elements/perimeter/density/groupBy/boundary/groupBy/tag`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.
- For tag grouping, the grouping key is supplied as `groupByKey` on the core request, and optional `groupByValues` are supplied on the same request; these values are not established by a setup endpoint.

Successful execution:
- Result:
  This function returns the selected element metric density split by boundary and tag value.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count/density/groupBy/boundary/groupBy/tag`, `POST /elements/count/density/groupBy/boundary/groupBy/tag`, `GET /elements/length/density/groupBy/boundary/groupBy/tag`, `POST /elements/length/density/groupBy/boundary/groupBy/tag`, `GET /elements/area/density/groupBy/boundary/groupBy/tag`, `POST /elements/area/density/groupBy/boundary/groupBy/tag`, `GET /elements/perimeter/density/groupBy/boundary/groupBy/tag`, or `POST /elements/perimeter/density/groupBy/boundary/groupBy/tag` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Provide exactly one boundary parameter and exactly one `groupByKey`. The density for each boundary-tag group is computed using that boundary’s area.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits `groupByKey` or supplies more than one key for an endpoint that requires exactly one grouping key.
  - Failure endpoint:
    `GET /elements/count/density/groupBy/boundary/groupBy/tag`
  - Why this fails:
    The implementation requires exactly one grouping key.
  - Intentionally violated constraints:
    Omitted `groupByKey` or supplied multiple values.
- Branch 2:
  - Preconditions:
    - The core request supplies `bpolys` as GeoJSON that cannot be converted into a valid boundary geometry.
  - Failure endpoint:
    `GET /elements/count/density/groupBy/boundary/groupBy/tag`
  - Why this fails:
    `bpolys` GeoJSON must be a valid FeatureCollection of polygonal features.
  - Intentionally violated constraints:
    Supplied invalid GeoJSON in `bpolys`.

Endpoint coverage:
- Covers:
  `GET /elements/count/density/groupBy/boundary/groupBy/tag`
  `POST /elements/count/density/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Count density by boundary and tag value.
- Covers:
  `GET /elements/length/density/groupBy/boundary/groupBy/tag`
  `POST /elements/length/density/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Length density by boundary and tag value.
- Covers:
  `GET /elements/area/density/groupBy/boundary/groupBy/tag`
  `POST /elements/area/density/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Area density by boundary and tag value.
- Covers:
  `GET /elements/perimeter/density/groupBy/boundary/groupBy/tag`
  `POST /elements/perimeter/density/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Perimeter density by boundary and tag value.

### Function 12: calculate OSM element metric ratio

Function name:
calculate OSM element metric ratio

Core endpoint(s):
- `GET /elements/count/ratio`
- `POST /elements/count/ratio`
- `GET /elements/length/ratio`
- `POST /elements/length/ratio`
- `GET /elements/area/ratio`
- `POST /elements/area/ratio`
- `GET /elements/perimeter/ratio`
- `POST /elements/perimeter/ratio`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.
- For ratio calculations, the denominator selector `filter2` or the `keys2`/`values2`/`types2` parameter set is supplied directly on the core request; there is no prior endpoint that creates a reusable numerator or denominator selection.

Successful execution:
- Result:
  This function returns `value`, `value2`, and `ratio`, where `value` is the selected metric for `filter` and `value2` is the selected metric for `filter2`.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count/ratio`, `POST /elements/count/ratio`, `GET /elements/length/ratio`, `POST /elements/length/ratio`, `GET /elements/area/ratio`, `POST /elements/area/ratio`, `GET /elements/perimeter/ratio`, or `POST /elements/perimeter/ratio` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  For the OpenAPI-supported filter form, both `filter` and `filter2` must be nonblank, valid snapshot-suitable filter expressions. Provide a usable boundary. The implementation also contains a deprecated keys/types ratio mode, but those parameters are not exposed in the Swagger file.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits `filter2` or supplies it as blank for a ratio endpoint that requires a denominator filter.
  - Failure endpoint:
    `GET /elements/count/ratio`
  - Why this fails:
    The implementation explicitly requires `filter2` for ratio endpoints.
  - Intentionally violated constraints:
    Omitted `filter2`.
- Branch 2:
  - Preconditions:
    - The core request targets a snapshot-based element endpoint while supplying a changeset-only filter expression.
  - Failure endpoint:
    `GET /elements/count/ratio`
  - Why this fails:
    Ratio endpoints operate on snapshots, and changeset filters are contribution-only.
  - Intentionally violated constraints:
    Used a contribution-only filter in a snapshot ratio request.

Endpoint coverage:
- Covers:
  `GET /elements/count/ratio`
  `POST /elements/count/ratio`
- Distinct meaning:
  Ratio of counted elements matching `filter2` to counted elements matching `filter`.
- Covers:
  `GET /elements/length/ratio`
  `POST /elements/length/ratio`
- Distinct meaning:
  Ratio of length matching `filter2` to length matching `filter`.
- Covers:
  `GET /elements/area/ratio`
  `POST /elements/area/ratio`
- Distinct meaning:
  Ratio of area matching `filter2` to area matching `filter`.
- Covers:
  `GET /elements/perimeter/ratio`
  `POST /elements/perimeter/ratio`
- Distinct meaning:
  Ratio of perimeter matching `filter2` to perimeter matching `filter`.

### Function 13: calculate OSM element metric ratio grouped by boundary

Function name:
calculate OSM element metric ratio grouped by boundary

Core endpoint(s):
- `GET /elements/count/ratio/groupBy/boundary`
- `POST /elements/count/ratio/groupBy/boundary`
- `GET /elements/length/ratio/groupBy/boundary`
- `POST /elements/length/ratio/groupBy/boundary`
- `GET /elements/area/ratio/groupBy/boundary`
- `POST /elements/area/ratio/groupBy/boundary`
- `GET /elements/perimeter/ratio/groupBy/boundary`
- `POST /elements/perimeter/ratio/groupBy/boundary`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.
- For ratio calculations, the denominator selector `filter2` or the `keys2`/`values2`/`types2` parameter set is supplied directly on the core request; there is no prior endpoint that creates a reusable numerator or denominator selection.

Successful execution:
- Result:
  This function returns the selected metric ratio separately for each requested boundary object.
- Invocation:
  Step 1: Use exactly one endpoint: `GET /elements/count/ratio/groupBy/boundary`, `POST /elements/count/ratio/groupBy/boundary`, `GET /elements/length/ratio/groupBy/boundary`, `POST /elements/length/ratio/groupBy/boundary`, `GET /elements/area/ratio/groupBy/boundary`, `POST /elements/area/ratio/groupBy/boundary`, `GET /elements/perimeter/ratio/groupBy/boundary`, or `POST /elements/perimeter/ratio/groupBy/boundary` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Provide exactly one boundary parameter, a valid nonblank `filter`, and a valid nonblank `filter2`. Each boundary object produces its own `value`, `value2`, and `ratio` series. `format=geojson` is meaningful for this boundary-grouped ratio response.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits `bboxes`, `bcircles`, and `bpolys`, so no boundary state is available for the boundary-grouped calculation.
  - Failure endpoint:
    `GET /elements/count/ratio/groupBy/boundary`
  - Why this fails:
    Boundary grouping cannot be computed without boundary groups.
  - Intentionally violated constraints:
    Omitted `bboxes`, `bcircles`, and `bpolys`.
- Branch 2:
  - Preconditions:
    - The core request omits the required `filter2` parameter for a ratio endpoint.
  - Failure endpoint:
    `GET /elements/count/ratio/groupBy/boundary`
  - Why this fails:
    Ratio computation needs both numerator and denominator filter definitions.
  - Intentionally violated constraints:
    Omitted `filter2`.

Endpoint coverage:
- Covers:
  `GET /elements/count/ratio/groupBy/boundary`
  `POST /elements/count/ratio/groupBy/boundary`
- Distinct meaning:
  Count ratio per boundary.
- Covers:
  `GET /elements/length/ratio/groupBy/boundary`
  `POST /elements/length/ratio/groupBy/boundary`
- Distinct meaning:
  Length ratio per boundary.
- Covers:
  `GET /elements/area/ratio/groupBy/boundary`
  `POST /elements/area/ratio/groupBy/boundary`
- Distinct meaning:
  Area ratio per boundary.
- Covers:
  `GET /elements/perimeter/ratio/groupBy/boundary`
  `POST /elements/perimeter/ratio/groupBy/boundary`
- Distinct meaning:
  Perimeter ratio per boundary.

### Function 14: count OSM contributions

Function name:
count OSM contributions

Core endpoint(s):
- `GET /contributions/count`
- `POST /contributions/count`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns counts of OSM contributions over time intervals.
- Invocation:
  Step 1: `GET /contributions/count` or `POST /contributions/count` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Provide at least one request parameter and a usable boundary for predictable success. If `time` is supplied for a contribution endpoint, it must define an interval or at least two timestamps; a single timestamp is rejected. `contributionType` is optional and may contain `creation`, `deletion`, `tagChange`, `geometryChange`, or a comma-separated combination.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request supplies only one `time` timestamp to a contribution-based endpoint that requires a time range or interval.
  - Failure endpoint:
    `GET /contributions/count`
  - Why this fails:
    Contribution counting requires intervals, not a single snapshot timestamp.
  - Intentionally violated constraints:
    Supplied one timestamp in `time`.
- Branch 2:
  - Preconditions:
    - The core request supplies `contributionType` with an unsupported value outside `creation`, `deletion`, `tagChange`, or `geometryChange`.
  - Failure endpoint:
    `GET /contributions/count`
  - Why this fails:
    The contribution-type filter only recognizes creation, deletion, tag change, and geometry change.
  - Intentionally violated constraints:
    Supplied an invalid `contributionType`.

Endpoint coverage:
- Covers:
  `GET /contributions/count`
  `POST /contributions/count`
- Distinct meaning:
  Count OSM contributions, optionally filtered by contribution type.

### Function 15: calculate OSM contribution density

Function name:
calculate OSM contribution density

Core endpoint(s):
- `GET /contributions/count/density`
- `POST /contributions/count/density`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns OSM contribution counts divided by boundary area in square kilometers.
- Invocation:
  Step 1: `GET /contributions/count/density` or `POST /contributions/count/density` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Contribution interval constraints apply. A usable boundary is required for density calculation. `contributionType` may restrict the counted contribution types.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits the boundary parameter or supplies boundary input that cannot be used to build the density geometry.
  - Failure endpoint:
    `GET /contributions/count/density`
  - Why this fails:
    Density calculation needs a geometry area.
  - Intentionally violated constraints:
    Omitted boundary input.
- Branch 2:
  - Preconditions:
    - The core request supplies `contributionType` with an unsupported value outside `creation`, `deletion`, `tagChange`, or `geometryChange`.
  - Failure endpoint:
    `GET /contributions/count/density`
  - Why this fails:
    The contribution-type filter rejects unrecognized names.
  - Intentionally violated constraints:
    Supplied a contribution type outside the allowed set.

Endpoint coverage:
- Covers:
  `GET /contributions/count/density`
  `POST /contributions/count/density`
- Distinct meaning:
  Count contribution density over time intervals.

### Function 16: count latest OSM contributions

Function name:
count latest OSM contributions

Core endpoint(s):
- `GET /contributions/latest/count`
- `POST /contributions/latest/count`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function groups contributions by OSM entity and counts only the latest contribution per entity in the requested interval.
- Invocation:
  Step 1: `GET /contributions/latest/count` or `POST /contributions/latest/count` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  `time`, if supplied, must define a contribution interval or timestamp list with at least two timestamps. Optional `contributionType` filtering is applied after choosing each entity’s latest contribution.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request supplies only one `time` timestamp to a contribution-based endpoint that requires a time range or interval.
  - Failure endpoint:
    `GET /contributions/latest/count`
  - Why this fails:
    Latest contribution counting still uses contribution-view interval processing.
  - Intentionally violated constraints:
    Supplied a single timestamp.
- Branch 2:
  - Preconditions:
    - The core request supplies a malformed `filter` value that cannot be parsed by the OSHDB filter parser.
  - Failure endpoint:
    `GET /contributions/latest/count`
  - Why this fails:
    The implementation parses `filter` before grouping latest contributions.
  - Intentionally violated constraints:
    Supplied malformed `filter`.

Endpoint coverage:
- Covers:
  `GET /contributions/latest/count`
  `POST /contributions/latest/count`
- Distinct meaning:
  Count only each entity’s latest contribution in the interval.

### Function 17: calculate latest OSM contribution density

Function name:
calculate latest OSM contribution density

Core endpoint(s):
- `GET /contributions/latest/count/density`
- `POST /contributions/latest/count/density`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function counts each entity’s latest contribution and divides the counts by boundary area.
- Invocation:
  Step 1: `GET /contributions/latest/count/density` or `POST /contributions/latest/count/density` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  The same latest-contribution and interval constraints apply as `/contributions/latest/count`. A usable boundary is needed for density.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request supplies boundary input that cannot be parsed into a valid geometry.
  - Failure endpoint:
    `GET /contributions/latest/count/density`
  - Why this fails:
    The implementation cannot compute density without valid geometry.
  - Intentionally violated constraints:
    Supplied malformed boundary input.
- Branch 2:
  - Preconditions:
    - The core request supplies only one `time` timestamp to a contribution-based endpoint that requires a time range or interval.
  - Failure endpoint:
    `GET /contributions/latest/count/density`
  - Why this fails:
    Contribution endpoints require interval-style time input.
  - Intentionally violated constraints:
    Supplied one timestamp.

Endpoint coverage:
- Covers:
  `GET /contributions/latest/count/density`
  `POST /contributions/latest/count/density`
- Distinct meaning:
  Density of latest contributions per entity.

### Function 18: count OSM contributions grouped by boundary

Function name:
count OSM contributions grouped by boundary

Core endpoint(s):
- `GET /contributions/count/groupBy/boundary`
- `POST /contributions/count/groupBy/boundary`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns OSM contribution counts separately for each requested boundary object.
- Invocation:
  Step 1: `GET /contributions/count/groupBy/boundary` or `POST /contributions/count/groupBy/boundary` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Exactly one boundary parameter must be supplied, and it may contain multiple boundary objects. Contribution interval and optional `contributionType` constraints apply. `format=geojson`, `json`, or `csv` may be used.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits `bboxes`, `bcircles`, and `bpolys`, so no boundary state is available for the boundary-grouped calculation.
  - Failure endpoint:
    `GET /contributions/count/groupBy/boundary`
  - Why this fails:
    Boundary grouping needs explicit boundary groups.
  - Intentionally violated constraints:
    Omitted `bboxes`, `bcircles`, and `bpolys`.
- Branch 2:
  - Preconditions:
    - The core request supplies `contributionType` with an unsupported value outside `creation`, `deletion`, `tagChange`, or `geometryChange`.
  - Failure endpoint:
    `GET /contributions/count/groupBy/boundary`
  - Why this fails:
    The contribution filter rejects unsupported type names.
  - Intentionally violated constraints:
    Supplied unsupported `contributionType`.

Endpoint coverage:
- Covers:
  `GET /contributions/count/groupBy/boundary`
  `POST /contributions/count/groupBy/boundary`
- Distinct meaning:
  Count contributions per boundary.

### Function 19: calculate OSM contribution density grouped by boundary

Function name:
calculate OSM contribution density grouped by boundary

Core endpoint(s):
- `GET /contributions/count/density/groupBy/boundary`
- `POST /contributions/count/density/groupBy/boundary`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns OSM contribution density separately for each requested boundary object.
- Invocation:
  Step 1: `GET /contributions/count/density/groupBy/boundary` or `POST /contributions/count/density/groupBy/boundary` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Exactly one boundary parameter must be supplied. Each boundary group’s contribution count is divided by that boundary’s area. Contribution interval and optional `contributionType` constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request supplies more than one boundary parameter type, such as both `bboxes` and `bpolys` or both `bboxes` and `bcircles`, creating an ambiguous area-of-interest state.
  - Failure endpoint:
    `GET /contributions/count/density/groupBy/boundary`
  - Why this fails:
    Input processing permits only one boundary source per request.
  - Intentionally violated constraints:
    Supplied both `bboxes` and `bpolys`.
- Branch 2:
  - Preconditions:
    - The core request supplies only one `time` timestamp to a contribution-based endpoint that requires a time range or interval.
  - Failure endpoint:
    `GET /contributions/count/density/groupBy/boundary`
  - Why this fails:
    Contribution endpoints require an interval or at least two timestamps.
  - Intentionally violated constraints:
    Supplied one timestamp.

Endpoint coverage:
- Covers:
  `GET /contributions/count/density/groupBy/boundary`
  `POST /contributions/count/density/groupBy/boundary`
- Distinct meaning:
  Contribution count density per boundary.

### Function 20: count OSM users

Function name:
count OSM users

Core endpoint(s):
- `GET /users/count`
- `POST /users/count`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns the number of distinct OSM contributor user IDs over contribution intervals.
- Invocation:
  Step 1: `GET /users/count` or `POST /users/count` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  The endpoint uses contribution-view processing, so explicit `time` must be an interval or at least two timestamps. Optional `contributionType` filters which contributions are considered before counting unique users.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request supplies only one `time` timestamp to a contribution-based endpoint that requires a time range or interval.
  - Failure endpoint:
    `GET /users/count`
  - Why this fails:
    User counting is based on contributions and requires interval processing.
  - Intentionally violated constraints:
    Supplied single-timestamp `time`.
- Branch 2:
  - Preconditions:
    - The core request supplies `contributionType` with an unsupported value outside `creation`, `deletion`, `tagChange`, or `geometryChange`.
  - Failure endpoint:
    `GET /users/count`
  - Why this fails:
    User endpoints reuse the contribution-type filter.
  - Intentionally violated constraints:
    Supplied unsupported `contributionType`.

Endpoint coverage:
- Covers:
  `GET /users/count`
  `POST /users/count`
- Distinct meaning:
  Count unique contributing users.

### Function 21: calculate OSM user density

Function name:
calculate OSM user density

Core endpoint(s):
- `GET /users/count/density`
- `POST /users/count/density`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns unique contributing user counts divided by boundary area.
- Invocation:
  Step 1: `GET /users/count/density` or `POST /users/count/density` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Contribution interval constraints apply. A usable boundary is required for density. `contributionType` optionally filters the contribution events used to derive users.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits the boundary parameter or supplies malformed boundary coordinates.
  - Failure endpoint:
    `GET /users/count/density`
  - Why this fails:
    Density requires valid spatial geometry.
  - Intentionally violated constraints:
    Omitted or malformed boundary input.
- Branch 2:
  - Preconditions:
    - The core request supplies only one `time` timestamp to a contribution-based endpoint that requires a time range or interval.
  - Failure endpoint:
    `GET /users/count/density`
  - Why this fails:
    User density is contribution-based and requires an interval.
  - Intentionally violated constraints:
    Supplied a single timestamp.

Endpoint coverage:
- Covers:
  `GET /users/count/density`
  `POST /users/count/density`
- Distinct meaning:
  Unique contributing users per square kilometer.

### Function 22: group OSM users by type

Function name:
group OSM users by type

Core endpoint(s):
- `GET /users/count/groupBy/type`
- `POST /users/count/groupBy/type`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns unique contributing user counts grouped by the OSM type of the contributed entity.
- Invocation:
  Step 1: `GET /users/count/groupBy/type` or `POST /users/count/groupBy/type` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Contribution interval and boundary constraints apply. `contributionType` may restrict which contributions are considered before grouping and counting users.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request supplies `contributionType` with an unsupported value outside `creation`, `deletion`, `tagChange`, or `geometryChange`.
  - Failure endpoint:
    `GET /users/count/groupBy/type`
  - Why this fails:
    The shared contribution-type validator rejects unsupported values.
  - Intentionally violated constraints:
    Supplied unsupported `contributionType`.
- Branch 2:
  - Preconditions:
    - The core request includes a parameter name that is not allowed for this resource-specific endpoint.
  - Failure endpoint:
    `GET /users/count/groupBy/type`
  - Why this fails:
    Resource-specific parameter validation rejects unknown parameters.
  - Intentionally violated constraints:
    Supplied a parameter not allowed for this endpoint.

Endpoint coverage:
- Covers:
  `GET /users/count/groupBy/type`
  `POST /users/count/groupBy/type`
- Distinct meaning:
  Count unique users grouped by contributed entity type.

### Function 23: group OSM user density by type

Function name:
group OSM user density by type

Core endpoint(s):
- `GET /users/count/density/groupBy/type`
- `POST /users/count/density/groupBy/type`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns unique contributing user density grouped by contributed entity type.
- Invocation:
  Step 1: `GET /users/count/density/groupBy/type` or `POST /users/count/density/groupBy/type` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  A usable boundary is required. Contribution interval and optional `contributionType` constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits `bboxes`, `bcircles`, and `bpolys`, so no boundary state is available for the density calculation.
  - Failure endpoint:
    `GET /users/count/density/groupBy/type`
  - Why this fails:
    Density grouping needs a boundary area.
  - Intentionally violated constraints:
    Omitted boundary input.
- Branch 2:
  - Preconditions:
    - The core request supplies only one `time` timestamp to a contribution-based endpoint that requires a time range or interval.
  - Failure endpoint:
    `GET /users/count/density/groupBy/type`
  - Why this fails:
    User density grouping is contribution-based and requires interval time semantics.
  - Intentionally violated constraints:
    Supplied a single timestamp.

Endpoint coverage:
- Covers:
  `GET /users/count/density/groupBy/type`
  `POST /users/count/density/groupBy/type`
- Distinct meaning:
  Unique user density grouped by contributed entity type.

### Function 24: group OSM users by tag

Function name:
group OSM users by tag

Core endpoint(s):
- `GET /users/count/groupBy/tag`
- `POST /users/count/groupBy/tag`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.
- For tag grouping, the grouping key is supplied as `groupByKey` on the core request, and optional `groupByValues` are supplied on the same request; these values are not established by a setup endpoint.

Successful execution:
- Result:
  This function returns unique contributing user counts grouped by values of one requested OSM tag key.
- Invocation:
  Step 1: `GET /users/count/groupBy/tag` or `POST /users/count/groupBy/tag` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  `groupByKey` must contain exactly one key. `groupByValues` is optional. Contribution interval, boundary, and optional `contributionType` constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits `groupByKey` or supplies more than one key for an endpoint that requires exactly one grouping key.
  - Failure endpoint:
    `GET /users/count/groupBy/tag`
  - Why this fails:
    The user tag-grouping executor requires exactly one grouping key.
  - Intentionally violated constraints:
    Omitted `groupByKey` or supplied multiple keys.
- Branch 2:
  - Preconditions:
    - The core request supplies `contributionType` with an unsupported value outside `creation`, `deletion`, `tagChange`, or `geometryChange`.
  - Failure endpoint:
    `GET /users/count/groupBy/tag`
  - Why this fails:
    The contribution-type filter rejects unsupported values.
  - Intentionally violated constraints:
    Supplied invalid `contributionType`.

Endpoint coverage:
- Covers:
  `GET /users/count/groupBy/tag`
  `POST /users/count/groupBy/tag`
- Distinct meaning:
  Count unique users by tag value.

### Function 25: group OSM user density by tag

Function name:
group OSM user density by tag

Core endpoint(s):
- `GET /users/count/density/groupBy/tag`
- `POST /users/count/density/groupBy/tag`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.
- For tag grouping, the grouping key is supplied as `groupByKey` on the core request, and optional `groupByValues` are supplied on the same request; these values are not established by a setup endpoint.

Successful execution:
- Result:
  This function returns unique contributing user density grouped by values of one requested OSM tag key.
- Invocation:
  Step 1: `GET /users/count/density/groupBy/tag` or `POST /users/count/density/groupBy/tag` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  `groupByKey` must contain exactly one key. `groupByValues` is optional. A usable boundary is required for density. Contribution interval constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits the required `groupByKey` parameter for a tag-grouped endpoint.
  - Failure endpoint:
    `GET /users/count/density/groupBy/tag`
  - Why this fails:
    Tag grouping cannot run without the grouping key.
  - Intentionally violated constraints:
    Omitted `groupByKey`.
- Branch 2:
  - Preconditions:
    - The core request supplies malformed boundary coordinates in `bboxes`, `bcircles`, or `bpolys`, so the boundary geometry cannot be built.
  - Failure endpoint:
    `GET /users/count/density/groupBy/tag`
  - Why this fails:
    The implementation cannot compute density without valid boundary geometry.
  - Intentionally violated constraints:
    Supplied invalid boundary input.

Endpoint coverage:
- Covers:
  `GET /users/count/density/groupBy/tag`
  `POST /users/count/density/groupBy/tag`
- Distinct meaning:
  Unique user density by tag value.

### Function 26: group OSM users by key

Function name:
group OSM users by key

Core endpoint(s):
- `GET /users/count/groupBy/key`
- `POST /users/count/groupBy/key`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.
- For key grouping, the grouping keys are supplied as `groupByKeys` on the core request; these values are not established by a setup endpoint.

Successful execution:
- Result:
  This function returns unique contributing user counts grouped by one or more requested OSM tag keys.
- Invocation:
  Step 1: `GET /users/count/groupBy/key` or `POST /users/count/groupBy/key` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  `groupByKeys` must contain at least one key. Contribution interval, boundary, and optional `contributionType` constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits the required `groupByKeys` parameter for a key-grouped endpoint.
  - Failure endpoint:
    `GET /users/count/groupBy/key`
  - Why this fails:
    The implementation requires `groupByKeys` for key grouping.
  - Intentionally violated constraints:
    Omitted `groupByKeys`.
- Branch 2:
  - Preconditions:
    - The core request supplies only one `time` timestamp to a contribution-based endpoint that requires a time range or interval.
  - Failure endpoint:
    `GET /users/count/groupBy/key`
  - Why this fails:
    User aggregation is contribution-based and requires interval time semantics.
  - Intentionally violated constraints:
    Supplied a single timestamp.

Endpoint coverage:
- Covers:
  `GET /users/count/groupBy/key`
  `POST /users/count/groupBy/key`
- Distinct meaning:
  Count unique users by tag key.

### Function 27: group OSM users by boundary

Function name:
group OSM users by boundary

Core endpoint(s):
- `GET /users/count/groupBy/boundary`
- `POST /users/count/groupBy/boundary`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns unique contributing user counts separately for each requested boundary object.
- Invocation:
  Step 1: `GET /users/count/groupBy/boundary` or `POST /users/count/groupBy/boundary` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Exactly one boundary parameter must be supplied, and it may contain multiple boundary objects. Contribution interval and optional `contributionType` constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request omits `bboxes`, `bcircles`, and `bpolys`, so no boundary state is available for the boundary-grouped calculation.
  - Failure endpoint:
    `GET /users/count/groupBy/boundary`
  - Why this fails:
    Boundary grouping needs explicit boundary groups.
  - Intentionally violated constraints:
    Omitted all boundary parameters.
- Branch 2:
  - Preconditions:
    - The core request supplies `contributionType` with an unsupported value outside `creation`, `deletion`, `tagChange`, or `geometryChange`.
  - Failure endpoint:
    `GET /users/count/groupBy/boundary`
  - Why this fails:
    The contribution-type filter rejects unsupported values.
  - Intentionally violated constraints:
    Supplied invalid `contributionType`.

Endpoint coverage:
- Covers:
  `GET /users/count/groupBy/boundary`
  `POST /users/count/groupBy/boundary`
- Distinct meaning:
  Count unique users per boundary.

### Function 28: group OSM user density by boundary

Function name:
group OSM user density by boundary

Core endpoint(s):
- `GET /users/count/density/groupBy/boundary`
- `POST /users/count/density/groupBy/boundary`

Preconditions:
- The OSHDB backend, keytables/tag translator, and extract metadata are initialized before the core request is handled. This can be satisfied by loading the required OSM data extract directly through database/application configuration; the API does not provide a setup endpoint for this state.
- The spatial area of interest is supplied directly on the core request in exactly one of `bboxes`, `bcircles`, or `bpolys`, or the service has an extract polygon configured that can act as the area of interest. No prior endpoint creates or returns this boundary state.
- Any `time`, `filter`, `format`, `showMetadata`, `timeout`, tag-grouping, contribution-type, or ratio parameters required by this function are request values on the core endpoint. No generated id, cursor, ETag, token, `Location`, or response value is reused from another endpoint.

Successful execution:
- Result:
  This function returns unique contributing user density separately for each requested boundary object.
- Invocation:
  Step 1: `GET /users/count/density/groupBy/boundary` or `POST /users/count/density/groupBy/boundary` with required path, query, body, form, and header values supplied on that same request.
- Constraints:
  Exactly one boundary parameter must be supplied. Each boundary group’s unique user count is divided by that boundary’s area. Contribution interval and optional `contributionType` constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The core request supplies more than one boundary parameter type, such as both `bboxes` and `bpolys` or both `bboxes` and `bcircles`, creating an ambiguous area-of-interest state.
  - Failure endpoint:
    `GET /users/count/density/groupBy/boundary`
  - Why this fails:
    Input processing accepts only one boundary parameter type per request.
  - Intentionally violated constraints:
    Supplied both `bboxes` and `bcircles`.
- Branch 2:
  - Preconditions:
    - The core request supplies only one `time` timestamp to a contribution-based endpoint that requires a time range or interval.
  - Failure endpoint:
    `GET /users/count/density/groupBy/boundary`
  - Why this fails:
    User density grouping uses contribution intervals.
  - Intentionally violated constraints:
    Supplied one timestamp.

Endpoint coverage:
- Covers:
  `GET /users/count/density/groupBy/boundary`
  `POST /users/count/density/groupBy/boundary`
- Distinct meaning:
  Unique contributing user density per boundary.
