# Domain-Level Behavior Analysis

## Domain Summary

This service manages a hospital network. Its main domain resources are hospitals, hospital locations, bed capacity, patients, and stock products. Hospitals can be registered with geocoded locations, queried by distance and bed availability, assigned patients through check-in/checkout workflows, and associated with stock products. The implementation stores hospitals, patients, products, and locations in MongoDB, with patients and products referenced from hospitals through DBRef lists.

The service is operationally simple and has weak ownership validation: several hospital-scoped routes accept a `hospital_id` path value but do not verify that the patient or product belongs to that hospital. Some workflows also rely on persisted relationships that are not reliably created by the exposed API.

## Available Function Inventory

### Hospital registry and capacity

- `list hospitals`
  - Endpoint: `GET /v1/hospitais/`
  - Domain meaning: list all registered hospitals.

- `create hospital`
  - Endpoint: `POST /v1/hospitais/`
  - Domain meaning: create a hospital and a corresponding hospital location from the address.

- `retrieve hospital by id`
  - Endpoint: `GET /v1/hospitais/{hospital_id}`
  - Domain meaning: read one hospital profile.

- `update hospital`
  - Endpoint: `PUT /v1/hospitais/{hospital_id}`
  - Domain meaning: update hospital name, address, total beds, and available beds from the submitted body.

- `copy hospital data`
  - Endpoint: `PUT /v1/hospitais/{hospital_id}`
  - Domain meaning: copy stored profile data from another hospital when the request body contains an existing hospital `id`.

- `delete hospital`
  - Endpoint: `DELETE /v1/hospitais/{hospital_id}`
  - Domain meaning: delete one hospital record.

- `get available beds`
  - Endpoint: `GET /v1/hospitais/{id}/leitos`
  - Domain meaning: inspect available bed count for one hospital.

- `find nearest hospital with beds`
  - Endpoint: `GET /v1/hospitais/maisProximo`
  - Domain meaning: find a nearby hospital with available beds from coordinates.

### Hospital stock

- `list hospital stock`
  - Endpoint: `GET /v1/hospitais/{hospital_id}/estoque`
  - Domain meaning: list products referenced by a hospital.

- `add product to stock`
  - Endpoint: `POST /v1/hospitais/{hospital_id}/estoque`
  - Domain meaning: create a stock product through a hospital-scoped route.

- `retrieve stock product`
  - Endpoint: `GET /v1/hospitais/{hospital_id}/estoque/{produto_id}`
  - Domain meaning: retrieve a product by id; hospital ownership is not enforced.

- `update stock product`
  - Endpoint: `PUT /v1/hospitais/{hospital_id}/estoque/{produto_id}`
  - Domain meaning: update product fields by product id; hospital ownership is not enforced.

- `delete stock product`
  - Endpoint: `DELETE /v1/hospitais/{hospital_id}/estoque/{produto_id}`
  - Domain meaning: delete a product and attempt to remove it from a hospital stock list.

- `transfer product`
  - Endpoint: `POST /v1/hospitais/{id}/transferencia/{productId}`
  - Domain meaning: transfer product quantity from a nearby source hospital to a destination hospital.

- `refuse product transfer`
  - Endpoint: `POST /v1/hospitais/{id}/transferencia/{productId}`
  - Domain meaning: return a successful refusal message when source stock is too low.

### Location and proximity

- `list nearby locations`
  - Endpoint: `GET /v1/hospitais/{hospital_id}/proximidades`
  - Domain meaning: list stored locations near a hospital.

- `list nearby hospitals`
  - Endpoint: `GET /v1/hospitais/{hospital_id}/hospitaisProximos`
  - Domain meaning: list nearby hospitals around a hospital within a radius.

### Patient admission and records

- `list hospital patients`
  - Endpoint: `GET /v1/hospitais/{hospital_id}/pacientes`
  - Domain meaning: list patients referenced by a hospital.

- `check in patient`
  - Endpoint: `POST /v1/hospitais/{hospital_id}/pacientes/checkin`
  - Domain meaning: admit a patient into a hospital, mark active, and consume one available bed.

- `check out patient`
  - Endpoint: `POST /v1/hospitais/{hospital_id}/pacientes/checkout`
  - Domain meaning: discharge a patient from a hospital, mark inactive, and release one bed.

- `retrieve patient`
  - Endpoint: `GET /v1/hospitais/{hospital_id}/pacientes/{patientId}`
  - Domain meaning: retrieve a patient by id; hospital ownership is not enforced.

- `update patient`
  - Endpoint: `PUT /v1/hospitais/{hospital_id}/pacientes/{patientId}`
  - Domain meaning: update patient demographic fields by patient id; hospital ownership is not enforced.

## Supported Business Behaviors

### Behavior 1: Register A Hospital

Business goal:
Create a hospital that can later be used for capacity checks, patient admission, stock operations, and proximity search.

Domain context:
Hospital is the central aggregate for this service. Patient, stock, bed, and proximity workflows all depend on a hospital id and, for location workflows, a stored hospital location.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Hospital A"`, `address="valid or free-text address"`, `beds=20`, and `availableBeds=10` to create the hospital and trigger location creation from the address.

Optional verification workflow:
1. Use function `retrieve hospital by id` (`GET /v1/hospitais/{hospital_id}`) with `{hospital_id}={created HospitalDTO.id}` to inspect the created hospital.
2. Use function `list hospitals` (`GET /v1/hospitais/`) to verify the hospital appears in the global collection.

Existing-state shortcuts:
- None for the core creation behavior.
- Direct database insertion of a `Hospital` plus matching `Location` can replace this workflow only when the inserted hospital has a valid id and location state equivalent to the API-created state.

Parameter and value bindings:
- The returned `HospitalDTO.id` becomes `{hospital_id}` or `{id}` in later hospital, patient, stock, bed, and proximity functions.
- The submitted `address` is reused internally as the created location `referenceId`.
- The submitted `name` is reused as the hospital location name and later drives location-to-hospital mapping in nearby hospital search.

Business result:
A hospital document exists with name, address, total beds, available beds, and an embedded/stored hospital location. If geocoding finds no address result, the location is still created with coordinates `0,0`.

Constraints and invariants:
- `name` and `address` must not be empty strings.
- `beds` and `availableBeds` must be non-negative.
- The implementation does not enforce `availableBeds <= beds`.
- OpenAPI documents `200` and `201`, but the controller returns `200 OK`.

Failure and exceptional cases:
- Failing function: `create hospital`
  - Failure condition: empty `name`, empty `address`, negative `beds`, or negative `availableBeds`.
  - Why it fails: controller validation throws before insert.
  - Violated prerequisite or constraint: hospital profile fields must be non-empty and non-negative.
- Failing function: `create hospital`
  - Failure condition: body `id` is non-null.
  - Why it fails: `HospitalService.fromDTO` treats body `id` as a lookup id, not as a create id; absent ids fail lookup and existing ids can cause duplicate insert behavior.
  - Violated prerequisite or constraint: creation body must omit `id` or send `id=null`.

Implementation notes:
Hospital creation geocodes through `LocationIQService`; if no result is returned, it persists a hospital location at `0,0`.

### Behavior 2: Maintain Hospital Profile

Business goal:
Read and update hospital profile data.

Domain context:
Hospital profile data controls display, total capacity, available capacity, and location-derived searches. Updates are directly persisted on the hospital document.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Hospital A"`, `address="Address A"`, `beds=20`, `availableBeds=10` to create the target hospital.
2. Use function `update hospital` (`PUT /v1/hospitais/{hospital_id}`) with `{hospital_id}={created HospitalDTO.id}` and body `id=null`, `name="Hospital A Updated"`, `address="Address B"`, `beds=25`, `availableBeds=12` to change the profile.

Optional verification workflow:
1. Use function `retrieve hospital by id` (`GET /v1/hospitais/{hospital_id}`) with `{hospital_id}={created HospitalDTO.id}` to inspect updated fields.
2. Use function `get available beds` (`GET /v1/hospitais/{id}/leitos`) with `{id}={created HospitalDTO.id}` to verify the updated available bed count.

Existing-state shortcuts:
- If the target hospital already exists, skip `create hospital` and reuse its id as `{hospital_id}`.
- Direct database setup can provide the target hospital, but the path id must match a persisted hospital.

Parameter and value bindings:
- The `HospitalDTO.id` returned by `create hospital` is reused as `{hospital_id}` in `update hospital`.
- The update body `id` must remain omitted or `null` for a normal field update.
- The update body values replace the target hospital’s `name`, `address`, `beds`, and `availableBeds`.

Business result:
The target hospital has the updated profile fields. Existing patient and product relationships are preserved because `updateData` only replaces scalar hospital fields.

Constraints and invariants:
- Updated `name` and `address` cannot be empty strings.
- Updated `beds` and `availableBeds` cannot be negative.
- The implementation does not re-geocode the hospital location after address changes, so the stored location can become stale.
- The implementation does not enforce that available beds match current checked-in patients.

Failure and exceptional cases:
- Failing function: `create hospital`
  - Failure condition: invalid creation body.
  - Why it fails: same validation as hospital creation.
  - Violated prerequisite or constraint: setup hospital must be valid.
- Failing function: `update hospital`
  - Failure condition: `{hospital_id}` does not exist.
  - Why it fails: update resolves the target hospital before saving.
  - Violated prerequisite or constraint: target hospital must exist.
- Failing function: `update hospital`
  - Failure condition: body has empty name/address or negative bed counts.
  - Why it fails: controller rejects invalid fields before service update.
  - Violated prerequisite or constraint: hospital profile fields must satisfy validation.

Implementation notes:
The address update changes the hospital document but not the associated location document or embedded location coordinates.

### Behavior 3: Copy Stored Data From One Hospital To Another

Business goal:
Overwrite one hospital’s profile using the stored data of another hospital.

Domain context:
This is not a typical business operation, but it is an implementation-backed behavior caused by how non-null `HospitalDTO.id` is handled during update.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Source Hospital"`, `address="Source Address"`, `beds=30`, `availableBeds=15` to create the source hospital.
2. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Target Hospital"`, `address="Target Address"`, `beds=10`, `availableBeds=5` to create the target hospital.
3. Use function `copy hospital data` (`PUT /v1/hospitais/{hospital_id}`) with `{hospital_id}={target HospitalDTO.id}` and body `id={source HospitalDTO.id}` to copy source stored fields into the target.

Optional verification workflow:
1. Use function `retrieve hospital by id` (`GET /v1/hospitais/{hospital_id}`) with `{hospital_id}={target HospitalDTO.id}` to verify the target now has source values.

Existing-state shortcuts:
- Skip either `create hospital` step if an equivalent source or target hospital already exists.
- Direct database setup can establish both hospitals, but body `id` must identify the source and path `{hospital_id}` must identify the target.

Parameter and value bindings:
- The source hospital id is used in the update request body as `id={sourceHospitalId}`.
- The target hospital id is used in the path as `{hospital_id}={targetHospitalId}`.
- These ids intentionally differ: body id selects source data; path id selects the target to overwrite.

Business result:
The target hospital’s `name`, `address`, `beds`, and `availableBeds` are overwritten with values loaded from the source hospital. The target id remains unchanged.

Constraints and invariants:
- Both source and target hospitals must exist.
- This behavior ignores any submitted body fields other than `id`.
- Location is not copied by `updateData`, so copied address/name can diverge from target location.

Failure and exceptional cases:
- Failing function: `copy hospital data`
  - Failure condition: source body `id` does not exist.
  - Why it fails: `fromDTO` resolves body id before the target is updated.
  - Violated prerequisite or constraint: body id must identify an existing source hospital.
- Failing function: `copy hospital data`
  - Failure condition: target path `{hospital_id}` does not exist.
  - Why it fails: `HospitalService.update` looks up the target id before saving.
  - Violated prerequisite or constraint: path id must identify an existing target hospital.

Implementation notes:
This behavior likely disagrees with normal REST update intent, but it is supported by the implementation.

### Behavior 4: Remove A Hospital

Business goal:
Delete a hospital from the registry.

Domain context:
Hospitals are operational roots for patient, stock, and location workflows. Removing a hospital should remove it from direct hospital lookup and listing.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Hospital To Delete"`, `address="Address"`, `beds=10`, `availableBeds=5` to create the target hospital.
2. Use function `delete hospital` (`DELETE /v1/hospitais/{hospital_id}`) with `{hospital_id}={created HospitalDTO.id}` to delete it.

Optional verification workflow:
1. Use function `retrieve hospital by id` (`GET /v1/hospitais/{hospital_id}`) with `{hospital_id}={deleted HospitalDTO.id}` to verify it is no longer retrievable.
2. Use function `list hospitals` (`GET /v1/hospitais/`) to inspect the remaining collection.

Existing-state shortcuts:
- If the target hospital already exists, skip `create hospital`.
- Direct database setup can create the target hospital, but deletion still requires its id.

Parameter and value bindings:
- The id returned from `create hospital` is reused as `{hospital_id}` for deletion.
- Any patient, product, or location records related to that hospital are not automatically identified or removed by this function.

Business result:
The hospital document is deleted. Products, patients, and location documents can remain in their own collections.

Constraints and invariants:
- The hospital id must exist.
- There is no cascade delete for patients, products, or locations.
- The endpoint returns a confirmation string on success.

Failure and exceptional cases:
- Failing function: `delete hospital`
  - Failure condition: `{hospital_id}` is unknown.
  - Why it fails: controller looks up the hospital before deleting.
  - Violated prerequisite or constraint: target hospital must exist.

Implementation notes:
Deleting a hospital can leave orphaned products, patients, and locations from a domain perspective.

### Behavior 5: Inspect Hospital Capacity

Business goal:
Check how many beds are currently available at a hospital.

Domain context:
Available bed count determines whether patient check-in can succeed and whether the hospital is eligible for nearest-hospital-with-beds search.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Hospital A"`, `address="Address A"`, `beds=20`, `availableBeds=10` to create a hospital with capacity.
2. Use function `get available beds` (`GET /v1/hospitais/{id}/leitos`) with `{id}={created HospitalDTO.id}` to read available beds.

Optional verification workflow:
1. Use function `retrieve hospital by id` (`GET /v1/hospitais/{hospital_id}`) with `{hospital_id}={created HospitalDTO.id}` to compare `availableBeds`.

Existing-state shortcuts:
- Skip `create hospital` if a hospital with a known id and available bed value already exists.
- Direct database setup can provide the hospital; `{id}` must match the persisted hospital id.

Parameter and value bindings:
- The created hospital id is reused as `{id}` in `get available beds`.
- The response map key is `"leitos"` and its value is the persisted `availableBeds`.

Business result:
The caller receives the current persisted available bed count.

Constraints and invariants:
- The hospital must exist.
- The function does not recalculate capacity from the patient list; it reads the stored `availableBeds`.

Failure and exceptional cases:
- Failing function: `get available beds`
  - Failure condition: `{id}` is unknown.
  - Why it fails: `HospitalService.findById` throws before the response map is built.
  - Violated prerequisite or constraint: hospital id must exist.

Implementation notes:
Unlike many other controller methods, this method does not catch exceptions locally; Spring exception handling determines the final HTTP status.

### Behavior 6: Find A Nearby Hospital With Available Beds

Business goal:
Locate a hospital near coordinates that has at least one available bed.

Domain context:
This supports patient routing or emergency redirection when a caller has geographic coordinates and needs a hospital with capacity.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Nearby Hospital"`, `address="address that geocodes near the search coordinates"`, `beds=20`, `availableBeds=5` to create a candidate hospital and location.
2. Use function `find nearest hospital with beds` (`GET /v1/hospitais/maisProximo`) with query `lat={lat}`, `lon={lon}`, and `raioMaximo={radiusKm}` to find a nearby candidate.

Optional verification workflow:
1. Use function `retrieve hospital by id` (`GET /v1/hospitais/{hospital_id}`) with `{hospital_id}={returned HospitalDTO.id}` to inspect the selected hospital.
2. Use function `get available beds` (`GET /v1/hospitais/{id}/leitos`) with `{id}={returned HospitalDTO.id}` to verify positive availability.

Existing-state shortcuts:
- Skip `create hospital` if a hospital with `availableBeds > 0` and a stored `HOSPITAL` location already exists within the effective radius.
- Direct database setup can create precise hospital and location coordinates when API geocoding is unreliable.
- Seeded hospitals can satisfy this behavior if their locations and available beds match the search.

Parameter and value bindings:
- The created hospital’s `name` must match its location name because location search maps locations back to hospitals by name.
- Query `lat`, `lon`, and `raioMaximo` drive the geospatial search.
- Implementation parameter ordering is inconsistent: the controller receives `lat, lon` but calls the service in a way that treats query `lat` as longitude and query `lon` as latitude.

Business result:
The response is the first nearby hospital DTO with `availableBeds > 0`.

Constraints and invariants:
- At least one hospital location must be inside the search radius.
- The mapped hospital must have positive `availableBeds`.
- Results depend on stored location coordinates, not only the hospital document.

Failure and exceptional cases:
- Failing function: `create hospital`
  - Failure condition: invalid body or address geocodes to `0,0` unexpectedly.
  - Why it fails or weakens setup: invalid fields block creation; failed geocoding creates a location but may make the hospital geographically wrong.
  - Violated prerequisite or constraint: candidate hospital must have usable location state.
- Failing function: `find nearest hospital with beds`
  - Failure condition: no nearby hospital has `availableBeds > 0`.
  - Why it fails: service filters nearby hospitals by positive availability and throws when none remain.
  - Violated prerequisite or constraint: nearby available-capacity hospital is required.

Implementation notes:
OpenAPI names the query parameters as `lat` and `lon`, but implementation passes them in reversed semantic order to `findHospitalMaisProximoComVagas`.

### Behavior 7: Manage Hospital Stock Product

Business goal:
Create, inspect, update, list, and delete stock products associated with hospital operations.

Domain context:
Hospitals keep stock such as common supplies and blood. Products have name, description, quantity, and type.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Hospital A"`, `address="Address A"`, `beds=10`, `availableBeds=5` to create the stock-owning hospital.
2. Use function `add product to stock` (`POST /v1/hospitais/{hospital_id}/estoque`) with `{hospital_id}={created HospitalDTO.id}` and body `name="Blood Pack"`, `description="O+"`, `quantity=10`, `productType="BLOOD"` to create the product.
3. Use function `update stock product` (`PUT /v1/hospitais/{hospital_id}/estoque/{produto_id}`) with `{hospital_id}={created HospitalDTO.id}`, `{produto_id}={created ProductDTO.id}`, and body `name="Blood Pack"`, `description="O+ updated"`, `quantity=12`, `productType="BLOOD"` to update stock fields.
4. Use function `delete stock product` (`DELETE /v1/hospitais/{hospital_id}/estoque/{produto_id}`) with `{hospital_id}={created HospitalDTO.id}` and `{produto_id}={created ProductDTO.id}` to remove the product.

Optional verification workflow:
1. Use function `retrieve stock product` (`GET /v1/hospitais/{hospital_id}/estoque/{produto_id}`) with `{hospital_id}={created HospitalDTO.id}` and `{produto_id}={created ProductDTO.id}` after creation or update.
2. Use function `list hospital stock` (`GET /v1/hospitais/{hospital_id}/estoque`) with `{hospital_id}={created HospitalDTO.id}` to inspect products referenced by the hospital.

Existing-state shortcuts:
- Skip `create hospital` if a hospital already exists.
- Skip `add product to stock` for update/delete if a product already exists and its id is known.
- Direct database setup can create both product and hospital-product DBRef membership. This is more reliable for membership-sensitive workflows because API product creation does not save the hospital after adding the product to its list.

Parameter and value bindings:
- The hospital id returned by `create hospital` is reused as `{hospital_id}`.
- The product id returned by `add product to stock` is reused as `{produto_id}`.
- `productType` must be one of `COMMON` or `BLOOD`.
- The route appears hospital-scoped, but retrieve and update consume only `{produto_id}` in implementation.

Business result:
A product is created, can be updated, and can be deleted. The product document is persisted independently. Hospital stock membership may not persist after creation because the hospital is mutated in memory but not saved.

Constraints and invariants:
- Product type must deserialize to the enum.
- Negative quantity is not rejected by implementation.
- Product retrieval and update do not verify hospital ownership.
- Delete requires both the product and hospital to exist, but does not verify that the product belongs to that hospital.

Failure and exceptional cases:
- Failing function: `add product to stock`
  - Failure condition: `{hospital_id}` is unknown.
  - Why it fails: service saves the product first, then hospital lookup fails.
  - Violated prerequisite or constraint: hospital id must exist; implementation can still leave an orphan product.
- Failing function: `retrieve stock product`
  - Failure condition: `{produto_id}` is unknown.
  - Why it fails: product lookup throws.
  - Violated prerequisite or constraint: product id must exist.
- Failing function: `update stock product`
  - Failure condition: `{produto_id}` is unknown.
  - Why it fails: update first resolves product by id.
  - Violated prerequisite or constraint: product id must exist.
- Failing function: `delete stock product`
  - Failure condition: product id exists but hospital id is unknown.
  - Why it fails: product lookup succeeds, then hospital lookup inside delete fails.
  - Violated prerequisite or constraint: both ids must identify persisted resources.

Implementation notes:
The stock association bug is important: `ProductService.insert` calls `hospital.setProduct(product)` but does not save the hospital, so a following `list hospital stock` may not show the newly created product.

### Behavior 8: Transfer Product From A Nearby Hospital

Business goal:
Move a requested quantity of a product from a nearby hospital with surplus stock to a destination hospital.

Domain context:
This models inter-hospital supply support. A nearby source hospital can supply a destination hospital if it retains a buffer of more than four units after the requested transfer threshold.

Starting point:
Pre-existing database state or direct database setup is required for reliable source stock membership.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Destination Hospital"`, `address="destination address"`, `beds=10`, `availableBeds=5` to create the destination hospital and location.
2. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Source Hospital"`, `address="nearby source address"`, `beds=10`, `availableBeds=5` to create a nearby source hospital and location.
3. Establish source stock membership by direct database setup, or use seeded data, so the source hospital references a `Product` with `id={productId}` and `quantity > {quantidade}+4`.
4. Use function `transfer product` (`POST /v1/hospitais/{id}/transferencia/{productId}`) with `{id}={destination HospitalDTO.id}`, `{productId}={source product id}`, and integer body `{quantidade}=3` to perform the transfer.

Optional verification workflow:
1. Use function `list hospital stock` (`GET /v1/hospitais/{hospital_id}/estoque`) with `{hospital_id}={source hospital id}` to inspect source stock quantity if membership is persisted.
2. Use function `retrieve stock product` (`GET /v1/hospitais/{hospital_id}/estoque/{produto_id}`) with `{hospital_id}={source hospital id}` and `{produto_id}={source product id}` to inspect the decremented source product.
3. Use function `list hospital stock` (`GET /v1/hospitais/{hospital_id}/estoque`) with `{hospital_id}={destination HospitalDTO.id}` to inspect destination stock, noting the destination hospital-product link may also not be saved.

Existing-state shortcuts:
- Skip both `create hospital` steps if destination and nearby source hospitals already exist with stored locations.
- Use seeded source hospital/product links from startup data where available.
- Direct database setup is the most reliable way to ensure the source hospital owns the product because `add product to stock` does not persist hospital membership.

Parameter and value bindings:
- Destination hospital id is reused as path `{id}`.
- Source product id is reused as `{productId}`.
- The request body integer `{quantidade}` is compared against source quantity using `sourceQuantity > quantidade + 4`.
- The source hospital is selected by nearby-hospital search from the destination hospital’s stored location, then filtered by product membership.

Business result:
If allowed, a new product document is created for the destination with quantity `{quantidade}`, and the source product quantity is reduced by `{quantidade}`. The response string is `transferencia realizada!`.

Constraints and invariants:
- Destination hospital must exist and have a stored location.
- A distinct nearby source hospital must reference the product.
- The source product must have quantity strictly greater than `{quantidade}+4`.
- The implementation does not save the destination hospital after adding the new product, so destination stock membership may not persist.

Failure and exceptional cases:
- Failing function: `transfer product`
  - Failure condition: destination `{id}` does not exist.
  - Why it fails: endpoint resolves destination hospital first.
  - Violated prerequisite or constraint: destination hospital must exist.
- Failing function: `transfer product`
  - Failure condition: `{productId}` does not identify a product.
  - Why it fails: product repository lookup throws.
  - Violated prerequisite or constraint: product must exist.
- Failing function: `transfer product`
  - Failure condition: no nearby distinct hospital references the product.
  - Why it fails: nearby source selection filters by `h.getProducts().contains(produto)` and throws if none match.
  - Violated prerequisite or constraint: nearby source ownership link must exist.

Implementation notes:
A complete API-only workflow cannot reliably establish the source hospital-product membership needed for transfer. The exposed add-product endpoint saves the product but not the hospital relationship.

### Behavior 9: Refuse Product Transfer When Source Stock Is Too Low

Business goal:
Determine that a nearby source hospital cannot spare the requested stock quantity.

Domain context:
The transfer rule preserves a minimum surplus: transfer only occurs when source quantity is greater than requested quantity plus four.

Starting point:
Pre-existing database state or direct database setup is required for reliable source stock membership.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Destination Hospital"`, `address="destination address"`, `beds=10`, `availableBeds=5` to create the destination.
2. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Source Hospital"`, `address="nearby source address"`, `beds=10`, `availableBeds=5` to create the source.
3. Establish source stock membership by direct database setup, or use seeded data, so the source hospital references `productId` with `quantity <= {quantidade}+4`.
4. Use function `refuse product transfer` (`POST /v1/hospitais/{id}/transferencia/{productId}`) with `{id}={destination HospitalDTO.id}`, `{productId}={source product id}`, and integer body `{quantidade}` to trigger refusal.

Optional verification workflow:
1. Use function `retrieve stock product` (`GET /v1/hospitais/{hospital_id}/estoque/{produto_id}`) with `{hospital_id}={source hospital id}` and `{produto_id}={source product id}` to verify source quantity is unchanged.

Existing-state shortcuts:
- Skip hospital creation when suitable destination and nearby source hospitals already exist.
- Direct DB setup or seed data can provide the source product relationship and insufficient quantity.

Parameter and value bindings:
- `{quantidade}` must be large enough relative to the source product quantity to make `quantity > quantidade + 4` false.
- `{productId}` must still identify a real product referenced by a nearby source hospital; otherwise the endpoint fails instead of returning the refusal message.

Business result:
The service returns `transferencia não pode ser feita!`. No new destination product is created and source product quantity is not decremented.

Constraints and invariants:
- This is a business refusal, not an HTTP-level failure.
- Destination, product, nearby source, and source-product ownership must all exist before the insufficient-stock branch is reached.

Failure and exceptional cases:
- Failing function: `refuse product transfer`
  - Failure condition: destination hospital is missing.
  - Why it fails: destination lookup occurs first.
  - Violated prerequisite or constraint: destination hospital must exist.
- Failing function: `refuse product transfer`
  - Failure condition: product exists but no nearby source hospital owns it.
  - Why it fails: source selection fails before quantity is evaluated.
  - Violated prerequisite or constraint: source hospital must own the product.

Implementation notes:
This behavior uses the same endpoint as successful transfer, but the domain result is a refusal string with no mutation.

### Behavior 10: Explore Nearby Hospitals And Locations

Business goal:
Find places and other hospitals near a selected hospital.

Domain context:
This supports proximity-aware hospital operations such as choosing nearby facilities or inspecting stored patient/hospital locations around a hospital.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Origin Hospital"`, `address="origin address"`, `beds=10`, `availableBeds=5` to create the origin hospital and location.
2. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Nearby Hospital"`, `address="nearby address"`, `beds=10`, `availableBeds=5` to create another hospital location in range.
3. Use function `list nearby locations` (`GET /v1/hospitais/{hospital_id}/proximidades`) with `{hospital_id}={origin HospitalDTO.id}` to list nearby stored locations within 100 km.
4. Use function `list nearby hospitals` (`GET /v1/hospitais/{hospital_id}/hospitaisProximos`) with `{hospital_id}={origin HospitalDTO.id}` and query `raio=100` to list nearby hospitals.

Optional verification workflow:
1. Use function `retrieve hospital by id` (`GET /v1/hospitais/{hospital_id}`) with `{hospital_id}={nearby HospitalDTO.id}` to inspect returned nearby hospitals.

Existing-state shortcuts:
- Skip hospital creation if origin and nearby hospitals already exist with stored locations.
- Direct database setup can create non-hospital locations and precise coordinates, which is useful because the public API has no standalone location creation endpoint.

Parameter and value bindings:
- The origin hospital id is reused as `{hospital_id}` in both proximity functions.
- `list nearby hospitals` uses query `raio` as radius.
- Hospital location names are mapped back to hospital records by case-insensitive hospital name search.

Business result:
The caller receives nearby stored locations and nearby hospitals around the origin hospital.

Constraints and invariants:
- The origin hospital must exist and have a non-null location.
- Nearby hospital results require location category `HOSPITAL`.
- `list nearby locations` filters by `referenceId != hospital_id`, but hospital-created locations use address as reference id, so the origin hospital’s own location can still appear.

Failure and exceptional cases:
- Failing function: `list nearby locations`
  - Failure condition: origin `{hospital_id}` is unknown.
  - Why it fails: service loads the origin hospital before geospatial search.
  - Violated prerequisite or constraint: origin hospital must exist.
- Failing function: `list nearby hospitals`
  - Failure condition: query `raio` is missing.
  - Why it fails: Spring request binding requires the query parameter.
  - Violated prerequisite or constraint: radius must be supplied.
- Failing function: `list nearby hospitals`
  - Failure condition: origin hospital has no stored location.
  - Why it fails: service dereferences hospital location coordinates.
  - Violated prerequisite or constraint: origin hospital must have location state.

Implementation notes:
The code contains internal `LocationService` CRUD methods, but no exposed REST endpoints for creating or managing arbitrary locations.

### Behavior 11: Admit A Patient

Business goal:
Check a patient into a hospital and consume one available bed.

Domain context:
Patient admission is the core bed-management workflow. A patient becomes active and is linked to the hospital’s patient list.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Hospital A"`, `address="Address A"`, `beds=10`, `availableBeds=3` to create a hospital with at least one available bed.
2. Use function `check in patient` (`POST /v1/hospitais/{hospital_id}/pacientes/checkin`) with `{hospital_id}={created HospitalDTO.id}` and body `name="Patient A"`, `cpf="123"`, `birthDate="2000-01-01T00:00:00.000+0000"`, `gender="F"` to admit the patient.

Optional verification workflow:
1. Use function `list hospital patients` (`GET /v1/hospitais/{hospital_id}/pacientes`) with `{hospital_id}={created HospitalDTO.id}` to verify the patient appears in the hospital.
2. Use function `retrieve patient` (`GET /v1/hospitais/{hospital_id}/pacientes/{patientId}`) with `{hospital_id}={created HospitalDTO.id}` and `{patientId}={returned Patient.id}` to inspect patient state.
3. Use function `get available beds` (`GET /v1/hospitais/{id}/leitos`) with `{id}={created HospitalDTO.id}` to verify one bed was consumed.

Existing-state shortcuts:
- Skip `create hospital` if a hospital with `availableBeds > 0` already exists.
- Direct database setup can create a hospital with positive available beds.

Parameter and value bindings:
- Hospital id from creation is reused as `{hospital_id}`.
- Returned `Patient.id` is reused as `{patientId}` for retrieve, update, and checkout.
- The check-in function sets `entryDate`, sets `active=true`, clears `exitDate`, appends the patient to the hospital list, and decrements `availableBeds`.

Business result:
A patient document exists, is active, has an entry date, has no exit date, is referenced by the hospital, and the hospital has one fewer available bed.

Constraints and invariants:
- Hospital must exist.
- Hospital `availableBeds` must be greater than zero.
- Patient fields are not meaningfully validated; duplicate CPF or missing demographic fields are not rejected.
- The implementation does not enforce total `beds` against patient count except through the mutable `availableBeds` value.

Failure and exceptional cases:
- Failing function: `check in patient`
  - Failure condition: `{hospital_id}` is unknown.
  - Why it fails: controller cannot load the hospital.
  - Violated prerequisite or constraint: hospital must exist.
- Failing function: `check in patient`
  - Failure condition: hospital has `availableBeds=0`.
  - Why it fails: `HospitalService.checkIn` calls `temVaga()` and throws when false.
  - Violated prerequisite or constraint: admission requires an available bed.

Implementation notes:
The controller maps check-in failures to `404 Not Found`, including full-hospital conditions, which is semantically misleading.

### Behavior 12: Discharge A Checked-In Patient

Business goal:
Check a patient out of a hospital and release one available bed.

Domain context:
Discharge completes a hospital stay. It removes the hospital-patient association and marks the patient inactive.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Hospital A"`, `address="Address A"`, `beds=10`, `availableBeds=3` to create the hospital.
2. Use function `check in patient` (`POST /v1/hospitais/{hospital_id}/pacientes/checkin`) with `{hospital_id}={created HospitalDTO.id}` and patient body `name="Patient A"`, `cpf="123"`, `birthDate="2000-01-01T00:00:00.000+0000"`, `gender="F"` to create and link the patient.
3. Use function `check out patient` (`POST /v1/hospitais/{hospital_id}/pacientes/checkout`) with `{hospital_id}={created HospitalDTO.id}` and raw string body `{patientId}={returned Patient.id}` to discharge the patient.

Optional verification workflow:
1. Use function `retrieve patient` (`GET /v1/hospitais/{hospital_id}/pacientes/{patientId}`) with `{hospital_id}={created HospitalDTO.id}` and `{patientId}={returned Patient.id}` to inspect inactive status.
2. Use function `list hospital patients` (`GET /v1/hospitais/{hospital_id}/pacientes`) with `{hospital_id}={created HospitalDTO.id}` to inspect hospital patient references.
3. Use function `get available beds` (`GET /v1/hospitais/{id}/leitos`) with `{id}={created HospitalDTO.id}` to verify the released bed.

Existing-state shortcuts:
- Skip `create hospital` and `check in patient` only if a hospital already exists with the patient currently linked in its patient list.
- Direct database setup can create a patient and DBRef relationship, but the patient id must be present in that hospital’s patient list.

Parameter and value bindings:
- Hospital id is reused in check-in and checkout.
- The returned patient id from check-in must be sent as the checkout request body, not as a JSON patient object.
- The checkout patient id must belong to the same hospital list.

Business result:
The patient is removed from the hospital’s patient list, marked inactive, receives an exit date, and the hospital’s available bed count is incremented.

Constraints and invariants:
- Checkout requires membership in the specified hospital, unlike retrieve/update patient.
- The checkout body is a raw string.
- The `Patient.getExitDate()` implementation incorrectly returns `entryDate`, so API serialization may expose the wrong exit date value even after `exitDate` is set.

Failure and exceptional cases:
- Failing function: `check in patient`
  - Failure condition: setup hospital has no available beds.
  - Why it fails: admission requires positive `availableBeds`.
  - Violated prerequisite or constraint: patient must be checked in before checkout.
- Failing function: `check out patient`
  - Failure condition: patient id is not present in the hospital’s patient list.
  - Why it fails: service streams hospital patients and throws if no id matches.
  - Violated prerequisite or constraint: patient must currently be checked into that hospital.
- Failing function: `check out patient`
  - Failure condition: hospital id is unknown.
  - Why it fails: hospital lookup fails before patient membership check.
  - Violated prerequisite or constraint: hospital must exist.

Implementation notes:
Checkout correctly persists both the hospital and patient, but patient serialization has an accessor bug for exit date.

### Behavior 13: Maintain Patient Demographics

Business goal:
Retrieve and update patient demographic data.

Domain context:
Patient records hold identity-like fields such as name, CPF, birth date, and gender. These can change independently of admission status.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Hospital A"`, `address="Address A"`, `beds=10`, `availableBeds=3` to create a hospital.
2. Use function `check in patient` (`POST /v1/hospitais/{hospital_id}/pacientes/checkin`) with `{hospital_id}={created HospitalDTO.id}` and patient body `name="Patient A"`, `cpf="123"`, `birthDate="2000-01-01T00:00:00.000+0000"`, `gender="F"` to create a patient.
3. Use function `update patient` (`PUT /v1/hospitais/{hospital_id}/pacientes/{patientId}`) with `{hospital_id}={created HospitalDTO.id}`, `{patientId}={returned Patient.id}`, and body `name="Patient A Updated"`, `cpf="456"`, `birthDate="2000-01-01T00:00:00.000+0000"`, `gender="F"` to update demographics.

Optional verification workflow:
1. Use function `retrieve patient` (`GET /v1/hospitais/{hospital_id}/pacientes/{patientId}`) with `{hospital_id}={created HospitalDTO.id}` and `{patientId}={returned Patient.id}` to verify updated demographic fields.
2. Use function `list hospital patients` (`GET /v1/hospitais/{hospital_id}/pacientes`) with `{hospital_id}={created HospitalDTO.id}` to inspect the hospital list.

Existing-state shortcuts:
- Skip hospital creation and check-in if a patient already exists and its id is known.
- Direct database setup can create the patient; despite route shape, the implementation does not require hospital membership for update.

Parameter and value bindings:
- Returned patient id from check-in is reused as `{patientId}`.
- `{hospital_id}` is accepted in the route but is ignored by `update patient`.
- Only `name`, `cpf`, `birthDate`, and `gender` are copied from the update body; active status and dates are preserved.

Business result:
The patient document has updated demographic fields. Hospital membership and admission state are not changed by this update.

Constraints and invariants:
- Patient id must exist.
- Hospital id is not validated.
- No uniqueness, CPF format, birth date, or gender validation is enforced.

Failure and exceptional cases:
- Failing function: `update patient`
  - Failure condition: `{patientId}` is unknown.
  - Why it fails: `PatientService.findById` throws before copying fields.
  - Violated prerequisite or constraint: patient must exist.
- Failing function: `check in patient`
  - Failure condition: setup hospital is full or unknown.
  - Why it fails: patient cannot be created through the API without a successful check-in.
  - Violated prerequisite or constraint: API-realizable patient creation depends on admission.

Implementation notes:
Patient retrieve and update are globally keyed by patient id even though the routes are hospital-scoped.

### Behavior 14: View Hospital Patients

Business goal:
List all patients associated with a hospital and retrieve individual patient records.

Domain context:
Operational users need to inspect current hospital patients and patient details.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create hospital` (`POST /v1/hospitais/`) with body `id=null`, `name="Hospital A"`, `address="Address A"`, `beds=10`, `availableBeds=3` to create a hospital.
2. Use function `check in patient` (`POST /v1/hospitais/{hospital_id}/pacientes/checkin`) with `{hospital_id}={created HospitalDTO.id}` and patient body `name="Patient A"`, `cpf="123"`, `birthDate="2000-01-01T00:00:00.000+0000"`, `gender="F"` to create a listed patient.
3. Use function `list hospital patients` (`GET /v1/hospitais/{hospital_id}/pacientes`) with `{hospital_id}={created HospitalDTO.id}` to list hospital patients.
4. Use function `retrieve patient` (`GET /v1/hospitais/{hospital_id}/pacientes/{patientId}`) with `{hospital_id}={created HospitalDTO.id}` and `{patientId}={returned Patient.id}` to retrieve one patient.

Optional verification workflow:
None.

Existing-state shortcuts:
- Skip setup if the hospital and patient relationship already exists.
- For `retrieve patient`, direct database setup of a patient alone is enough because implementation does not require hospital ownership.

Parameter and value bindings:
- Hospital id from creation is reused for check-in and patient listing.
- Patient id from check-in is reused as `{patientId}`.
- For listing, hospital-patient DBRef membership matters. For retrieval, only patient id matters.

Business result:
The caller sees the hospital’s patient list and can retrieve an individual patient record.

Constraints and invariants:
- `list hospital patients` requires the hospital to exist.
- `retrieve patient` does not validate hospital existence or membership.
- Empty non-null patient lists return successfully.

Failure and exceptional cases:
- Failing function: `list hospital patients`
  - Failure condition: hospital id is unknown.
  - Why it fails: controller loads hospital first.
  - Violated prerequisite or constraint: hospital must exist.
- Failing function: `retrieve patient`
  - Failure condition: patient id is unknown.
  - Why it fails: patient lookup throws.
  - Violated prerequisite or constraint: patient must exist.

Implementation notes:
The route implies hospital-scoped patient access, but retrieve is not actually scoped.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Reliable API-Only Product Stock Association

Priority:
Critical domain gap

Expected business goal:
A caller should be able to add a product to a hospital’s stock and later list, transfer, or delete it as that hospital’s product.

Why it is unsupported:
`add product to stock` saves the product and mutates the hospital object in memory, but does not save the hospital afterward.

Existing functions considered:
- `add product to stock`: creates the product but does not reliably persist the hospital-product relationship.
- `list hospital stock`: reads products from the hospital’s persisted DBRef list, so it may not show the newly added product.
- `transfer product`: requires a nearby source hospital’s persisted product list to contain the product.
- `delete stock product`: can delete a product with any existing hospital id and does not require real ownership.

Missing capability:
The service needs persisted hospital stock membership creation, either by saving the hospital after adding the product or by modeling ownership directly on the product.

Proof that function composition is insufficient:
Chaining `add product to stock` and `list hospital stock` cannot guarantee that the product appears in the hospital list because the relationship mutation is not saved. Delete-and-recreate is not equivalent because it still cannot persist the membership through the exposed endpoint.

Evidence from existing functions/source:
`ProductService.insert` calls `productRepository.save(product)`, loads the hospital, calls `hospital.setProduct(product)`, and returns without `hospitalRepository.save(hospital)`.

Business impact:
Stock lists and transfer workflows are unreliable unless seed data or direct database setup creates the relationship.

### Missing Behavior 2: Enforced Hospital Ownership For Products And Patients

Priority:
Critical domain gap

Expected business goal:
Hospital-scoped routes should only retrieve, update, or delete products and patients that belong to the specified hospital.

Why it is unsupported:
Several functions accept `{hospital_id}` but ignore it for ownership checks.

Existing functions considered:
- `retrieve stock product`: retrieves by product id only.
- `update stock product`: updates by product id only.
- `retrieve patient`: retrieves by patient id only.
- `update patient`: updates by patient id only.
- `delete stock product`: requires a hospital to exist but allows deletion of any product id.

Missing capability:
Ownership validation between hospital and child resource is missing.

Proof that function composition is insufficient:
No function can assert or repair ownership before these operations. Listing can show membership but cannot prevent a later cross-hospital update or retrieve. Delete-and-recreate is not equivalent because unauthorized access remains possible.

Evidence from existing functions/source:
`ProductResource.findProductBy` and `PatientResource.findPatientById` pass only product or patient id into services. `PatientResource.updatePatient` does not load the hospital.

Business impact:
Cross-hospital data access and mutation are possible, weakening tenant/ownership boundaries.

### Missing Behavior 3: Safe Hospital Deletion With Relationship Cleanup

Priority:
Critical domain gap

Expected business goal:
Deleting a hospital should either reject deletion when patients/products exist or cascade/clean up related data consistently.

Why it is unsupported:
`delete hospital` removes only the hospital document.

Existing functions considered:
- `delete hospital`: deletes the hospital by id.
- `list hospital patients`: cannot be used after deletion because hospital lookup fails.
- `list hospital stock`: cannot be used after deletion because hospital lookup fails.
- `retrieve patient` and `retrieve stock product`: can still retrieve orphaned records.

Missing capability:
Cascade delete, relationship cleanup, or guarded delete is missing.

Proof that function composition is insufficient:
Once the hospital is deleted, the API cannot enumerate its former patients/products via hospital-scoped list functions. Direct child retrieval requires known ids and cannot discover all orphaned children.

Evidence from existing functions/source:
`HospitalService.delete` calls `repo.deleteById(hospital_id)` only.

Business impact:
Hospital deletion can leave orphaned patients, products, and locations with no API path to reconcile them.

### Missing Behavior 4: Location Lifecycle Management Through REST

Priority:
Important robustness gap

Expected business goal:
Users should be able to create, update, retrieve, and delete standalone patient or facility locations.

Why it is unsupported:
`LocationService` has internal CRUD methods, but `LocationResource` exposes only proximity queries.

Existing functions considered:
- `list nearby locations`: reads nearby stored locations.
- `list nearby hospitals`: reads hospital locations.
- `create hospital`: creates only hospital locations from hospital addresses.

Missing capability:
REST endpoints for location create, retrieve, update, delete, and search by name/category are absent.

Proof that function composition is insufficient:
Creating hospitals can only create `HOSPITAL` locations. There is no API function to create `PATIENT` locations or arbitrary nearby points, so proximity data cannot be fully managed through the API.

Evidence from existing functions/source:
`LocationService.insert`, `update`, `delete`, and `findByNameLikeIgnoreCase` exist but are not mapped in `LocationResource`.

Business impact:
Location-based workflows depend on seed data or direct database manipulation for non-hospital locations.

### Missing Behavior 5: Recalculate Or Validate Bed Capacity From Patient State

Priority:
Important robustness gap

Expected business goal:
Available beds should remain consistent with total beds and active patient count.

Why it is unsupported:
The service mutates `availableBeds` during check-in/checkout but also allows direct update of `availableBeds`.

Existing functions considered:
- `update hospital`: can set any non-negative `availableBeds`.
- `check in patient`: decrements available beds.
- `check out patient`: increments available beds.
- `get available beds`: reads stored value only.

Missing capability:
Capacity reconciliation or validation rule such as `availableBeds = beds - activeAdmissions`.

Proof that function composition is insufficient:
A caller can update `availableBeds` to a value inconsistent with patient membership. No function recomputes it from active patients or rejects inconsistent updates.

Evidence from existing functions/source:
`HospitalService.updateData` directly assigns `availableBeds`; `get available beds` returns the stored value.

Business impact:
Capacity search and admission decisions can be based on stale or incorrect bed counts.

### Missing Behavior 6: Re-Geocode Hospital Location After Address Update

Priority:
Important robustness gap

Expected business goal:
Changing a hospital address should update the hospital’s stored location coordinates.

Why it is unsupported:
`update hospital` changes `address` but does not call location geocoding or update the location.

Existing functions considered:
- `update hospital`: updates address only.
- `find nearest hospital with beds`: uses stored location.
- `list nearby hospitals`: uses stored location.

Missing capability:
Address-to-location refresh on update.

Proof that function composition is insufficient:
There is no exposed location update endpoint, and repeating `update hospital` cannot update coordinates. Delete-and-recreate creates a new hospital id and is not equivalent because it loses identity and existing relationships.

Evidence from existing functions/source:
`HospitalService.updateData` updates name, address, beds, and availableBeds only.

Business impact:
Proximity search can return wrong results after address changes.

### Missing Behavior 7: Search Patients Or Products By Business Attributes

Priority:
API ergonomics gap

Expected business goal:
Users should search patients by CPF/name/status and products by name/type/quantity.

Why it is unsupported:
No REST search endpoints exist for patient or product attributes.

Existing functions considered:
- `retrieve patient`: requires patient id.
- `list hospital patients`: requires hospital id and returns only that hospital’s list.
- `retrieve stock product`: requires product id.
- `list hospital stock`: requires hospital id.
- `ProductService.findByName` exists internally but is not exposed.

Missing capability:
Search/list endpoints by CPF, patient name, active status, product name, product type, and quantity thresholds.

Proof that function composition is insufficient:
Without knowing ids or a hospital scope, the API cannot discover matching records. Listing every hospital and then every hospital’s patients/products is incomplete because orphaned or unlinked products/patients may exist.

Evidence from existing functions/source:
Repository and service have some product name search support, but no resource method exposes it.

Business impact:
Operational lookup depends on external id knowledge or direct database access.

### Missing Behavior 8: Transactional Admission And Transfer Consistency

Priority:
Important robustness gap

Expected business goal:
Multi-document workflows should either complete fully or leave no partial state.

Why it is unsupported:
Check-in, product add, and transfer perform multiple writes without transaction handling.

Existing functions considered:
- `check in patient`: saves patient and then saves hospital.
- `add product to stock`: saves product before hospital lookup and may leave orphan product on failure.
- `transfer product`: saves a new destination product and decrements source product, but does not save destination hospital membership.

Missing capability:
Transactional boundary or compensating rollback for multi-document operations.

Proof that function composition is insufficient:
A client cannot undo partial internal writes reliably because it may not receive all generated ids or persisted relationship state after failure.

Evidence from existing functions/source:
`ProductService.insert` saves product before hospital lookup; `HospitalService.transfereProduto` saves new product and source product separately.

Business impact:
Failures can leave orphaned products, inconsistent stock quantities, or incomplete transfers.

## Cross-Behavior Observations

- Validation is weak. Hospitals reject empty name/address and negative bed counts, but product quantity, patient identity fields, CPF uniqueness, gender values, and `availableBeds <= beds` are not enforced.
- Hospital ownership is incomplete. Patient and product detail/update routes are globally keyed by child id despite hospital-scoped paths.
- Location consistency is event-driven only at hospital creation. Address updates do not update geospatial state.
- Product stock membership is unreliable through the public API because product creation does not save the mutated hospital.
- Transfer depends on seeded or directly inserted hospital-product relationships.
- Hospital deletion does not cascade to related patients, products, or locations.
- Several OpenAPI responses advertise `201 Created`, but controllers return `200 OK`.
- `find nearest hospital with beds` has a coordinate naming/order discrepancy between query names and service parameters.
- Patient checkout sets `exitDate`, but `Patient.getExitDate()` returns `entryDate`, which can expose incorrect response data.

## Coverage Summary

Supported domain areas:
Hospital creation, listing, retrieval, update, deletion, bed count inspection, patient check-in/check-out, patient retrieval/update, stock product CRUD by id, proximity search, and transfer/refusal of products under specific persisted-state conditions.

Partially supported domain areas:
Hospital stock ownership, inter-hospital product transfer, proximity-based routing, hospital address/location consistency, and patient/product child-resource scoping.

Unsupported domain areas:
Reliable API-only stock association, enforced child ownership, safe cascading hospital deletion, standalone location management, capacity reconciliation, hospital location refresh after address changes, business-attribute search, and transactional consistency for multi-document workflows.