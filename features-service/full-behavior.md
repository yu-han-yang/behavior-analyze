### Function 1: list products

Function name:
list products

Core endpoint(s):
- `GET /products`

Preconditions:
- None.

Successful execution:
- Result:
  This function returns the names of all products currently stored by the service.
- Invocation:
  Step 1: `GET /products`
- Constraints:
  No product-specific prerequisite is required. The implementation reads all `Product` entities and returns only their `name` values.

Failure or exceptional branches:
None visible in the OpenAPI contract or `src` implementation.

Endpoint coverage:
- Covers:
  `GET /products`
- Distinct meaning:
  Global product collection listing.

### Function 2: create product

Function name:
create product

Core endpoint(s):
- `POST /products/{productName}`

Preconditions:
- None.

Successful execution:
- Result:
  This function creates a product named `{productName}`.
- Invocation:
  Step 1: `POST /products/{productName}` with path value `productName={productName}`
- Constraints:
  `{productName}` is taken from the path. No request body is used. The implementation persists a `Product` with `name = {productName}` and does not visibly enforce product-name uniqueness.

Failure or exceptional branches:
None visible in the OpenAPI contract or `src` implementation.

Endpoint coverage:
- Covers:
  `POST /products/{productName}`
- Distinct meaning:
  Creates a product resource.

### Function 3: retrieve product by name

Function name:
retrieve product by name

Core endpoint(s):
- `GET /products/{productName}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.

Successful execution:
- Result:
  This function retrieves the product named `{productName}`, including its features and constraints.
- Invocation:
  Step 1: `GET /products/{productName}` with path value `productName={productName}`
- Constraints:
  The `{productName}` path value must identify an existing product.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No product named `{productName}` exists in the database. This can be produced by starting from an empty database, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /products/{productName}`.
  - Failure endpoint:
    `GET /products/{productName}`
  - Why this fails:
    The implementation calls `ProductsDAO.findByName`, which throws `ObjectNotFoundException` when no `Product` row has `name = {productName}`.
  - Intentionally violated constraints:
    The required product state was omitted.

Endpoint coverage:
- Covers:
  `GET /products/{productName}`
- Distinct meaning:
  Reads one product resource by path name.

### Function 4: delete product

Function name:
delete product

Core endpoint(s):
- `DELETE /products/{productName}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.

Successful execution:
- Result:
  This function deletes the product named `{productName}`. The implementation also removes configurations and constraints for that product, and product features are removed through the product relationship.
- Invocation:
  Step 1: `DELETE /products/{productName}` with path value `productName={productName}`
- Constraints:
  The `{productName}` path value must identify the product to remove.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No product named `{productName}` exists in the database. This can be produced by starting from an empty database, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /products/{productName}`.
  - Failure endpoint:
    `DELETE /products/{productName}`
  - Why this fails:
    Product deletion calls `ProductsDAO.findByName`, which throws `ObjectNotFoundException` when the product is absent.
  - Intentionally violated constraints:
    The required product state was omitted.

Endpoint coverage:
- Covers:
  `DELETE /products/{productName}`
- Distinct meaning:
  Removes a product resource.

### Function 5: list product features

Function name:
list product features

Core endpoint(s):
- `GET /products/{productName}/features`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.

Successful execution:
- Result:
  This function retrieves the feature set for the product named `{productName}`.
- Invocation:
  Step 1: `GET /products/{productName}/features` with path value `productName={productName}`
- Constraints:
  The `{productName}` path value must identify an existing product. A product with no features returns an empty feature set.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No product named `{productName}` exists in the database. This can be produced by starting from an empty database, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /products/{productName}`.
  - Failure endpoint:
    `GET /products/{productName}/features`
  - Why this fails:
    The implementation resolves the product by name before reading its features, and `ProductsDAO.findByName` throws `ObjectNotFoundException` when the product is absent.
  - Intentionally violated constraints:
    The required product state was omitted.

Endpoint coverage:
- Covers:
  `GET /products/{productName}/features`
- Distinct meaning:
  Lists features belonging to one product.

### Function 6: add feature to product

Function name:
add feature to product

Core endpoint(s):
- `POST /products/{productName}/features/{featureName}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.

Successful execution:
- Result:
  This function adds a feature named `{featureName}` to product `{productName}` and optionally stores the submitted `description`.
- Invocation:
  Step 1: `POST /products/{productName}/features/{featureName}` with path values `productName={productName}` and `featureName={featureName}`, and optional form field `description={description}`
- Constraints:
  `{productName}` must identify an existing product. `{featureName}` must not already exist on that product; the implementation compares feature names case-insensitively. If `description` is omitted, the stored feature description is `null`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No product named `{productName}` exists in the database. This can be produced by starting from an empty database, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /products/{productName}`.
  - Failure endpoint:
    `POST /products/{productName}/features/{featureName}`
  - Why this fails:
    The implementation resolves the product before inserting the feature, and `ProductsDAO.findByName` throws `ObjectNotFoundException` if the product is missing.
  - Intentionally violated constraints:
    The required product state was omitted.
- Branch 2:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - A feature named `{featureName}` already exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {featureName}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{featureName}` once before the failing request with optional form field `description={description}`.
  - Failure endpoint:
    `POST /products/{productName}/features/{featureName}`
  - Why this fails:
    `ProductsService.addFeatureToProduct` checks `product.hasFeatureNamed(featureName)` and throws `DuplicatedObjectException` when the product already has that feature name.
  - Intentionally violated constraints:
    The feature-name uniqueness requirement for a single product was violated by invoking the creation endpoint for an already existing feature.

Endpoint coverage:
- Covers:
  `POST /products/{productName}/features/{featureName}`
- Distinct meaning:
  Creates a product feature.

### Function 7: update product feature description

Function name:
update product feature description

Core endpoint(s):
- `PUT /products/{productName}/features/{featureName}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
- A feature named `{featureName}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {featureName}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{featureName}` with optional form field `description={initialDescription}`.

Successful execution:
- Result:
  This function updates the `description` of feature `{featureName}` on product `{productName}` and returns the updated feature.
- Invocation:
  Step 1: `PUT /products/{productName}/features/{featureName}` with path values `productName={productName}` and `featureName={featureName}`, and optional form field `description={description}`
- Constraints:
  `{productName}` must identify an existing product, and `{featureName}` must identify a feature linked to that product. If `description` is omitted, the implementation sets the stored description to `null`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - No feature named `{featureName}` exists for product `{productName}`. This can be produced by not inserting a linked `Feature` row and not calling `POST /products/{productName}/features/{featureName}`.
  - Failure endpoint:
    `PUT /products/{productName}/features/{featureName}`
  - Why this fails:
    The product exists, but `Product.findProductFeatureByName` throws `ObjectNotFoundException` because the feature is missing from that product.
  - Intentionally violated constraints:
    The required product-feature state was omitted.
- Branch 2:
  - Preconditions:
    - No product named `{productName}` exists in the database. This can be produced by starting from an empty database, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /products/{productName}`.
  - Failure endpoint:
    `PUT /products/{productName}/features/{featureName}`
  - Why this fails:
    The implementation calls `ProductsDAO.findByName` before looking up the feature, and that lookup throws `ObjectNotFoundException` when the product is absent.
  - Intentionally violated constraints:
    The required product state was omitted.

Endpoint coverage:
- Covers:
  `PUT /products/{productName}/features/{featureName}`
- Distinct meaning:
  Updates a feature's description.

### Function 8: delete product feature

Function name:
delete product feature

Core endpoint(s):
- `DELETE /products/{productName}/features/{featureName}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
- A feature named `{featureName}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {featureName}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{featureName}` with optional form field `description={description}`.

Successful execution:
- Result:
  This function deletes feature `{featureName}` from product `{productName}`. If that feature is active in any product configuration, the implementation removes it from those configurations before deleting the feature.
- Invocation:
  Step 1: `DELETE /products/{productName}/features/{featureName}` with path values `productName={productName}` and `featureName={featureName}`
- Constraints:
  `{productName}` must identify an existing product, and `{featureName}` must identify a feature linked to that product.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - No feature named `{featureName}` exists for product `{productName}`. This can be produced by not inserting a linked `Feature` row and not calling `POST /products/{productName}/features/{featureName}`.
  - Failure endpoint:
    `DELETE /products/{productName}/features/{featureName}`
  - Why this fails:
    `Product.findProductFeatureByName` throws `ObjectNotFoundException` because the feature is missing from the product.
  - Intentionally violated constraints:
    The required product-feature state was omitted.
- Branch 2:
  - Preconditions:
    - No product named `{productName}` exists in the database. This can be produced by starting from an empty database, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /products/{productName}`.
  - Failure endpoint:
    `DELETE /products/{productName}/features/{featureName}`
  - Why this fails:
    The implementation calls `ProductsDAO.findByName` before looking up the feature, and that lookup throws `ObjectNotFoundException` when the product is absent.
  - Intentionally violated constraints:
    The required product state was omitted.

Endpoint coverage:
- Covers:
  `DELETE /products/{productName}/features/{featureName}`
- Distinct meaning:
  Removes a feature from a product.

### Function 9: list product configurations

Function name:
list product configurations

Core endpoint(s):
- `GET /products/{productName}/configurations`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.

Successful execution:
- Result:
  This function returns the names of configurations belonging to product `{productName}`.
- Invocation:
  Step 1: `GET /products/{productName}/configurations` with path value `productName={productName}`
- Constraints:
  The path is product-scoped, and a product with no configurations can return an empty list. The visible implementation does not validate the parent product before querying configurations.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No product named `{productName}` exists in the database. This can be produced by starting from an empty database, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /products/{productName}`.
  - Failure endpoint:
    `GET /products/{productName}/configurations`
  - Why this fails:
    This does not fail in the visible implementation. `ProductsConfigurationsDAO.findByProductName` queries configurations joined to products by name and returns an empty list when no matching product/configuration rows exist.
  - Intentionally violated constraints:
    The parent product state was omitted, but the implementation does not enforce that product-scoped prerequisite.

Endpoint coverage:
- Covers:
  `GET /products/{productName}/configurations`
- Distinct meaning:
  Lists configuration names for one product.

### Function 10: create product configuration

Function name:
create product configuration

Core endpoint(s):
- `POST /products/{productName}/configurations/{configurationName}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.

Successful execution:
- Result:
  This function creates configuration `{configurationName}` for product `{productName}`. The new configuration starts valid and has no active features.
- Invocation:
  Step 1: `POST /products/{productName}/configurations/{configurationName}` with path values `productName={productName}` and `configurationName={configurationName}`
- Constraints:
  `{productName}` must identify an existing product. The visible implementation does not reject duplicate configuration names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No product named `{productName}` exists in the database. This can be produced by starting from an empty database, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /products/{productName}`.
  - Failure endpoint:
    `POST /products/{productName}/configurations/{configurationName}`
  - Why this fails:
    Configuration creation resolves the product first through `ProductsService.findByName`, which throws `ObjectNotFoundException` if the product is absent.
  - Intentionally violated constraints:
    The required product state was omitted.

Endpoint coverage:
- Covers:
  `POST /products/{productName}/configurations/{configurationName}`
- Distinct meaning:
  Creates a configuration for a product.

### Function 11: retrieve product configuration

Function name:
retrieve product configuration

Core endpoint(s):
- `GET /products/{productName}/configurations/{configurationName}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
- A configuration named `{configurationName}` exists for product `{productName}`. This can be satisfied by directly inserting a `ProductConfiguration` row with `name = {configurationName}`, `valid = true`, and a link to the product, or by calling `POST /products/{productName}/configurations/{configurationName}`.

Successful execution:
- Result:
  This function retrieves configuration `{configurationName}` for product `{productName}`, including its validity and active features.
- Invocation:
  Step 1: `GET /products/{productName}/configurations/{configurationName}` with path values `productName={productName}` and `configurationName={configurationName}`
- Constraints:
  `{productName}` and `{configurationName}` must identify a configuration linked to that product.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - No configuration named `{configurationName}` exists for product `{productName}`. This can be produced by not inserting a linked `ProductConfiguration` row and not calling `POST /products/{productName}/configurations/{configurationName}`.
  - Failure endpoint:
    `GET /products/{productName}/configurations/{configurationName}`
  - Why this fails:
    The DAO returns no configuration. The visible implementation does not explicitly throw `ObjectNotFoundException` here, so the source code does not map this missing configuration to a clear not-found response.
  - Intentionally violated constraints:
    The required product-configuration state was omitted.

Endpoint coverage:
- Covers:
  `GET /products/{productName}/configurations/{configurationName}`
- Distinct meaning:
  Reads one product configuration.

### Function 12: delete product configuration

Function name:
delete product configuration

Core endpoint(s):
- `DELETE /products/{productName}/configurations/{configurationName}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
- A configuration named `{configurationName}` exists for product `{productName}`. This can be satisfied by directly inserting a `ProductConfiguration` row with `name = {configurationName}`, `valid = true`, and a link to the product, or by calling `POST /products/{productName}/configurations/{configurationName}`.

Successful execution:
- Result:
  This function deletes configuration `{configurationName}` from product `{productName}`.
- Invocation:
  Step 1: `DELETE /products/{productName}/configurations/{configurationName}` with path values `productName={productName}` and `configurationName={configurationName}`
- Constraints:
  `{productName}` and `{configurationName}` must identify a configuration linked to that product.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - No configuration named `{configurationName}` exists for product `{productName}`. This can be produced by not inserting a linked `ProductConfiguration` row and not calling `POST /products/{productName}/configurations/{configurationName}`.
  - Failure endpoint:
    `DELETE /products/{productName}/configurations/{configurationName}`
  - Why this fails:
    `ProductsConfigurationsDAO.findByNameAndProductName` returns no configuration, and `deleteConfigurationForProduct` attempts to remove that missing entity.
  - Intentionally violated constraints:
    The required product-configuration state was omitted.

Endpoint coverage:
- Covers:
  `DELETE /products/{productName}/configurations/{configurationName}`
- Distinct meaning:
  Removes one product configuration.

### Function 13: list active configuration features

Function name:
list active configuration features

Core endpoint(s):
- `GET /products/{productName}/configurations/{configurationName}/features`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
- A configuration named `{configurationName}` exists for product `{productName}`. This can be satisfied by directly inserting a `ProductConfiguration` row with `name = {configurationName}`, `valid = true`, and a link to the product, or by calling `POST /products/{productName}/configurations/{configurationName}`.

Successful execution:
- Result:
  This function returns the names of features currently active in configuration `{configurationName}` for product `{productName}`.
- Invocation:
  Step 1: `GET /products/{productName}/configurations/{configurationName}/features` with path values `productName={productName}` and `configurationName={configurationName}`
- Constraints:
  `{productName}` and `{configurationName}` must identify a configuration linked to that product. A configuration with no active features returns an empty active-feature list.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - No configuration named `{configurationName}` exists for product `{productName}`. This can be produced by not inserting a linked `ProductConfiguration` row and not calling `POST /products/{productName}/configurations/{configurationName}`.
  - Failure endpoint:
    `GET /products/{productName}/configurations/{configurationName}/features`
  - Why this fails:
    The DAO returns no configuration, and the implementation then dereferences that missing configuration to read active features.
  - Intentionally violated constraints:
    The required product-configuration state was omitted.

Endpoint coverage:
- Covers:
  `GET /products/{productName}/configurations/{configurationName}/features`
- Distinct meaning:
  Lists active feature names for one configuration.

### Function 14: activate feature in configuration

Function name:
activate feature in configuration

Core endpoint(s):
- `POST /products/{productName}/configurations/{configurationName}/features/{featureName}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
- A feature named `{featureName}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {featureName}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{featureName}` with optional form field `description={description}`.
- A configuration named `{configurationName}` exists for product `{productName}`. This can be satisfied by directly inserting a `ProductConfiguration` row with `name = {configurationName}`, `valid = true`, and a link to the product, or by calling `POST /products/{productName}/configurations/{configurationName}`.

Successful execution:
- Result:
  This function activates product feature `{featureName}` in configuration `{configurationName}` for product `{productName}`.
- Invocation:
  Step 1: `POST /products/{productName}/configurations/{configurationName}/features/{featureName}` with path values `productName={productName}`, `configurationName={configurationName}`, and `featureName={featureName}`
- Constraints:
  The same `{productName}` must scope the product, feature, and configuration. `{featureName}` must be a feature of that product, `{configurationName}` must be a configuration of that product, the feature must not already be active in that configuration, and constraint evaluation must leave the configuration valid.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - A feature named `{featureName}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {featureName}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{featureName}` with optional form field `description={description}`.
    - A configuration named `{configurationName}` exists for product `{productName}`. This can be satisfied by directly inserting a `ProductConfiguration` row with `name = {configurationName}`, `valid = true`, and a link to the product, or by calling `POST /products/{productName}/configurations/{configurationName}`.
    - Feature `{featureName}` is already active in configuration `{configurationName}`. This can be satisfied by directly inserting the many-to-many active-feature relation between the `ProductConfiguration` and `Feature`, or by calling `POST /products/{productName}/configurations/{configurationName}/features/{featureName}` once before the failing request.
  - Failure endpoint:
    `POST /products/{productName}/configurations/{configurationName}/features/{featureName}`
  - Why this fails:
    `ProductsConfigurationsService.addFeatureFromConfiguration` checks `configuration.hasActiveFeature(featureName)` and throws `DuplicatedObjectException` when the feature is already active.
  - Intentionally violated constraints:
    The active-feature uniqueness requirement was violated by invoking the activation endpoint for an already active feature.
- Branch 2:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - A configuration named `{configurationName}` exists for product `{productName}`. This can be satisfied by directly inserting a `ProductConfiguration` row with `name = {configurationName}`, `valid = true`, and a link to the product, or by calling `POST /products/{productName}/configurations/{configurationName}`.
    - No feature named `{featureName}` exists for product `{productName}`. This can be produced by not inserting a linked `Feature` row and not calling `POST /products/{productName}/features/{featureName}`; it also includes using a feature name that belongs only to another product.
  - Failure endpoint:
    `POST /products/{productName}/configurations/{configurationName}/features/{featureName}`
  - Why this fails:
    `ProductConfiguration.active(featureName)` resolves the feature through `Product.findProductFeatureByName`, which throws `ObjectNotFoundException` if the feature is not part of the configuration's product.
  - Intentionally violated constraints:
    The activated feature does not belong to the product that owns the configuration.
- Branch 3:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - A feature named `{featureName}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {featureName}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{featureName}` with optional form field `description={description}`.
    - No configuration named `{configurationName}` exists for product `{productName}`. This can be produced by not inserting a linked `ProductConfiguration` row and not calling `POST /products/{productName}/configurations/{configurationName}`.
  - Failure endpoint:
    `POST /products/{productName}/configurations/{configurationName}/features/{featureName}`
  - Why this fails:
    The DAO returns no configuration, and the implementation then tries to use that missing configuration.
  - Intentionally violated constraints:
    The required product-configuration state was omitted.
- Branch 4:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - A feature named `{sourceFeature}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {sourceFeature}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{sourceFeature}` with optional form field `description={description}`.
    - A feature named `{excludedFeature}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {excludedFeature}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{excludedFeature}` with optional form field `description={description}`.
    - An excludes constraint exists for product `{productName}` with `sourceFeatureName = {sourceFeature}` and `excludedFeatureName = {excludedFeature}`. This can be satisfied by directly inserting a `ConstraintExcludes` row linked to `{productName}` or by calling `POST /products/{productName}/constraints/excludes` with form fields `sourceFeature={sourceFeature}` and `excludedFeature={excludedFeature}`.
    - A configuration named `{configurationName}` exists for product `{productName}`. This can be satisfied by directly inserting a `ProductConfiguration` row with `name = {configurationName}`, `valid = true`, and a link to the product, or by calling `POST /products/{productName}/configurations/{configurationName}`.
    - Feature `{sourceFeature}` is already active in configuration `{configurationName}`. This can be satisfied by directly inserting the many-to-many active-feature relation between the configuration and the source feature, or by calling `POST /products/{productName}/configurations/{configurationName}/features/{sourceFeature}` after the product, feature, constraint, and configuration state exists.
  - Failure endpoint:
    `POST /products/{productName}/configurations/{configurationName}/features/{excludedFeature}`
  - Why this fails:
    The evaluator marks the configuration invalid because both excluded features are active, and the resource throws `WrongProductConfigurationException`. The implementation sets the configuration invalid before the resource throws, so the invalid active-feature state can be persisted.
  - Intentionally violated constraints:
    `{excludedFeature}` is activated while `{sourceFeature}` is already active under an excludes constraint for the same product.

Endpoint coverage:
- Covers:
  `POST /products/{productName}/configurations/{configurationName}/features/{featureName}`
- Distinct meaning:
  Activates one existing product feature in one configuration and evaluates product constraints.

### Function 15: activate feature and derive required feature

Function name:
activate feature and derive required feature

Core endpoint(s):
- `POST /products/{productName}/configurations/{configurationName}/features/{featureName}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
- A feature named `{sourceFeature}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {sourceFeature}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{sourceFeature}` with optional form field `description={description}`.
- A feature named `{requiredFeature}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {requiredFeature}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{requiredFeature}` with optional form field `description={description}`.
- A requires constraint exists for product `{productName}` with `sourceFeatureName = {sourceFeature}` and `requiredFeatureName = {requiredFeature}`. This can be satisfied by directly inserting a `ConstraintRequires` row linked to `{productName}` or by calling `POST /products/{productName}/constraints/requires` with form fields `sourceFeature={sourceFeature}` and `requiredFeature={requiredFeature}`.
- A configuration named `{configurationName}` exists for product `{productName}`. This can be satisfied by directly inserting a `ProductConfiguration` row with `name = {configurationName}`, `valid = true`, and a link to the product, or by calling `POST /products/{productName}/configurations/{configurationName}`.

Successful execution:
- Result:
  This function activates `{sourceFeature}` in configuration `{configurationName}` and, because of the requires constraint, automatically activates `{requiredFeature}` as a derived feature.
- Invocation:
  Step 1: `POST /products/{productName}/configurations/{configurationName}/features/{sourceFeature}` with path values `productName={productName}`, `configurationName={configurationName}`, and `featureName={sourceFeature}`
- Constraints:
  The same `{productName}` must scope the product, both features, the requires constraint, and the configuration. The constraint's `sourceFeature` and `requiredFeature` values must exactly match existing product feature names. `{sourceFeature}` must not already be active before the invocation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - A feature named `{sourceFeature}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {sourceFeature}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{sourceFeature}` with optional form field `description={description}`.
    - A requires constraint exists for product `{productName}` with `sourceFeatureName = {sourceFeature}` and `requiredFeatureName = {missingRequiredFeature}`. This can be satisfied by directly inserting a `ConstraintRequires` row linked to `{productName}` or by calling `POST /products/{productName}/constraints/requires` with form fields `sourceFeature={sourceFeature}` and `requiredFeature={missingRequiredFeature}`.
    - A configuration named `{configurationName}` exists for product `{productName}`. This can be satisfied by directly inserting a `ProductConfiguration` row with `name = {configurationName}`, `valid = true`, and a link to the product, or by calling `POST /products/{productName}/configurations/{configurationName}`.
    - No feature named `{missingRequiredFeature}` exists for product `{productName}`. This can be produced by not inserting a linked `Feature` row and not calling `POST /products/{productName}/features/{missingRequiredFeature}`.
  - Failure endpoint:
    `POST /products/{productName}/configurations/{configurationName}/features/{sourceFeature}`
  - Why this fails:
    The evaluator derives `{missingRequiredFeature}` from the requires constraint, and `ConfigurationEvaluator` then calls `configuration.active(missingRequiredFeature)`. That call resolves the feature through the product and throws `ObjectNotFoundException` when the required feature does not exist.
  - Intentionally violated constraints:
    The requires constraint references a required feature that is not a feature of the same product.

Endpoint coverage:
- Covers:
  `POST /products/{productName}/configurations/{configurationName}/features/{featureName}`
- Distinct meaning:
  Uses requires-constraint evaluation during feature activation to add another active feature automatically.

### Function 16: deactivate feature in configuration

Function name:
deactivate feature in configuration

Core endpoint(s):
- `DELETE /products/{productName}/configurations/{configurationName}/features/{featureName}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
- A feature named `{featureName}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {featureName}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{featureName}` with optional form field `description={description}`.
- A configuration named `{configurationName}` exists for product `{productName}`. This can be satisfied by directly inserting a `ProductConfiguration` row with `name = {configurationName}`, `valid = true`, and a link to the product, or by calling `POST /products/{productName}/configurations/{configurationName}`.
- Feature `{featureName}` is active in configuration `{configurationName}`. This can be satisfied by directly inserting the many-to-many active-feature relation between the configuration and feature, or by calling `POST /products/{productName}/configurations/{configurationName}/features/{featureName}` after the product, feature, and configuration state exists.

Successful execution:
- Result:
  This function removes active feature `{featureName}` from configuration `{configurationName}` for product `{productName}`.
- Invocation:
  Step 1: `DELETE /products/{productName}/configurations/{configurationName}/features/{featureName}` with path values `productName={productName}`, `configurationName={configurationName}`, and `featureName={featureName}`
- Constraints:
  The same `{productName}`, `{configurationName}`, and `{featureName}` must identify the active feature relation being removed. The implementation still returns success for an existing but already inactive feature; the active-state precondition is required for this function to perform an actual deactivation.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - A configuration named `{configurationName}` exists for product `{productName}`. This can be satisfied by directly inserting a `ProductConfiguration` row with `name = {configurationName}`, `valid = true`, and a link to the product, or by calling `POST /products/{productName}/configurations/{configurationName}`.
    - No feature named `{featureName}` exists for product `{productName}`. This can be produced by not inserting a linked `Feature` row and not calling `POST /products/{productName}/features/{featureName}`.
  - Failure endpoint:
    `DELETE /products/{productName}/configurations/{configurationName}/features/{featureName}`
  - Why this fails:
    Deactivation resolves `{featureName}` through the product's feature set, and `Product.findProductFeatureByName` throws `ObjectNotFoundException` if the feature is absent.
  - Intentionally violated constraints:
    The required product-feature state was omitted.
- Branch 2:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - A feature named `{featureName}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {featureName}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{featureName}` with optional form field `description={description}`.
    - No configuration named `{configurationName}` exists for product `{productName}`. This can be produced by not inserting a linked `ProductConfiguration` row and not calling `POST /products/{productName}/configurations/{configurationName}`.
  - Failure endpoint:
    `DELETE /products/{productName}/configurations/{configurationName}/features/{featureName}`
  - Why this fails:
    The DAO returns no configuration, and the implementation attempts to deactivate a feature on that missing configuration.
  - Intentionally violated constraints:
    The required product-configuration state was omitted.

Endpoint coverage:
- Covers:
  `DELETE /products/{productName}/configurations/{configurationName}/features/{featureName}`
- Distinct meaning:
  Removes an active feature from a configuration and reevaluates product constraints.

### Function 17: attempt to deactivate required feature

Function name:
attempt to deactivate required feature

Core endpoint(s):
- `DELETE /products/{productName}/configurations/{configurationName}/features/{featureName}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
- A feature named `{sourceFeature}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {sourceFeature}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{sourceFeature}` with optional form field `description={description}`.
- A feature named `{requiredFeature}` exists for product `{productName}`. This can be satisfied by directly inserting a `Feature` row with `name = {requiredFeature}`, optional `description`, and a link to the product, or by calling `POST /products/{productName}/features/{requiredFeature}` with optional form field `description={description}`.
- A requires constraint exists for product `{productName}` with `sourceFeatureName = {sourceFeature}` and `requiredFeatureName = {requiredFeature}`. This can be satisfied by directly inserting a `ConstraintRequires` row linked to `{productName}` or by calling `POST /products/{productName}/constraints/requires` with form fields `sourceFeature={sourceFeature}` and `requiredFeature={requiredFeature}`.
- A configuration named `{configurationName}` exists for product `{productName}`. This can be satisfied by directly inserting a `ProductConfiguration` row with `name = {configurationName}`, `valid = true`, and a link to the product, or by calling `POST /products/{productName}/configurations/{configurationName}`.
- Feature `{sourceFeature}` is active in configuration `{configurationName}`, and `{requiredFeature}` is active because it was derived by the requires constraint. This can be satisfied by directly inserting active-feature relations for both features, or by calling `POST /products/{productName}/configurations/{configurationName}/features/{sourceFeature}` after the product, features, requires constraint, and configuration state exists.

Successful execution:
- Result:
  This function attempts to remove `{requiredFeature}` from a configuration, but because `{sourceFeature}` remains active and requires it, the evaluator automatically activates `{requiredFeature}` again. The endpoint returns success, and the required feature remains active.
- Invocation:
  Step 1: `DELETE /products/{productName}/configurations/{configurationName}/features/{requiredFeature}` with path values `productName={productName}`, `configurationName={configurationName}`, and `featureName={requiredFeature}`
- Constraints:
  `{sourceFeature}` must remain active, the requires constraint must still exist, and `{requiredFeature}` must identify the feature named by that constraint. The delete request is accepted only if the product, configuration, and feature lookup steps succeed.

Failure or exceptional branches:
None specific to the required-feature restoration path. Missing product-feature or product-configuration state causes the same endpoint-level failures described for deleting a configuration feature.

Endpoint coverage:
- Covers:
  `DELETE /products/{productName}/configurations/{configurationName}/features/{featureName}`
- Distinct meaning:
  Accepts a delete request while constraint evaluation restores the required feature.

### Function 18: add requires constraint to product

Function name:
add requires constraint to product

Core endpoint(s):
- `POST /products/{productName}/constraints/requires`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.

Successful execution:
- Result:
  This function adds a requires constraint to product `{productName}`: when `sourceFeature` is active in a configuration, `requiredFeature` should also become active.
- Invocation:
  Step 1: `POST /products/{productName}/constraints/requires` with path value `productName={productName}` and form fields `sourceFeature={sourceFeature}` and `requiredFeature={requiredFeature}`
- Constraints:
  `{productName}` must identify an existing product. The implementation stores the submitted feature-name strings and does not validate that either feature exists at constraint-creation time. The OpenAPI definition marks both form fields optional, and the implementation does not add separate null checks.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No product named `{productName}` exists in the database. This can be produced by starting from an empty database, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /products/{productName}`.
  - Failure endpoint:
    `POST /products/{productName}/constraints/requires`
  - Why this fails:
    The implementation resolves the product before persisting the constraint, and `ProductsDAO.findByName` throws `ObjectNotFoundException` if the product is absent.
  - Intentionally violated constraints:
    The required product state was omitted.

Endpoint coverage:
- Covers:
  `POST /products/{productName}/constraints/requires`
- Distinct meaning:
  Creates a product-level requires constraint.

### Function 19: add excludes constraint to product

Function name:
add excludes constraint to product

Core endpoint(s):
- `POST /products/{productName}/constraints/excludes`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.

Successful execution:
- Result:
  This function adds an excludes constraint to product `{productName}`: `excludedFeature` must not be active when `sourceFeature` is active.
- Invocation:
  Step 1: `POST /products/{productName}/constraints/excludes` with path value `productName={productName}` and form fields `sourceFeature={sourceFeature}` and `excludedFeature={excludedFeature}`
- Constraints:
  `{productName}` must identify an existing product. The implementation stores the submitted feature-name strings and does not validate that either feature exists at constraint-creation time. The OpenAPI definition marks both form fields optional, and the implementation does not add separate null checks. The implementation returns a `Location` using `/products/{productName}/constraint/{id}` with singular `constraint`, while the documented delete path is `/products/{productName}/constraints/{constraintId}`.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No product named `{productName}` exists in the database. This can be produced by starting from an empty database, deleting the product beforehand, or intentionally not inserting it directly and not calling `POST /products/{productName}`.
  - Failure endpoint:
    `POST /products/{productName}/constraints/excludes`
  - Why this fails:
    The implementation resolves the product before persisting the constraint, and `ProductsDAO.findByName` throws `ObjectNotFoundException` if the product is absent.
  - Intentionally violated constraints:
    The required product state was omitted.

Endpoint coverage:
- Covers:
  `POST /products/{productName}/constraints/excludes`
- Distinct meaning:
  Creates a product-level excludes constraint.

### Function 20: delete product constraint

Function name:
delete product constraint

Core endpoint(s):
- `DELETE /products/{productName}/constraints/{constraintId}`

Preconditions:
- A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
- A requires constraint exists for product `{productName}` with `sourceFeatureName = {sourceFeature}` and `requiredFeatureName = {requiredFeature}`. This can be satisfied by directly inserting a `ConstraintRequires` row linked to `{productName}` or by calling `POST /products/{productName}/constraints/requires` with form fields `sourceFeature={sourceFeature}` and `requiredFeature={requiredFeature}`. An excludes constraint created by `POST /products/{productName}/constraints/excludes` can also provide a deletable constraint row, but this function's stated precondition uses a requires constraint.
- The `{constraintId}` used by `DELETE /products/{productName}/constraints/{constraintId}` must identify the constraint described above. If the API is used to establish the constraint, `{constraintId}` must be obtained from the constraint creation response location or created resource id. If direct database setup is used, `{constraintId}` must be the generated or assigned id of the inserted `FeatureConstraint` row.

Successful execution:
- Result:
  This function deletes constraint `{constraintId}`.
- Invocation:
  Step 1: `DELETE /products/{productName}/constraints/{constraintId}` with path values `productName={productName}` and `constraintId={constraintId}`
- Constraints:
  `{constraintId}` must refer to an existing feature constraint. The OpenAPI path suggests `{productName}` scopes the deletion, but the implementation deletes by `constraintId` only and ignores `{productName}` in the delete query.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - A product named `{productNameA}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productNameA}` or by calling `POST /products/{productNameA}`.
    - A requires constraint exists for product `{productNameA}` with `sourceFeatureName = {sourceFeature}` and `requiredFeatureName = {requiredFeature}`. This can be satisfied by directly inserting a `ConstraintRequires` row linked to `{productNameA}` or by calling `POST /products/{productNameA}/constraints/requires` with form fields `sourceFeature={sourceFeature}` and `requiredFeature={requiredFeature}`.
    - A product named `{productNameB}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productNameB}` or by calling `POST /products/{productNameB}`.
    - The `{constraintId}` used in the delete request identifies the constraint owned by `{productNameA}`, not `{productNameB}`. If the API is used to establish the constraint, `{constraintId}` must be obtained from the creation response for `POST /products/{productNameA}/constraints/requires`; direct database setup must use the id of the constraint row linked to `{productNameA}`.
  - Failure endpoint:
    `DELETE /products/{productNameB}/constraints/{constraintId}`
  - Why this fails:
    This does not fail in the visible implementation. `ProductsDAO.deleteConstraintForProduct` ignores the `productName` argument and deletes the row matching `{constraintId}` without checking whether `{productNameB}` owns it.
  - Intentionally violated constraints:
    The product ownership relationship was mismatched: `{constraintId}` belongs to `{productNameA}`, but the delete path uses `{productNameB}`.
- Branch 2:
  - Preconditions:
    - A product named `{productName}` exists in the database. This can be satisfied by directly inserting a `Product` row with `name = {productName}` or by calling `POST /products/{productName}`.
    - No `FeatureConstraint` row with id `{constraintId}` exists for `{productName}` or any other product. This can be produced by not inserting a constraint directly and not calling `POST /products/{productName}/constraints/requires` or `POST /products/{productName}/constraints/excludes`; any arbitrary `{constraintId}` used here is not a generated id from a constraint creation response.
  - Failure endpoint:
    `DELETE /products/{productName}/constraints/{constraintId}`
  - Why this fails:
    This does not visibly fail in the implementation. The delete query can affect zero rows, and the resource still returns no content.
  - Intentionally violated constraints:
    The required generated constraint id was omitted or replaced with an id that does not identify a stored constraint.

Endpoint coverage:
- Covers:
  `DELETE /products/{productName}/constraints/{constraintId}`
- Distinct meaning:
  Removes a stored feature constraint by id.
