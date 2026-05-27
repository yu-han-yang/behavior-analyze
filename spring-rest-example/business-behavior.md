# Domain-Level Behavior Analysis

## Domain Summary
The service manages a small project-planning domain. The main resources are:

- `Project`: a named workspace identified by a server-generated six-character `code`.
- `Problem`: a task/problem linked to a project through the project `code`.
- `Subproblem`: a smaller item linked to a problem through the problem `idx`.

The API is code-centric for projects and problems, but problem and subproblem creation rely on generated database ids that are not returned by create endpoints. This makes several workflows dependent on direct database lookup, test fixtures, or out-of-band access to generated values.

## Available Function Inventory

### Project management
- `create project`
  - Core endpoint(s): `POST /api/project`
  - Domain meaning: Creates a project with a caller-supplied `title` and a server-generated `code`.

- `retrieve project by code`
  - Core endpoint(s): `GET /api/project/{code}`
  - Domain meaning: Retrieves one project by its persisted project code.

- `update project title`
  - Core endpoint(s): `PUT /api/project/{code}`
  - Domain meaning: Replaces the title of an existing project identified by code.

- `delete project`
  - Core endpoint(s): `DELETE /api/project/{code}`
  - Domain meaning: Deletes a project by code, with JPA cascade behavior for associated problems when deletion is entity-managed.

### Problem management
- `create problem for project`
  - Core endpoint(s): `POST /api/problem/{code}`
  - Domain meaning: Adds a problem to the project identified by code.

- `retrieve problems for project code`
  - Core endpoint(s): `GET /api/problem/{code}`
  - Domain meaning: Lists problem rows linked to a project code.

- `delete problem rows for code`
  - Core endpoint(s): `DELETE /api/problem/{code}`
  - Domain meaning: Performs native bulk deletion of problem rows with the given project code.

- `delete problems and subproblems for code`
  - Core endpoint(s): `DELETE /api/problem/{code}/all`
  - Domain meaning: Attempts to delete subproblems for a project-code-selected problem, then delete problems with that code.

### Subproblem management
- `create subproblem for problem`
  - Core endpoint(s): `POST /api/subproblem`
  - Domain meaning: Adds a subproblem to an existing problem by `pro_idx`.

## Supported Business Behaviors

### Behavior 1: Create A Project

Business goal:
Create a project workspace with a business title.

Domain context:
A project is the top-level container for problems. Other domain work depends on a valid project code.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create project` (`POST /api/project`) with JSON body `title="Release Planning Board"` to create a new project. Any supplied body `code` is ignored by the implementation.

Optional verification workflow:
1. Use function `retrieve project by code` (`GET /api/project/{code}`) with `code={generatedProjectCode}` to inspect the created project, but only if the generated code has been obtained outside the create response.

Existing-state shortcuts:
- None for the core create action. Direct database insertion of a `Project` row with `title` and `code` can create equivalent state for later workflows, but it is not equivalent to exercising project creation.
- If direct database setup is used, `code` must be unique, non-null, and no longer than six characters.

Parameter and value bindings:
- The request body `title` becomes the persisted project title.
- The persisted `code` is generated server-side and must be reused later as `{code}` in `retrieve project by code`, `update project title`, `delete project`, and all problem functions.
- The generated code is not returned in the response body.

Business result:
A new `Project` row exists with the supplied title and a generated six-character code. The response is `201 Created` with an empty body.

Constraints and invariants:
- `title` must be non-null and at least five characters for the create endpoint.
- The entity also requires `title` non-null, max length 200.
- `code` is unique and max length six.
- Caller-supplied `code` is ignored.

Failure and exceptional cases:
- Failing function: `create project`
  - Failure condition: `title` shorter than five characters.
  - Why it fails: The controller checks `title.length() < 5`.
  - Violated prerequisite or constraint: Project title minimum length.
- Failing function: `create project`
  - Failure condition: `title` is omitted or null.
  - Why it fails: The controller calls `title.length()` before null handling, causing an unhandled null dereference.
  - Violated prerequisite or constraint: Required non-null title.
- Failing function: `create project`
  - Failure condition: Generated `code` collides with an existing row.
  - Why it fails: Database uniqueness on `Project.code`.
  - Violated prerequisite or constraint: Unique project code.

Implementation notes:
OpenAPI documents `200 OK`, but the implementation returns `201 Created`. The `Location` header is built from the current request and does not expose the generated code.

### Behavior 2: Retrieve A Project By Code

Business goal:
Inspect an existing project workspace.

Domain context:
Project code is the public handle used to find the project and scope problem operations.

Starting point:
Existing project state with known `code`. A fully API-only setup from no prior state is not possible because `create project` does not return the generated code.

Required execution workflow:
1. Use function `retrieve project by code` (`GET /api/project/{code}`) with `code="abc123"` to retrieve the project.

Optional verification workflow:
None.

Existing-state shortcuts:
- If a project row already exists with `code="abc123"`, no setup function is needed.
- Direct database setup can insert `Project(code="abc123", title="Release Planning Board")`.
- If the project was created through `create project`, the generated code must be obtained out-of-band before this behavior can be executed.

Parameter and value bindings:
- The path value `code="abc123"` must exactly match the persisted `Project.code`.

Business result:
The matching project is returned and no state changes.

Constraints and invariants:
- `code` must be at least six characters.
- OpenAPI describes a six-character alphanumeric code, but implementation only enforces length at least six.

Failure and exceptional cases:
- Failing function: `retrieve project by code`
  - Failure condition: `code` is shorter than six characters.
  - Why it fails: Controller throws `DataFormatException`.
  - Violated prerequisite or constraint: Project code format.
- Failing function: `retrieve project by code`
  - Failure condition: No project has the supplied code.
  - Why it fails: Repository returns null and controller throws `ResourceNotFoundException`.
  - Violated prerequisite or constraint: Existing project with matching code.

Implementation notes:
The lookup is by native query on `project.code`.

### Behavior 3: Rename A Project

Business goal:
Change the title of an existing project while preserving its code.

Domain context:
A project title is mutable business metadata; the code remains the stable identifier for later operations.

Starting point:
Existing project state with known `code`.

Required execution workflow:
1. Use function `update project title` (`PUT /api/project/{code}`) with `code="abc123"` and JSON body `title="Updated Release Planning Board"` to replace the project title.

Optional verification workflow:
1. Use function `retrieve project by code` (`GET /api/project/{code}`) with `code="abc123"` to verify the title changed.

Existing-state shortcuts:
- If the project already exists with the target code, no setup function is needed.
- Direct database setup can insert the project before update.
- If created through `create project`, the generated code must still be obtained out-of-band.

Parameter and value bindings:
- The same `code` scopes both update and verification.
- The body `title` replaces the persisted `Project.title`; body `code` is not used for lookup.

Business result:
The existing project remains under the same code, but its title changes to the supplied value.

Constraints and invariants:
- `code` must be at least six characters.
- Update does not enforce the create endpoint’s five-character title minimum.
- Persistence still requires non-null title and max length 200.

Failure and exceptional cases:
- Failing function: `update project title`
  - Failure condition: `code` shorter than six characters.
  - Why it fails: Controller rejects the code format.
  - Violated prerequisite or constraint: Project code format.
- Failing function: `update project title`
  - Failure condition: No project exists for `code`.
  - Why it fails: Controller looks up the project before saving.
  - Violated prerequisite or constraint: Existing target project.
- Failing function: `update project title`
  - Failure condition: Body `title` is null or persistence-invalid.
  - Why it fails: The invalid title is assigned and flushed.
  - Violated prerequisite or constraint: Project title persistence rules.

Implementation notes:
The implementation prioritizes path `code`; any body `code` is irrelevant.

### Behavior 4: Delete A Project

Business goal:
Remove a project workspace.

Domain context:
Deleting a project is the top-level cleanup operation for a project container.

Starting point:
Existing project state with known `code`.

Required execution workflow:
1. Use function `delete project` (`DELETE /api/project/{code}`) with `code="abc123"` to delete the project.

Optional verification workflow:
1. Use function `retrieve project by code` (`GET /api/project/{code}`) with `code="abc123"` to verify it is no longer retrievable.

Existing-state shortcuts:
- If the project already exists, no setup function is needed.
- Direct database setup can insert the project before deletion.
- If equivalent project state does not exist, the delete may behave as a no-op success depending on repository behavior.

Parameter and value bindings:
- The delete path `code` must match the persisted project code.
- Verification reuses the same `code`.

Business result:
The project row is removed. Entity-managed deletion can cascade to associated problem rows and, through problem relationships, associated subproblem rows.

Constraints and invariants:
- `code` must be at least six characters.
- The controller does not fetch the project before deletion.
- Deleting by code does not report affected-row count.

Failure and exceptional cases:
- Failing function: `delete project`
  - Failure condition: `code` shorter than six characters.
  - Why it fails: Controller throws `DataFormatException`.
  - Violated prerequisite or constraint: Project code format.
- Failing function: `delete project`
  - Failure condition: No project exists for `code`.
  - Why it fails: The service only notices exceptions; zero-row delete may not throw.
  - Violated prerequisite or constraint: Existing target project. Implementation may still return `200 OK`.

Implementation notes:
This endpoint may report success for a missing project if the repository delete affects zero rows without throwing.

### Behavior 5: Add A Problem To A Project

Business goal:
Record a problem/task inside a project.

Domain context:
Problems are child work items under a project code.

Starting point:
Existing project state with known `code`.

Required execution workflow:
1. Use function `create problem for project` (`POST /api/problem/{code}`) with `code="abc123"` and JSON body `title="Design API workflow"` to create a problem under that project.

Optional verification workflow:
1. Use function `retrieve problems for project code` (`GET /api/problem/{code}`) with `code="abc123"` to inspect the project’s problems.

Existing-state shortcuts:
- If the project already exists with known code, project setup can be skipped.
- Direct database setup can insert the project.
- If using `create project`, the generated code must be obtained outside the API response before problem creation.

Parameter and value bindings:
- The path `code` selects the parent project.
- The body `title` becomes the problem title.
- Any body project linkage is overwritten by the project found from the path code.
- Verification reuses the same project `code`.

Business result:
A `Problem` row exists and is linked to the project whose code is `abc123`.

Constraints and invariants:
- `code` must be at least six characters.
- Problem title must be non-null.
- Multiple problems can be created for the same project code; no uniqueness is enforced.

Failure and exceptional cases:
- Failing function: `create problem for project`
  - Failure condition: `code` shorter than six characters.
  - Why it fails: Controller rejects invalid code format.
  - Violated prerequisite or constraint: Project code format.
- Failing function: `create problem for project`
  - Failure condition: No project exists for `code`.
  - Why it fails: Controller must resolve the parent project before saving.
  - Violated prerequisite or constraint: Existing parent project.
- Failing function: `create problem for project`
  - Failure condition: Body `title` is null.
  - Why it fails: Service returns false when `problem.getTitle() == null`.
  - Violated prerequisite or constraint: Required problem title.
- Failing function: `retrieve problems for project code`
  - Failure condition: Verification is run before any problem exists.
  - Why it fails: Empty result throws `ElementNullException`.
  - Violated prerequisite or constraint: At least one problem for the code.

Implementation notes:
OpenAPI documents `200 OK`, but implementation returns `201 Created` with no body. The problem `idx` is not returned by creation.

### Behavior 6: List Problems For A Project

Business goal:
View the problems belonging to a project.

Domain context:
This is the only API-visible way to discover problem `idx` values for subproblem creation.

Starting point:
Existing project state with known `code` and at least one problem linked to that code.

Required execution workflow:
1. Use function `retrieve problems for project code` (`GET /api/problem/{code}`) with `code="abc123"` to retrieve problems for the project.

Optional verification workflow:
None.

Existing-state shortcuts:
- If problems already exist for `code`, no setup function is needed.
- Direct database setup can insert project and problem rows.
- API setup requires `create problem for project` first, but that itself requires a known project code.

Parameter and value bindings:
- The path `code` is matched against problem rows through their project-code foreign key.
- Returned problem `idx` values can be reused as `pro_idx` in `create subproblem for problem`.

Business result:
A collection of problem rows is returned. No state changes.

Constraints and invariants:
- `code` must be at least six characters.
- The endpoint does not separately verify project existence; it only checks whether problems exist.

Failure and exceptional cases:
- Failing function: `retrieve problems for project code`
  - Failure condition: `code` shorter than six characters.
  - Why it fails: Controller throws `DataFormatException`.
  - Violated prerequisite or constraint: Project code format.
- Failing function: `retrieve problems for project code`
  - Failure condition: No problem rows match `code`.
  - Why it fails: Empty collection throws `ElementNullException`, mapped to `501 Not Implemented`.
  - Violated prerequisite or constraint: Existing problem collection for the code.

Implementation notes:
A missing project and an existing project with no problems are not distinguished; both appear as an empty problem query.

### Behavior 7: Delete Problem Rows For A Project Code

Business goal:
Remove all problem rows associated with a project code.

Domain context:
Despite the OpenAPI summary implying recent-problem deletion, the implementation performs bulk deletion by code.

Starting point:
Existing project state with known `code` and one or more problem rows for that code.

Required execution workflow:
1. Use function `delete problem rows for code` (`DELETE /api/problem/{code}`) with `code="abc123"` to delete problem rows for that code.

Optional verification workflow:
1. Use function `retrieve problems for project code` (`GET /api/problem/{code}`) with `code="abc123"` to verify no problems are listed; the expected absence response is `501 Not Implemented`, not an empty `200 OK`.

Existing-state shortcuts:
- If matching problem rows already exist, setup can be skipped.
- Direct database setup can insert project and problem rows.
- The core delete action cannot be skipped.

Parameter and value bindings:
- The same project `code` scopes deletion and verification.
- The delete does not use individual problem `idx`.

Business result:
Native SQL attempts to delete all `problem` rows where `code="abc123"`. The project row itself remains.

Constraints and invariants:
- `code` must be at least six characters.
- This delete does not clean subproblems first.
- A problem with dependent subproblems can cause a foreign-key failure, but the controller ignores the service result.

Failure and exceptional cases:
- Failing function: `delete problem rows for code`
  - Failure condition: `code` shorter than six characters.
  - Why it fails: Controller rejects code format.
  - Violated prerequisite or constraint: Project code format.
- Failing function: `delete problem rows for code`
  - Failure condition: Matching problems have dependent subproblems.
  - Why it fails: Native problem delete can violate foreign-key constraints.
  - Violated prerequisite or constraint: Child subproblems must be removed first for clean deletion.
- Failing function: `delete problem rows for code`
  - Failure condition: No matching problems exist.
  - Why it fails: Domain expectation would be target absence, but implementation can still return `200 OK` as a no-op.
  - Violated prerequisite or constraint: Existing target problem rows.

Implementation notes:
The controller always returns `200 OK` after format validation, even if the service caught a delete exception.

### Behavior 8: Delete Problems And Subproblems For A Project Code

Business goal:
Clean up a project’s problem hierarchy before or during project cleanup.

Domain context:
This is the only endpoint that attempts to remove subproblems before deleting problems.

Starting point:
Existing project state with known `code`, exactly one problem for that code, and zero or more subproblems attached to that problem.

Required execution workflow:
1. Use function `delete problems and subproblems for code` (`DELETE /api/problem/{code}/all`) with `code="abc123"` to delete subproblems for the selected problem and then delete problems for the code.

Optional verification workflow:
1. Use function `retrieve problems for project code` (`GET /api/problem/{code}`) with `code="abc123"` to verify no problem rows remain; absence is reported as `501 Not Implemented`.

Existing-state shortcuts:
- If the project/problem/subproblem state already exists, setup can be skipped.
- Direct database setup can insert one project, one problem with `code="abc123"`, and subproblems with `pro_idx={problemIdx}`.
- If multiple problems exist for the same code, this shortcut is unsafe because the delete query assumes one problem id.

Parameter and value bindings:
- The path `code` selects problem rows.
- Internally, subproblem deletion uses `pro_idx = (SELECT idx FROM problem WHERE code=?1)`, so the project `code` must map to a single problem `idx`.

Business result:
Subproblem rows for the selected problem are deleted first, then problem rows with the project code are deleted. The project row remains.

Constraints and invariants:
- `code` must be at least six characters.
- The subproblem delete assumes a single problem per project code, but the create function allows multiple problems per code.
- The endpoint does not inspect affected-row counts.

Failure and exceptional cases:
- Failing function: `delete problems and subproblems for code`
  - Failure condition: `code` shorter than six characters.
  - Why it fails: Controller throws `DataFormatException`.
  - Violated prerequisite or constraint: Project code format.
- Failing function: `delete problems and subproblems for code`
  - Failure condition: More than one problem exists for the same code.
  - Why it fails: The scalar subquery for problem `idx` can return multiple rows.
  - Violated prerequisite or constraint: One-problem-per-code assumption.
- Failing function: `delete problems and subproblems for code`
  - Failure condition: No matching problem exists.
  - Why it fails: Domain target is absent, but zero-row native deletes may still be treated as success.
  - Violated prerequisite or constraint: Existing target problem hierarchy.

Implementation notes:
This endpoint is not transactional at the controller level. A failure in one native delete path is collapsed into false and then a `404`, but zero-row deletes can still look successful.

### Behavior 9: Add A Subproblem To A Problem

Business goal:
Break a problem into a smaller subproblem item.

Domain context:
Subproblems are child items under a specific problem id, not directly under a project code.

Starting point:
Existing project with known `code`, and at least one problem under that code.

Required execution workflow:
1. Use function `retrieve problems for project code` (`GET /api/problem/{code}`) with `code="abc123"` to obtain the target problem `idx`.
2. Use function `create subproblem for problem` (`POST /api/subproblem`) with JSON body `pro_idx={problemIdxFromStep1}` and `content="Document validation behavior"` to create the subproblem.

Optional verification workflow:
None. There is no supported subproblem read or list function.

Existing-state shortcuts:
- If `problemIdx` is already known, step 1 can be skipped.
- Direct database setup can insert project, problem, and use the known problem `idx`.
- The `pro_idx` must still refer to an existing problem row.

Parameter and value bindings:
- The `code` in step 1 scopes which project’s problem ids are discovered.
- The returned problem `idx` from step 1 is reused exactly as body `pro_idx` in step 2.
- The body `content` becomes persisted subproblem content.
- There is no project code in the subproblem creation request, so ownership is only indirectly controlled by the chosen `pro_idx`.

Business result:
A `subproblem` row exists with the supplied content and a foreign-key relationship to the selected problem.

Constraints and invariants:
- `content` must be non-null and at least ten characters.
- Persistence also requires content max length 200.
- `pro_idx` must identify an existing problem.
- No uniqueness is enforced for subproblem content.

Failure and exceptional cases:
- Failing function: `retrieve problems for project code`
  - Failure condition: No problem exists for `code`.
  - Why it fails: Empty collection maps to `501 Not Implemented`.
  - Violated prerequisite or constraint: Existing parent problem.
- Failing function: `create subproblem for problem`
  - Failure condition: `content` shorter than ten characters.
  - Why it fails: Controller length check throws `DataFormatException`.
  - Violated prerequisite or constraint: Subproblem content minimum length.
- Failing function: `create subproblem for problem`
  - Failure condition: `pro_idx` is wrong or stale.
  - Why it fails: Problem lookup by id returns empty.
  - Violated prerequisite or constraint: Existing parent problem id.
- Failing function: `create subproblem for problem`
  - Failure condition: `content` omitted or null.
  - Why it fails: Controller calls `getContent().length()` before null handling.
  - Violated prerequisite or constraint: Required non-null content.
- Failing function: `create subproblem for problem`
  - Failure condition: `content` longer than 200 characters.
  - Why it fails: Persistence validation can fail and service returns false.
  - Violated prerequisite or constraint: Subproblem content max length.

Implementation notes:
OpenAPI documents `200 OK`, but successful creation returns `201 Created`. There is no API to retrieve the created subproblem.

### Behavior 10: Create And Decompose A Project Problem

Business goal:
Add a problem to a project and immediately create a first subproblem for it.

Domain context:
This is the practical workflow for turning a project-level problem into actionable detail.

Starting point:
Existing project with known `code`.

Required execution workflow:
1. Use function `create problem for project` (`POST /api/problem/{code}`) with `code="abc123"` and JSON body `title="Design API workflow"` to add the problem.
2. Use function `retrieve problems for project code` (`GET /api/problem/{code}`) with `code="abc123"` to obtain the created problem’s `idx`.
3. Use function `create subproblem for problem` (`POST /api/subproblem`) with JSON body `pro_idx={problemIdxFromStep2}` and `content="Document endpoint behavior"` to add the subproblem.

Optional verification workflow:
1. Use function `retrieve problems for project code` (`GET /api/problem/{code}`) with `code="abc123"` to inspect the project’s problems. Subproblem verification is not supported by an API function.

Existing-state shortcuts:
- If the target problem already exists and its `idx` is known, steps 1 and 2 can be skipped.
- If the target problem exists but its `idx` is unknown, skip step 1 but keep step 2.
- Direct database setup can create project/problem state, but `pro_idx` must still match the actual problem id.

Parameter and value bindings:
- `code="abc123"` binds the problem to the project.
- The problem `idx` returned by `retrieve problems for project code` is reused as `pro_idx` in `create subproblem for problem`.
- The subproblem is scoped by `pro_idx`, not by project code.

Business result:
The project has a new problem, and that problem has at least one subproblem.

Constraints and invariants:
- The project code must already be known.
- Problem title must be non-null.
- Subproblem content must be 10 to 200 characters.
- Multiple problems with the same title/code can make it ambiguous which returned problem is the newly created one, because creation returns no id.

Failure and exceptional cases:
- Failing function: `create problem for project`
  - Failure condition: Parent project code does not exist.
  - Why it fails: Project lookup is required before saving problem.
  - Violated prerequisite or constraint: Existing project.
- Failing function: `retrieve problems for project code`
  - Failure condition: The problem was not created or no problems match the code.
  - Why it fails: Empty collection throws `ElementNullException`.
  - Violated prerequisite or constraint: Existing problem for project code.
- Failing function: `create subproblem for problem`
  - Failure condition: The wrong `idx` is selected from the problem list.
  - Why it fails: It may attach the subproblem to a different problem, or fail if the id is stale.
  - Violated prerequisite or constraint: Correct parent problem identity.
- Failing function: `create subproblem for problem`
  - Failure condition: Content is too short, null, or too long.
  - Why it fails: Controller and persistence validation reject or crash.
  - Violated prerequisite or constraint: Subproblem content rules.

Implementation notes:
This workflow is API-realizable only after a known project code exists. It is not fully API-realizable from project creation because `create project` does not expose the generated code.

### Behavior 11: Clean Up A Project Problem Hierarchy

Business goal:
Remove problems and their subproblems for a project code while keeping the project itself.

Domain context:
This supports clearing work items from a project without deleting the project container.

Starting point:
Existing project with known `code`, one problem for that code, and optionally subproblems attached to that problem.

Required execution workflow:
1. Use function `delete problems and subproblems for code` (`DELETE /api/problem/{code}/all`) with `code="abc123"` to remove the hierarchy.

Optional verification workflow:
1. Use function `retrieve problems for project code` (`GET /api/problem/{code}`) with `code="abc123"` to confirm no problems remain; absence is represented by `501 Not Implemented`.
2. Use function `retrieve project by code` (`GET /api/project/{code}`) with `code="abc123"` to confirm the project itself still exists.

Existing-state shortcuts:
- If no subproblems exist, the same delete can still remove the problem row.
- Direct database setup can create the exact one-problem state required.
- If multiple problems exist for the code, this endpoint is unsafe; `delete problem rows for code` may delete problems but does not safely handle subproblems.

Parameter and value bindings:
- The same `code` is reused for hierarchy deletion and optional project verification.
- Internally, subproblem cleanup binds `code` to a single problem `idx`.

Business result:
Problem rows for the code are removed, subproblem rows for the selected problem are removed, and the project remains.

Constraints and invariants:
- The delete assumes one problem per project code even though the service allows many.
- No affected-row count is checked.
- There is no subproblem verification endpoint.

Failure and exceptional cases:
- Failing function: `delete problems and subproblems for code`
  - Failure condition: More than one problem exists for `code`.
  - Why it fails: Scalar subquery for `idx` may return multiple rows.
  - Violated prerequisite or constraint: Single-problem assumption.
- Failing function: `delete problems and subproblems for code`
  - Failure condition: `code` has invalid length.
  - Why it fails: Controller format validation.
  - Violated prerequisite or constraint: Code format.
- Failing function: `retrieve project by code`
  - Failure condition: The project was separately deleted before verification.
  - Why it fails: Project lookup returns null.
  - Violated prerequisite or constraint: Project retained while clearing problems.

Implementation notes:
This behavior is only reliable for a project code with one problem. It is not a general safe cleanup for projects with multiple problems.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: API-Only Use Of A Newly Created Project

Priority:
Critical domain gap

Expected business goal:
Create a project and immediately use it for retrieval, update, deletion, or problem creation.

Why it is unsupported:
`create project` generates the project code but does not return it in the body or a usable `Location`.

Existing functions considered:
- `create project`: creates the project but hides the generated code.
- `retrieve project by code`: requires the code that creation does not expose.
- `create problem for project`: requires the code that creation does not expose.
- `update project title`: requires the code that creation does not expose.
- `delete project`: requires the code that creation does not expose.

Missing capability:
A response body or `Location` containing the generated project code, or a list/search endpoint to discover projects.

Proof that function composition is insufficient:
No existing function lists projects or searches by title. Since the generated `code` is not returned, API calls cannot bind the created project to later `{code}` parameters.

Evidence from existing functions/source:
`create project` constructs `new Project(title)` and returns an empty `201 Created`; `retrieve project by code` requires `{code}`.

Business impact:
The central lifecycle workflow is broken for API clients unless they use database access or logs.

### Missing Behavior 2: Manage A Single Problem By Id

Priority:
Critical domain gap

Expected business goal:
Retrieve, update, or delete one specific problem without affecting other problems in the same project.

Why it is unsupported:
There is no public endpoint using problem `idx` for single-problem read, update, or delete.

Existing functions considered:
- `create problem for project`: creates a problem but returns no `idx`.
- `retrieve problems for project code`: lists all problems for a code but does not manage one.
- `delete problem rows for code`: deletes all matching problem rows by code.
- `delete problems and subproblems for code`: also operates by code and assumes one problem.

Missing capability:
Endpoints such as `GET /api/problem/id/{idx}`, `PUT /api/problem/{idx}`, and `DELETE /api/problem/{idx}`.

Proof that function composition is insufficient:
Listing can reveal ids, but no later function accepts problem `idx` except subproblem creation. Delete-by-code cannot preserve sibling problems.

Evidence from existing functions/source:
Problem repository has `getProblem(idx)`, but controller does not expose it except indirectly for `create subproblem for problem`.

Business impact:
Clients cannot safely modify or remove one problem in a multi-problem project.

### Missing Behavior 3: Read Or Manage Subproblems

Priority:
Critical domain gap

Expected business goal:
List, retrieve, update, or delete subproblems for a problem.

Why it is unsupported:
Only subproblem creation is exposed.

Existing functions considered:
- `create subproblem for problem`: creates a subproblem but returns no body.
- `delete problems and subproblems for code`: deletes subproblems only as part of code-level cleanup.
- `retrieve problems for project code`: does not include a usable subproblem list.

Missing capability:
Subproblem read/list/update/delete endpoints scoped by `pro_idx` or subproblem `idx`.

Proof that function composition is insufficient:
After creation, no function can query the created subproblem, update its content/count, or delete it without deleting parent problems.

Evidence from existing functions/source:
`subProblemService` has a `getSubProblemById` method, but no controller endpoint exposes it. The repository query is also incorrectly declared as `WHERE code=?1` against `subproblem`.

Business impact:
Subproblem data is write-only through the API and cannot be maintained independently.

### Missing Behavior 4: Safe Multi-Problem Hierarchy Cleanup

Priority:
Important robustness gap

Expected business goal:
Delete all problems and subproblems for a project with any number of problems.

Why it is unsupported:
The recursive delete uses a scalar subquery that assumes one problem per code.

Existing functions considered:
- `delete problems and subproblems for code`: fails or becomes unreliable when multiple problems share the code.
- `delete problem rows for code`: can violate subproblem foreign keys and still return `200 OK`.

Missing capability:
A delete strategy that removes subproblems with `pro_idx IN (SELECT idx FROM problem WHERE code=?)`, wrapped in a transaction with affected-row handling.

Proof that function composition is insufficient:
The service allows multiple problems per code. Existing delete functions either cannot clean all subproblems safely or cannot delete problems without risking foreign-key failure.

Evidence from existing functions/source:
`create problem for project` has no uniqueness guard. `deleteAllSubProblemByCodeInQuery` uses `pro_idx = (SELECT idx FROM problem WHERE code=?1)`.

Business impact:
Real projects with multiple problems cannot be reliably cleaned up.

### Missing Behavior 5: Validate Requests Without Persisting Invalid Or Crashing State

Priority:
Important robustness gap

Expected business goal:
Reject invalid input consistently with clear client errors.

Why it is unsupported:
Several null and persistence-invalid cases cause unhandled errors or inconsistent status codes.

Existing functions considered:
- `create project`: null `title` can cause a null dereference.
- `create subproblem for problem`: null `content` can cause a null dereference.
- `update project title`: does not enforce create-time minimum title length.
- `create subproblem for problem`: content over 200 can surface as `500`.

Missing capability:
Consistent validation before service/persistence operations.

Proof that function composition is insufficient:
Chaining functions cannot add validation to existing endpoints or prevent server-side null dereferences.

Evidence from existing functions/source:
Controllers call `.length()` before null checks in project and subproblem creation.

Business impact:
Clients receive unstable errors and may not know which business rule was violated.

### Missing Behavior 6: Ownership-Scoped Subproblem Creation

Priority:
Important robustness gap

Expected business goal:
Create a subproblem only under a problem that belongs to the intended project.

Why it is unsupported:
`create subproblem for problem` accepts only `pro_idx`, not project `code`.

Existing functions considered:
- `retrieve problems for project code`: can discover ids for a code.
- `create subproblem for problem`: accepts any existing problem id.

Missing capability:
A scoped endpoint such as `POST /api/project/{code}/problem/{idx}/subproblem`, or validation that `pro_idx` belongs to the supplied project code.

Proof that function composition is insufficient:
A client can choose a stale or wrong `pro_idx`; the create endpoint validates existence but not intended project ownership.

Evidence from existing functions/source:
The subproblem controller only calls `problemService.getProblemById(pro_idx)`.

Business impact:
Subproblems can be attached to the wrong problem if ids are mixed up.

### Missing Behavior 7: Accurate Delete Outcomes

Priority:
Important robustness gap

Expected business goal:
Know whether delete operations actually removed state.

Why it is unsupported:
Delete functions often ignore affected-row counts or service failure results.

Existing functions considered:
- `delete project`: may return success for missing project.
- `delete problem rows for code`: returns `200 OK` even when service deletion fails.
- `delete problems and subproblems for code`: may treat zero-row native deletes as success.

Missing capability:
Affected-row checking and error propagation.

Proof that function composition is insufficient:
Verification reads can detect absence of problems, but they cannot distinguish successful deletion from pre-existing absence or partial failure, especially without subproblem reads.

Evidence from existing functions/source:
`deleteProblem` ignores `problemService.deleteAllProblemWithCode(code)` return value.

Business impact:
Clients can falsely believe cleanup succeeded.

### Missing Behavior 8: Search Or List Projects

Priority:
API ergonomics gap

Expected business goal:
Browse projects, find a project by title, or recover a generated code.

Why it is unsupported:
There is no project list or search endpoint.

Existing functions considered:
- `create project`: hides generated code.
- `retrieve project by code`: requires exact code.

Missing capability:
`GET /api/project`, filtering by title, or lookup by database id.

Proof that function composition is insufficient:
Without known code, no function can discover candidate projects.

Evidence from existing functions/source:
Only `POST /api/project` and `GET/PUT/DELETE /api/project/{code}` are exposed.

Business impact:
Project recovery and normal navigation are impossible through the API.

### Missing Behavior 9: OpenAPI-Accurate Client Contract

Priority:
API ergonomics gap

Expected business goal:
Generate clients that understand real success and error responses.

Why it is unsupported:
OpenAPI declares mostly `200 OK` and omits many real `201`, `400`, `404`, `500`, and `501` outcomes.

Existing functions considered:
- `create project`: documented `200`, returns `201`.
- `create problem for project`: documented `200`, returns `201`.
- `create subproblem for problem`: documented `200`, returns `201`.
- Read/delete functions: error responses are largely undocumented.

Missing capability:
Accurate OpenAPI response definitions and schemas.

Proof that function composition is insufficient:
Client contract mismatch is metadata-level; chaining functions cannot make generated clients aware of real statuses.

Evidence from existing functions/source:
Controller implementations return statuses not declared in `spring-rest-example.json`.

Business impact:
Generated clients and tests will mishandle valid and invalid responses.

## Cross-Behavior Observations

- Project creation is the root of the domain, but the generated project code is not exposed, preventing complete API-only workflows from no prior state.
- Problem creation allows multiple problems per project code, while recursive deletion assumes one problem per code.
- Subproblems are create-only through the public API.
- Validation is inconsistent: create-project title minimum is enforced, update-project title minimum is not.
- Several null-input cases cause server errors instead of controlled validation failures.
- Native delete queries bypass some JPA cascade behavior and do not reliably report affected rows.
- OpenAPI and implementation disagree on success statuses and omit meaningful error responses.
- There is no authentication, user ownership, audit workflow, pagination, search, or lifecycle status model.

## Coverage Summary

Supported domain areas:
Project creation, project retrieval by code, project title update, project deletion, problem creation under a known project code, problem listing by project code, code-level problem deletion, attempted recursive problem/subproblem cleanup, and subproblem creation by known problem id.

Partially supported domain areas:
Project lifecycle is partially supported because generated codes are not exposed. Problem lifecycle is partially supported because only create/list/bulk-delete exist. Subproblem lifecycle is highly partial because only create exists.

Unsupported domain areas:
API-only project continuation after creation, single-problem management, subproblem read/update/delete, safe multi-problem recursive cleanup, accurate delete outcomes, project search/listing, scoped ownership validation, and a reliable OpenAPI contract.