### Function 1: list hospitals

Function name:
list hospitals

Core endpoint(s):
- `GET /v1/hospitais/`

Preconditions:
- None. The endpoint reads the hospital collection and can return an empty list when no hospital documents exist.

Successful execution:
- Result:
  Returns all hospital records as `HospitalDTO` objects.
- Invocation:
  Step 1: `GET /v1/hospitais/`
- Constraints:
  No specific hospital id, request body, query parameter, or persisted hospital state is required.

Failure or exceptional branches:
- None visible in the API contract or controller logic beyond repository/runtime errors, which the controller catches as a bad request.

Endpoint coverage:
- Covers:
  `GET /v1/hospitais/`
- Distinct meaning:
  Global hospital collection listing.

### Function 2: create hospital

Function name:
create hospital

Core endpoint(s):
- `POST /v1/hospitais/`

Preconditions:
- None. This endpoint creates the hospital state itself from the submitted `HospitalDTO` body.

Successful execution:
- Result:
  Creates a hospital with `name`, `address`, `beds`, `availableBeds`, and an associated geocoded hospital `Location`.
- Invocation:
  Step 1: `POST /v1/hospitais/` with a JSON `HospitalDTO` body containing `name`, `address`, `beds`, and `availableBeds`, with `id` omitted or `null`.
- Constraints:
  `name` and `address` must not be empty strings. `beds` and `availableBeds` must be non-negative. The implementation geocodes `address`; if geocoding returns no result, the saved location uses coordinates `0,0`. OpenAPI documents `200` and `201`, but the controller returns `200 OK` with a `HospitalDTO` on success.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No prior hospital state is required; the invalid state is entirely in the request body.
  - Failure endpoint:
    `POST /v1/hospitais/`
  - Why this fails:
    The controller rejects a body where `name` is an empty string, `address` is an empty string, `beds` is negative, or `availableBeds` is negative, then returns a bad request.
  - Intentionally violated constraints:
    The submitted `HospitalDTO` field validation requirements are violated.
- Branch 2:
  - Preconditions:
    - A hospital with body `id = {sourceHospitalId}` either does not exist or already exists in the database. If it exists, it can be satisfied by directly inserting a `Hospital` document with `id = {sourceHospitalId}` or by calling `POST /v1/hospitais/` without an id and reusing the returned `id`.
  - Failure endpoint:
    `POST /v1/hospitais/`
  - Why this fails:
    When `HospitalDTO.id` is non-null, `HospitalService.fromDTO` treats it as a lookup id instead of a create id. If the lookup id is absent, lookup fails. If the lookup succeeds, the controller attempts to insert the existing hospital object again, which can fail as a duplicate id.
  - Intentionally violated constraints:
    The create request supplies `HospitalDTO.id` instead of omitting it.

Endpoint coverage:
- Covers:
  `POST /v1/hospitais/`
- Distinct meaning:
  Creates a new hospital and its associated location.

### Function 3: retrieve hospital by id

Function name:
retrieve hospital by id

Core endpoint(s):
- `GET /v1/hospitais/{hospital_id}`

Preconditions:
- A hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document with that id or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- The `{hospital_id}` path value used by `GET /v1/hospitais/{hospital_id}` must identify that hospital. If the API is used to create the hospital, `{hospital_id}` must be obtained from the returned `HospitalDTO.id`.

Successful execution:
- Result:
  Retrieves the hospital identified by `{hospital_id}` as a `HospitalDTO`.
- Invocation:
  Step 1: `GET /v1/hospitais/{hospital_id}` with `{hospital_id}` set to an existing hospital id.
- Constraints:
  `{hospital_id}` must match a persisted hospital document id.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No hospital with `id = {hospital_id}` exists in the database. This can be produced by starting from an empty database, deleting the hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `GET /v1/hospitais/{hospital_id}`
  - Why this fails:
    `HospitalService.findById` throws when the id is absent, and the controller returns not found.
  - Intentionally violated constraints:
    The required hospital id state is omitted or an unknown id is used.

Endpoint coverage:
- Covers:
  `GET /v1/hospitais/{hospital_id}`
- Distinct meaning:
  Reads one hospital record by id.

### Function 4: update hospital from request body

Function name:
update hospital

Core endpoint(s):
- `PUT /v1/hospitais/{hospital_id}`

Preconditions:
- A target hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- The `{hospital_id}` path value used by `PUT /v1/hospitais/{hospital_id}` must identify the target hospital. If the API is used to create the target, `{hospital_id}` must be obtained from the returned `HospitalDTO.id`.

Successful execution:
- Result:
  Updates the target hospital's `name`, `address`, `beds`, and `availableBeds` from the request body.
- Invocation:
  Step 1: `PUT /v1/hospitais/{hospital_id}` with `{hospital_id}` set to an existing hospital id and a JSON `HospitalDTO` body whose `id` is omitted or `null`.
- Constraints:
  Body `id` must be omitted or `null` for direct field update. Updated `name` and `address` must not be empty strings, and updated `beds` and `availableBeds` must be non-negative. The implementation sets the converted object's id to the path id before saving.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No hospital with `id = {hospital_id}` exists in the database. This can be produced by starting from an empty database, deleting the hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `PUT /v1/hospitais/{hospital_id}`
  - Why this fails:
    The update service looks up the path id and returns a bad request when it is absent.
  - Intentionally violated constraints:
    The target hospital id does not identify a persisted hospital.
- Branch 2:
  - Preconditions:
    - A target hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body and reusing the returned `id`.
  - Failure endpoint:
    `PUT /v1/hospitais/{hospital_id}`
  - Why this fails:
    The controller rejects empty `name`, empty `address`, negative `beds`, or negative `availableBeds` before saving the update.
  - Intentionally violated constraints:
    The update body violates the hospital field validation requirements.

Endpoint coverage:
- Covers:
  `PUT /v1/hospitais/{hospital_id}`
- Distinct meaning:
  Normal field update when body `id` is omitted or `null`.

### Function 5: copy another hospital's stored data into a hospital

Function name:
copy hospital data

Core endpoint(s):
- `PUT /v1/hospitais/{hospital_id}`

Preconditions:
- A source hospital exists with `id = {sourceHospitalId}` and the field values to copy. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body and reusing the returned source `id`.
- A target hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a separate `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body and reusing the returned target `id`.
- The request body `id` must be `{sourceHospitalId}`, while the path `{hospital_id}` must be the target hospital id. If the API is used to establish either hospital, both ids must be obtained from their respective creation responses.

Successful execution:
- Result:
  Copies stored `name`, `address`, `beds`, and `availableBeds` from the source hospital into the target hospital.
- Invocation:
  Step 1: `PUT /v1/hospitais/{hospital_id}` with `{hospital_id}` set to the target hospital id and a JSON `HospitalDTO` body containing `id = {sourceHospitalId}`.
- Constraints:
  This is an implementation-backed effect that likely disagrees with the intended API contract. When body `id` is non-null, `HospitalService.fromDTO` loads that existing hospital and ignores the submitted body fields; the controller then overwrites the loaded object's id with the path id and saves the loaded field values into the target hospital.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A target hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body and reusing the returned id.
    - No hospital with `id = {sourceHospitalId}` exists in the database. This can be produced by starting from an empty database for that id, deleting the source hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/` for the source.
  - Failure endpoint:
    `PUT /v1/hospitais/{hospital_id}`
  - Why this fails:
    The implementation first resolves the body `id`; if it is absent, the request fails before updating the path hospital.
  - Intentionally violated constraints:
    The body `id` does not identify an existing source hospital.

Endpoint coverage:
- Covers:
  `PUT /v1/hospitais/{hospital_id}`
- Distinct meaning:
  Non-null body `id` causes a copy-from-existing-hospital effect instead of normal request-body update.

### Function 6: delete hospital

Function name:
delete hospital

Core endpoint(s):
- `DELETE /v1/hospitais/{hospital_id}`

Preconditions:
- A hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- The `{hospital_id}` path value used by `DELETE /v1/hospitais/{hospital_id}` must identify that hospital. If the API is used to create the hospital, `{hospital_id}` must be obtained from the returned `HospitalDTO.id`.

Successful execution:
- Result:
  Deletes the hospital identified by `{hospital_id}` and returns a confirmation string.
- Invocation:
  Step 1: `DELETE /v1/hospitais/{hospital_id}` with `{hospital_id}` set to an existing hospital id.
- Constraints:
  `{hospital_id}` must identify an existing hospital. Deletion removes the hospital document; the implementation does not separately cascade-delete patients, products, or locations.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No hospital with `id = {hospital_id}` exists in the database. This can be produced by starting from an empty database, deleting the hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `DELETE /v1/hospitais/{hospital_id}`
  - Why this fails:
    The controller looks up the hospital before deleting; a missing id is caught and returned as a bad request.
  - Intentionally violated constraints:
    The target hospital id does not identify a persisted hospital.

Endpoint coverage:
- Covers:
  `DELETE /v1/hospitais/{hospital_id}`
- Distinct meaning:
  Deletes one hospital record.

### Function 7: get available beds

Function name:
get available beds

Core endpoint(s):
- `GET /v1/hospitais/{id}/leitos`

Preconditions:
- A hospital exists with `id = {id}` and an `availableBeds` value. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- The `{id}` path value used by `GET /v1/hospitais/{id}/leitos` must identify that hospital. If the API is used to create the hospital, `{id}` must be obtained from the returned `HospitalDTO.id`.

Successful execution:
- Result:
  Returns a map containing `{ "leitos": availableBeds }` for the hospital identified by `{id}`.
- Invocation:
  Step 1: `GET /v1/hospitais/{id}/leitos` with `{id}` set to an existing hospital id.
- Constraints:
  `{id}` must identify an existing hospital. This controller method does not wrap exceptions in `ResponseEntity`, so lookup failures propagate through Spring's exception handling rather than the local bad-request/not-found pattern used by other methods.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No hospital with `id = {id}` exists in the database. This can be produced by starting from an empty database, deleting the hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `GET /v1/hospitais/{id}/leitos`
  - Why this fails:
    `HospitalService.findById` throws before the response map can be built.
  - Intentionally violated constraints:
    The required hospital id state is omitted or an unknown id is used.

Endpoint coverage:
- Covers:
  `GET /v1/hospitais/{id}/leitos`
- Distinct meaning:
  Reads the current available bed count.

### Function 8: find nearest hospital with available beds

Function name:
find nearest hospital with beds

Core endpoint(s):
- `GET /v1/hospitais/maisProximo`

Preconditions:
- A hospital exists with `availableBeds > 0`, a `Location` with category `HOSPITAL`, and coordinates within `{raioMaximo}` of the request coordinates after the implementation's coordinate ordering is applied. This can be satisfied by directly inserting matching `Hospital` and `Location` documents or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body whose `address` geocodes to the intended nearby coordinates.
- If the API is used to create the hospital, the returned `HospitalDTO.id` identifies the candidate hospital, while the endpoint itself selects candidates by stored location and name rather than by id.

Successful execution:
- Result:
  Returns the first nearby hospital whose `availableBeds` value is greater than zero.
- Invocation:
  Step 1: `GET /v1/hospitais/maisProximo?lat={lat}&lon={lon}&raioMaximo={raioMaximo}` with required numeric query parameters.
- Constraints:
  At least one stored hospital location must fall within the radius and map back to a hospital with `availableBeds > 0`. The implementation passes query `lat` into the service's `longitude` argument and query `lon` into the `latitude` argument, so the OpenAPI parameter names and internal coordinate order disagree.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No stored hospital location within the effective `{raioMaximo}` search radius maps to a hospital with `availableBeds > 0`. This can be produced by directly inserting only hospitals with `availableBeds = 0`, by placing all hospital locations outside the radius, or by calling `POST /v1/hospitais/` only for hospitals whose created location is out of range or has no available beds.
  - Failure endpoint:
    `GET /v1/hospitais/maisProximo`
  - Why this fails:
    The service filters nearby hospitals to `availableBeds > 0` and throws when none remain.
  - Intentionally violated constraints:
    No hospital in range has available beds.

Endpoint coverage:
- Covers:
  `GET /v1/hospitais/maisProximo`
- Distinct meaning:
  Location-based hospital search constrained by bed availability.

### Function 9: list hospital stock

Function name:
list hospital stock

Core endpoint(s):
- `GET /v1/hospitais/{hospital_id}/estoque`

Preconditions:
- A hospital exists with `id = {hospital_id}` and a `products` list, which may be empty. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- The `{hospital_id}` path value used by `GET /v1/hospitais/{hospital_id}/estoque` must identify that hospital. If the API is used to create the hospital, `{hospital_id}` must be obtained from the returned `HospitalDTO.id`.

Successful execution:
- Result:
  Lists products currently referenced by the hospital identified by `{hospital_id}`.
- Invocation:
  Step 1: `GET /v1/hospitais/{hospital_id}/estoque` with `{hospital_id}` set to an existing hospital id.
- Constraints:
  `{hospital_id}` must identify an existing hospital. The list may be empty.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No hospital with `id = {hospital_id}` exists in the database. This can be produced by starting from an empty database, deleting the hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `GET /v1/hospitais/{hospital_id}/estoque`
  - Why this fails:
    Product listing first resolves the hospital id.
  - Intentionally violated constraints:
    The required hospital id state is omitted or an unknown id is used.

Endpoint coverage:
- Covers:
  `GET /v1/hospitais/{hospital_id}/estoque`
- Distinct meaning:
  Reads a hospital's stock list.

### Function 10: add product to hospital stock

Function name:
add product to stock

Core endpoint(s):
- `POST /v1/hospitais/{hospital_id}/estoque`

Preconditions:
- A hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- The `{hospital_id}` path value used by `POST /v1/hospitais/{hospital_id}/estoque` must identify that hospital. If the API is used to create the hospital, `{hospital_id}` must be obtained from the returned `HospitalDTO.id`.

Successful execution:
- Result:
  Saves a product and returns its `ProductDTO`.
- Invocation:
  Step 1: `POST /v1/hospitais/{hospital_id}/estoque` with `{hospital_id}` set to an existing hospital id and a JSON `ProductDTO` body containing `productName`, `description`, `quantity`, and `productType`.
- Constraints:
  `productType` must deserialize as `COMMON` or `BLOOD`. The service saves the product before resolving the hospital, then mutates the hospital's in-memory product list. It does not save the hospital afterward, so the stock association may not persist even though the product is returned. OpenAPI documents `200` and `201`, but the controller returns `200 OK` with a `ProductDTO` on success.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No hospital with `id = {hospital_id}` exists in the database. This can be produced by starting from an empty database, deleting the hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `POST /v1/hospitais/{hospital_id}/estoque`
  - Why this fails:
    The product is saved first, then hospital lookup fails and the controller returns a bad request. This can leave a saved product without a hospital stock reference.
  - Intentionally violated constraints:
    The hospital-scoped path id does not identify a persisted hospital.

Endpoint coverage:
- Covers:
  `POST /v1/hospitais/{hospital_id}/estoque`
- Distinct meaning:
  Creates a stock product through a hospital-scoped path.

### Function 11: retrieve stock product by id

Function name:
retrieve stock product

Core endpoint(s):
- `GET /v1/hospitais/{hospital_id}/estoque/{produto_id}`

Preconditions:
- A hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- A product exists with `id = {produto_id}`. This can be satisfied by directly inserting a `Product` document or by calling `POST /v1/hospitais/{hospital_id}/estoque` with a valid `ProductDTO` body.
- The `{produto_id}` path value must identify the product. If the API is used to create the product, `{produto_id}` must be obtained from the returned `ProductDTO.id`. The implementation does not require the product to belong to `{hospital_id}`.

Successful execution:
- Result:
  Retrieves the product identified by `{produto_id}` as a `ProductDTO`.
- Invocation:
  Step 1: `GET /v1/hospitais/{hospital_id}/estoque/{produto_id}` with `{produto_id}` set to an existing product id.
- Constraints:
  `{produto_id}` must identify an existing product. The implementation accepts `{hospital_id}` in the route but does not validate that the hospital exists or that the product belongs to that hospital.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No product with `id = {produto_id}` exists in the database. This can be produced by starting from an empty product collection, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/{hospital_id}/estoque`.
  - Failure endpoint:
    `GET /v1/hospitais/{hospital_id}/estoque/{produto_id}`
  - Why this fails:
    Product lookup by `{produto_id}` throws and the controller returns not found.
  - Intentionally violated constraints:
    The product id does not identify a persisted product.

Endpoint coverage:
- Covers:
  `GET /v1/hospitais/{hospital_id}/estoque/{produto_id}`
- Distinct meaning:
  Reads one product by id; hospital scoping is documented but not enforced by implementation.

### Function 12: update stock product

Function name:
update stock product

Core endpoint(s):
- `PUT /v1/hospitais/{hospital_id}/estoque/{produto_id}`

Preconditions:
- A hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- A product exists with `id = {produto_id}`. This can be satisfied by directly inserting a `Product` document or by calling `POST /v1/hospitais/{hospital_id}/estoque` with a valid `ProductDTO` body.
- The `{produto_id}` path value must identify the product. If the API is used to create the product, `{produto_id}` must be obtained from the returned `ProductDTO.id`. The implementation does not require the product to belong to `{hospital_id}`.

Successful execution:
- Result:
  Updates product `name`, `description`, `quantity`, and `productType`.
- Invocation:
  Step 1: `PUT /v1/hospitais/{hospital_id}/estoque/{produto_id}` with `{produto_id}` set to an existing product id and a JSON `ProductDTO` body containing updated values.
- Constraints:
  `{produto_id}` must identify an existing product. The path hospital id is accepted by the route but is not checked by the update service. The controller overwrites body `id` with the path product id before updating.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No product with `id = {produto_id}` exists in the database. This can be produced by starting from an empty product collection, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/{hospital_id}/estoque`.
  - Failure endpoint:
    `PUT /v1/hospitais/{hospital_id}/estoque/{produto_id}`
  - Why this fails:
    The service looks up the product by path `{produto_id}` and returns a bad request when absent.
  - Intentionally violated constraints:
    The product id does not identify a persisted product.

Endpoint coverage:
- Covers:
  `PUT /v1/hospitais/{hospital_id}/estoque/{produto_id}`
- Distinct meaning:
  Updates one product by id; hospital membership is not enforced.

### Function 13: delete stock product

Function name:
delete stock product

Core endpoint(s):
- `DELETE /v1/hospitais/{hospital_id}/estoque/{produto_id}`

Preconditions:
- A hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- A product exists with `id = {produto_id}`. This can be satisfied by directly inserting a `Product` document or by calling `POST /v1/hospitais/{hospital_id}/estoque` with a valid `ProductDTO` body.
- The `{produto_id}` path value must identify the product, and `{hospital_id}` must identify an existing hospital. If the API is used to establish these resources, the ids must be obtained from the creation responses. The implementation does not require the product to actually belong to the hospital.

Successful execution:
- Result:
  Deletes the product identified by `{produto_id}` and attempts to remove it from the hospital's product list.
- Invocation:
  Step 1: `DELETE /v1/hospitais/{hospital_id}/estoque/{produto_id}` with both path ids set to existing resources.
- Constraints:
  Product and hospital must both exist. Any existing hospital id allows deletion of any existing product id because membership is not enforced.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No product with `id = {produto_id}` exists in the database. This can be produced by starting from an empty product collection, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/{hospital_id}/estoque`.
  - Failure endpoint:
    `DELETE /v1/hospitais/{hospital_id}/estoque/{produto_id}`
  - Why this fails:
    The controller looks up the product before deleting and returns a bad request when absent.
  - Intentionally violated constraints:
    The product id does not identify a persisted product.
- Branch 2:
  - Preconditions:
    - A hospital exists with `id = {createdHospitalId}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
    - A product exists with `id = {produto_id}`. This can be satisfied by directly inserting a `Product` document or by calling `POST /v1/hospitais/{createdHospitalId}/estoque` with a valid `ProductDTO` body and reusing the returned `ProductDTO.id`.
    - No hospital with `id = {hospital_id}` exists for the deletion path. This can be produced by deleting that hospital beforehand or by intentionally not inserting it directly and not calling `POST /v1/hospitais/` for that id.
  - Failure endpoint:
    `DELETE /v1/hospitais/{hospital_id}/estoque/{produto_id}`
  - Why this fails:
    Product lookup succeeds, but hospital lookup inside deletion fails.
  - Intentionally violated constraints:
    The product id is valid, but the hospital id in the deletion path is unknown.

Endpoint coverage:
- Covers:
  `DELETE /v1/hospitais/{hospital_id}/estoque/{produto_id}`
- Distinct meaning:
  Deletes one product by id.

### Function 14: list nearby locations for a hospital

Function name:
list nearby locations

Core endpoint(s):
- `GET /v1/hospitais/{hospital_id}/proximidades`

Preconditions:
- A hospital exists with `id = {hospital_id}` and a non-null stored `Location`. This can be satisfied by directly inserting a `Hospital` document with an embedded location or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body, which saves a hospital location from the address.
- The `{hospital_id}` path value used by `GET /v1/hospitais/{hospital_id}/proximidades` must identify that hospital. If the API is used to create the hospital, `{hospital_id}` must be obtained from the returned `HospitalDTO.id`.

Successful execution:
- Result:
  Returns locations within 100 km of the hospital's stored location.
- Invocation:
  Step 1: `GET /v1/hospitais/{hospital_id}/proximidades` with `{hospital_id}` set to an existing hospital id.
- Constraints:
  `{hospital_id}` must identify a hospital with a stored location. The implementation filters locations by `referenceId != hospital_id`; hospital-created locations use the hospital address as `referenceId`, so the hospital's own location may still appear in the result.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No hospital with `id = {hospital_id}` exists in the database. This can be produced by starting from an empty database, deleting the hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `GET /v1/hospitais/{hospital_id}/proximidades`
  - Why this fails:
    Nearby-location search first loads the hospital.
  - Intentionally violated constraints:
    The required hospital id state is omitted or an unknown id is used.

Endpoint coverage:
- Covers:
  `GET /v1/hospitais/{hospital_id}/proximidades`
- Distinct meaning:
  Location search around one hospital.

### Function 15: list nearby hospitals for a hospital

Function name:
list nearby hospitals

Core endpoint(s):
- `GET /v1/hospitais/{hospital_id}/hospitaisProximos`

Preconditions:
- A hospital exists with `id = {hospital_id}` and a non-null stored `Location`. This can be satisfied by directly inserting a `Hospital` document with an embedded location or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body, which saves a hospital location from the address.
- The `{hospital_id}` path value used by `GET /v1/hospitais/{hospital_id}/hospitaisProximos` must identify that hospital. If the API is used to create the hospital, `{hospital_id}` must be obtained from the returned `HospitalDTO.id`.

Successful execution:
- Result:
  Returns hospitals whose locations are near the hospital identified by `{hospital_id}` within query radius `{raio}`.
- Invocation:
  Step 1: `GET /v1/hospitais/{hospital_id}/hospitaisProximos?raio={raio}` with `{hospital_id}` set to an existing hospital id and `raio` set to a numeric search radius.
- Constraints:
  `{hospital_id}` must exist. OpenAPI marks `raio` as required, and Spring request binding requires it for the controller method. The service only returns location records with category `HOSPITAL` whose location name differs from the origin hospital name, then maps each location name back to a hospital by case-insensitive name search.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No hospital with `id = {hospital_id}` exists in the database. This can be produced by starting from an empty database, deleting the hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `GET /v1/hospitais/{hospital_id}/hospitaisProximos`
  - Why this fails:
    The service must load the origin hospital before searching nearby hospitals.
  - Intentionally violated constraints:
    The required hospital id state is omitted or an unknown id is used.

Endpoint coverage:
- Covers:
  `GET /v1/hospitais/{hospital_id}/hospitaisProximos`
- Distinct meaning:
  Hospital-to-hospital proximity search.

### Function 16: list hospital patients

Function name:
list hospital patients

Core endpoint(s):
- `GET /v1/hospitais/{hospital_id}/pacientes`

Preconditions:
- A hospital exists with `id = {hospital_id}` and a `patients` list, which may be empty. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- The `{hospital_id}` path value used by `GET /v1/hospitais/{hospital_id}/pacientes` must identify that hospital. If the API is used to create the hospital, `{hospital_id}` must be obtained from the returned `HospitalDTO.id`.

Successful execution:
- Result:
  Returns the patient list referenced by the hospital identified by `{hospital_id}`.
- Invocation:
  Step 1: `GET /v1/hospitais/{hospital_id}/pacientes` with `{hospital_id}` set to an existing hospital id.
- Constraints:
  `{hospital_id}` must exist. A non-null empty patient list is still a successful result.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No hospital with `id = {hospital_id}` exists in the database. This can be produced by starting from an empty database, deleting the hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `GET /v1/hospitais/{hospital_id}/pacientes`
  - Why this fails:
    The controller loads the hospital before reading its patient list.
  - Intentionally violated constraints:
    The required hospital id state is omitted or an unknown id is used.

Endpoint coverage:
- Covers:
  `GET /v1/hospitais/{hospital_id}/pacientes`
- Distinct meaning:
  Reads patients currently associated with one hospital.

### Function 17: check in patient

Function name:
check in patient

Core endpoint(s):
- `POST /v1/hospitais/{hospital_id}/pacientes/checkin`

Preconditions:
- A hospital exists with `id = {hospital_id}` and `availableBeds > 0`. This can be satisfied by directly inserting a `Hospital` document with positive `availableBeds` or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body whose `availableBeds` is greater than zero.
- The `{hospital_id}` path value used by `POST /v1/hospitais/{hospital_id}/pacientes/checkin` must identify that hospital. If the API is used to create the hospital, `{hospital_id}` must be obtained from the returned `HospitalDTO.id`.

Successful execution:
- Result:
  Saves a patient, marks the patient active, sets `entryDate`, clears `exitDate`, adds the patient to the hospital, decrements available beds, and returns the saved `Patient`.
- Invocation:
  Step 1: `POST /v1/hospitais/{hospital_id}/pacientes/checkin` with `{hospital_id}` set to an existing hospital id and a JSON `Patient` body.
- Constraints:
  The hospital must exist and `availableBeds` must be greater than zero. The returned `Patient.id` is needed for later patient-specific operations.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No hospital with `id = {hospital_id}` exists in the database. This can be produced by starting from an empty database, deleting the hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `POST /v1/hospitais/{hospital_id}/pacientes/checkin`
  - Why this fails:
    The controller cannot load the hospital and returns not found.
  - Intentionally violated constraints:
    The required hospital id state is omitted or an unknown id is used.
- Branch 2:
  - Preconditions:
    - A hospital exists with `id = {hospital_id}` and `availableBeds = 0`. This can be satisfied by directly inserting a `Hospital` document with no available beds or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body whose `availableBeds` is zero.
  - Failure endpoint:
    `POST /v1/hospitais/{hospital_id}/pacientes/checkin`
  - Why this fails:
    `HospitalService.checkIn` rejects hospitals where `availableBeds <= 0`; the controller catches the exception as not found.
  - Intentionally violated constraints:
    The hospital has no available bed for the check-in.

Endpoint coverage:
- Covers:
  `POST /v1/hospitais/{hospital_id}/pacientes/checkin`
- Distinct meaning:
  Admits a patient into a hospital.

### Function 18: check out patient

Function name:
check out patient

Core endpoint(s):
- `POST /v1/hospitais/{hospital_id}/pacientes/checkout`

Preconditions:
- A hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- A patient exists in the hospital's `patients` list with `id = {patientId}`. This can be satisfied by directly inserting a `Patient` document and linking it in the hospital's `patients` DBRef list or by calling `POST /v1/hospitais/{hospital_id}/pacientes/checkin` with a `Patient` body.
- The checkout request body must be the patient id string `{patientId}`. If the API is used to check in the patient, `{patientId}` must be obtained from the returned `Patient.id`.

Successful execution:
- Result:
  Removes a patient from the hospital, increments available beds, marks the patient inactive, sets `exitDate`, and returns the updated patient.
- Invocation:
  Step 1: `POST /v1/hospitais/{hospital_id}/pacientes/checkout` with `{hospital_id}` set to an existing hospital id and the request body set to the patient id string.
- Constraints:
  The checkout body is a raw string patient id, not a patient object. That id must come from a patient currently present in the same hospital's patient list.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
    - No patient with `id = {patientId}` is present in that hospital's `patients` list. This can be produced by omitting direct linkage, by not calling `POST /v1/hospitais/{hospital_id}/pacientes/checkin`, or by using a patient id linked to another hospital.
  - Failure endpoint:
    `POST /v1/hospitais/{hospital_id}/pacientes/checkout`
  - Why this fails:
    The service searches the hospital's patient list and throws when the body id is absent.
  - Intentionally violated constraints:
    The patient id is not currently checked into the specified hospital.
- Branch 2:
  - Preconditions:
    - No hospital with `id = {hospital_id}` exists in the database. This can be produced by starting from an empty database, deleting the hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `POST /v1/hospitais/{hospital_id}/pacientes/checkout`
  - Why this fails:
    Hospital lookup fails before patient lookup.
  - Intentionally violated constraints:
    The required hospital id state is omitted or an unknown id is used.

Endpoint coverage:
- Covers:
  `POST /v1/hospitais/{hospital_id}/pacientes/checkout`
- Distinct meaning:
  Discharges a checked-in patient.

### Function 19: retrieve patient by id

Function name:
retrieve patient

Core endpoint(s):
- `GET /v1/hospitais/{hospital_id}/pacientes/{patientId}`

Preconditions:
- A hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- A patient exists with `id = {patientId}`. This can be satisfied by directly inserting a `Patient` document or by calling `POST /v1/hospitais/{hospital_id}/pacientes/checkin` with a `Patient` body.
- The `{patientId}` path value must identify the patient. If the API is used to check in the patient, `{patientId}` must be obtained from the returned `Patient.id`. The implementation does not require the patient to belong to `{hospital_id}`.

Successful execution:
- Result:
  Retrieves the patient identified by `{patientId}`.
- Invocation:
  Step 1: `GET /v1/hospitais/{hospital_id}/pacientes/{patientId}` with `{patientId}` set to an existing patient id.
- Constraints:
  `{patientId}` must identify an existing patient. The implementation does not validate that `{hospital_id}` exists or that the patient belongs to that hospital.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No patient with `id = {patientId}` exists in the database. This can be produced by starting from an empty patient collection, deleting the patient beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/{hospital_id}/pacientes/checkin`.
  - Failure endpoint:
    `GET /v1/hospitais/{hospital_id}/pacientes/{patientId}`
  - Why this fails:
    Patient lookup by id throws and the controller returns not found.
  - Intentionally violated constraints:
    The patient id does not identify a persisted patient.

Endpoint coverage:
- Covers:
  `GET /v1/hospitais/{hospital_id}/pacientes/{patientId}`
- Distinct meaning:
  Reads one patient by id; hospital scoping is documented but not enforced.

### Function 20: update patient

Function name:
update patient

Core endpoint(s):
- `PUT /v1/hospitais/{hospital_id}/pacientes/{patientId}`

Preconditions:
- A hospital exists with `id = {hospital_id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body.
- A patient exists with `id = {patientId}`. This can be satisfied by directly inserting a `Patient` document or by calling `POST /v1/hospitais/{hospital_id}/pacientes/checkin` with a `Patient` body.
- The `{patientId}` path value must identify the patient. If the API is used to check in the patient, `{patientId}` must be obtained from the returned `Patient.id`. The implementation does not require the patient to belong to `{hospital_id}`.

Successful execution:
- Result:
  Updates a patient's `name`, `cpf`, `birthDate`, and `gender`.
- Invocation:
  Step 1: `PUT /v1/hospitais/{hospital_id}/pacientes/{patientId}` with `{patientId}` set to an existing patient id and a JSON `Patient` body containing updated values.
- Constraints:
  `{patientId}` must identify an existing patient. The implementation ignores `{hospital_id}` during update and only copies `name`, `cpf`, `birthDate`, and `gender`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No patient with `id = {patientId}` exists in the database. This can be produced by starting from an empty patient collection, deleting the patient beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/{hospital_id}/pacientes/checkin`.
  - Failure endpoint:
    `PUT /v1/hospitais/{hospital_id}/pacientes/{patientId}`
  - Why this fails:
    Patient lookup by path id fails before fields are copied.
  - Intentionally violated constraints:
    The patient id does not identify a persisted patient.

Endpoint coverage:
- Covers:
  `PUT /v1/hospitais/{hospital_id}/pacientes/{patientId}`
- Distinct meaning:
  Updates one patient by id; hospital membership is not enforced.

### Function 21: transfer product from a nearby hospital

Function name:
transfer product

Core endpoint(s):
- `POST /v1/hospitais/{id}/transferencia/{productId}`

Preconditions:
- A destination hospital exists with `id = {id}` and a stored location. This can be satisfied by directly inserting a `Hospital` document with a location or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body and reusing the returned `id`.
- A distinct nearby source hospital exists with a stored location near the destination hospital. This can be satisfied by directly inserting a source `Hospital` and matching `Location` document or by using an existing seeded hospital/location pair; `POST /v1/hospitais/` can create a source hospital and location, but the address must geocode near the destination.
- The source hospital references a `Product` with `id = {productId}` and `quantity > {quantidade} + 4`. This can be satisfied by directly inserting a `Product` document and linking it in the source hospital's `products` DBRef list. The product id can be discovered with `GET /v1/hospitais/{sourceHospitalId}/estoque`, or produced by `POST /v1/hospitais/{sourceHospitalId}/estoque`, but the implementation does not save the hospital after adding the product through that endpoint, so direct database linkage or seeded data may be required for reliable membership.
- The `{productId}` path value must identify the product referenced by the nearby source hospital, and the request body must be the integer `{quantidade}` to transfer.

Successful execution:
- Result:
  Transfers `{quantidade}` units of a product from a nearby source hospital to destination hospital `{id}`, decreases the source product quantity, creates a new destination product for the transferred quantity, and returns `transferencia realizada!`.
- Invocation:
  Step 1: `POST /v1/hospitais/{id}/transferencia/{productId}` with `{id}` set to the destination hospital id, `{productId}` set to the source product id, and an integer body `{quantidade}`.
- Constraints:
  The source hospital must be distinct from the destination, nearby according to stored locations, and must reference the product. Product quantity must satisfy `sourceQuantity > quantidade + 4`. Implementation discrepancy: the documented `POST /v1/hospitais/{hospital_id}/estoque` endpoint saves the product but does not persist the hospital's product list mutation, so a complete documented endpoint sequence cannot reliably create the source hospital stock membership; the shipped startup data seeds such memberships.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No destination hospital with `id = {id}` exists in the database. This can be produced by starting from an empty database, deleting the destination hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `POST /v1/hospitais/{id}/transferencia/{productId}`
  - Why this fails:
    The endpoint first resolves destination hospital `{id}`.
  - Intentionally violated constraints:
    The destination hospital id does not identify a persisted hospital.
- Branch 2:
  - Preconditions:
    - A destination hospital exists with `id = {id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body and reusing the returned id.
    - No product with `id = {productId}` exists in the database. This can be produced by starting from an empty product collection, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/{hospital_id}/estoque`.
  - Failure endpoint:
    `POST /v1/hospitais/{id}/transferencia/{productId}`
  - Why this fails:
    Product lookup by `{productId}` fails before nearby source selection.
  - Intentionally violated constraints:
    The product id does not identify any persisted product.
- Branch 3:
  - Preconditions:
    - A destination hospital exists with `id = {id}` and a stored location. This can be satisfied by directly inserting a `Hospital` document with a location or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body and reusing the returned id.
    - A product exists with `id = {productId}`. This can be satisfied by directly inserting a `Product` document or by calling `POST /v1/hospitais/{hospital_id}/estoque` with a valid `ProductDTO` body and reusing the returned `ProductDTO.id`.
    - No nearby distinct hospital references that product in its `products` list. This can be produced by omitting the source hospital product linkage, placing source hospitals outside the search area, or using a product id linked only to the destination or to no hospital.
  - Failure endpoint:
    `POST /v1/hospitais/{id}/transferencia/{productId}`
  - Why this fails:
    Nearby hospitals are searched, but none has the product in its hospital product list.
  - Intentionally violated constraints:
    The product exists, but no nearby distinct source hospital owns it.

Endpoint coverage:
- Covers:
  `POST /v1/hospitais/{id}/transferencia/{productId}`
- Distinct meaning:
  Performs the transfer when a nearby source hospital has enough surplus quantity.

### Function 22: refuse product transfer because source stock is too low

Function name:
refuse product transfer

Core endpoint(s):
- `POST /v1/hospitais/{id}/transferencia/{productId}`

Preconditions:
- A destination hospital exists with `id = {id}` and a stored location. This can be satisfied by directly inserting a `Hospital` document with a location or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body and reusing the returned `id`.
- A distinct nearby source hospital exists with a stored location near the destination hospital. This can be satisfied by directly inserting a source `Hospital` and matching `Location` document or by using an existing seeded hospital/location pair; `POST /v1/hospitais/` can create a source hospital and location, but the address must geocode near the destination.
- The source hospital references a `Product` with `id = {productId}` and `quantity <= {quantidade} + 4`. This can be satisfied by directly inserting a `Product` document and linking it in the source hospital's `products` DBRef list. The product id can be discovered with `GET /v1/hospitais/{sourceHospitalId}/estoque`, or produced by `POST /v1/hospitais/{sourceHospitalId}/estoque`, but the implementation does not save the hospital after adding the product through that endpoint, so direct database linkage or seeded data may be required for reliable membership.
- The `{productId}` path value must identify the product referenced by the nearby source hospital, and the request body must be the integer `{quantidade}` that makes the source quantity insufficient.

Successful execution:
- Result:
  Returns `transferencia não pode ser feita!` and does not perform the transfer because the nearby source product does not have enough surplus quantity.
- Invocation:
  Step 1: `POST /v1/hospitais/{id}/transferencia/{productId}` with `{id}` set to the destination hospital id, `{productId}` set to the source product id, and an integer body `{quantidade}` where source quantity is not greater than `{quantidade} + 4`.
- Constraints:
  Destination hospital, source hospital, and product must exist; otherwise the endpoint fails before reaching this refusal branch. This is a distinct user-visible meaning of the same transfer endpoint because it returns a successful HTTP response string but makes no transfer.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No destination hospital with `id = {id}` exists in the database. This can be produced by starting from an empty database, deleting the destination hospital beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/`.
  - Failure endpoint:
    `POST /v1/hospitais/{id}/transferencia/{productId}`
  - Why this fails:
    The endpoint resolves destination hospital `{id}` before looking up the product or checking source quantity.
  - Intentionally violated constraints:
    The destination hospital id does not identify a persisted hospital.
- Branch 2:
  - Preconditions:
    - A destination hospital exists with `id = {id}`. This can be satisfied by directly inserting a `Hospital` document or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body and reusing the returned id.
    - No product with `id = {productId}` exists in the database. This can be produced by starting from an empty product collection, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /v1/hospitais/{hospital_id}/estoque`.
  - Failure endpoint:
    `POST /v1/hospitais/{id}/transferencia/{productId}`
  - Why this fails:
    Product lookup by `{productId}` fails before nearby source selection and before the insufficient-quantity branch can be reached.
  - Intentionally violated constraints:
    The product id does not identify any persisted product.
- Branch 3:
  - Preconditions:
    - A destination hospital exists with `id = {id}` and a stored location. This can be satisfied by directly inserting a `Hospital` document with a location or by calling `POST /v1/hospitais/` with a valid `HospitalDTO` body and reusing the returned id.
    - A product exists with `id = {productId}`. This can be satisfied by directly inserting a `Product` document or by calling `POST /v1/hospitais/{hospital_id}/estoque` with a valid `ProductDTO` body and reusing the returned `ProductDTO.id`.
    - No nearby distinct hospital references that product in its `products` list. This can be produced by omitting direct hospital-product linkage, relying only on `POST /v1/hospitais/{hospital_id}/estoque` without persisted hospital linkage, or placing all source hospitals outside the search area.
  - Failure endpoint:
    `POST /v1/hospitais/{id}/transferencia/{productId}`
  - Why this fails:
    The service can resolve the destination and product, but nearby source selection throws before the insufficient-quantity branch can be reached.
  - Intentionally violated constraints:
    The source-hospital product relationship required for the refusal function is absent.

Endpoint coverage:
- Covers:
  `POST /v1/hospitais/{id}/transferencia/{productId}`
- Distinct meaning:
  Refuses transfer due to insufficient source quantity while using the same endpoint as product transfer.
