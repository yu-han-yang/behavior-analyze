Analyzed only `ohsome-api.json` and `src`. I did not execute the API. One discrepancy: `src` contains additional extraction/metadata mappings such as `/metadata`, `/elements/geometry`, `/elementsFullHistory/...`, and `/contributions/...`, but they are absent from `ohsome-api.json`, so I do not count them as Swagger-supported behaviors below.

### Behavior 1: calculate OSM element metric

Behavior name:
calculate OSM element metric

Successful execution:
- Result:
  This behavior returns a time series for one selected OSM element metric: count, length, area, or perimeter.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count`, `POST /elements/count`, `GET /elements/length`, `POST /elements/length`, `GET /elements/area`, `POST /elements/area`, `GET /elements/perimeter`, or `POST /elements/perimeter`.
- Constraints:
  Provide at least one request parameter. For predictable success, provide exactly one spatial boundary parameter: `bboxes`, `bcircles`, or `bpolys`. `time`, when supplied, must be ISO-8601. `format` may be `json` or `csv`. `showMetadata` must be boolean-like if supplied. The selected path determines the metric; no response value is reused by another endpoint.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No request parameter is supplied.
  - Endpoint group:
    Step 1: `GET /elements/count`
  - Failure endpoint:
    `GET /elements/count`
  - Why this fails:
    The implementation rejects requests whose parameter map is empty.
  - Intentionally violated constraints:
    Omitted all request parameters, including boundary input.
- Branch 2:
  - Unsatisfied condition:
    More than one boundary parameter is supplied.
  - Endpoint group:
    Step 1: `GET /elements/count`
  - Failure endpoint:
    `GET /elements/count`
  - Why this fails:
    The implementation accepts only one of `bboxes`, `bcircles`, or `bpolys`.
  - Intentionally violated constraints:
    Supplied both `bboxes` and `bpolys`.

Endpoint coverage:
- Covers:
  `GET /elements/count`, `POST /elements/count`
- Distinct meaning:
  Count matching OSM elements.
- Covers:
  `GET /elements/length`, `POST /elements/length`
- Distinct meaning:
  Sum matching element geometry length.
- Covers:
  `GET /elements/area`, `POST /elements/area`
- Distinct meaning:
  Sum matching element polygon area.
- Covers:
  `GET /elements/perimeter`, `POST /elements/perimeter`
- Distinct meaning:
  Sum matching polygon perimeter.

### Behavior 2: calculate OSM element metric density

Behavior name:
calculate OSM element metric density

Successful execution:
- Result:
  This behavior returns the selected element metric divided by the requested boundary area in square kilometers.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count/density`, `POST /elements/count/density`, `GET /elements/length/density`, `POST /elements/length/density`, `GET /elements/area/density`, `POST /elements/area/density`, `GET /elements/perimeter/density`, or `POST /elements/perimeter/density`.
- Constraints:
  Provide at least one request parameter and exactly one spatial boundary parameter for predictable success. The implementation computes density from the selected metric value and the spatial boundary geometry area. `time`, `format`, `showMetadata`, and `timeout` follow the same validation as non-density element metrics.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The boundary parameter is absent and no data-extract polygon can be used.
  - Endpoint group:
    Step 1: `GET /elements/count/density`
  - Failure endpoint:
    `GET /elements/count/density`
  - Why this fails:
    Density requires a geometry area; the implementation cannot establish one without a usable boundary.
  - Intentionally violated constraints:
    Omitted `bboxes`, `bcircles`, and `bpolys`.
- Branch 2:
  - Unsatisfied condition:
    `timeout` is not numeric or exceeds the configured maximum.
  - Endpoint group:
    Step 1: `GET /elements/count/density`
  - Failure endpoint:
    `GET /elements/count/density`
  - Why this fails:
    The implementation parses `timeout` as seconds and rejects invalid or too-large values.
  - Intentionally violated constraints:
    Supplied an invalid `timeout`.

Endpoint coverage:
- Covers:
  `GET /elements/count/density`, `POST /elements/count/density`
- Distinct meaning:
  Count elements per square kilometer.
- Covers:
  `GET /elements/length/density`, `POST /elements/length/density`
- Distinct meaning:
  Element length per square kilometer.
- Covers:
  `GET /elements/area/density`, `POST /elements/area/density`
- Distinct meaning:
  Element area per square kilometer.
- Covers:
  `GET /elements/perimeter/density`, `POST /elements/perimeter/density`
- Distinct meaning:
  Element perimeter per square kilometer.

### Behavior 3: group OSM element metric by type

Behavior name:
group OSM element metric by type

Successful execution:
- Result:
  This behavior returns the selected element metric split by OSM type, such as node, way, or relation.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count/groupBy/type`, `POST /elements/count/groupBy/type`, `GET /elements/length/groupBy/type`, `POST /elements/length/groupBy/type`, `GET /elements/area/groupBy/type`, `POST /elements/area/groupBy/type`, `GET /elements/perimeter/groupBy/type`, or `POST /elements/perimeter/groupBy/type`.
- Constraints:
  Provide at least one request parameter and exactly one spatial boundary parameter. `filter`, if supplied, must parse successfully and must not use changeset-only filters on these snapshot-based element endpoints.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `filter` has invalid syntax.
  - Endpoint group:
    Step 1: `GET /elements/count/groupBy/type`
  - Failure endpoint:
    `GET /elements/count/groupBy/type`
  - Why this fails:
    The implementation parses `filter` with the OSHDB filter parser and rejects parser errors.
  - Intentionally violated constraints:
    Supplied a malformed `filter`.
- Branch 2:
  - Unsatisfied condition:
    A changeset filter is used on a snapshot-based endpoint.
  - Endpoint group:
    Step 1: `GET /elements/count/groupBy/type`
  - Failure endpoint:
    `GET /elements/count/groupBy/type`
  - Why this fails:
    Changeset filters are accepted only on contribution-based endpoints.
  - Intentionally violated constraints:
    Used a contribution-only filter on an element snapshot query.

Endpoint coverage:
- Covers:
  `GET /elements/count/groupBy/type`, `POST /elements/count/groupBy/type`
- Distinct meaning:
  Count elements grouped by OSM type.
- Covers:
  `GET /elements/length/groupBy/type`, `POST /elements/length/groupBy/type`
- Distinct meaning:
  Sum element length grouped by OSM type.
- Covers:
  `GET /elements/area/groupBy/type`, `POST /elements/area/groupBy/type`
- Distinct meaning:
  Sum element area grouped by OSM type.
- Covers:
  `GET /elements/perimeter/groupBy/type`, `POST /elements/perimeter/groupBy/type`
- Distinct meaning:
  Sum element perimeter grouped by OSM type.

### Behavior 4: group OSM element metric density by type

Behavior name:
group OSM element metric density by type

Successful execution:
- Result:
  This behavior returns the selected element metric density split by OSM type.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count/density/groupBy/type`, `POST /elements/count/density/groupBy/type`, `GET /elements/length/density/groupBy/type`, `POST /elements/length/density/groupBy/type`, `GET /elements/area/density/groupBy/type`, `POST /elements/area/density/groupBy/type`, `GET /elements/perimeter/density/groupBy/type`, or `POST /elements/perimeter/density/groupBy/type`.
- Constraints:
  The selected endpoint uses snapshot data, groups by OSM type, and divides each metric value by boundary area. Exactly one usable boundary parameter is needed for predictable density calculation.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The request has no usable boundary.
  - Endpoint group:
    Step 1: `GET /elements/count/density/groupBy/type`
  - Failure endpoint:
    `GET /elements/count/density/groupBy/type`
  - Why this fails:
    Density and input processing need a spatial area of interest.
  - Intentionally violated constraints:
    Omitted the boundary parameters.
- Branch 2:
  - Unsatisfied condition:
    `showMetadata` is not boolean-like.
  - Endpoint group:
    Step 1: `GET /elements/count/density/groupBy/type`
  - Failure endpoint:
    `GET /elements/count/density/groupBy/type`
  - Why this fails:
    The implementation accepts only `true`, `yes`, `false`, `no`, or blank for `showMetadata`.
  - Intentionally violated constraints:
    Supplied an unsupported `showMetadata` value.

Endpoint coverage:
- Covers:
  `GET /elements/count/density/groupBy/type`, `POST /elements/count/density/groupBy/type`
- Distinct meaning:
  Count density grouped by OSM type.
- Covers:
  `GET /elements/length/density/groupBy/type`, `POST /elements/length/density/groupBy/type`
- Distinct meaning:
  Length density grouped by OSM type.
- Covers:
  `GET /elements/area/density/groupBy/type`, `POST /elements/area/density/groupBy/type`
- Distinct meaning:
  Area density grouped by OSM type.
- Covers:
  `GET /elements/perimeter/density/groupBy/type`, `POST /elements/perimeter/density/groupBy/type`
- Distinct meaning:
  Perimeter density grouped by OSM type.

### Behavior 5: group OSM element metric by boundary

Behavior name:
group OSM element metric by boundary

Successful execution:
- Result:
  This behavior returns the selected element metric separately for each requested boundary object.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count/groupBy/boundary`, `POST /elements/count/groupBy/boundary`, `GET /elements/length/groupBy/boundary`, `POST /elements/length/groupBy/boundary`, `GET /elements/area/groupBy/boundary`, `POST /elements/area/groupBy/boundary`, `GET /elements/perimeter/groupBy/boundary`, or `POST /elements/perimeter/groupBy/boundary`.
- Constraints:
  Exactly one boundary parameter must be supplied, and it may contain multiple boundary objects. Boundary IDs are taken from supplied IDs or generated automatically. `format=geojson` is meaningful here and returns a GeoJSON feature collection; `csv` and `json` are also supported.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    A boundary object has malformed coordinates.
  - Endpoint group:
    Step 1: `GET /elements/count/groupBy/boundary`
  - Failure endpoint:
    `GET /elements/count/groupBy/boundary`
  - Why this fails:
    Boundary parsing rejects wrong coordinate counts or non-numeric coordinate values.
  - Intentionally violated constraints:
    Supplied an invalid `bboxes`, `bcircles`, or `bpolys` value.
- Branch 2:
  - Unsatisfied condition:
    Boundary lies outside the underlying data extract.
  - Endpoint group:
    Step 1: `GET /elements/count/groupBy/boundary`
  - Failure endpoint:
    `GET /elements/count/groupBy/boundary`
  - Why this fails:
    Geometry validation checks that the requested boundary is within the data polygon when one is configured.
  - Intentionally violated constraints:
    Supplied a boundary outside the available dataset.

Endpoint coverage:
- Covers:
  `GET /elements/count/groupBy/boundary`, `POST /elements/count/groupBy/boundary`
- Distinct meaning:
  Count elements per boundary.
- Covers:
  `GET /elements/length/groupBy/boundary`, `POST /elements/length/groupBy/boundary`
- Distinct meaning:
  Sum element length per boundary.
- Covers:
  `GET /elements/area/groupBy/boundary`, `POST /elements/area/groupBy/boundary`
- Distinct meaning:
  Sum element area per boundary.
- Covers:
  `GET /elements/perimeter/groupBy/boundary`, `POST /elements/perimeter/groupBy/boundary`
- Distinct meaning:
  Sum element perimeter per boundary.

### Behavior 6: group OSM element metric density by boundary

Behavior name:
group OSM element metric density by boundary

Successful execution:
- Result:
  This behavior returns the selected element metric density separately for each requested boundary object.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count/density/groupBy/boundary`, `POST /elements/count/density/groupBy/boundary`, `GET /elements/length/density/groupBy/boundary`, `POST /elements/length/density/groupBy/boundary`, `GET /elements/area/density/groupBy/boundary`, `POST /elements/area/density/groupBy/boundary`, `GET /elements/perimeter/density/groupBy/boundary`, or `POST /elements/perimeter/density/groupBy/boundary`.
- Constraints:
  Exactly one boundary parameter must be supplied. Each group’s metric is divided by that boundary group’s area. `format=geojson`, `json`, and `csv` are supported for these boundary-grouped responses.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Multiple boundary parameter types are supplied.
  - Endpoint group:
    Step 1: `GET /elements/count/density/groupBy/boundary`
  - Failure endpoint:
    `GET /elements/count/density/groupBy/boundary`
  - Why this fails:
    The implementation accepts one boundary parameter type per request.
  - Intentionally violated constraints:
    Supplied both `bboxes` and `bcircles`.
- Branch 2:
  - Unsatisfied condition:
    `format` is not one of the accepted values.
  - Endpoint group:
    Step 1: `GET /elements/count/density/groupBy/boundary`
  - Failure endpoint:
    `GET /elements/count/density/groupBy/boundary`
  - Why this fails:
    Format validation accepts only `json`, `csv`, or `geojson`.
  - Intentionally violated constraints:
    Supplied an unsupported `format`.

Endpoint coverage:
- Covers:
  `GET /elements/count/density/groupBy/boundary`, `POST /elements/count/density/groupBy/boundary`
- Distinct meaning:
  Count density per boundary.
- Covers:
  `GET /elements/length/density/groupBy/boundary`, `POST /elements/length/density/groupBy/boundary`
- Distinct meaning:
  Length density per boundary.
- Covers:
  `GET /elements/area/density/groupBy/boundary`, `POST /elements/area/density/groupBy/boundary`
- Distinct meaning:
  Area density per boundary.
- Covers:
  `GET /elements/perimeter/density/groupBy/boundary`, `POST /elements/perimeter/density/groupBy/boundary`
- Distinct meaning:
  Perimeter density per boundary.

### Behavior 7: group OSM element metric by tag

Behavior name:
group OSM element metric by tag

Successful execution:
- Result:
  This behavior returns the selected element metric grouped by values of one requested OSM tag key.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count/groupBy/tag`, `POST /elements/count/groupBy/tag`, `GET /elements/length/groupBy/tag`, `POST /elements/length/groupBy/tag`, `GET /elements/area/groupBy/tag`, `POST /elements/area/groupBy/tag`, `GET /elements/perimeter/groupBy/tag`, or `POST /elements/perimeter/groupBy/tag`.
- Constraints:
  `groupByKey` must contain exactly one key. `groupByValues` is optional and limits the returned values; unmatched elements go into a remainder group. Provide a usable boundary and valid time/filter parameters.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `groupByKey` is missing or contains more than one key.
  - Endpoint group:
    Step 1: `GET /elements/count/groupBy/tag`
  - Failure endpoint:
    `GET /elements/count/groupBy/tag`
  - Why this fails:
    The implementation requires exactly one `groupByKey` for `/groupBy/tag`.
  - Intentionally violated constraints:
    Omitted `groupByKey` or supplied a comma-separated multi-key value.
- Branch 2:
  - Unsatisfied condition:
    The request repeats a parameter.
  - Endpoint group:
    Step 1: `GET /elements/count/groupBy/tag`
  - Failure endpoint:
    `GET /elements/count/groupBy/tag`
  - Why this fails:
    The implementation rejects any parameter with more than one submitted value.
  - Intentionally violated constraints:
    Repeated a query/form parameter instead of using one value.

Endpoint coverage:
- Covers:
  `GET /elements/count/groupBy/tag`, `POST /elements/count/groupBy/tag`
- Distinct meaning:
  Count elements by tag value.
- Covers:
  `GET /elements/length/groupBy/tag`, `POST /elements/length/groupBy/tag`
- Distinct meaning:
  Sum element length by tag value.
- Covers:
  `GET /elements/area/groupBy/tag`, `POST /elements/area/groupBy/tag`
- Distinct meaning:
  Sum element area by tag value.
- Covers:
  `GET /elements/perimeter/groupBy/tag`, `POST /elements/perimeter/groupBy/tag`
- Distinct meaning:
  Sum element perimeter by tag value.

### Behavior 8: group OSM element metric density by tag

Behavior name:
group OSM element metric density by tag

Successful execution:
- Result:
  This behavior returns the selected element metric density grouped by values of one requested OSM tag key.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count/density/groupBy/tag`, `POST /elements/count/density/groupBy/tag`, `GET /elements/length/density/groupBy/tag`, `POST /elements/length/density/groupBy/tag`, `GET /elements/area/density/groupBy/tag`, `POST /elements/area/density/groupBy/tag`, `GET /elements/perimeter/density/groupBy/tag`, or `POST /elements/perimeter/density/groupBy/tag`.
- Constraints:
  `groupByKey` must contain exactly one key. `groupByValues` is optional. A usable boundary is required because the grouped values are density values.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `groupByKey` is missing.
  - Endpoint group:
    Step 1: `GET /elements/count/density/groupBy/tag`
  - Failure endpoint:
    `GET /elements/count/density/groupBy/tag`
  - Why this fails:
    Tag grouping cannot be computed without the tag key to group by.
  - Intentionally violated constraints:
    Omitted `groupByKey`.
- Branch 2:
  - Unsatisfied condition:
    Boundary input is malformed.
  - Endpoint group:
    Step 1: `GET /elements/count/density/groupBy/tag`
  - Failure endpoint:
    `GET /elements/count/density/groupBy/tag`
  - Why this fails:
    Input processing cannot build a geometry for density calculation.
  - Intentionally violated constraints:
    Supplied invalid boundary coordinates.

Endpoint coverage:
- Covers:
  `GET /elements/count/density/groupBy/tag`, `POST /elements/count/density/groupBy/tag`
- Distinct meaning:
  Count density by tag value.
- Covers:
  `GET /elements/length/density/groupBy/tag`, `POST /elements/length/density/groupBy/tag`
- Distinct meaning:
  Length density by tag value.
- Covers:
  `GET /elements/area/density/groupBy/tag`, `POST /elements/area/density/groupBy/tag`
- Distinct meaning:
  Area density by tag value.
- Covers:
  `GET /elements/perimeter/density/groupBy/tag`, `POST /elements/perimeter/density/groupBy/tag`
- Distinct meaning:
  Perimeter density by tag value.

### Behavior 9: group OSM element metric by key

Behavior name:
group OSM element metric by key

Successful execution:
- Result:
  This behavior returns the selected element metric grouped by one or more requested OSM tag keys.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count/groupBy/key`, `POST /elements/count/groupBy/key`, `GET /elements/length/groupBy/key`, `POST /elements/length/groupBy/key`, `GET /elements/area/groupBy/key`, `POST /elements/area/groupBy/key`, `GET /elements/perimeter/groupBy/key`, or `POST /elements/perimeter/groupBy/key`.
- Constraints:
  `groupByKeys` must contain at least one key. Multiple keys may be comma-separated in the single parameter value. Elements without the requested keys are placed in a remainder group.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `groupByKeys` is missing.
  - Endpoint group:
    Step 1: `GET /elements/count/groupBy/key`
  - Failure endpoint:
    `GET /elements/count/groupBy/key`
  - Why this fails:
    The implementation requires `groupByKeys` for `/groupBy/key`.
  - Intentionally violated constraints:
    Omitted `groupByKeys`.
- Branch 2:
  - Unsatisfied condition:
    Request includes an unexpected parameter name.
  - Endpoint group:
    Step 1: `GET /elements/count/groupBy/key`
  - Failure endpoint:
    `GET /elements/count/groupBy/key`
  - Why this fails:
    The implementation checks resource-specific allowed parameters and rejects unknown names.
  - Intentionally violated constraints:
    Supplied a parameter not allowed for this resource.

Endpoint coverage:
- Covers:
  `GET /elements/count/groupBy/key`, `POST /elements/count/groupBy/key`
- Distinct meaning:
  Count elements by tag key.
- Covers:
  `GET /elements/length/groupBy/key`, `POST /elements/length/groupBy/key`
- Distinct meaning:
  Sum element length by tag key.
- Covers:
  `GET /elements/area/groupBy/key`, `POST /elements/area/groupBy/key`
- Distinct meaning:
  Sum element area by tag key.
- Covers:
  `GET /elements/perimeter/groupBy/key`, `POST /elements/perimeter/groupBy/key`
- Distinct meaning:
  Sum element perimeter by tag key.

### Behavior 10: group OSM element metric by boundary and tag

Behavior name:
group OSM element metric by boundary and tag

Successful execution:
- Result:
  This behavior returns the selected element metric split first by boundary and then by values of one requested tag key.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count/groupBy/boundary/groupBy/tag`, `POST /elements/count/groupBy/boundary/groupBy/tag`, `GET /elements/length/groupBy/boundary/groupBy/tag`, `POST /elements/length/groupBy/boundary/groupBy/tag`, `GET /elements/area/groupBy/boundary/groupBy/tag`, `POST /elements/area/groupBy/boundary/groupBy/tag`, `GET /elements/perimeter/groupBy/boundary/groupBy/tag`, or `POST /elements/perimeter/groupBy/boundary/groupBy/tag`.
- Constraints:
  Provide exactly one boundary parameter and exactly one `groupByKey`. `groupByValues` is optional. Boundary IDs and tag identifiers together define each result group.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `groupByKey` is missing.
  - Endpoint group:
    Step 1: `GET /elements/count/groupBy/boundary/groupBy/tag`
  - Failure endpoint:
    `GET /elements/count/groupBy/boundary/groupBy/tag`
  - Why this fails:
    The endpoint cannot form tag groups without a tag key.
  - Intentionally violated constraints:
    Omitted `groupByKey`.
- Branch 2:
  - Unsatisfied condition:
    More than one boundary parameter type is supplied.
  - Endpoint group:
    Step 1: `GET /elements/count/groupBy/boundary/groupBy/tag`
  - Failure endpoint:
    `GET /elements/count/groupBy/boundary/groupBy/tag`
  - Why this fails:
    Boundary grouping requires a single boundary source.
  - Intentionally violated constraints:
    Supplied two boundary parameter types.

Endpoint coverage:
- Covers:
  `GET /elements/count/groupBy/boundary/groupBy/tag`, `POST /elements/count/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Count elements by boundary and tag value.
- Covers:
  `GET /elements/length/groupBy/boundary/groupBy/tag`, `POST /elements/length/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Sum element length by boundary and tag value.
- Covers:
  `GET /elements/area/groupBy/boundary/groupBy/tag`, `POST /elements/area/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Sum element area by boundary and tag value.
- Covers:
  `GET /elements/perimeter/groupBy/boundary/groupBy/tag`, `POST /elements/perimeter/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Sum element perimeter by boundary and tag value.

### Behavior 11: group OSM element metric density by boundary and tag

Behavior name:
group OSM element metric density by boundary and tag

Successful execution:
- Result:
  This behavior returns the selected element metric density split by boundary and tag value.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count/density/groupBy/boundary/groupBy/tag`, `POST /elements/count/density/groupBy/boundary/groupBy/tag`, `GET /elements/length/density/groupBy/boundary/groupBy/tag`, `POST /elements/length/density/groupBy/boundary/groupBy/tag`, `GET /elements/area/density/groupBy/boundary/groupBy/tag`, `POST /elements/area/density/groupBy/boundary/groupBy/tag`, `GET /elements/perimeter/density/groupBy/boundary/groupBy/tag`, or `POST /elements/perimeter/density/groupBy/boundary/groupBy/tag`.
- Constraints:
  Provide exactly one boundary parameter and exactly one `groupByKey`. The density for each boundary-tag group is computed using that boundary’s area.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `groupByKey` is missing or contains more than one key.
  - Endpoint group:
    Step 1: `GET /elements/count/density/groupBy/boundary/groupBy/tag`
  - Failure endpoint:
    `GET /elements/count/density/groupBy/boundary/groupBy/tag`
  - Why this fails:
    The implementation requires exactly one grouping key.
  - Intentionally violated constraints:
    Omitted `groupByKey` or supplied multiple values.
- Branch 2:
  - Unsatisfied condition:
    Boundary GeoJSON cannot be converted.
  - Endpoint group:
    Step 1: `GET /elements/count/density/groupBy/boundary/groupBy/tag`
  - Failure endpoint:
    `GET /elements/count/density/groupBy/boundary/groupBy/tag`
  - Why this fails:
    `bpolys` GeoJSON must be a valid FeatureCollection of polygonal features.
  - Intentionally violated constraints:
    Supplied invalid GeoJSON in `bpolys`.

Endpoint coverage:
- Covers:
  `GET /elements/count/density/groupBy/boundary/groupBy/tag`, `POST /elements/count/density/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Count density by boundary and tag value.
- Covers:
  `GET /elements/length/density/groupBy/boundary/groupBy/tag`, `POST /elements/length/density/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Length density by boundary and tag value.
- Covers:
  `GET /elements/area/density/groupBy/boundary/groupBy/tag`, `POST /elements/area/density/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Area density by boundary and tag value.
- Covers:
  `GET /elements/perimeter/density/groupBy/boundary/groupBy/tag`, `POST /elements/perimeter/density/groupBy/boundary/groupBy/tag`
- Distinct meaning:
  Perimeter density by boundary and tag value.

### Behavior 12: calculate OSM element metric ratio

Behavior name:
calculate OSM element metric ratio

Successful execution:
- Result:
  This behavior returns `value`, `value2`, and `ratio`, where `value` is the selected metric for `filter` and `value2` is the selected metric for `filter2`.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count/ratio`, `POST /elements/count/ratio`, `GET /elements/length/ratio`, `POST /elements/length/ratio`, `GET /elements/area/ratio`, `POST /elements/area/ratio`, `GET /elements/perimeter/ratio`, or `POST /elements/perimeter/ratio`.
- Constraints:
  For the OpenAPI-supported filter form, both `filter` and `filter2` must be nonblank, valid snapshot-suitable filter expressions. Provide a usable boundary. The implementation also contains a deprecated keys/types ratio mode, but those parameters are not exposed in the Swagger file.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `filter2` is missing or blank.
  - Endpoint group:
    Step 1: `GET /elements/count/ratio`
  - Failure endpoint:
    `GET /elements/count/ratio`
  - Why this fails:
    The implementation explicitly requires `filter2` for ratio endpoints.
  - Intentionally violated constraints:
    Omitted `filter2`.
- Branch 2:
  - Unsatisfied condition:
    `filter` or `filter2` uses a changeset filter.
  - Endpoint group:
    Step 1: `GET /elements/count/ratio`
  - Failure endpoint:
    `GET /elements/count/ratio`
  - Why this fails:
    Ratio endpoints operate on snapshots, and changeset filters are contribution-only.
  - Intentionally violated constraints:
    Used a contribution-only filter in a snapshot ratio request.

Endpoint coverage:
- Covers:
  `GET /elements/count/ratio`, `POST /elements/count/ratio`
- Distinct meaning:
  Ratio of counted elements matching `filter2` to counted elements matching `filter`.
- Covers:
  `GET /elements/length/ratio`, `POST /elements/length/ratio`
- Distinct meaning:
  Ratio of length matching `filter2` to length matching `filter`.
- Covers:
  `GET /elements/area/ratio`, `POST /elements/area/ratio`
- Distinct meaning:
  Ratio of area matching `filter2` to area matching `filter`.
- Covers:
  `GET /elements/perimeter/ratio`, `POST /elements/perimeter/ratio`
- Distinct meaning:
  Ratio of perimeter matching `filter2` to perimeter matching `filter`.

### Behavior 13: calculate OSM element metric ratio grouped by boundary

Behavior name:
calculate OSM element metric ratio grouped by boundary

Successful execution:
- Result:
  This behavior returns the selected metric ratio separately for each requested boundary object.
- Endpoint sequence:
  Step 1: Use exactly one endpoint: `GET /elements/count/ratio/groupBy/boundary`, `POST /elements/count/ratio/groupBy/boundary`, `GET /elements/length/ratio/groupBy/boundary`, `POST /elements/length/ratio/groupBy/boundary`, `GET /elements/area/ratio/groupBy/boundary`, `POST /elements/area/ratio/groupBy/boundary`, `GET /elements/perimeter/ratio/groupBy/boundary`, or `POST /elements/perimeter/ratio/groupBy/boundary`.
- Constraints:
  Provide exactly one boundary parameter, a valid nonblank `filter`, and a valid nonblank `filter2`. Each boundary object produces its own `value`, `value2`, and `ratio` series. `format=geojson` is meaningful for this boundary-grouped ratio response.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No boundary is supplied.
  - Endpoint group:
    Step 1: `GET /elements/count/ratio/groupBy/boundary`
  - Failure endpoint:
    `GET /elements/count/ratio/groupBy/boundary`
  - Why this fails:
    Boundary grouping cannot be computed without boundary groups.
  - Intentionally violated constraints:
    Omitted `bboxes`, `bcircles`, and `bpolys`.
- Branch 2:
  - Unsatisfied condition:
    `filter2` is missing.
  - Endpoint group:
    Step 1: `GET /elements/count/ratio/groupBy/boundary`
  - Failure endpoint:
    `GET /elements/count/ratio/groupBy/boundary`
  - Why this fails:
    Ratio computation needs both numerator and denominator filter definitions.
  - Intentionally violated constraints:
    Omitted `filter2`.

Endpoint coverage:
- Covers:
  `GET /elements/count/ratio/groupBy/boundary`, `POST /elements/count/ratio/groupBy/boundary`
- Distinct meaning:
  Count ratio per boundary.
- Covers:
  `GET /elements/length/ratio/groupBy/boundary`, `POST /elements/length/ratio/groupBy/boundary`
- Distinct meaning:
  Length ratio per boundary.
- Covers:
  `GET /elements/area/ratio/groupBy/boundary`, `POST /elements/area/ratio/groupBy/boundary`
- Distinct meaning:
  Area ratio per boundary.
- Covers:
  `GET /elements/perimeter/ratio/groupBy/boundary`, `POST /elements/perimeter/ratio/groupBy/boundary`
- Distinct meaning:
  Perimeter ratio per boundary.

### Behavior 14: count OSM contributions

Behavior name:
count OSM contributions

Successful execution:
- Result:
  This behavior returns counts of OSM contributions over time intervals.
- Endpoint sequence:
  Step 1: `GET /contributions/count` or `POST /contributions/count`.
- Constraints:
  Provide at least one request parameter and a usable boundary for predictable success. If `time` is supplied for a contribution endpoint, it must define an interval or at least two timestamps; a single timestamp is rejected. `contributionType` is optional and may contain `creation`, `deletion`, `tagChange`, `geometryChange`, or a comma-separated combination.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `time` is a single timestamp.
  - Endpoint group:
    Step 1: `GET /contributions/count`
  - Failure endpoint:
    `GET /contributions/count`
  - Why this fails:
    Contribution counting requires intervals, not a single snapshot timestamp.
  - Intentionally violated constraints:
    Supplied one timestamp in `time`.
- Branch 2:
  - Unsatisfied condition:
    `contributionType` has an unsupported value.
  - Endpoint group:
    Step 1: `GET /contributions/count`
  - Failure endpoint:
    `GET /contributions/count`
  - Why this fails:
    The contribution-type filter only recognizes creation, deletion, tag change, and geometry change.
  - Intentionally violated constraints:
    Supplied an invalid `contributionType`.

Endpoint coverage:
- Covers:
  `GET /contributions/count`, `POST /contributions/count`
- Distinct meaning:
  Count OSM contributions, optionally filtered by contribution type.

### Behavior 15: calculate OSM contribution density

Behavior name:
calculate OSM contribution density

Successful execution:
- Result:
  This behavior returns OSM contribution counts divided by boundary area in square kilometers.
- Endpoint sequence:
  Step 1: `GET /contributions/count/density` or `POST /contributions/count/density`.
- Constraints:
  Contribution interval constraints apply. A usable boundary is required for density calculation. `contributionType` may restrict the counted contribution types.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Boundary input is missing or unusable.
  - Endpoint group:
    Step 1: `GET /contributions/count/density`
  - Failure endpoint:
    `GET /contributions/count/density`
  - Why this fails:
    Density calculation needs a geometry area.
  - Intentionally violated constraints:
    Omitted boundary input.
- Branch 2:
  - Unsatisfied condition:
    `contributionType` is invalid.
  - Endpoint group:
    Step 1: `GET /contributions/count/density`
  - Failure endpoint:
    `GET /contributions/count/density`
  - Why this fails:
    The contribution-type filter rejects unrecognized names.
  - Intentionally violated constraints:
    Supplied a contribution type outside the allowed set.

Endpoint coverage:
- Covers:
  `GET /contributions/count/density`, `POST /contributions/count/density`
- Distinct meaning:
  Count contribution density over time intervals.

### Behavior 16: count latest OSM contributions

Behavior name:
count latest OSM contributions

Successful execution:
- Result:
  This behavior groups contributions by OSM entity and counts only the latest contribution per entity in the requested interval.
- Endpoint sequence:
  Step 1: `GET /contributions/latest/count` or `POST /contributions/latest/count`.
- Constraints:
  `time`, if supplied, must define a contribution interval or timestamp list with at least two timestamps. Optional `contributionType` filtering is applied after choosing each entity’s latest contribution.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `time` contains only one timestamp.
  - Endpoint group:
    Step 1: `GET /contributions/latest/count`
  - Failure endpoint:
    `GET /contributions/latest/count`
  - Why this fails:
    Latest contribution counting still uses contribution-view interval processing.
  - Intentionally violated constraints:
    Supplied a single timestamp.
- Branch 2:
  - Unsatisfied condition:
    `filter` syntax is invalid.
  - Endpoint group:
    Step 1: `GET /contributions/latest/count`
  - Failure endpoint:
    `GET /contributions/latest/count`
  - Why this fails:
    The implementation parses `filter` before grouping latest contributions.
  - Intentionally violated constraints:
    Supplied malformed `filter`.

Endpoint coverage:
- Covers:
  `GET /contributions/latest/count`, `POST /contributions/latest/count`
- Distinct meaning:
  Count only each entity’s latest contribution in the interval.

### Behavior 17: calculate latest OSM contribution density

Behavior name:
calculate latest OSM contribution density

Successful execution:
- Result:
  This behavior counts each entity’s latest contribution and divides the counts by boundary area.
- Endpoint sequence:
  Step 1: `GET /contributions/latest/count/density` or `POST /contributions/latest/count/density`.
- Constraints:
  The same latest-contribution and interval constraints apply as `/contributions/latest/count`. A usable boundary is needed for density.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Boundary input is invalid.
  - Endpoint group:
    Step 1: `GET /contributions/latest/count/density`
  - Failure endpoint:
    `GET /contributions/latest/count/density`
  - Why this fails:
    The implementation cannot compute density without valid geometry.
  - Intentionally violated constraints:
    Supplied malformed boundary input.
- Branch 2:
  - Unsatisfied condition:
    `time` is a single timestamp.
  - Endpoint group:
    Step 1: `GET /contributions/latest/count/density`
  - Failure endpoint:
    `GET /contributions/latest/count/density`
  - Why this fails:
    Contribution endpoints require interval-style time input.
  - Intentionally violated constraints:
    Supplied one timestamp.

Endpoint coverage:
- Covers:
  `GET /contributions/latest/count/density`, `POST /contributions/latest/count/density`
- Distinct meaning:
  Density of latest contributions per entity.

### Behavior 18: count OSM contributions grouped by boundary

Behavior name:
count OSM contributions grouped by boundary

Successful execution:
- Result:
  This behavior returns OSM contribution counts separately for each requested boundary object.
- Endpoint sequence:
  Step 1: `GET /contributions/count/groupBy/boundary` or `POST /contributions/count/groupBy/boundary`.
- Constraints:
  Exactly one boundary parameter must be supplied, and it may contain multiple boundary objects. Contribution interval and optional `contributionType` constraints apply. `format=geojson`, `json`, or `csv` may be used.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No boundary is supplied.
  - Endpoint group:
    Step 1: `GET /contributions/count/groupBy/boundary`
  - Failure endpoint:
    `GET /contributions/count/groupBy/boundary`
  - Why this fails:
    Boundary grouping needs explicit boundary groups.
  - Intentionally violated constraints:
    Omitted `bboxes`, `bcircles`, and `bpolys`.
- Branch 2:
  - Unsatisfied condition:
    `contributionType` is invalid.
  - Endpoint group:
    Step 1: `GET /contributions/count/groupBy/boundary`
  - Failure endpoint:
    `GET /contributions/count/groupBy/boundary`
  - Why this fails:
    The contribution filter rejects unsupported type names.
  - Intentionally violated constraints:
    Supplied unsupported `contributionType`.

Endpoint coverage:
- Covers:
  `GET /contributions/count/groupBy/boundary`, `POST /contributions/count/groupBy/boundary`
- Distinct meaning:
  Count contributions per boundary.

### Behavior 19: calculate OSM contribution density grouped by boundary

Behavior name:
calculate OSM contribution density grouped by boundary

Successful execution:
- Result:
  This behavior returns OSM contribution density separately for each requested boundary object.
- Endpoint sequence:
  Step 1: `GET /contributions/count/density/groupBy/boundary` or `POST /contributions/count/density/groupBy/boundary`.
- Constraints:
  Exactly one boundary parameter must be supplied. Each boundary group’s contribution count is divided by that boundary’s area. Contribution interval and optional `contributionType` constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Multiple boundary parameter types are supplied.
  - Endpoint group:
    Step 1: `GET /contributions/count/density/groupBy/boundary`
  - Failure endpoint:
    `GET /contributions/count/density/groupBy/boundary`
  - Why this fails:
    Input processing permits only one boundary source per request.
  - Intentionally violated constraints:
    Supplied both `bboxes` and `bpolys`.
- Branch 2:
  - Unsatisfied condition:
    `time` is a single timestamp.
  - Endpoint group:
    Step 1: `GET /contributions/count/density/groupBy/boundary`
  - Failure endpoint:
    `GET /contributions/count/density/groupBy/boundary`
  - Why this fails:
    Contribution endpoints require an interval or at least two timestamps.
  - Intentionally violated constraints:
    Supplied one timestamp.

Endpoint coverage:
- Covers:
  `GET /contributions/count/density/groupBy/boundary`, `POST /contributions/count/density/groupBy/boundary`
- Distinct meaning:
  Contribution count density per boundary.

### Behavior 20: count OSM users

Behavior name:
count OSM users

Successful execution:
- Result:
  This behavior returns the number of distinct OSM contributor user IDs over contribution intervals.
- Endpoint sequence:
  Step 1: `GET /users/count` or `POST /users/count`.
- Constraints:
  The endpoint uses contribution-view processing, so explicit `time` must be an interval or at least two timestamps. Optional `contributionType` filters which contributions are considered before counting unique users.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `time` contains only one timestamp.
  - Endpoint group:
    Step 1: `GET /users/count`
  - Failure endpoint:
    `GET /users/count`
  - Why this fails:
    User counting is based on contributions and requires interval processing.
  - Intentionally violated constraints:
    Supplied single-timestamp `time`.
- Branch 2:
  - Unsatisfied condition:
    `contributionType` is invalid.
  - Endpoint group:
    Step 1: `GET /users/count`
  - Failure endpoint:
    `GET /users/count`
  - Why this fails:
    User endpoints reuse the contribution-type filter.
  - Intentionally violated constraints:
    Supplied unsupported `contributionType`.

Endpoint coverage:
- Covers:
  `GET /users/count`, `POST /users/count`
- Distinct meaning:
  Count unique contributing users.

### Behavior 21: calculate OSM user density

Behavior name:
calculate OSM user density

Successful execution:
- Result:
  This behavior returns unique contributing user counts divided by boundary area.
- Endpoint sequence:
  Step 1: `GET /users/count/density` or `POST /users/count/density`.
- Constraints:
  Contribution interval constraints apply. A usable boundary is required for density. `contributionType` optionally filters the contribution events used to derive users.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Boundary input is missing or invalid.
  - Endpoint group:
    Step 1: `GET /users/count/density`
  - Failure endpoint:
    `GET /users/count/density`
  - Why this fails:
    Density requires valid spatial geometry.
  - Intentionally violated constraints:
    Omitted or malformed boundary input.
- Branch 2:
  - Unsatisfied condition:
    `time` is a single timestamp.
  - Endpoint group:
    Step 1: `GET /users/count/density`
  - Failure endpoint:
    `GET /users/count/density`
  - Why this fails:
    User density is contribution-based and requires an interval.
  - Intentionally violated constraints:
    Supplied a single timestamp.

Endpoint coverage:
- Covers:
  `GET /users/count/density`, `POST /users/count/density`
- Distinct meaning:
  Unique contributing users per square kilometer.

### Behavior 22: group OSM users by type

Behavior name:
group OSM users by type

Successful execution:
- Result:
  This behavior returns unique contributing user counts grouped by the OSM type of the contributed entity.
- Endpoint sequence:
  Step 1: `GET /users/count/groupBy/type` or `POST /users/count/groupBy/type`.
- Constraints:
  Contribution interval and boundary constraints apply. `contributionType` may restrict which contributions are considered before grouping and counting users.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `contributionType` is invalid.
  - Endpoint group:
    Step 1: `GET /users/count/groupBy/type`
  - Failure endpoint:
    `GET /users/count/groupBy/type`
  - Why this fails:
    The shared contribution-type validator rejects unsupported values.
  - Intentionally violated constraints:
    Supplied unsupported `contributionType`.
- Branch 2:
  - Unsatisfied condition:
    Request contains an unexpected parameter.
  - Endpoint group:
    Step 1: `GET /users/count/groupBy/type`
  - Failure endpoint:
    `GET /users/count/groupBy/type`
  - Why this fails:
    Resource-specific parameter validation rejects unknown parameters.
  - Intentionally violated constraints:
    Supplied a parameter not allowed for this endpoint.

Endpoint coverage:
- Covers:
  `GET /users/count/groupBy/type`, `POST /users/count/groupBy/type`
- Distinct meaning:
  Count unique users grouped by contributed entity type.

### Behavior 23: group OSM user density by type

Behavior name:
group OSM user density by type

Successful execution:
- Result:
  This behavior returns unique contributing user density grouped by contributed entity type.
- Endpoint sequence:
  Step 1: `GET /users/count/density/groupBy/type` or `POST /users/count/density/groupBy/type`.
- Constraints:
  A usable boundary is required. Contribution interval and optional `contributionType` constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Boundary input is missing.
  - Endpoint group:
    Step 1: `GET /users/count/density/groupBy/type`
  - Failure endpoint:
    `GET /users/count/density/groupBy/type`
  - Why this fails:
    Density grouping needs a boundary area.
  - Intentionally violated constraints:
    Omitted boundary input.
- Branch 2:
  - Unsatisfied condition:
    `time` is a single timestamp.
  - Endpoint group:
    Step 1: `GET /users/count/density/groupBy/type`
  - Failure endpoint:
    `GET /users/count/density/groupBy/type`
  - Why this fails:
    User density grouping is contribution-based and requires interval time semantics.
  - Intentionally violated constraints:
    Supplied a single timestamp.

Endpoint coverage:
- Covers:
  `GET /users/count/density/groupBy/type`, `POST /users/count/density/groupBy/type`
- Distinct meaning:
  Unique user density grouped by contributed entity type.

### Behavior 24: group OSM users by tag

Behavior name:
group OSM users by tag

Successful execution:
- Result:
  This behavior returns unique contributing user counts grouped by values of one requested OSM tag key.
- Endpoint sequence:
  Step 1: `GET /users/count/groupBy/tag` or `POST /users/count/groupBy/tag`.
- Constraints:
  `groupByKey` must contain exactly one key. `groupByValues` is optional. Contribution interval, boundary, and optional `contributionType` constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `groupByKey` is missing or contains more than one key.
  - Endpoint group:
    Step 1: `GET /users/count/groupBy/tag`
  - Failure endpoint:
    `GET /users/count/groupBy/tag`
  - Why this fails:
    The user tag-grouping executor requires exactly one grouping key.
  - Intentionally violated constraints:
    Omitted `groupByKey` or supplied multiple keys.
- Branch 2:
  - Unsatisfied condition:
    `contributionType` is invalid.
  - Endpoint group:
    Step 1: `GET /users/count/groupBy/tag`
  - Failure endpoint:
    `GET /users/count/groupBy/tag`
  - Why this fails:
    The contribution-type filter rejects unsupported values.
  - Intentionally violated constraints:
    Supplied invalid `contributionType`.

Endpoint coverage:
- Covers:
  `GET /users/count/groupBy/tag`, `POST /users/count/groupBy/tag`
- Distinct meaning:
  Count unique users by tag value.

### Behavior 25: group OSM user density by tag

Behavior name:
group OSM user density by tag

Successful execution:
- Result:
  This behavior returns unique contributing user density grouped by values of one requested OSM tag key.
- Endpoint sequence:
  Step 1: `GET /users/count/density/groupBy/tag` or `POST /users/count/density/groupBy/tag`.
- Constraints:
  `groupByKey` must contain exactly one key. `groupByValues` is optional. A usable boundary is required for density. Contribution interval constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `groupByKey` is missing.
  - Endpoint group:
    Step 1: `GET /users/count/density/groupBy/tag`
  - Failure endpoint:
    `GET /users/count/density/groupBy/tag`
  - Why this fails:
    Tag grouping cannot run without the grouping key.
  - Intentionally violated constraints:
    Omitted `groupByKey`.
- Branch 2:
  - Unsatisfied condition:
    Boundary input is malformed.
  - Endpoint group:
    Step 1: `GET /users/count/density/groupBy/tag`
  - Failure endpoint:
    `GET /users/count/density/groupBy/tag`
  - Why this fails:
    The implementation cannot compute density without valid boundary geometry.
  - Intentionally violated constraints:
    Supplied invalid boundary input.

Endpoint coverage:
- Covers:
  `GET /users/count/density/groupBy/tag`, `POST /users/count/density/groupBy/tag`
- Distinct meaning:
  Unique user density by tag value.

### Behavior 26: group OSM users by key

Behavior name:
group OSM users by key

Successful execution:
- Result:
  This behavior returns unique contributing user counts grouped by one or more requested OSM tag keys.
- Endpoint sequence:
  Step 1: `GET /users/count/groupBy/key` or `POST /users/count/groupBy/key`.
- Constraints:
  `groupByKeys` must contain at least one key. Contribution interval, boundary, and optional `contributionType` constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `groupByKeys` is missing.
  - Endpoint group:
    Step 1: `GET /users/count/groupBy/key`
  - Failure endpoint:
    `GET /users/count/groupBy/key`
  - Why this fails:
    The implementation requires `groupByKeys` for key grouping.
  - Intentionally violated constraints:
    Omitted `groupByKeys`.
- Branch 2:
  - Unsatisfied condition:
    `time` is a single timestamp.
  - Endpoint group:
    Step 1: `GET /users/count/groupBy/key`
  - Failure endpoint:
    `GET /users/count/groupBy/key`
  - Why this fails:
    User aggregation is contribution-based and requires interval time semantics.
  - Intentionally violated constraints:
    Supplied a single timestamp.

Endpoint coverage:
- Covers:
  `GET /users/count/groupBy/key`, `POST /users/count/groupBy/key`
- Distinct meaning:
  Count unique users by tag key.

### Behavior 27: group OSM users by boundary

Behavior name:
group OSM users by boundary

Successful execution:
- Result:
  This behavior returns unique contributing user counts separately for each requested boundary object.
- Endpoint sequence:
  Step 1: `GET /users/count/groupBy/boundary` or `POST /users/count/groupBy/boundary`.
- Constraints:
  Exactly one boundary parameter must be supplied, and it may contain multiple boundary objects. Contribution interval and optional `contributionType` constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No boundary is supplied.
  - Endpoint group:
    Step 1: `GET /users/count/groupBy/boundary`
  - Failure endpoint:
    `GET /users/count/groupBy/boundary`
  - Why this fails:
    Boundary grouping needs explicit boundary groups.
  - Intentionally violated constraints:
    Omitted all boundary parameters.
- Branch 2:
  - Unsatisfied condition:
    `contributionType` is invalid.
  - Endpoint group:
    Step 1: `GET /users/count/groupBy/boundary`
  - Failure endpoint:
    `GET /users/count/groupBy/boundary`
  - Why this fails:
    The contribution-type filter rejects unsupported values.
  - Intentionally violated constraints:
    Supplied invalid `contributionType`.

Endpoint coverage:
- Covers:
  `GET /users/count/groupBy/boundary`, `POST /users/count/groupBy/boundary`
- Distinct meaning:
  Count unique users per boundary.

### Behavior 28: group OSM user density by boundary

Behavior name:
group OSM user density by boundary

Successful execution:
- Result:
  This behavior returns unique contributing user density separately for each requested boundary object.
- Endpoint sequence:
  Step 1: `GET /users/count/density/groupBy/boundary` or `POST /users/count/density/groupBy/boundary`.
- Constraints:
  Exactly one boundary parameter must be supplied. Each boundary group’s unique user count is divided by that boundary’s area. Contribution interval and optional `contributionType` constraints apply.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    Multiple boundary parameter types are supplied.
  - Endpoint group:
    Step 1: `GET /users/count/density/groupBy/boundary`
  - Failure endpoint:
    `GET /users/count/density/groupBy/boundary`
  - Why this fails:
    Input processing accepts only one boundary parameter type per request.
  - Intentionally violated constraints:
    Supplied both `bboxes` and `bcircles`.
- Branch 2:
  - Unsatisfied condition:
    `time` is a single timestamp.
  - Endpoint group:
    Step 1: `GET /users/count/density/groupBy/boundary`
  - Failure endpoint:
    `GET /users/count/density/groupBy/boundary`
  - Why this fails:
    User density grouping uses contribution intervals.
  - Intentionally violated constraints:
    Supplied one timestamp.

Endpoint coverage:
- Covers:
  `GET /users/count/density/groupBy/boundary`, `POST /users/count/density/groupBy/boundary`
- Distinct meaning:
  Unique contributing user density per boundary.

### Unclear or auxiliary endpoints

No endpoint declared in `ohsome-api.json` is purely auxiliary or unmapped. All documented OpenAPI endpoints are covered above.