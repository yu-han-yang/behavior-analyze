# Business Behavior Coverage Report

## Executive Summary

- Project under analysis: `/Users/yangyuhan/behavior-analyze/features-service`
- Test suites analyzed: `tests/EM_features_service_True_6_Test.java`, `tests/EM_features_service_False_6_Test.java`
- Coverage reports analyzed: `coverage/evomaster_10130_features-service__10131/report.xml`, `coverage/evomaster_10140_features-service__10141/report.xml`
- Source analyzed: `src/main/java`
- Total documented business behaviors: `21`
- Covered: `1`
- Partially covered: `18`
- Not covered: `2`
- Unclear: `0`
- Business behavior coverage: `47.6%`
- Combined JaCoCo coverage signal: `31.7% lines`, `8.3% branches`, `33.5% methods`. The two XML reports have identical aggregate counters, so the element-level union equals each individual report: 145/457 lines, 7/84 branches, and 58/173 methods.
- Failure/exceptional-case coverage: `60.9%` (`28/46` countable documented failure/edge items)
- Successful required-workflow coverage: `4.8%` (`1/21` behaviors have the full successful workflow covered)
- Behavior checklist coverage: `29/67` coverage items covered, counting one happy-path item per behavior plus countable documented failure/edge items.

The generated tests provide broad negative-path evidence for missing products, missing configurations, and arbitrary no-op constraint deletion. They do not build the product, feature, configuration, constraint, and active-feature state needed for most successful business workflows. Only `Create Product` is fully covered as a successful required workflow. Constraint evaluation, required-feature derivation, excluded-feature invalidation, feature/configuration lifecycle success paths, and read-after-write verification are the largest gaps.

Rows whose documented failure condition is `None visible` or `No meaningful setup failure is visible` are treated as non-countable documentation notes, not failure coverage items.

## Coverage Matrix

| ID | Business Behavior | Required Workflow Function Coverage | Failing Function Coverage | Optional Verification Coverage | Status | Confidence |
|---|---|---|---|---|---|---|
| B1 | List Products | No `list products` call; no `GET /products` generated test. | No countable failure item. | Not applicable. | Not Covered | High |
| B2 | Create Product | `create product` is directly exercised by `test_3_postOnProductReturns201` in both generated suites. | No countable failure item. | `retrieve product by name` and `list products` not executed after creation. | Covered | Medium |
| B3 | Retrieve Product Definition | No ordered `create product` then `retrieve product by name` workflow. | `retrieve product by name` missing-product failure covered by `test_1_getOnProductCauses500_internalServerError`. | Not applicable. | Partially Covered | High |
| B4 | Retire Product And Dependent State | No create-feature-configuration-constraint-delete workflow. | `delete product`, `create product configuration`, and `add requires constraint to product` missing-product failures covered; duplicate `add feature to product` not covered. | Post-delete `list products` and `retrieve product by name` not executed. | Partially Covered | High |
| B5 | List Product Features | No create-product then `list product features` workflow. | `list product features` missing-product failure covered by `test_4_getOnProductFeaturesCauses500_internalServerError`. | Not applicable. | Partially Covered | High |
| B6 | Add Feature To Product | No product setup followed by successful `add feature to product`. | Missing-product `add feature to product` covered by `test_8_postOnProductFeaturWithQueryParamCauses500_internalServerError`; duplicate feature not covered. | `list product features` and `retrieve product by name` not executed after add. | Partially Covered | High |
| B7 | Update Feature Description | No `update product feature description` call; no `PUT` requests in generated tests. | No documented update or duplicate setup failure is covered. | Feature-list and product-retrieve checks not executed. | Not Covered | High |
| B8 | Remove Feature From Product And Configurations | No feature activation setup followed by `delete product feature`. | Missing-product `delete product feature` and missing setup for `activate feature in configuration` covered; missing feature under existing product not covered. | Catalog and active-feature cleanup checks not executed. | Partially Covered | High |
| B9 | List Product Configurations | Endpoint called, but without required product creation or existing-product precondition. | `list product configurations` absent-product edge covered by `test_5_getOnConfigurationsReturnsEmptyList`. | Not applicable. | Partially Covered | Medium |
| B10 | Create Product Configuration | No create-product then successful `create product configuration` workflow. | Missing-product `create product configuration` covered by `test_7_postOnConfigurCauses500_internalServerError`. | Retrieve/list verification not executed. | Partially Covered | High |
| B11 | Retrieve Product Configuration | No create-product, create-configuration, then retrieve workflow. | Missing-configuration retrieval covered by `test_12_getOnConfigurReturns204`; missing-product setup for `create product configuration` covered. | Not applicable. | Partially Covered | Medium |
| B12 | Delete Product Configuration | No create-product, create-configuration, then delete workflow. | Missing configuration delete covered by `test_10_deleteOnConfigurCauses500_internalServerError`; missing-product setup for creation covered. | List-after-delete not executed. | Partially Covered | Medium |
| B13 | List Active Configuration Features | No activation setup followed by `list active configuration features`. | Missing configuration list and missing activation setup covered by `test_14_getOnConfigurFeaturesWithQueryParamCauses500_internalServerError` and `test_15_postOnConfigurFeaturCauses500_internalServerError`. | `retrieve product configuration` not executed. | Partially Covered | High |
| B14 | Activate Feature In Configuration | No product-feature-configuration setup before activation. | Missing-configuration activation covered; duplicate active feature, missing feature under existing product, excludes conflict, and duplicate feature setup not covered. | Active-feature list and configuration retrieval not executed. | Partially Covered | High |
| B15 | Deactivate Feature In Configuration | No active feature exists before deactivation. | Missing-configuration deactivation and activation-setup failure covered; missing feature under existing product not covered. | Active-feature list and configuration retrieval not executed. | Partially Covered | High |
| B16 | Add Requires Constraint | No product/features setup followed by successful `add requires constraint to product`. | Missing-product `add requires constraint to product` covered; duplicate feature setup not covered. | Product retrieval and derivation verification not executed. | Partially Covered | High |
| B17 | Add Excludes Constraint | No product/features setup followed by successful `add excludes constraint to product`. | Missing-product `add excludes constraint to product` covered; duplicate feature setup not covered. | Product retrieval and conflict verification not executed. | Partially Covered | High |
| B18 | Delete Product Constraint | Generated test deletes arbitrary id without creating/capturing a constraint id, so required binding is absent. | No-constraint-id delete covered with low confidence; missing-product `add requires constraint to product` covered; cross-product ownership not covered. | Product retrieval not executed. | Partially Covered | Low |
| B19 | Configure Variant With Required Feature Derivation | No setup for source/required features, requires constraint, configuration, and activation. | Missing-product `add requires constraint to product` and missing-product `create product configuration` covered; derivation-specific failures not covered. | Active-feature list and configuration retrieval not executed. | Partially Covered | High |
| B20 | Record Invalid Configuration From Excluded Feature Conflict | No excludes rule or conflicting two-step activation workflow. | Missing-product `add excludes constraint to product` and missing activation setup covered; actual excludes violation not covered. | Invalid-state retrieval/list verification not executed. | Partially Covered | High |
| B21 | Attempt To Deactivate A Required Feature | No requires derivation setup followed by attempted required-feature deletion. | Generic missing configuration for `attempt to deactivate required feature` covered; derivation failure not covered. | Active-feature list and configuration retrieval not executed. | Partially Covered | Medium |

## Behavior Details

### B1: List Products

- Business goal: Return the available product names in the catalog.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `list products` | `GET /products` with no request values | No | No generated test calls `GET /products`. | `ProductsResource.getAllProducts`, `ProductsService.getAllProductNames`, and `ProductsDAO.findAll` are missed in JaCoCo. |

- Happy-path item: Not Covered, because the only required read operation is absent.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| - | - | No optional verification workflow documented. | Not Applicable | - |

- Additional verification evidence: None.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `list products` | None visible; no domain-backed failure branch implemented. | Not Applicable | Excluded from failure-item denominator. | - |

- Coverage item summary: `0/1`.
- Status: Not Covered.
- Confidence: High.
- Gap: The generated suite never exercises global product discovery.
- Recommended tests: Call `GET /products` on an empty database and after creating products; assert that only product names are returned.

### B2: Create Product

- Business goal: Add a new configurable product to the catalog.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | Yes | `test_3_postOnProductReturns201` in both generated suites posts a product path and asserts `201` and empty body. | `ProductsService.add` is partially covered; JaCoCo does not corroborate the resource method or `ProductsDAO.insert`, so direct test evidence is primary. |

- Happy-path item: Covered, because the documented one-step creation operation is directly invoked with a path `productName`.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve product by name` | `GET /products/{productName}` for the created name | Not Executed | No read-after-write test reuses the created product name. |
| 2 | `list products` | `GET /products` | Not Executed | No generated test calls the global product list. |

- Additional verification evidence: The tests assert only status/body shape; no persisted-state assertion proves discoverability.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `create product` | No domain-level failure visible for duplicate names or missing body. | Not Applicable | Excluded from failure-item denominator. | - |

- Coverage item summary: `1/1`.
- Status: Covered.
- Confidence: Medium.
- Gap: Persistence and later discovery are not verified.
- Recommended tests: Create a product, retrieve it by name, and list all products to prove the created name is stored.

### B3: Retrieve Product Definition

- Business goal: Read a product's current feature catalog and constraints.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | Product creation is tested only as a separate one-request scenario; it is not followed by retrieval in the same reset state. | `ProductsService.add` partially covered. |
| 2 | `retrieve product by name` | `GET /products/{productName}` using the same name | No | `test_1_getOnProductCauses500_internalServerError` retrieves only a missing product. | `ProductsDAO.findByName` missing-object branch is covered; successful product serialization is not corroborated. |

- Happy-path item: Not Covered, because no test creates and retrieves the same product.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| - | - | No optional verification workflow documented. | Not Applicable | - |

- Additional verification evidence: No assertions inspect features or constraints on an existing product.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `retrieve product by name` | No product exists with `name={productName}`. | Covered | `test_1_getOnProductCauses500_internalServerError` calls `GET /products/{missing}` and asserts `500`. | `ProductsDAO.findByName` branch with `ObjectNotFoundException` is covered. |
| `create product` | No meaningful setup failure is visible. | Not Applicable | Excluded from failure-item denominator. | - |

- Coverage item summary: `1/2`.
- Status: Partially Covered.
- Confidence: High.
- Gap: Successful retrieval of an existing product is absent.
- Recommended tests: Create a product, add a feature and a constraint, retrieve it, and assert name, features, and constraints.

### B4: Retire Product And Dependent State

- Business goal: Remove a product and its dependent feature model, constraints, and configurations.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No dependent delete scenario starts by creating the product. | `ProductsService.add` partially covered only in isolated create test. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{featureName}` | No | Feature POST is attempted only with a missing product. | `ProductsService.addFeatureToProduct` and `ProductsDAO.insertFeature` are missed. |
| 3 | `create product configuration` | `POST /products/{productName}/configurations/{configurationName}` | No | Configuration POST is attempted only with a missing product. | `ProductsConfigurationsService.add` and DAO insert are missed. |
| 4 | `add requires constraint to product` | `POST /products/{productName}/constraints/requires` | No | Requires POST is attempted only with a missing product and empty form body. | Constraint creation methods are missed. |
| 5 | `delete product` | `DELETE /products/{productName}` | No | Product DELETE is attempted only for a missing product. | `ProductsService.deleteByName` is partially covered, but cleanup/delete DAO methods are missed. |

- Happy-path item: Not Covered, because no test builds dependent state and deletes it.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `list products` | Verify product name removed. | Not Executed | No `GET /products` test. |
| 2 | `retrieve product by name` | Verify absence after delete. | Not Executed | Missing-product retrieval is tested independently, not as post-delete verification. |

- Additional verification evidence: No cascading cleanup assertion exists.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `delete product` | No product exists with `name={productName}`. | Covered | `test_2_deleteOnProductCauses500_internalServerError` deletes a missing product. | `ProductsDAO.findByName` failure branch covered before actual delete. |
| `add feature to product` | Feature name already exists on the same product. | Not Covered | No product/feature setup or duplicate feature POST. | Duplicate branch and `DuplicatedObjectException` missed. |
| `create product configuration` | Product setup was skipped or used a different name. | Covered | `test_7_postOnConfigurCauses500_internalServerError` posts configuration under a missing product. | Missing product lookup covered. |
| `add requires constraint to product` | Product setup was skipped or used a different name. | Covered | `test_6_postOnRequiresCauses500_internalServerError` / `test_9_postOnRequiresCauses500_internalServerError` post requires under a missing product. | Missing product lookup covered; constraint insert missed. |

- Coverage item summary: `3/5`.
- Status: Partially Covered.
- Confidence: High.
- Gap: Successful aggregate deletion and dependent-state cleanup are untested.
- Recommended tests: Create product, feature, configuration, and requires constraint; delete product; assert product and dependent reads no longer find state.

### B5: List Product Features

- Business goal: Inspect the feature catalog for one product.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | Product creation is not followed by feature listing in one scenario. | `ProductsService.add` partially covered only in isolated create test. |
| 2 | `list product features` | `GET /products/{productName}/features` | No | `test_4_getOnProductFeaturesCauses500_internalServerError` uses a missing product. | `ProductsService.getFeaturesForProduct` is missed; missing lookup is covered in DAO. |

- Happy-path item: Not Covered, because the required existing-product precondition is absent.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| - | - | No optional verification workflow documented. | Not Applicable | - |

- Additional verification evidence: No feature array is asserted for an existing product.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `list product features` | No product exists with `name={productName}`. | Covered | `test_4_getOnProductFeaturesCauses500_internalServerError` calls `GET /products/{missing}/features` and asserts `500`. | `ProductsDAO.findByName` missing branch covered. |
| `create product` | No meaningful setup failure is visible. | Not Applicable | Excluded from failure-item denominator. | - |

- Coverage item summary: `1/2`.
- Status: Partially Covered.
- Confidence: High.
- Gap: Existing-product feature listing is missing.
- Recommended tests: Create a product, add two features, then assert `list product features` returns both with descriptions.

### B6: Add Feature To Product

- Business goal: Add a selectable feature to a product's feature catalog.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No continuous create-product then add-feature scenario. | `ProductsService.add` partially covered in isolated test. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{featureName}` with optional `description` | No | Feature POST is executed only for a missing product. | `ProductsService.addFeatureToProduct`, `Feature.withName`, and `ProductsDAO.insertFeature` are missed. |

- Happy-path item: Not Covered, because no existing product receives a feature.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `list product features` | Verify feature appears. | Not Executed | No successful list after add. |
| 2 | `retrieve product by name` | Inspect product model. | Not Executed | No successful product retrieval after add. |

- Additional verification evidence: No response location, stored name, or description is checked.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `add feature to product` | No product exists with `name={productName}`. | Covered | `test_8_postOnProductFeaturWithQueryParamCauses500_internalServerError` posts feature under a missing product. | Missing product lookup covered. |
| `add feature to product` | Feature already exists on the same product. | Not Covered | No duplicate feature setup. | `Product.hasFeatureNamed` and duplicate exception are missed. |
| `create product` | No meaningful setup failure is visible. | Not Applicable | Excluded from failure-item denominator. | - |

- Coverage item summary: `1/3`.
- Status: Partially Covered.
- Confidence: High.
- Gap: Successful feature creation and duplicate validation are missing.
- Recommended tests: Create a product, add a feature with description, list/retrieve product, and assert feature identity and description; add the same feature again and assert the documented duplicate failure.

### B7: Update Feature Description

- Business goal: Change descriptive metadata for a product feature.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No update scenario creates a product. | Isolated product creation only. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{featureName}` with initial description | No | No successful feature add. | `ProductsService.addFeatureToProduct` missed. |
| 3 | `update product feature description` | `PUT /products/{productName}/features/{featureName}` | No | No generated test issues a `PUT`. | `ProductsFeaturesResource.updateFeatureOfProduct` and `ProductsService.updateFeatureOfProduct` are missed. |

- Happy-path item: Not Covered, because the update operation is absent.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `list product features` | Inspect updated feature. | Not Executed | No successful feature list. |
| 2 | `retrieve product by name` | Inspect product model. | Not Executed | No successful product retrieve. |

- Additional verification evidence: None.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `update product feature description` | Product does not exist. | Not Covered | No `PUT` request. | Update resource/service missed. |
| `update product feature description` | Product exists but linked feature is missing. | Not Covered | No product setup and no `PUT`. | `Product.findProductFeatureByName` missed. |
| `add feature to product` | Setup feature already exists. | Not Covered | No duplicate setup. | Duplicate branch missed. |

- Coverage item summary: `0/4`.
- Status: Not Covered.
- Confidence: High.
- Gap: Entire description update behavior is absent.
- Recommended tests: Create product and feature, update description, retrieve/list feature, and assert only the description changed; add missing-product and missing-feature update tests.

### B8: Remove Feature From Product And Configurations

- Business goal: Retire a feature and remove it from configurations where it is active.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No delete-feature workflow setup. | Isolated product creation only. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{featureName}` | No | No successful feature add. | Feature insert missed. |
| 3 | `create product configuration` | `POST /products/{productName}/configurations/{configurationName}` | No | No successful configuration create. | Configuration insert missed. |
| 4 | `activate feature in configuration` | `POST /products/{productName}/configurations/{configurationName}/features/{featureName}` | No | Activation is attempted only without setup. | `ProductConfiguration.active` and evaluator are missed. |
| 5 | `delete product feature` | `DELETE /products/{productName}/features/{featureName}` | No | Delete feature is attempted only for a missing product. | `ProductsService.deleteFeatureOfProduct` and `ProductsDAO.deleteFeature` are missed. |

- Happy-path item: Not Covered, because no active feature is created before deletion.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `list product features` | Verify deleted feature is absent. | Not Executed | No successful delete workflow. |
| 2 | `list active configuration features` | Verify feature no longer active. | Not Executed | No successful activation or cleanup. |

- Additional verification evidence: None.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `delete product feature` | No product exists with `name={productName}`. | Covered | `test_11_deleteOnFeaturCauses500_internalServerError` / `test_11_deleteOnProductFeaturCauses500_internalServerError` delete feature under missing product. | Missing product lookup covered. |
| `delete product feature` | Product exists but linked feature is missing. | Not Covered | No existing-product/missing-feature setup. | `Product.findProductFeatureByName` missed. |
| `activate feature in configuration` | Feature or configuration setup is missing. | Covered | `test_15_postOnConfigurFeaturCauses500_internalServerError` activates without configuration setup. | `ProductsConfigurationsService.addFeatureFromConfiguration` failure entry is partially covered before successful activation logic. |

- Coverage item summary: `2/4`.
- Status: Partially Covered.
- Confidence: High.
- Gap: Successful deletion and active-feature cleanup are untested.
- Recommended tests: Create and activate a feature in a configuration, delete the feature, and assert it is absent from both the product catalog and active feature list.

### B9: List Product Configurations

- Business goal: See which named configurations exist for a product.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No list-configurations scenario creates the product. | Isolated product creation only. |
| 2 | `list product configurations` | `GET /products/{productName}/configurations` | No | `test_5_getOnConfigurationsReturnsEmptyList` calls the endpoint without creating the product. | `ProductsConfigurationsService.getConfigurationsNamesForProduct` and DAO query are covered for empty result. |

- Happy-path item: Not Covered, because the documented product-exists precondition is absent.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| - | - | No optional verification workflow documented. | Not Applicable | - |

- Additional verification evidence: The empty-list response is asserted, but it conflates absent product with existing product with no configurations.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `list product configurations` | No product exists with `name={productName}`; implementation returns empty list instead of failing. | Covered | `test_5_getOnConfigurationsReturnsEmptyList` calls the endpoint on an absent product and asserts `200` with empty array. | DAO join query returns no rows; covered. |
| `create product` | No meaningful setup failure is visible. | Not Applicable | Excluded from failure-item denominator. | - |

- Coverage item summary: `1/2`.
- Status: Partially Covered.
- Confidence: Medium.
- Gap: Existing-product configuration listing is absent.
- Recommended tests: Create a product and configurations, list configurations, and assert only names belonging to that product are returned.

### B10: Create Product Configuration

- Business goal: Create a named configuration for a product.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No create-configuration workflow starts with product creation. | Isolated product creation only. |
| 2 | `create product configuration` | `POST /products/{productName}/configurations/{configurationName}` | No | Configuration POST is executed only under a missing product. | `ProductsConfigurationsService.add` and DAO insert are missed. |

- Happy-path item: Not Covered, because no existing product receives a configuration.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve product configuration` | Inspect created configuration. | Not Executed | Only missing-configuration retrieval is tested. |
| 2 | `list product configurations` | Verify name appears. | Not Executed | Only absent-product empty listing is tested. |

- Additional verification evidence: None.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `create product configuration` | No product exists with `name={productName}`. | Covered | `test_7_postOnConfigurCauses500_internalServerError` posts configuration under a missing product. | Missing product lookup covered; configuration insert missed. |
| `create product` | No meaningful setup failure is visible. | Not Applicable | Excluded from failure-item denominator. | - |

- Coverage item summary: `1/2`.
- Status: Partially Covered.
- Confidence: High.
- Gap: Successful configuration creation is missing.
- Recommended tests: Create a product, create a configuration, retrieve it, and assert name, `valid=true`, and empty active features.

### B11: Retrieve Product Configuration

- Business goal: Inspect one configuration's active features and validity state.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No retrieve-configuration workflow creates a product. | Isolated product creation only. |
| 2 | `create product configuration` | `POST /products/{productName}/configurations/{configurationName}` | No | Configuration creation is attempted only under missing product. | `ProductsConfigurationsService.add` missed. |
| 3 | `retrieve product configuration` | `GET /products/{productName}/configurations/{configurationName}` | No | `test_12_getOnConfigurReturns204` retrieves only a missing configuration. | DAO lookup method is covered; successful serialization is not. |

- Happy-path item: Not Covered, because no existing configuration is retrieved.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| - | - | No optional verification workflow documented. | Not Applicable | - |

- Additional verification evidence: No assertions cover `valid` or active features.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `retrieve product configuration` | No configuration exists with `configurationName={configurationName}` for `productName={productName}`. | Covered | `test_12_getOnConfigurReturns204` calls `GET /products/{name}/configurations/{missing}` and asserts `204`/empty body. | DAO lookup path covered; source has no explicit not-found mapping. |
| `create product configuration` | Product does not exist. | Covered | `test_7_postOnConfigurCauses500_internalServerError` covers missing-product configuration creation. | Missing product lookup covered. |

- Coverage item summary: `2/3`.
- Status: Partially Covered.
- Confidence: Medium.
- Gap: Successful retrieval of a stored configuration is absent.
- Recommended tests: Create product and configuration, retrieve it, and assert name, validity, product scope, and active feature set.

### B12: Delete Product Configuration

- Business goal: Remove a named product configuration.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No delete-configuration workflow creates the product. | Isolated product creation only. |
| 2 | `create product configuration` | `POST /products/{productName}/configurations/{configurationName}` | No | No successful configuration creation. | Configuration insert missed. |
| 3 | `delete product configuration` | `DELETE /products/{productName}/configurations/{configurationName}` | No | Delete is attempted only for missing state. | DAO removal failure path covered; successful removal not corroborated. |

- Happy-path item: Not Covered, because no matching configuration exists before deletion.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `list product configurations` | Verify deleted name is absent. | Not Executed | No successful delete workflow. |

- Additional verification evidence: None.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `delete product configuration` | No matching configuration exists under the product. | Covered | `test_10_deleteOnConfigurCauses500_internalServerError` deletes a non-existent configuration and asserts `500`. | `ProductsConfigurationsDAO.deleteConfigurationForProduct` failure path is covered. |
| `create product configuration` | Product setup was omitted. | Covered | `test_7_postOnConfigurCauses500_internalServerError`. | Missing product lookup covered. |

- Coverage item summary: `2/3`.
- Status: Partially Covered.
- Confidence: Medium.
- Gap: Successful deletion and absence verification are missing.
- Recommended tests: Create a configuration, delete it, then list/retrieve configurations to prove it is gone.

### B13: List Active Configuration Features

- Business goal: Inspect the feature selections currently active in a configuration.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No active-feature listing workflow creates product. | Isolated product creation only. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{featureName}` | No | No successful feature add. | Feature creation missed. |
| 3 | `create product configuration` | `POST /products/{productName}/configurations/{configurationName}` | No | No successful configuration create. | Configuration creation missed. |
| 4 | `activate feature in configuration` | `POST /products/{productName}/configurations/{configurationName}/features/{featureName}` | No | Activation attempted only without setup. | `ProductConfiguration.active` and evaluator missed. |
| 5 | `list active configuration features` | `GET /products/{productName}/configurations/{configurationName}/features` | No | Active-feature list is attempted only for missing configuration. | `ProductsConfigurationsService.getConfigurationActivedFeaturesNames` partially covered up to null dereference; successful feature iteration not covered. |

- Happy-path item: Not Covered, because no active feature relation exists before listing.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve product configuration` | Inspect active feature objects and validity. | Not Executed | Only missing configuration retrieval is tested. |

- Additional verification evidence: None.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `list active configuration features` | No matching configuration exists. | Covered | `test_14_getOnConfigurFeaturesWithQueryParamCauses500_internalServerError` calls active-feature list without configuration setup. | Service method failure path covered. |
| `activate feature in configuration` | Feature does not belong to product or configuration is missing. | Covered | `test_15_postOnConfigurFeaturCauses500_internalServerError` activates without matching configuration. | Activation service method partially covered before real activation logic. |

- Coverage item summary: `2/3`.
- Status: Partially Covered.
- Confidence: High.
- Gap: Successful active-feature listing is missing.
- Recommended tests: Create product, feature, and configuration; activate feature; assert active-feature list contains exactly that feature.

### B14: Activate Feature In Configuration

- Business goal: Select a product feature for a specific product configuration.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No activation workflow creates product. | Isolated product creation only. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{featureName}` | No | No successful feature add. | Feature creation missed. |
| 3 | `create product configuration` | `POST /products/{productName}/configurations/{configurationName}` | No | No successful configuration create. | Configuration creation missed. |
| 4 | `activate feature in configuration` | `POST /products/{productName}/configurations/{configurationName}/features/{featureName}` | No | Activation is attempted only without setup. | `ProductConfiguration.active`, `ConfigurationEvaluator`, and constraint evaluation are missed. |

- Happy-path item: Not Covered, because no feature and configuration are prepared under the same product.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `list active configuration features` | Verify activation. | Not Executed | Only missing configuration list is tested. |
| 2 | `retrieve product configuration` | Inspect `valid`. | Not Executed | Only missing configuration retrieval is tested. |

- Additional verification evidence: None for persistence or validity.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `activate feature in configuration` | Feature is already active. | Not Covered | No active-feature setup or duplicate activation. | `configuration.hasActiveFeature` duplicate branch missed. |
| `activate feature in configuration` | `featureName` is not a feature of the product. | Not Covered | No existing product/configuration with missing feature. | `Product.findProductFeatureByName` missed. |
| `activate feature in configuration` | No matching configuration exists. | Covered | `test_15_postOnConfigurFeaturCauses500_internalServerError` posts activation for missing configuration and asserts `500`. | Activation method partially covered before successful activation. |
| `activate feature in configuration` | Activation creates an excludes violation. | Not Covered | No excludes constraint or conflicting activation setup. | `ConstraintExcludes.evaluateConfiguration` and wrong-configuration exception are missed. |
| `add feature to product` | Setup feature already exists. | Not Covered | No duplicate feature setup. | Duplicate feature branch missed. |

- Coverage item summary: `1/6`.
- Status: Partially Covered.
- Confidence: High.
- Gap: Successful activation and business-rule validation are mostly absent.
- Recommended tests: Create product, feature, and configuration; activate feature; assert active list and `valid=true`; add duplicate, missing-feature, and excludes-conflict tests.

### B15: Deactivate Feature In Configuration

- Business goal: Remove a selected feature from a configuration.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No deactivation workflow creates product. | Isolated product creation only. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{featureName}` | No | No successful feature add. | Feature creation missed. |
| 3 | `create product configuration` | `POST /products/{productName}/configurations/{configurationName}` | No | No successful configuration create. | Configuration creation missed. |
| 4 | `activate feature in configuration` | `POST /products/{productName}/configurations/{configurationName}/features/{featureName}` | No | No successful activation setup. | Activation success missed. |
| 5 | `deactivate feature in configuration` | `DELETE /products/{productName}/configurations/{configurationName}/features/{featureName}` | No | Deactivation attempted only without matching configuration. | `ProductConfiguration.deactive` and evaluator update are missed. |

- Happy-path item: Not Covered, because the active feature relation is never created.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `list active configuration features` | Verify feature absent. | Not Executed | No successful deactivation. |
| 2 | `retrieve product configuration` | Inspect validity. | Not Executed | No successful configuration retrieval. |

- Additional verification evidence: None.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `deactivate feature in configuration` | Feature does not exist on the product. | Not Covered | No existing product/configuration with missing feature. | Product feature lookup missed. |
| `deactivate feature in configuration` | Configuration does not exist. | Covered | `test_16_deleteOnFeaturCauses500_internalServerErrorUsingSql` / `test_16_deleteOnConfigurFeaturCauses500_internalServerError` deletes active feature under missing configuration. | Deactivation service method partially covered before real deactivation. |
| `activate feature in configuration` | Setup activation fails because feature/configuration is missing, already active, or constraints invalidate it. | Covered | `test_15_postOnConfigurFeaturCauses500_internalServerError` covers activation setup failure due missing configuration. | Activation failure path partially covered. |

- Coverage item summary: `2/4`.
- Status: Partially Covered.
- Confidence: High.
- Gap: Successful deactivation and validity reevaluation are missing.
- Recommended tests: Activate a feature, deactivate it, then assert active-feature list is empty and configuration remains valid.

### B16: Add Requires Constraint

- Business goal: Define a rule that selecting one feature automatically requires another feature.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No constraint workflow creates product. | Isolated product creation only. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{sourceFeature}` | No | No successful source feature add. | Feature creation missed. |
| 3 | `add feature to product` | `POST /products/{productName}/features/{requiredFeature}` | No | No successful required feature add. | Feature creation missed. |
| 4 | `add requires constraint to product` | `POST /products/{productName}/constraints/requires` with `sourceFeature` and `requiredFeature` | No | Requires POST is executed only for a missing product with empty form body. | `ProductsService.addRequiresConstraintToProduct`, `ConstraintRequires(String,String)`, and constraint insert are missed. |

- Happy-path item: Not Covered, because no successful requires rule is created with bound feature names.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve product by name` | Inspect product constraints. | Not Executed | No successful product retrieval. |
| 2 | `activate feature and derive required feature` | Verify derivation after configuration setup. | Not Executed | No derivation scenario. |

- Additional verification evidence: None for stored source/required names.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `add requires constraint to product` | No product exists with `name={productName}`. | Covered | `test_6_postOnRequiresCauses500_internalServerError` / `test_9_postOnRequiresCauses500_internalServerError` post requires under a missing product. | Missing product lookup covered; constraint creation missed. |
| `add feature to product` | Source or required feature setup duplicates an existing feature name. | Not Covered | No duplicate feature setup. | Duplicate branch missed. |

- Coverage item summary: `1/3`.
- Status: Partially Covered.
- Confidence: High.
- Gap: Successful requires constraint creation and derivation are missing.
- Recommended tests: Create product, source feature, and required feature; add requires constraint with form values; retrieve product and assert rule data.

### B17: Add Excludes Constraint

- Business goal: Define a rule that two features must not be active together.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No excludes workflow creates product. | Isolated product creation only. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{sourceFeature}` | No | No successful source feature add. | Feature creation missed. |
| 3 | `add feature to product` | `POST /products/{productName}/features/{excludedFeature}` | No | No successful excluded feature add. | Feature creation missed. |
| 4 | `add excludes constraint to product` | `POST /products/{productName}/constraints/excludes` with `sourceFeature` and `excludedFeature` | No | Excludes POST is executed only for a missing product with empty form body. | `ProductsService.addExcludesConstraintToProduct`, `ConstraintExcludes(String,String)`, and constraint insert are missed. |

- Happy-path item: Not Covered, because no successful excludes rule is created with bound feature names.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve product by name` | Inspect product constraints. | Not Executed | No successful product retrieval. |
| 2 | `activate feature in configuration` | Observe invalidation after conflicting activation. | Not Executed | No conflict scenario. |

- Additional verification evidence: None.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `add excludes constraint to product` | No product exists with `name={productName}`. | Covered | `test_9_postOnExcludesCauses500_internalServerError` / `test_6_postOnExcludesCauses500_internalServerError` post excludes under a missing product. | Missing product lookup covered; constraint creation missed. |
| `add feature to product` | Source or excluded feature setup duplicates an existing feature name. | Not Covered | No duplicate feature setup. | Duplicate branch missed. |

- Coverage item summary: `1/3`.
- Status: Partially Covered.
- Confidence: High.
- Gap: Successful excludes constraint creation and conflict evaluation are missing.
- Recommended tests: Create product and two features, add excludes constraint with correct `excludedFeature` form key, retrieve product, and then test conflicting activation.

### B18: Delete Product Constraint

- Business goal: Remove a stored product constraint.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No constraint-deletion workflow creates product. | Isolated product creation only. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{sourceFeature}` | No | No successful feature add. | Feature creation missed. |
| 3 | `add feature to product` | `POST /products/{productName}/features/{targetFeature}` | No | No successful target feature add. | Feature creation missed. |
| 4 | `add requires constraint to product` | `POST /products/{productName}/constraints/requires` | No | Requires creation is attempted only for a missing product. | Constraint creation missed. |
| 5 | Capture `{constraintId}` | Use generated id or response `Location`. | No | No test captures a generated constraint id. | Not represented in code coverage. |
| 6 | `delete product constraint` | `DELETE /products/{productName}/constraints/{constraintId}` | No | `test_13_deleteOnConstraintReturns204` deletes arbitrary id `189` without setup. | `ProductsService.deleteConstraintFromProduct` and `ProductsDAO.deleteConstraintForProduct` are covered. |

- Happy-path item: Not Covered, because the generated id binding and existing constraint precondition are absent.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve product by name` | Inspect remaining constraints. | Not Executed | No successful product retrieval. |

- Additional verification evidence: No state check proves a constraint row was removed.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `delete product constraint` | `{constraintId}` belongs to a different product than `productName`. | Not Covered | No two-product setup or cross-product id reuse. | Product ownership is not checked in source. |
| `delete product constraint` | No constraint exists with `{constraintId}`. | Covered | `test_13_deleteOnConstraintReturns204` deletes arbitrary id `189` with no created constraint and asserts `204`. | Id-only delete query covered; zero-row behavior returns no content. |
| `add requires constraint to product` | Product setup was omitted. | Covered | Requires POST under missing product is covered by generated tests. | Missing product lookup covered. |

- Coverage item summary: `2/4`.
- Status: Partially Covered.
- Confidence: Low.
- Gap: Real constraint deletion and product-scoped ownership behavior are untested.
- Recommended tests: Add a requires constraint, capture the returned id/location, delete that id, retrieve product, and assert the constraint is gone; separately test cross-product id misuse.

### B19: Configure Variant With Required Feature Derivation

- Business goal: Activate a source feature and automatically include a required dependent feature.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No derivation workflow creates product. | Isolated product creation only. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{sourceFeature}` | No | No successful source feature add. | Feature creation missed. |
| 3 | `add feature to product` | `POST /products/{productName}/features/{requiredFeature}` | No | No successful required feature add. | Feature creation missed. |
| 4 | `add requires constraint to product` | `POST /products/{productName}/constraints/requires` | No | Requires POST only under missing product. | Requires constraint constructor and insert missed. |
| 5 | `create product configuration` | `POST /products/{productName}/configurations/{configurationName}` | No | Configuration POST only under missing product. | Configuration insert missed. |
| 6 | `activate feature and derive required feature` | `POST /products/{productName}/configurations/{configurationName}/features/{sourceFeature}` | No | No source activation with a requires rule exists. | `ConfigurationEvaluator`, `ConstraintRequires.evaluateConfiguration`, and derived activation are missed. |

- Happy-path item: Not Covered, because the dependency setup and derivation activation are absent.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `list active configuration features` | Verify source and required features active. | Not Executed | No successful active-feature list. |
| 2 | `retrieve product configuration` | Verify `valid=true`. | Not Executed | No successful configuration retrieval. |

- Additional verification evidence: None.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `activate feature and derive required feature` | Requires constraint references a required feature that does not exist on the product. | Not Covered | No invalid requires rule plus activation setup. | Derivation path missed. |
| `activate feature and derive required feature` | Source feature is already active. | Not Covered | No duplicate source activation setup. | Duplicate active branch missed. |
| `add requires constraint to product` | Product setup is missing. | Covered | Requires POST under missing product is covered. | Missing product lookup covered. |
| `create product configuration` | Product setup is missing. | Covered | Configuration POST under missing product is covered. | Missing product lookup covered. |

- Coverage item summary: `2/5`.
- Status: Partially Covered.
- Confidence: High.
- Gap: Constraint-driven derivation behavior is absent.
- Recommended tests: Add requires rule `source -> required`, create configuration, activate source, and assert both features are active and configuration remains valid.

### B20: Record Invalid Configuration From Excluded Feature Conflict

- Business goal: Detect that a configuration violates an excludes rule when incompatible features are active together.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No excludes conflict workflow creates product. | Isolated product creation only. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{sourceFeature}` | No | No successful source feature add. | Feature creation missed. |
| 3 | `add feature to product` | `POST /products/{productName}/features/{excludedFeature}` | No | No successful excluded feature add. | Feature creation missed. |
| 4 | `add excludes constraint to product` | `POST /products/{productName}/constraints/excludes` | No | Excludes POST only under missing product. | Excludes constraint constructor and insert missed. |
| 5 | `create product configuration` | `POST /products/{productName}/configurations/{configurationName}` | No | Configuration POST only under missing product. | Configuration insert missed. |
| 6 | `activate feature in configuration` | Activate `{sourceFeature}` | No | Activation only attempted without setup. | Successful activation missed. |
| 7 | `activate feature in configuration` | Attempt activating `{excludedFeature}` under excludes rule | No | No conflicting activation setup. | `ConstraintExcludes.evaluateConfiguration` and `WrongProductConfigurationException` are missed. |

- Happy-path item: Not Covered, because no conflict scenario is built.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve product configuration` | Inspect `valid=false`. | Not Executed | No invalid configuration retrieval. |
| 2 | `list active configuration features` | Inspect both active conflicting features. | Not Executed | No conflict active-feature listing. |

- Additional verification evidence: None for error response or persisted invalid state.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `activate feature in configuration` | Excluded feature is activated while source feature is already active under an excludes constraint. | Not Covered | No excludes rule or source active-feature setup. | Excludes evaluator missed. |
| `add excludes constraint to product` | Product setup is missing. | Covered | Excludes POST under missing product is covered. | Missing product lookup covered. |
| `activate feature in configuration` | Either feature is missing from the product or the configuration is missing. | Covered | `test_15_postOnConfigurFeaturCauses500_internalServerError` activates without matching configuration. | Activation failure path partially covered. |

- Coverage item summary: `2/4`.
- Status: Partially Covered.
- Confidence: High.
- Gap: The central excludes-conflict business rule is untested.
- Recommended tests: Add excludes rule, activate source successfully, activate excluded and expect documented error, then retrieve configuration and assert `valid=false` and active features match documented implementation behavior.

### B21: Attempt To Deactivate A Required Feature

- Business goal: Keep a required feature active when its source feature remains active.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create product` | `POST /products/{productName}` | No | No required-feature deactivation workflow creates product. | Isolated product creation only. |
| 2 | `add feature to product` | `POST /products/{productName}/features/{sourceFeature}` | No | No successful source feature add. | Feature creation missed. |
| 3 | `add feature to product` | `POST /products/{productName}/features/{requiredFeature}` | No | No successful required feature add. | Feature creation missed. |
| 4 | `add requires constraint to product` | `POST /products/{productName}/constraints/requires` | No | Requires POST only under missing product. | Requires creation missed. |
| 5 | `create product configuration` | `POST /products/{productName}/configurations/{configurationName}` | No | No successful configuration create. | Configuration creation missed. |
| 6 | `activate feature and derive required feature` | Activate source and derive required feature | No | No derivation setup. | Required-feature derivation missed. |
| 7 | `attempt to deactivate required feature` | `DELETE /products/{productName}/configurations/{configurationName}/features/{requiredFeature}` | No | Deactivation attempted only without matching configuration. | Deactivation reevaluation and restoration path missed. |

- Happy-path item: Not Covered, because no required-feature derivation exists before attempted removal.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `list active configuration features` | Verify required feature remains active. | Not Executed | No derivation/deactivation workflow. |
| 2 | `retrieve product configuration` | Verify `valid=true`. | Not Executed | No successful configuration retrieval. |

- Additional verification evidence: None.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `attempt to deactivate required feature` | Missing product feature or missing configuration. | Covered | `test_16_deleteOnFeaturCauses500_internalServerErrorUsingSql` / `test_16_deleteOnConfigurFeaturCauses500_internalServerError` delete active feature under missing configuration. | Deactivation failure path partially covered. |
| `activate feature and derive required feature` | Required feature is missing or source feature already active. | Not Covered | No requires derivation setup. | Derivation and duplicate source paths missed. |

- Coverage item summary: `1/3`.
- Status: Partially Covered.
- Confidence: Medium.
- Gap: Dependency-preserving deactivation is untested.
- Recommended tests: Build `source -> required`, activate source to derive required, delete required, then assert the required feature is still active and configuration remains valid.

## Cross-Behavior Gaps

- Generated tests reset the database before every test and usually perform one request, so multi-step required workflows are almost completely absent.
- Most assertions stop at HTTP status, content type, empty body, or empty array size; they rarely assert persisted business state.
- Direct database setup is not used to satisfy documented preconditions, and API setup is not chained within continuous scenarios.
- Successful feature, configuration, active-feature, and constraint lifecycles are missing.
- Constraint behavior is untested: requires derivation, required-feature restoration, excludes invalidation, duplicate active-feature handling, and missing required feature during derivation.
- Duplicate and ownership edge cases are missing, including duplicate features, duplicate active features, update/delete missing feature under an existing product, and cross-product constraint deletion.
- JaCoCo shows low coverage in domain-core methods: `ProductConfiguration.active/deactive`, `ConfigurationEvaluator.evaluateConfiguration`, `ConstraintRequires.evaluateConfiguration`, and `ConstraintExcludes.evaluateConfiguration` are missed.
- Some code is executed without proving business behavior, especially absent-product configuration listing and arbitrary no-op constraint deletion.
- JaCoCo does not always corroborate direct endpoint evidence from generated tests, so behavior scoring uses test calls as primary evidence and coverage only as a supporting signal.

## Suggested Additional Tests

1. Target B2, B3, B1: Product create and discovery. Minimal setup: empty database. Calls: `POST /products/Phone`, `GET /products/Phone`, `GET /products`. Assertions: `201`, returned product name `Phone`, empty feature/constraint sets, product list contains `Phone`. Type: success/regression.
2. Target B6, B5, B7: Feature lifecycle. Minimal setup: create product. Calls: add feature with description, list features, update description, retrieve/list again. Assertions: feature name unchanged, description updated, no duplicate rows. Type: success/regression.
3. Target B10, B11, B12: Configuration lifecycle. Minimal setup: create product. Calls: create configuration, retrieve, list, delete, list/retrieve. Assertions: `valid=true`, empty active features, name present then absent. Type: success/regression.
4. Target B14, B13, B15: Active feature lifecycle. Minimal setup: product, feature, configuration. Calls: activate feature, list active, retrieve configuration, deactivate feature, list active. Assertions: feature appears, then disappears; validity remains true. Type: success/regression.
5. Target B16, B19, B21: Requires rule behavior. Minimal setup: product with source and required features. Calls: add requires constraint, create configuration, activate source, list active, delete required, list active. Assertions: both features active after source activation; required remains active after deletion attempt; `valid=true`. Type: success/edge.
6. Target B17, B20: Excludes rule behavior. Minimal setup: product with two features and excludes constraint. Calls: create configuration, activate source, activate excluded, retrieve configuration/list active. Assertions: documented error on conflict, `valid=false`, and persisted active features match implementation notes. Type: failure/edge.
7. Target B18: Real constraint deletion and scoping. Minimal setup: two products and a constraint on product A. Calls: delete with product A/id, then in a separate test attempt product B/id. Assertions: correct deletion removes only expected constraint; cross-product behavior is either fixed or documented. Type: success/security regression.
8. Target B6, B14, B7, B8: Negative ownership and duplicate checks. Minimal setup: products, features, configuration as needed. Calls: duplicate feature add, duplicate active-feature activation, update/delete missing feature under existing product, activate feature from another product. Assertions: documented failures and no unintended state changes. Type: failure/regression.

## Notes And Assumptions

- `business-behavior.md` was treated as the authoritative behavior list; `full-behavior.md` was used to normalize endpoint/function mapping and implementation branches.
- Generated tests under `tests/` were analyzed together as one generated-test corpus. Existing hand-written tests under `src/test/java` were not counted as generated-test evidence.
- I did not rerun tests; I used the supplied JaCoCo XML reports and static generated-test evidence.
- The two JaCoCo XML reports have identical aggregate counters, so the combined union equals either report for headline line, branch, and method percentages.
- The 9 `Unsupported or Missing Business Behaviors` in `business-behavior.md` were treated as product/API gaps, not as implemented behaviors in the 21-behavior denominator.
- Documentation and implementation disagree for `list product configurations`: the domain precondition says the product should exist, while the implementation returns an empty list for an absent product.
- Documentation and implementation disagree for `delete product constraint`: the URL is product-scoped, but `ProductsDAO.deleteConstraintForProduct` deletes by `constraintId` only and ignores `productName`.
- Some generated missing-configuration tests omit the parent product too. They were counted only where the observed failing function and missing-state condition directly matched the documented failure item closely enough to be traceable; confidence is lower where the setup is less precise.
