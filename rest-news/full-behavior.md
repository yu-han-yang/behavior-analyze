### Behavior 1: retrieve country names

Behavior name:
retrieve country names

Successful execution:
- Result:
  This behavior returns the configured list of country names accepted by the API’s country validator.
- Endpoint sequence:
  Step 1: `GET /countries`
- Constraints:
  No prerequisite resource state is required. The response is the `country_list.txt` resource loaded by `CountryList`.

Failure or exceptional branches:
- No implementation-defined failure branch is visible in `src`. Swagger lists generic `401`, `403`, and `404`, but the controller contains no authentication, authorization, or not-found logic for this endpoint.

Endpoint coverage:
- Covers:
  `GET /countries`
- Distinct meaning:
  Discovery endpoint for valid country names.

### Behavior 2: create news

Behavior name:
create news

Successful execution:
- Result:
  This behavior creates a news item with an author id, text, server-generated creation time, country, and generated numeric id.
- Endpoint sequence:
  Step 1: `POST /news`
- Constraints:
  The request body must contain `authorId`, `text`, and `country`. It must not contain `id`, `newsId`, or `creationTime`. `country` must match the configured country list case-insensitively. `authorId` must be non-blank and at most 32 characters. `text` must be non-blank and at most 1024 characters. The implementation returns `201` with the generated id in the response body, although Swagger’s response schema does not fully describe that `201` body.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    A create request must not provide an existing id.
  - Endpoint group:
    Step 1: `POST /news`
  - Failure endpoint:
    `POST /news`
  - Why this fails:
    The implementation rejects the request with `400` when either `id` or `newsId` is present.
  - Intentionally violated constraints:
    The body includes `id` or `newsId`.

- Branch 2:
  - Unsatisfied condition:
    A create request must let the server assign creation time.
  - Endpoint group:
    Step 1: `POST /news`
  - Failure endpoint:
    `POST /news`
  - Why this fails:
    The implementation rejects the request with `400` when `creationTime` is present.
  - Intentionally violated constraints:
    The body includes `creationTime`.

- Branch 3:
  - Unsatisfied condition:
    A create request must provide all required content fields.
  - Endpoint group:
    Step 1: `POST /news`
  - Failure endpoint:
    `POST /news`
  - Why this fails:
    The implementation returns `400` if `authorId`, `text`, or `country` is missing.
  - Intentionally violated constraints:
    One or more of `authorId`, `text`, or `country` is omitted or null.

- Branch 4:
  - Unsatisfied condition:
    Stored news must satisfy entity validation.
  - Endpoint group:
    Step 1: `POST /news`
  - Failure endpoint:
    `POST /news`
  - Why this fails:
    Persistence validation raises `ConstraintViolationException`, and the endpoint returns `400`.
  - Intentionally violated constraints:
    `country` is not in the configured country list, `authorId` is blank or over 32 characters, or `text` is blank or over 1024 characters.

Endpoint coverage:
- Covers:
  `POST /news`
- Distinct meaning:
  Creates a new server-assigned news resource.

### Behavior 3: list all news

Behavior name:
list all news

Successful execution:
- Result:
  This behavior returns all currently stored news items.
- Endpoint sequence:
  Step 1: `GET /news`
- Constraints:
  Both `country` and `authorId` query parameters must be omitted or blank. No prerequisite resource is required; the endpoint can succeed with an empty list.

Failure or exceptional branches:
- No implementation-defined failure branch is visible in `src`. Swagger lists generic `401`, `403`, and `404`, but the implementation always returns `200` for this controller path when reached.

Endpoint coverage:
- Covers:
  `GET /news`
- Distinct meaning:
  Collection listing with no filters.

### Behavior 4: list news by country

Behavior name:
list news by country

Successful execution:
- Result:
  This behavior returns stored news items whose `country` field matches the supplied `country` query value.
- Endpoint sequence:
  Step 1: `GET /news`
- Constraints:
  `country` must be supplied as a non-blank query parameter. `authorId` must be omitted or blank. No prerequisite resource is required; this search can succeed with an empty list. The implementation does not validate the query country against `/countries`.

Failure or exceptional branches:
- No implementation-defined failure branch is visible in `src`; unmatched or invalid country values simply produce an empty result set.

Endpoint coverage:
- Covers:
  `GET /news`
- Distinct meaning:
  Country-filtered news search.

### Behavior 5: list news by author

Behavior name:
list news by author

Successful execution:
- Result:
  This behavior returns stored news items whose `authorId` field matches the supplied `authorId` query value.
- Endpoint sequence:
  Step 1: `GET /news`
- Constraints:
  `authorId` must be supplied as a non-blank query parameter. `country` must be omitted or blank. No prerequisite resource is required; this search can succeed with an empty list.

Failure or exceptional branches:
- No implementation-defined failure branch is visible in `src`; unmatched author ids simply produce an empty result set.

Endpoint coverage:
- Covers:
  `GET /news`
- Distinct meaning:
  Author-filtered news search.

### Behavior 6: list news by country and author

Behavior name:
list news by country and author

Successful execution:
- Result:
  This behavior returns stored news items matching both the supplied `country` and `authorId`.
- Endpoint sequence:
  Step 1: `GET /news`
- Constraints:
  Both `country` and `authorId` must be supplied as non-blank query parameters. No prerequisite resource is required; this search can succeed with an empty list. The implementation applies both filters together.

Failure or exceptional branches:
- No implementation-defined failure branch is visible in `src`; unmatched combinations simply produce an empty result set.

Endpoint coverage:
- Covers:
  `GET /news`
- Distinct meaning:
  Combined country-and-author filtered news search.

### Behavior 7: retrieve news by id

Behavior name:
retrieve news by id

Successful execution:
- Result:
  This behavior retrieves the news item identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /news`
  Step 2: `GET /news/{id}`
- Constraints:
  Step 1 must create the news resource. Step 2 must reuse the generated id returned in the Step 1 response body as `{id}`. The returned DTO contains both `newsId` and deprecated `id`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{id}` must be numeric.
  - Endpoint group:
    Step 1: `GET /news/{id}`
  - Failure endpoint:
    `GET /news/{id}`
  - Why this fails:
    The implementation parses `{id}` as `Long`; parsing failure returns `404`.
  - Intentionally violated constraints:
    `{id}` is non-numeric.

- Branch 2:
  - Unsatisfied condition:
    A news item with the numeric `{id}` must exist.
  - Endpoint group:
    Step 1: `GET /news/{id}`
  - Failure endpoint:
    `GET /news/{id}`
  - Why this fails:
    The repository lookup returns no entity, so the implementation returns `404`.
  - Intentionally violated constraints:
    The prerequisite `POST /news` that would create the referenced id is intentionally omitted, or `{id}` is not the id returned by any successful create call.

Endpoint coverage:
- Covers:
  `GET /news/{id}`
- Distinct meaning:
  Resource-specific read by generated news id.

### Behavior 8: replace existing news

Behavior name:
replace existing news

Successful execution:
- Result:
  This behavior replaces the mutable fields of an existing news item: `text`, `authorId`, `country`, and `creationTime`.
- Endpoint sequence:
  Step 1: `POST /news`
  Step 2: `PUT /news/{id}`
- Constraints:
  Step 2 must reuse the id returned by Step 1 as `{id}`. The Step 2 JSON body must contain either `newsId` or deprecated `id` equal to `{id}`; if both are supplied, `newsId` is used. The body must contain non-null `text`, `authorId`, `country`, and `creationTime`. `country`, `authorId`, and `text` must satisfy the same persistence validation as creation. Swagger lists `200` and `201`, but the implementation returns `204` on success.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The body id must be present and numeric.
  - Endpoint group:
    Step 1: `PUT /news/{id}`
  - Failure endpoint:
    `PUT /news/{id}`
  - Why this fails:
    The implementation reads `newsId` first, falling back to deprecated `id`, then parses it as `Long`. Missing or non-numeric body id returns `404`.
  - Intentionally violated constraints:
    The body omits both `newsId` and `id`, or supplies a non-numeric value.

- Branch 2:
  - Unsatisfied condition:
    The body id must match the path id.
  - Endpoint group:
    Step 1: `POST /news`
    Step 2: `PUT /news/{id}`
  - Failure endpoint:
    `PUT /news/{id}`
  - Why this fails:
    The implementation rejects attempts to change the resource id and returns `409`.
  - Intentionally violated constraints:
    Step 2 uses the id returned by Step 1 as `{id}`, but the JSON body contains a different `newsId` or `id`.

- Branch 3:
  - Unsatisfied condition:
    The target news resource must already exist.
  - Endpoint group:
    Step 1: `PUT /news/{id}`
  - Failure endpoint:
    `PUT /news/{id}`
  - Why this fails:
    The implementation checks `existsById` and returns `404` when the id is absent.
  - Intentionally violated constraints:
    The prerequisite `POST /news` is intentionally omitted, while the body id still matches `{id}`.

- Branch 4:
  - Unsatisfied condition:
    A replacement body must include every required field.
  - Endpoint group:
    Step 1: `POST /news`
    Step 2: `PUT /news/{id}`
  - Failure endpoint:
    `PUT /news/{id}`
  - Why this fails:
    The implementation returns `400` if `text`, `authorId`, `country`, or `creationTime` is null.
  - Intentionally violated constraints:
    Step 2 reuses the id from Step 1, but omits or nulls one or more required replacement fields.

- Branch 5:
  - Unsatisfied condition:
    Replacement values must satisfy validation.
  - Endpoint group:
    Step 1: `POST /news`
    Step 2: `PUT /news/{id}`
  - Failure endpoint:
    `PUT /news/{id}`
  - Why this fails:
    Repository update triggers validation and returns `400` on `ConstraintViolationException`.
  - Intentionally violated constraints:
    Step 2 uses an invalid country, blank or overlong `authorId`, or blank or overlong `text`.

Endpoint coverage:
- Covers:
  `PUT /news/{id}`
- Distinct meaning:
  Full replacement of an existing news item; implementation does not create missing resources via PUT.

### Behavior 9: update news text

Behavior name:
update news text

Successful execution:
- Result:
  This behavior changes only the `text` field of an existing news item.
- Endpoint sequence:
  Step 1: `POST /news`
  Step 2: `PUT /news/{id}/text`
- Constraints:
  Step 2 must reuse the id returned by Step 1 as `{id}`. The Step 2 request body is `text/plain` and becomes the new text. The text must be non-blank and at most 1024 characters. Swagger lists `200` and `201`, but the implementation returns `204` on success.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The target news resource must exist.
  - Endpoint group:
    Step 1: `PUT /news/{id}/text`
  - Failure endpoint:
    `PUT /news/{id}/text`
  - Why this fails:
    The implementation checks `existsById` and returns `404` when no entity has that id.
  - Intentionally violated constraints:
    The prerequisite `POST /news` is intentionally omitted.

- Branch 2:
  - Unsatisfied condition:
    Text must satisfy entity validation.
  - Endpoint group:
    Step 1: `POST /news`
    Step 2: `PUT /news/{id}/text`
  - Failure endpoint:
    `PUT /news/{id}/text`
  - Why this fails:
    The repository update violates the `text` constraints and the endpoint returns `400`.
  - Intentionally violated constraints:
    Step 2 reuses the id from Step 1, but the plain-text body is blank or longer than 1024 characters.

- Branch 3:
  - Unsatisfied condition:
    `{id}` must bind as a numeric `Long`.
  - Endpoint group:
    Step 1: `PUT /news/{id}/text`
  - Failure endpoint:
    `PUT /news/{id}/text`
  - Why this fails:
    The controller path variable is typed as `Long`; a non-numeric path value cannot satisfy that binding and is rejected as a bad request.
  - Intentionally violated constraints:
    `{id}` is non-numeric.

Endpoint coverage:
- Covers:
  `PUT /news/{id}/text`
- Distinct meaning:
  Partial update of only the text field.

### Behavior 10: delete news

Behavior name:
delete news

Successful execution:
- Result:
  This behavior deletes the news item identified by `{id}`.
- Endpoint sequence:
  Step 1: `POST /news`
  Step 2: `DELETE /news/{id}`
- Constraints:
  Step 2 must reuse the generated id returned in the Step 1 response body as `{id}`. Swagger lists `204` and also a generic `200`; the implementation returns `204` on success.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{id}` must be numeric.
  - Endpoint group:
    Step 1: `DELETE /news/{id}`
  - Failure endpoint:
    `DELETE /news/{id}`
  - Why this fails:
    The implementation parses `{id}` as `Long`; parsing failure returns `400`.
  - Intentionally violated constraints:
    `{id}` is non-numeric.

- Branch 2:
  - Unsatisfied condition:
    The target news resource must exist at deletion time.
  - Endpoint group:
    Step 1: `POST /news`
    Step 2: `DELETE /news/{id}`
    Step 3: `DELETE /news/{id}`
  - Failure endpoint:
    `DELETE /news/{id}`
  - Why this fails:
    Step 2 deletes the entity. Step 3 reuses the same id after it no longer exists, so the implementation returns `404`. Swagger does not document this `404`, but the source does.
  - Intentionally violated constraints:
    Step 3 attempts to delete an id whose resource state was removed by Step 2.

Endpoint coverage:
- Covers:
  `DELETE /news/{id}`
- Distinct meaning:
  Deletes an existing generated news resource.