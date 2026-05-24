# Domain-Level Behavior Analysis

## Domain Summary

The service manages configurable software products. A `Product` has a name, a catalog of `Feature` records, product-level feature constraints, and named `ProductConfiguration` records. A configuration represents a customer/product variant with a set of active features and a persisted `valid` flag.

The core business concepts are:

- Product catalog: named products.
- Feature catalog: named features scoped to one product, with optional descriptions.
- Product configuration: named selections of active product features.
- Requires constraint: if one feature is active, another feature should also become active.
- Excludes constraint: if one feature is active, another feature must not be active.
- Configuration evaluation: performed only when active features are added to or removed from a configuration.

Implementation behavior is more permissive than the domain model suggests: product names and configuration names are not visibly unique, constraints can reference missing or null feature names, and some invalid operations persist state before returning an error.

## Available Function Inventory

### Product Catalog

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list products` | `GET /products` | Lists all stored product names. |
| `create product` | `POST /products/{productName}` | Creates a product with the path name. |
| `retrieve product by name` | `GET /products/{productName}` | Retrieves one product, including features and constraints. |
| `delete product` | `DELETE /products/{productName}` | Deletes a product and its dependent configurations, constraints, and features. |

### Product Feature Catalog

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list product features` | `GET /products/{productName}/features` | Lists the feature catalog for one product. |
| `add feature to product` | `POST /products/{productName}/features/{featureName}` | Adds a named feature to a product, with optional description. |
| `update product feature description` | `PUT /products/{productName}/features/{featureName}` | Changes a feature description. |
| `delete product feature` | `DELETE /products/{productName}/features/{featureName}` | Removes a feature from the product and from configurations where it is active. |

### Product Configurations

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list product configurations` | `GET /products/{productName}/configurations` | Lists names of configurations scoped to a product. |
| `create product configuration` | `POST /products/{productName}/configurations/{configurationName}` | Creates a named configuration for a product. |
| `retrieve product configuration` | `GET /products/{productName}/configurations/{configurationName}` | Retrieves one configuration, including validity and active features. |
| `delete product configuration` | `DELETE /products/{productName}/configurations/{configurationName}` | Deletes one named configuration. |

### Active Configuration Features

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `list active configuration features` | `GET /products/{productName}/configurations/{configurationName}/features` | Lists active feature names in one configuration. |
| `activate feature in configuration` | `POST /products/{productName}/configurations/{configurationName}/features/{featureName}` | Activates one product feature in one configuration and evaluates constraints. |
| `activate feature and derive required feature` | `POST /products/{productName}/configurations/{configurationName}/features/{featureName}` | Activates a source feature and automatically activates its required feature. |
| `deactivate feature in configuration` | `DELETE /products/{productName}/configurations/{configurationName}/features/{featureName}` | Removes one active feature and reevaluates constraints. |
| `attempt to deactivate required feature` | `DELETE /products/{productName}/configurations/{configurationName}/features/{featureName}` | Accepts removal of a required feature, but evaluation reactivates it when the source feature remains active. |

### Product Constraints

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `add requires constraint to product` | `POST /products/{productName}/constraints/requires` | Stores a rule that one feature requires another. |
| `add excludes constraint to product` | `POST /products/{productName}/constraints/excludes` | Stores a rule that one feature excludes another. |
| `delete product constraint` | `DELETE /products/{productName}/constraints/{constraintId}` | Deletes a stored constraint by generated id. |

## Supported Business Behaviors

### Behavior 1: List Products

Business goal:
Return the available product names in the catalog.

Domain context:
This is the entry point for discovering configurable products.

Starting point:
Any service state, including an empty database.

Required execution workflow:
1. Use function `list products` (`GET /products`) with no path, query, body, form, or header values to return the stored product names.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. The behavior itself is a read operation.
- Direct database setup can insert `Product` rows before calling `list products` if a non-empty list is needed.

Parameter and value bindings:
- No request values are reused.
- Returned product names can be reused as `productName` path values in product-scoped functions.

Business result:
No state changes. The response is a list of product names.

Constraints and invariants:
- No product must pre-exist.
- The implementation returns names only, not product ids or full product records.
- Duplicate product names are not visibly prevented, so the list may contain duplicate names if duplicate rows exist.

Failure and exceptional cases:
- Failing function: `list products`
  - Failure condition: None visible in the OpenAPI contract or implementation.
  - Why it fails: No domain-backed failure branch is implemented.
  - Violated prerequisite or constraint: None.

Implementation notes:
The implementation reads all `Product` entities and maps them to `name`.

### Behavior 2: Create Product

Business goal:
Add a new configurable product to the catalog.

Domain context:
Products are the parent scope for features, constraints, and configurations.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.

Optional verification workflow:
1. Use function `retrieve product by name` (`GET /products/{productName}`) with `productName={productName}` to inspect the created product.
2. Use function `list products` (`GET /products`) to verify that `{productName}` appears in the catalog.

Existing-state shortcuts:
- None for the core create action.
- Direct database setup can insert a `Product` row with `name={productName}`, but that replaces the API create behavior rather than shortening it.

Parameter and value bindings:
- The path value `productName={productName}` becomes the product name persisted in the database.
- The same `{productName}` must be reused for later feature, configuration, and constraint functions.

Business result:
A `Product` row exists with `name={productName}`. It has no features, no constraints, and no configurations created by this function.

Constraints and invariants:
- `productName` is required by the path.
- No request body is used.
- The implementation does not visibly enforce product-name uniqueness.

Failure and exceptional cases:
- Failing function: `create product`
  - Failure condition: No domain-level failure is visible for duplicate names or missing body.
  - Why it fails: The implementation simply persists a new `Product` using the path value.
  - Violated prerequisite or constraint: None enforced, apart from the path value existing.

Implementation notes:
Creating the same `productName` more than once can create ambiguous product identity because later lookup uses `findByName`.

### Behavior 3: Retrieve Product Definition

Business goal:
Read a product’s current feature catalog and constraints.

Domain context:
A product definition is the business model that configurations are built from.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `retrieve product by name` (`GET /products/{productName}`) with `productName={productName}` to retrieve the product.

Optional verification workflow:
None.

Existing-state shortcuts:
- Skip `create product` if a product row with `name={productName}` already exists.
- Direct database setup can insert the product, features, and constraints before retrieval.
- The same `productName` must identify exactly the intended product; duplicate product names make retrieval ambiguous.

Parameter and value bindings:
- The `productName` used in `create product` must be reused in `retrieve product by name`.
- Feature and constraint state linked to that same product is returned as part of the product representation.

Business result:
No state changes from the retrieval step. The response is the product record with its features and constraints.

Constraints and invariants:
- The product must exist.
- Features and constraints are product-scoped.
- OpenAPI shows `FeatureConstraint` as only `id` and `type`, while implementation subclasses also have source/target feature getters.

Failure and exceptional cases:
- Failing function: `retrieve product by name`
  - Failure condition: No product exists with `name={productName}`.
  - Why it fails: `ProductsDAO.findByName` throws `ObjectNotFoundException`.
  - Violated prerequisite or constraint: Product existence.
- Failing function: `create product`
  - Failure condition: No meaningful setup failure is visible.
  - Why it fails: Creation has no implemented domain validation.
  - Violated prerequisite or constraint: None enforced.

Implementation notes:
The retrieval endpoint is the only broad read surface for product constraints; there is no dedicated constraint list endpoint.

### Behavior 4: Retire Product And Dependent State

Business goal:
Remove a product and its dependent feature model, constraints, and configurations.

Domain context:
Retiring a product should remove the entire configurable product line from the service.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{featureName}`) with `productName={productName}`, `featureName={featureName}`, and optional form `description={description}` to create dependent feature state.
3. Use function `create product configuration` (`POST /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to create dependent configuration state.
4. Use function `add requires constraint to product` (`POST /products/{productName}/constraints/requires`) with `productName={productName}`, form `sourceFeature={featureName}`, and form `requiredFeature={featureName}` to create dependent constraint state.
5. Use function `delete product` (`DELETE /products/{productName}`) with `productName={productName}` to delete the product and dependent state.

Optional verification workflow:
1. Use function `list products` (`GET /products`) to verify that `{productName}` is no longer listed.
2. Use function `retrieve product by name` (`GET /products/{productName}`) with `productName={productName}` to verify absence by expecting failure.

Existing-state shortcuts:
- Skip setup steps for features, configurations, or constraints if equivalent dependent state already exists and is linked to the same product.
- If only product deletion is needed, only an existing `Product` with `name={productName}` is required.
- Direct database setup can create the product, feature, configuration, and constraint rows, but ownership links must point to the same product row.

Parameter and value bindings:
- `productName={productName}` scopes all setup and the final delete.
- The `featureName` created in step 2 is reused as source and required feature text in step 4.
- The configuration is linked by product ownership, not by passing a product id.

Business result:
The product no longer exists. Product configurations and constraints are removed explicitly before product deletion; product features are removed through the product relationship.

Constraints and invariants:
- The product must exist at deletion time.
- The implementation intends product-scoped cascading cleanup.
- Product deletion calls configuration deletion, constraint deletion, then product removal.

Failure and exceptional cases:
- Failing function: `delete product`
  - Failure condition: No product exists with `name={productName}`.
  - Why it fails: Deletion resolves the product by name and `ProductsDAO.findByName` throws `ObjectNotFoundException`.
  - Violated prerequisite or constraint: Product existence.
- Failing function: `add feature to product`
  - Failure condition: The feature name already exists on the same product, case-insensitively.
  - Why it fails: `product.hasFeatureNamed(featureName)` throws `DuplicatedObjectException`.
  - Violated prerequisite or constraint: Feature-name uniqueness within a product.
- Failing function: `create product configuration`
  - Failure condition: Product setup was skipped or used a different `productName`.
  - Why it fails: Configuration creation resolves the product first.
  - Violated prerequisite or constraint: Configuration must be owned by an existing product.
- Failing function: `add requires constraint to product`
  - Failure condition: Product setup was skipped or used a different `productName`.
  - Why it fails: Constraint creation resolves the product first.
  - Violated prerequisite or constraint: Constraint must be owned by an existing product.

Implementation notes:
The delete workflow is not a single explicit aggregate transaction at the resource layer, but the DAO/service methods perform the cleanup in sequence.

### Behavior 5: List Product Features

Business goal:
Inspect the feature catalog for one product.

Domain context:
Product features define what can be selected in product configurations.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `list product features` (`GET /products/{productName}/features`) with `productName={productName}` to list its features.

Optional verification workflow:
None.

Existing-state shortcuts:
- Skip `create product` if a product with `name={productName}` already exists.
- Direct database setup can insert the product and optional linked feature rows.
- A product with no features is valid and returns an empty feature set.

Parameter and value bindings:
- The `productName` used to create the product must be reused in the feature-list path.
- Returned feature names can be reused as `featureName` in update, delete, and configuration activation functions.

Business result:
No state changes from the list step. The response contains the product’s features.

Constraints and invariants:
- The product must exist.
- Features are scoped to one product.
- Feature names are compared case-insensitively for lookup in model methods.

Failure and exceptional cases:
- Failing function: `list product features`
  - Failure condition: No product exists with `name={productName}`.
  - Why it fails: The service resolves the product before reading features and `ProductsDAO.findByName` throws `ObjectNotFoundException`.
  - Violated prerequisite or constraint: Product existence.
- Failing function: `create product`
  - Failure condition: No meaningful setup failure is visible.
  - Why it fails: Creation has no implemented domain validation.
  - Violated prerequisite or constraint: None enforced.

Implementation notes:
The OpenAPI response is a set-like array of `Feature` objects with `id`, `name`, and `description`.

### Behavior 6: Add Feature To Product

Business goal:
Add a selectable feature to a product’s feature catalog.

Domain context:
Only product features can be activated in configurations for that product.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{featureName}`) with `productName={productName}`, `featureName={featureName}`, and optional form `description={description}` to create the feature.

Optional verification workflow:
1. Use function `list product features` (`GET /products/{productName}/features`) with `productName={productName}` to verify the feature appears.
2. Use function `retrieve product by name` (`GET /products/{productName}`) with `productName={productName}` to inspect the product model.

Existing-state shortcuts:
- Skip `create product` if the product already exists.
- Direct database setup can insert a linked `Feature` row, but the feature’s `product_id` must point to the same product.
- The core `add feature to product` step cannot be skipped for this behavior.

Parameter and value bindings:
- `productName={productName}` scopes the feature.
- `featureName={featureName}` becomes the feature name later consumed by update, delete, constraint, and activation functions.
- Form `description={description}` becomes the stored feature description; omitted description stores `null`.

Business result:
A feature exists on the product with the submitted name and optional description.

Constraints and invariants:
- The product must exist.
- The same product cannot already have a feature with the same name, compared case-insensitively by the service.
- No global feature-name uniqueness exists across products.

Failure and exceptional cases:
- Failing function: `add feature to product`
  - Failure condition: No product exists with `name={productName}`.
  - Why it fails: The function resolves the product before inserting the feature.
  - Violated prerequisite or constraint: Product existence.
- Failing function: `add feature to product`
  - Failure condition: A feature with `featureName={featureName}` already exists on the same product.
  - Why it fails: `ProductsService.addFeatureToProduct` checks `product.hasFeatureNamed(featureName)` and throws `DuplicatedObjectException`.
  - Violated prerequisite or constraint: Product-scoped feature-name uniqueness.
- Failing function: `create product`
  - Failure condition: No meaningful setup failure is visible.
  - Why it fails: Creation has no implemented domain validation.
  - Violated prerequisite or constraint: None enforced.

Implementation notes:
The endpoint returns `201 Created` with a feature `Location`, but no response body is required by the implementation.

### Behavior 7: Update Feature Description

Business goal:
Change the descriptive metadata for a product feature.

Domain context:
Feature descriptions let product managers document selectable capabilities without changing feature identity.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{featureName}`) with `productName={productName}`, `featureName={featureName}`, and form `description={initialDescription}` to create the feature.
3. Use function `update product feature description` (`PUT /products/{productName}/features/{featureName}`) with `productName={productName}`, `featureName={featureName}`, and form `description={newDescription}` to update the description.

Optional verification workflow:
1. Use function `list product features` (`GET /products/{productName}/features`) with `productName={productName}` to inspect the updated feature.
2. Use function `retrieve product by name` (`GET /products/{productName}`) with `productName={productName}` to inspect the product model.

Existing-state shortcuts:
- Skip `create product` and `add feature to product` if the same product and feature already exist.
- Direct database setup can insert the product and linked feature.
- The core update step cannot be skipped.

Parameter and value bindings:
- The same `productName` and `featureName` must be reused across create, add, and update.
- `description={initialDescription}` is replaced by `description={newDescription}`.
- If the update omits `description`, the implementation sets the stored description to `null`.

Business result:
The feature still exists under the same product and name, but its description value changes.

Constraints and invariants:
- Product and feature must both exist.
- The function cannot rename a feature.
- The update does not reevaluate configurations or constraints.

Failure and exceptional cases:
- Failing function: `update product feature description`
  - Failure condition: The product does not exist.
  - Why it fails: The implementation resolves the product first with `ProductsDAO.findByName`.
  - Violated prerequisite or constraint: Product existence.
- Failing function: `update product feature description`
  - Failure condition: The product exists but no linked feature has `featureName={featureName}`.
  - Why it fails: `Product.findProductFeatureByName` throws `ObjectNotFoundException`.
  - Violated prerequisite or constraint: Feature must belong to the product.
- Failing function: `add feature to product`
  - Failure condition: The setup feature already exists.
  - Why it fails: Duplicate feature detection throws `DuplicatedObjectException`.
  - Violated prerequisite or constraint: Product-scoped feature-name uniqueness.

Implementation notes:
Only the description is mutable through the API.

### Behavior 8: Remove Feature From Product And Configurations

Business goal:
Retire a feature from a product and remove it from configurations where it is active.

Domain context:
A removed feature should no longer be selectable and should not remain active in product variants.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{featureName}`) with `productName={productName}`, `featureName={featureName}`, and optional form `description={description}` to create the feature.
3. Use function `create product configuration` (`POST /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to create a configuration.
4. Use function `activate feature in configuration` (`POST /products/{productName}/configurations/{configurationName}/features/{featureName}`) with `productName={productName}`, `configurationName={configurationName}`, and `featureName={featureName}` to make the feature active.
5. Use function `delete product feature` (`DELETE /products/{productName}/features/{featureName}`) with `productName={productName}` and `featureName={featureName}` to delete the feature.

Optional verification workflow:
1. Use function `list product features` (`GET /products/{productName}/features`) with `productName={productName}` to verify the feature is gone.
2. Use function `list active configuration features` (`GET /products/{productName}/configurations/{configurationName}/features`) with `productName={productName}` and `configurationName={configurationName}` to verify it is no longer active.

Existing-state shortcuts:
- Skip setup steps when the product, feature, configuration, and active-feature relation already exist.
- Direct database setup can insert the product, feature, configuration, and many-to-many active-feature relation.
- The `featureName` must identify the same feature linked to the same product and active in the configuration if active cleanup is being verified.

Parameter and value bindings:
- `productName={productName}` scopes all steps.
- `featureName={featureName}` created in step 2 is consumed by activation and deletion.
- `configurationName={configurationName}` created in step 3 is consumed by activation and active-feature verification.

Business result:
The feature is deleted from the product catalog. If it was active in any configuration, the implementation removes it from those configurations before deleting it.

Constraints and invariants:
- Product and feature must exist.
- Active-feature cleanup happens for all configurations containing the feature, not only the configuration named in the workflow.
- The implementation does not explicitly reevaluate configuration validity after feature deletion.

Failure and exceptional cases:
- Failing function: `delete product feature`
  - Failure condition: No product exists with `name={productName}`.
  - Why it fails: The implementation resolves the product before finding the feature.
  - Violated prerequisite or constraint: Product existence.
- Failing function: `delete product feature`
  - Failure condition: The product exists but no linked feature has `featureName={featureName}`.
  - Why it fails: `Product.findProductFeatureByName` throws `ObjectNotFoundException`.
  - Violated prerequisite or constraint: Feature must belong to the product.
- Failing function: `activate feature in configuration`
  - Failure condition: The feature or configuration setup is missing.
  - Why it fails: Activation resolves the configuration and then the feature through the owning product.
  - Violated prerequisite or constraint: Feature and configuration must belong to the same product.

Implementation notes:
Deleting a feature is stronger than merely deactivating it; it removes the catalog entry and cleans active references.

### Behavior 9: List Product Configurations

Business goal:
See which named configurations exist for a product.

Domain context:
Configurations represent customer variants or product deployments.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `list product configurations` (`GET /products/{productName}/configurations`) with `productName={productName}` to list configuration names.

Optional verification workflow:
None.

Existing-state shortcuts:
- Skip `create product` if the product already exists.
- Direct database setup can insert the product and optional linked `ProductConfiguration` rows.
- A product with no configurations returns an empty list.

Parameter and value bindings:
- The `productName` used in product creation is reused in the list path.
- Returned configuration names can be reused as `configurationName` in retrieve, delete, activation, and deactivation functions.

Business result:
No state change. The response contains configuration names for the product.

Constraints and invariants:
- Domain-wise, the product should exist.
- Implementation-wise, the DAO query returns an empty list when the product is absent instead of failing.

Failure and exceptional cases:
- Failing function: `list product configurations`
  - Failure condition: No product exists with `name={productName}`.
  - Why it fails: It does not fail in the visible implementation; the join query simply returns no rows.
  - Violated prerequisite or constraint: Product-scoped listing should require product existence, but this is not enforced.
- Failing function: `create product`
  - Failure condition: No meaningful setup failure is visible.
  - Why it fails: Creation has no implemented domain validation.
  - Violated prerequisite or constraint: None enforced.

Implementation notes:
This endpoint cannot distinguish an existing product with zero configurations from an absent product.

### Behavior 10: Create Product Configuration

Business goal:
Create a named configuration for a product.

Domain context:
Configurations hold active feature selections for a product variant.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the owning product.
2. Use function `create product configuration` (`POST /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to create the configuration.

Optional verification workflow:
1. Use function `retrieve product configuration` (`GET /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to inspect the created configuration.
2. Use function `list product configurations` (`GET /products/{productName}/configurations`) with `productName={productName}` to verify the name appears.

Existing-state shortcuts:
- Skip `create product` if the product already exists.
- Direct database setup can insert a `ProductConfiguration` row linked to the product.
- The core create-configuration step cannot be skipped.

Parameter and value bindings:
- `productName={productName}` identifies the owning product.
- `configurationName={configurationName}` becomes the configuration name used by later configuration and active-feature functions.

Business result:
A configuration exists for the product, initially valid and with no active features.

Constraints and invariants:
- Product must exist.
- The visible implementation does not reject duplicate configuration names for the same product.
- The default `valid` value is `true`.

Failure and exceptional cases:
- Failing function: `create product configuration`
  - Failure condition: No product exists with `name={productName}`.
  - Why it fails: Configuration creation resolves the product through `ProductsService.findByName`.
  - Violated prerequisite or constraint: Configuration must be owned by an existing product.
- Failing function: `create product`
  - Failure condition: No meaningful setup failure is visible.
  - Why it fails: Creation has no implemented domain validation.
  - Violated prerequisite or constraint: None enforced.

Implementation notes:
The endpoint returns `201 Created` with a configuration `Location`.

### Behavior 11: Retrieve Product Configuration

Business goal:
Inspect one configuration’s active features and validity state.

Domain context:
Users need to see the selected features and whether the variant is currently valid.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `create product configuration` (`POST /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to create the configuration.
3. Use function `retrieve product configuration` (`GET /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to retrieve it.

Optional verification workflow:
None.

Existing-state shortcuts:
- Skip the setup functions if a configuration with `name={configurationName}` already exists and is linked to product `name={productName}`.
- Direct database setup can insert the product and configuration rows.
- Duplicate configuration names for a product can make retrieval ambiguous.

Parameter and value bindings:
- `productName` and `configurationName` must match the product-configuration ownership link.
- The returned `activedFeatures` values are feature objects currently active in that configuration.
- The returned `valid` flag is the persisted configuration validity.

Business result:
No state change. The response is the named configuration.

Constraints and invariants:
- Domain-wise, both product and configuration must exist.
- The implementation does not explicitly throw `ObjectNotFoundException` for a missing configuration; it returns the DAO result.

Failure and exceptional cases:
- Failing function: `retrieve product configuration`
  - Failure condition: No configuration exists with `configurationName={configurationName}` for `productName={productName}`.
  - Why it fails: The DAO returns no configuration, and the resource has no explicit not-found mapping.
  - Violated prerequisite or constraint: Configuration must exist under the product.
- Failing function: `create product configuration`
  - Failure condition: The product does not exist.
  - Why it fails: Setup resolves the product first.
  - Violated prerequisite or constraint: Product existence.

Implementation notes:
Missing configuration behavior is less clean than missing product behavior; the source does not map it to a clear domain error.

### Behavior 12: Delete Product Configuration

Business goal:
Remove a named product configuration.

Domain context:
Configurations may represent customer variants that need to be retired independently from the product model.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `create product configuration` (`POST /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to create the configuration.
3. Use function `delete product configuration` (`DELETE /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to delete it.

Optional verification workflow:
1. Use function `list product configurations` (`GET /products/{productName}/configurations`) with `productName={productName}` to verify the configuration name is absent.

Existing-state shortcuts:
- Skip setup if the target configuration already exists under the product.
- Direct database setup can insert the product and configuration.
- The core delete step cannot be skipped.

Parameter and value bindings:
- `productName` and `configurationName` must identify the same linked configuration across create and delete.

Business result:
The named configuration no longer exists for the product.

Constraints and invariants:
- The target configuration must exist.
- Configuration deletion does not delete the product, features, or constraints.

Failure and exceptional cases:
- Failing function: `delete product configuration`
  - Failure condition: No matching configuration exists under the product.
  - Why it fails: `findByNameAndProductName` returns no entity and removal is attempted on the missing entity.
  - Violated prerequisite or constraint: Configuration existence.
- Failing function: `create product configuration`
  - Failure condition: Product setup was omitted.
  - Why it fails: The product is resolved before configuration creation.
  - Violated prerequisite or constraint: Product existence.

Implementation notes:
There is no explicit not-found exception for missing configurations.

### Behavior 13: List Active Configuration Features

Business goal:
Inspect the feature selections currently active in a configuration.

Domain context:
This is the focused read model for a configured product variant.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{featureName}`) with `productName={productName}`, `featureName={featureName}`, and optional form `description={description}` to create a selectable feature.
3. Use function `create product configuration` (`POST /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to create the configuration.
4. Use function `activate feature in configuration` (`POST /products/{productName}/configurations/{configurationName}/features/{featureName}`) with `productName={productName}`, `configurationName={configurationName}`, and `featureName={featureName}` to create an active selection.
5. Use function `list active configuration features` (`GET /products/{productName}/configurations/{configurationName}/features`) with `productName={productName}` and `configurationName={configurationName}` to list active feature names.

Optional verification workflow:
1. Use function `retrieve product configuration` (`GET /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to inspect active feature objects and validity.

Existing-state shortcuts:
- Skip setup steps if the product, feature, configuration, and active-feature relation already exist.
- Direct database setup can create the same rows and many-to-many active-feature relation.
- If an empty active list is acceptable, the activation setup step can be skipped.

Parameter and value bindings:
- `productName` scopes the product, feature, and configuration.
- `featureName` created as a product feature is activated in the configuration and appears in the active-feature list.
- `configurationName` scopes the active-feature list.

Business result:
No state change from the list step. The response contains active feature names.

Constraints and invariants:
- Product and configuration must exist.
- Active feature names must belong to the configuration’s product.

Failure and exceptional cases:
- Failing function: `list active configuration features`
  - Failure condition: No matching configuration exists.
  - Why it fails: The DAO returns no configuration and the implementation dereferences it.
  - Violated prerequisite or constraint: Configuration existence.
- Failing function: `activate feature in configuration`
  - Failure condition: The feature does not belong to the product or the configuration is missing.
  - Why it fails: Activation resolves both through product-scoped state.
  - Violated prerequisite or constraint: Product, feature, and configuration must share the same product scope.

Implementation notes:
The function returns names, while configuration retrieval returns feature objects.

### Behavior 14: Activate Feature In Configuration

Business goal:
Select a product feature for a specific product configuration.

Domain context:
This is the core action for building a configured product variant.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{featureName}`) with `productName={productName}`, `featureName={featureName}`, and optional form `description={description}` to create the feature.
3. Use function `create product configuration` (`POST /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to create the configuration.
4. Use function `activate feature in configuration` (`POST /products/{productName}/configurations/{configurationName}/features/{featureName}`) with `productName={productName}`, `configurationName={configurationName}`, and `featureName={featureName}` to activate the feature.

Optional verification workflow:
1. Use function `list active configuration features` (`GET /products/{productName}/configurations/{configurationName}/features`) with `productName={productName}` and `configurationName={configurationName}` to verify activation.
2. Use function `retrieve product configuration` (`GET /products/{productName}/configurations/{configurationName}`) with the same values to inspect `valid`.

Existing-state shortcuts:
- Skip product, feature, or configuration setup if equivalent state already exists.
- Direct database setup can create the feature and configuration, but the feature must be linked to the same product as the configuration.
- The feature must not already be active unless testing duplicate failure.

Parameter and value bindings:
- The same `productName` must scope the feature and configuration.
- `featureName` created in the feature catalog is consumed as the activation path value.
- `configurationName` created in the configuration path is consumed by the activation path.

Business result:
The feature becomes active in the configuration. The configuration is evaluated against product constraints and its `valid` flag is updated.

Constraints and invariants:
- The feature must belong to the configuration’s product.
- The feature must not already be active.
- Constraint evaluation happens after activation.
- If excludes constraints are violated, the implementation can persist the invalid active-feature state before the resource throws an error.

Failure and exceptional cases:
- Failing function: `activate feature in configuration`
  - Failure condition: Feature is already active.
  - Why it fails: `configuration.hasActiveFeature(featureName)` triggers `DuplicatedObjectException`.
  - Violated prerequisite or constraint: Active-feature uniqueness in one configuration.
- Failing function: `activate feature in configuration`
  - Failure condition: `featureName` is not a feature of the product.
  - Why it fails: `Product.findProductFeatureByName` throws `ObjectNotFoundException`.
  - Violated prerequisite or constraint: Product-scoped feature ownership.
- Failing function: `activate feature in configuration`
  - Failure condition: No matching configuration exists.
  - Why it fails: The DAO returns no configuration and the implementation tries to use it.
  - Violated prerequisite or constraint: Configuration existence.
- Failing function: `activate feature in configuration`
  - Failure condition: Activation creates an excludes violation.
  - Why it fails: Evaluation marks the configuration invalid and the resource throws `WrongProductConfigurationException`.
  - Violated prerequisite or constraint: Excluded features cannot both be active under the same product constraints.
- Failing function: `add feature to product`
  - Failure condition: Setup feature already exists.
  - Why it fails: Duplicate feature detection throws `DuplicatedObjectException`.
  - Violated prerequisite or constraint: Product-scoped feature uniqueness.

Implementation notes:
Evaluation is event-driven: it runs on activation and deactivation, not continuously whenever product constraints change.

### Behavior 15: Deactivate Feature In Configuration

Business goal:
Remove a selected feature from a configuration.

Domain context:
Users need to adjust configured variants by turning selected features off.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{featureName}`) with `productName={productName}`, `featureName={featureName}`, and optional form `description={description}` to create the feature.
3. Use function `create product configuration` (`POST /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to create the configuration.
4. Use function `activate feature in configuration` (`POST /products/{productName}/configurations/{configurationName}/features/{featureName}`) with `productName={productName}`, `configurationName={configurationName}`, and `featureName={featureName}` to make the feature active.
5. Use function `deactivate feature in configuration` (`DELETE /products/{productName}/configurations/{configurationName}/features/{featureName}`) with `productName={productName}`, `configurationName={configurationName}`, and `featureName={featureName}` to deactivate it.

Optional verification workflow:
1. Use function `list active configuration features` (`GET /products/{productName}/configurations/{configurationName}/features`) with `productName={productName}` and `configurationName={configurationName}` to verify the feature is absent.
2. Use function `retrieve product configuration` (`GET /products/{productName}/configurations/{configurationName}`) with the same values to inspect validity.

Existing-state shortcuts:
- Skip setup if the product, feature, configuration, and active-feature relation already exist.
- Direct database setup can insert the required rows and active-feature relation.
- If the feature exists but is already inactive, the endpoint can still return success, but the business deactivation has no effect.

Parameter and value bindings:
- The same `productName`, `configurationName`, and `featureName` bind activation and deactivation to the same active-feature relation.
- The feature must be a product feature of the same product that owns the configuration.

Business result:
The feature is removed from the configuration’s active feature set. The configuration is reevaluated and its `valid` flag is updated.

Constraints and invariants:
- Product feature and configuration must exist.
- The implementation removes by feature object, not by raw string only.
- If a remaining active feature requires the removed feature, evaluation can add it back; that is covered by `attempt to deactivate required feature`.

Failure and exceptional cases:
- Failing function: `deactivate feature in configuration`
  - Failure condition: The feature does not exist on the product.
  - Why it fails: Deactivation resolves the feature through the product and throws `ObjectNotFoundException`.
  - Violated prerequisite or constraint: Feature must belong to the product.
- Failing function: `deactivate feature in configuration`
  - Failure condition: The configuration does not exist.
  - Why it fails: The DAO returns no configuration and the implementation attempts to use it.
  - Violated prerequisite or constraint: Configuration existence.
- Failing function: `activate feature in configuration`
  - Failure condition: Setup activation fails because the feature is missing, configuration is missing, already active, or constraints invalidate the configuration.
  - Why it fails: Activation performs ownership, duplication, and constraint checks.
  - Violated prerequisite or constraint: Valid active-feature setup.

Implementation notes:
Deactivation reevaluates constraints but does not distinguish “was active and removed” from “was already inactive” in the success response.

### Behavior 16: Add Requires Constraint

Business goal:
Define a rule that selecting one feature automatically requires another feature.

Domain context:
Requires constraints encode product model dependencies, such as a payment feature requiring a selling feature.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{sourceFeature}`) with `productName={productName}`, `featureName={sourceFeature}`, and optional form `description={sourceDescription}` to create the source feature.
3. Use function `add feature to product` (`POST /products/{productName}/features/{requiredFeature}`) with `productName={productName}`, `featureName={requiredFeature}`, and optional form `description={requiredDescription}` to create the required feature.
4. Use function `add requires constraint to product` (`POST /products/{productName}/constraints/requires`) with `productName={productName}`, form `sourceFeature={sourceFeature}`, and form `requiredFeature={requiredFeature}` to create the rule.

Optional verification workflow:
1. Use function `retrieve product by name` (`GET /products/{productName}`) with `productName={productName}` to inspect product constraints.
2. Use function `activate feature and derive required feature` (`POST /products/{productName}/configurations/{configurationName}/features/{sourceFeature}`) after creating a configuration to verify derivation.

Existing-state shortcuts:
- Skip product or feature setup if the same product and features already exist.
- Direct database setup can insert a `ConstraintRequires` row linked to the product.
- Although the domain rule expects both features to exist, the implementation allows constraint creation even if they do not.

Parameter and value bindings:
- `sourceFeature={sourceFeature}` and `requiredFeature={requiredFeature}` are stored as strings in the constraint.
- Those strings must match product feature names later used during configuration evaluation.
- The generated constraint id is returned through the creation response `Location` and can be reused by `delete product constraint`.

Business result:
A requires constraint exists for the product.

Constraints and invariants:
- The product must exist.
- Source and required feature names are not validated at creation time.
- The rule affects configurations only when active-feature add/remove evaluation runs.

Failure and exceptional cases:
- Failing function: `add requires constraint to product`
  - Failure condition: No product exists with `name={productName}`.
  - Why it fails: The service resolves the product before persisting the constraint.
  - Violated prerequisite or constraint: Constraint must be product-owned.
- Failing function: `add feature to product`
  - Failure condition: Source or required feature setup duplicates an existing feature name.
  - Why it fails: Duplicate feature detection throws `DuplicatedObjectException`.
  - Violated prerequisite or constraint: Product-scoped feature uniqueness.

Implementation notes:
OpenAPI marks `sourceFeature` and `requiredFeature` as optional form fields, and the implementation does not add null checks.

### Behavior 17: Add Excludes Constraint

Business goal:
Define a rule that two features must not be active together.

Domain context:
Excludes constraints encode mutually incompatible product options.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{sourceFeature}`) with `productName={productName}`, `featureName={sourceFeature}`, and optional form `description={sourceDescription}` to create the source feature.
3. Use function `add feature to product` (`POST /products/{productName}/features/{excludedFeature}`) with `productName={productName}`, `featureName={excludedFeature}`, and optional form `description={excludedDescription}` to create the excluded feature.
4. Use function `add excludes constraint to product` (`POST /products/{productName}/constraints/excludes`) with `productName={productName}`, form `sourceFeature={sourceFeature}`, and form `excludedFeature={excludedFeature}` to create the rule.

Optional verification workflow:
1. Use function `retrieve product by name` (`GET /products/{productName}`) with `productName={productName}` to inspect product constraints.
2. Create a configuration and use function `activate feature in configuration` for both features to observe invalidation.

Existing-state shortcuts:
- Skip product or feature setup if the same product and features already exist.
- Direct database setup can insert a `ConstraintExcludes` row linked to the product.
- The implementation allows creation even when feature names are missing or null, but the rule will not work as a meaningful domain constraint.

Parameter and value bindings:
- `sourceFeature={sourceFeature}` and `excludedFeature={excludedFeature}` are stored as strings.
- Those strings must match feature names used during later configuration evaluation.
- The generated constraint id is returned in the `Location`, but the implementation uses singular `/constraint/{id}` for excludes creation while the delete endpoint is plural `/constraints/{constraintId}`.

Business result:
An excludes constraint exists for the product.

Constraints and invariants:
- The product must exist.
- Source and excluded feature names are not validated at creation time.
- The rule affects configurations only during activation/deactivation evaluation.

Failure and exceptional cases:
- Failing function: `add excludes constraint to product`
  - Failure condition: No product exists with `name={productName}`.
  - Why it fails: The service resolves the product before persisting the constraint.
  - Violated prerequisite or constraint: Constraint must be product-owned.
- Failing function: `add feature to product`
  - Failure condition: Source or excluded feature setup duplicates an existing feature name.
  - Why it fails: Duplicate feature detection throws `DuplicatedObjectException`.
  - Violated prerequisite or constraint: Product-scoped feature uniqueness.

Implementation notes:
One integration test posts form `excluded` instead of `excludedFeature`; the resource expects `excludedFeature`, so that test only proves a constraint row can be added, not that the excluded feature value is correctly populated.

### Behavior 18: Delete Product Constraint

Business goal:
Remove a stored product constraint.

Domain context:
Product model rules may need to be retired independently from features and configurations.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{sourceFeature}`) with `productName={productName}` and `featureName={sourceFeature}` to create the source feature.
3. Use function `add feature to product` (`POST /products/{productName}/features/{targetFeature}`) with `productName={productName}` and `featureName={targetFeature}` to create the target feature.
4. Use function `add requires constraint to product` (`POST /products/{productName}/constraints/requires`) with `productName={productName}`, form `sourceFeature={sourceFeature}`, and form `requiredFeature={targetFeature}` to create a deletable constraint.
5. Capture `{constraintId}` from the created constraint id or response `Location`.
6. Use function `delete product constraint` (`DELETE /products/{productName}/constraints/{constraintId}`) with `productName={productName}` and `constraintId={constraintId}` to delete it.

Optional verification workflow:
1. Use function `retrieve product by name` (`GET /products/{productName}`) with `productName={productName}` to inspect remaining constraints.

Existing-state shortcuts:
- Skip setup if a constraint already exists and `{constraintId}` is known.
- Direct database setup can insert a `FeatureConstraint` row; `{constraintId}` must be its generated or assigned id.
- An excludes constraint can also be deleted by id, even though the documented precondition in `full-behavior.md` uses a requires constraint.

Parameter and value bindings:
- The `constraintId` generated by constraint creation is consumed by deletion.
- The path `productName` is intended to scope deletion, but the DAO deletes by `constraintId` only.
- The source and target feature names establish the rule text but are not needed by the delete call once the id is known.

Business result:
The constraint row with id `{constraintId}` is removed if it exists.

Constraints and invariants:
- Domain-wise, the constraint should belong to the product named in the path.
- Implementation-wise, product ownership is not checked during deletion.
- Deleting a constraint does not reevaluate existing configurations.

Failure and exceptional cases:
- Failing function: `delete product constraint`
  - Failure condition: `{constraintId}` belongs to a different product than `productName`.
  - Why it fails: It does not fail in the visible implementation; the DAO ignores `productName` and deletes by id.
  - Violated prerequisite or constraint: Product ownership of the constraint.
- Failing function: `delete product constraint`
  - Failure condition: No constraint exists with `{constraintId}`.
  - Why it fails: It does not visibly fail; the delete query affects zero rows and the resource still returns no content.
  - Violated prerequisite or constraint: Constraint id existence.
- Failing function: `add requires constraint to product`
  - Failure condition: Product setup was omitted.
  - Why it fails: Constraint creation resolves the product first.
  - Violated prerequisite or constraint: Product existence.

Implementation notes:
This is the clearest product-scoping discrepancy: the URL is product-scoped, but the implementation is id-only.

### Behavior 19: Configure Variant With Required Feature Derivation

Business goal:
Activate a source feature and automatically include a required dependent feature.

Domain context:
This supports product models where selecting one option implies another option.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{sourceFeature}`) with `productName={productName}`, `featureName={sourceFeature}`, and optional form `description={sourceDescription}` to create the source feature.
3. Use function `add feature to product` (`POST /products/{productName}/features/{requiredFeature}`) with `productName={productName}`, `featureName={requiredFeature}`, and optional form `description={requiredDescription}` to create the required feature.
4. Use function `add requires constraint to product` (`POST /products/{productName}/constraints/requires`) with `productName={productName}`, form `sourceFeature={sourceFeature}`, and form `requiredFeature={requiredFeature}` to create the dependency.
5. Use function `create product configuration` (`POST /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to create the configuration.
6. Use function `activate feature and derive required feature` (`POST /products/{productName}/configurations/{configurationName}/features/{sourceFeature}`) with `productName={productName}`, `configurationName={configurationName}`, and `featureName={sourceFeature}` to activate the source and derive the required feature.

Optional verification workflow:
1. Use function `list active configuration features` (`GET /products/{productName}/configurations/{configurationName}/features`) with `productName={productName}` and `configurationName={configurationName}` to verify both `{sourceFeature}` and `{requiredFeature}` are active.
2. Use function `retrieve product configuration` (`GET /products/{productName}/configurations/{configurationName}`) with the same values to verify `valid=true`.

Existing-state shortcuts:
- Skip setup functions for existing product, features, requires constraint, or configuration.
- Direct database setup can insert the product, features, constraint, and configuration.
- The source and required feature names in the constraint must exactly match features owned by the same product.

Parameter and value bindings:
- `sourceFeature` is used as a feature path value and as the constraint source form value.
- `requiredFeature` is used as a feature path value and as the constraint required form value.
- The same `productName` scopes the features, rule, and configuration.
- Activation consumes `featureName={sourceFeature}` and evaluation consumes the stored `requiredFeature`.

Business result:
The configuration contains both the explicitly activated source feature and the automatically derived required feature. The configuration remains valid.

Constraints and invariants:
- The required feature must exist before derivation.
- The source feature must not already be active.
- Requires constraints are evaluated during activation/deactivation, not when the constraint is created.

Failure and exceptional cases:
- Failing function: `activate feature and derive required feature`
  - Failure condition: The requires constraint references a required feature that does not exist on the product.
  - Why it fails: The evaluator derives the missing name and calls `configuration.active(missingRequiredFeature)`, which resolves through product features and throws `ObjectNotFoundException`.
  - Violated prerequisite or constraint: Required feature must belong to the same product.
- Failing function: `activate feature and derive required feature`
  - Failure condition: The source feature is already active.
  - Why it fails: Duplicate active-feature detection throws `DuplicatedObjectException`.
  - Violated prerequisite or constraint: Source feature must not already be active.
- Failing function: `add requires constraint to product`
  - Failure condition: Product setup is missing.
  - Why it fails: The product is resolved before constraint persistence.
  - Violated prerequisite or constraint: Product existence.
- Failing function: `create product configuration`
  - Failure condition: Product setup is missing.
  - Why it fails: The product is resolved before configuration persistence.
  - Violated prerequisite or constraint: Product existence.

Implementation notes:
The evaluator recursively applies derived features. It mutates the derived-feature set while iterating, which is a technical fragility for more complex derivation sets.

### Behavior 20: Record Invalid Configuration From Excluded Feature Conflict

Business goal:
Detect that a configuration violates an excludes rule when incompatible features are active together.

Domain context:
The product model should prevent or flag invalid feature combinations.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{sourceFeature}`) with `productName={productName}` and `featureName={sourceFeature}` to create the source feature.
3. Use function `add feature to product` (`POST /products/{productName}/features/{excludedFeature}`) with `productName={productName}` and `featureName={excludedFeature}` to create the excluded feature.
4. Use function `add excludes constraint to product` (`POST /products/{productName}/constraints/excludes`) with `productName={productName}`, form `sourceFeature={sourceFeature}`, and form `excludedFeature={excludedFeature}` to create the exclusion.
5. Use function `create product configuration` (`POST /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to create the configuration.
6. Use function `activate feature in configuration` (`POST /products/{productName}/configurations/{configurationName}/features/{sourceFeature}`) with `productName={productName}`, `configurationName={configurationName}`, and `featureName={sourceFeature}` to activate the source feature.
7. Use function `activate feature in configuration` (`POST /products/{productName}/configurations/{configurationName}/features/{excludedFeature}`) with `productName={productName}`, `configurationName={configurationName}`, and `featureName={excludedFeature}` to attempt the conflicting activation.

Optional verification workflow:
1. Use function `retrieve product configuration` (`GET /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to inspect `valid=false`.
2. Use function `list active configuration features` (`GET /products/{productName}/configurations/{configurationName}/features`) with the same values to inspect that both conflicting features can be persisted as active.

Existing-state shortcuts:
- Skip setup if the product, both features, excludes constraint, configuration, and source active-feature state already exist.
- Direct database setup can create the same state, but both feature names in the constraint must match product features for meaningful evaluation.
- The core conflicting activation cannot be skipped for this behavior.

Parameter and value bindings:
- `sourceFeature` and `excludedFeature` are both product feature names and constraint form values.
- The first activation uses `featureName={sourceFeature}`.
- The second activation uses `featureName={excludedFeature}` and consumes the existing excludes rule.
- `configurationName` binds both activations to the same configuration.

Business result:
The endpoint returns an error through `WrongProductConfigurationException`, but the implementation persists the conflicting active feature and sets the configuration `valid=false`.

Constraints and invariants:
- Both features must exist on the same product.
- The excludes constraint is directional in naming but evaluation invalidates when both named features are active.
- The response outcome and persisted state outcome differ: error response, mutated invalid configuration.

Failure and exceptional cases:
- Failing function: `activate feature in configuration`
  - Failure condition: The excluded feature is activated while the source feature is already active under an excludes constraint.
  - Why it fails: The evaluator marks the configuration invalid and the resource throws `WrongProductConfigurationException`.
  - Violated prerequisite or constraint: Excluded feature cannot be active when source feature is active.
- Failing function: `add excludes constraint to product`
  - Failure condition: Product setup is missing.
  - Why it fails: The product is resolved before constraint persistence.
  - Violated prerequisite or constraint: Product existence.
- Failing function: `activate feature in configuration`
  - Failure condition: Either feature is missing from the product or the configuration is missing.
  - Why it fails: Activation resolves configuration and feature ownership.
  - Violated prerequisite or constraint: Product-scoped feature/configuration ownership.

Implementation notes:
Integration tests confirm the invalid state persists after the error: active features increase and `valid` becomes false.

### Behavior 21: Attempt To Deactivate A Required Feature

Business goal:
Keep a required feature active when its source feature remains active.

Domain context:
This enforces dependency consistency when users try to remove an implied feature.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `create product` (`POST /products/{productName}`) with `productName={productName}` to create the product.
2. Use function `add feature to product` (`POST /products/{productName}/features/{sourceFeature}`) with `productName={productName}` and `featureName={sourceFeature}` to create the source feature.
3. Use function `add feature to product` (`POST /products/{productName}/features/{requiredFeature}`) with `productName={productName}` and `featureName={requiredFeature}` to create the required feature.
4. Use function `add requires constraint to product` (`POST /products/{productName}/constraints/requires`) with `productName={productName}`, form `sourceFeature={sourceFeature}`, and form `requiredFeature={requiredFeature}` to create the dependency.
5. Use function `create product configuration` (`POST /products/{productName}/configurations/{configurationName}`) with `productName={productName}` and `configurationName={configurationName}` to create the configuration.
6. Use function `activate feature and derive required feature` (`POST /products/{productName}/configurations/{configurationName}/features/{sourceFeature}`) with `productName={productName}`, `configurationName={configurationName}`, and `featureName={sourceFeature}` to activate both source and required feature.
7. Use function `attempt to deactivate required feature` (`DELETE /products/{productName}/configurations/{configurationName}/features/{requiredFeature}`) with `productName={productName}`, `configurationName={configurationName}`, and `featureName={requiredFeature}` to attempt removal.

Optional verification workflow:
1. Use function `list active configuration features` (`GET /products/{productName}/configurations/{configurationName}/features`) with `productName={productName}` and `configurationName={configurationName}` to verify `{requiredFeature}` remains active.
2. Use function `retrieve product configuration` (`GET /products/{productName}/configurations/{configurationName}`) with the same values to verify `valid=true`.

Existing-state shortcuts:
- Skip setup if the product, source feature, required feature, requires constraint, configuration, and active source/required features already exist.
- Direct database setup can create the same active-feature relations.
- The source feature must remain active; otherwise removing the required feature is just ordinary deactivation.

Parameter and value bindings:
- `sourceFeature` is bound to the constraint source and remains active in the configuration.
- `requiredFeature` is bound to the constraint target and used as the delete path `featureName`.
- The same `productName` and `configurationName` scope derivation and attempted removal.

Business result:
The delete request returns success, but the required feature remains active because evaluation derives it again. The configuration remains valid.

Constraints and invariants:
- Requires rules are reapplied after deactivation.
- The endpoint response does not indicate that the requested feature was restored.
- The behavior depends on source feature remaining active.

Failure and exceptional cases:
- Failing function: `attempt to deactivate required feature`
  - Failure condition: Missing product feature or missing configuration.
  - Why it fails: The same endpoint-level lookup failures as `deactivate feature in configuration` apply.
  - Violated prerequisite or constraint: Product, feature, and configuration must exist and be consistently scoped.
- Failing function: `activate feature and derive required feature`
  - Failure condition: Required feature is missing or source feature already active.
  - Why it fails: Derivation resolves the required feature by product feature name, and duplicate activation is rejected.
  - Violated prerequisite or constraint: Required feature existence and active-feature uniqueness.

Implementation notes:
This is a net-no-op removal from the user’s perspective, but it still exercises deactivation and reevaluation.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Safely Reject Invalid Excluded Feature Activation Without Persisting It

Priority:
Critical domain gap

Expected business goal:
When a user attempts an excluded combination, the service should reject the activation and leave the configuration unchanged.

Why it is unsupported:
The current activation path mutates the configuration first, evaluates second, and the resource throws after the service transaction returns.

Existing functions considered:
- `activate feature in configuration`: Activates the feature before evaluation and can persist invalid state.
- `add excludes constraint to product`: Creates the rule but does not provide rollback behavior.
- `retrieve product configuration`: Can inspect the invalid result but cannot undo it.
- `deactivate feature in configuration`: Can manually repair by removing a conflicting feature, but that is not equivalent to never persisting the invalid selection.

Missing capability:
Transactional validation or pre-validation that rejects an excludes violation before committing the active-feature relation and `valid=false`.

Proof that function composition is insufficient:
Chaining existing functions cannot make the conflicting activation atomic. Once `activate feature in configuration` returns an error for an excludes violation, the invalid active feature and invalid flag may already be persisted. A later deactivation is a compensating repair, not a rollback, and cannot preserve the original uninterrupted valid state.

Evidence from existing functions/source:
- `activate feature in configuration` evaluates after `configuration.active(featureName)`.
- `WrongProductConfigurationException` is thrown by the resource after the service call returns.
- Tests show the invalid feature remains active and `valid=false`.

Business impact:
Clients can receive an error while the stored configuration changes, which makes retries, UI state, and business validation unreliable.

### Missing Behavior 2: Product-Scoped Constraint Deletion

Priority:
Critical domain gap

Expected business goal:
Deleting `/products/{productName}/constraints/{constraintId}` should delete only a constraint owned by `{productName}`.

Why it is unsupported:
The implementation ignores `productName` and deletes by `constraintId` only.

Existing functions considered:
- `delete product constraint`: Deletes by id without ownership enforcement.
- `retrieve product by name`: May help discover constraints but cannot protect deletion.
- `add requires constraint to product` and `add excludes constraint to product`: Create product-owned constraints but do not enforce ownership during deletion.

Missing capability:
A product ownership check in the delete query or service logic.

Proof that function composition is insufficient:
No existing function can make `delete product constraint` verify ownership. Even if a client first retrieves the product and checks the id, the subsequent delete call can still use another product name and delete the constraint by id.

Evidence from existing functions/source:
- `ProductsDAO.deleteConstraintForProduct(productName, constraintId)` builds a delete clause only on `qFeatureConstraint.id.eq(constraintId)`.
- `full-behavior.md` notes that a mismatched product name does not fail.

Business impact:
One product path can delete another product’s rules, weakening product isolation.

### Missing Behavior 3: Validate Constraint Feature Names At Creation

Priority:
Important robustness gap

Expected business goal:
A requires or excludes rule should be accepted only when its source and target feature names exist on the same product and are non-null.

Why it is unsupported:
Constraint creation stores raw submitted strings without checking product features.

Existing functions considered:
- `add requires constraint to product`: Stores `sourceFeature` and `requiredFeature` strings without validation.
- `add excludes constraint to product`: Stores `sourceFeature` and `excludedFeature` strings without validation.
- `add feature to product`: Can create valid feature names, but constraint creation does not require using them.
- `activate feature and derive required feature`: Fails later if a required feature is missing.
- `activate feature in configuration`: Can silently ignore constraints with null or non-matching names until relevant evaluation.

Missing capability:
Creation-time validation for required form fields and product-scoped feature existence.

Proof that function composition is insufficient:
A client can voluntarily create features before constraints, but the API cannot enforce that another caller does so. Once an invalid constraint is stored, no existing function validates or repairs the rule except deleting it by id.

Evidence from existing functions/source:
- OpenAPI marks constraint form fields as `required=false`.
- Service constructors accept submitted strings directly.
- Evaluator resolves derived required features only later during configuration evaluation.

Business impact:
Invalid rules can remain latent until a configuration action fails or behaves as a no-op.

### Missing Behavior 4: Reevaluate Existing Configurations After Model Or Rule Changes

Priority:
Important robustness gap

Expected business goal:
When features or constraints are added, removed, or changed, existing configurations should have their `valid` state recomputed.

Why it is unsupported:
Evaluation is only triggered by active-feature add/remove operations.

Existing functions considered:
- `add requires constraint to product`: Adds a rule but does not evaluate existing configurations.
- `add excludes constraint to product`: Adds a rule but does not invalidate existing conflicting configurations.
- `delete product constraint`: Removes a rule but does not mark previously invalid configurations valid.
- `delete product feature`: Removes active references but does not explicitly reevaluate configuration validity.
- `activate feature in configuration` and `deactivate feature in configuration`: Trigger evaluation, but only for one configuration during that active-feature change.

Missing capability:
A revalidation endpoint, background rule-change evaluation, or transactional model-change propagation.

Proof that function composition is insufficient:
A client can manually touch configurations one by one by activating or deactivating features, but there is no no-op reevaluate operation and no complete list of all affected states with guaranteed consistency. Delete-and-recreate loses configuration identity and active selections.

Evidence from existing functions/source:
- `evaluateAndUpdateConfiguration` is called only from `addFeatureFromConfiguration` and `removeFeatureFromConfiguration`.
- Constraint creation and deletion do not call configuration evaluation.

Business impact:
Configuration validity can become stale after product model changes.

### Missing Behavior 5: Enforce Unique Product And Configuration Identity

Priority:
Important robustness gap

Expected business goal:
Product names should uniquely identify products, and configuration names should uniquely identify configurations within a product.

Why it is unsupported:
The implementation does not visibly enforce uniqueness for products or configurations.

Existing functions considered:
- `create product`: Persists a product without duplicate-name validation.
- `retrieve product by name`: Uses single-result lookup by name, which becomes ambiguous with duplicates.
- `create product configuration`: Persists a configuration without duplicate-name validation.
- `retrieve product configuration`: Uses product/name lookup, which becomes ambiguous with duplicates.
- `list products` and `list product configurations`: Can expose duplicate names but cannot repair identity ambiguity.

Missing capability:
Database unique constraints and API-level duplicate checks for product names and product-scoped configuration names.

Proof that function composition is insufficient:
A client can avoid duplicates voluntarily, but no chain of existing functions prevents another request from creating duplicates. Delete-and-recreate is not equivalent because it loses ids and dependent links.

Evidence from existing functions/source:
- `Product.name` and `ProductConfiguration.name` are `nullable=false` but not unique.
- No duplicate checks exist in `ProductsService.add` or `ProductsConfigurationsService.add`.

Business impact:
Name-based paths become unreliable identifiers.

### Missing Behavior 6: Rename Or Update Product, Feature Name, Configuration Name, Or Constraint Rule

Priority:
API ergonomics gap

Expected business goal:
Users should be able to correct names and edit constraint rules without destructive replacement.

Why it is unsupported:
Only feature descriptions are updateable.

Existing functions considered:
- `update product feature description`: Updates description only, not feature name.
- `delete product feature` plus `add feature to product`: Can replace a feature name but removes active references and does not preserve identity.
- `delete product configuration` plus `create product configuration`: Can replace a configuration name but loses active selections.
- `delete product constraint` plus `add requires constraint to product` or `add excludes constraint to product`: Can replace a rule but changes generated id and does not reevaluate configurations.
- `delete product` plus `create product`: Replaces a product but destroys the aggregate.

Missing capability:
PUT/PATCH endpoints for product metadata, feature names, configuration names, and constraint rule fields.

Proof that function composition is insufficient:
Delete-and-recreate changes identity, relationships, generated ids, active-feature relations, and possibly validity state. It cannot preserve the original resource while changing only the intended field.

Evidence from existing functions/source:
- The only PUT endpoint is `PUT /products/{productName}/features/{featureName}` and it writes only `description`.
- No PUT/PATCH endpoints exist for products, configurations, or constraints.

Business impact:
Routine corrections require destructive workflows and can lose configuration state.

### Missing Behavior 7: Atomically Replace A Configuration’s Full Feature Selection

Priority:
Important robustness gap

Expected business goal:
A client should submit the desired complete feature set for a configuration and have the service validate and apply it atomically.

Why it is unsupported:
The API supports only incremental activate/deactivate operations.

Existing functions considered:
- `activate feature in configuration`: Adds one feature and evaluates after mutation.
- `deactivate feature in configuration`: Removes one feature and evaluates after mutation.
- `list active configuration features`: Can inspect current state but cannot apply a set.
- `delete product configuration` plus `create product configuration`: Loses configuration identity and requires rebuilding state.

Missing capability:
A replace-selection endpoint, such as PUT on configuration features, with all-or-nothing validation.

Proof that function composition is insufficient:
Sequencing individual activations and deactivations can pass through invalid intermediate states, persist invalid excludes conflicts, and produce different results depending on order. Delete-and-recreate is not equivalent because it changes identity and may lose validity history.

Evidence from existing functions/source:
- Only POST and DELETE exist under `/configurations/{configurationName}/features/{featureName}`.
- Evaluation is per mutation.

Business impact:
Clients must implement complex ordering and repair logic for what should be a single product-configuration operation.

### Missing Behavior 8: Dedicated Constraint Discovery And Typed Constraint Retrieval

Priority:
API ergonomics gap

Expected business goal:
Users should list constraints for a product, retrieve one constraint by id, and see source/target feature names in a stable contract.

Why it is unsupported:
There is no dedicated GET endpoint for constraints.

Existing functions considered:
- `retrieve product by name`: Returns product constraints as part of the product, but OpenAPI defines `FeatureConstraint` only with `id` and `type`.
- `add requires constraint to product` and `add excludes constraint to product`: Return only creation status and `Location`, not a full read model.
- `delete product constraint`: Requires knowing `constraintId`.

Missing capability:
`GET /products/{productName}/constraints`, `GET /products/{productName}/constraints/{constraintId}`, and accurate schemas for requires/excludes fields.

Proof that function composition is insufficient:
A client can retrieve the whole product, but that is not a typed constraint query and the OpenAPI schema does not guarantee source/required/excluded fields. There is no way to retrieve a single constraint by id.

Evidence from existing functions/source:
- OpenAPI paths include POST and DELETE for constraints but no GET.
- OpenAPI `FeatureConstraint` schema omits rule-specific fields.

Business impact:
Constraint management is hard to build in a UI or integration client.

### Missing Behavior 9: Clear Not-Found Semantics For Product-Scoped Reads

Priority:
API ergonomics gap

Expected business goal:
The service should distinguish “product exists but has no configurations” from “product does not exist,” and should return clear not-found responses for missing configurations.

Why it is unsupported:
Some product-scoped functions do not validate the parent product or missing child explicitly.

Existing functions considered:
- `list product configurations`: Returns an empty list when the product is absent.
- `retrieve product configuration`: Returns the DAO result without explicit not-found handling.
- `list active configuration features`: Dereferences a missing configuration.
- `delete product configuration`: Attempts to remove a missing configuration.

Missing capability:
Consistent not-found validation and error mapping for product-scoped configuration functions.

Proof that function composition is insufficient:
A client can call `retrieve product by name` first, but the target function itself still has ambiguous or inconsistent behavior. Prechecking also introduces race conditions because the product or configuration can change between calls.

Evidence from existing functions/source:
- `ProductsConfigurationsDAO.findByProductName` returns an empty list from a join query.
- `findByNameAndProductName` returns `singleResult` without throwing the service’s `ObjectNotFoundException`.

Business impact:
Clients cannot reliably interpret empty or failed configuration responses.