### Function 1: create contact

Function name:
create contact

Core endpoint(s):
- `POST /contacts`

Preconditions:
- None. Creating a contact does not require an existing contact in the in-memory contact store.

Successful execution:
- Result:
  A new contact is created from a JSON contact payload. The implementation maps `ContactResourceInput` to `Contact`, assigns a generated numeric `id`, sets `createdDateTime` and `lastModifiedDateTime`, stores the contact in the static in-memory `CONTACTS` list, and returns a `Location` header pointing to `/contacts/{id}`.
- Invocation:
  Step 1: `POST /contacts` with `Content-Type: application/json` and a `ContactResourceInput` body containing a nonblank `name`; optional body fields are `birthday`, `work`, `phones`, `emails`, `groups`, `favorite`, and `notes`.
- Constraints:
  The body is validated with `@Valid`, and `ContactResourceInput.name` is annotated with `@NotBlank`. The client cannot choose `id`, `createdDateTime`, or `lastModifiedDateTime`; those fields are not part of the input resource and are set by the service. OpenAPI documents `200 OK`, but the implementation returns `201 Created` with `Location: /contacts/{id}`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No persisted contact state is required. The failing request body has `name` missing, null, empty, or blank.
  - Failure endpoint:
    `POST /contacts`
  - Why this fails:
    Spring validates the `@RequestBody` because the controller parameter is annotated with `@Valid`. `ContactResourceInput.name @NotBlank` rejects the request, and `WebApiExceptionHandler.handleMethodArgumentNotValid` returns a validation error with `422 Unprocessable Entity`.
  - Intentionally violated constraints:
    The required nonblank `ContactResourceInput.name` value is omitted or blank.

Endpoint coverage:
- Covers:
  `POST /contacts`
- Distinct meaning:
  This endpoint directly creates a contact and produces the generated contact id through the implementation's `Location` response header.

### Function 2: list contacts

Function name:
list contacts

Core endpoint(s):
- `GET /contacts`

Preconditions:
- None. Listing contacts reads the whole in-memory contact collection and can succeed when the collection is empty.

Successful execution:
- Result:
  The current static in-memory contact collection is returned as a JSON array. When contacts exist, each item is mapped to `ContactResourceOutput`, including generated `id`, `createdDateTime`, and `lastModifiedDateTime`.
- Invocation:
  Step 1: `GET /contacts` with `Accept: application/json` if a JSON response is requested.
- Constraints:
  No specific contact id or prior setup endpoint is required. The controller delegates to `service.findContacts()` and returns `200 OK` with an array, including an empty array when no contacts exist.

Failure or exceptional branches:
- No implementation-specific failure branch is visible in `src` for this endpoint when invoked as `GET /contacts`.

Endpoint coverage:
- Covers:
  `GET /contacts`
- Distinct meaning:
  This endpoint directly reads the contact collection without requiring a specific pre-existing contact.

### Function 3: retrieve contact by id

Function name:
retrieve contact by id

Core endpoint(s):
- `GET /contacts/{id}`

Preconditions:
- A contact with id `{id}` exists in the in-memory contact store. This can be satisfied by directly seeding the static `CONTACTS` list with a `Contact` whose `id = {id}`, `name` is nonblank, and timestamp fields are populated as needed, or by calling `POST /contacts` with `Content-Type: application/json` and a valid `ContactResourceInput` body containing a nonblank `name`.
- If the API is used to establish the contact, the `{id}` path value used by `GET /contacts/{id}` must be obtained from the `Location: /contacts/{id}` response header returned by `POST /contacts`.

Successful execution:
- Result:
  The existing contact identified by `{id}` is returned as `ContactResourceOutput`.
- Invocation:
  Step 1: `GET /contacts/{id}` with the generated or pre-seeded `{id}` path value and `Accept: application/json` if a JSON response is requested.
- Constraints:
  The `{id}` path value must equal an existing contact id in the in-memory `CONTACTS` list at the time of lookup. The controller calls `service.findContact(id)` and maps the found `Contact` to `ContactResourceOutput`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No contact with id `{id}` exists in the in-memory contact store. This can be produced by starting from an empty `CONTACTS` list, by deleting the contact beforehand, by using an id never returned by `POST /contacts`, or by intentionally not directly seeding a `Contact` with `id = {id}`.
  - Failure endpoint:
    `GET /contacts/{id}`
  - Why this fails:
    The controller calls `service.findContact(id).orElseThrow(ResourceNotFoundException::new)`. When the service cannot find a matching contact id, `ResourceNotFoundException` produces `404 Not Found`.
  - Intentionally violated constraints:
    The required existing contact state for `{id}` is omitted or the consumed `{id}` does not match any stored contact.

Endpoint coverage:
- Covers:
  `GET /contacts/{id}`
- Distinct meaning:
  This endpoint directly retrieves one existing contact by generated or pre-seeded id.

### Function 4: replace contact editable fields

Function name:
replace contact editable fields

Core endpoint(s):
- `PUT /contacts/{id}`

Preconditions:
- A contact with id `{id}` exists in the in-memory contact store. This can be satisfied by directly seeding the static `CONTACTS` list with a `Contact` whose `id = {id}`, `name` is nonblank, and timestamp fields are populated as needed, or by calling `POST /contacts` with `Content-Type: application/json` and a valid `ContactResourceInput` body containing a nonblank `name`.
- If the API is used to establish the contact, the `{id}` path value used by `PUT /contacts/{id}` must be obtained from the `Location: /contacts/{id}` response header returned by `POST /contacts`.

Successful execution:
- Result:
  The existing contact's editable fields are replaced from the full JSON contact input. The stored `id` and `createdDateTime` remain on the existing `Contact`, and `lastModifiedDateTime` is refreshed by the service.
- Invocation:
  Step 1: `PUT /contacts/{id}` with `Content-Type: application/json`, the existing `{id}` path value, and a valid `ContactResourceInput` body containing a nonblank `name`.
- Constraints:
  The `{id}` path value must identify an existing contact. The request body must satisfy `ContactResourceInput.name @NotBlank`. Optional editable fields omitted from the replacement body are mapped through MapStruct as null values on the existing contact. OpenAPI documents `200 OK`, but the implementation returns `204 No Content`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No contact with id `{id}` exists in the in-memory contact store. This can be produced by starting from an empty `CONTACTS` list, by using an id never returned by `POST /contacts`, or by intentionally not directly seeding a `Contact` with `id = {id}`.
    - The replacement body is otherwise valid JSON for `ContactResourceInput` and contains a nonblank `name`.
  - Failure endpoint:
    `PUT /contacts/{id}`
  - Why this fails:
    The controller looks up the contact before applying the update. `service.findContact(id)` returns empty, so `ResourceNotFoundException` is thrown and Spring returns `404 Not Found`.
  - Intentionally violated constraints:
    The required existing contact state for `{id}` is omitted.
- Branch 2:
  - Preconditions:
    - A contact with id `{id}` exists in the in-memory contact store. This can be satisfied by direct in-memory setup or by calling `POST /contacts` with a valid contact body and reusing the generated id from the `Location` header.
    - The replacement request body has `name` missing, null, empty, or blank.
  - Failure endpoint:
    `PUT /contacts/{id}`
  - Why this fails:
    `@Valid` rejects the request body before the update is applied. `WebApiExceptionHandler.handleMethodArgumentNotValid` returns a validation error with `422 Unprocessable Entity`.
  - Intentionally violated constraints:
    The replacement body violates `ContactResourceInput.name @NotBlank`.

Endpoint coverage:
- Covers:
  `PUT /contacts/{id}`
- Distinct meaning:
  This endpoint directly replaces editable fields of an existing contact.

### Function 5: partially update contact with JSON Patch

Function name:
partially update contact with JSON Patch

Core endpoint(s):
- `PATCH /contacts/{id}`

Preconditions:
- A contact with id `{id}` exists in the in-memory contact store. This can be satisfied by directly seeding the static `CONTACTS` list with a `Contact` whose `id = {id}`, `name` is nonblank, and any fields targeted by the JSON Patch operations are present in the expected shape, or by calling `POST /contacts` with `Content-Type: application/json` and a valid `ContactResourceInput` body containing a nonblank `name`.
- If the API is used to establish the contact, the `{id}` path value used by `PATCH /contacts/{id}` must be obtained from the `Location: /contacts/{id}` response header returned by `POST /contacts`.

Successful execution:
- Result:
  A JSON Patch document is applied to the existing contact's input representation. The patched input is validated, mapped back onto the existing `Contact`, stored in the in-memory list, and `lastModifiedDateTime` is refreshed.
- Invocation:
  Step 1: `PATCH /contacts/{id}` with `Content-Type: application/json-patch+json`, the existing `{id}` path value, and a JSON Patch array such as operations against `/name`, `/birthday`, `/work/title`, `/phones/0/phone`, `/emails`, `/groups`, `/favorite`, or `/notes`.
- Constraints:
  The `{id}` path value must identify an existing contact. The body must be readable by `JsonPatchHttpMessageConverter`, which calls `reader.readArray()`, so the implementation requires a JSON Patch array even though OpenAPI documents the `application/json-patch+json` schema as `type: object`. Patch paths and operation preconditions must match the current contact input JSON, and the patched result must still satisfy `ContactResourceInput.name @NotBlank`. OpenAPI documents `200 OK`, but the implementation returns `204 No Content`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No contact with id `{id}` exists in the in-memory contact store. This can be produced by starting from an empty `CONTACTS` list, by using an id never returned by `POST /contacts`, or by intentionally not directly seeding a `Contact` with `id = {id}`.
    - The JSON Patch body is otherwise syntactically valid and uses `Content-Type: application/json-patch+json`.
  - Failure endpoint:
    `PATCH /contacts/{id}`
  - Why this fails:
    The controller looks up the contact before applying the patch. `service.findContact(id)` returns empty, so `ResourceNotFoundException` is thrown and Spring returns `404 Not Found`.
  - Intentionally violated constraints:
    The required existing contact state for `{id}` is omitted.
- Branch 2:
  - Preconditions:
    - A contact with id `{id}` exists in the in-memory contact store. This can be satisfied by direct in-memory setup or by calling `POST /contacts` with a valid contact body and reusing the generated id from the `Location` header.
    - The JSON Patch array contains an operation whose path or operation precondition does not match the current contact input representation, such as replacing `/work/title` when `work` is absent or removing an array index that does not exist.
  - Failure endpoint:
    `PATCH /contacts/{id}`
  - Why this fails:
    `PatchHelper.patch` converts the contact input to a JSON structure and calls `patch.apply(target)`. Patch application failures are caught and wrapped in `UnprocessableEntityException`, which produces `422 Unprocessable Entity`.
  - Intentionally violated constraints:
    The JSON Patch operation path or operation-specific precondition is invalid for the current stored contact state.
- Branch 3:
  - Preconditions:
    - A contact with id `{id}` exists in the in-memory contact store. This can be satisfied by direct in-memory setup or by calling `POST /contacts` with a valid contact body and reusing the generated id from the `Location` header.
    - The JSON Patch array applies successfully but removes `name` or replaces `/name` with null, an empty string, or a blank string.
  - Failure endpoint:
    `PATCH /contacts/{id}`
  - Why this fails:
    After applying the patch, `PatchHelper.convertAndValidate` converts the patched JSON back to `ContactResourceInput` and validates it. `ContactResourceInput.name @NotBlank` fails, and `WebApiExceptionHandler.handleConstraintViolation` returns `422 Unprocessable Entity`.
  - Intentionally violated constraints:
    The patched contact input does not contain a nonblank `name`.

Endpoint coverage:
- Covers:
  `PATCH /contacts/{id}`
- Distinct meaning:
  This endpoint directly performs a partial update using JSON Patch when `Content-Type` is `application/json-patch+json`.

### Function 6: partially update contact with JSON Merge Patch

Function name:
partially update contact with JSON Merge Patch

Core endpoint(s):
- `PATCH /contacts/{id}`

Preconditions:
- A contact with id `{id}` exists in the in-memory contact store. This can be satisfied by directly seeding the static `CONTACTS` list with a `Contact` whose `id = {id}`, `name` is nonblank, and fields suitable for the intended merge patch are present as needed, or by calling `POST /contacts` with `Content-Type: application/json` and a valid `ContactResourceInput` body containing a nonblank `name`.
- If the API is used to establish the contact, the `{id}` path value used by `PATCH /contacts/{id}` must be obtained from the `Location: /contacts/{id}` response header returned by `POST /contacts`.

Successful execution:
- Result:
  A JSON Merge Patch document is applied to the existing contact's input representation. Object members in the merge patch update editable fields, null members remove fields, the patched input is validated and mapped back onto the existing `Contact`, and `lastModifiedDateTime` is refreshed.
- Invocation:
  Step 1: `PATCH /contacts/{id}` with `Content-Type: application/merge-patch+json`, the existing `{id}` path value, and a JSON Merge Patch document such as `{"name":"Johnny Appleseed"}`.
- Constraints:
  The `{id}` path value must identify an existing contact. The body must be readable by `JsonMergePatchHttpMessageConverter`, which accepts a JSON value via `reader.readValue()`. The patched result must still satisfy `ContactResourceInput.name @NotBlank`; setting optional fields to null removes them, but setting `name` to null, empty, or blank is invalid. OpenAPI documents `200 OK`, but the implementation returns `204 No Content`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No contact with id `{id}` exists in the in-memory contact store. This can be produced by starting from an empty `CONTACTS` list, by using an id never returned by `POST /contacts`, or by intentionally not directly seeding a `Contact` with `id = {id}`.
    - The JSON Merge Patch body is otherwise syntactically valid and uses `Content-Type: application/merge-patch+json`.
  - Failure endpoint:
    `PATCH /contacts/{id}`
  - Why this fails:
    The controller looks up the contact before applying the merge patch. `service.findContact(id)` returns empty, so `ResourceNotFoundException` is thrown and Spring returns `404 Not Found`.
  - Intentionally violated constraints:
    The required existing contact state for `{id}` is omitted.
- Branch 2:
  - Preconditions:
    - A contact with id `{id}` exists in the in-memory contact store. This can be satisfied by direct in-memory setup or by calling `POST /contacts` with a valid contact body and reusing the generated id from the `Location` header.
    - The JSON Merge Patch document applies successfully but sets `name` to null, an empty string, or a blank string.
  - Failure endpoint:
    `PATCH /contacts/{id}`
  - Why this fails:
    `PatchHelper.mergePatch` applies the merge patch and then `PatchHelper.convertAndValidate` converts the patched JSON back to `ContactResourceInput` and validates it. `ContactResourceInput.name @NotBlank` fails, and `WebApiExceptionHandler.handleConstraintViolation` returns `422 Unprocessable Entity`.
  - Intentionally violated constraints:
    The patched contact input does not contain a nonblank `name`.

Endpoint coverage:
- Covers:
  `PATCH /contacts/{id}`
- Distinct meaning:
  This endpoint directly performs a partial update using JSON Merge Patch when `Content-Type` is `application/merge-patch+json`.

### Function 7: delete contact

Function name:
delete contact

Core endpoint(s):
- `DELETE /contacts/{id}`

Preconditions:
- A contact with id `{id}` exists in the in-memory contact store. This can be satisfied by directly seeding the static `CONTACTS` list with a `Contact` whose `id = {id}` and `name` is nonblank, or by calling `POST /contacts` with `Content-Type: application/json` and a valid `ContactResourceInput` body containing a nonblank `name`.
- If the API is used to establish the contact, the `{id}` path value used by `DELETE /contacts/{id}` must be obtained from the `Location: /contacts/{id}` response header returned by `POST /contacts`.

Successful execution:
- Result:
  The existing contact identified by `{id}` is removed from the static in-memory `CONTACTS` list.
- Invocation:
  Step 1: `DELETE /contacts/{id}` with the existing `{id}` path value.
- Constraints:
  The `{id}` path value must identify an existing contact at the time deletion is requested. The controller looks up the contact before deletion and delegates to `service.deleteContact(contact)`. OpenAPI documents `200 OK`, but the implementation returns `204 No Content`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No contact with id `{id}` exists in the in-memory contact store. This can be produced by starting from an empty `CONTACTS` list, by using an id never returned by `POST /contacts`, by deleting the contact beforehand, or by intentionally not directly seeding a `Contact` with `id = {id}`.
  - Failure endpoint:
    `DELETE /contacts/{id}`
  - Why this fails:
    The controller calls `service.findContact(id).orElseThrow(ResourceNotFoundException::new)` before deletion. When no matching contact is found, `ResourceNotFoundException` produces `404 Not Found`.
  - Intentionally violated constraints:
    The required existing contact state for `{id}` is omitted or the contact was already removed before this delete request.

Endpoint coverage:
- Covers:
  `DELETE /contacts/{id}`
- Distinct meaning:
  This endpoint directly deletes one existing contact by generated or pre-seeded id.
