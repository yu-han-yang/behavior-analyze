# Domain-Level Behavior Analysis

## Domain Summary

The service exposes request-scoped analytics over OpenStreetMap history data stored in OSHDB. Its main business concepts are OSM elements, OSM contributions, OSM contributor users, spatial boundaries, time ranges, OSM types, OSM tags, filters, and derived metrics.

The API is not a CRUD service. It does not create OSM elements, users, contributions, boundaries, datasets, jobs, sessions, or reusable queries. Every supported function performs an immediate analytical read against a preconfigured OSHDB extract. The domain result is a count, length, area, perimeter, density, ratio, or grouped time series.

The implementation contains additional controllers for `/metadata`, `/elements/geometry`, `/elementsFullHistory/...`, and data-extraction `/contributions/...` endpoints, but `ohsome-api.json` and `full-behavior.md` exclude them. Supported behaviors below therefore use only exact function names from `full-behavior.md`.

## Available Function Inventory

### OSM Element Metrics

- `calculate OSM element metric`
  - Core endpoints: `GET|POST /elements/count`, `/elements/length`, `/elements/area`, `/elements/perimeter`
  - Domain meaning: compute count, total length, total area, or total perimeter of matching OSM elements over time.

- `calculate OSM element metric density`
  - Core endpoints: `GET|POST /elements/count/density`, `/elements/length/density`, `/elements/area/density`, `/elements/perimeter/density`
  - Domain meaning: compute an element metric divided by requested boundary area.

- `calculate OSM element metric ratio`
  - Core endpoints: `GET|POST /elements/count/ratio`, `/elements/length/ratio`, `/elements/area/ratio`, `/elements/perimeter/ratio`
  - Domain meaning: compare two element selections and return `value`, `value2`, and `ratio`.

### OSM Element Grouping

- `group OSM element metric by type`
  - Core endpoints: `GET|POST /elements/{metric}/groupBy/type`
  - Domain meaning: split an element metric by OSM type.

- `group OSM element metric density by type`
  - Core endpoints: `GET|POST /elements/{metric}/density/groupBy/type`
  - Domain meaning: split an element density metric by OSM type.

- `group OSM element metric by boundary`
  - Core endpoints: `GET|POST /elements/{metric}/groupBy/boundary`
  - Domain meaning: compute one element metric separately per supplied boundary.

- `group OSM element metric density by boundary`
  - Core endpoints: `GET|POST /elements/{metric}/density/groupBy/boundary`
  - Domain meaning: compute density separately per supplied boundary.

- `group OSM element metric by tag`
  - Core endpoints: `GET|POST /elements/{metric}/groupBy/tag`
  - Domain meaning: split an element metric by values of one OSM tag key.

- `group OSM element metric density by tag`
  - Core endpoints: `GET|POST /elements/{metric}/density/groupBy/tag`
  - Domain meaning: split element density by values of one OSM tag key.

- `group OSM element metric by key`
  - Core endpoints: `GET|POST /elements/{metric}/groupBy/key`
  - Domain meaning: split an element metric by presence of one or more OSM tag keys.

- `group OSM element metric by boundary and tag`
  - Core endpoints: `GET|POST /elements/{metric}/groupBy/boundary/groupBy/tag`
  - Domain meaning: split an element metric first by boundary and then by tag value.

- `group OSM element metric density by boundary and tag`
  - Core endpoints: `GET|POST /elements/{metric}/density/groupBy/boundary/groupBy/tag`
  - Domain meaning: split element density by boundary and tag value.

- `calculate OSM element metric ratio grouped by boundary`
  - Core endpoints: `GET|POST /elements/{metric}/ratio/groupBy/boundary`
  - Domain meaning: compute a two-filter element ratio separately per boundary.

### OSM Contributions

- `count OSM contributions`
  - Core endpoints: `GET|POST /contributions/count`
  - Domain meaning: count contribution events over contribution intervals.

- `calculate OSM contribution density`
  - Core endpoints: `GET|POST /contributions/count/density`
  - Domain meaning: count contribution events per square kilometer.

- `count latest OSM contributions`
  - Core endpoints: `GET|POST /contributions/latest/count`
  - Domain meaning: count only each entity’s latest contribution in an interval.

- `calculate latest OSM contribution density`
  - Core endpoints: `GET|POST /contributions/latest/count/density`
  - Domain meaning: count latest contributions per square kilometer.

- `count OSM contributions grouped by boundary`
  - Core endpoints: `GET|POST /contributions/count/groupBy/boundary`
  - Domain meaning: count contribution events separately per boundary.

- `calculate OSM contribution density grouped by boundary`
  - Core endpoints: `GET|POST /contributions/count/density/groupBy/boundary`
  - Domain meaning: count contribution density separately per boundary.

### OSM Contributor Users

- `count OSM users`
  - Core endpoints: `GET|POST /users/count`
  - Domain meaning: count distinct contributor user IDs over contribution intervals.

- `calculate OSM user density`
  - Core endpoints: `GET|POST /users/count/density`
  - Domain meaning: count distinct contributors per square kilometer.

- `group OSM users by type`
  - Core endpoints: `GET|POST /users/count/groupBy/type`
  - Domain meaning: split distinct contributor counts by contributed entity type.

- `group OSM user density by type`
  - Core endpoints: `GET|POST /users/count/density/groupBy/type`
  - Domain meaning: split contributor density by contributed entity type.

- `group OSM users by tag`
  - Core endpoints: `GET|POST /users/count/groupBy/tag`
  - Domain meaning: split distinct contributor counts by one OSM tag key’s values.

- `group OSM user density by tag`
  - Core endpoints: `GET|POST /users/count/density/groupBy/tag`
  - Domain meaning: split contributor density by tag value.

- `group OSM users by key`
  - Core endpoints: `GET|POST /users/count/groupBy/key`
  - Domain meaning: split distinct contributor counts by tag-key presence.

- `group OSM users by boundary`
  - Core endpoints: `GET|POST /users/count/groupBy/boundary`
  - Domain meaning: count distinct contributors separately per boundary.

- `group OSM user density by boundary`
  - Core endpoints: `GET|POST /users/count/density/groupBy/boundary`
  - Domain meaning: contributor density separately per boundary.

## Supported Business Behaviors

### Behavior 1: Measure OSM Element Presence or Geometry Over Time

Business goal:
Measure how many OSM elements exist, or how much element length, area, or perimeter exists, inside a boundary over time.

Domain context:
This is the core snapshot analytics behavior for map completeness and feature inventory analysis.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `calculate OSM element metric` (`GET /elements/count`, `GET /elements/length`, `GET /elements/area`, or `GET /elements/perimeter`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, optional `filter=building=*`, optional `types=way`, `format=json`, `showMetadata=false`, and optional `timeout=10` to compute the selected metric. The same values may be sent as form values to the matching `POST` endpoint.

Optional verification workflow:
None.

Existing-state shortcuts:
- No API setup steps can be skipped because the API has no setup functions.
- Direct database/application setup may preload the OSHDB extract, keytables, attribution metadata, and extract polygon.
- If equivalent dataset and boundary knowledge already exist, the caller still must send exactly one boundary parameter unless relying on an implementation-configured extract polygon.

Parameter and value bindings:
- The endpoint path selects the metric: `/count`, `/length`, `/area`, or `/perimeter`.
- `bboxes`, `bcircles`, or `bpolys` defines the area of interest and is consumed only by this request.
- `time` binds all returned timestamps to the same temporal selection.
- `filter`, `types`, `keys`, and `values` select the element population; if `filter` is used, implementation rejects simultaneous `keys`, `values`, or `types`.

Business result:
The response contains a time series of `value` entries for the selected metric. No service state is created or changed.

Constraints and invariants:
- At least one request parameter is required.
- Exactly one of `bboxes`, `bcircles`, or `bpolys` is accepted by implementation, despite source comments suggesting an extract polygon fallback.
- Snapshot filters must not use changeset-only expressions.
- `types` may be OSM types or simple feature types, but not a mixture.
- `format` accepts `json`, `csv`, and implementation-wide `geojson`, although GeoJSON is meaningful mainly for boundary-grouped and extraction responses.

Failure and exceptional cases:
- Failing function: `calculate OSM element metric`
  - Failure condition: request has no parameters.
  - Why it fails: `InputProcessor.checkParameters` rejects an empty parameter map.
  - Violated prerequisite or constraint: at least one request parameter must be supplied.
- Failing function: `calculate OSM element metric`
  - Failure condition: both `bboxes` and `bpolys` are supplied.
  - Why it fails: `setBoundaryType` accepts only one boundary source.
  - Violated prerequisite or constraint: exactly one boundary parameter type.
- Failing function: `calculate OSM element metric`
  - Failure condition: malformed `filter` or changeset-only filter on a snapshot endpoint.
  - Why it fails: the OSHDB filter parser or snapshot suitability check rejects it.
  - Violated prerequisite or constraint: valid snapshot filter expression.

Implementation notes:
Metric endpoints are pure analytical reads. Length, area, and perimeter are derived from snapshot geometries. Perimeter contributes `0.0` for non-polygonal geometries.

### Behavior 2: Measure OSM Element Metric Density

Business goal:
Normalize an element metric by the requested boundary area.

Domain context:
Density makes regions of different sizes comparable, for example buildings per square kilometer or road length per square kilometer.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `calculate OSM element metric density` (`GET /elements/count/density`, `GET /elements/length/density`, `GET /elements/area/density`, or `GET /elements/perimeter/density`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, optional `filter=highway=*`, `format=json`, and `showMetadata=false` to compute the selected metric divided by boundary area. The matching `POST` endpoint accepts the same values as form data.

Optional verification workflow:
None.

Existing-state shortcuts:
- Direct database setup can provide the OSHDB extract and extract metadata.
- Existing knowledge of the same boundary can be reused by sending the identical `bboxes`, `bcircles`, or `bpolys` value again; there is no reusable boundary ID.

Parameter and value bindings:
- The same boundary geometry both selects the data and supplies the area denominator.
- The path metric determines the numerator.
- `time`, `filter`, and type/tag parameters scope numerator values for all density calculations.

Business result:
The response contains density values per timestamp. No persisted density object is created.

Constraints and invariants:
- Density requires a valid non-empty polygonal boundary.
- Boundary area is computed from the request geometry.
- `timeout` must parse as a number and must not exceed the configured maximum.

Failure and exceptional cases:
- Failing function: `calculate OSM element metric density`
  - Failure condition: boundary is omitted or invalid.
  - Why it fails: density cannot be computed without geometry area.
  - Violated prerequisite or constraint: valid single boundary input.
- Failing function: `calculate OSM element metric density`
  - Failure condition: `timeout=abc` or timeout greater than configuration.
  - Why it fails: `defineRequestTimeout` parses and bounds the timeout.
  - Violated prerequisite or constraint: valid timeout seconds.

Implementation notes:
The implementation computes density as metric value divided by `Geo.areaOf(boundary) * 0.000001`, converting square meters to square kilometers.

### Behavior 3: Segment OSM Element Metrics by OSM Type

Business goal:
Understand how count, length, area, or perimeter is distributed across nodes, ways, and relations.

Domain context:
OSM type segmentation helps distinguish point, linear, polygonal, and relation-backed mapping activity.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM element metric by type` (`GET /elements/count/groupBy/type`, `/elements/length/groupBy/type`, `/elements/area/groupBy/type`, or `/elements/perimeter/groupBy/type`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, optional `filter=building=*`, `format=json`, and `showMetadata=false` to compute the selected metric split by OSM type.

Optional verification workflow:
None.

Existing-state shortcuts:
- Dataset initialization may be done directly through database/application configuration.
- The caller can reuse a known boundary and temporal window by resending the same parameter values.

Parameter and value bindings:
- The same boundary and time values scope all type groups.
- The selected metric endpoint applies to every group.
- Optional `filter` is applied before grouping.

Business result:
The response contains grouped time series, with group names such as `node`, `way`, and `relation`.

Constraints and invariants:
- Snapshot filter constraints apply.
- Unknown or repeated parameters are rejected.
- Type grouping is fixed to OSM entity type; callers cannot rename or configure these groups.

Failure and exceptional cases:
- Failing function: `group OSM element metric by type`
  - Failure condition: malformed `filter`.
  - Why it fails: parser rejects invalid OSHDB filter syntax.
  - Violated prerequisite or constraint: valid filter expression.
- Failing function: `group OSM element metric by type`
  - Failure condition: changeset-only filter is used.
  - Why it fails: snapshot endpoints reject contribution-only filters.
  - Violated prerequisite or constraint: snapshot-suitable filter.

Implementation notes:
The grouping is computed by OSHDB aggregation and returns only groups present in the result.

### Behavior 4: Segment OSM Element Density by OSM Type

Business goal:
Compare normalized element metrics across OSM entity types.

Domain context:
This supports regional comparison of type-specific mapping density.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM element metric density by type` (`GET /elements/count/density/groupBy/type`, `/elements/length/density/groupBy/type`, `/elements/area/density/groupBy/type`, or `/elements/perimeter/density/groupBy/type`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, optional `filter=building=*`, `format=json`, and `showMetadata=false` to compute density split by OSM type.

Optional verification workflow:
None.

Existing-state shortcuts:
- Preloaded OSHDB and known boundaries can be reused outside the API.
- No API-created state exists to skip.

Parameter and value bindings:
- The boundary is reused as both spatial scope and density denominator for every type group.
- The metric path binds the numerator used for all groups.

Business result:
The response contains per-type density time series.

Constraints and invariants:
- A valid single boundary is required.
- `showMetadata` accepts only `true`, `yes`, `false`, `no`, or blank.
- Snapshot filter restrictions apply.

Failure and exceptional cases:
- Failing function: `group OSM element metric density by type`
  - Failure condition: no boundary is supplied.
  - Why it fails: density and input processing need spatial area.
  - Violated prerequisite or constraint: valid boundary.
- Failing function: `group OSM element metric density by type`
  - Failure condition: `showMetadata=maybe`.
  - Why it fails: boolean parsing rejects unsupported values.
  - Violated prerequisite or constraint: supported boolean value.

Implementation notes:
Metadata is included only when `showMetadata` evaluates to true; POST responses omit request URL metadata.

### Behavior 5: Compare OSM Element Metrics Across Multiple Boundaries

Business goal:
Compute a selected metric independently for multiple named or generated spatial areas.

Domain context:
This supports regional dashboards, administrative comparisons, and boundary-by-boundary reporting.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM element metric by boundary` (`GET /elements/count/groupBy/boundary`, `/elements/length/groupBy/boundary`, `/elements/area/groupBy/boundary`, or `/elements/perimeter/groupBy/boundary`) with `bboxes=west:8.675,49.385,8.690,49.400|east:8.690,49.385,8.705,49.400`, `time=2014-01-01/2015-01-01/P1M`, optional `filter=building=*`, `format=json` or `format=geojson`, and `showMetadata=false`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Direct database setup can provide extract coverage.
- Existing boundary definitions can be reused only by resending their coordinates and optional IDs.

Parameter and value bindings:
- Each boundary ID in `bboxes`, `bcircles`, or `bpolys` becomes the group identifier.
- If IDs are omitted, implementation generates positional IDs.
- The same `time`, metric path, and filter are applied to every boundary group.

Business result:
The response contains one grouped result per boundary. With `format=geojson`, boundary geometries are returned as features carrying the computed results.

Constraints and invariants:
- Exactly one boundary parameter type is allowed, but it may contain multiple boundary objects separated by `|`.
- Boundary geometry must parse correctly and lie inside the configured extract polygon when one is configured.
- `format=geojson` is meaningful here.

Failure and exceptional cases:
- Failing function: `group OSM element metric by boundary`
  - Failure condition: malformed boundary coordinates.
  - Why it fails: geometry builder rejects wrong coordinate counts or non-numeric values.
  - Violated prerequisite or constraint: valid boundary format.
- Failing function: `group OSM element metric by boundary`
  - Failure condition: boundary is outside the configured data polygon.
  - Why it fails: geometry validation checks extract coverage.
  - Violated prerequisite or constraint: requested area must be covered by dataset.

Implementation notes:
Boundary IDs are taken from request prefixes such as `west:` or are generated automatically.

### Behavior 6: Compare OSM Element Metric Density Across Boundaries

Business goal:
Compute normalized element metrics independently for multiple spatial areas.

Domain context:
This is the size-normalized variant of regional element comparison.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM element metric density by boundary` (`GET /elements/count/density/groupBy/boundary`, `/elements/length/density/groupBy/boundary`, `/elements/area/density/groupBy/boundary`, or `/elements/perimeter/density/groupBy/boundary`) with `bboxes=west:8.675,49.385,8.690,49.400|east:8.690,49.385,8.705,49.400`, `time=2014-01-01/2015-01-01/P1M`, optional `filter=highway=*`, and `format=json` or `format=geojson`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Existing extract and boundary knowledge can be reused by resending the same boundary values.
- There is no API boundary registry or generated boundary ID lookup.

Parameter and value bindings:
- Each boundary provides both the group ID and its own density denominator.
- The selected metric endpoint defines the numerator for all boundary groups.

Business result:
The response contains one density time series per boundary.

Constraints and invariants:
- Exactly one boundary source is allowed.
- Each boundary’s density is computed using that boundary’s own area.
- `format` must be `json`, `csv`, or `geojson`.

Failure and exceptional cases:
- Failing function: `group OSM element metric density by boundary`
  - Failure condition: both `bboxes` and `bcircles` are provided.
  - Why it fails: boundary source is ambiguous.
  - Violated prerequisite or constraint: one boundary parameter type.
- Failing function: `group OSM element metric density by boundary`
  - Failure condition: `format=xml`.
  - Why it fails: format validation accepts only supported values.
  - Violated prerequisite or constraint: supported output format.

Implementation notes:
The same group-by-boundary machinery also powers contribution and user boundary grouping.

### Behavior 7: Segment OSM Element Metrics by Tag Value

Business goal:
Measure how an element metric is distributed across values of one OSM tag key.

Domain context:
This supports analyses such as road length by `highway` value or building area by `building` value.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM element metric by tag` (`GET /elements/count/groupBy/tag`, `/elements/length/groupBy/tag`, `/elements/area/groupBy/tag`, or `/elements/perimeter/groupBy/tag`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, `groupByKey=highway`, optional `groupByValues=primary,residential`, optional `filter=highway=*`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Known tag keys and values can be reused by resending `groupByKey` and `groupByValues`.
- No API can discover or persist tag-group definitions.

Parameter and value bindings:
- `groupByKey` names the single tag key used for grouping.
- `groupByValues` limits explicit groups; unmatched data is placed in a remainder group.
- The boundary, time, filter, and metric path apply to all tag groups.

Business result:
The response contains grouped metric time series by tag value, plus total/remainder groups where produced by the executor.

Constraints and invariants:
- Exactly one `groupByKey` is required.
- Repeating a query/form parameter is rejected; comma-separated values must be supplied in one parameter value.
- Unknown tag values are not necessarily errors; they can produce empty or fallback groups.

Failure and exceptional cases:
- Failing function: `group OSM element metric by tag`
  - Failure condition: `groupByKey` is omitted or contains multiple keys.
  - Why it fails: tag grouping requires exactly one key.
  - Violated prerequisite or constraint: one grouping key.
- Failing function: `group OSM element metric by tag`
  - Failure condition: `groupByValues` is sent twice as repeated parameters.
  - Why it fails: implementation requires every parameter to be unique.
  - Violated prerequisite or constraint: one value per parameter name.

Implementation notes:
The implementation maps known tags through the tag translator; unknown requested values can be represented by negative synthetic IDs.

### Behavior 8: Segment OSM Element Density by Tag Value

Business goal:
Compare normalized element metrics across values of one OSM tag key.

Domain context:
This supports density comparisons such as road-length density by road class.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM element metric density by tag` (`GET /elements/count/density/groupBy/tag`, `/elements/length/density/groupBy/tag`, `/elements/area/density/groupBy/tag`, or `/elements/perimeter/density/groupBy/tag`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, `groupByKey=highway`, optional `groupByValues=primary,residential`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- No API setup exists.
- Existing knowledge of tag values and boundaries can be reused by resending them.

Parameter and value bindings:
- `groupByKey` and optional `groupByValues` define groups.
- The same boundary is used to select data and compute the density denominator for every tag group.

Business result:
The response contains density time series grouped by tag value.

Constraints and invariants:
- `groupByKey` is mandatory.
- Valid boundary geometry is mandatory.
- Density denominator is shared across tag groups for a single-boundary request.

Failure and exceptional cases:
- Failing function: `group OSM element metric density by tag`
  - Failure condition: `groupByKey` omitted.
  - Why it fails: tag grouping cannot be computed without the grouping key.
  - Violated prerequisite or constraint: mandatory grouping key.
- Failing function: `group OSM element metric density by tag`
  - Failure condition: malformed `bboxes`, `bcircles`, or `bpolys`.
  - Why it fails: geometry cannot be built for density calculation.
  - Violated prerequisite or constraint: valid boundary geometry.

Implementation notes:
The result is analytical only; no tag statistics are cached or persisted.

### Behavior 9: Segment OSM Element Metrics by Tag-Key Presence

Business goal:
Measure how much of an element metric is associated with selected OSM tag keys.

Domain context:
This supports inventory questions such as how many elements carry `building` or `highway`.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM element metric by key` (`GET /elements/count/groupBy/key`, `/elements/length/groupBy/key`, `/elements/area/groupBy/key`, or `/elements/perimeter/groupBy/key`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, `groupByKeys=building,highway`, optional `filter=type:way`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Known tag-key lists can be reused by resending `groupByKeys`.
- No API can list available keys from the extract in the function inventory.

Parameter and value bindings:
- `groupByKeys` supplies one or more keys in a single parameter value.
- The same boundary and time scope all key groups.

Business result:
The response contains metric time series grouped by tag-key presence, with remainder/total behavior handled by the executor.

Constraints and invariants:
- At least one `groupByKeys` value is required.
- Unknown parameters are rejected by `ResourceParameters`.
- Elements without requested keys are placed in a remainder group.

Failure and exceptional cases:
- Failing function: `group OSM element metric by key`
  - Failure condition: `groupByKeys` omitted.
  - Why it fails: key grouping requires at least one requested key.
  - Violated prerequisite or constraint: mandatory key list.
- Failing function: `group OSM element metric by key`
  - Failure condition: unsupported parameter name is supplied.
  - Why it fails: resource-specific parameter validation rejects unknown names.
  - Violated prerequisite or constraint: allowed parameter set.

Implementation notes:
OpenAPI does not list all implementation-supported element filtering parameters such as `types`, `keys`, and `values`.

### Behavior 10: Segment OSM Element Metrics by Boundary and Tag Value

Business goal:
Compare tag-value distributions across multiple spatial areas.

Domain context:
This supports regional categorical comparisons, such as road class length by district.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM element metric by boundary and tag` (`GET /elements/count/groupBy/boundary/groupBy/tag`, `/elements/length/groupBy/boundary/groupBy/tag`, `/elements/area/groupBy/boundary/groupBy/tag`, or `/elements/perimeter/groupBy/boundary/groupBy/tag`) with `bboxes=west:8.675,49.385,8.690,49.400|east:8.690,49.385,8.705,49.400`, `time=2014-01-01/2015-01-01/P1M`, `groupByKey=highway`, optional `groupByValues=primary,residential`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Existing boundary and tag definitions can be reused only by resending coordinates and tag parameters.
- No generated boundary group can be referenced later by ID.

Parameter and value bindings:
- Boundary IDs define the first grouping dimension.
- `groupByKey` and `groupByValues` define the second grouping dimension inside each boundary.
- The metric path applies to every boundary-tag combination.

Business result:
The response contains metric time series per boundary and tag-value combination.

Constraints and invariants:
- Exactly one boundary source and exactly one `groupByKey` are required.
- Boundary IDs and tag identifiers together identify result groups.

Failure and exceptional cases:
- Failing function: `group OSM element metric by boundary and tag`
  - Failure condition: `groupByKey` omitted.
  - Why it fails: tag groups cannot be formed.
  - Violated prerequisite or constraint: mandatory grouping key.
- Failing function: `group OSM element metric by boundary and tag`
  - Failure condition: two boundary parameter types are supplied.
  - Why it fails: boundary grouping requires one boundary source.
  - Violated prerequisite or constraint: one boundary source.

Implementation notes:
This is a single analytical request, not a composition of a boundary grouping call followed by a tag grouping call.

### Behavior 11: Segment OSM Element Density by Boundary and Tag Value

Business goal:
Compare tag-value density across multiple spatial areas.

Domain context:
This is the normalized form of boundary-plus-tag comparison.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM element metric density by boundary and tag` (`GET /elements/count/density/groupBy/boundary/groupBy/tag`, `/elements/length/density/groupBy/boundary/groupBy/tag`, `/elements/area/density/groupBy/boundary/groupBy/tag`, or `/elements/perimeter/density/groupBy/boundary/groupBy/tag`) with `bboxes=west:8.675,49.385,8.690,49.400|east:8.690,49.385,8.705,49.400`, `time=2014-01-01/2015-01-01/P1M`, `groupByKey=highway`, optional `groupByValues=primary,residential`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Dataset and boundary setup can exist outside the API.
- Boundary IDs remain request-scoped and cannot be reused without resending coordinates.

Parameter and value bindings:
- Each boundary’s area is used as the density denominator for that boundary’s tag groups.
- `groupByKey` binds every tag-value group in every boundary.

Business result:
The response contains density time series per boundary-tag group.

Constraints and invariants:
- Exactly one boundary source and one `groupByKey` are required.
- GeoJSON `bpolys` must be a valid polygonal FeatureCollection.

Failure and exceptional cases:
- Failing function: `group OSM element metric density by boundary and tag`
  - Failure condition: missing or multi-key `groupByKey`.
  - Why it fails: implementation requires exactly one grouping key.
  - Violated prerequisite or constraint: one tag grouping key.
- Failing function: `group OSM element metric density by boundary and tag`
  - Failure condition: invalid GeoJSON in `bpolys`.
  - Why it fails: GeoJSON cannot be converted to valid boundary geometry.
  - Violated prerequisite or constraint: valid polygonal boundary input.

Implementation notes:
Density is computed per boundary, not against the union of all boundaries.

### Behavior 12: Compare Two OSM Element Selections as a Ratio

Business goal:
Compute the ratio between two filtered element populations for count, length, area, or perimeter.

Domain context:
This supports questions such as the share of residential roads among all roads or mapped building area matching a category.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `calculate OSM element metric ratio` (`GET /elements/count/ratio`, `/elements/length/ratio`, `/elements/area/ratio`, or `/elements/perimeter/ratio`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, `filter=highway=*`, `filter2=highway=residential`, `format=json`, and `showMetadata=false`.

Optional verification workflow:
None.

Existing-state shortcuts:
- No numerator or denominator selection can be created ahead of time through the API.
- Equivalent filters can be reused by resending the same `filter` and `filter2`.

Parameter and value bindings:
- `filter` defines `value`.
- `filter2` defines `value2`.
- The response `ratio` is derived from those two values for the same timestamp, boundary, and metric.
- The same boundary and time window bind both selections.

Business result:
The response contains `value`, `value2`, and `ratio` per timestamp.

Constraints and invariants:
- `filter2` is required for the OpenAPI-supported ratio form.
- `filter` and `filter2` must be valid snapshot-suitable filters.
- Implementation also supports deprecated `keys2`, `values2`, and `types2` ratio mode, but these are not exposed in `ohsome-api.json`.

Failure and exceptional cases:
- Failing function: `calculate OSM element metric ratio`
  - Failure condition: `filter2` omitted or blank.
  - Why it fails: implementation explicitly requires denominator filter.
  - Violated prerequisite or constraint: denominator selection must exist.
- Failing function: `calculate OSM element metric ratio`
  - Failure condition: changeset-only filter is used.
  - Why it fails: ratio endpoints operate on snapshots.
  - Violated prerequisite or constraint: snapshot-suitable filters.

Implementation notes:
If an element matches both filters, the implementation adds it to both `value` and `value2`.

### Behavior 13: Compare Two OSM Element Selections as Ratios per Boundary

Business goal:
Compute the same two-filter ratio separately for multiple spatial areas.

Domain context:
This supports regional share comparisons, such as residential-road share by district.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `calculate OSM element metric ratio grouped by boundary` (`GET /elements/count/ratio/groupBy/boundary`, `/elements/length/ratio/groupBy/boundary`, `/elements/area/ratio/groupBy/boundary`, or `/elements/perimeter/ratio/groupBy/boundary`) with `bboxes=west:8.675,49.385,8.690,49.400|east:8.690,49.385,8.705,49.400`, `time=2014-01-01/2015-01-01/P1M`, `filter=highway=*`, `filter2=highway=residential`, and `format=json` or `format=geojson`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Existing boundary coordinates and filters can be resent.
- There is no API-created boundary, query, numerator, or denominator resource.

Parameter and value bindings:
- Each boundary ID scopes its own `value`, `value2`, and `ratio`.
- `filter` and `filter2` are reused across every boundary group.
- `time` binds ratio values across all groups.

Business result:
The response contains ratio time series per boundary.

Constraints and invariants:
- Explicit boundary groups are required; extract-polygon fallback is not sufficient for boundary grouping.
- `filter2` is required.
- `format=geojson` can attach ratio results to boundary features.

Failure and exceptional cases:
- Failing function: `calculate OSM element metric ratio grouped by boundary`
  - Failure condition: all boundary parameters omitted.
  - Why it fails: boundary grouping cannot be computed without boundary groups.
  - Violated prerequisite or constraint: explicit boundary input.
- Failing function: `calculate OSM element metric ratio grouped by boundary`
  - Failure condition: `filter2` omitted.
  - Why it fails: ratio needs numerator and denominator definitions.
  - Violated prerequisite or constraint: both filters required.

Implementation notes:
Source includes deprecated basic-filter ratio variants using `keys2`, `values2`, and `types2`; the OpenAPI file only documents `filter2`.

### Behavior 14: Count OSM Contributions Over Time

Business goal:
Count contribution events in a boundary and time interval.

Domain context:
This measures editing activity, not the number of resulting map elements.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `count OSM contributions` (`GET /contributions/count`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, optional `contributionType=creation,tagChange`, optional `filter=building=*`, `format=json`, and `showMetadata=false`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Contribution history must exist in the OSHDB extract through database setup.
- No API function creates contribution records.

Parameter and value bindings:
- `time` must define intervals; each returned result binds `fromTimestamp` and `toTimestamp`.
- `contributionType` filters contribution events before counting.
- Boundary scopes the contribution view.

Business result:
The response contains contribution counts per interval.

Constraints and invariants:
- Contribution endpoints require interval-style time when `time` is supplied.
- Supported `contributionType` values are `creation`, `deletion`, `tagChange`, and `geometryChange`.

Failure and exceptional cases:
- Failing function: `count OSM contributions`
  - Failure condition: `time=2015-01-01` as a single timestamp.
  - Why it fails: contribution counting requires intervals.
  - Violated prerequisite or constraint: interval or timestamp list with at least two timestamps.
- Failing function: `count OSM contributions`
  - Failure condition: `contributionType=rename`.
  - Why it fails: contribution-type filter rejects unknown names.
  - Violated prerequisite or constraint: allowed contribution type.

Implementation notes:
The implementation removes deprecated `types`, `keys`, and `values` parameters for contribution count endpoints.

### Behavior 15: Measure OSM Contribution Density

Business goal:
Normalize contribution activity by boundary area.

Domain context:
This supports comparison of editing intensity across differently sized areas.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `calculate OSM contribution density` (`GET /contributions/count/density`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, optional `contributionType=creation`, optional `filter=building=*`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Existing OSHDB contribution data can be preloaded directly.
- Boundary definitions must still be supplied on the request.

Parameter and value bindings:
- Boundary selects contributions and provides area denominator.
- `contributionType` and `filter` restrict numerator events.

Business result:
The response contains contribution counts per square kilometer per interval.

Constraints and invariants:
- Valid boundary geometry is mandatory.
- Contribution interval constraints apply.

Failure and exceptional cases:
- Failing function: `calculate OSM contribution density`
  - Failure condition: boundary omitted.
  - Why it fails: density requires geometry area.
  - Violated prerequisite or constraint: valid boundary.
- Failing function: `calculate OSM contribution density`
  - Failure condition: invalid `contributionType`.
  - Why it fails: shared contribution-type filter rejects unsupported values.
  - Violated prerequisite or constraint: supported contribution type.

Implementation notes:
The same `fillContributionsResult` density logic is used for contribution and user density responses.

### Behavior 16: Count Each Entity’s Latest Contribution

Business goal:
Count only the latest contribution per OSM entity in a requested interval.

Domain context:
This reduces multiple edits to the same entity to one latest activity event.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `count latest OSM contributions` (`GET /contributions/latest/count`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01`, optional `contributionType=geometryChange`, optional `filter=building=*`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Existing contribution history can be preloaded in OSHDB.
- There is no API function to mark or persist “latest” contribution state.

Parameter and value bindings:
- `time` defines the interval in which latest contribution per entity is selected.
- `contributionType` is applied after choosing each entity’s latest contribution.

Business result:
The response contains counts of latest entity contributions per interval.

Constraints and invariants:
- Still uses contribution interval processing.
- Filter handling is delayed so grouping by entity can occur before latest selection.

Failure and exceptional cases:
- Failing function: `count latest OSM contributions`
  - Failure condition: single timestamp `time`.
  - Why it fails: latest contribution counting still uses interval processing.
  - Violated prerequisite or constraint: interval time.
- Failing function: `count latest OSM contributions`
  - Failure condition: malformed `filter`.
  - Why it fails: filter is parsed before grouping latest contributions.
  - Violated prerequisite or constraint: valid filter.

Implementation notes:
The implementation sets a `fullHistory` flag to avoid premature filtering before `groupByEntity()`.

### Behavior 17: Measure Density of Each Entity’s Latest Contribution

Business goal:
Normalize latest-contribution counts by boundary area.

Domain context:
This measures latest editing activity intensity rather than all edits.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `calculate latest OSM contribution density` (`GET /contributions/latest/count/density`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01`, optional `contributionType=tagChange`, optional `filter=building=*`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Existing OSHDB contribution history can be configured outside the API.
- Boundary must still be supplied in the density request.

Parameter and value bindings:
- Boundary supplies both contribution scope and density denominator.
- Latest contribution selection is scoped by the same `time`.

Business result:
The response contains latest contribution density per interval.

Constraints and invariants:
- Boundary must be valid.
- Contribution interval constraints apply.

Failure and exceptional cases:
- Failing function: `calculate latest OSM contribution density`
  - Failure condition: malformed boundary.
  - Why it fails: density cannot be computed without valid geometry.
  - Violated prerequisite or constraint: valid boundary.
- Failing function: `calculate latest OSM contribution density`
  - Failure condition: single timestamp `time`.
  - Why it fails: contribution endpoints require intervals.
  - Violated prerequisite or constraint: interval time.

Implementation notes:
This is latest-per-entity logic followed by density normalization.

### Behavior 18: Count OSM Contributions per Boundary

Business goal:
Compare contribution activity across multiple spatial areas.

Domain context:
This supports regional activity monitoring and editing hot spot analysis.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `count OSM contributions grouped by boundary` (`GET /contributions/count/groupBy/boundary`) with `bboxes=west:8.675,49.385,8.690,49.400|east:8.690,49.385,8.705,49.400`, `time=2014-01-01/2015-01-01/P1M`, optional `contributionType=creation,deletion`, optional `filter=building=*`, and `format=json` or `format=geojson`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Existing contribution data and boundary definitions can be maintained outside the API.
- Boundary IDs remain request-scoped.

Parameter and value bindings:
- Boundary IDs define contribution groups.
- `time`, `filter`, and `contributionType` are reused across every boundary.

Business result:
The response contains contribution-count time series per boundary.

Constraints and invariants:
- Explicit boundary groups are required.
- Contribution interval and contribution-type constraints apply.

Failure and exceptional cases:
- Failing function: `count OSM contributions grouped by boundary`
  - Failure condition: all boundary parameters omitted.
  - Why it fails: boundary grouping needs explicit groups.
  - Violated prerequisite or constraint: boundary input.
- Failing function: `count OSM contributions grouped by boundary`
  - Failure condition: invalid `contributionType`.
  - Why it fails: unsupported type names are rejected.
  - Violated prerequisite or constraint: allowed contribution types.

Implementation notes:
With `format=geojson`, response uses boundary geometries as GeoJSON features.

### Behavior 19: Measure OSM Contribution Density per Boundary

Business goal:
Compare normalized contribution activity across multiple boundaries.

Domain context:
This controls for area size when comparing editing activity.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `calculate OSM contribution density grouped by boundary` (`GET /contributions/count/density/groupBy/boundary`) with `bboxes=west:8.675,49.385,8.690,49.400|east:8.690,49.385,8.705,49.400`, `time=2014-01-01/2015-01-01/P1M`, optional `contributionType=creation`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- OSHDB and extract metadata can be initialized outside the API.
- No request-generated boundary ID can be reused later.

Parameter and value bindings:
- Each boundary group supplies its own density denominator.
- The same interval and contribution-type filter applies to all groups.

Business result:
The response contains contribution density time series per boundary.

Constraints and invariants:
- Exactly one boundary parameter type is accepted.
- Time must define intervals.

Failure and exceptional cases:
- Failing function: `calculate OSM contribution density grouped by boundary`
  - Failure condition: both `bboxes` and `bpolys` supplied.
  - Why it fails: input processing permits one boundary source.
  - Violated prerequisite or constraint: one boundary source.
- Failing function: `calculate OSM contribution density grouped by boundary`
  - Failure condition: one timestamp in `time`.
  - Why it fails: contribution endpoints require intervals.
  - Violated prerequisite or constraint: interval time.

Implementation notes:
Boundary-grouped density is computed per group, not against total area.

### Behavior 20: Count Distinct OSM Contributor Users

Business goal:
Count how many unique users contributed within an area and interval.

Domain context:
This measures contributor participation, not number of edits.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `count OSM users` (`GET /users/count`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, optional `contributionType=creation,tagChange`, optional `filter=building=*`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Contributor data exists only through preloaded OSHDB contribution history.
- No API function creates or retrieves user resources.

Parameter and value bindings:
- User IDs are derived from matching contributions.
- `contributionType` filters contributions before unique user counting.
- `time` defines contribution intervals.

Business result:
The response contains unique contributor counts per interval.

Constraints and invariants:
- User analytics are contribution-based and require interval time.
- The API returns counts, not user identities.

Failure and exceptional cases:
- Failing function: `count OSM users`
  - Failure condition: single timestamp `time`.
  - Why it fails: user counting is contribution-based and requires intervals.
  - Violated prerequisite or constraint: interval time.
- Failing function: `count OSM users`
  - Failure condition: invalid `contributionType`.
  - Why it fails: user endpoints reuse contribution-type validation.
  - Violated prerequisite or constraint: allowed contribution type.

Implementation notes:
Implementation uses `countUniq()` over `getContributorUserId()`.

### Behavior 21: Measure OSM Contributor User Density

Business goal:
Normalize unique contributor counts by boundary area.

Domain context:
This supports comparing contributor participation across areas of different size.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `calculate OSM user density` (`GET /users/count/density`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, optional `contributionType=creation`, optional `filter=building=*`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Existing OSHDB contribution history may be preconfigured.
- The density request still needs a boundary.

Parameter and value bindings:
- Boundary scopes contributions and provides the denominator.
- Unique users are derived from filtered contribution events.

Business result:
The response contains unique contributor density per interval.

Constraints and invariants:
- Valid boundary geometry is mandatory.
- Contribution interval constraints apply.

Failure and exceptional cases:
- Failing function: `calculate OSM user density`
  - Failure condition: missing or malformed boundary.
  - Why it fails: density requires valid spatial geometry.
  - Violated prerequisite or constraint: valid boundary.
- Failing function: `calculate OSM user density`
  - Failure condition: single timestamp `time`.
  - Why it fails: user density is contribution-based.
  - Violated prerequisite or constraint: interval time.

Implementation notes:
The endpoint does not expose individual user IDs or usernames.

### Behavior 22: Segment OSM Contributors by Contributed Entity Type

Business goal:
Count unique contributors grouped by the OSM type of the contributed entity.

Domain context:
This shows whether contributor activity is associated with nodes, ways, or relations.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM users by type` (`GET /users/count/groupBy/type`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, optional `contributionType=creation`, optional `filter=building=*`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- OSHDB user/contribution history can exist before the request.
- No API-created type groups exist.

Parameter and value bindings:
- The same contribution filter and time interval are reused for all type groups.
- OSM type is taken from the contributed entity.

Business result:
The response contains unique user counts grouped by entity type.

Constraints and invariants:
- Contribution interval constraints apply.
- Unknown request parameters are rejected.

Failure and exceptional cases:
- Failing function: `group OSM users by type`
  - Failure condition: invalid `contributionType`.
  - Why it fails: shared contribution-type validator rejects it.
  - Violated prerequisite or constraint: allowed contribution type.
- Failing function: `group OSM users by type`
  - Failure condition: unsupported parameter supplied.
  - Why it fails: resource-specific parameter validation rejects unknown names.
  - Violated prerequisite or constraint: allowed parameter set.

Implementation notes:
Grouping uses `getEntityAfter().getType()`, so deleted contributions may depend on OSHDB contribution state availability.

### Behavior 23: Segment OSM Contributor Density by Contributed Entity Type

Business goal:
Normalize type-grouped contributor counts by boundary area.

Domain context:
This provides density-based comparison of contributor participation by entity type.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM user density by type` (`GET /users/count/density/groupBy/type`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, optional `contributionType=creation`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Preloaded OSHDB data can satisfy contribution history.
- Boundary still must be supplied.

Parameter and value bindings:
- Boundary supplies the density denominator for all entity-type groups.
- `contributionType` scopes the contribution set before grouping.

Business result:
The response contains contributor density grouped by entity type.

Constraints and invariants:
- Boundary and contribution interval constraints apply.

Failure and exceptional cases:
- Failing function: `group OSM user density by type`
  - Failure condition: no boundary.
  - Why it fails: density grouping needs area.
  - Violated prerequisite or constraint: valid boundary.
- Failing function: `group OSM user density by type`
  - Failure condition: single timestamp `time`.
  - Why it fails: user density grouping uses contribution intervals.
  - Violated prerequisite or constraint: interval time.

Implementation notes:
The denominator is the request boundary area.

### Behavior 24: Segment OSM Contributors by Tag Value

Business goal:
Count unique contributors associated with values of one OSM tag key.

Domain context:
This supports questions like how many users contributed to different `highway` classes.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM users by tag` (`GET /users/count/groupBy/tag`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, `groupByKey=highway`, optional `groupByValues=primary,residential`, optional `contributionType=tagChange`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Known tag keys and values can be reused by resending them.
- No API lists all possible tag values in the function inventory.

Parameter and value bindings:
- `groupByKey` defines the tag key for grouping contributions before unique user counting.
- `groupByValues` identifies explicit tag-value groups.
- `contributionType` is applied before user ID uniqueness.

Business result:
The response contains unique contributor counts grouped by tag value.

Constraints and invariants:
- Exactly one `groupByKey` is required.
- Contribution interval and contribution-type constraints apply.

Failure and exceptional cases:
- Failing function: `group OSM users by tag`
  - Failure condition: `groupByKey` omitted or multi-key.
  - Why it fails: executor requires exactly one grouping key.
  - Violated prerequisite or constraint: one tag key.
- Failing function: `group OSM users by tag`
  - Failure condition: invalid `contributionType`.
  - Why it fails: contribution-type filter rejects it.
  - Violated prerequisite or constraint: allowed contribution type.

Implementation notes:
Unknown tag keys/values are represented through synthetic negative IDs and can still produce groups or remainders.

### Behavior 25: Segment OSM Contributor Density by Tag Value

Business goal:
Normalize tag-value grouped contributor counts by boundary area.

Domain context:
This compares contributor participation by tag category across regions.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM user density by tag` (`GET /users/count/density/groupBy/tag`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, `groupByKey=highway`, optional `groupByValues=primary,residential`, optional `contributionType=tagChange`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Existing extract and known tag values can be reused outside the API.
- No setup function is available or required.

Parameter and value bindings:
- Boundary is reused as spatial scope and density denominator.
- `groupByKey` defines all tag groups.

Business result:
The response contains unique contributor density grouped by tag value.

Constraints and invariants:
- `groupByKey` and valid boundary are required.
- Contribution interval constraints apply.

Failure and exceptional cases:
- Failing function: `group OSM user density by tag`
  - Failure condition: missing `groupByKey`.
  - Why it fails: tag grouping cannot run without the key.
  - Violated prerequisite or constraint: mandatory grouping key.
- Failing function: `group OSM user density by tag`
  - Failure condition: malformed boundary.
  - Why it fails: density cannot be computed.
  - Violated prerequisite or constraint: valid boundary geometry.

Implementation notes:
The endpoint returns counts/densities, not contributor identity lists.

### Behavior 26: Segment OSM Contributors by Tag-Key Presence

Business goal:
Count unique contributors associated with selected OSM tag keys.

Domain context:
This supports participation analysis by mapping theme, such as contributors to `building` versus `highway` features.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM users by key` (`GET /users/count/groupBy/key`) with `bboxes=8.675,49.385,8.700,49.410`, `time=2014-01-01/2015-01-01/P1M`, `groupByKeys=building,highway`, optional `contributionType=creation`, and `format=json`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Known key lists can be reused by resending `groupByKeys`.
- No API can persist or discover key groups in the function inventory.

Parameter and value bindings:
- `groupByKeys` defines one or more tag-key groups.
- User IDs are counted uniquely inside each key group and interval.

Business result:
The response contains unique contributor counts grouped by tag-key presence.

Constraints and invariants:
- At least one `groupByKeys` value is required.
- Time must define contribution intervals.

Failure and exceptional cases:
- Failing function: `group OSM users by key`
  - Failure condition: `groupByKeys` omitted.
  - Why it fails: implementation requires key grouping input.
  - Violated prerequisite or constraint: mandatory key list.
- Failing function: `group OSM users by key`
  - Failure condition: one timestamp in `time`.
  - Why it fails: user aggregation is contribution-based.
  - Violated prerequisite or constraint: interval time.

Implementation notes:
The executor emits total and remainder groups in addition to requested key groups.

### Behavior 27: Compare OSM Contributors Across Boundaries

Business goal:
Count distinct contributors separately for multiple spatial areas.

Domain context:
This supports regional contributor participation reports.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM users by boundary` (`GET /users/count/groupBy/boundary`) with `bboxes=west:8.675,49.385,8.690,49.400|east:8.690,49.385,8.705,49.400`, `time=2014-01-01/2015-01-01/P1M`, optional `contributionType=creation`, optional `filter=building=*`, and `format=json` or `format=geojson`.

Optional verification workflow:
None.

Existing-state shortcuts:
- OSHDB contribution/user history can be preconfigured.
- Boundary IDs are request-local and must be resent for later comparisons.

Parameter and value bindings:
- Boundary IDs define groups.
- The same time, filter, and contribution-type values apply to every boundary.

Business result:
The response contains distinct contributor counts per boundary.

Constraints and invariants:
- Explicit boundary groups are required.
- Contribution interval and contribution-type constraints apply.

Failure and exceptional cases:
- Failing function: `group OSM users by boundary`
  - Failure condition: no boundary input.
  - Why it fails: boundary grouping needs explicit boundary groups.
  - Violated prerequisite or constraint: boundary input.
- Failing function: `group OSM users by boundary`
  - Failure condition: invalid `contributionType`.
  - Why it fails: contribution-type filter rejects unsupported values.
  - Violated prerequisite or constraint: allowed contribution type.

Implementation notes:
The implementation reuses contribution boundary grouping and maps contribution user IDs through `countUniq()`.

### Behavior 28: Compare OSM Contributor Density Across Boundaries

Business goal:
Compute distinct contributor density separately for multiple spatial areas.

Domain context:
This normalizes contributor participation by boundary area.

Starting point:
Preconfigured OSHDB extract and tag translator; no prior API-created service state.

Required execution workflow:
1. Use function `group OSM user density by boundary` (`GET /users/count/density/groupBy/boundary`) with `bboxes=west:8.675,49.385,8.690,49.400|east:8.690,49.385,8.705,49.400`, `time=2014-01-01/2015-01-01/P1M`, optional `contributionType=creation`, and `format=json` or `format=geojson`.

Optional verification workflow:
None.

Existing-state shortcuts:
- Existing contribution data and known boundaries can be reused outside the API.
- Request-local boundary identifiers must still match the supplied boundary list.

Parameter and value bindings:
- Each boundary’s own area is used as the denominator.
- `contributionType` and `time` are reused across all boundary groups.

Business result:
The response contains unique contributor density per boundary.

Constraints and invariants:
- Exactly one boundary parameter type is accepted.
- Time must define intervals.

Failure and exceptional cases:
- Failing function: `group OSM user density by boundary`
  - Failure condition: both `bboxes` and `bcircles` supplied.
  - Why it fails: input processing accepts only one boundary source.
  - Violated prerequisite or constraint: one boundary parameter type.
- Failing function: `group OSM user density by boundary`
  - Failure condition: one timestamp in `time`.
  - Why it fails: user density grouping uses contribution intervals.
  - Violated prerequisite or constraint: interval time.

Implementation notes:
Density per boundary is not affected by the area of other supplied boundaries.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: API-Managed Dataset or Extract Lifecycle

Priority:
Critical domain gap

Expected business goal:
A user would expect to register, select, update, validate, or inspect which OSM/OSHDB extract is being analyzed.

Why it is unsupported:
No function in `full-behavior.md` creates or selects an OSHDB extract. All supported functions assume `DbConnData`, tag translator, extract metadata, and optional data polygon already exist.

Existing functions considered:
- `calculate OSM element metric`: consumes configured OSHDB data but cannot load or choose it.
- `count OSM contributions`: reads contribution history but cannot import it.
- `count OSM users`: derives users from contribution history but cannot manage user data.

Missing capability:
Dataset/extract registration, selection, metadata lookup in the documented function set, readiness validation, and extract refresh.

Proof that function composition is insufficient:
Chaining analytics functions can only query the currently configured backend. It cannot create dataset state, switch extracts, validate that an extract covers a region before execution, or roll back to an earlier extract.

Evidence from existing functions/source:
All supported functions instantiate request executors over `DbConnData.db`. Source has a `/metadata` controller, but it is absent from `ohsome-api.json` and from `full-behavior.md`.

Business impact:
Users cannot build a fully API-realizable workflow from data import to analytics.

### Missing Behavior 2: Reusable Boundary Management

Priority:
Important robustness gap

Expected business goal:
A user would expect to create, validate, name, list, reuse, or delete common analysis areas.

Why it is unsupported:
Boundaries are request parameters only. Boundary IDs are parsed from request prefixes or generated positionally and are not persisted.

Existing functions considered:
- `group OSM element metric by boundary`: accepts boundary coordinates and IDs for one request.
- `calculate OSM contribution density grouped by boundary`: computes per-boundary density but does not store boundaries.
- `group OSM users by boundary`: uses boundaries only as request-scoped groups.

Missing capability:
Boundary CRUD, validation-only endpoint, generated boundary ID lookup, and reusable boundary references.

Proof that function composition is insufficient:
Calling a boundary-grouped function returns analytics, not a persistent boundary resource. A later request cannot refer to a prior generated ID without resending the full geometry.

Evidence from existing functions/source:
`InputProcessingUtils` stores boundary IDs in request-local arrays; no repository or controller persists them.

Business impact:
Repeated workflows are error-prone and cannot rely on stable server-side boundary identifiers.

### Missing Behavior 3: Contracted Raw OSM Element and Contribution Extraction

Priority:
Important robustness gap

Expected business goal:
A user would expect to retrieve the actual elements or contributions behind an aggregate result.

Why it is unsupported:
The function inventory contains only aggregate analytics. Implementation source has extraction controllers, but they are excluded from the OpenAPI file and `full-behavior.md`.

Existing functions considered:
- `calculate OSM element metric`: returns aggregate values only.
- `count OSM contributions`: returns counts only.
- `count latest OSM contributions`: returns counts only.

Missing capability:
Documented functions for raw geometry, bbox, centroid, full history, latest contribution extraction, and result pagination/stream semantics.

Proof that function composition is insufficient:
Aggregates cannot reconstruct individual OSM entity IDs, geometries, tags, contributors, or contribution details.

Evidence from existing functions/source:
Source contains `/elements/geometry`, `/elementsFullHistory/...`, and `/contributions/...` extraction controllers, but `ohsome-api.json` omits them.

Business impact:
Users cannot move from aggregate anomaly detection to documented drill-down through the function set.

### Missing Behavior 4: Contributor Identity Listing and Search

Priority:
Important robustness gap

Expected business goal:
A user may need to identify which users contributed in a region or category.

Why it is unsupported:
User functions count distinct contributor IDs but do not return the IDs, names, profiles, or contribution memberships.

Existing functions considered:
- `count OSM users`: counts distinct users.
- `group OSM users by tag`: counts distinct users per tag group.
- `group OSM users by boundary`: counts distinct users per boundary.

Missing capability:
List/search contributors, retrieve contributor IDs per group, or page user identities.

Proof that function composition is insufficient:
Counts lose identity information. No sequence of count functions can recover which users were counted.

Evidence from existing functions/source:
`UsersRequestExecutor` maps contributions to `getContributorUserId()` and immediately calls `countUniq()`.

Business impact:
Participation analysis cannot support follow-up contributor auditing or community engagement workflows.

### Missing Behavior 5: Contribution Grouping by Type, Tag, or Key in the Contract

Priority:
API ergonomics gap

Expected business goal:
A user might expect contribution counts to be grouped by OSM type, tag value, or tag key, as element and user analytics support.

Why it is unsupported:
Contribution functions in `full-behavior.md` support ungrouped counts, density, latest counts, and boundary grouping only.

Existing functions considered:
- `count OSM contributions`: can filter by `contributionType` but not group by OSM type or tag.
- `count OSM contributions grouped by boundary`: groups only by boundary.
- `group OSM users by tag`: groups contributors, not contribution events.

Missing capability:
`/contributions/count/groupBy/type`, `/contributions/count/groupBy/tag`, and `/contributions/count/groupBy/key` style functions.

Proof that function composition is insufficient:
Counting users by tag or elements by tag cannot produce contribution-event counts by tag. Boundary grouping cannot split by tag/key/type.

Evidence from existing functions/source:
`ContributionsCountController` exposes only count, density, latest count, latest density, and boundary group variants.

Business impact:
Editing activity cannot be categorized as richly as element inventory or user participation.

### Missing Behavior 6: Atomic Multi-Metric Reports

Priority:
API ergonomics gap

Expected business goal:
A user may want count, length, area, perimeter, density, and ratio results in one consistent report.

Why it is unsupported:
Each function computes one metric family per request.

Existing functions considered:
- `calculate OSM element metric`: one metric selected by path.
- `calculate OSM element metric density`: one density metric selected by path.
- `calculate OSM element metric ratio`: one ratio metric selected by path.

Missing capability:
A multi-metric endpoint or server-side report definition.

Proof that function composition is insufficient:
Multiple calls can approximate a report by reusing parameters, but there is no atomicity, shared execution plan, or guarantee that all values come from the same backend state if the configured extract changes between calls.

Evidence from existing functions/source:
Controllers are split by metric resource (`CountController`, `LengthController`, `AreaController`, `PerimeterController`).

Business impact:
Dashboard clients must orchestrate many calls and handle partial failures themselves.

### Missing Behavior 7: Validation-Only Query Planning

Priority:
API ergonomics gap

Expected business goal:
A user may want to validate a boundary, filter, tag grouping, or time range before running an expensive query.

Why it is unsupported:
Validation is embedded in execution.

Existing functions considered:
- All metric, contribution, and user functions validate parameters only as part of computing results.

Missing capability:
Dry-run or validation endpoint for boundaries, filters, time ranges, and expected computation cost.

Proof that function composition is insufficient:
Any existing function that validates also executes OSHDB processing if validation passes.

Evidence from existing functions/source:
`InputProcessor.processParameters()` validates and constructs the `MapReducer` for execution.

Business impact:
Clients cannot cheaply preflight requests or provide precise UI feedback without running analytics.

## Cross-Behavior Observations

- The service is stateless at the API layer. All supported functions are analytical reads over preconfigured OSHDB state.
- The main reusable domain values are not server resources. Boundaries, filters, tag keys, contribution types, and time ranges must be resent on every request.
- Implementation validation is stricter and richer than the OpenAPI file. It rejects empty parameter maps, repeated parameters, unknown parameter names, invalid boolean values, invalid time formats, unsupported content types, and ambiguous boundary sources.
- OpenAPI and implementation disagree. Source supports implementation-only endpoints such as `/metadata` and data extraction routes, while the OpenAPI file excludes them. Source also supports `types`, `keys`, `values`, and deprecated ratio parameters `types2`, `keys2`, `values2`, but the OpenAPI parameter inventory is narrower.
- Density is derived from request boundary area. For grouped boundary density, each boundary uses its own area.
- Contribution and user analytics require interval semantics. A single timestamp is valid for snapshot element metrics but invalid for contribution-based functions.
- `filter` cannot be combined with legacy `keys`, `values`, or `types` in normal processing.
- The API returns aggregate result values, not persisted analytics, jobs, raw entity identity, or reusable query objects.
- CSV responses are written directly to the servlet response and return no response object internally.
- `format=geojson` is accepted by generic format validation, but it is only domain-meaningful for boundary-grouped and extraction-style responses.

## Coverage Summary

Supported domain areas:
OSM element metric analytics, element density, element grouping by type/boundary/tag/key, element ratios, contribution counts, contribution densities, latest contribution counts, boundary-grouped contribution analytics, user counts, user densities, and user grouping by type/tag/key/boundary.

Partially supported domain areas:
Dataset metadata and raw extraction exist in source but are not covered by `ohsome-api.json` or `full-behavior.md`; contribution grouping is less complete than element and user grouping; multi-metric reporting requires client orchestration.

Unsupported domain areas:
API-managed dataset lifecycle, reusable boundary resources, documented raw result drill-down from aggregates, contributor identity listing, validation-only dry runs, persistent reports/jobs, and atomic multi-metric analytics.