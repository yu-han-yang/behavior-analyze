Analyzed only `src` and `spring-rest-example.json`.

### Behavior 1: create project

Behavior name:
create project

Successful execution:
- Result:
  This behavior creates a new project with a server-generated six-character `code` and the supplied `title`.
- Endpoint sequence:
  Step 1: `POST /api/project` with JSON body containing `title`.
- Constraints:
  `title` must be non-null and at least 5 characters long. The implementation ignores any request body `code` and constructs a new `Project(title)`, which generates `code` internally. The response is `201 Created`, not the `200` shown in Swagger, and it does not expose the generated `code`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `title` is shorter than 5 characters.
  - Endpoint group:
    Step 1: `POST /api/project`
  - Failure endpoint:
    `POST /api/project`
  - Why this fails:
    `ProjectController.createProject` throws `DataFormatException`.
  - Intentionally violated constraints:
    Body `title` length is less than 5.
- Branch 2:
  - Unsatisfied condition:
    `title` is missing or null.
  - Endpoint group:
    Step 1: `POST /api/project`
  - Failure endpoint:
    `POST /api/project`
  - Why this fails:
    The implementation calls `title.length()` before a null check, causing an unhandled null failure rather than the documented validation response.
  - Intentionally violated constraints:
    Body omits required `title`.

Endpoint coverage:
- Covers:
  `POST /api/project`
- Distinct meaning:
  Creates a project. Request body `code` is not user-controlled despite appearing in the schema.

### Behavior 2: retrieve project by code

Behavior name:
retrieve project by code

Successful execution:
- Result:
  This behavior retrieves the project whose `code` matches `{code}`.
- Endpoint sequence:
  Step 1: `POST /api/project`
  Step 2: `GET /api/project/{code}`
- Constraints:
  `{code}` in Step 2 must equal the server-generated project code from Step 1. The implementation requires length at least 6, while Swagger describes a six-character code. Contract gap: `POST /api/project` creates the required code but does not return it in the body or a useful `Location`, so a documented client has no reliable way to obtain `{code}` from Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{code}` is shorter than 6 characters.
  - Endpoint group:
    Step 1: `GET /api/project/{code}`
  - Failure endpoint:
    `GET /api/project/{code}`
  - Why this fails:
    The controller throws `DataFormatException`.
  - Intentionally violated constraints:
    Path `{code}` length is less than 6.
- Branch 2:
  - Unsatisfied condition:
    No project exists with the supplied syntactically valid `{code}`.
  - Endpoint group:
    Step 1: `GET /api/project/{code}`
  - Failure endpoint:
    `GET /api/project/{code}`
  - Why this fails:
    `projectRepository.getProject(code)` returns null and the controller throws `ResourceNotFoundException`.
  - Intentionally violated constraints:
    The required `POST /api/project` setup for this `{code}` is omitted, or a wrong code is used.

Endpoint coverage:
- Covers:
  `GET /api/project/{code}`
- Distinct meaning:
  Reads one existing project by code.

### Behavior 3: update project title

Behavior name:
update project title

Successful execution:
- Result:
  This behavior changes the `title` of the project identified by `{code}`.
- Endpoint sequence:
  Step 1: `POST /api/project`
  Step 2: `PUT /api/project/{code}`
- Constraints:
  `{code}` in Step 2 must identify the project created in Step 1. The request body supplies the replacement `title`. The update endpoint does not enforce the create endpoint’s minimum title length, but persistence still requires a non-null title. The generated code needed for Step 2 is not exposed by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{code}` is shorter than 6 characters.
  - Endpoint group:
    Step 1: `PUT /api/project/{code}`
  - Failure endpoint:
    `PUT /api/project/{code}`
  - Why this fails:
    The controller throws `DataFormatException`.
  - Intentionally violated constraints:
    Path `{code}` length is less than 6.
- Branch 2:
  - Unsatisfied condition:
    No project exists with `{code}`.
  - Endpoint group:
    Step 1: `PUT /api/project/{code}`
  - Failure endpoint:
    `PUT /api/project/{code}`
  - Why this fails:
    The lookup throws `ResourceNotFoundException`.
  - Intentionally violated constraints:
    The required `POST /api/project` setup is omitted, or a wrong code is used.
- Branch 3:
  - Unsatisfied condition:
    Replacement `title` is null or invalid for persistence.
  - Endpoint group:
    Step 1: `POST /api/project`
    Step 2: `PUT /api/project/{code}`
  - Failure endpoint:
    `PUT /api/project/{code}`
  - Why this fails:
    The controller sets the persisted project title from the request body and then flushes it; null or persistence-invalid values can fail during `saveAndFlush`.
  - Intentionally violated constraints:
    Body `title` is null or violates entity persistence constraints.

Endpoint coverage:
- Covers:
  `PUT /api/project/{code}`
- Distinct meaning:
  Replaces the title of an existing project.

### Behavior 4: delete project

Behavior name:
delete project

Successful execution:
- Result:
  This behavior deletes the project identified by `{code}`.
- Endpoint sequence:
  Step 1: `POST /api/project`
  Step 2: `DELETE /api/project/{code}`
- Constraints:
  `{code}` in Step 2 must identify the project created in Step 1. The controller only validates code length and delegates to `deleteByCode`; it does not explicitly fetch the project first. The generated code needed for Step 2 is not exposed by Step 1.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{code}` is shorter than 6 characters.
  - Endpoint group:
    Step 1: `DELETE /api/project/{code}`
  - Failure endpoint:
    `DELETE /api/project/{code}`
  - Why this fails:
    The controller throws `DataFormatException`.
  - Intentionally violated constraints:
    Path `{code}` length is less than 6.
- Branch 2:
  - Unsatisfied condition:
    The supplied code does not identify a project.
  - Endpoint group:
    Step 1: `DELETE /api/project/{code}`
  - Failure endpoint:
    `DELETE /api/project/{code}`
  - Why this fails:
    The implementation has no explicit existence check. If `deleteByCode` throws, the controller returns `404`; otherwise the endpoint may report `200 OK` even though no project was deleted.
  - Intentionally violated constraints:
    The required `POST /api/project` setup is omitted, or a wrong code is used.

Endpoint coverage:
- Covers:
  `DELETE /api/project/{code}`
- Distinct meaning:
  Deletes a project by code.

### Behavior 5: create problem for project

Behavior name:
create problem for project

Successful execution:
- Result:
  This behavior creates a problem attached to the project identified by `{code}`.
- Endpoint sequence:
  Step 1: `POST /api/project`
  Step 2: `POST /api/problem/{code}`
- Constraints:
  `{code}` in Step 2 must equal the project code generated in Step 1. The problem request body must contain a non-null `title`; request body project linkage is overwritten by the path code lookup. The project creation response does not expose `{code}`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{code}` is shorter than 6 characters.
  - Endpoint group:
    Step 1: `POST /api/problem/{code}`
  - Failure endpoint:
    `POST /api/problem/{code}`
  - Why this fails:
    The controller throws `DataFormatException`.
  - Intentionally violated constraints:
    Path `{code}` length is less than 6.
- Branch 2:
  - Unsatisfied condition:
    No project exists with `{code}`.
  - Endpoint group:
    Step 1: `POST /api/problem/{code}`
  - Failure endpoint:
    `POST /api/problem/{code}`
  - Why this fails:
    The project lookup fails and throws `ResourceNotFoundException`.
  - Intentionally violated constraints:
    The required `POST /api/project` setup is omitted, or a wrong code is used.
- Branch 3:
  - Unsatisfied condition:
    Problem `title` is null.
  - Endpoint group:
    Step 1: `POST /api/project`
    Step 2: `POST /api/problem/{code}`
  - Failure endpoint:
    `POST /api/problem/{code}`
  - Why this fails:
    `ProblemServiceImpl.saveProblem` returns false when `problem.getTitle() == null`, and the controller returns `400`.
  - Intentionally violated constraints:
    Problem body omits `title` or sets it to null.

Endpoint coverage:
- Covers:
  `POST /api/problem/{code}`
- Distinct meaning:
  Adds a problem under an existing project code.

### Behavior 6: retrieve problems for project code

Behavior name:
retrieve problems for project code

Successful execution:
- Result:
  This behavior retrieves the collection of problems whose project code matches `{code}`.
- Endpoint sequence:
  Step 1: `POST /api/project`
  Step 2: `POST /api/problem/{code}`
  Step 3: `GET /api/problem/{code}`
- Constraints:
  The `{code}` used in Steps 2 and 3 must be the same generated project code from Step 1. At least one problem row must exist for that code. The implementation queries problems directly by `code`; it does not separately check whether the project exists.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{code}` is shorter than 6 characters.
  - Endpoint group:
    Step 1: `GET /api/problem/{code}`
  - Failure endpoint:
    `GET /api/problem/{code}`
  - Why this fails:
    The controller throws `DataFormatException`.
  - Intentionally violated constraints:
    Path `{code}` length is less than 6.
- Branch 2:
  - Unsatisfied condition:
    No problem rows exist for `{code}`.
  - Endpoint group:
    Step 1: `POST /api/project`
    Step 2: `GET /api/problem/{code}`
  - Failure endpoint:
    `GET /api/problem/{code}`
  - Why this fails:
    The problem query returns an empty collection and the controller throws `ElementNullException`, mapped to `501 Not Implemented`.
  - Intentionally violated constraints:
    `POST /api/problem/{code}` is intentionally omitted before the read.

Endpoint coverage:
- Covers:
  `GET /api/problem/{code}`
- Distinct meaning:
  Lists existing problems associated with a code.

### Behavior 7: delete problem rows for code without subproblem cleanup

Behavior name:
delete problem rows for code

Successful execution:
- Result:
  This behavior deletes problem rows whose `code` matches `{code}`. Swagger says “delete recent problem,” but the implementation calls `deleteAllProblemByCodeInQuery`, so it deletes all matching problems, not only the most recent one.
- Endpoint sequence:
  Step 1: `POST /api/project`
  Step 2: `POST /api/problem/{code}`
  Step 3: `DELETE /api/problem/{code}`
- Constraints:
  `{code}` in Steps 2 and 3 must be the same project code from Step 1. For a clean successful deletion, matching problems should not have subproblems, because this endpoint does not delete subproblems first. The controller ignores the service return value and returns `200 OK` after format validation.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{code}` is shorter than 6 characters.
  - Endpoint group:
    Step 1: `DELETE /api/problem/{code}`
  - Failure endpoint:
    `DELETE /api/problem/{code}`
  - Why this fails:
    The controller throws `DataFormatException`.
  - Intentionally violated constraints:
    Path `{code}` length is less than 6.
- Branch 2:
  - Unsatisfied condition:
    Matching problems have subproblems.
  - Endpoint group:
    Step 1: `POST /api/project`
    Step 2: `POST /api/problem/{code}`
    Step 3: `GET /api/problem/{code}`
    Step 4: `POST /api/subproblem`
    Step 5: `DELETE /api/problem/{code}`
  - Failure endpoint:
    `DELETE /api/problem/{code}`
  - Why this fails:
    The native delete removes `problem` rows without first deleting `subproblem` rows. A foreign-key failure can occur, the service catches it, and the controller still returns `200 OK`, so the visible response can falsely indicate success.
  - Intentionally violated constraints:
    A subproblem is intentionally left attached to the problem before using the non-recursive delete endpoint.

Endpoint coverage:
- Covers:
  `DELETE /api/problem/{code}`
- Distinct meaning:
  Implementation-backed meaning is bulk problem deletion by code, not “delete latest problem” as described by Swagger.

### Behavior 8: delete problems and subproblems for code

Behavior name:
delete problems and subproblems for code

Successful execution:
- Result:
  This behavior deletes subproblems for the matching problem and then deletes problem rows for `{code}`.
- Endpoint sequence:
  Step 1: `POST /api/project`
  Step 2: `POST /api/problem/{code}`
  Step 3: `GET /api/problem/{code}`
  Step 4: `POST /api/subproblem`
  Step 5: `DELETE /api/problem/{code}/all`
- Constraints:
  `{code}` in Steps 2, 3, and 5 must match the generated project code from Step 1. Step 3 must provide a problem `idx`; Step 4 must send that value as `pro_idx`. The subproblem `content` must be at least 10 characters. Implementation detail: the subproblem delete query uses `pro_idx = (SELECT idx FROM problem WHERE code=?1)`, so it assumes the code maps to a single problem row.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{code}` is shorter than 6 characters.
  - Endpoint group:
    Step 1: `DELETE /api/problem/{code}/all`
  - Failure endpoint:
    `DELETE /api/problem/{code}/all`
  - Why this fails:
    The controller throws `DataFormatException`.
  - Intentionally violated constraints:
    Path `{code}` length is less than 6.
- Branch 2:
  - Unsatisfied condition:
    More than one problem exists for the same code.
  - Endpoint group:
    Step 1: `POST /api/project`
    Step 2: `POST /api/problem/{code}`
    Step 3: `POST /api/problem/{code}`
    Step 4: `DELETE /api/problem/{code}/all`
  - Failure endpoint:
    `DELETE /api/problem/{code}/all`
  - Why this fails:
    The native subproblem delete query is written as a scalar subquery. Multiple problem rows for one code can make the subquery return multiple `idx` values, causing the service to return false and the controller to throw `ResourceNotFoundException`.
  - Intentionally violated constraints:
    The sequence creates multiple problems for the same code before calling the `/all` delete endpoint.
- Branch 3:
  - Unsatisfied condition:
    No matching problem exists.
  - Endpoint group:
    Step 1: `DELETE /api/problem/{code}/all`
  - Failure endpoint:
    `DELETE /api/problem/{code}/all`
  - Why this fails:
    The endpoint is intended to report `404` when deletion fails, but the service does not check row counts. Native deletes that affect zero rows can still return success, so this may become a no-op `200 OK`.
  - Intentionally violated constraints:
    The required `POST /api/project` and `POST /api/problem/{code}` setup is omitted.

Endpoint coverage:
- Covers:
  `DELETE /api/problem/{code}/all`
- Distinct meaning:
  Attempts recursive cleanup of subproblems before deleting problems for a code.

### Behavior 9: create subproblem for problem

Behavior name:
create subproblem for problem

Successful execution:
- Result:
  This behavior creates a subproblem attached to an existing problem identified by `pro_idx`.
- Endpoint sequence:
  Step 1: `POST /api/project`
  Step 2: `POST /api/problem/{code}`
  Step 3: `GET /api/problem/{code}`
  Step 4: `POST /api/subproblem`
- Constraints:
  `{code}` in Steps 2 and 3 must identify the project from Step 1. Step 3 returns problem objects; the `idx` of the intended problem must be reused as `pro_idx` in Step 4. Step 4 body must include `content` with length at least 10. Problem creation does not return `idx`, so Step 3 is required to obtain it through the documented API.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `content` is empty or shorter than 10 characters.
  - Endpoint group:
    Step 1: `POST /api/project`
    Step 2: `POST /api/problem/{code}`
    Step 3: `GET /api/problem/{code}`
    Step 4: `POST /api/subproblem`
  - Failure endpoint:
    `POST /api/subproblem`
  - Why this fails:
    The controller throws `DataFormatException`.
  - Intentionally violated constraints:
    Step 4 uses valid `pro_idx` but invalid `content`.
- Branch 2:
  - Unsatisfied condition:
    `pro_idx` does not identify an existing problem.
  - Endpoint group:
    Step 1: `POST /api/subproblem`
  - Failure endpoint:
    `POST /api/subproblem`
  - Why this fails:
    `problemService.getProblemById(pro_idx)` returns empty and the controller throws `ResourceNotFoundException`.
  - Intentionally violated constraints:
    The required `POST /api/problem/{code}` and `GET /api/problem/{code}` sequence to obtain a valid problem `idx` is omitted, or the wrong `pro_idx` is supplied.
- Branch 3:
  - Unsatisfied condition:
    `content` is null.
  - Endpoint group:
    Step 1: `POST /api/subproblem`
  - Failure endpoint:
    `POST /api/subproblem`
  - Why this fails:
    The implementation calls `problemBody.getContent().length()` before a null check, causing an unhandled null failure.
  - Intentionally violated constraints:
    Body omits `content` or sets it to null.
- Branch 4:
  - Unsatisfied condition:
    `content` exceeds the entity maximum length.
  - Endpoint group:
    Step 1: `POST /api/project`
    Step 2: `POST /api/problem/{code}`
    Step 3: `GET /api/problem/{code}`
    Step 4: `POST /api/subproblem`
  - Failure endpoint:
    `POST /api/subproblem`
  - Why this fails:
    `subProblem.content` has `@Size(max=200)` and persistence can reject longer content; the service returns false and the controller returns `500`.
  - Intentionally violated constraints:
    Step 4 uses valid `pro_idx` but body `content` is longer than 200 characters.

Endpoint coverage:
- Covers:
  `POST /api/subproblem`
- Distinct meaning:
  Adds a subproblem to an existing problem by `pro_idx`.

### Unclear or auxiliary endpoints

None. Every endpoint in `spring-rest-example.json` maps to at least one behavior above.