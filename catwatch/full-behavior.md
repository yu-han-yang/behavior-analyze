### Function 1: expose public runtime configuration

Function name:
expose public runtime configuration

Core endpoint(s):
- `GET /config`

Preconditions:
- None.

Successful execution:
- Result:
  This function returns a sorted map of public configuration properties selected by `application-public.properties`.
- Invocation:
  Step 1: `GET /config` with no path, query, body, form, or header values
- Constraints:
  Only property names listed in `src/main/resources/application-public.properties` are returned. Values are read from the active Spring `Environment`.

Failure or exceptional branches:
None identified from the OpenAPI contract or `src` implementation.

Endpoint coverage:
- Covers:
  `GET /config`
- Distinct meaning:
  Returns public runtime configuration values, not stored application data.

### Function 2: trigger GitHub data fetch

Function name:
trigger GitHub data fetch

Core endpoint(s):
- `GET /fetch`

Preconditions:
- The application has a configured `organization.list` value. This can be satisfied by setting the Spring property directly; it is not established by another REST endpoint.
- GitHub access, cache access, and repository persistence are available for the configured organizations. This can be satisfied by configured GitHub credentials or unauthenticated GitHub access, plus working database repositories; direct database setup is not equivalent because this function's purpose is to collect and save a new snapshot.

Successful execution:
- Result:
  This function fetches the configured GitHub organizations, creates a snapshot, and saves statistics, projects, and contributors with the current timestamp as `snapshotDate`.
- Invocation:
  Step 1: `GET /fetch` with no path, query, body, form, or header values
- Constraints:
  The configured organizations come from `organization.list`. `RetryableFetcher` retries `CrawlerRetryException`; saved records are produced by `Fetcher.fetchData()` and persisted through the statistics, project, and contributor repositories.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - GitHub snapshot collection for at least one configured organization cannot complete because the GitHub API, network, credentials, cache, or snapshot task execution fails repeatedly. This is not created by a setup endpoint; it is an external runtime condition.
  - Failure endpoint:
    `GET /fetch`
  - Why this fails:
    `RetryableFetcher` retries transient crawler failures. After retries are exhausted, it calls the recovery mail sender. If recovery completes, the controller can still return `"OK"` even though no new snapshot was saved.
  - Intentionally violated constraints:
    The required external snapshot collection path did not allow `Fetcher.fetchData()` to save a complete snapshot.

Endpoint coverage:
- Covers:
  `GET /fetch`
- Distinct meaning:
  Creates project, contributor, and statistics snapshot state used by read endpoints.

### Function 3: list latest projects

Function name:
list latest projects

Core endpoint(s):
- `GET /projects`

Preconditions:
- None. Existing `Project` rows are required only for a non-empty response; they can be inserted directly or created by `GET /fetch`, but the endpoint still executes successfully when no matching projects exist.

Successful execution:
- Result:
  This function returns latest project snapshots for selected organizations.
- Invocation:
  Step 1: `GET /projects` with optional query values `organizations={organizations}`, `limit={limit}`, `offset={offset}`, `sortBy={sortBy}`, `q={projectNamePrefix}`, and `language={primaryLanguage}`, while omitting `start_date` and `end_date`
- Constraints:
  If `organizations` is omitted, the implementation uses `organization.list`. `q` filters project names by prefix. `language` filters exact `primaryLanguage`. `sortBy` supports `stars`, `commits`, `forks`, `contributors`, and `score`; prefix `-` requests descending order. Default sorting is descending `score`. `limit` defaults to 5 and `offset` defaults to 0.

Failure or exceptional branches:
None specific to this parameter combination beyond normal request binding and runtime errors.

Endpoint coverage:
- Covers:
  `GET /projects`
- Distinct meaning:
  Without date parameters, this endpoint returns current or latest project rows.

### Function 4: list projects as of an end date

Function name:
list projects as of an end date

Core endpoint(s):
- `GET /projects`

Preconditions:
- None. Existing `Project` rows at or before `{endDate}` are required only for a non-empty response; they can be inserted directly or created by `GET /fetch` before the requested `end_date`.

Successful execution:
- Result:
  This function returns projects from the latest stored snapshot at or before the supplied `end_date`.
- Invocation:
  Step 1: `GET /projects` with query value `end_date={endDate}`, optional query values `organizations={organizations}`, `limit={limit}`, `offset={offset}`, `sortBy={sortBy}`, `q={projectNamePrefix}`, and `language={primaryLanguage}`, while omitting `start_date`
- Constraints:
  `end_date` must bind to the controller's `yyyy-MM-dd'T'HH:mm:ss` pattern. For each organization, the repository selects the latest snapshot date less than or equal to `{endDate}`. Filtering, sorting, limit, and offset follow the same rules as latest project listing.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request supplies `end_date={malformedEndDate}`, and `{malformedEndDate}` is not parseable by Spring using the controller pattern `yyyy-MM-dd'T'HH:mm:ss`.
  - Failure endpoint:
    `GET /projects`
  - Why this fails:
    Request binding fails before the controller can call `ProjectService`.
  - Intentionally violated constraints:
    The `end_date` query value does not match the expected date-time format.

Endpoint coverage:
- Covers:
  `GET /projects`
- Distinct meaning:
  With only `end_date`, this endpoint returns project state at or before a historical date.

### Function 5: list project metric deltas over time

Function name:
list project metric deltas over time

Core endpoint(s):
- `GET /projects`

Preconditions:
- None. Existing start and end `Project` snapshots are required only for meaningful deltas; they can be inserted directly or created by separate `GET /fetch` calls before the request.

Successful execution:
- Result:
  This function returns project records where count-like metrics are differences between a start snapshot and an end snapshot.
- Invocation:
  Step 1: `GET /projects` with query value `start_date={startDate}`, optional query value `end_date={endDate}`, and optional query values `organizations={organizations}`, `limit={limit}`, `offset={offset}`, `sortBy={sortBy}`, `q={projectNamePrefix}`, and `language={primaryLanguage}`
- Constraints:
  `start_date` and `end_date`, when supplied, must bind to `yyyy-MM-dd'T'HH:mm:ss`. If `end_date` is omitted, the end side is the latest snapshot. Projects are matched by `gitHubProjectId`. For projects present at both start and end, `starsCount`, `commitsCount`, `forksCount`, `contributorsCount`, and `score` become end-minus-start deltas. Projects present only at the end are returned with their end values unchanged.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request supplies `start_date={malformedStartDate}` or `end_date={malformedEndDate}`, and at least one supplied date is not parseable by Spring using the controller pattern `yyyy-MM-dd'T'HH:mm:ss`.
  - Failure endpoint:
    `GET /projects`
  - Why this fails:
    Date binding fails before the controller can call `ProjectService`.
  - Intentionally violated constraints:
    One or both date query values do not match the expected date-time format.

Endpoint coverage:
- Covers:
  `GET /projects`
- Distinct meaning:
  With `start_date`, this endpoint returns project metric changes over a time window.

### Function 6: list current top contributors

Function name:
list current top contributors

Core endpoint(s):
- `GET /contributors`

Preconditions:
- A contributor snapshot exists at or before request time for the selected organizations. This can be satisfied by directly inserting `Contributor` rows with `key.snapshotDate <= now`, `key.organizationId` values matching the selected organizations, and `organizationName` values matching `organizations={organizations}`, or by calling `GET /fetch`, which stores contributor rows for the configured `organization.list`. `GET /fetch` returns `"OK"` and does not expose the generated `snapshotDate`, so the snapshot timestamp must be inferred from the database or made earlier than the contributor request.

Successful execution:
- Result:
  This function returns aggregated contributor metrics for selected organizations at the latest available contributor snapshot.
- Invocation:
  Step 1: `GET /contributors` with required query value `organizations={organizations}` and optional query values `limit={limit}`, `offset={offset}`, `sortBy={sortBy}`, and `q={contributorNamePrefix}`, while omitting `start_date` and `end_date`
- Constraints:
  Swagger and the controller binding mark `organizations` as required, although the internal helper can fall back to `organization.list` if it receives a null or empty value. `offset` must be greater than or equal to 0. `limit` must be greater than 0. Valid `sortBy` values are `organizationalCommitsCount`, `organizationalProjectsCount`, `personalCommitsCount`, `personalProjectsCount`, `organizationName`, and `name`, optionally prefixed with `-`. `q` filters contributor names by prefix.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No contributor snapshot exists at or before the request time. This can be produced by starting from an empty contributor table, deleting contributor snapshot rows, or intentionally not inserting them directly and not calling `GET /fetch`.
  - Failure endpoint:
    `GET /contributors`
  - Why this fails:
    The no-date path requires `repository.findPreviousSnapShotDate(now)` to return a snapshot date. If it returns `null`, the controller throws `UnsupportedOperationException`.
  - Intentionally violated constraints:
    The required contributor snapshot state was omitted.
- Branch 2:
  - Preconditions:
    - A contributor snapshot exists at or before request time for the selected organizations. This can be satisfied by directly inserting matching `Contributor` rows or by calling `GET /fetch` before the failing request.
    - The request supplies `offset={offset}`, `limit={limit}`, or `sortBy={sortBy}` with an invalid value.
  - Failure endpoint:
    `GET /contributors`
  - Why this fails:
    The controller validates `offset >= 0`, `limit > 0`, and `sortBy` membership in the allowed contributor sort list.
  - Intentionally violated constraints:
    `offset` is negative, `limit` is zero or negative, or `sortBy` is not one of the allowed contributor sort fields.

Endpoint coverage:
- Covers:
  `GET /contributors`
- Distinct meaning:
  Without both date parameters, this endpoint returns current top contributors.

### Function 7: list contributor activity deltas over time

Function name:
list contributor activity deltas over time

Core endpoint(s):
- `GET /contributors`

Preconditions:
- An earlier contributor snapshot exists at or before `start_date={startDate}` for the selected organizations. This can be satisfied by directly inserting `Contributor` rows with `key.snapshotDate <= {startDate}` and organization fields matching `organizations={organizations}`, or by calling `GET /fetch` before `{startDate}` so it stores those contributor rows. `GET /fetch` returns `"OK"` and does not expose the generated `snapshotDate`, so the chosen `{startDate}` must be after the generated snapshot timestamp.
- A later contributor snapshot exists at or before `end_date={endDate}` for the selected organizations and after the earlier snapshot. This can be satisfied by directly inserting later `Contributor` rows with matching contributor ids and organization ids, or by calling `GET /fetch` a second time before `{endDate}`. The second fetch also returns only `"OK"`; the generated `snapshotDate` must be included by the `{endDate}` query value.

Successful execution:
- Result:
  This function returns contributor metric differences between two stored contributor snapshots.
- Invocation:
  Step 1: `GET /contributors` with required query values `organizations={organizations}`, `start_date={startDate}`, and `end_date={endDate}`, plus optional query values `limit={limit}`, `offset={offset}`, `sortBy={sortBy}`, and `q={contributorNamePrefix}`
- Constraints:
  `start_date` and `end_date` must both be supplied and parseable by `DateUtil.iso8601` using `yyyy-MM-dd'T'HH:mm:ss'Z'`. `start_date` must be before `end_date`. The implementation uses the latest snapshot at or before each supplied date. Contributors are matched by contributor id, only ids present in both snapshots are included, and returned counts are end-minus-start deltas aggregated across selected organizations.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A contributor snapshot exists at or before the supplied date that is present. This can be satisfied by directly inserting matching `Contributor` rows or by calling `GET /fetch` before the request.
    - Only one of `start_date={startDate}` or `end_date={endDate}` is supplied.
  - Failure endpoint:
    `GET /contributors`
  - Why this fails:
    The implementation supports either no dates or both dates. A single-date contributor request falls into an unsupported parameter configuration.
  - Intentionally violated constraints:
    The paired date parameter required for delta calculation was omitted.
- Branch 2:
  - Preconditions:
    - An earlier or equal contributor snapshot exists at or before `start_date={startDate}`. This can be satisfied by direct database insertion or by calling `GET /fetch` before `{startDate}`.
    - A later contributor snapshot exists at or before `end_date={endDate}`. This can be satisfied by direct database insertion or by calling `GET /fetch` again before `{endDate}`.
    - The request supplies `start_date={startDate}` and `end_date={endDate}` such that `{startDate}` is equal to or later than `{endDate}`.
  - Failure endpoint:
    `GET /contributors`
  - Why this fails:
    Controller validation requires `iso8601(start_date).before(iso8601(end_date))`.
  - Intentionally violated constraints:
    `start_date` is not before `end_date`.
- Branch 3:
  - Preconditions:
    - A contributor snapshot exists, but no contributor snapshot exists at or before one of the supplied dates. This can be produced by calling `GET /fetch` only after `{startDate}` or `{endDate}`, or by directly inserting contributor rows whose `key.snapshotDate` values are later than one supplied date.
  - Failure endpoint:
    `GET /contributors`
  - Why this fails:
    Validation requires both `repository.findPreviousSnapShotDate(start_date)` and `repository.findPreviousSnapShotDate(end_date)` to be non-null.
  - Intentionally violated constraints:
    `start_date` or `end_date` is earlier than all available contributor snapshots.
- Branch 4:
  - Preconditions:
    - The request supplies `start_date={malformedStartDate}` or `end_date={malformedEndDate}`, and at least one supplied value is not parseable by `DateUtil.iso8601`.
  - Failure endpoint:
    `GET /contributors`
  - Why this fails:
    `DateUtil.iso8601` throws a runtime parse exception during controller validation.
  - Intentionally violated constraints:
    One or both contributor date query values do not match `yyyy-MM-dd'T'HH:mm:ss'Z'`.

Endpoint coverage:
- Covers:
  `GET /contributors`
- Distinct meaning:
  With both date parameters, this endpoint returns contributor activity deltas.

### Function 8: list primary language usage

Function name:
list primary language usage

Core endpoint(s):
- `GET /languages`

Preconditions:
- None. Existing `Project` rows with non-empty `primaryLanguage` values are required only for a non-empty response; they can be inserted directly or created by `GET /fetch`.

Successful execution:
- Result:
  This function returns primary programming languages used by projects in selected organizations, sorted by project count.
- Invocation:
  Step 1: `GET /languages` with optional query values `organizations={organizations}`, `limit={limit}`, `offset={offset}`, and `q={primaryLanguage}`
- Constraints:
  Swagger marks `organizations` as required, but the implementation accepts it as optional. If omitted, `StringParser.parseStringList(null, ",")` returns an empty organization list and no languages are returned. `limit` defaults from `default.item.limit` or 5. `offset` defaults to 0. Swagger describes `q` as a language-name prefix, but the implementation passes it to project lookup as an exact `primaryLanguage` filter.

Failure or exceptional branches:
None specific to this function beyond normal runtime errors such as invalid negative stream pagination.

Endpoint coverage:
- Covers:
  `GET /languages`
- Distinct meaning:
  Returns language usage derived from project `primaryLanguage` fields.

### Function 9: retrieve latest aggregate organization statistics

Function name:
retrieve latest aggregate organization statistics

Core endpoint(s):
- `GET /statistics`

Preconditions:
- None. Existing `Statistics` rows are required only for a non-empty response; they can be inserted directly or created by `GET /fetch`.

Successful execution:
- Result:
  This function returns the latest statistics for selected organizations, aggregated into one statistics object when data exists.
- Invocation:
  Step 1: `GET /statistics` with optional query value `organizations={organizations}`, while omitting `start_date` and `end_date`
- Constraints:
  If `organizations` is omitted, the implementation uses `organization.list`. For each organization, the latest statistics snapshot is loaded. Multiple organizations are aggregated by summing count fields and joining organization names.

Failure or exceptional branches:
None identified from the OpenAPI contract or `src` implementation.

Endpoint coverage:
- Covers:
  `GET /statistics`
- Distinct meaning:
  Without date parameters, this endpoint returns latest organization statistics.

### Function 10: retrieve historical organization statistics

Function name:
retrieve historical organization statistics

Core endpoint(s):
- `GET /statistics`

Preconditions:
- None. Existing `Statistics` rows in the requested date range are required only for a non-empty response; they can be inserted directly or created by `GET /fetch` before the request.

Successful execution:
- Result:
  This function returns organization statistics over a requested time range.
- Invocation:
  Step 1: `GET /statistics` with optional query value `organizations={organizations}` and at least one date query value `start_date={startDate}` or `end_date={endDate}`
- Constraints:
  Dates must be ISO-8601 parseable by `StringParser.parseIso8601Date`. If `start_date` is omitted, the implementation uses the earliest snapshot for each organization. If `start_date` is provided, it uses the latest snapshot at or before that date. If `end_date` is omitted, current time is used. Multiple organizations are aggregated positionally across result lists.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request supplies `start_date={malformedStartDate}` or `end_date={malformedEndDate}`, and at least one supplied date is not parseable by `StringParser.parseIso8601Date`.
  - Failure endpoint:
    `GET /statistics`
  - Why this fails:
    `StatisticsService` throws `IllegalArgumentException`, and `StatisticsApi` maps it to HTTP 400.
  - Intentionally violated constraints:
    A malformed statistics date string is supplied.

Endpoint coverage:
- Covers:
  `GET /statistics`
- Distinct meaning:
  With date parameters, this endpoint returns historical statistics over a time range.

### Function 11: retrieve project statistics time series

Function name:
retrieve project statistics time series

Core endpoint(s):
- `GET /statistics/projects`

Preconditions:
- Project snapshot rows exist in the requested date range and produce at least 10 distinct project-name groups. This can be satisfied by directly inserting `Project` rows with `snapshotDate` values between `start_date={startDate}` and `end_date={endDate}` and matching `organizationName` values, or by calling `GET /fetch`, which stores project rows for the configured `organization.list` with a generated current `snapshotDate`. `GET /fetch` returns `"OK"` and does not expose the generated `snapshotDate`, so the query range must include the generated timestamp.

Successful execution:
- Result:
  This function returns the top 10 project time series, grouped by project name, with arrays of commits, forks, contributors, scores, and snapshot dates.
- Invocation:
  Step 1: `GET /statistics/projects` with optional query values `organizations={organizations}`, `start_date={startDate}`, and `end_date={endDate}`
- Constraints:
  If dates are omitted, the endpoint defaults to the last 30 days through now. If `organizations` is supplied, only those organizations are used; otherwise all projects in the date range are used. The queried range must produce at least 10 project groups because the implementation returns `result.subList(0, 10)`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Fewer than 10 project groups are available after organization and date filtering. This can be produced by starting from an empty project table, inserting fewer than 10 distinct project-name groups, filtering too narrowly, or intentionally not calling `GET /fetch` to populate enough project snapshot state.
  - Failure endpoint:
    `GET /statistics/projects`
  - Why this fails:
    The implementation calls `subList(0, 10)` even when fewer than ten `ProjectStats` objects exist.
  - Intentionally violated constraints:
    Required project snapshot state was omitted or filtered below 10 project groups.
- Branch 2:
  - Preconditions:
    - The request supplies `start_date={malformedStartDate}` or `end_date={malformedEndDate}`, and at least one supplied date is not parseable by `StringParser.parseIso8601Date`.
  - Failure endpoint:
    `GET /statistics/projects`
  - Why this fails:
    `parseDate` throws `IllegalArgumentException`, and `StatisticsApi` maps it to HTTP 400.
  - Intentionally violated constraints:
    A malformed project-statistics date string is supplied.

Endpoint coverage:
- Covers:
  `GET /statistics/projects`
- Distinct meaning:
  Returns compact project metric histories rather than raw project rows.

### Function 12: retrieve contributor statistics time series

Function name:
retrieve contributor statistics time series

Core endpoint(s):
- `GET /statistics/contributors`

Preconditions:
- Contributor snapshot rows exist in the requested date range and produce at least 10 distinct contributor groups. This can be satisfied by directly inserting `Contributor` rows with `key.snapshotDate` values between `start_date={startDate}` and `end_date={endDate}`, `organizationName` values matching the selected organizations, and `url` values beginning with `https://github.com/`, or by calling `GET /fetch`, which stores contributor rows for the configured `organization.list` with a generated current `snapshotDate`. `GET /fetch` returns `"OK"` and does not expose the generated `snapshotDate`, so the query range must include the generated timestamp.

Successful execution:
- Result:
  This function returns the top 10 contributor time series, grouped by GitHub login, with arrays of commit counts, project counts, organizations, and snapshot dates.
- Invocation:
  Step 1: `GET /statistics/contributors` with optional query values `organizations={organizations}`, `start_date={startDate}`, and `end_date={endDate}`
- Constraints:
  If dates are omitted, the endpoint defaults to the last 30 days through now. If `organizations` is supplied, only those organizations are used; otherwise all contributors in the date range are used. The result must contain at least 10 contributor groups because the implementation returns `result.subList(0, 10)`. Contributor URLs must start with `https://github.com/` so a login id can be derived.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - Fewer than 10 contributor groups are available after organization and date filtering. This can be produced by starting from an empty contributor table, inserting fewer than 10 distinct contributor groups, filtering too narrowly, or intentionally not calling `GET /fetch` to populate enough contributor snapshot state.
  - Failure endpoint:
    `GET /statistics/contributors`
  - Why this fails:
    The implementation calls `subList(0, 10)` even when fewer than ten `ContributorStats` objects exist.
  - Intentionally violated constraints:
    Required contributor snapshot state was omitted or filtered below 10 contributor groups.
- Branch 2:
  - Preconditions:
    - The request supplies `start_date={malformedStartDate}` or `end_date={malformedEndDate}`, and at least one supplied date is not parseable by `StringParser.parseIso8601Date`.
  - Failure endpoint:
    `GET /statistics/contributors`
  - Why this fails:
    `parseDate` throws `IllegalArgumentException`, and `StatisticsApi` maps it to HTTP 400.
  - Intentionally violated constraints:
    A malformed contributor-statistics date string is supplied.

Endpoint coverage:
- Covers:
  `GET /statistics/contributors`
- Distinct meaning:
  Returns compact contributor histories rather than raw contributor rows.

### Function 13: retrieve language statistics time series

Function name:
retrieve language statistics time series

Core endpoint(s):
- `GET /statistics/languages`

Preconditions:
- None. Existing `Project` rows in the requested date range are required only for a non-empty response; they can be inserted directly or created by `GET /fetch`.

Successful execution:
- Result:
  This function returns language project-count time series over project snapshots.
- Invocation:
  Step 1: `GET /statistics/languages` with optional query values `organizations={organizations}`, `start_date={startDate}`, and `end_date={endDate}`
- Constraints:
  If dates are omitted, the endpoint defaults to the last 30 days through now. If `organizations` is omitted, the implementation uses `organization.list`. `start_date` and `end_date` must be parseable if supplied. Projects with `null` primary language are grouped as `unknown`. Duplicate snapshots are filtered by language, project name, organization, and snapshot date.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request supplies `start_date={malformedStartDate}` or `end_date={malformedEndDate}`, and at least one supplied date is not parseable by `StringParser.parseIso8601Date`.
  - Failure endpoint:
    `GET /statistics/languages`
  - Why this fails:
    `parseDate` throws `IllegalArgumentException`, and `StatisticsApi` maps it to HTTP 400.
  - Intentionally violated constraints:
    A malformed language-statistics date string is supplied.

Endpoint coverage:
- Covers:
  `GET /statistics/languages`
- Distinct meaning:
  Returns language histories derived from project snapshots.

### Function 14: export database contents

Function name:
export database contents

Core endpoint(s):
- `GET /export`

Preconditions:
- None.

Successful execution:
- Result:
  This function exports all stored contributors, projects, and statistics as a `DatabaseDto`.
- Invocation:
  Step 1: `GET /export` with no path, query, body, form, or header values
- Constraints:
  The response contains three arrays: `contributors`, `projects`, and `statistics`. Empty arrays are valid if no data exists.

Failure or exceptional branches:
None identified from the OpenAPI contract or `src` implementation.

Endpoint coverage:
- Covers:
  `GET /export`
- Distinct meaning:
  Read-only database export.

### Function 15: reject project scoring reconfiguration

Function name:
reject project scoring reconfiguration

Core endpoint(s):
- `POST /config/scoring.project`

Preconditions:
- None.

Successful execution:
- Result:
  This function rejects attempts to update the project scoring script.
- Invocation:
  Step 1: `POST /config/scoring.project` with optional body value `scoringProject={scoringProject}` and optional header `X-Organizations={organizations}`
- Constraints:
  Swagger documents possible 200 and 201 responses with a string-array schema, but the implementation always throws `UnsupportedOperationException`. `AdminController` maps that exception to HTTP 403 with message `"This endpoint is deactivated."` The body and header are ignored by the implemented controller logic.

Failure or exceptional branches:
None separate. Rejection is the implemented endpoint result.

Endpoint coverage:
- Covers:
  `POST /config/scoring.project`
- Distinct meaning:
  Disabled administrative scoring-configuration endpoint.

### Function 16: reject database initialization

Function name:
reject database initialization

Core endpoint(s):
- `GET /init`

Preconditions:
- None.

Successful execution:
- Result:
  This function rejects attempts to initialize or populate the database through the API.
- Invocation:
  Step 1: `GET /init` with no path, query, body, form, or header values
- Constraints:
  Swagger documents a 200 response, but the implementation always throws `UnsupportedOperationException`, which `AdminController` maps to HTTP 403. `OAuthConfiguration` also restricts `GET /init/**` to requests with OAuth scope `uid`; if the security filter applies first, missing authorization can prevent the request from reaching the controller-level deactivation response.

Failure or exceptional branches:
None separate. Rejection is the implemented endpoint result.

Endpoint coverage:
- Covers:
  `GET /init`
- Distinct meaning:
  Disabled administrative initialization endpoint.

### Function 17: reject database deletion

Function name:
reject database deletion

Core endpoint(s):
- `GET /delete`

Preconditions:
- None.

Successful execution:
- Result:
  This function rejects attempts to delete all stored data through the API.
- Invocation:
  Step 1: `GET /delete` with no path, query, body, form, or header values
- Constraints:
  Swagger documents a 200 response, but the implementation always throws `UnsupportedOperationException`, which `AdminController` maps to HTTP 403. `OAuthConfiguration` also restricts `GET /delete/**` to requests with OAuth scope `uid`; if the security filter applies first, missing authorization can prevent the request from reaching the controller-level deactivation response.

Failure or exceptional branches:
None separate. Rejection is the implemented endpoint result.

Endpoint coverage:
- Covers:
  `GET /delete`
- Distinct meaning:
  Disabled administrative delete-all endpoint.

### Function 18: reject database import

Function name:
reject database import

Core endpoint(s):
- `POST /import`

Preconditions:
- None.

Successful execution:
- Result:
  This function rejects attempts to import contributors, projects, and statistics.
- Invocation:
  Step 1: `POST /import` with body value `DatabaseDto={databaseDto}`
- Constraints:
  Swagger documents 200 and 201 responses and a `DatabaseDto` body, but the implementation always throws `UnsupportedOperationException`, which `AdminController` maps to HTTP 403. The submitted body is not saved.

Failure or exceptional branches:
None separate. Rejection is the implemented endpoint result.

Endpoint coverage:
- Covers:
  `POST /import`
- Distinct meaning:
  Disabled administrative database import endpoint.

### Function 19: check service health

Function name:
check service health

Core endpoint(s):
- `GET /health`
- `GET /health.json`

Preconditions:
- None.

Successful execution:
- Result:
  This function returns Spring Boot actuator health information.
- Invocation:
  Step 1: `GET /health` with no path, query, body, form, or header values, or `GET /health.json` with no path, query, body, form, or header values
- Constraints:
  The project properties set `endpoints.enabled=false` and `endpoints.health.enabled=true`, so health is the explicitly enabled actuator endpoint. The endpoint implementation is provided by Spring Boot actuator rather than a controller in project `src`, and it is documented in Swagger.

Failure or exceptional branches:
None identified from project source.

Endpoint coverage:
- Covers:
  `GET /health`
- Distinct meaning:
  Health check endpoint.
- Covers:
  `GET /health.json`
- Distinct meaning:
  Alternate health check path.

### Function 20: render framework error response

Function name:
render framework error response

Core endpoint(s):
- `GET /error`
- `HEAD /error`
- `POST /error`
- `PUT /error`
- `DELETE /error`
- `OPTIONS /error`
- `PATCH /error`

Preconditions:
- A request is dispatched to Spring Boot's `/error` endpoint after an error or by directly requesting `/error`. This state is provided by the Spring Boot web framework; no project-specific setup endpoint establishes it, and direct database setup is irrelevant.

Successful execution:
- Result:
  This function returns the framework error model or response for the current request.
- Invocation:
  Step 1: `{HTTP_METHOD} /error` using one of `GET`, `HEAD`, `POST`, `PUT`, `DELETE`, `OPTIONS`, or `PATCH`
- Constraints:
  These endpoints are Swagger-generated entries for Spring Boot `BasicErrorController`, not project-specific REST controller methods in `src`. Their exact response depends on the framework error attributes and the original failing request.

Failure or exceptional branches:
None identified from project source.

Endpoint coverage:
- Covers:
  `GET /error`
- Distinct meaning:
  Framework error response endpoint for GET dispatches.
- Covers:
  `HEAD /error`
- Distinct meaning:
  Framework error response endpoint for HEAD dispatches.
- Covers:
  `POST /error`
- Distinct meaning:
  Framework error response endpoint for POST dispatches.
- Covers:
  `PUT /error`
- Distinct meaning:
  Framework error response endpoint for PUT dispatches.
- Covers:
  `DELETE /error`
- Distinct meaning:
  Framework error response endpoint for DELETE dispatches.
- Covers:
  `OPTIONS /error`
- Distinct meaning:
  Framework error response endpoint for OPTIONS dispatches.
- Covers:
  `PATCH /error`
- Distinct meaning:
  Framework error response endpoint for PATCH dispatches.
