### Function 1: list available projects

Function name:
list available projects

Core endpoint(s):
- `GET /v2/projects`

Preconditions:
- None. The function can execute against an empty database or a database with any number of `Project` rows.

Successful execution:
- Result:
  Returns the identifiers of all available projects.
- Invocation:
  Step 1: `GET /v2/projects` with no path, query, body, or form values
- Constraints:
  No specific resource must exist. The implementation reads all `Project` rows and maps each row to `Project.name`.

Failure or exceptional branches:
- None visible in the OpenAPI contract or controller implementation.

Endpoint coverage:
- Covers:
  `GET /v2/projects`
- Distinct meaning:
  Global project discovery.

### Function 2: retrieve project information

Function name:
retrieve project information

Core endpoint(s):
- `GET /v2/projects/{project}`

Preconditions:
- A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
- Any version group names returned by this function come from `VersionFamily` rows whose `project` field equals the resolved `Project._id`. This can be satisfied by directly inserting linked `VersionFamily` rows; no documented endpoint exists to create version-group state.
- Any version names returned by this function come from `Version` rows whose `project` field equals the resolved `Project._id`. This can be satisfied by directly inserting linked `Version` rows; no documented endpoint exists to create version state.

Successful execution:
- Result:
  Retrieves the project named `{project}`, including its friendly name, version groups, and versions.
- Invocation:
  Step 1: `GET /v2/projects/{project}` with path value `project={project}`
- Constraints:
  `{project}` must match `[a-z]+` and must equal an existing `Project.name`. The response `project_id` is the same project identifier. `version_groups` and `versions` are read from records linked to that project.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No project named `{project}` exists in the database. This can be produced by starting from an empty database, deleting the project row beforehand, or intentionally not inserting a `Project` row with `name = {project}`.
  - Failure endpoint:
    `GET /v2/projects/{project}`
  - Why this fails:
    `ProjectCollection.findByName({project})` is empty, so the controller throws `ProjectNotFound` and returns `404` with `{"error":"Project not found."}`.
  - Intentionally violated constraints:
    `{project}` is syntactically valid but does not identify a persisted project.

Endpoint coverage:
- Covers:
  `GET /v2/projects/{project}`
- Distinct meaning:
  Reads one project summary and its available version/version-group names.

### Function 3: retrieve version group information

Function name:
retrieve version group information

Core endpoint(s):
- `GET /v2/projects/{project}/version_group/{family}`

Preconditions:
- A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
- A version group named `{family}` exists for project `{project}`. This can be satisfied by directly inserting a `VersionFamily` row with `name = {family}` and `project = Project._id`; no documented endpoint exists to create version-group state.
- Any version names returned for the group come from `Version` rows whose `project` field equals the resolved `Project._id` and whose `group` field equals the resolved `VersionFamily._id`. This can be satisfied by directly inserting linked `Version` rows; no documented endpoint exists to create version state.
- The generated `Project._id` and `VersionFamily._id` values must be reused consistently when inserting linked version rows.

Successful execution:
- Result:
  Retrieves the version group `{family}` for project `{project}`, including the versions in that group.
- Invocation:
  Step 1: `GET /v2/projects/{project}/version_group/{family}` with path values `project={project}` and `family={family}`
- Constraints:
  `{project}` must match `[a-z]+` and identify an existing project. `{family}` must match `[0-9.]+-?(?:pre|SNAPSHOT)?(?:[0-9.]+)?` and identify a `VersionFamily` whose `project` field matches the resolved project `_id`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No project named `{project}` exists in the database. This can be produced by starting from an empty database, deleting the project row beforehand, or intentionally not inserting a `Project` row with `name = {project}`.
  - Failure endpoint:
    `GET /v2/projects/{project}/version_group/{family}`
  - Why this fails:
    The project lookup fails before the version-group lookup, returning `404` with `{"error":"Project not found."}`.
  - Intentionally violated constraints:
    `{project}` is syntactically valid but absent from persisted projects.
- Branch 2:
  - Preconditions:
    - A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
    - No `VersionFamily` row exists with `project = Project._id` and `name = {family}`. This can be produced by omitting the version-group row, using a different `name`, or inserting `{family}` under another project's generated `_id`.
  - Failure endpoint:
    `GET /v2/projects/{project}/version_group/{family}`
  - Why this fails:
    `VersionFamilyCollection.findByProjectAndName(project._id, {family})` is empty, so the controller returns `404` with `{"error":"Version not found."}`.
  - Intentionally violated constraints:
    `{family}` is syntactically valid but does not match a persisted version group under `{project}`.

Endpoint coverage:
- Covers:
  `GET /v2/projects/{project}/version_group/{family}`
- Distinct meaning:
  Reads one project version group and its contained version names.

### Function 4: list builds for a version group

Function name:
list builds for a version group

Core endpoint(s):
- `GET /v2/projects/{project}/version_group/{family}/builds`

Preconditions:
- A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
- A version group named `{family}` exists for project `{project}`. This can be satisfied by directly inserting a `VersionFamily` row with `name = {family}` and `project = Project._id`; no documented endpoint exists to create version-group state.
- Builds returned for this group come from `Build` rows whose `project` field equals the resolved `Project._id` and whose `version` field is one of the `Version._id` values for versions linked to the resolved `VersionFamily._id`. This can be satisfied by directly inserting linked `Version` and `Build` rows; no documented endpoint exists to create version or build state.
- The generated `Project._id`, `VersionFamily._id`, `Version._id`, and `Build.version` values must be scoped consistently. Empty version or build lists are still successful if the project and version group exist.

Successful execution:
- Result:
  Lists builds for all versions inside version group `{family}` of project `{project}`.
- Invocation:
  Step 1: `GET /v2/projects/{project}/version_group/{family}/builds` with path values `project={project}` and `family={family}`
- Constraints:
  `{project}` must identify an existing project. `{family}` must identify an existing `VersionFamily` for that project. The implementation first loads versions whose `project` and `group` match, then loads builds whose `project` matches and whose `version` is one of those version ids.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No project named `{project}` exists in the database. This can be produced by starting from an empty database, deleting the project row beforehand, or intentionally not inserting a `Project` row with `name = {project}`.
  - Failure endpoint:
    `GET /v2/projects/{project}/version_group/{family}/builds`
  - Why this fails:
    The project lookup fails, returning `404` with `{"error":"Project not found."}`.
  - Intentionally violated constraints:
    `{project}` is syntactically valid but absent.
- Branch 2:
  - Preconditions:
    - A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
    - No `VersionFamily` row exists with `project = Project._id` and `name = {family}`. This can be produced by omitting the version-group row, using a different `name`, or inserting `{family}` under another project's generated `_id`.
  - Failure endpoint:
    `GET /v2/projects/{project}/version_group/{family}/builds`
  - Why this fails:
    The version-group lookup fails, returning `404` with `{"error":"Version not found."}`.
  - Intentionally violated constraints:
    `{family}` is not a persisted version group under `{project}`.

Endpoint coverage:
- Covers:
  `GET /v2/projects/{project}/version_group/{family}/builds`
- Distinct meaning:
  Reads build metadata across all versions in a version group.

### Function 5: retrieve version information

Function name:
retrieve version information

Core endpoint(s):
- `GET /v2/projects/{project}/versions/{version}`

Preconditions:
- A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
- A version named `{version}` exists for project `{project}`. This can be satisfied by directly inserting a `Version` row with `name = {version}` and `project = Project._id`; no documented endpoint exists to create version state.
- Build numbers returned by this function come from `Build` rows whose `project` field equals the resolved `Project._id` and whose `version` field equals the resolved `Version._id`. This can be satisfied by directly inserting linked `Build` rows; no documented endpoint exists to create build state.
- The generated `Project._id` and `Version._id` values must be reused consistently when inserting linked build rows.

Successful execution:
- Result:
  Retrieves version `{version}` for project `{project}`, including its available build numbers.
- Invocation:
  Step 1: `GET /v2/projects/{project}/versions/{version}` with path values `project={project}` and `version={version}`
- Constraints:
  `{project}` must identify an existing project. `{version}` must match `[0-9.]+-?(?:pre|SNAPSHOT)?(?:[0-9.]+)?` and identify a `Version` whose `project` field matches the resolved project `_id`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No project named `{project}` exists in the database. This can be produced by starting from an empty database, deleting the project row beforehand, or intentionally not inserting a `Project` row with `name = {project}`.
  - Failure endpoint:
    `GET /v2/projects/{project}/versions/{version}`
  - Why this fails:
    The project lookup fails, returning `404` with `{"error":"Project not found."}`.
  - Intentionally violated constraints:
    `{project}` is syntactically valid but absent.
- Branch 2:
  - Preconditions:
    - A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
    - No `Version` row exists with `project = Project._id` and `name = {version}`. This can be produced by omitting the version row, using a different `name`, or inserting `{version}` under another project's generated `_id`.
  - Failure endpoint:
    `GET /v2/projects/{project}/versions/{version}`
  - Why this fails:
    `VersionCollection.findByProjectAndName(project._id, {version})` is empty, returning `404` with `{"error":"Version not found."}`.
  - Intentionally violated constraints:
    `{version}` is syntactically valid but absent under `{project}`.

Endpoint coverage:
- Covers:
  `GET /v2/projects/{project}/versions/{version}`
- Distinct meaning:
  Reads one version summary and its build numbers.

### Function 6: list builds for a version

Function name:
list builds for a version

Core endpoint(s):
- `GET /v2/projects/{project}/versions/{version}/builds`

Preconditions:
- A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
- A version named `{version}` exists for project `{project}`. This can be satisfied by directly inserting a `Version` row with `name = {version}` and `project = Project._id`; no documented endpoint exists to create version state.
- Builds returned by this function come from `Build` rows whose `project` field equals the resolved `Project._id` and whose `version` field equals the resolved `Version._id`. This can be satisfied by directly inserting linked `Build` rows with `number`, `time`, `changes`, `downloads`, and optional `channel` and `promoted` values; no documented endpoint exists to create build state.
- The generated `Project._id` and `Version._id` values must be reused consistently when inserting build rows. A version with no builds can still return a successful empty list.

Successful execution:
- Result:
  Lists build metadata for version `{version}` of project `{project}`.
- Invocation:
  Step 1: `GET /v2/projects/{project}/versions/{version}/builds` with path values `project={project}` and `version={version}`
- Constraints:
  `{project}` must identify an existing project. `{version}` must identify an existing version for that project. The response contains build number, time, channel, promotion flag, changes, and downloads for each matching build.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No project named `{project}` exists in the database. This can be produced by starting from an empty database, deleting the project row beforehand, or intentionally not inserting a `Project` row with `name = {project}`.
  - Failure endpoint:
    `GET /v2/projects/{project}/versions/{version}/builds`
  - Why this fails:
    The project lookup fails, returning `404` with `{"error":"Project not found."}`.
  - Intentionally violated constraints:
    `{project}` is syntactically valid but absent.
- Branch 2:
  - Preconditions:
    - A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
    - No `Version` row exists with `project = Project._id` and `name = {version}`. This can be produced by omitting the version row, using a different `name`, or inserting `{version}` under another project's generated `_id`.
  - Failure endpoint:
    `GET /v2/projects/{project}/versions/{version}/builds`
  - Why this fails:
    The version lookup fails, returning `404` with `{"error":"Version not found."}`.
  - Intentionally violated constraints:
    `{version}` is absent under `{project}`.

Endpoint coverage:
- Covers:
  `GET /v2/projects/{project}/versions/{version}/builds`
- Distinct meaning:
  Reads all builds for one project version.

### Function 7: retrieve build information

Function name:
retrieve build information

Core endpoint(s):
- `GET /v2/projects/{project}/versions/{version}/builds/{build}`

Preconditions:
- A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
- A version named `{version}` exists for project `{project}`. This can be satisfied by directly inserting a `Version` row with `name = {version}` and `project = Project._id`; no documented endpoint exists to create version state.
- A build numbered `{build}` exists for version `{version}` of project `{project}`. This can be satisfied by directly inserting a `Build` row with `project = Project._id`, `version = Version._id`, `number = {build}`, `time`, `changes`, `downloads`, and optional `channel` and `promoted` values; no documented endpoint exists to create build state.
- The generated `Project._id` and `Version._id` values must be reused in the `Build.project` and `Build.version` fields. A build with the same number under another project or version does not satisfy this function.

Successful execution:
- Result:
  Retrieves metadata for build `{build}` of version `{version}` in project `{project}`.
- Invocation:
  Step 1: `GET /v2/projects/{project}/versions/{version}/builds/{build}` with path values `project={project}`, `version={version}`, and `build={build}`
- Constraints:
  `{project}` must identify an existing project. `{version}` must identify an existing version for that project. `{build}` must match `\d+` and equal a persisted `Build.number` whose `project` and `version` fields match the resolved project and version ids.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No project named `{project}` exists in the database. This can be produced by starting from an empty database, deleting the project row beforehand, or intentionally not inserting a `Project` row with `name = {project}`.
  - Failure endpoint:
    `GET /v2/projects/{project}/versions/{version}/builds/{build}`
  - Why this fails:
    The project lookup fails, returning `404` with `{"error":"Project not found."}`.
  - Intentionally violated constraints:
    `{project}` is absent.
- Branch 2:
  - Preconditions:
    - A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
    - No `Version` row exists with `project = Project._id` and `name = {version}`. This can be produced by omitting the version row, using a different `name`, or inserting `{version}` under another project's generated `_id`.
  - Failure endpoint:
    `GET /v2/projects/{project}/versions/{version}/builds/{build}`
  - Why this fails:
    The version lookup fails, returning `404` with `{"error":"Version not found."}`.
  - Intentionally violated constraints:
    `{version}` is absent under `{project}`.
- Branch 3:
  - Preconditions:
    - A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
    - A version named `{version}` exists for project `{project}`. This can be satisfied by directly inserting a `Version` row with `name = {version}` and `project = Project._id`; no documented endpoint exists to create version state.
    - No `Build` row exists with `project = Project._id`, `version = Version._id`, and `number = {build}`. This can be produced by omitting the build row, using a different build number, or inserting build number `{build}` under another project or version id.
  - Failure endpoint:
    `GET /v2/projects/{project}/versions/{version}/builds/{build}`
  - Why this fails:
    `BuildCollection.findByProjectAndVersionAndNumber(...)` is empty, returning `404` with `{"error":"Build not found."}`.
  - Intentionally violated constraints:
    `{build}` is syntactically valid but not a persisted build number for `{project}` and `{version}`.

Endpoint coverage:
- Covers:
  `GET /v2/projects/{project}/versions/{version}/builds/{build}`
- Distinct meaning:
  Reads one build's detailed metadata.

### Function 8: download build artifact

Function name:
download build artifact

Core endpoint(s):
- `GET /v2/projects/{project}/versions/{version}/builds/{build}/downloads/{download}`

Preconditions:
- A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
- A version named `{version}` exists for project `{project}`. This can be satisfied by directly inserting a `Version` row with `name = {version}` and `project = Project._id`; no documented endpoint exists to create version state.
- A build numbered `{build}` exists for version `{version}` of project `{project}`. This can be satisfied by directly inserting a `Build` row with `project = Project._id`, `version = Version._id`, `number = {build}`, `time`, `changes`, and `downloads`; no documented endpoint exists to create build state.
- A download object with `name = {download}` exists in the build's `downloads` map. This can be satisfied by directly inserting the build with a `downloads` map entry whose `Build.Download.name` is `{download}` and whose `sha256` is the expected artifact hash; no documented endpoint exists to create download metadata.
- A readable file exists at `storagePath/{project}/{version}/{build}/{download}`. This must be satisfied through filesystem or storage setup outside the documented API.
- The generated `Project._id` and `Version._id` values must be reused in the `Build.project` and `Build.version` fields. The `{download}` path value must match the download object's `name`; the implementation does not rely on the `downloads` map key.

Successful execution:
- Result:
  Downloads the artifact named `{download}` for build `{build}` of version `{version}` in project `{project}` as an attachment with `application/java-archive`.
- Invocation:
  Step 1: `GET /v2/projects/{project}/versions/{version}/builds/{build}/downloads/{download}` with path values `project={project}`, `version={version}`, `build={build}`, and `download={download}`
- Constraints:
  `{project}`, `{version}`, and `{build}` must resolve to existing project, version, and build records. `{download}` must match `[a-zA-Z0-9._-]+` and must equal a `Build.Download.name` value in the build's `downloads` map. The file must exist and be readable at `storagePath/{project}/{version}/{build}/{download}`. OpenAPI lists both `application/json` and `application/java-archive` for a `200` response, but the implementation's successful response is the Java archive resource; JSON is used for errors.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No project named `{project}` exists in the database. This can be produced by starting from an empty database, deleting the project row beforehand, or intentionally not inserting a `Project` row with `name = {project}`.
  - Failure endpoint:
    `GET /v2/projects/{project}/versions/{version}/builds/{build}/downloads/{download}`
  - Why this fails:
    The project lookup fails, returning `404` with `{"error":"Project not found."}`.
  - Intentionally violated constraints:
    `{project}` is absent.
- Branch 2:
  - Preconditions:
    - A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
    - No `Version` row exists with `project = Project._id` and `name = {version}`. This can be produced by omitting the version row, using a different `name`, or inserting `{version}` under another project's generated `_id`.
  - Failure endpoint:
    `GET /v2/projects/{project}/versions/{version}/builds/{build}/downloads/{download}`
  - Why this fails:
    The version lookup fails, returning `404` with `{"error":"Version not found."}`.
  - Intentionally violated constraints:
    `{version}` is absent under `{project}`.
- Branch 3:
  - Preconditions:
    - A project named `{project}` exists in the database. This can be satisfied by directly inserting a `Project` row with `name = {project}` and a generated `_id`; no documented endpoint exists to create this project state.
    - A version named `{version}` exists for project `{project}`. This can be satisfied by directly inserting a `Version` row with `name = {version}` and `project = Project._id`; no documented endpoint exists to create version state.
    - No `Build` row exists with `project = Project._id`, `version = Version._id`, and `number = {build}`. This can be produced by omitting the build row, using a different build number, or inserting build number `{build}` under another project or version id.
  - Failure endpoint:
    `GET /v2/projects/{project}/versions/{version}/builds/{build}/downloads/{download}`
  - Why this fails:
    The build lookup fails, returning `404` with `{"error":"Build not found."}`.
  - Intentionally violated constraints:
    `{build}` is absent for `{project}` and `{version}`.
- Branch 4:
  - Preconditions:
    - Build metadata for `{project}`, `{version}`, and `{build}` is retrievable. This can be satisfied by directly inserting matching `Project`, `Version`, and `Build` rows, or by confirming that `GET /v2/projects/{project}/versions/{version}/builds/{build}` with the same path values returns a build representation.
    - No download object in that build has `name = {download}`. This can be produced by omitting the download entry, using a different `Build.Download.name`, or choosing a `{download}` value that is not equal to any returned `downloads.*.name` from the build metadata response.
  - Failure endpoint:
    `GET /v2/projects/{project}/versions/{version}/builds/{build}/downloads/{download}`
  - Why this fails:
    The controller iterates over the build's `downloads` map and compares `{download}` against each download object's `name`. Because no object has `name == {download}`, it returns `404` with `{"error":"Download not found."}`.
  - Intentionally violated constraints:
    `{download}` is not reused from the build's download object names.
- Branch 5:
  - Preconditions:
    - Build metadata for `{project}`, `{version}`, and `{build}` is retrievable. This can be satisfied by directly inserting matching `Project`, `Version`, and `Build` rows, or by confirming that `GET /v2/projects/{project}/versions/{version}/builds/{build}` with the same path values returns a build representation.
    - A download object with `name = {download}` exists in the build's `downloads` map. This can be satisfied by directly inserting the build with that download metadata, or by reusing a `downloads.*.name` value returned by the build metadata endpoint.
    - The storage file at `storagePath/{project}/{version}/{build}/{download}` is missing, unreadable, or otherwise cannot provide file metadata. This can be produced by omitting the file from direct storage setup or by making the file inaccessible while leaving the MongoDB download metadata intact.
  - Failure endpoint:
    `GET /v2/projects/{project}/versions/{version}/builds/{build}/downloads/{download}`
  - Why this fails:
    After finding the download entry, `JavaArchive` reads file metadata from storage. An `IOException` is converted to `DownloadFailed`, returning `500` with `{"error":"An internal error occurred while serving your download."}`.
  - Intentionally violated constraints:
    The persisted download metadata points to a file path that is missing or not readable.

Endpoint coverage:
- Covers:
  `GET /v2/projects/{project}/versions/{version}/builds/{build}/downloads/{download}`
- Distinct meaning:
  Serves one concrete build artifact file.
