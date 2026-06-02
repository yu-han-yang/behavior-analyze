# Business Behavior Coverage Report

## Executive Summary

- Project under analysis: `/Users/yangyuhan/behavior-analyze/gestaohospital`
- Test suites analyzed: `tests/EM_gestaohospital_True_6_Test.java` (140 EvoMaster-generated tests)
- Coverage reports analyzed: `coverage/evomaster_10170_gestaohospital__10171/report.xml`, `coverage/evomaster_10180_gestaohospital__10181/report.xml`
- Source analyzed: `src/main/java`
- Total documented business behaviors: 14
- Covered: 5
- Partially covered: 9
- Not covered: 0
- Unclear: 0
- Business behavior coverage: 67.9%
- Combined JaCoCo coverage signal: 54.6% lines, 13.7% branches, 59.3% methods
- Failure/exceptional-case coverage: 75.0% (24/32 documented failure items)
- Successful required-workflow coverage: 35.7% (5/14 behaviors)
- Behavior checklist coverage: 29/46 coverage items covered

The generated suite is strongest around hospital registration, profile update, deletion, capacity lookup, and direct patient demographic update. The largest gaps are end-to-end patient admission/discharge, stock CRUD, transfer/refusal, and proximity workflows. Many generated tests are useful negative-path probes, but they often stop at status-code assertions or use direct database setup instead of exercising the documented business workflow.

The combined JaCoCo signal was computed as a union over XML source-file line entries and method identities. Line and method union is exact for the available XML. Branch union is conservative because JaCoCo XML reports covered branch counts per source line, not branch identities.

## Coverage Matrix

| ID | Business Behavior | Required Workflow Function Coverage | Failing Function Coverage | Optional Verification Coverage | Status | Confidence |
|---|---|---|---|---|---|---|
| B1 | Register A Hospital | `create hospital` covered by 200 `POST /v1/hospitais/` in tests 30, 37, 43, 71, 72, 109, 110. | Invalid body covered by many 400 POSTs; non-null body `id` covered by test 3. | `retrieve hospital by id` executed in test 42 and via `resolveLocation`; `list hospitals` executed in tests 13, 14, 104. | Covered | High |
| B2 | Maintain Hospital Profile | `create hospital` then `update hospital` covered by test 72 using created id through `resolveLocation`. | Invalid create covered; missing target covered by tests 34/36; invalid update body covered by test 43. | Direct retrieve covered in test 42; bed lookup covered in tests 16, 34, 37. | Covered | Medium |
| B3 | Copy Stored Data From One Hospital To Another | No successful source-target copy found. | Non-null body `id` failures covered by tests 32, 33, 41, but target-missing copy failure is not isolated. | No target-after-copy verification. | Partially Covered | Low |
| B4 | Remove A Hospital | Direct DB setup plus `delete hospital` covered by tests 6, 46, 47, 48. | Unknown hospital delete covered by tests 56, 58, 59. | Some list/retrieve endpoints are exercised separately, not as post-delete verification. | Covered | High |
| B5 | Inspect Hospital Capacity | Direct DB setup plus `get available beds` covered by tests 16, 34, 37. | Unknown id for `get available beds` covered by tests 9, 10, 81, 116. | `retrieve hospital by id` covered separately in test 42. | Covered | High |
| B6 | Find A Nearby Hospital With Available Beds | No successful `find nearest hospital with beds` response found. | Invalid candidate creation covered; `GET /maisProximo` failure probes covered by tests 27-31 and others, but JaCoCo method corroboration is weak. | `get available beds` executed separately; returned-hospital retrieval not shown. | Partially Covered | Low |
| B7 | Manage Hospital Stock Product | Happy CRUD chain is absent: no successful add/update/delete stock product. | Add unknown hospital covered by test 98; retrieve unknown product by tests 1, 6, 129, 131; update unknown product by test 116; delete product with unknown hospital by tests 117/120. | `retrieve stock product` covered by test 107; `list hospital stock` covered by tests 70 and 73. | Partially Covered | Medium |
| B8 | Transfer Product From A Nearby Hospital | No successful transfer. | Missing destination hospital covered by transfer 400s in tests 37, 62, 66, 67, 113. Missing product and no nearby source are not isolated. | No source/destination stock verification. | Partially Covered | Low |
| B9 | Refuse Product Transfer When Source Stock Is Too Low | No successful business refusal response found. | Missing destination hospital covered by same transfer 400s; no-source failure not isolated. | No unchanged-source verification. | Partially Covered | Low |
| B10 | Explore Nearby Hospitals And Locations | No successful `list nearby locations` or `list nearby hospitals` workflow found. | Unknown origin for `list nearby locations` covered by tests 63, 80, 91-94, 109, 111, 112. Missing `raio` and origin-without-location are not covered. | Nearby hospital retrieval not executed after proximity response. | Partially Covered | Medium |
| B11 | Admit A Patient | No successful `check in patient`. | Unknown hospital check-in covered by tests 114 and 137. Full-hospital check-in is not covered. | Patient list, patient retrieve, and bed lookup are exercised separately. | Partially Covered | Medium |
| B12 | Discharge A Checked-In Patient | No create/check-in/checkout workflow. | Checkout with patient not in hospital list covered by tests 5, 16, 21, 34, 37; unknown hospital checkout covered by tests 0, 3, 20, 136. Full-hospital setup failure is not covered. | Patient retrieve/list and bed lookup are separate, not after checkout. | Partially Covered | Medium |
| B13 | Maintain Patient Demographics | Direct DB patient setup plus `update patient` covered by test 16; shortcut is documented. | Unknown patient update covered by tests 25, 104, 138, 139; setup check-in unknown hospital covered by tests 114/137. | `retrieve patient` covered by test 108; `list hospital patients` covered by tests 32, 69. | Covered | Medium |
| B14 | View Hospital Patients | Individual `list hospital patients` and `retrieve patient` successes exist, but not in one continuous list-then-retrieve scenario. | Unknown hospital list covered by tests 99-103; unknown patient retrieve covered by tests 21, 22, 129-135. | Not applicable. | Partially Covered | Medium |

## Behavior Details

### B1: Register A Hospital

- Business goal: Create a reusable hospital record and associated hospital location.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | `POST /v1/hospitais/` with valid hospital body and omitted/null `id` | Yes | Tests 30, 37, 43, 71, 72, 109, 110 return 200 and assert returned hospital fields. | `HospitalResource.insert`, `HospitalService.fromDTO`, `HospitalService.insert`, and `LocationService.insertLocationByHospital` covered in union. |

- Happy-path item: Covered, because the creation function is directly exercised with valid non-empty hospital data and persisted location evidence.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve hospital by id` | `GET /v1/hospitais/{hospital_id}` | Executed | Test 42 retrieves a DB-created hospital; several `resolveLocation` calls inspect API-created hospitals. |
| 2 | `list hospitals` | `GET /v1/hospitais/` | Executed | Tests 13, 14, 104 list hospitals. |

- Additional verification evidence: Creation tests assert name, address, bed counts, and geocoded latitude/longitude.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `create hospital` | Empty `name`, empty `address`, negative `beds`, or negative `availableBeds` | Covered | Multiple 400 POSTs, including tests 0, 8, 22, 23, 26. | Controller validation branch covered. |
| `create hospital` | Body `id` is non-null | Covered | Test 3 posts non-null `id` with otherwise valid-looking fields and receives 400. | `HospitalService.fromDTO` lookup path covered. |

- Coverage item summary: 3/3 items covered.
- Status: Covered.
- Confidence: High.
- Gap: None for the documented coverage items.
- Recommended tests: Add a curated create test with exact documented values and explicit follow-up retrieve/list assertions.

### B2: Maintain Hospital Profile

- Business goal: Read and update hospital profile data.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | `POST /v1/hospitais/` | Yes | Test 72 creates a hospital and extracts the id. | Create path covered. |
| 2 | `update hospital` | `PUT /v1/hospitais/{hospital_id}` with null/omitted body `id` | Yes | Test 72 uses `resolveLocation(location_hospitais__2, ...)`, returns 200, and asserts updated fields. | `HospitalResource.update`, `HospitalService.update`, and `HospitalService.updateData` covered. |

- Happy-path item: Covered, because the test creates a hospital and updates the same hospital id in order.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve hospital by id` | Inspect updated fields | Executed | Direct retrieve covered by test 42; not specifically after test 72 update. |
| 2 | `get available beds` | Verify updated available bed count | Executed | Bed lookup covered by tests 16, 34, 37; not specifically after update. |

- Additional verification evidence: Test 72 asserts updated name, address, beds, availableBeds, and coordinates.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `create hospital` | Invalid creation body | Covered | Many 400 POSTs. | Create validation branch covered. |
| `update hospital` | `{hospital_id}` does not exist | Covered | Test 34 sends a valid-looking update body to unknown `_EM_2068_XYZ_` and receives 400. | `HospitalService.findById` exception path covered. |
| `update hospital` | Empty name/address or negative bed counts | Covered | Test 43 updates a created hospital with negative beds and receives 400. | Update validation branch covered. |

- Coverage item summary: 4/4 items covered.
- Status: Covered.
- Confidence: Medium.
- Gap: Optional verification is not tied to the update scenario.
- Recommended tests: Add a stable create-update-retrieve-bed-count scenario with documented values.

### B3: Copy Stored Data From One Hospital To Another

- Business goal: Overwrite a target hospital profile using another hospital's stored data.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | Create source hospital | No | Create is covered elsewhere, but not as source setup in a copy scenario. | Create path covered generally. |
| 2 | `create hospital` | Create target hospital | No | No two-hospital source/target copy scenario found. | Create path covered generally. |
| 3 | `copy hospital data` | `PUT /v1/hospitais/{target}` with body `id={source}` | No | No successful 200 copy with distinct source and target ids. | Non-null-id update failures covered; successful copy effect not shown. |

- Happy-path item: Not Covered, because no test copies source values into a distinct target.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve hospital by id` | Verify target now has source values | Not Executed | No post-copy verification exists. |

- Additional verification evidence: None.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `copy hospital data` | Source body `id` does not exist | Covered | Tests 32, 33, 41 use non-null body ids that are not seeded and receive 400. | `HospitalService.fromDTO` lookup failure covered. |
| `copy hospital data` | Target path `{hospital_id}` does not exist | Not Covered | No test uses an existing source body id with a missing target path id. | Target lookup path covered for normal update, not copy-specific. |

- Coverage item summary: 1/3 items covered.
- Status: Partially Covered.
- Confidence: Low.
- Gap: No positive copy scenario and no isolated missing-target copy failure.
- Recommended tests: Create two hospitals, PUT target with source id, assert target scalar fields copied and target id preserved; then add missing-source and missing-target variants.

### B4: Remove A Hospital

- Business goal: Delete a hospital from the registry.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | Establish target hospital | Yes | Direct DB setup supplies existing targets in tests 6, 46, 47, 48, which is a documented shortcut. | Repository-backed lookup/delete paths covered. |
| 2 | `delete hospital` | `DELETE /v1/hospitais/{hospital_id}` | Yes | Tests 6, 46, 47, 48 return 200 and assert confirmation text. | `HospitalResource.deleteById`, `HospitalService.delete` covered. |

- Happy-path item: Covered, using the documented direct database setup shortcut.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve hospital by id` | Verify deleted hospital no longer exists | Not Executed | Missing as a post-delete check. |
| 2 | `list hospitals` | Inspect remaining collection | Executed | List endpoint covered in tests 13, 14, 104, but not after delete. |

- Additional verification evidence: Confirmation body is asserted, but no post-delete absence assertion follows in the same scenario.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `delete hospital` | `{hospital_id}` is unknown | Covered | Tests 56, 58, 59 delete unknown ids and receive 400. | Exception path through lookup covered. |

- Coverage item summary: 2/2 items covered.
- Status: Covered.
- Confidence: High.
- Gap: No post-delete lookup/list verification.
- Recommended tests: Delete a created hospital, then assert `retrieve hospital by id` fails and `list hospitals` excludes it.

### B5: Inspect Hospital Capacity

- Business goal: Check the available bed count for a hospital.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | Establish hospital with available beds | Yes | Direct DB setup supplies hospitals with `availableBeds` values. | Hospital lookup covered. |
| 2 | `get available beds` | `GET /v1/hospitais/{id}/leitos` | Yes | Tests 16 and 34 assert `"leitos": 64`; test 37 asserts `"leitos": 0`. | `HospitalResource.verificaLeitosDisponiveis`, `HospitalService.findById` covered. |

- Happy-path item: Covered, using documented existing-state shortcuts.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve hospital by id` | Compare `availableBeds` | Executed | Test 42 retrieves hospital profile; not tied to bed-count scenario. |

- Additional verification evidence: Response map key `"leitos"` and numeric value are asserted.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `get available beds` | `{id}` is unknown | Covered | Tests 9, 10, 81, 116 assert 400 with missing hospital message/path. | Lookup exception path covered. |

- Coverage item summary: 2/2 items covered.
- Status: Covered.
- Confidence: High.
- Gap: No create-then-bed-count flow using returned id.
- Recommended tests: Create hospital with `availableBeds=10`, call `/leitos` with returned id, assert `{"leitos":10}`.

### B6: Find A Nearby Hospital With Available Beds

- Business goal: Locate a nearby hospital with positive bed availability.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | Create candidate hospital and location | Yes | Hospital creation is covered separately; no candidate tied to `/maisProximo` success. | Creation/location insertion covered. |
| 2 | `find nearest hospital with beds` | `GET /v1/hospitais/maisProximo` with `lat`, `lon`, `raioMaximo` | No | Many calls return 500, but no successful candidate response. | `HospitalResource.hospitalMaisProximo` and `HospitalService.findHospitalMaisProximoComVagas` are not corroborated as covered in union output; LocationService failure lines are signaled by EvoMaster comments. |

- Happy-path item: Not Covered, because no test returns a nearby available hospital.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve hospital by id` | Inspect selected hospital | Not Executed | No selected hospital exists. |
| 2 | `get available beds` | Verify positive availability | Executed | Bed lookup covered separately, not for a selected nearby hospital. |

- Additional verification evidence: `/maisProximo` failures assert status/error/path only.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `create hospital` | Invalid body or unusable geocoded location | Covered | Invalid create 400s cover invalid setup; API-created `WX`/`p` locations demonstrate geocoding variation. | Creation validation and location insertion covered. |
| `find nearest hospital with beds` | No nearby hospital has `availableBeds > 0` | Covered | Tests 27-31 and others call `/maisProximo` with no usable candidate and assert 500. | Code corroboration is weak/inconsistent for the controller/service method. |

- Coverage item summary: 2/3 items covered.
- Status: Partially Covered.
- Confidence: Low.
- Gap: No successful geospatial search with a positive-capacity hospital.
- Recommended tests: Seed two hospitals and matching `HOSPITAL` locations near query coordinates, call `/maisProximo`, and assert returned hospital id and positive beds.

### B7: Manage Hospital Stock Product

- Business goal: Create, inspect, update, list, and delete stock products for hospital operations.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | Establish stock-owning hospital | Yes | Creation covered separately and direct DB hospitals used. | Covered generally. |
| 2 | `add product to stock` | `POST /v1/hospitais/{hospital_id}/estoque` | No | No successful 200 add-product call. | `ProductService.insert` covered only through failure paths; hospital relationship persistence is not verified. |
| 3 | `update stock product` | `PUT /v1/hospitais/{hospital_id}/estoque/{produto_id}` | No | Only 400 update attempts; no 200 update. | `ProductService.update` is not covered in union. |
| 4 | `delete stock product` | `DELETE /v1/hospitais/{hospital_id}/estoque/{produto_id}` | No | Only 400 delete attempts; no 200 stock delete. | Delete failure paths covered. |

- Happy-path item: Not Covered, because the CRUD chain is absent.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve stock product` | Inspect product after create/update | Executed | Test 107 retrieves a directly inserted product with 200. |
| 2 | `list hospital stock` | Inspect products referenced by hospital | Executed | Tests 70 and 73 list empty stock for directly inserted hospitals. |

- Additional verification evidence: Test 107 asserts product name, description, quantity, type, and productName.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `add product to stock` | `{hospital_id}` is unknown | Covered | Test 98 posts valid productType `BLOOD` to unknown hospital and receives 400. | Product insert failure path covered. |
| `retrieve stock product` | `{produto_id}` is unknown | Covered | Tests 1, 6, 129, 131 retrieve unknown products and receive 404. | `ProductResource.findProductBy`, `ProductService.findById` covered. |
| `update stock product` | `{produto_id}` is unknown | Covered | Test 116 uses valid `BLOOD` body with unknown product id and receives 400. | Product update success method not covered, but lookup failure is exercised. |
| `delete stock product` | Product exists but hospital id is unknown | Covered | Tests 117 and 120 insert a product, delete through an unknown hospital id, and receive 400. | Delete lookup/failure path covered. |

- Coverage item summary: 4/5 items covered.
- Status: Partially Covered.
- Confidence: Medium.
- Gap: No successful add/update/delete workflow and no persisted stock membership verification.
- Recommended tests: Create hospital, add product, retrieve product, update product, retrieve again, delete product, then assert retrieval fails and stock list no longer contains it.

### B8: Transfer Product From A Nearby Hospital

- Business goal: Move product quantity from a nearby source hospital to a destination hospital.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | Create destination hospital | No | Creation covered elsewhere, not tied to transfer. | Covered generally. |
| 2 | `create hospital` | Create nearby source hospital | No | No source/destination transfer setup. | Covered generally. |
| 3 | Direct DB/source stock setup | Establish source product membership | No | No test sets up membership then transfers successfully. | Transfer source-selection helper largely uncovered. |
| 4 | `transfer product` | `POST /v1/hospitais/{id}/transferencia/{productId}` | No | All transfer calls return 400/404. | `HospitalService.transfereProduto` partially covered; nearby-source helper is not meaningfully covered. |

- Happy-path item: Not Covered, because no test returns successful transfer.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `list hospital stock` | Inspect source quantity | Executed | Stock list covered separately, not transfer-related. |
| 2 | `retrieve stock product` | Inspect decremented source product | Executed | Product retrieve covered separately in test 107. |
| 3 | `list hospital stock` | Inspect destination stock | Not Executed | No successful destination transfer product. |

- Additional verification evidence: None for transfer mutation.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `transfer product` | Destination `{id}` does not exist | Covered | Transfer 400/404s in tests 37, 62, 66, 67, 113 use missing destination ids. | Initial destination lookup path covered. |
| `transfer product` | `{productId}` does not identify a product | Not Covered | No transfer call reaches product lookup with an existing destination. | Product lookup branch for transfer not isolated. |
| `transfer product` | No nearby distinct hospital references the product | Not Covered | No test sets up destination/product then omits nearby ownership. | Source-selection branch not covered. |

- Coverage item summary: 1/4 items covered.
- Status: Partially Covered.
- Confidence: Low.
- Gap: No positive transfer and only one isolated failure class.
- Recommended tests: Directly seed destination/source hospitals with nearby locations and source product membership, then test successful transfer, missing product, and no-source variants.

### B9: Refuse Product Transfer When Source Stock Is Too Low

- Business goal: Return the business refusal when nearby source stock cannot spare the requested quantity.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | Create destination | No | No refusal setup. | Covered generally. |
| 2 | `create hospital` | Create source | No | No refusal setup. | Covered generally. |
| 3 | Direct DB/source stock setup | Source owns product with insufficient quantity | No | No insufficient-stock source setup. | Quantity branch not shown. |
| 4 | `refuse product transfer` | `POST /v1/hospitais/{id}/transferencia/{productId}` | No | No response body with refusal message found. | Refusal branch not covered. |

- Happy-path item: Not Covered, because no test reaches the insufficient-stock business refusal.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve stock product` | Verify source unchanged | Executed | Product retrieval covered separately, not after refusal. |

- Additional verification evidence: None.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `refuse product transfer` | Destination hospital is missing | Covered | Same missing-destination transfer calls as B8. | Destination lookup path covered. |
| `refuse product transfer` | Product exists but no nearby source hospital owns it | Not Covered | No test reaches source selection with a valid destination/product. | Source-selection branch not isolated. |

- Coverage item summary: 1/3 items covered.
- Status: Partially Covered.
- Confidence: Low.
- Gap: No low-stock refusal branch.
- Recommended tests: Seed nearby source product with `quantity <= quantidade + 4`, call transfer endpoint, assert refusal string and unchanged product quantity.

### B10: Explore Nearby Hospitals And Locations

- Business goal: Find nearby locations and hospitals around an origin hospital.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | Create origin hospital | No | Creation covered elsewhere, not tied to successful proximity. | Covered generally. |
| 2 | `create hospital` | Create nearby hospital | No | No two-hospital proximity success setup. | Covered generally. |
| 3 | `list nearby locations` | `GET /v1/hospitais/{hospital_id}/proximidades` | No | Only 400 proximity calls. | `LocationResource.findLocationNearHospitalBy` failure paths covered; DTO conversion largely uncovered. |
| 4 | `list nearby hospitals` | `GET /v1/hospitais/{hospital_id}/hospitaisProximos?raio=100` | No | Only 400 nearby-hospital calls. | LocationService proximity paths weakly covered. |

- Happy-path item: Not Covered, because no proximity list succeeds.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve hospital by id` | Inspect returned nearby hospital | Not Executed | No successful nearby hospital response. |

- Additional verification evidence: Proximity tests assert status/path only.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `list nearby locations` | Origin `{hospital_id}` is unknown | Covered | Tests 63, 80, 91-94, 109, 111, 112 call `/proximidades` with unknown ids and receive 400. | Origin lookup failure covered. |
| `list nearby hospitals` | Query `raio` is missing | Not Covered | Nearby-hospital calls include `raio` or malformed/non-documented variants; no clear missing-`raio` request. | Spring binding failure not isolated. |
| `list nearby hospitals` | Origin hospital has no stored location | Not Covered | No existing origin hospital without location is used. | Null-location dereference branch not isolated. |

- Coverage item summary: 1/4 items covered.
- Status: Partially Covered.
- Confidence: Medium.
- Gap: No successful proximity search and two missing failure rows.
- Recommended tests: Seed origin and nearby hospital locations, assert non-empty `proximidades` and `hospitaisProximos`; add missing-`raio` and null-location origin tests.

### B11: Admit A Patient

- Business goal: Check a patient into a hospital and consume one available bed.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | Create hospital with available bed | No | Creation covered elsewhere, not tied to successful check-in. | Covered generally. |
| 2 | `check in patient` | `POST /v1/hospitais/{hospital_id}/pacientes/checkin` | No | Tests 114 and 137 fail; no 200 check-in. | `PatientResource.checkinPacient` and `HospitalService.checkIn` are covered only on failure/partial paths. |

- Happy-path item: Not Covered, because no patient is admitted successfully.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `list hospital patients` | Verify patient appears | Executed | Direct DB list covered by tests 32, 69. |
| 2 | `retrieve patient` | Inspect active patient | Executed | Direct DB retrieve covered by test 108. |
| 3 | `get available beds` | Verify one bed consumed | Executed | Bed lookup covered separately. |

- Additional verification evidence: None tied to admission.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `check in patient` | `{hospital_id}` is unknown | Covered | Tests 114 and 137 call check-in with unknown hospitals and receive 400/404. | Initial hospital lookup failure covered. |
| `check in patient` | Hospital has `availableBeds=0` | Not Covered | No check-in call uses an existing full hospital. | `HospitalCheioException` constructor is covered, but not an admission assertion. |

- Coverage item summary: 1/3 items covered.
- Status: Partially Covered.
- Confidence: Medium.
- Gap: No successful admission and no full-hospital negative test.
- Recommended tests: Create hospital with `availableBeds=1`, check in patient, assert active patient and bed decrement; repeat with `availableBeds=0`.

### B12: Discharge A Checked-In Patient

- Business goal: Check a patient out and release one bed.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | Create hospital | No | Not tied to checkout workflow. | Covered generally. |
| 2 | `check in patient` | Admit patient first | No | No successful check-in. | Not covered as success. |
| 3 | `check out patient` | `POST /v1/hospitais/{hospital_id}/pacientes/checkout` | No | Only 404 checkout failures. | `HospitalService.checkOut` membership failure path partly covered. |

- Happy-path item: Not Covered, because no test checks out a currently checked-in patient.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve patient` | Inspect inactive status | Executed | Direct patient retrieve covered separately. |
| 2 | `list hospital patients` | Inspect hospital references | Executed | Direct hospital patient list covered separately. |
| 3 | `get available beds` | Verify released bed | Executed | Bed lookup covered separately. |

- Additional verification evidence: None tied to successful checkout.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `check in patient` | Setup hospital has no available beds | Not Covered | No full-hospital check-in setup. | Not isolated. |
| `check out patient` | Patient id is not present in hospital patient list | Covered | Tests 5, 16, 21, 34, 37 call checkout against existing/seeded hospital ids with non-member raw string ids and receive 404. | Membership stream failure path covered. |
| `check out patient` | Hospital id is unknown | Covered | Tests 0, 3, 20, 136 call checkout with unknown hospitals and receive 404. | Initial hospital lookup failure covered. |

- Coverage item summary: 2/4 items covered.
- Status: Partially Covered.
- Confidence: Medium.
- Gap: No successful checkout and no full-hospital setup failure.
- Recommended tests: Check in a patient, check out with returned id, assert inactive status, exit date, hospital list removal, and bed increment; add non-member and missing-hospital variants.

### B13: Maintain Patient Demographics

- Business goal: Retrieve and update patient demographic fields.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | Establish hospital | Yes | Direct DB patient shortcut is documented, so hospital creation can be skipped. | Covered generally. |
| 2 | `check in patient` | Create patient through API | Yes | Direct DB patient shortcut replaces setup. | Check-in success not covered, but shortcut applies. |
| 3 | `update patient` | `PUT /v1/hospitais/{hospital_id}/pacientes/{patientId}` | Yes | Test 16 directly inserts a patient, updates demographics by patient id, returns 200, and asserts updated/retained fields. | `PatientResource.updatePatient` covered; `PatientService.update` is not corroborated in union, so code signal is weaker than direct test evidence. |

- Happy-path item: Covered, using the documented direct patient setup shortcut.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| 1 | `retrieve patient` | Verify updated demographic fields | Executed | Test 108 retrieves a directly inserted patient; not after test 16 update. |
| 2 | `list hospital patients` | Inspect hospital list | Executed | Tests 32 and 69 list directly seeded hospital patients. |

- Additional verification evidence: Test 16 asserts CPF, birthDate, active flag, and location fields after update.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `update patient` | `{patientId}` is unknown | Covered | Tests 25, 104, 138, 139 update unknown patients and receive 404. | `PatientService.findById` failure path covered. |
| `check in patient` | Setup hospital is full or unknown | Covered | Unknown-hospital check-in covered by tests 114 and 137. | Hospital lookup failure covered. |

- Coverage item summary: 3/3 items covered.
- Status: Covered.
- Confidence: Medium.
- Gap: Optional retrieval is not after the update; check-in-based patient creation is not covered.
- Recommended tests: Check in a patient through API, update demographics, retrieve same patient, and assert only demographic fields changed.

### B14: View Hospital Patients

- Business goal: List hospital patients and retrieve individual patient records.

| Step | Function Name | Operation | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|---|
| 1 | `create hospital` | Create hospital | Yes | Direct DB relationship shortcut is documented. | Covered generally. |
| 2 | `check in patient` | Create listed patient | Yes | Direct DB relationship shortcut is documented. | Check-in success not covered. |
| 3 | `list hospital patients` | `GET /v1/hospitais/{hospital_id}/pacientes` | Yes | Tests 32 and 69 return 200 with seeded patient lists and assert list content. | `PatientResource.findPatients` covered. |
| 4 | `retrieve patient` | `GET /v1/hospitais/{hospital_id}/pacientes/{patientId}` | Yes | Test 108 returns 200 for a directly seeded patient and asserts details. | `PatientResource.findPatientById`, `PatientService.findById` covered. |

- Happy-path item: Not Covered, because list and retrieve are not exercised in one continuous scenario with the same hospital-patient relationship.

| Step | Function Name | Operation | Executed? | Evidence |
|---|---|---|---|---|
| - | - | Optional verification workflow | Not Applicable | No optional verification workflow is documented. |

- Additional verification evidence: Patient list tests assert multiple fields; patient retrieve test asserts demographic, active, and location fields.

| Failing Function | Failure Condition | Covered? | Test Evidence | Code Coverage Evidence |
|---|---|---|---|---|
| `list hospital patients` | Hospital id is unknown | Covered | Tests 99-103 return 404 for unknown hospital patient lists. | Hospital lookup failure covered. |
| `retrieve patient` | Patient id is unknown | Covered | Tests 21, 22, 129-135 return 404 for unknown patients. | Patient lookup failure covered. |

- Coverage item summary: 2/3 items covered.
- Status: Partially Covered.
- Confidence: Medium.
- Gap: Missing list-then-retrieve scenario sharing one patient id.
- Recommended tests: Seed or check in a patient, list hospital patients, extract one id, retrieve that id, and assert the same patient data.

## Cross-Behavior Gaps

- Multi-step workflows are rarely exercised end to end. The suite favors one-off generated calls over business scenarios with id reuse and ordered state transitions.
- Several success paths rely on direct database setup rather than API workflows: hospital deletion, bed lookup, patient list/retrieve, product retrieve/list, and patient update.
- Response assertions are strongest for DTO shape and scalar fields, but weak for domain side effects such as relationship persistence, bed count changes after admission/checkout, and stock quantity mutation after transfer.
- Stock operations lack successful `add product to stock`, `update stock product`, and `delete stock product` coverage; generated tests mostly cover invalid enum, missing hospital, missing product, and delete-with-missing-hospital failures.
- Transfer/refusal behavior is barely covered beyond missing destination hospital; no test reaches product lookup, nearby source selection, successful transfer, or insufficient-stock refusal.
- Proximity workflows lack any successful `proximidades`, `hospitaisProximos`, or `maisProximo` result. JaCoCo also shows weak coverage for `LocationService` and location DTO conversion.
- Check-in and checkout workflows do not prove core bed-management behavior: no patient admission succeeds, no checkout of a linked patient succeeds, and full-hospital admission is missing.
- Some generated failure requests violate several constraints at once. These are useful robustness probes but weak evidence for specific documented failure conditions unless the failing function and violated condition are traceable.
- Code coverage sometimes disagrees with direct test evidence, especially around generated tests using `resolveLocation` and around patient update service coverage. Behavior scoring therefore prioritizes explicit test operations and assertions.

