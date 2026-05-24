# Domain-Level Behavior Analysis

## Domain Summary

CatWatch is a GitHub organization analytics service. It collects snapshot data from configured GitHub organizations and exposes read-oriented analytics for projects, contributors, programming languages, and organization-level statistics. The main business resources are organization snapshots, project snapshots, contributor snapshots, aggregate statistics, language summaries, and exported database contents.

The service is primarily snapshot-based. `GET /fetch` creates new snapshot state from GitHub, and most domain reads either return the latest snapshot, a snapshot at or before a requested date, or deltas/time series between stored snapshots. Administrative write endpoints exist in Swagger but are intentionally disabled in implementation.

## Available Function Inventory

### Configuration and Operations

- `expose public runtime configuration`
  - `GET /config`
  - Returns public runtime properties listed in `application-public.properties`.

- `check service health`
  - `GET /health`, `GET /health.json`
  - Returns Spring Boot actuator health information.

- `render framework error response`
  - `GET|HEAD|POST|PUT|DELETE|OPTIONS|PATCH /error`
  - Returns Spring Boot framework error output.

### Snapshot Collection

- `trigger GitHub data fetch`
  - `GET /fetch`
  - Fetches configured GitHub organizations and persists project, contributor, and statistics snapshots.

### Projects

- `list latest projects`
  - `GET /projects`
  - Lists latest project snapshots, with organization, pagination, prefix, language, and sort filters.

- `list projects as of an end date`
  - `GET /projects`
  - Lists project snapshots from the latest stored snapshot at or before `end_date`.

- `list project metric deltas over time`
  - `GET /projects`
  - Returns project metric differences between start and end snapshots.

### Contributors

- `list current top contributors`
  - `GET /contributors`
  - Lists current contributor metrics aggregated across selected organizations.

- `list contributor activity deltas over time`
  - `GET /contributors`
  - Returns contributor metric differences between two stored snapshots.

### Languages

- `list primary language usage`
  - `GET /languages`
  - Returns current primary-language usage derived from latest project snapshots.

### Organization Statistics

- `retrieve latest aggregate organization statistics`
  - `GET /statistics`
  - Returns latest aggregate statistics for selected organizations.

- `retrieve historical organization statistics`
  - `GET /statistics`
  - Returns statistics records over a requested date range.

- `retrieve project statistics time series`
  - `GET /statistics/projects`
  - Returns top 10 project metric time series.

- `retrieve contributor statistics time series`
  - `GET /statistics/contributors`
  - Returns top 10 contributor activity time series.

- `retrieve language statistics time series`
  - `GET /statistics/languages`
  - Returns language project-count time series.

### Database Administration

- `export database contents`
  - `GET /export`
  - Exports all contributors, projects, and statistics.

- `reject project scoring reconfiguration`
  - `POST /config/scoring.project`
  - Always rejects scoring-script updates.

- `reject database initialization`
  - `GET /init`
  - Always rejects API database initialization.

- `reject database deletion`
  - `GET /delete`
  - Always rejects API database deletion.

- `reject database import`
  - `POST /import`
  - Always rejects API database import.

## Supported Business Behaviors

### Behavior 1: Publish Public Runtime Configuration

Business goal:
Expose public client-facing configuration values.

Domain context:
Frontend or integration clients need to discover safe runtime settings without reading server files.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `expose public runtime configuration` (`GET /config`) with no path, query, body, form, or header values to retrieve the public configuration map.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. The behavior is already a read-only runtime operation.
- Direct database setup is irrelevant because values come from the Spring `Environment`.

Parameter and value bindings:
- Property names are bound to `src/main/resources/application-public.properties`.
- Returned values are looked up by the same property names in the active runtime environment.

Business result:
A sorted map of public configuration keys and current runtime values is returned.

Constraints and invariants:
- Only property names listed in `application-public.properties` are exposed.
- Missing environment values may appear as null values depending on active configuration.

Failure and exceptional cases:
- Failing function: `expose public runtime configuration`
  - Failure condition: `application-public.properties` cannot be loaded from the classpath.
  - Why it fails: the controller loads that resource directly before building the result map.
  - Violated prerequisite or constraint: required configuration resource is unavailable.

Implementation notes:
Swagger and implementation agree on the endpoint purpose.

### Behavior 2: Capture a New GitHub Organization Snapshot

Business goal:
Refresh the analytics database with current GitHub organization data.

Domain context:
Projects, contributors, languages, and statistics are derived from point-in-time snapshots. This is the only supported API behavior that creates analytics data.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `trigger GitHub data fetch` (`GET /fetch`) with no request values to collect all organizations configured in `organization.list` and persist the generated `snapshotDate` for statistics, project, and contributor records.

Optional verification workflow:
1. Use function `export database contents` (`GET /export`) with no request values to inspect saved `projects`, `contributors`, and `statistics`.
2. Use function `retrieve latest aggregate organization statistics` (`GET /statistics`) with `organizations={configuredOrganizationNames}` to inspect the latest aggregate statistics.

Existing-state shortcuts:
- If equivalent project, contributor, and statistics snapshot rows already exist, skip `trigger GitHub data fetch` for downstream read behaviors.
- Direct database setup can substitute for downstream reads, but it is not equivalent to this behavior because this behavior specifically collects fresh GitHub state.
- Generated `snapshotDate`, organization names, GitHub organization ids, GitHub project ids, and contributor ids must remain internally consistent.

Parameter and value bindings:
- `organization.list` supplies the organization names consumed by the fetcher.
- A generated `snapshotDate` is reused across all records created for the same fetch cycle.
- GitHub organization id scopes statistics and contributor keys.
- GitHub project id links project snapshots across time.

Business result:
New snapshot rows exist for configured organizations: organization statistics, project snapshots, and contributor snapshots. Language rows are collected in memory but not persisted because the save call is commented out.

Constraints and invariants:
- Fetch depends on GitHub access, configured organizations, cache/network availability, and database repositories.
- The response is only `"OK"` and does not expose the generated `snapshotDate`.
- Retryable crawler failures are retried; after recovery mail handling, the controller can still return `"OK"` even if no new snapshot was saved.

Failure and exceptional cases:
- Failing function: `trigger GitHub data fetch`
  - Failure condition: GitHub access, credentials, network, snapshot execution, or persistence fails repeatedly.
  - Why it fails: `RetryableFetcher` retries `CrawlerRetryException`; recovery sends mail instead of producing snapshot records.
  - Violated prerequisite or constraint: external snapshot collection did not complete.

Implementation notes:
The implementation uses current time as `snapshotDate`. It saves statistics, projects, and contributors, but not the collected language objects.

### Behavior 3: Browse the Current Project Portfolio

Business goal:
Show the latest known project portfolio for one or more GitHub organizations.

Domain context:
This is the main catalog view for repositories, including names, URLs, descriptions, stars, forks, commits, contributors, primary language, score, maintainers, title, and image metadata.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `trigger GitHub data fetch` (`GET /fetch`) with no request values to create latest project snapshot rows for configured organizations.
2. Use function `list latest projects` (`GET /projects`) with `organizations={organizationNameCsv}`, optional `limit={positiveInteger}`, `offset={nonNegativeInteger}`, `sortBy={stars|commits|forks|contributors|score|-stars|-commits|-forks|-contributors|-score}`, `q={projectNamePrefix}`, and `language={primaryLanguage}` to retrieve the current project portfolio.

Optional verification workflow:
1. Use function `retrieve latest aggregate organization statistics` (`GET /statistics`) with `organizations={sameOrganizationNameCsv}` to inspect organization-level project counts.
2. Use function `list primary language usage` (`GET /languages`) with `organizations={sameOrganizationNameCsv}` to inspect language distribution.

Existing-state shortcuts:
- Skip `trigger GitHub data fetch` if latest project rows already exist for the selected organizations.
- Direct database setup can insert `Project` rows with matching `organizationName` and latest `snapshotDate`.
- The same organization names used in `GET /projects` must match stored `Project.organizationName` values.

Parameter and value bindings:
- `organizations` scopes the project lookup to matching `organizationName`.
- `q` is consumed as a project-name prefix.
- `language` is consumed as an exact `primaryLanguage`.
- `sortBy` controls ordering; `-` means descending.
- `limit` and `offset` page the sorted result.

Business result:
The client receives the latest project snapshot rows matching the requested organizations and filters. No state is changed by the listing step.

Constraints and invariants:
- If `organizations` is omitted, implementation falls back to `organization.list`.
- Default sorting is descending score.
- Invalid project `sortBy` values do not fail; the implementation silently falls back to score sorting.
- Negative `offset` or non-positive `limit` are not explicitly validated in the project API and may produce empty or surprising stream behavior.

Failure and exceptional cases:
- Failing function: `trigger GitHub data fetch`
  - Failure condition: configured GitHub snapshot collection cannot complete.
  - Why it fails: external collection or persistence fails inside the fetcher.
  - Violated prerequisite or constraint: no project snapshot state is created.
- Failing function: `list latest projects`
  - Failure condition: no matching projects exist.
  - Why it fails: this is not an error; the implementation returns an empty list.
  - Violated prerequisite or constraint: no business data exists for the selected organization/filter.

Implementation notes:
Project rows use a generated database `id`, but project deltas are matched by `gitHubProjectId`, not by database id.

### Behavior 4: Browse Projects as of a Historical Cutoff

Business goal:
View the project portfolio as it existed at or before a requested date.

Domain context:
Users can inspect historical repository state without needing the exact snapshot timestamp.

Starting point:
No prior service state for a current-cutoff scenario.

Required execution workflow:
1. Use function `trigger GitHub data fetch` (`GET /fetch`) with no request values to create project snapshot rows.
2. Use function `list projects as of an end date` (`GET /projects`) with `end_date={yyyy-MM-dd'T'HH:mm:ss value at or after the generated snapshotDate}`, optional `organizations={organizationNameCsv}`, `limit={positiveInteger}`, `offset={nonNegativeInteger}`, `sortBy={sortField}`, `q={projectNamePrefix}`, and `language={primaryLanguage}` to retrieve the latest project snapshot at or before the cutoff.

Optional verification workflow:
1. Use function `export database contents` (`GET /export`) with no request values to inspect stored project `snapshotDate` values.
2. Use function `retrieve historical organization statistics` (`GET /statistics`) with `organizations={sameOrganizationNameCsv}` and `end_date={sameCutoffDate}` to compare aggregate statistics.

Existing-state shortcuts:
- Skip `trigger GitHub data fetch` if project rows already exist with `snapshotDate <= end_date`.
- Direct database setup can insert historical `Project` rows for the target `organizationName`.
- `end_date` must be after or equal to the stored snapshot to select it.

Parameter and value bindings:
- `end_date` is compared against stored `Project.snapshotDate`.
- `organizations` must match stored `Project.organizationName`.
- `q`, `language`, `sortBy`, `limit`, and `offset` bind exactly as in latest project listing.

Business result:
The client receives project rows from the latest stored snapshot not later than the supplied cutoff date.

Constraints and invariants:
- Project date binding uses `yyyy-MM-dd'T'HH:mm:ss`, without the trailing `Z` required by contributor date parsing.
- If no snapshot exists at or before `end_date`, the implementation returns an empty list for that organization.

Failure and exceptional cases:
- Failing function: `list projects as of an end date`
  - Failure condition: `end_date={malformedEndDate}`.
  - Why it fails: Spring request binding cannot parse the controller date pattern.
  - Violated prerequisite or constraint: `end_date` does not match `yyyy-MM-dd'T'HH:mm:ss`.
- Failing function: `trigger GitHub data fetch`
  - Failure condition: GitHub collection fails.
  - Why it fails: snapshot rows are not persisted.
  - Violated prerequisite or constraint: required historical project state is absent.

Implementation notes:
The endpoint selects the closest previous snapshot per organization, not an exact timestamp match.

### Behavior 5: Measure Project Metric Change Over Time

Business goal:
Report how project metrics changed between two snapshots.

Domain context:
This supports trend analysis for stars, commits, forks, contributors, and score.

Starting point:
No prior service state, with two API-created snapshots.

Required execution workflow:
1. Use function `trigger GitHub data fetch` (`GET /fetch`) with no request values to create the first snapshot.
2. Use function `export database contents` (`GET /export`) with no request values to capture `firstSnapshotDate` from a saved project row for `organizationName={organizationName}`.
3. Use function `trigger GitHub data fetch` (`GET /fetch`) again at a later time to create the second snapshot.
4. Use function `list project metric deltas over time` (`GET /projects`) with `start_date={firstSnapshotDate formatted as yyyy-MM-dd'T'HH:mm:ss}`, optional `end_date={secondSnapshotDate formatted as yyyy-MM-dd'T'HH:mm:ss or omitted to use latest}`, `organizations={organizationNameCsv}`, `limit={positiveInteger}`, `offset={nonNegativeInteger}`, `sortBy={sortField}`, `q={projectNamePrefix}`, and `language={primaryLanguage}` to retrieve metric deltas.

Optional verification workflow:
1. Use function `export database contents` (`GET /export`) to inspect both project snapshots and confirm the same `gitHubProjectId` appears in both.
2. Use function `retrieve project statistics time series` (`GET /statistics/projects`) with `organizations={sameOrganizationNameCsv}`, `start_date={firstSnapshotDate}`, and `end_date={secondSnapshotDate}` to inspect time-series shape when at least 10 project groups exist.

Existing-state shortcuts:
- Skip either fetch if equivalent first and second project snapshots already exist.
- Direct database setup can insert project rows with matching `gitHubProjectId`, `organizationName`, and ordered `snapshotDate` values.
- Export can be skipped if the caller already knows usable snapshot dates.

Parameter and value bindings:
- `start_date` must select the earlier project snapshot.
- `end_date` must select the later project snapshot, or be omitted to select latest.
- Project rows are matched by the same `gitHubProjectId`.
- For projects present in both snapshots, counts become `end - start`.
- Projects present only at the end are returned with their end values unchanged.

Business result:
The returned project records represent metric deltas rather than raw totals for stars, commits, forks, contributors, and score.

Constraints and invariants:
- The implementation mutates the end `Project` objects in memory when computing deltas.
- Removed projects are not represented because only projects present in the end snapshot are iterated.
- `externalContributorsCount` is not delta-adjusted.
- No validation enforces that `start_date` is before `end_date`.

Failure and exceptional cases:
- Failing function: `list project metric deltas over time`
  - Failure condition: malformed `start_date` or `end_date`.
  - Why it fails: Spring date binding fails before service execution.
  - Violated prerequisite or constraint: date does not match `yyyy-MM-dd'T'HH:mm:ss`.
- Failing function: `list project metric deltas over time`
  - Failure condition: no start snapshot exists at or before `start_date`.
  - Why it fails: the repository returns an empty start list; the service treats all end projects as new.
  - Violated prerequisite or constraint: expected baseline snapshot is absent, but the implementation does not fail.
- Failing function: `trigger GitHub data fetch`
  - Failure condition: second fetch does not persist a later snapshot.
  - Why it fails: latest end side may equal the first snapshot, producing zero or stale deltas.
  - Violated prerequisite or constraint: no later comparison state exists.

Implementation notes:
Because `GET /fetch` returns only `"OK"`, `GET /export` is the only API-visible way to discover generated snapshot dates.

### Behavior 6: Rank Current Contributors

Business goal:
Show the current top contributors for selected organizations.

Domain context:
The service measures organizational commits, organizational projects, personal projects, and contributor identity across GitHub organizations.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `trigger GitHub data fetch` (`GET /fetch`) with no request values to create contributor snapshot rows.
2. Use function `list current top contributors` (`GET /contributors`) with required `organizations={organizationNameCsv}`, optional `limit={positiveInteger}`, `offset={nonNegativeInteger}`, `sortBy={organizationalCommitsCount|organizationalProjectsCount|personalCommitsCount|personalProjectsCount|organizationName|name}` optionally prefixed by `-`, and `q={contributorNamePrefix}` to retrieve current contributors.

Optional verification workflow:
1. Use function `export database contents` (`GET /export`) with no request values to inspect contributor rows.
2. Use function `retrieve contributor statistics time series` (`GET /statistics/contributors`) with `organizations={sameOrganizationNameCsv}` when at least 10 contributor groups exist.

Existing-state shortcuts:
- Skip `trigger GitHub data fetch` if contributor rows already exist at or before request time.
- Direct database setup can insert `Contributor` rows with matching `organizationName`, `organizationId`, `id`, and `snapshotDate`.
- `organizations` must map to known organization ids through existing contributor rows.

Parameter and value bindings:
- `organizations` is mapped to `organizationId` by looking up contributor rows with matching `organizationName`.
- Contributor aggregation groups by contributor `id`.
- `q` is applied as a contributor-name prefix.
- `sortBy`, `limit`, and `offset` control the final aggregated list.

Business result:
The client receives a sorted, paginated list of contributor metrics aggregated across selected organizations.

Constraints and invariants:
- Swagger and controller require `organizations`; internal helper can fall back to `organization.list` only if it receives null or empty, but request binding normally requires the query parameter.
- `offset >= 0` and `limit > 0` are enforced.
- Unknown organization names map to `-1`, not null, so the intended validation does not reject them; the query simply returns no contributors for organization id `-1`.
- `personalCommitsCount` is not populated by the fetcher and may remain null.

Failure and exceptional cases:
- Failing function: `list current top contributors`
  - Failure condition: no contributor snapshot exists at or before request time.
  - Why it fails: `repository.findPreviousSnapShotDate(now)` returns null and the controller throws `UnsupportedOperationException`.
  - Violated prerequisite or constraint: contributor snapshot state is missing.
- Failing function: `list current top contributors`
  - Failure condition: invalid `offset`, `limit`, or `sortBy`.
  - Why it fails: controller validation rejects negative offset, non-positive limit, or unrecognized sort field.
  - Violated prerequisite or constraint: pagination or sorting constraint is violated.
- Failing function: `trigger GitHub data fetch`
  - Failure condition: GitHub contributor collection fails.
  - Why it fails: no contributor rows are persisted.
  - Violated prerequisite or constraint: contributor snapshot state is absent.

Implementation notes:
Contributor date parsing and validation are implemented manually and differ from project date binding.

### Behavior 7: Measure Contributor Activity Change Over Time

Business goal:
Compare contributor activity between two snapshots.

Domain context:
This supports measuring contribution growth or decline across organizational commits and projects.

Starting point:
No prior service state, with two API-created snapshots.

Required execution workflow:
1. Use function `trigger GitHub data fetch` (`GET /fetch`) with no request values to create the first contributor snapshot.
2. Use function `export database contents` (`GET /export`) with no request values to capture `firstSnapshotDate` from contributor rows.
3. Use function `trigger GitHub data fetch` (`GET /fetch`) again later to create the second contributor snapshot.
4. Use function `export database contents` (`GET /export`) with no request values if `secondSnapshotDate` is needed explicitly.
5. Use function `list contributor activity deltas over time` (`GET /contributors`) with required `organizations={organizationNameCsv}`, `start_date={firstSnapshotDate formatted as yyyy-MM-dd'T'HH:mm:ss'Z'}`, `end_date={secondSnapshotDate formatted as yyyy-MM-dd'T'HH:mm:ss'Z'}`, optional `limit={positiveInteger}`, `offset={nonNegativeInteger}`, `sortBy={contributorSortField}`, and `q={contributorNamePrefix}`.

Optional verification workflow:
1. Use function `retrieve contributor statistics time series` (`GET /statistics/contributors`) with `organizations={sameOrganizationNameCsv}`, `start_date={firstSnapshotDate}`, and `end_date={secondSnapshotDate}` if at least 10 contributor groups exist.

Existing-state shortcuts:
- Skip fetches if equivalent earlier and later contributor snapshots already exist.
- Direct database setup can insert contributor rows with matching contributor `id`, `organizationId`, `organizationName`, and ordered `snapshotDate` values.
- Export can be skipped if the caller already knows valid snapshot dates.

Parameter and value bindings:
- `start_date` and `end_date` must both be supplied and must select stored contributor snapshots.
- Contributor rows are matched by contributor `id`.
- Organization names are converted to organization ids before contributor lookup.
- Only contributors present in both snapshots are included.

Business result:
Returned contributor records contain delta values for organizational commits, organizational projects, personal commits, and personal projects where both sides are non-null.

Constraints and invariants:
- `start_date` must be before `end_date`.
- Both dates must parse as `yyyy-MM-dd'T'HH:mm:ss'Z'`.
- New contributors present only in the end snapshot are omitted.
- Null metric values remain null in subtraction.

Failure and exceptional cases:
- Failing function: `list contributor activity deltas over time`
  - Failure condition: only one of `start_date` or `end_date` is supplied.
  - Why it fails: contributor API supports either no dates or both dates.
  - Violated prerequisite or constraint: paired date requirement is violated.
- Failing function: `list contributor activity deltas over time`
  - Failure condition: `start_date` is equal to or later than `end_date`.
  - Why it fails: controller validation requires `startDate.before(endDate)`.
  - Violated prerequisite or constraint: chronological ordering is violated.
- Failing function: `list contributor activity deltas over time`
  - Failure condition: no snapshot exists at or before one supplied date.
  - Why it fails: validation requires `findPreviousSnapShotDate` to return non-null for both dates.
  - Violated prerequisite or constraint: baseline or comparison snapshot is missing.
- Failing function: `list contributor activity deltas over time`
  - Failure condition: malformed date string.
  - Why it fails: `DateUtil.iso8601` throws during validation.
  - Violated prerequisite or constraint: date format is invalid.

Implementation notes:
The contributor delta API is stricter than the project delta API: it validates both dates and rejects unsupported date combinations.

### Behavior 8: Analyze Current Primary Language Usage

Business goal:
Show which primary programming languages dominate the selected organization projects.

Domain context:
Language distribution helps understand the technology portfolio of organizations.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `trigger GitHub data fetch` (`GET /fetch`) with no request values to create project snapshot rows with `primaryLanguage`.
2. Use function `list primary language usage` (`GET /languages`) with optional `organizations={organizationNameCsv}`, `limit={positiveInteger}`, `offset={nonNegativeInteger}`, and `q={primaryLanguage}` to retrieve language counts.

Optional verification workflow:
1. Use function `list latest projects` (`GET /projects`) with `organizations={sameOrganizationNameCsv}` and `language={samePrimaryLanguage}` to inspect projects behind a language count.

Existing-state shortcuts:
- Skip `trigger GitHub data fetch` if latest project rows already exist with non-empty `primaryLanguage`.
- Direct database setup can insert `Project` rows with matching `organizationName`, latest `snapshotDate`, and `primaryLanguage`.
- The `organizations` values must match stored project organization names.

Parameter and value bindings:
- `organizations` scopes project lookup.
- `q` is passed to project lookup as an exact `primaryLanguage` filter, despite Swagger describing it as a prefix.
- Counts are derived from latest projects returned by repository lookup.

Business result:
The client receives language names, project counts, and integer percentages among projects with non-empty primary language.

Constraints and invariants:
- Projects with empty primary language are ignored.
- If no language-bearing projects exist, the result is empty.
- If `organizations` is omitted, implementation parses null to an empty organization list and returns no languages; it does not fall back to configured organizations here.
- Negative pagination is not explicitly validated.

Failure and exceptional cases:
- Failing function: `trigger GitHub data fetch`
  - Failure condition: GitHub project collection fails.
  - Why it fails: no source project rows are created.
  - Violated prerequisite or constraint: language derivation has no project state.
- Failing function: `list primary language usage`
  - Failure condition: `offset` is larger than result size.
  - Why it fails: this is not an error; stream pagination returns an empty list.
  - Violated prerequisite or constraint: requested page has no data.

Implementation notes:
Collected `Language` objects from GitHub snapshots are not persisted. The language endpoint derives language usage from `Project.primaryLanguage`.

### Behavior 9: Retrieve Latest Organization KPI Summary

Business goal:
Show the latest aggregate KPI totals for one or more organizations.

Domain context:
Organization-level statistics summarize public/private projects, members, teams, contributors, stars, forks, repository size, languages, tags, and external contributors.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `trigger GitHub data fetch` (`GET /fetch`) with no request values to create statistics rows.
2. Use function `retrieve latest aggregate organization statistics` (`GET /statistics`) with optional `organizations={organizationNameCsv}` while omitting `start_date` and `end_date`.

Optional verification workflow:
1. Use function `export database contents` (`GET /export`) with no request values to inspect raw `statistics` rows.

Existing-state shortcuts:
- Skip `trigger GitHub data fetch` if latest `Statistics` rows already exist for selected organizations.
- Direct database setup can insert statistics rows with matching `organizationName` and `snapshotDate`.
- If multiple organizations are requested, each should have a latest statistics row for meaningful aggregation.

Parameter and value bindings:
- `organizations` selects statistics by `organizationName`; if omitted, implementation uses `organization.list`.
- Multiple organization rows are aggregated by summing count fields.
- Aggregated `organizationName` joins organization names and `snapshotDate` becomes the newest snapshot date among aggregated rows.

Business result:
The client receives zero or one aggregate statistics object for latest organization KPIs.

Constraints and invariants:
- Missing organizations are silently ignored.
- Aggregated statistics use a random generated id in memory.
- If no statistics rows exist, the result is an empty collection, not an error.

Failure and exceptional cases:
- Failing function: `trigger GitHub data fetch`
  - Failure condition: statistics collection fails.
  - Why it fails: `TakeSnapshotTask.collectStatistics` cannot complete or persistence fails.
  - Violated prerequisite or constraint: statistics snapshot state is absent.

Implementation notes:
The implementation prioritizes latest statistics per organization, then aggregates across organizations.

### Behavior 10: Retrieve Historical Organization KPI Series

Business goal:
View organization KPI records across a date range.

Domain context:
This supports historical reporting for organization-level metrics.

Starting point:
No prior service state, with at least one snapshot.

Required execution workflow:
1. Use function `trigger GitHub data fetch` (`GET /fetch`) with no request values to create a statistics snapshot.
2. Use function `export database contents` (`GET /export`) with no request values if exact generated dates are needed.
3. Use function `retrieve historical organization statistics` (`GET /statistics`) with optional `organizations={organizationNameCsv}`, and at least one of `start_date={iso8601Date}` or `end_date={iso8601Date}` to retrieve statistics in the range.

Optional verification workflow:
1. Use function `retrieve latest aggregate organization statistics` (`GET /statistics`) with `organizations={sameOrganizationNameCsv}` and no dates to compare latest values.

Existing-state shortcuts:
- Skip `trigger GitHub data fetch` if statistics rows already exist in the requested range.
- Direct database setup can insert `Statistics` rows with matching organization names and ordered snapshot dates.
- Export can be skipped when valid snapshot dates are already known.

Parameter and value bindings:
- `start_date` is converted to the latest statistics snapshot at or before that date.
- If `start_date` is omitted, the earliest snapshot per organization is used.
- If `end_date` is omitted, current time is used.
- `organizations` controls which organization statistic lists are collected.

Business result:
The client receives statistics rows over the selected period, either raw for one organization or positionally aggregated for multiple organizations.

Constraints and invariants:
- Dates are parsed by `StringParser.parseIso8601Date`, not by the project or contributor date parsers.
- If `start` is after `end`, the organization is skipped.
- Multi-organization historical aggregation assumes lists have compatible positions; it does not align by timestamp.

Failure and exceptional cases:
- Failing function: `retrieve historical organization statistics`
  - Failure condition: malformed `start_date` or `end_date`.
  - Why it fails: `StatisticsService` throws `IllegalArgumentException`, mapped to HTTP 400.
  - Violated prerequisite or constraint: date value is not parseable as expected ISO-8601.
- Failing function: `trigger GitHub data fetch`
  - Failure condition: statistics collection fails.
  - Why it fails: no statistics rows are persisted.
  - Violated prerequisite or constraint: historical statistics state is absent.

Implementation notes:
For multiple organizations, aggregation is order-based and can combine different real timestamps if organization lists are not aligned.

### Behavior 11: View Top Project Metric Time Series

Business goal:
Show top project histories for dashboard charts.

Domain context:
This behavior compresses project snapshots into per-project arrays of commits, forks, contributors, scores, and snapshot dates.

Starting point:
No prior service state, assuming configured GitHub organizations have at least 10 distinct projects.

Required execution workflow:
1. Use function `trigger GitHub data fetch` (`GET /fetch`) with no request values to create project snapshots.
2. Use function `retrieve project statistics time series` (`GET /statistics/projects`) with optional `organizations={organizationNameCsv}`, `start_date={iso8601Date}`, and `end_date={iso8601Date}` to retrieve the top 10 project time series.

Optional verification workflow:
1. Use function `list latest projects` (`GET /projects`) with `organizations={sameOrganizationNameCsv}` and `limit=10` to inspect current top projects.
2. Use function `export database contents` (`GET /export`) to inspect raw project snapshot rows.

Existing-state shortcuts:
- Skip `trigger GitHub data fetch` if project snapshot rows already exist in the query range.
- Direct database setup can insert at least 10 distinct project-name groups with matching organization names and dates.
- The date range must include the stored snapshot dates.

Parameter and value bindings:
- `organizations` filters by `Project.organizationName`; omitted means all projects in range.
- `start_date` and `end_date` define the snapshot date range; defaults are last 30 days through now.
- Project time series are grouped by project name.

Business result:
The client receives exactly the first 10 project time-series objects after sorting by last score, provided at least 10 groups exist.

Constraints and invariants:
- At least 10 project groups must be present; otherwise `subList(0, 10)` fails.
- Grouping by project name can merge unrelated projects with the same name across organizations.
- The endpoint returns chart summaries, not raw project rows.

Failure and exceptional cases:
- Failing function: `retrieve project statistics time series`
  - Failure condition: fewer than 10 project groups match.
  - Why it fails: implementation calls `result.subList(0, 10)` unconditionally.
  - Violated prerequisite or constraint: required minimum group count is not met.
- Failing function: `retrieve project statistics time series`
  - Failure condition: malformed date.
  - Why it fails: parse failure is mapped to HTTP 400.
  - Violated prerequisite or constraint: date is not parseable.
- Failing function: `trigger GitHub data fetch`
  - Failure condition: fetch creates fewer than 10 project groups or fails.
  - Why it fails: the time-series endpoint cannot safely take top 10.
  - Violated prerequisite or constraint: dashboard data volume is insufficient.

Implementation notes:
This endpoint is brittle for small organizations and empty databases.

### Behavior 12: View Top Contributor Time Series

Business goal:
Show top contributor histories for dashboard charts.

Domain context:
This behavior presents contributor activity over time by GitHub login.

Starting point:
No prior service state, assuming configured GitHub organizations have at least 10 contributor groups.

Required execution workflow:
1. Use function `trigger GitHub data fetch` (`GET /fetch`) with no request values to create contributor snapshots.
2. Use function `retrieve contributor statistics time series` (`GET /statistics/contributors`) with optional `organizations={organizationNameCsv}`, `start_date={iso8601Date}`, and `end_date={iso8601Date}` to retrieve top 10 contributor time series.

Optional verification workflow:
1. Use function `list current top contributors` (`GET /contributors`) with `organizations={sameOrganizationNameCsv}` and `limit=10` to inspect current contributor ranking.
2. Use function `export database contents` (`GET /export`) to inspect raw contributor rows.

Existing-state shortcuts:
- Skip `trigger GitHub data fetch` if contributor rows already exist in the query range.
- Direct database setup can insert at least 10 distinct contributor groups with GitHub-style URLs beginning `https://github.com/`.
- The date range must include stored contributor `snapshotDate` values.

Parameter and value bindings:
- `organizations` filters by `Contributor.organizationName`; omitted means all contributors in range.
- `start_date` and `end_date` define the range; defaults are last 30 days through now.
- Contributor login is derived from the `url` field.

Business result:
The client receives top 10 contributor time-series summaries sorted by latest organizational commit count.

Constraints and invariants:
- At least 10 contributor groups must be present or the endpoint fails.
- Contributor URLs must start with `https://github.com/` to produce meaningful login ids.
- Time series use stored contributor snapshots; no GitHub call is made by the read endpoint.

Failure and exceptional cases:
- Failing function: `retrieve contributor statistics time series`
  - Failure condition: fewer than 10 contributor groups match.
  - Why it fails: implementation calls `result.subList(0, 10)` unconditionally.
  - Violated prerequisite or constraint: required minimum group count is not met.
- Failing function: `retrieve contributor statistics time series`
  - Failure condition: malformed date.
  - Why it fails: parse failure is mapped to HTTP 400.
  - Violated prerequisite or constraint: date is not parseable.
- Failing function: `trigger GitHub data fetch`
  - Failure condition: contributor collection fails or produces fewer than 10 contributors.
  - Why it fails: chart state is unavailable or insufficient.
  - Violated prerequisite or constraint: contributor time-series state is insufficient.

Implementation notes:
This endpoint is separate from contributor delta listing and has different date parsing behavior.

### Behavior 13: View Language Adoption Time Series

Business goal:
Show how project counts by language change over time.

Domain context:
This supports technology trend dashboards based on project primary language.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `trigger GitHub data fetch` (`GET /fetch`) with no request values to create project snapshots with primary languages.
2. Use function `retrieve language statistics time series` (`GET /statistics/languages`) with optional `organizations={organizationNameCsv}`, `start_date={iso8601Date}`, and `end_date={iso8601Date}` to retrieve language time series.

Optional verification workflow:
1. Use function `list primary language usage` (`GET /languages`) with `organizations={sameOrganizationNameCsv}` to inspect current language usage.

Existing-state shortcuts:
- Skip `trigger GitHub data fetch` if project rows already exist in the date range.
- Direct database setup can insert `Project` rows with `organizationName`, `snapshotDate`, and `primaryLanguage`.
- If `organizations` is omitted, the implementation uses configured `organization.list`.

Parameter and value bindings:
- `organizations` scopes the project rows used for language stats.
- `start_date` and `end_date` define the project snapshot range.
- `Project.primaryLanguage` is the source language value; null values are grouped as `unknown`.

Business result:
The client receives language project-count histories across selected snapshots.

Constraints and invariants:
- Duplicate snapshots are filtered by language, project name, organization, and snapshot date.
- Unlike project/contributor top time-series endpoints, this does not require at least 10 groups.
- Dates must be parseable by the statistics API parser.

Failure and exceptional cases:
- Failing function: `retrieve language statistics time series`
  - Failure condition: malformed `start_date` or `end_date`.
  - Why it fails: date parse failure is mapped to HTTP 400.
  - Violated prerequisite or constraint: date is not parseable.
- Failing function: `trigger GitHub data fetch`
  - Failure condition: no project rows are persisted.
  - Why it fails: language stats have no source data.
  - Violated prerequisite or constraint: project snapshot state is absent.

Implementation notes:
Language time series are project-derived, not based on a persisted language table.

### Behavior 14: Export Stored Analytics Data

Business goal:
Extract all stored analytics data for backup, inspection, or offline analysis.

Domain context:
The service has disabled import and delete endpoints, but still supports read-only full export.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `export database contents` (`GET /export`) with no path, query, body, form, or header values to retrieve all contributors, projects, and statistics.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. Export is itself a read behavior.
- If the database is empty, the response still succeeds with empty arrays.

Parameter and value bindings:
- No request values are supplied.
- Response fields are bound directly to repository `findAll()` results for contributors, projects, and statistics.

Business result:
The client receives a `DatabaseDto` containing arrays named `contributors`, `projects`, and `statistics`.

Constraints and invariants:
- Export is not filtered by organization, date, or resource type.
- It can expose all stored data to any caller that can access the endpoint.

Failure and exceptional cases:
- Failing function: `export database contents`
  - Failure condition: database repository access fails.
  - Why it fails: the controller directly calls `findAll()` on all three repositories.
  - Violated prerequisite or constraint: database read availability is missing.

Implementation notes:
This is the only supported administrative data endpoint that returns stored content.

### Behavior 15: Enforce Disabled Administrative Mutations

Business goal:
Prevent runtime mutation of database contents and project scoring configuration through unsafe administrative endpoints.

Domain context:
The codebase contains endpoints that historically could initialize, delete, import, or reconfigure scoring. The implementation intentionally rejects them.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `reject project scoring reconfiguration` (`POST /config/scoring.project`) with optional body `scoringProject={scriptText}` and optional header `X-Organizations={organizationNameCsv}` to receive rejection.
2. Use function `reject database initialization` (`GET /init`) with no request values to receive rejection.
3. Use function `reject database deletion` (`GET /delete`) with no request values to receive rejection.
4. Use function `reject database import` (`POST /import`) with body `DatabaseDto={databaseDto}` to receive rejection.

Optional verification workflow:
1. Use function `export database contents` (`GET /export`) before and after a rejected mutation attempt to verify no imported, initialized, deleted, or reconfigured data changed.

Existing-state shortcuts:
- Individual rejection steps can be skipped when only one disabled administrative capability is being tested.
- Direct database setup is not equivalent to these behaviors because the business result is API-level rejection.
- Existing OAuth or security filters may reject `/init` and `/delete` before controller-level rejection.

Parameter and value bindings:
- `scoringProject` body and `X-Organizations` header are ignored by implementation.
- `DatabaseDto` body is ignored by implementation.
- No submitted values are persisted or executed.

Business result:
Each administrative mutation attempt receives HTTP 403 with `"This endpoint is deactivated."` when the controller handles it. No database import, initialization, deletion, or scoring reconfiguration occurs.

Constraints and invariants:
- Swagger advertises successful responses for these endpoints, but implementation always rejects them.
- `/init/**` and `/delete/**` are also restricted by OAuth scope `uid` if security applies first.
- No partial mutation occurs in controller code.

Failure and exceptional cases:
- Failing function: `reject project scoring reconfiguration`
  - Failure condition: any request is made to update scoring.
  - Why it fails: controller always throws `UnsupportedOperationException`.
  - Violated prerequisite or constraint: administrative scoring mutation is disabled.
- Failing function: `reject database initialization`
  - Failure condition: any API initialization request is made.
  - Why it fails: controller always throws `UnsupportedOperationException`, or security may reject earlier.
  - Violated prerequisite or constraint: API database initialization is disabled.
- Failing function: `reject database deletion`
  - Failure condition: any API delete-all request is made.
  - Why it fails: controller always throws `UnsupportedOperationException`, or security may reject earlier.
  - Violated prerequisite or constraint: API database deletion is disabled.
- Failing function: `reject database import`
  - Failure condition: any API import request is made.
  - Why it fails: controller always throws `UnsupportedOperationException`.
  - Violated prerequisite or constraint: API database import is disabled.

Implementation notes:
This is an implementation/OpenAPI discrepancy: Swagger documents success paths, while source code enforces rejection.

### Behavior 16: Check Operational Health and Error Rendering

Business goal:
Confirm the service is running and can render framework error responses.

Domain context:
Operational clients need liveness or health checks separate from domain analytics.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `check service health` (`GET /health`) with no request values, or use function `check service health` (`GET /health.json`) with no request values, to inspect actuator health.
2. Use function `render framework error response` (`GET /error`) with no request values only when inspecting the framework error model directly or after an error dispatch.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. These are runtime framework reads.
- Direct database setup is not relevant unless health indicators include database health at runtime.

Parameter and value bindings:
- `/health` and `/health.json` are alternate paths for the same actuator health capability.
- `/error` output depends on the current framework error attributes and request method.

Business result:
Health information is returned for health checks, and framework error metadata is returned for error rendering.

Constraints and invariants:
- Health is enabled while most actuator endpoints are disabled by properties.
- `/error` is framework-provided and not a project-specific business endpoint.

Failure and exceptional cases:
- Failing function: `check service health`
  - Failure condition: the actuator endpoint is unavailable or reports unhealthy state.
  - Why it fails: Spring Boot actuator health infrastructure or underlying health indicators determine status.
  - Violated prerequisite or constraint: operational readiness is not satisfied.
- Failing function: `render framework error response`
  - Failure condition: the original request failed.
  - Why it fails: `/error` represents framework error handling rather than resolving the original domain failure.
  - Violated prerequisite or constraint: original request could not complete normally.

Implementation notes:
These endpoints are operational rather than core CatWatch domain analytics.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: API-Supported Data Import, Initialization, or Manual Repair

Priority:
Critical domain gap

Expected business goal:
An administrator should be able to seed, restore, migrate, or repair analytics data through controlled API operations.

Why it is unsupported:
The relevant endpoints are present but deliberately disabled.

Existing functions considered:
- `reject database import`: accepts `DatabaseDto` in Swagger but always rejects.
- `reject database initialization`: always rejects initialization.
- `export database contents`: can read all data but cannot restore it.
- `trigger GitHub data fetch`: can create fresh GitHub-derived snapshots but cannot import arbitrary historical or repaired data.

Missing capability:
A secured, validated import or initialization endpoint that persists contributors, projects, and statistics transactionally.

Proof that function composition is insufficient:
Export plus fetch cannot recreate arbitrary historical snapshots, corrected metrics, deleted rows, or externally supplied backup data. Fetch can only collect current configured GitHub state, and disabled import/init endpoints persist nothing.

Evidence from existing functions/source:
`AdminController.importJson`, `init`, and `deleteAll` always throw `UnsupportedOperationException`.

Business impact:
Backup restoration, deterministic test seeding, migration repair, and historical backfill are blocked through the API.

### Missing Behavior 2: Reliable Snapshot Timestamp Discovery After Fetch

Priority:
Important robustness gap

Expected business goal:
After collecting a snapshot, clients should receive or retrieve the generated snapshot id/date for later historical and delta queries.

Why it is unsupported:
`trigger GitHub data fetch` returns only `"OK"` and does not expose `snapshotDate`.

Existing functions considered:
- `trigger GitHub data fetch`: creates the timestamp but does not return it.
- `export database contents`: exposes raw rows and can be used as a heavy workaround.
- `list project metric deltas over time`: needs dates to select baselines.
- `list contributor activity deltas over time`: requires both dates.

Missing capability:
A fetch response containing generated snapshot metadata, or a lightweight snapshot listing endpoint.

Proof that function composition is insufficient:
The only API-visible workaround is full database export, which is not scoped and does not distinguish which exact rows came from the immediately preceding fetch when multiple snapshots or organizations exist.

Evidence from existing functions/source:
`FetchController.fetch()` calls `fetcher.tryFetchData()` and returns `"OK"`.

Business impact:
Clients cannot cleanly chain fetch into precise historical or delta reads.

### Missing Behavior 3: Safe Small-Data Time Series Retrieval

Priority:
Important robustness gap

Expected business goal:
A dashboard should return zero to ten project or contributor time series when fewer than 10 groups exist.

Why it is unsupported:
The implementation requires at least 10 result groups.

Existing functions considered:
- `retrieve project statistics time series`: fails below 10 project groups.
- `retrieve contributor statistics time series`: fails below 10 contributor groups.
- `list latest projects`: can list fewer projects but does not return time-series arrays.
- `list current top contributors`: can list fewer contributors but does not return time-series arrays.

Missing capability:
Bounds-safe top-N time-series behavior using `min(10, result.size())`.

Proof that function composition is insufficient:
List endpoints do not produce historical arrays, and time-series endpoints cannot be made safe by pre-calling another API. The missing state is not data alone; it is bounds-safe response construction.

Evidence from existing functions/source:
Both time-series endpoints call `result.subList(0, 10)` unconditionally.

Business impact:
Small organizations, narrow date filters, and empty systems produce server errors instead of valid dashboard responses.

### Missing Behavior 4: Individual Project or Contributor Detail Retrieval

Priority:
API ergonomics gap

Expected business goal:
A client should retrieve one project or one contributor by stable identity.

Why it is unsupported:
Only list endpoints exist.

Existing functions considered:
- `list latest projects`: can filter by project name prefix but cannot retrieve by `gitHubProjectId`.
- `list projects as of an end date`: can filter by prefix but not stable id.
- `list current top contributors`: can filter by contributor name prefix but not contributor id or login.
- `export database contents`: returns all rows but is too broad and not a detail endpoint.

Missing capability:
`GET /projects/{gitHubProjectId}` and `GET /contributors/{id}` or equivalent identity filters.

Proof that function composition is insufficient:
Prefix search can return multiple resources and cannot distinguish renamed projects, duplicate names, or contributors with similar names. Export requires client-side scanning of all stored data and bypasses domain scoping.

Evidence from existing functions/source:
Controllers expose only collection routes: `/projects` and `/contributors`.

Business impact:
Detail pages and precise integrations require inefficient or ambiguous client-side filtering.

### Missing Behavior 5: Project and Contributor Lifecycle Management

Priority:
Critical domain gap

Expected business goal:
Administrators may reasonably expect to create, update, correct, or delete project/contributor records when GitHub data is wrong or unavailable.

Why it is unsupported:
There are no resource-level write endpoints, and import/delete are disabled.

Existing functions considered:
- `trigger GitHub data fetch`: creates only GitHub-derived snapshots.
- `reject database import`: rejects bulk create/update.
- `reject database deletion`: rejects delete-all.
- `export database contents`: read-only.

Missing capability:
Controlled CRUD or correction endpoints with validation and ownership/scoping rules.

Proof that function composition is insufficient:
Fetch cannot create arbitrary corrections, cannot delete stale records, and cannot update a single field. Delete-and-recreate is not available through the API, and direct database manipulation bypasses service invariants.

Evidence from existing functions/source:
No controller exposes POST/PUT/PATCH/DELETE for project or contributor resources.

Business impact:
Data quality repair depends on external database access or waiting for future GitHub fetches.

### Missing Behavior 6: Score Reconfiguration and Score Recalculation

Priority:
Important robustness gap

Expected business goal:
An administrator should update scoring rules and recompute project scores consistently.

Why it is unsupported:
The scoring configuration endpoint is disabled, and scores are calculated only during fetch.

Existing functions considered:
- `reject project scoring reconfiguration`: always rejects.
- `trigger GitHub data fetch`: calculates score using current server-side scorer during collection.
- `list latest projects`: can sort by score but cannot recompute it.

Missing capability:
A safe scoring configuration API and a controlled recomputation endpoint for existing snapshots.

Proof that function composition is insufficient:
Fetching again creates new snapshots but does not recalculate scores for existing historical snapshots. Listing only reads stored scores.

Evidence from existing functions/source:
`AdminController.configScoringProjects` throws, and `TakeSnapshotTask.collectProjects` sets score during snapshot collection.

Business impact:
Score history can become stale relative to business rules, and clients cannot normalize historical scores.

### Missing Behavior 7: Consistent Cross-Organization Historical Aggregation

Priority:
Important robustness gap

Expected business goal:
Historical statistics across organizations should align records by snapshot date or explicit time buckets.

Why it is unsupported:
The implementation aggregates multiple organization lists positionally.

Existing functions considered:
- `retrieve historical organization statistics`: aggregates by list index.
- `trigger GitHub data fetch`: can create same-cycle timestamps, but historical data may still be uneven.
- `export database contents`: exposes raw data but does not aggregate safely.

Missing capability:
Timestamp-aligned or bucketed aggregation logic.

Proof that function composition is insufficient:
Clients cannot force existing uneven historical records to align through query parameters. Export allows external recomputation but does not make the API behavior correct.

Evidence from existing functions/source:
`StatisticsService.aggregateHistoricalStatistics` uses `statisticsLists.get(0).size()` and `orgStats.get(i)`.

Business impact:
Multi-organization trend charts can combine unrelated timestamps and produce misleading totals.

### Missing Behavior 8: Explicit Organization Discovery

Priority:
API ergonomics gap

Expected business goal:
Clients should list known/configured organizations before querying analytics.

Why it is unsupported:
No endpoint returns organizations as first-class resources.

Existing functions considered:
- `expose public runtime configuration`: may expose `organization.list` only if included in public properties.
- `export database contents`: can infer organizations from rows, but only by scanning all data.
- `retrieve latest aggregate organization statistics`: requires the caller to know organization names or rely on default config.

Missing capability:
`GET /organizations` or equivalent metadata endpoint.

Proof that function composition is insufficient:
Configuration exposure is not guaranteed to include organization list, and export is unfiltered, heavy, and tied to existing data rather than configured scope.

Evidence from existing functions/source:
No controller maps an organization listing route.

Business impact:
Clients must be preconfigured out of band or use broad export workarounds.

### Missing Behavior 9: Consistent Date Format Contract

Priority:
API ergonomics gap

Expected business goal:
All date-based analytics endpoints should accept one documented date format.

Why it is unsupported:
Projects, contributors, and statistics parse dates differently.

Existing functions considered:
- `list projects as of an end date`: uses `yyyy-MM-dd'T'HH:mm:ss`.
- `list project metric deltas over time`: uses the same Spring project pattern.
- `list contributor activity deltas over time`: uses `yyyy-MM-dd'T'HH:mm:ss'Z'`.
- `retrieve historical organization statistics`: uses `StringParser.parseIso8601Date`.
- `retrieve project statistics time series`, `retrieve contributor statistics time series`, and `retrieve language statistics time series`: use the statistics parser.

Missing capability:
A uniform date parser and OpenAPI schema across endpoints.

Proof that function composition is insufficient:
A date string valid for one endpoint can fail in another. Chaining functions cannot normalize server-side parsing behavior.

Evidence from existing functions/source:
Projects use `@DateTimeFormat`, contributors use `DateUtil.iso8601`, and statistics use `StringParser.parseIso8601Date`.

Business impact:
Clients need endpoint-specific date handling and are more likely to fail workflows involving multiple analytics views.

### Missing Behavior 10: Prefix Language Search as Documented

Priority:
API ergonomics gap

Expected business goal:
Search language usage by language-name prefix.

Why it is unsupported:
Swagger says `q` can be a language prefix, but implementation uses it as an exact project `primaryLanguage` filter.

Existing functions considered:
- `list primary language usage`: filters exact primary language.
- `list latest projects`: supports exact `language`, not prefix language search.
- `retrieve language statistics time series`: has no language filter.

Missing capability:
Prefix filtering on language names after aggregation or repository-level `startsWith` filtering for `primaryLanguage`.

Proof that function composition is insufficient:
Exact filtering cannot return all languages matching a prefix such as `Java` for both `Java` and `JavaScript`. Project prefix search applies to project names, not languages.

Evidence from existing functions/source:
`LanguageService.getMainLanguages` passes `filterLanguage` to `repository.findProjects`, which applies `project.primaryLanguage.eq(language)`.

Business impact:
Language search behavior is weaker than documented and can surprise API consumers.

## Cross-Behavior Observations

- The service is read-heavy and snapshot-based. `trigger GitHub data fetch` is the only supported API path that creates analytics data.
- Administrative write endpoints are present in Swagger but disabled in source, returning HTTP 403 when handled by `AdminController`.
- Fetch returns only `"OK"`, so generated snapshot dates are not cleanly reusable without full export.
- Date formats are inconsistent across project, contributor, and statistics endpoints.
- Project delta logic is permissive and can silently treat missing baselines as new projects; contributor delta logic validates more strictly.
- Top 10 project and contributor time-series endpoints are unsafe for fewer than 10 groups.
- Language analytics are derived from project rows; collected language objects are not persisted.
- Multi-organization historical statistics aggregation is positional rather than timestamp-aligned.
- Several Swagger descriptions disagree with implementation, especially disabled admin endpoints, language `q`, and required organization parameters.

## Coverage Summary

Supported domain areas:
Project listing, project historical views, project metric deltas, contributor ranking, contributor deltas, language usage, organization statistics, time-series dashboard summaries, snapshot fetching, configuration exposure, health checks, and full database export.

Partially supported domain areas:
Historical analytics, cross-organization aggregation, language search, score-based ranking, and dashboard time series are supported but have correctness, robustness, or contract limitations.

Unsupported domain areas:
API-based data import/restore, resource-level CRUD, individual resource detail lookup, safe reset/delete administration, scoring reconfiguration/recalculation, explicit snapshot metadata discovery, organization discovery, and consistently validated date/query contracts.