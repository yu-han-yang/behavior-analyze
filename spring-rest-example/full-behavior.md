### Function 1: create project

Function name:
create project

Core endpoint(s):
- `POST /api/project`

Preconditions:
- No prerequisite resource state is required. The caller supplies a JSON request body with `title`; any supplied `code` value is ignored by the implementation.

Successful execution:
- Result:
  Creates a new `Project` row with the supplied `title` and a server-generated six-character `code`.
- Invocation:
  Step 1: `POST /api/project` with JSON body containing `title`.
- Constraints:
  `title` must be non-null and at least 5 characters long. The `Project` entity also requires a non-null `title` no longer than 200 characters and a unique `code` no longer than 6 characters. OpenAPI documents a `200` response, but the implementation returns `201 Created` with an empty body. The generated `code` is not returned in the response body, and the generated `Location` is built from the current request rather than from the new code.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The request body contains a `title` value shorter than 5 characters.
  - Failure endpoint:
    `POST /api/project`
  - Why this fails:
    `ProjectController.createProject` checks `title.length() < 5` and throws `DataFormatException`, which is mapped to `400 Bad Request`.
  - Intentionally violated constraints:
    The minimum title length required by the create endpoint is violated.
- Branch 2:
  - Preconditions:
    - The request body omits `title` or sets `title` to null.
  - Failure endpoint:
    `POST /api/project`
  - Why this fails:
    The controller calls `title.length()` before checking for null, so a null title causes an unhandled null dereference rather than the documented validation response.
  - Intentionally violated constraints:
    The required non-null `title` value is omitted.
- Branch 3:
  - Preconditions:
    - A project already exists with the same randomly generated `code`. This can be satisfied only by direct database setup or by chance through earlier `POST /api/project` calls, because the create endpoint does not accept a caller-controlled code.
  - Failure endpoint:
    `POST /api/project`
  - Why this fails:
    `Project.code` is unique in the database. `projectService.saveProject` catches the persistence exception and the controller returns `400 Bad Request` with the persistence error text.
  - Intentionally violated constraints:
    The generated project code uniqueness requirement is violated.

Endpoint coverage:
- Covers:
  `POST /api/project`
- Distinct meaning:
  This endpoint directly creates a project. Source code shows that request body `code` is not user-controlled even though it appears in the OpenAPI schema.

### Function 2: retrieve project by code

Function name:
retrieve project by code

Core endpoint(s):
- `GET /api/project/{code}`

Preconditions:
- A project with `code = {code}` exists in the database. This can be satisfied by directly inserting a `Project` row with `code = {code}` and a non-null `title`, or by calling `POST /api/project` with a valid `title`.
- The `{code}` used by `GET /api/project/{code}` must equal the existing project code. If the API is used to create the project, the generated code must be obtained outside the documented create response, because `POST /api/project` returns no response body containing the code.

Successful execution:
- Result:
  Returns the project whose persisted `code` matches `{code}`.
- Invocation:
  Step 1: `GET /api/project/{code}` with `{code}` in the path.
- Constraints:
  `{code}` must be non-empty and at least 6 characters long. OpenAPI describes the code as a six-character alphanumeric value, while the implementation only checks that length is at least 6 before querying the database.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The supplied path `{code}` is empty or shorter than 6 characters.
  - Failure endpoint:
    `GET /api/project/{code}`
  - Why this fails:
    `ProjectController.getProject` throws `DataFormatException`, which is mapped to `400 Bad Request`.
  - Intentionally violated constraints:
    The project code format requirement is violated.
- Branch 2:
  - Preconditions:
    - No project with `code = {code}` exists in the database. This can be produced by starting from an empty database, deleting the project beforehand, directly omitting the row, or not calling `POST /api/project` for that code.
  - Failure endpoint:
    `GET /api/project/{code}`
  - Why this fails:
    `projectRepository.getProject(code)` returns null and the controller throws `ResourceNotFoundException`, which is mapped to `404 Not Found`.
  - Intentionally violated constraints:
    The required project state for `{code}` is omitted or a wrong code is used.

Endpoint coverage:
- Covers:
  `GET /api/project/{code}`
- Distinct meaning:
  This endpoint directly reads one existing project by code.

### Function 3: update project title

Function name:
update project title

Core endpoint(s):
- `PUT /api/project/{code}`

Preconditions:
- A project with `code = {code}` exists in the database. This can be satisfied by directly inserting a `Project` row with `code = {code}` and a non-null `title`, or by calling `POST /api/project` with a valid `title`.
- The `{code}` used by `PUT /api/project/{code}` must identify that project. If the API is used to establish the project, the server-generated code must be obtained outside the documented create response because `POST /api/project` does not expose it.

Successful execution:
- Result:
  Replaces the persisted `title` of the project identified by `{code}`.
- Invocation:
  Step 1: `PUT /api/project/{code}` with `{code}` in the path and a JSON body containing replacement `title`.
- Constraints:
  `{code}` must be non-empty and at least 6 characters long. The update endpoint does not enforce the create endpoint's minimum title length, but persistence still requires `Project.title` to be non-null and no longer than 200 characters.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The supplied path `{code}` is empty or shorter than 6 characters.
  - Failure endpoint:
    `PUT /api/project/{code}`
  - Why this fails:
    `ProjectController.updateProject` throws `DataFormatException`, which is mapped to `400 Bad Request`.
  - Intentionally violated constraints:
    The project code format requirement is violated.
- Branch 2:
  - Preconditions:
    - No project with `code = {code}` exists in the database. This can be produced by direct database omission or by not calling `POST /api/project` for that code.
  - Failure endpoint:
    `PUT /api/project/{code}`
  - Why this fails:
    The controller looks up the project before updating it. A missing row causes `ResourceNotFoundException`, which is mapped to `404 Not Found`.
  - Intentionally violated constraints:
    The required target project state is omitted or a wrong code is used.
- Branch 3:
  - Preconditions:
    - A project with `code = {code}` exists in the database. This can be satisfied by direct insertion or by calling `POST /api/project` with a valid `title` and reusing the generated code.
    - The update request body omits `title`, sets `title` to null, or supplies a value invalid for `Project.title` persistence.
  - Failure endpoint:
    `PUT /api/project/{code}`
  - Why this fails:
    The controller assigns `project.getTitle()` to the persisted project and calls `projectRepository.saveAndFlush`. Null or persistence-invalid values can fail during flush; the service does not catch that exception.
  - Intentionally violated constraints:
    The replacement project title violates persistence constraints.

Endpoint coverage:
- Covers:
  `PUT /api/project/{code}`
- Distinct meaning:
  This endpoint directly updates the title of an existing project.

### Function 4: delete project

Function name:
delete project

Core endpoint(s):
- `DELETE /api/project/{code}`

Preconditions:
- A project with `code = {code}` exists in the database. This can be satisfied by directly inserting a `Project` row with `code = {code}` and a non-null `title`, or by calling `POST /api/project` with a valid `title`.
- The `{code}` used by `DELETE /api/project/{code}` must identify that project. If the API is used to establish the project, the server-generated code must be obtained outside the documented create response because `POST /api/project` does not expose it.

Successful execution:
- Result:
  Deletes the project identified by `{code}`. Because `Project.problems` is mapped with remove cascade and orphan removal, deleting a managed project can also remove its associated problem rows.
- Invocation:
  Step 1: `DELETE /api/project/{code}` with `{code}` in the path.
- Constraints:
  `{code}` must be non-empty and at least 6 characters long. The controller delegates to `projectRepository.deleteByCode(code)` and does not fetch the project first.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The supplied path `{code}` is empty or shorter than 6 characters.
  - Failure endpoint:
    `DELETE /api/project/{code}`
  - Why this fails:
    `ProjectController.deleteProject` throws `DataFormatException`, which is mapped to `400 Bad Request`.
  - Intentionally violated constraints:
    The project code format requirement is violated.
- Branch 2:
  - Preconditions:
    - No project with `code = {code}` exists in the database. This can be produced by direct database omission or by not calling `POST /api/project` for that code.
  - Failure endpoint:
    `DELETE /api/project/{code}`
  - Why this fails:
    The service catches exceptions from `deleteByCode` and returns false, causing `404 Not Found`. If the repository delete affects zero rows without throwing, the endpoint can instead return `200 OK` as a no-op because the implementation does not check an affected-row count.
  - Intentionally violated constraints:
    The required target project state is omitted or a wrong code is used.

Endpoint coverage:
- Covers:
  `DELETE /api/project/{code}`
- Distinct meaning:
  This endpoint directly deletes a project by code.

### Function 5: create problem for project

Function name:
create problem for project

Core endpoint(s):
- `POST /api/problem/{code}`

Preconditions:
- A project with `code = {code}` exists in the database. This can be satisfied by directly inserting a `Project` row with `code = {code}` and a non-null `title`, or by calling `POST /api/project` with a valid `title`.
- The `{code}` used by `POST /api/problem/{code}` must identify that project. If the API is used to establish the project, the server-generated code must be obtained outside the documented create response because `POST /api/project` does not expose it.

Successful execution:
- Result:
  Creates a `Problem` row linked to the project identified by `{code}`.
- Invocation:
  Step 1: `POST /api/problem/{code}` with `{code}` in the path and a JSON body containing problem `title`.
- Constraints:
  `{code}` must be non-empty and at least 6 characters long. The request body must contain a non-null `title`; request body project linkage is overwritten by the project found from the path code. OpenAPI documents a `200` response, but the implementation returns `201 Created` with an empty body and a `Location` based on the current request.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The supplied path `{code}` is empty or shorter than 6 characters.
  - Failure endpoint:
    `POST /api/problem/{code}`
  - Why this fails:
    `ProblemController.saveProblem` throws `DataFormatException`, which is mapped to `400 Bad Request`.
  - Intentionally violated constraints:
    The project code format requirement is violated.
- Branch 2:
  - Preconditions:
    - No project with `code = {code}` exists in the database. This can be produced by direct database omission or by not calling `POST /api/project` for that code.
  - Failure endpoint:
    `POST /api/problem/{code}`
  - Why this fails:
    The controller must find the parent project before saving the problem. A missing project causes `ResourceNotFoundException`, which is mapped to `404 Not Found`.
  - Intentionally violated constraints:
    The required parent project state is omitted or a wrong code is used.
- Branch 3:
  - Preconditions:
    - A project with `code = {code}` exists in the database. This can be satisfied by direct insertion or by calling `POST /api/project` with a valid `title` and reusing the generated code.
    - The problem request body omits `title` or sets `title` to null.
  - Failure endpoint:
    `POST /api/problem/{code}`
  - Why this fails:
    `ProblemServiceImpl.saveProblem` returns false when `problem.getTitle() == null`, and the controller returns `400 Bad Request` with an error JSON body.
  - Intentionally violated constraints:
    The required non-null problem title is omitted.

Endpoint coverage:
- Covers:
  `POST /api/problem/{code}`
- Distinct meaning:
  This endpoint directly adds a problem under an existing project code.

### Function 6: retrieve problems for project code

Function name:
retrieve problems for project code

Core endpoint(s):
- `GET /api/problem/{code}`

Preconditions:
- A project with `code = {code}` exists in the database. This can be satisfied by directly inserting a `Project` row with `code = {code}` and a non-null `title`, or by calling `POST /api/project` with a valid `title`.
- At least one `Problem` row exists with foreign key `code = {code}`. This can be satisfied by directly inserting a problem row linked to the project code, or by calling `POST /api/problem/{code}` with a JSON body containing non-null `title`.
- The `{code}` used by `GET /api/problem/{code}` must match the code on the existing problem rows. If the API is used to establish the project, the server-generated code must be obtained outside the documented create response because `POST /api/project` does not expose it.

Successful execution:
- Result:
  Returns the collection of problem rows whose project code matches `{code}`.
- Invocation:
  Step 1: `GET /api/problem/{code}` with `{code}` in the path.
- Constraints:
  `{code}` must be non-empty and at least 6 characters long. The implementation queries the `problem` table directly by code and does not separately verify that a project row still exists.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The supplied path `{code}` is empty or shorter than 6 characters.
  - Failure endpoint:
    `GET /api/problem/{code}`
  - Why this fails:
    `ProblemController.getProblemByCode` throws `DataFormatException`, which is mapped to `400 Bad Request`.
  - Intentionally violated constraints:
    The project code format requirement is violated.
- Branch 2:
  - Preconditions:
    - A project with `code = {code}` exists in the database. This can be satisfied by direct insertion or by calling `POST /api/project` with a valid `title` and reusing the generated code.
    - No problem row exists with `code = {code}`. This can be produced by direct database omission or by intentionally not calling `POST /api/problem/{code}` before the read.
  - Failure endpoint:
    `GET /api/problem/{code}`
  - Why this fails:
    The repository query returns an empty collection and the controller throws `ElementNullException`, which is mapped to `501 Not Implemented`.
  - Intentionally violated constraints:
    The required problem state for the requested code is omitted.
- Branch 3:
  - Preconditions:
    - No problem row exists with `code = {code}`. A project row may also be absent because this endpoint does not check project existence separately.
  - Failure endpoint:
    `GET /api/problem/{code}`
  - Why this fails:
    The endpoint only checks whether the problem query result is empty. Missing project state is therefore observed as an empty problem collection and mapped to `501 Not Implemented`, not `404 Not Found`.
  - Intentionally violated constraints:
    The required problem collection for `{code}` is omitted or a wrong code is used.

Endpoint coverage:
- Covers:
  `GET /api/problem/{code}`
- Distinct meaning:
  This endpoint directly lists existing problems associated with a project code.

### Function 7: delete problem rows for code

Function name:
delete problem rows for code

Core endpoint(s):
- `DELETE /api/problem/{code}`

Preconditions:
- A project with `code = {code}` exists in the database. This can be satisfied by directly inserting a `Project` row with `code = {code}` and a non-null `title`, or by calling `POST /api/project` with a valid `title`.
- At least one `Problem` row exists with foreign key `code = {code}`. This can be satisfied by directly inserting a problem row linked to the project code, or by calling `POST /api/problem/{code}` with a JSON body containing non-null `title`.
- The `{code}` used by `DELETE /api/problem/{code}` must match the code on the problem rows. If the API is used to establish the project, the server-generated code must be obtained outside the documented create response because `POST /api/project` does not expose it.

Successful execution:
- Result:
  Deletes problem rows whose `code` matches `{code}`. OpenAPI describes this as deleting the recent problem, but source code calls `deleteAllProblemByCodeInQuery`, so the runtime effect is bulk deletion by code.
- Invocation:
  Step 1: `DELETE /api/problem/{code}` with `{code}` in the path.
- Constraints:
  `{code}` must be non-empty and at least 6 characters long. For clean deletion, matching problems should not have dependent subproblem rows because this endpoint does not delete subproblems first. The controller ignores the service return value and returns `200 OK` after format validation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The supplied path `{code}` is empty or shorter than 6 characters.
  - Failure endpoint:
    `DELETE /api/problem/{code}`
  - Why this fails:
    `ProblemController.deleteProblem` throws `DataFormatException`, which is mapped to `400 Bad Request`.
  - Intentionally violated constraints:
    The project code format requirement is violated.
- Branch 2:
  - Preconditions:
    - A project with `code = {code}` exists in the database. This can be satisfied by direct insertion or by calling `POST /api/project` with a valid `title` and reusing the generated code.
    - A problem row exists with `code = {code}`. This can be satisfied by direct insertion or by calling `POST /api/problem/{code}` with a non-null problem `title`.
    - The problem's `idx` is known. This can be satisfied by direct database lookup or by calling `GET /api/problem/{code}` and reading the returned problem `idx`.
    - A `subproblem` row exists with `pro_idx` equal to that problem `idx`. This can be satisfied by direct insertion or by calling `POST /api/subproblem` with JSON body containing `pro_idx` and `content` of at least 10 characters.
  - Failure endpoint:
    `DELETE /api/problem/{code}`
  - Why this fails:
    The native delete removes rows from `problem` without first deleting rows from `subproblem`. A foreign-key failure can occur inside `ProblemServiceImpl.deleteAllProblemWithCode`, but the controller ignores the false return value and still returns `200 OK`, so the response can falsely indicate success.
  - Intentionally violated constraints:
    A dependent subproblem is intentionally left attached before using the non-recursive problem delete endpoint.
- Branch 3:
  - Preconditions:
    - No problem row exists with `code = {code}`. This can be produced by direct database omission or by intentionally not calling `POST /api/problem/{code}`.
  - Failure endpoint:
    `DELETE /api/problem/{code}`
  - Why this fails:
    The service does not report affected-row count to the controller. A zero-row delete can still result in `200 OK`, making the endpoint a no-op instead of a visible failure.
  - Intentionally violated constraints:
    The target problem state for `{code}` is omitted.

Endpoint coverage:
- Covers:
  `DELETE /api/problem/{code}`
- Distinct meaning:
  This endpoint directly performs bulk deletion of problem rows by code, despite the OpenAPI summary implying deletion of only the recent problem.

### Function 8: delete problems and subproblems for code

Function name:
delete problems and subproblems for code

Core endpoint(s):
- `DELETE /api/problem/{code}/all`

Preconditions:
- A project with `code = {code}` exists in the database. This can be satisfied by directly inserting a `Project` row with `code = {code}` and a non-null `title`, or by calling `POST /api/project` with a valid `title`.
- A single `Problem` row exists with foreign key `code = {code}`. This can be satisfied by directly inserting one problem row linked to the project code, or by calling `POST /api/problem/{code}` once with a JSON body containing non-null `title`.
- The problem `idx` for that code is known. This can be satisfied by direct database lookup or by calling `GET /api/problem/{code}` and reading the returned problem `idx`.
- A `subproblem` row exists with `pro_idx` equal to that problem `idx`. This can be satisfied by directly inserting a `subproblem` row linked to the problem, or by calling `POST /api/subproblem` with JSON body containing `pro_idx` and `content` of at least 10 characters.
- The `{code}` used by `DELETE /api/problem/{code}/all` must match the single problem row and its parent project. If the API is used to establish the project, the server-generated code must be obtained outside the documented create response because `POST /api/project` does not expose it.

Successful execution:
- Result:
  Deletes subproblem rows for the problem selected by `{code}`, then deletes problem rows whose `code` matches `{code}`.
- Invocation:
  Step 1: `DELETE /api/problem/{code}/all` with `{code}` in the path.
- Constraints:
  `{code}` must be non-empty and at least 6 characters long. The subproblem delete query is `DELETE FROM subproblem WHERE pro_idx = (SELECT idx FROM problem WHERE code=?1)`, so it assumes the code maps to a single problem `idx`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The supplied path `{code}` is empty or shorter than 6 characters.
  - Failure endpoint:
    `DELETE /api/problem/{code}/all`
  - Why this fails:
    `ProblemController.deleteAllProblem` throws `DataFormatException`, which is mapped to `400 Bad Request`.
  - Intentionally violated constraints:
    The project code format requirement is violated.
- Branch 2:
  - Preconditions:
    - A project with `code = {code}` exists in the database. This can be satisfied by direct insertion or by calling `POST /api/project` with a valid `title` and reusing the generated code.
    - More than one `Problem` row exists with `code = {code}`. This can be satisfied by directly inserting duplicate problem rows for the same code or by calling `POST /api/problem/{code}` more than once with valid problem bodies.
  - Failure endpoint:
    `DELETE /api/problem/{code}/all`
  - Why this fails:
    The native subproblem delete uses a scalar subquery for `idx`. Multiple problem rows for the same code can make the subquery return multiple values, causing a database exception. The service catches the exception and returns false, and the controller throws `ResourceNotFoundException`, mapped to `404 Not Found`.
  - Intentionally violated constraints:
    The one-problem-per-code assumption of the recursive delete query is violated.
- Branch 3:
  - Preconditions:
    - No problem row exists with `code = {code}`. This can be produced by direct database omission or by intentionally not calling `POST /api/problem/{code}`.
  - Failure endpoint:
    `DELETE /api/problem/{code}/all`
  - Why this fails:
    The endpoint is intended to report `404 Not Found` when deletion fails, but the service does not inspect affected-row counts. Native deletes affecting zero rows may still return success, so this can become a no-op `200 OK`.
  - Intentionally violated constraints:
    The required problem state for recursive deletion is omitted.
- Branch 4:
  - Preconditions:
    - A project with `code = {code}` exists in the database. This can be satisfied by direct insertion or by calling `POST /api/project` with a valid `title` and reusing the generated code.
    - A problem row exists with `code = {code}`. This can be satisfied by direct insertion or by calling `POST /api/problem/{code}` with a non-null problem `title`.
    - No subproblem row exists for the matching problem. This can be produced by direct database omission or by intentionally not calling `POST /api/subproblem`.
  - Failure endpoint:
    `DELETE /api/problem/{code}/all`
  - Why this fails:
    The subproblem delete can affect zero rows and still allow the following problem delete to run. The endpoint may return `200 OK`, so the missing subproblem setup is not necessarily visible as a failure even though the recursive cleanup state is absent.
  - Intentionally violated constraints:
    The expected dependent subproblem state is omitted.

Endpoint coverage:
- Covers:
  `DELETE /api/problem/{code}/all`
- Distinct meaning:
  This endpoint directly attempts recursive cleanup of subproblems before deleting problems for a code.

### Function 9: create subproblem for problem

Function name:
create subproblem for problem

Core endpoint(s):
- `POST /api/subproblem`

Preconditions:
- A project with `code = {code}` exists in the database. This can be satisfied by directly inserting a `Project` row with `code = {code}` and a non-null `title`, or by calling `POST /api/project` with a valid `title`.
- A `Problem` row exists under that project code. This can be satisfied by directly inserting a problem row linked to the project, or by calling `POST /api/problem/{code}` with the generated `{code}` and a JSON body containing non-null `title`.
- The target problem `idx` is known. This can be satisfied by direct database lookup or by calling `GET /api/problem/{code}` and reading the returned problem `idx`.
- The `pro_idx` value in the `POST /api/subproblem` body must equal the target problem `idx`. If the API is used to establish the problem, `idx` must be taken from `GET /api/problem/{code}` because problem creation returns no body containing the new `idx`.

Successful execution:
- Result:
  Creates a `subproblem` row linked to the existing problem identified by `pro_idx`.
- Invocation:
  Step 1: `POST /api/subproblem` with JSON body containing `pro_idx` and `content`.
- Constraints:
  `content` must be non-null and at least 10 characters long. The `subProblem` entity also requires non-null `content` no longer than 200 characters. `pro_idx` must identify an existing problem row.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A project with `code = {code}` exists in the database. This can be satisfied by direct insertion or by calling `POST /api/project` with a valid `title` and reusing the generated code.
    - A problem row exists under that project code. This can be satisfied by direct insertion or by calling `POST /api/problem/{code}` with a non-null problem `title`.
    - The target problem `idx` is known. This can be satisfied by direct database lookup or by calling `GET /api/problem/{code}` and reading the returned problem `idx`.
    - The subproblem request body uses that valid `pro_idx` but supplies `content` shorter than 10 characters or an empty string.
  - Failure endpoint:
    `POST /api/subproblem`
  - Why this fails:
    `subProblemController.saveSubProblem` checks `problemBody.getContent().length() < 10` or empty content and throws `DataFormatException`, which is mapped to `400 Bad Request`.
  - Intentionally violated constraints:
    The subproblem content minimum length requirement is violated.
- Branch 2:
  - Preconditions:
    - No problem row exists with `idx = {pro_idx}`. This can be produced by direct database omission, by not calling `POST /api/problem/{code}` and `GET /api/problem/{code}` to obtain a valid id, or by supplying a wrong or stale `pro_idx`.
  - Failure endpoint:
    `POST /api/subproblem`
  - Why this fails:
    `problemService.getProblemById(pro_idx)` returns empty and the controller throws `ResourceNotFoundException`, which is mapped to `404 Not Found`.
  - Intentionally violated constraints:
    The required parent problem id is missing, wrong, or stale.
- Branch 3:
  - Preconditions:
    - The subproblem request body omits `content` or sets `content` to null.
  - Failure endpoint:
    `POST /api/subproblem`
  - Why this fails:
    The controller calls `problemBody.getContent().length()` before checking for null, causing an unhandled null dereference rather than a controlled validation response.
  - Intentionally violated constraints:
    The required non-null subproblem content value is omitted.
- Branch 4:
  - Preconditions:
    - A project with `code = {code}` exists in the database. This can be satisfied by direct insertion or by calling `POST /api/project` with a valid `title` and reusing the generated code.
    - A problem row exists under that project code. This can be satisfied by direct insertion or by calling `POST /api/problem/{code}` with a non-null problem `title`.
    - The target problem `idx` is known. This can be satisfied by direct database lookup or by calling `GET /api/problem/{code}` and reading the returned problem `idx`.
    - The subproblem request body uses that valid `pro_idx` but supplies `content` longer than 200 characters.
  - Failure endpoint:
    `POST /api/subproblem`
  - Why this fails:
    `subProblem.content` has `@Size(max=200)`. Persistence can reject longer content, `subProblemServiceImpl.saveSubProblem` returns false, and the controller returns `500 Internal Server Error`.
  - Intentionally violated constraints:
    The subproblem content maximum persistence length is violated.

Endpoint coverage:
- Covers:
  `POST /api/subproblem`
- Distinct meaning:
  This endpoint directly adds a subproblem to an existing problem by `pro_idx`.
