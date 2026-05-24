# Domain-Level Behavior Analysis

## Domain Summary
This service is a read-only public catalog and artifact download API for PaperMC-style software projects. The main domain resources are projects, version groups, versions, builds, build changes, download metadata, and downloadable Java archive files.

The API exposes discovery and retrieval workflows only. It does not expose public endpoints to create, update, delete, publish, promote, or repair catalog data. Project, version group, version, build, and download metadata must already exist in MongoDB, and downloadable files must already exist under the configured storage path.

## Available Function Inventory

### Project Catalog
- `list available projects`
  - Endpoint: `GET /v2/projects`
  - Domain meaning: Discover all project identifiers currently available in the catalog.

- `retrieve project information`
  - Endpoint: `GET /v2/projects/{project}`
  - Domain meaning: Retrieve a project summary, including friendly project name, version group names, and version names.

### Version Group Catalog
- `retrieve version group information`
  - Endpoint: `GET /v2/projects/{project}/version_group/{family}`
  - Domain meaning: Retrieve one version group under a project and the versions assigned to that group.

- `list builds for a version group`
  - Endpoint: `GET /v2/projects/{project}/version_group/{family}/builds`
  - Domain meaning: Retrieve build metadata across all versions in one version group.

### Version Catalog
- `retrieve version information`
  - Endpoint: `GET /v2/projects/{project}/versions/{version}`
  - Domain meaning: Retrieve one version under a project and its build numbers.

- `list builds for a version`
  - Endpoint: `GET /v2/projects/{project}/versions/{version}/builds`
  - Domain meaning: Retrieve detailed build metadata for all builds of one version.

### Build and Artifact Delivery
- `retrieve build information`
  - Endpoint: `GET /v2/projects/{project}/versions/{version}/builds/{build}`
  - Domain meaning: Retrieve detailed metadata for one build.

- `download build artifact`
  - Endpoint: `GET /v2/projects/{project}/versions/{version}/builds/{build}/downloads/{download}`
  - Domain meaning: Serve one artifact file for a build download entry.

## Supported Business Behaviors

### Behavior 1: Discover Available Projects

Business goal:
Allow a client to discover which software projects are exposed by the catalog.

Domain context:
Project identifiers are the root entry point for every project-scoped release, version, build, and download workflow.

Starting point:
No prior service state is required. The database may be empty.

Required execution workflow:
1. Use function `list available projects` (`GET /v2/projects`) with no path, query, body, form, or header values to retrieve the current list of project identifiers.

Optional verification workflow:
1. Use function `retrieve project information` (`GET /v2/projects/{project}`) with `project={projectNameFromProjects}` to inspect one returned project.

Existing-state shortcuts:
- None for the core behavior. This behavior is already the discovery shortcut for other workflows.
- Direct database setup may insert `Project` rows beforehand, but it is not required for successful execution; an empty list is still a valid result.

Parameter and value bindings:
- Returned `projects[]` values are reusable as `{project}` path values in all project-scoped functions.
- No generated ID is returned. Internal MongoDB `Project._id` values remain hidden and are resolved by project name.

Business result:
The client receives a list of project identifiers. If no projects exist, the result is an empty project list rather than an error.

Constraints and invariants:
- The function has no project existence requirement.
- The implementation returns `Project.name` values from all MongoDB project rows.
- No uniqueness guarantee is enforced by the model annotation; `Project.name` is indexed but not declared unique.

Failure and exceptional cases:
- No function-specific failure branch is visible in `full-behavior.md`, OpenAPI, or controller logic.

Implementation notes:
- The controller does not explicitly sort projects; ordering depends on repository `findAll()` behavior.
- The response is cached with a public seven-day shared-cache policy.

---

### Behavior 2: Inspect a Project Release Catalog

Business goal:
Allow a client to inspect one project and see the version groups and versions available for it.

Domain context:
This is the main project landing behavior. It exposes the release catalog structure needed before selecting a version group, version, build, or artifact.

Starting point:
Pre-existing catalog database state: at least one `Project` row may exist. The client may begin without knowing a valid project identifier.

Required execution workflow:
1. Use function `list available projects` (`GET /v2/projects`) with no values to obtain `projects[]`.
2. Use function `retrieve project information` (`GET /v2/projects/{project}`) with `project={projectNameFromProjects}` to retrieve the project summary, version groups, and versions.

Optional verification workflow:
None.

Existing-state shortcuts:
- If the client already knows a valid `project` value, step 1 can be skipped.
- Direct database setup can satisfy the state by inserting a `Project` row with `name={project}` and optional linked `VersionFamily` and `Version` rows using the generated `Project._id`.
- The core project inspection step cannot be skipped for this behavior.

Parameter and value bindings:
- The `project` path value in step 2 must equal one of the project names returned in step 1, or another existing `Project.name`.
- `version_groups[]` values returned by step 2 are reusable as `{family}` in version-group functions.
- `versions[]` values returned by step 2 are reusable as `{version}` in version functions.
- Internally, linked version groups and versions are selected by matching their `project` field to the resolved `Project._id`.

Business result:
The client receives the project identifier, friendly project name, available version group names, and available version names.

Constraints and invariants:
- `{project}` must match `[a-z]+`.
- The project must exist by name.
- Version groups and versions are scoped by internal `Project._id`, not just by visible project name.
- Version groups and versions in the response are sorted by time when both compared records have time values; otherwise by name.

Failure and exceptional cases:
- Failing function: `retrieve project information`
  - Failure condition: No project exists with `name={project}`.
  - Why it fails: `ProjectCollection.findByName(project)` returns empty and the controller throws `ProjectNotFound`.
  - Violated prerequisite or constraint: The selected project identifier does not resolve to persisted project state.

Implementation notes:
- OpenAPI documents only a `200` response, but the implementation returns `404` with `{"error":"Project not found."}` for missing projects.
- Project names are indexed but not uniquely constrained in the model.

---

### Behavior 3: Inspect Versions in a Version Group

Business goal:
Allow a client to inspect a release line or version group and see which concrete versions belong to it.

Domain context:
Version groups represent grouped release families, such as a major/minor line. They help clients browse versions at a higher domain level than individual builds.

Starting point:
Pre-existing catalog database state: a project and a version group under that project exist.

Required execution workflow:
1. Use function `list available projects` (`GET /v2/projects`) to obtain `projects[]`.
2. Use function `retrieve project information` (`GET /v2/projects/{project}`) with `project={projectNameFromProjects}` to obtain `version_groups[]`.
3. Use function `retrieve version group information` (`GET /v2/projects/{project}/version_group/{family}`) with `project={sameProject}` and `family={versionGroupNameFromProject}` to retrieve the group and its versions.

Optional verification workflow:
1. Use function `retrieve version information` (`GET /v2/projects/{project}/versions/{version}`) with `project={sameProject}` and `version={versionNameFromVersionGroup}` to inspect an individual version from the group.

Existing-state shortcuts:
- If the client already knows valid `project` and `family` values, steps 1 and 2 can be skipped.
- Direct database setup can insert `Project`, `VersionFamily`, and linked `Version` rows. The `VersionFamily.project` and `Version.project` values must equal the generated `Project._id`, and `Version.group` must equal the generated `VersionFamily._id`.

Parameter and value bindings:
- The `project` value must be reused across steps 2 and 3.
- The `family` value in step 3 must come from `version_groups[]` returned by `retrieve project information`, or otherwise match a persisted `VersionFamily.name` scoped to the same project.
- Returned `versions[]` values can be reused as `{version}` for version and build functions.

Business result:
The client receives the selected project, selected version group, and all version names currently assigned to that group.

Constraints and invariants:
- `{project}` must match `[a-z]+`.
- `{family}` must match `[0-9.]+-?(?:pre|SNAPSHOT)?(?:[0-9.]+)?`.
- The group lookup is project-scoped through `Project._id`.
- A group with no linked versions can still return successfully with an empty version list.

Failure and exceptional cases:
- Failing function: `retrieve project information`
  - Failure condition: No project exists with `name={project}`.
  - Why it fails: The project repository lookup returns empty.
  - Violated prerequisite or constraint: The selected project does not exist.

- Failing function: `retrieve version group information`
  - Failure condition: `project` exists, but no `VersionFamily` exists with `project=Project._id` and `name={family}`.
  - Why it fails: `VersionFamilyCollection.findByProjectAndName(...)` returns empty and the controller throws `VersionNotFound`.
  - Violated prerequisite or constraint: The selected group is not owned by the selected project.

Implementation notes:
- Missing version groups return the same error message as missing versions: `{"error":"Version not found."}`.
- OpenAPI documents only successful `200` responses, not the implementation-backed `404` cases.

---

### Behavior 4: Inspect a Version and Its Build Numbers

Business goal:
Allow a client to inspect a concrete project version and see which build numbers exist for that version.

Domain context:
Versions are the direct parent of builds. This behavior is useful before selecting a specific build or before listing detailed build metadata.

Starting point:
Pre-existing catalog database state: a project and version under that project exist.

Required execution workflow:
1. Use function `list available projects` (`GET /v2/projects`) to obtain `projects[]`.
2. Use function `retrieve project information` (`GET /v2/projects/{project}`) with `project={projectNameFromProjects}` to obtain `versions[]`.
3. Use function `retrieve version information` (`GET /v2/projects/{project}/versions/{version}`) with `project={sameProject}` and `version={versionNameFromProject}` to retrieve the version and its build numbers.

Optional verification workflow:
1. Use function `retrieve build information` (`GET /v2/projects/{project}/versions/{version}/builds/{build}`) with `project={sameProject}`, `version={sameVersion}`, and `build={buildNumberFromVersion}` to inspect one listed build.

Existing-state shortcuts:
- If the client already knows valid `project` and `version` values, steps 1 and 2 can be skipped.
- Direct database setup can insert `Project`, `Version`, and optional linked `Build` rows. `Version.project` and `Build.project` must equal the generated `Project._id`; `Build.version` must equal the generated `Version._id`.

Parameter and value bindings:
- The `project` path value must be reused between project and version calls.
- The `version` path value must come from `versions[]` returned by `retrieve project information`, or otherwise match a persisted `Version.name` under the same project.
- Build numbers returned by `retrieve version information` are reusable as `{build}` for build retrieval or download workflows.

Business result:
The client receives project identity, friendly project name, selected version, and available build numbers.

Constraints and invariants:
- `{version}` must match `[0-9.]+-?(?:pre|SNAPSHOT)?(?:[0-9.]+)?`.
- Version ownership is enforced by lookup on both `Project._id` and `Version.name`.
- A version with no builds still returns successfully with an empty `builds` array.

Failure and exceptional cases:
- Failing function: `retrieve version information`
  - Failure condition: The project exists, but no version exists under that project with `name={version}`.
  - Why it fails: `VersionCollection.findByProjectAndName(...)` returns empty.
  - Violated prerequisite or constraint: The selected version is absent or belongs to another project.

- Failing function: `retrieve project information`
  - Failure condition: The project does not exist.
  - Why it fails: The project lookup fails before version lookup.
  - Violated prerequisite or constraint: The version cannot be scoped to a valid project.

Implementation notes:
- Build numbers are returned from `BuildCollection.findAllByProjectAndVersion(...)` without explicit sorting.
- The response does not include build metadata beyond build numbers.

---

### Behavior 5: List Detailed Builds for a Version

Business goal:
Allow a client to view detailed build metadata for all builds of a selected version.

Domain context:
Builds are the deployable/releasable units. Their metadata includes build number, time, release channel, promotion flag, change list, and download entries.

Starting point:
Pre-existing catalog database state: a project, version, and zero or more builds for that version exist.

Required execution workflow:
1. Use function `list available projects` (`GET /v2/projects`) to obtain `projects[]`.
2. Use function `retrieve project information` (`GET /v2/projects/{project}`) with `project={projectNameFromProjects}` to obtain `versions[]`.
3. Use function `list builds for a version` (`GET /v2/projects/{project}/versions/{version}/builds`) with `project={sameProject}` and `version={versionNameFromProject}` to retrieve detailed build metadata.

Optional verification workflow:
1. Use function `retrieve version information` (`GET /v2/projects/{project}/versions/{version}`) with `project={sameProject}` and `version={sameVersion}` to compare the returned build numbers.
2. Use function `retrieve build information` (`GET /v2/projects/{project}/versions/{version}/builds/{build}`) with `project={sameProject}`, `version={sameVersion}`, and `build={buildNumberFromBuildsList}` to inspect one build independently.

Existing-state shortcuts:
- If valid `project` and `version` are already known, steps 1 and 2 can be skipped.
- Direct database setup can insert linked `Build` rows. Each build must reuse the same generated `Project._id` and `Version._id`.

Parameter and value bindings:
- The `project` value must be the same in all steps.
- The `version` value in step 3 must be selected from `versions[]` or otherwise match `Version.name` under the same project.
- Each returned build object’s `build` value is reusable as `{build}`.
- Each returned `downloads.*.name` value is reusable as `{download}` for `download build artifact`.

Business result:
The client receives all build metadata for the selected version, including changes and download metadata.

Constraints and invariants:
- Project and version existence are required.
- Builds are scoped by internal project and version IDs.
- Missing `channel` defaults to `default`; missing `promoted` defaults to `false`.
- Build listing order is not explicitly sorted by the controller or repository.

Failure and exceptional cases:
- Failing function: `list builds for a version`
  - Failure condition: No project exists with `name={project}`.
  - Why it fails: Project lookup returns empty.
  - Violated prerequisite or constraint: Builds cannot be scoped without a valid project.

- Failing function: `list builds for a version`
  - Failure condition: Project exists, but version does not exist under that project.
  - Why it fails: Version lookup returns empty.
  - Violated prerequisite or constraint: The selected version is absent or not owned by the selected project.

Implementation notes:
- The response includes download metadata but does not verify that the corresponding files exist in storage.
- OpenAPI does not document error responses.

---

### Behavior 6: List Builds Across a Version Group

Business goal:
Allow a client to view builds across all versions in a release group.

Domain context:
This supports release-line browsing, where a user cares about all builds in a family rather than one exact version.

Starting point:
Pre-existing catalog database state: a project and version group exist; the group may contain zero or more versions and builds.

Required execution workflow:
1. Use function `list available projects` (`GET /v2/projects`) to obtain `projects[]`.
2. Use function `retrieve project information` (`GET /v2/projects/{project}`) with `project={projectNameFromProjects}` to obtain `version_groups[]`.
3. Use function `list builds for a version group` (`GET /v2/projects/{project}/version_group/{family}/builds`) with `project={sameProject}` and `family={versionGroupNameFromProject}` to retrieve builds for all versions in the group.

Optional verification workflow:
1. Use function `retrieve version group information` (`GET /v2/projects/{project}/version_group/{family}`) with `project={sameProject}` and `family={sameFamily}` to inspect the group’s version membership.
2. Use function `retrieve build information` (`GET /v2/projects/{project}/versions/{version}/builds/{build}`) with `project={sameProject}`, `version={versionFromGroupBuild}`, and `build={buildNumberFromGroupBuild}` to inspect one returned build.

Existing-state shortcuts:
- If valid `project` and `family` values are already known, steps 1 and 2 can be skipped.
- Direct database setup can insert `Project`, `VersionFamily`, linked `Version` rows, and linked `Build` rows. `Version.group` must equal the generated `VersionFamily._id`; builds must point to one of those generated `Version._id` values.

Parameter and value bindings:
- The same `project` value scopes every step.
- The `family` value must match a version group owned by that project.
- Returned group-build items include both `version` and `build`; those two values must be reused together when calling version-specific build or download functions.

Business result:
The client receives the selected project, selected version group, group version names, and build metadata for builds whose versions belong to that group.

Constraints and invariants:
- A version group with no versions or no builds can return successfully with empty lists.
- Build ownership is enforced by `project` and by membership in the group’s version ID set.
- The implementation assumes every returned build’s `version` ID exists in the loaded group-version map.

Failure and exceptional cases:
- Failing function: `list builds for a version group`
  - Failure condition: No project exists with `name={project}`.
  - Why it fails: Project lookup returns empty.
  - Violated prerequisite or constraint: The group cannot be scoped.

- Failing function: `list builds for a version group`
  - Failure condition: Project exists, but no version group exists with `name={family}` under that project.
  - Why it fails: Version-family lookup returns empty and raises `VersionNotFound`.
  - Violated prerequisite or constraint: The selected group is absent or belongs to another project.

Implementation notes:
- Missing version groups return `{"error":"Version not found."}`, which is domain-ambiguous.
- Build ordering is not explicitly sorted.

---

### Behavior 7: Retrieve One Build’s Metadata

Business goal:
Allow a client to inspect the exact metadata for one selected build.

Domain context:
A build is the concrete release unit that carries change history and download entries. This behavior supports release detail pages and artifact selection.

Starting point:
Pre-existing catalog database state: a project, version, and build exist.

Required execution workflow:
1. Use function `list available projects` (`GET /v2/projects`) to obtain `projects[]`.
2. Use function `retrieve project information` (`GET /v2/projects/{project}`) with `project={projectNameFromProjects}` to obtain `versions[]`.
3. Use function `retrieve version information` (`GET /v2/projects/{project}/versions/{version}`) with `project={sameProject}` and `version={versionNameFromProject}` to obtain `builds[]`.
4. Use function `retrieve build information` (`GET /v2/projects/{project}/versions/{version}/builds/{build}`) with `project={sameProject}`, `version={sameVersion}`, and `build={buildNumberFromVersion}` to retrieve the build.

Optional verification workflow:
1. Use function `list builds for a version` (`GET /v2/projects/{project}/versions/{version}/builds`) with `project={sameProject}` and `version={sameVersion}` to verify the build appears in the version’s detailed build list.

Existing-state shortcuts:
- If valid `project`, `version`, and `build` values are already known, steps 1 through 3 can be skipped.
- Direct database setup can insert the build with `project=Project._id`, `version=Version._id`, and `number={build}`.

Parameter and value bindings:
- The same `project` and `version` values must be reused through steps 2 through 4.
- The `build` value in step 4 must come from the selected version’s `builds[]`, or otherwise match a persisted `Build.number` under the same project and version.
- Download names returned in the build’s `downloads` map are reusable as `{download}` for artifact download.

Business result:
The client receives exact build metadata: project, version, build number, build time, channel, promotion status, changes, and downloads.

Constraints and invariants:
- Build numbers are version-scoped, not globally unique.
- The same build number under another version or project does not satisfy the lookup.
- Missing `channel` and `promoted` values are normalized in responses to `default` and `false`.

Failure and exceptional cases:
- Failing function: `retrieve build information`
  - Failure condition: Project is missing.
  - Why it fails: Project lookup fails first.
  - Violated prerequisite or constraint: Build lookup requires project scope.

- Failing function: `retrieve build information`
  - Failure condition: Version is missing under the selected project.
  - Why it fails: Version lookup fails before build lookup.
  - Violated prerequisite or constraint: Build lookup requires version scope.

- Failing function: `retrieve build information`
  - Failure condition: No build exists with `number={build}` under the selected project and version.
  - Why it fails: `BuildCollection.findByProjectAndVersionAndNumber(...)` returns empty.
  - Violated prerequisite or constraint: The selected build number is not valid for that version.

Implementation notes:
- OpenAPI documents the successful schema but not `404` errors.
- The API returns download metadata as stored, but file availability is only tested by the download function.

---

### Behavior 8: Download a Known Build Artifact

Business goal:
Allow a client to download a specific artifact file for a known build.

Domain context:
Artifact delivery is the final consumer-facing workflow: a user or tool obtains the actual Java archive associated with catalog metadata.

Starting point:
Pre-existing catalog and storage state: project, version, build, download metadata, and the corresponding file under `storagePath/{project}/{version}/{build}/{download}` already exist. The client already has the path values.

Required execution workflow:
1. Use function `download build artifact` (`GET /v2/projects/{project}/versions/{version}/builds/{build}/downloads/{download}`) with `project={knownProject}`, `version={knownVersion}`, `build={knownBuildNumber}`, and `download={knownDownloadName}` to download the artifact.

Optional verification workflow:
1. Use function `retrieve build information` (`GET /v2/projects/{project}/versions/{version}/builds/{build}`) with `project={sameProject}`, `version={sameVersion}`, and `build={sameBuild}` to inspect download metadata and expected `sha256`.

Existing-state shortcuts:
- Discovery and metadata lookup can be skipped only if the caller already has valid values.
- Direct database setup can insert the required `Project`, `Version`, and `Build` rows, including a `downloads` map entry whose `Download.name={download}`.
- Direct filesystem setup must place a readable file at `storagePath/{project}/{version}/{build}/{download}`. There is no API function to create that file.

Parameter and value bindings:
- The `project`, `version`, and `build` path values must resolve to the same internal `Project._id`, `Version._id`, and `Build.number`.
- The `download` path value must equal a `Build.Download.name` value inside the build’s downloads map.
- The implementation compares against the download object’s `name`, not the map key.
- The same visible path values are reused to construct the storage path.

Business result:
The service returns the artifact as an attachment with content type `application/java-archive`. The persisted catalog state is unchanged.

Constraints and invariants:
- `{download}` path values may contain uppercase letters according to the route pattern `[a-zA-Z0-9._-]+`.
- The download schema documents lowercase `[a-z0-9._-]+` for `Download.name`, creating a documentation mismatch.
- The implementation does not compute or validate the file’s SHA-256 before serving it.
- The OpenAPI response documents an `ETag` header, but the implementation sets cache control, content disposition, content type, and last modified, not ETag.

Failure and exceptional cases:
- Failing function: `download build artifact`
  - Failure condition: Project is missing.
  - Why it fails: Project lookup returns empty.
  - Violated prerequisite or constraint: Artifact lookup requires project scope.

- Failing function: `download build artifact`
  - Failure condition: Version is missing under the project.
  - Why it fails: Version lookup returns empty.
  - Violated prerequisite or constraint: Artifact lookup requires version scope.

- Failing function: `download build artifact`
  - Failure condition: Build number is missing under the selected project and version.
  - Why it fails: Build lookup returns empty.
  - Violated prerequisite or constraint: Artifact lookup requires an existing build.

- Failing function: `download build artifact`
  - Failure condition: No download object has `name={download}`.
  - Why it fails: The controller iterates the build’s downloads and finds no matching download object name.
  - Violated prerequisite or constraint: The download path value is not bound to persisted download metadata.

- Failing function: `download build artifact`
  - Failure condition: The download metadata exists but the storage file is missing or unreadable.
  - Why it fails: File metadata access throws `IOException`, converted to `DownloadFailed`.
  - Violated prerequisite or constraint: Metadata and filesystem storage are inconsistent.

Implementation notes:
- OpenAPI lists both `application/json` and `application/java-archive` for a `200` download response. In implementation, successful downloads are Java archive resources; JSON is used for errors.
- The file path is derived from visible names, not internal IDs.

---

### Behavior 9: Browse a Release Line and Download an Artifact

Business goal:
Allow a client starting from project discovery to browse a release group, choose a build, and download one artifact.

Domain context:
This is the complete consumer workflow for users who know only that they want an artifact from a project release line.

Starting point:
Pre-existing catalog and storage state: projects, version groups, versions, builds, download metadata, and matching files already exist. The client starts with no project, group, version, build, or download value.

Required execution workflow:
1. Use function `list available projects` (`GET /v2/projects`) with no values to obtain `projects[]`.
2. Use function `retrieve project information` (`GET /v2/projects/{project}`) with `project={projectNameFromProjects}` to obtain `version_groups[]`.
3. Use function `list builds for a version group` (`GET /v2/projects/{project}/version_group/{family}/builds`) with `project={sameProject}` and `family={versionGroupNameFromProject}` to obtain build entries containing `version`, `build`, and `downloads`.
4. Use function `download build artifact` (`GET /v2/projects/{project}/versions/{version}/builds/{build}/downloads/{download}`) with `project={sameProject}`, `version={versionFromSelectedGroupBuild}`, `build={buildFromSelectedGroupBuild}`, and `download={downloadsEntryNameFromSelectedGroupBuild}` to download the selected artifact.

Optional verification workflow:
1. Use function `retrieve version group information` (`GET /v2/projects/{project}/version_group/{family}`) with `project={sameProject}` and `family={sameFamily}` to inspect group membership.
2. Use function `retrieve build information` (`GET /v2/projects/{project}/versions/{version}/builds/{build}`) with `project={sameProject}`, `version={selectedVersion}`, and `build={selectedBuild}` to inspect the selected build before download.

Existing-state shortcuts:
- If the client already knows `project`, step 1 can be skipped.
- If the client already knows `family`, step 2 can be skipped.
- If the client already knows `version`, `build`, and `download`, steps 2 and 3 can be skipped and the workflow reduces to `download build artifact`.
- Direct database and filesystem setup can establish all required state, but generated `Project._id`, `VersionFamily._id`, and `Version._id` values must be linked consistently, and the storage path must match visible names.

Parameter and value bindings:
- `project` selected in step 1 is reused in every later step.
- `family` selected from `version_groups[]` scopes the group-build listing.
- The selected group-build item’s `version` and `build` values must be reused together in the download path.
- The selected download object’s `name` must be reused as `{download}`.
- The filesystem path must use the same visible `project`, `version`, `build`, and `download` values.

Business result:
The client obtains an artifact file from a build that belongs to a selected project release group. Catalog state is not changed.

Constraints and invariants:
- The API does not select a latest, stable, promoted, or recommended build; the client must choose from returned data.
- Promotion status and channel are metadata only; they do not affect download eligibility.
- Metadata existence does not guarantee file availability until the download function accesses storage.

Failure and exceptional cases:
- Failing function: `list available projects`
  - Failure condition: None visible; empty project list is successful.
  - Why it fails: No implementation-backed domain failure.
  - Violated prerequisite or constraint: None.

- Failing function: `list builds for a version group`
  - Failure condition: Selected project or group is absent.
  - Why it fails: Project or version-family lookup returns empty.
  - Violated prerequisite or constraint: The release line is not valid under the selected project.

- Failing function: `download build artifact`
  - Failure condition: Selected build metadata no longer exists, download metadata no longer contains the selected name, or the file is missing.
  - Why it fails: The function independently re-resolves project, version, build, download metadata, and filesystem state.
  - Violated prerequisite or constraint: Previously observed catalog values are stale or storage is inconsistent.

Implementation notes:
- This workflow is possible only because `list builds for a version group` returns enough data to choose a version, build number, and download name.
- There is no atomicity across metadata listing and later download; catalog or storage can change between calls.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Publish or Ingest a Project Release

Priority:
Critical domain gap

Expected business goal:
A maintainer should be able to publish a new project, version group, version, build, download metadata, and artifact file through the service.

Why it is unsupported:
All available functions are `GET` functions. No public function creates MongoDB records or stores artifact files.

Existing functions considered:
- `list available projects`: can reveal projects but cannot create them.
- `retrieve project information`: can read linked groups and versions but cannot create links.
- `list builds for a version`: can read build metadata but cannot add builds.
- `download build artifact`: can serve a file but cannot upload or register one.

Missing capability:
Missing create/publish endpoints, artifact upload/storage support, generated ID management, and transactional metadata/file persistence.

Proof that function composition is insufficient:
Chaining reads cannot create `Project._id`, `VersionFamily._id`, `Version._id`, `Build` rows, download metadata, or filesystem artifacts. Direct database and filesystem writes outside the API are not equivalent to a supported business publish workflow.

Evidence from existing functions/source:
All documented functions in `full-behavior.md` are `GET` operations. Controllers use repositories only for lookup/listing, not `save`.

Business impact:
The service can expose an existing catalog but cannot be the system of record for publishing releases.

---

### Missing Behavior 2: Maintain, Correct, or Delete Catalog Data

Priority:
Critical domain gap

Expected business goal:
A maintainer should be able to rename projects, move versions between groups, update build metadata, correct SHA-256 values, remove bad builds, or delete obsolete artifacts.

Why it is unsupported:
No update, patch, or delete functions exist for any first-class resource.

Existing functions considered:
- `retrieve build information`: exposes incorrect metadata if present but cannot repair it.
- `list builds for a version group`: reflects current grouping but cannot change membership.
- `download build artifact`: depends on existing metadata and storage but cannot repair either.

Missing capability:
Missing update/delete endpoints, ownership-safe mutation rules, cascading delete behavior, and metadata/file consistency management.

Proof that function composition is insufficient:
Read and download functions cannot alter persisted documents or remove files. Delete-and-recreate outside the API is not equivalent because the API provides no validation, transaction boundary, or cascade semantics.

Evidence from existing functions/source:
Repository interfaces inherit mutation methods from `MongoRepository`, but controllers do not expose them.

Business impact:
Bad releases, stale groups, incorrect hashes, and broken download entries cannot be fixed through the API.

---

### Missing Behavior 3: Publicly Verify Artifact Integrity

Priority:
Important robustness gap

Expected business goal:
A client should be able to verify that a served artifact matches the cataloged SHA-256 hash.

Why it is unsupported:
Build metadata exposes `sha256`, and download serves a file, but the server does not validate file contents against the stored hash during download and does not expose a dedicated verification function.

Existing functions considered:
- `retrieve build information`: returns expected hash metadata.
- `download build artifact`: serves the file but does not compute or compare SHA-256.

Missing capability:
Missing server-side checksum verification, integrity status, or guaranteed hash/file consistency.

Proof that function composition is insufficient:
A client can manually hash the downloaded file, but the service itself cannot prove or enforce that persisted metadata and storage content match. Chaining metadata retrieval and download only gives the client two values to compare externally.

Evidence from existing functions/source:
`DownloadController` checks metadata presence and file readability, then streams the file. It does not read and hash file contents.

Business impact:
The catalog can advertise a hash that does not match the served artifact, weakening release trust.

---

### Missing Behavior 4: Select Latest, Stable, Promoted, or Channel-Filtered Builds

Priority:
Important robustness gap

Expected business goal:
A client should be able to ask for the latest build, latest promoted build, stable/default-channel build, or experimental builds for a version or version group.

Why it is unsupported:
Build list functions return all matching builds without query filters or explicit sorting.

Existing functions considered:
- `list builds for a version`: returns all build metadata for one version.
- `list builds for a version group`: returns all build metadata for a group.
- `retrieve build information`: requires the caller to already know the build number.

Missing capability:
Missing query parameters or endpoints for latest selection, channel filtering, promotion filtering, and deterministic build ordering.

Proof that function composition is insufficient:
The client can filter returned data locally, but the service does not define authoritative ordering or selection semantics. Repository calls do not explicitly sort builds, so “latest” cannot be reliably derived from API contract alone.

Evidence from existing functions/source:
Build list controllers call repository find methods without `Sort`. `channel` and `promoted` are response fields only.

Business impact:
Automation clients cannot safely select recommended releases without embedding their own interpretation.

---

### Missing Behavior 5: Search Across Projects, Versions, Builds, or Changes

Priority:
API ergonomics gap

Expected business goal:
A user should be able to search for versions, builds, commits, change summaries, or artifacts across the catalog.

Why it is unsupported:
Every available function is scoped by known path values, except project listing.

Existing functions considered:
- `list available projects`: discovers only project IDs.
- `retrieve project information`: lists versions and groups only for one project.
- `list builds for a version`: requires known project and version.
- `retrieve build information`: requires known project, version, and build.

Missing capability:
Missing search endpoints, query parameters, commit lookup, artifact-name lookup, and cross-project indexes.

Proof that function composition is insufficient:
A client could crawl every project and version, but that is not equivalent to supported search: it is inefficient, not queryable by server-side criteria, and cannot search hidden or omitted fields beyond returned response data.

Evidence from existing functions/source:
No controller exposes query parameters for search. OpenAPI paths are all fixed path-based reads.

Business impact:
Catalog exploration and troubleshooting are cumbersome for clients that do not already know exact identifiers.

---

### Missing Behavior 6: Safe Conditional or Cache-Aware Artifact Retrieval

Priority:
API ergonomics gap

Expected business goal:
A client should be able to use documented validators such as ETag for efficient and safe repeated downloads.

Why it is unsupported:
OpenAPI documents an `ETag` header for downloads, but the implementation does not set one.

Existing functions considered:
- `download build artifact`: sets content disposition, content type, cache control, and last modified, but not ETag.

Missing capability:
Missing ETag generation and conditional request handling.

Proof that function composition is insufficient:
No existing function returns an artifact validator that can be reused in request headers. Clients can use `Last-Modified`, but that is not the documented ETag behavior and may not identify content uniquely.

Evidence from existing functions/source:
`DownloadController.JavaArchive.headersFor(...)` calls `setCacheControl`, `setContentDisposition`, `setContentType`, and `setLastModified`; it does not set ETag.

Business impact:
Clients relying on the OpenAPI contract may implement cache behavior that the service does not actually support.

## Cross-Behavior Observations

- The API is read-only for domain metadata; all catalog mutation is external to the documented API.
- There is no authentication or authorization visible in the controllers.
- Ownership/scoping is consistently enforced for reads by resolving project name to `Project._id`, then looking up child resources by internal IDs.
- Mongo indexes are present but not declared unique, so uniqueness is not guaranteed by the model annotations.
- Version and version-group names are constrained by the same version pattern, while project names are lowercase letters only.
- Build `channel` and `promoted` have response defaults but are not selection rules.
- Build and artifact metadata can become inconsistent with filesystem storage; only the download function detects missing/unreadable files.
- OpenAPI under-documents errors: implementation returns structured JSON errors for missing project, version/group, build, download, and failed file serving.
- OpenAPI and implementation disagree on download details: JSON is listed as a `200` media type, ETag is documented but not set, and download-name casing differs between path and schema.
- No workflow is transactional across catalog reads and artifact download; values can become stale between calls.

## Coverage Summary

Supported domain areas:
Project discovery, project catalog inspection, version-group inspection, version inspection, build listing, build detail retrieval, and artifact download.

Partially supported domain areas:
Release browsing is supported for existing data, but without authoritative latest/stable selection, filtering, search, or mutation. Artifact integrity is partially represented through stored SHA-256 metadata but not enforced by the server.

Unsupported domain areas:
Publishing releases, managing catalog lifecycle, uploading artifacts, correcting metadata, deleting resources, validating storage consistency, server-side search, promoted/latest selection, and safe conditional artifact retrieval are not supported by the available API functions.