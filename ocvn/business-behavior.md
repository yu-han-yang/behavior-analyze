# Domain-Level Behavior Analysis

## Domain Summary

The service is an Open Contracting dashboard API for Vietnam procurement data. It exposes read-only dashboard, lookup, risk-flag, and Excel export functions over MongoDB OCDS release data. Main domain resources are releases, release packages, tenders, awards, procurement plans, organizations, procuring entities, buyers, suppliers, locations, item classifications, procurement methods, bid selection methods, and computed corruption-risk flags.

`full-behavior.md` is empty in this workspace, so there are no literal function names available from that file. The function names below use the OpenAPI `operationId` values from `ocvn.json`, verified against controller method names in source. This is an input/source discrepancy: the required function source is present but contains no content.

The documented API does not expose create, update, delete, import, authentication, or state-transition endpoints. All supported behaviors start from pre-existing imported MongoDB data.

## Available Function Inventory

### OCDS release and package access

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `ocdsReleasesUsingGET` / `ocdsReleasesUsingPOST` | `GET/POST /api/ocds/release/all` | List releases filtered by year and common dashboard filters. |
| `ocdsByOcidUsingGET` / `ocdsByOcidUsingPOST` | `GET/POST /api/ocds/release/ocid/{ocid}` | Retrieve one release by OCID. |
| `ocdsByProjectIdUsingGET` / `ocdsByProjectIdUsingPOST` | `GET/POST /api/ocds/release/budgetProjectId/{projectId}` | Retrieve one release by `planning.budget.projectID`. |
| `ocdsByPlanningBidNoUsingGET` / `ocdsByPlanningBidNoUsingPOST` | `GET/POST /api/ocds/release/planningBidNo/{bidNo}` | Retrieve one release by `planning.bidNo`. |
| `ocdsPackagesUsingGET` / `ocdsPackagesUsingPOST` | `GET/POST /api/ocds/package/all` | List releases wrapped as OCDS release packages. |
| `ocdsPackageByOcidUsingGET` / `ocdsPackageByOcidUsingPOST` | `GET/POST /api/ocds/package/ocid/{ocid}` | Retrieve one release package by OCID. |
| `packagedReleaseByProjectIdUsingGET` / `packagedReleaseByProjectIdUsingPOST` | `GET/POST /api/ocds/package/budgetProjectId/{projectId}` | Retrieve one release package by budget project id. |
| `packagedReleaseByPlanningBidNoUsingGET` / `packagedReleaseByPlanningBidNoUsingPOST` | `GET/POST /api/ocds/package/planningBidNo/{bidNo}` | Retrieve one release package by planning bid number. |

### Organization and reference lookup

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `searchTextUsingGET_1` / `searchTextUsingPOST_1` | `GET/POST /api/ocds/organization/all` | List/search all organizations by name text. |
| `byIdUsingGET_1` / `byIdUsingPOST_1` | `GET/POST /api/ocds/organization/id/{id}` | Retrieve any organization by id. |
| `byIdCollectionUsingGET` / `byIdCollectionUsingPOST` | `GET/POST /api/ocds/organization/ids` | Retrieve organizations by repeated `id` query values. |
| `searchTextUsingGET_2` / `searchTextUsingPOST_2` | `GET/POST /api/ocds/organization/procuringEntity/all` | List/search procuring entities. |
| `byIdUsingGET_2` / `byIdUsingPOST_2` | `GET/POST /api/ocds/organization/procuringEntity/id/{id}` | Retrieve procuring entity by id. |
| `searchTextUsingGET_3` / `searchTextUsingPOST_3` | `GET/POST /api/ocds/organization/supplier/all` | List/search suppliers. |
| `byIdUsingGET_3` / `byIdUsingPOST_3` | `GET/POST /api/ocds/organization/supplier/id/{id}` | Retrieve supplier by id. |
| `searchTextUsingGET` / `searchTextUsingPOST` | `GET/POST /api/ocds/organization/buyer/all` | List/search buyers. |
| `byIdUsingGET` / `byIdUsingPOST` | `GET/POST /api/ocds/organization/buyer/id/{id}` | Retrieve buyer by id. |
| `bidSelectionMethodsUsingGET` / `bidSelectionMethodsUsingPOST` | `GET/POST /api/ocds/bidSelectionMethod/all` | List distinct `tender.procurementMethodDetails`. |
| `procurementMethodsUsingGET` / `procurementMethodsUsingPOST` | `GET/POST /api/ocds/procurementMethod/all` | List distinct `tender.procurementMethod`. |
| `bidTypesUsingGET` / `bidTypesUsingPOST` | `GET/POST /api/ocds/bidType/all` | List item classifications used as bid types. |
| `contrMethodsUsingGET` / `contrMethodsUsingPOST` | `GET/POST /api/ocds/contrMethod/all` | List contract methods. |
| `locationsAllUsingGET` / `locationsAllUsingPOST` | `GET/POST /api/ocds/location/all` | List locations. |
| `locationsSearchUsingGET` / `locationsSearchUsingPOST` | `GET/POST /api/ocds/location/search` | Search locations. |
| `orgCitiesUsingGET` / `orgCitiesUsingPOST` | `GET/POST /api/ocds/city/all` | List procuring-entity cities. |
| `citiesSearchUsingGET` / `citiesSearchUsingPOST` | `GET/POST /api/ocds/city/search` | Search cities. |
| `orgDepartmentsUsingGET` / `orgDepartmentsUsingPOST` | `GET/POST /api/ocds/orgDepartment/all` | List organization departments. |
| `departmentSearchUsingGET` / `departmentSearchUsingPOST` | `GET/POST /api/ocds/orgDepartment/search` | Search departments. |
| `orgGroupsUsingGET` / `orgGroupsUsingPOST` | `GET/POST /api/ocds/orgGroup/all` | List organization groups. |
| `groupsSearchUsingGET` / `groupsSearchUsingPOST` | `GET/POST /api/ocds/orgGroup/search` | Search groups. |

### Procurement activity and timing analytics

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `countTendersByYearUsingGET` / `countTendersByYearUsingPOST` | `GET/POST /api/countTendersByYear` | Count tenders by tender start year/month. |
| `countAwardsByYearUsingGET` / `countAwardsByYearUsingPOST` | `GET/POST /api/countAwardsByYear` | Count awards by award date year/month. |
| `countBidPlansByYearUsingGET` / `countBidPlansByYearUsingPOST` | `GET/POST /api/countBidPlansByYear` | Count bid plans by plan approval year/month. |
| `tendersAwardsYearsUsingGET` / `tendersAwardsYearsUsingPOST` | `GET/POST /api/tendersAwardsYears` | List all years appearing in tenders, awards, or bid plans. |
| `averageTenderPeriodUsingGET` / `averageTenderPeriodUsingPOST` | `GET/POST /api/averageTenderPeriod` | Average tender duration by year/month. |
| `averageAwardPeriodUsingGET` / `averageAwardPeriodUsingPOST` | `GET/POST /api/averageAwardPeriod` | Average time from tender close to active award date. |
| `qualityAverageTenderPeriodUsingGET` / `qualityAverageTenderPeriodUsingPOST` | `GET/POST /api/qualityAverageTenderPeriod` | Percentage of tenders with tender period start and end dates. |
| `qualityAverageAwardPeriodUsingGET` / `qualityAverageAwardPeriodUsingPOST` | `GET/POST /api/qualityAverageAwardPeriod` | Percentage of awards with award date and tender end date. |
| `avgTimeFromPlanToTenderPhaseUsingGET` / `avgTimeFromPlanToTenderPhaseUsingPOST` | `GET/POST /api/avgTimeFromPlanToTenderPhase` | Average days from plan approval to tender start. |

### Competition, e-procurement, cancellation, and value analytics

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `averageNumberOfTenderersUsingGET` / `averageNumberOfTenderersUsingPOST` | `GET/POST /api/averageNumberOfTenderers` | Average number of tenderers by tender year/month. |
| `percentTendersWithTwoOrMoreTenderersUsingGET` / `percentTendersWithTwoOrMoreTenderersUsingPOST` | `GET/POST /api/percentTendersWithTwoOrMoreTenderers` | Share of tenders with more than one tenderer. |
| `percentTendersAwardedUsingGET` / `percentTendersAwardedUsingPOST` | `GET/POST /api/percentTendersAwardedWithTwoOrMoreTenderers` | Share of awarded tenders with more than one tenderer. |
| `percentTendersUsingEBidUsingGET` / `percentTendersUsingEBidUsingPOST` | `GET/POST /api/percentTendersUsingEBid` | Share of active-award tenders using electronic submission. |
| `percentTendersUsingEgpUsingGET` / `percentTendersUsingEgpUsingPOST` | `GET/POST /api/percentTendersUsingEgp` | Share of tenders using `publicationMethod=eGP`. |
| `percentTendersWithLinkedProcurementPlanUsingGET` / `percentTendersWithLinkedProcurementPlanUsingPOST` | `GET/POST /api/percentTendersWithLinkedProcurementPlan` | Share of tenders with linked plan budget amount. |
| `percentTendersCancelledUsingGET` / `percentTendersCancelledUsingPOST` | `GET/POST /api/percentTendersCancelled` | Share and count of cancelled tenders by year/month. |
| `totalCancelledTendersByYearUsingGET` / `totalCancelledTendersByYearUsingPOST` | `GET/POST /api/totalCancelledTendersByYear` | Total cancelled tender value by year/month. |
| `totalCancelledTendersByYearByRationaleUsingGET` / `totalCancelledTendersByYearByRationaleUsingPOST` | `GET/POST /api/totalCancelledTendersByYearByRationale` | Cancelled tender value by year/month and rationale. |
| `tenderValueIntervalUsingGET` / `tenderValueIntervalUsingPOST` | `GET/POST /api/tenderValueInterval` | Minimum and maximum tender value. |
| `awardValueIntervalUsingGET` / `awardValueIntervalUsingPOST` | `GET/POST /api/awardValueInterval` | Minimum and maximum award value. |
| `tenderPriceByProcurementMethodUsingGET` / `tenderPriceByProcurementMethodUsingPOST` | `GET/POST /api/tenderPriceByProcurementMethod` | Tender value by OCDS procurement method. |
| `tenderPriceByBidSelectionMethodUsingGET` / `tenderPriceByBidSelectionMethodUsingPOST` | `GET/POST /api/tenderPriceByBidSelectionMethod` | Tender value by local bid selection method. |
| `tenderPriceByAllBidSelectionMethodsUsingGET` / `tenderPriceByAllBidSelectionMethodsUsingPOST` | `GET/POST /api/tenderPriceByAllBidSelectionMethods` | Tender value by all known bid selection methods, adding zero rows. |
| `costEffectivenessAwardAmountUsingGET` / `costEffectivenessAwardAmountUsingPOST` | `GET/POST /api/costEffectivenessAwardAmount` | Active award value by tender year/month. |
| `costEffectivenessTenderAmountUsingGET` / `costEffectivenessTenderAmountUsingPOST` | `GET/POST /api/costEffectivenessTenderAmount` | Active tender value with active awards by year/month or group. |
| `costEffectivenessTenderAwardAmountUsingGET` / `costEffectivenessTenderAwardAmountUsingPOST` | `GET/POST /api/costEffectivenessTenderAwardAmount` | Combined tender-vs-award value, difference, and percentages. |
| `tendersByItemClassificationUsingGET` / `tendersByItemClassificationUsingPOST` | `GET/POST /api/tendersByItemClassification` | Tender count and value by item classification. |
| `fundingByTenderDeliveryLocationUsingGET` / `fundingByTenderDeliveryLocationUsingPOST` | `GET/POST /api/fundingByTenderDeliveryLocation` | Tender funding by delivery location and year/month. |
| `plannedFundingByLocationUsingGET` / `plannedFundingByLocationUsingPOST` | `GET/POST /api/plannedFundingByLocation` | Planned budget by project location and year/month. |
| `qualityFundingByTenderDeliveryLocationUsingGET` / `qualityFundingByTenderDeliveryLocationUsingPOST` | `GET/POST /api/qualityFundingByTenderDeliveryLocation` | Share of started tenders with delivery location. |
| `qualityPlannedFundingByLocationUsingGET` / `qualityPlannedFundingByLocationUsingPOST` | `GET/POST /api/qualityPlannedFundingByLocation` | Share of budgeted plans with project location. |
| `topTenLargestTendersUsingGET` / `topTenLargestTendersUsingPOST` | `GET/POST /api/topTenLargestTenders` | Top ten largest tenders by tender value. |
| `topTenLargestAwardsUsingGET` / `topTenLargestAwardsUsingPOST` | `GET/POST /api/topTenLargestAwards` | Top ten largest active awards by award value. |
| `topTenLargestSuppliersUsingGET` / `topTenLargestSuppliersUsingPOST` | `GET/POST /api/topTenSuppliers` | Top suppliers by active award value. |

### Risk indicators and relationship signals

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `releaseFlagSearchUsingGET` / `releaseFlagSearchUsingPOST` | `GET/POST /api/flags/i003/releases` | List releases flagged by indicator I003. |
| `flagStatsUsingGET` / `flagStatsUsingPOST` | `GET/POST /api/flags/i003/stats` | Stats for indicator I003. |
| `releaseFlagSearchUsingGET_1` / `releaseFlagSearchUsingPOST_1` | `GET/POST /api/flags/i004/releases` | List releases flagged by indicator I004. |
| `flagStatsUsingGET_1` / `flagStatsUsingPOST_1` | `GET/POST /api/flags/i004/stats` | Stats for indicator I004. |
| `releaseFlagSearchUsingGET_2` / `releaseFlagSearchUsingPOST_2` | `GET/POST /api/flags/i007/releases` | List releases flagged by indicator I007. |
| `flagStatsUsingGET_2` / `flagStatsUsingPOST_2` | `GET/POST /api/flags/i007/stats` | Stats for indicator I007. |
| `releaseFlagSearchUsingGET_3` / `releaseFlagSearchUsingPOST_3` | `GET/POST /api/flags/i019/releases` | List releases flagged by indicator I019. |
| `flagStatsUsingGET_3` / `flagStatsUsingPOST_3` | `GET/POST /api/flags/i019/stats` | Stats for indicator I019. |
| `releaseFlagSearchUsingGET_4` / `releaseFlagSearchUsingPOST_4` | `GET/POST /api/flags/i038/releases` | List releases flagged by indicator I038. |
| `flagStatsUsingGET_4` / `flagStatsUsingPOST_4` | `GET/POST /api/flags/i038/stats` | Stats for indicator I038. |
| `releaseFlagSearchUsingGET_5` / `releaseFlagSearchUsingPOST_5` | `GET/POST /api/flags/i077/releases` | List releases flagged by indicator I077. |
| `flagStatsUsingGET_5` / `flagStatsUsingPOST_5` | `GET/POST /api/flags/i077/stats` | Stats for indicator I077. |
| `releaseFlagSearchUsingGET_6` / `releaseFlagSearchUsingPOST_6` | `GET/POST /api/flags/i180/releases` | List releases flagged by indicator I180. |
| `flagStatsUsingGET_6` / `flagStatsUsingPOST_6` | `GET/POST /api/flags/i180/stats` | Stats for indicator I180. |
| `frequentSuppliersTimeIntervalUsingGET` / `frequentSuppliersTimeIntervalUsingPOST` | `GET/POST /api/frequentSuppliersTimeInterval` | Detect frequent supplier awards by procuring entity and time interval. |
| `frequentTenderersUsingGET` / `frequentTenderersUsingPOST` | `GET/POST /api/frequentTenderers` | Detect repeated supplier/tenderer pairings. |
| `percentageAwardsNarrowPublicationDatesUsingGET` / `percentageAwardsNarrowPublicationDatesUsingPOST` | `GET/POST /api/percentageAwardsNarrowPublicationDates` | Share of awards published within seven days of award date. |

### Excel export

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `excelExportUsingGET` / `excelExportUsingPOST` | `GET/POST /api/ocds/excelExport` | Export filtered releases to Excel. |
| `procurementActivityExcelChartUsingGET` / `procurementActivityExcelChartUsingPOST` | `GET/POST /api/ocds/procurementActivityExcelChart` | Export procurement activity chart. |
| `averageNumberBidsExcelChartUsingGET` / `averageNumberBidsExcelChartUsingPOST` | `GET/POST /api/ocds/averageNumberBidsExcelChart` | Export average bids chart. |
| `bidTimelineExcelChartUsingGET` / `bidTimelineExcelChartUsingPOST` | `GET/POST /api/ocds/bidTimelineExcelChart` | Export tender/award timeline chart. |
| `bidSelectionExcelChartUsingGET` / `bidSelectionExcelChartUsingPOST` | `GET/POST /api/ocds/bidSelectionExcelChart` | Export bid selection chart. |
| `procurementMethodExcelChartUsingGET` / `procurementMethodExcelChartUsingPOST` | `GET/POST /api/ocds/procurementMethodExcelChart` | Export procurement method chart. |
| `costEffectivenessExcelChartUsingGET` / `costEffectivenessExcelChartUsingPOST` | `GET/POST /api/ocds/costEffectivenessExcelChart` | Export cost effectiveness chart. |
| `cancelledFundingExcelChartUsingGET` / `cancelledFundingExcelChartUsingPOST` | `GET/POST /api/ocds/cancelledFundingExcelChart` | Export cancelled funding chart. |
| `cancelledTendersByYearByRationaleExcelChartUsingGET` / `cancelledTendersByYearByRationaleExcelChartUsingPOST` | `GET/POST /api/ocds/cancelledTendersByYearByRationaleExcelChart` | Export cancelled funding by reason chart. |
| `cancelledFundingPercentageExcelChartUsingGET` / `cancelledFundingPercentageExcelChartUsingPOST` | `GET/POST /api/ocds/cancelledFundingPercentageExcelChart` | Export cancellation percentage chart. |
| `numberCancelledFundingExcelChartUsingGET` / `numberCancelledFundingExcelChartUsingPOST` | `GET/POST /api/ocds/numberCancelledFundingExcelChart` | Export number of cancelled bids chart. |
| `percentTendersUsingEBidExcelChartUsingGET` / `percentTendersUsingEBidExcelChartUsingPOST` | `GET/POST /api/ocds/percentTendersUsingEBidExcelChart` | Export e-bid percentage chart. |
| `numberTendersUsingEBidExcelChartUsingGET` / `numberTendersUsingEBidExcelChartUsingPOST` | `GET/POST /api/ocds/numberTendersUsingEBidExcelChart` | Export number of e-bid awards chart. |
| `percentTendersUsingEgpExcelChartUsingGET` / `percentTendersUsingEgpExcelChartUsingPOST` | `GET/POST /api/ocds/percentTendersUsingEgpExcelChart` | Export e-procurement percentage chart. |
| `tendersWithLinkedProcurementPlanExcelChartUsingGET` / `tendersWithLinkedProcurementPlanExcelChartUsingPOST` | `GET/POST /api/ocds/tendersWithLinkedProcurementPlanExcelChart` | Export linked plan percentage chart. |
| `numberOfTendersByItemExcelChartUsingGET` / `numberOfTendersByItemExcelChartUsingPOST` | `GET/POST /api/ocds/tendersByItemExcelChart` | Export tenders by item chart. |

## Supported Business Behaviors

### Behavior 1: Browse OCDS Releases and Release Packages

Business goal:
Allow a dashboard or data consumer to retrieve raw procurement releases or OCDS release packages by filters or public identifiers.

Domain context:
Releases are the source record for tenders, awards, planning data, organizations, locations, and flags. Packages add publisher, license, URI, and published date metadata around releases.

Starting point:
Pre-existing imported MongoDB release data. No documented API function can create the release state.

Required execution workflow:
1. Use function `ocdsReleasesUsingGET` (`GET /api/ocds/release/all`) with optional query values `year=2020`, `month=1`, `monthly=false`, `pageNumber=0`, `pageSize=300`, and common filters such as `procuringEntityId=PE1`, `supplierId=SUP1`, `bidTypeId=CLASS1`, `minTenderValue=1000`, `maxTenderValue=1000000` to list matching releases.
2. Alternatively, use function `ocdsByOcidUsingGET` (`GET /api/ocds/release/ocid/{ocid}`) with `ocid=ocds-example-1`, or `ocdsByProjectIdUsingGET` (`GET /api/ocds/release/budgetProjectId/{projectId}`) with `projectId=PROJECT1`, or `ocdsByPlanningBidNoUsingGET` (`GET /api/ocds/release/planningBidNo/{bidNo}`) with `bidNo=BID1` to retrieve one release.
3. To retrieve packaged releases, use `ocdsPackagesUsingGET` (`GET /api/ocds/package/all`) with the same filter values, or `ocdsPackageByOcidUsingGET`, `packagedReleaseByProjectIdUsingGET`, or `packagedReleaseByPlanningBidNoUsingGET` with the same identifier values.

Optional verification workflow:
1. Use function `ocdsReleasesUsingGET` (`GET /api/ocds/release/all`) with `pageNumber=0` and narrow identifier-related filters to inspect that the same release appears in list form.
2. Use function `ocdsPackagesUsingGET` (`GET /api/ocds/package/all`) with the same query values to verify package wrapping for the same result set.

Existing-state shortcuts:
- Direct database fixtures can insert `release` documents with the desired `ocid`, `planning.budget.projectID`, and `planning.bidNo`.
- If the release already exists, identifier lookup steps can be used directly.
- The identifier value must match the field used by the selected function; `ocid`, `projectId`, and `bidNo` are not interchangeable.

Parameter and value bindings:
- `ocid` in `/api/ocds/release/ocid/{ocid}` and `/api/ocds/package/ocid/{ocid}` must equal `release.ocid`.
- `projectId` must equal `planning.budget.projectID`.
- `bidNo` must equal `planning.bidNo`.
- Filter ids bind to nested release fields: `procuringEntityId` to `tender.procuringEntity._id`, `supplierId` to `awards.suppliers._id`, `bidTypeId` to `tender.items.classification._id`, `tenderLoc` to `tender.items.deliveryLocation._id`, and `planningLoc` to `planning.budget.projectLocation._id`.

Business result:
No service state changes. The caller receives raw releases or release packages. Package responses derive metadata at request time: license, publication policy, URI, publisher name, publisher scheme, and publisher UID from the release OCID.

Constraints and invariants:
- `projectId` and `bidNo` path patterns allow only alphanumeric values in the implementation.
- List pagination defaults to `pageNumber=0`, `pageSize=300`, with `pageSize` capped at 1000.
- Year filters accept 1900 through 2200. Month filters work only when exactly one year is supplied.
- Package creation assumes a non-null release.

Failure and exceptional cases:
- Failing function: `ocdsPackageByOcidUsingGET`
  - Failure condition: `ocid` does not match any release.
  - Why it fails: implementation calls `createReleasePackage(release)` and dereferences `release.getDate()` and `release.getOcid()`.
  - Violated prerequisite or constraint: a matching release must exist before package wrapping.
- Failing function: `ocdsByProjectIdUsingGET`
  - Failure condition: `projectId` contains non-alphanumeric characters.
  - Why it fails: the route pattern restricts the path variable.
  - Violated prerequisite or constraint: `projectId` must match the controller route pattern.
- Failing function: `ocdsReleasesUsingGET`
  - Failure condition: `pageSize=0`, `pageSize>1000`, `pageNumber<0`, `year<1900`, `year>2200`, `month<1`, or `month>12`.
  - Why it fails: bean validation constraints are applied to the request model.
  - Violated prerequisite or constraint: dashboard paging and date filter bounds.

Implementation notes:
The OpenAPI description says package lookup by `planningBidNo` returns by OCID, but the controller actually retrieves by `planning.bidNo`. Release package URIs are hard-coded to `http://ocvn.developmentgateway.org`.

### Behavior 2: Discover Organizations and Filter Dimensions

Business goal:
Allow users to find organizations and reference values that can be reused as dashboard filters.

Domain context:
Most analytics can be scoped by procuring entity, supplier, buyer, location, city, department, group, bid type, procurement method, bid selection method, or contract method.

Starting point:
Pre-existing imported organization and release-derived reference data.

Required execution workflow:
1. Use function `searchTextUsingGET_1` (`GET /api/ocds/organization/all`) with `text=water`, `pageNumber=0`, `pageSize=300` to search organizations, or omit `text` to list.
2. Use function `searchTextUsingGET_2` (`GET /api/ocds/organization/procuringEntity/all`) with `text=ministry` to find procuring entities, then capture an organization `_id`.
3. Use function `searchTextUsingGET_3` (`GET /api/ocds/organization/supplier/all`) with `text=construction` to find suppliers, then capture an organization `_id`.
4. Use function `byIdUsingGET_1` (`GET /api/ocds/organization/id/{id}`) with `id=ORG1`, or role-specific `byIdUsingGET_2`, `byIdUsingGET_3`, or `byIdUsingGET` to retrieve a selected organization.
5. Use function `bidSelectionMethodsUsingGET` (`GET /api/ocds/bidSelectionMethod/all`), `procurementMethodsUsingGET` (`GET /api/ocds/procurementMethod/all`), `bidTypesUsingGET` (`GET /api/ocds/bidType/all`), `contrMethodsUsingGET` (`GET /api/ocds/contrMethod/all`), `locationsAllUsingGET`, `orgCitiesUsingGET`, `orgDepartmentsUsingGET`, or `orgGroupsUsingGET` to collect filter values for analytics.

Optional verification workflow:
1. Use function `ocdsReleasesUsingGET` (`GET /api/ocds/release/all`) with captured `procuringEntityId=ORG1`, `supplierId=SUP1`, `bidSelectionMethod=METHOD1`, `procurementMethod=open`, or `tenderLoc=LOC1` to inspect records matching the chosen dimensions.
2. Use function `byIdCollectionUsingGET` (`GET /api/ocds/organization/ids`) with repeated query values `id=ORG1&id=SUP1` to verify multiple selected organizations.

Existing-state shortcuts:
- If organization ids or reference ids are known from a database fixture or external catalog, discovery functions can be skipped.
- Direct database setup must preserve role membership: supplier, buyer, and procuring-entity lookups require the matching organization role/type in stored organization data.
- Release-derived reference lists can be skipped only if the chosen values actually appear in the nested release fields that downstream filters query.

Parameter and value bindings:
- Captured organization `_id` from procuring-entity search is reused as `procuringEntityId` in analytics.
- Captured supplier `_id` is reused as `supplierId`.
- Captured location `_id` from location search is reused as `tenderLoc` or `planningLoc` depending on whether the later behavior analyzes tender delivery location or planning project location.
- Bid selection values from `bidSelectionMethodsUsingGET` bind to `tender.procurementMethodDetails`; procurement methods bind to `tender.procurementMethod`.

Business result:
The caller obtains domain identifiers and labels that scope later dashboard behaviors. No state changes occur.

Constraints and invariants:
- Text search `text` must be 3 to 30 characters.
- Organization role-specific retrieval is stricter than generic retrieval: supplier/procuring-entity/buyer lookup requires both id and role.
- The implementation searches organization `name` with a case-insensitive regex, despite OpenAPI saying full-text search.

Failure and exceptional cases:
- Failing function: `searchTextUsingGET_1`
  - Failure condition: `text` is shorter than 3 or longer than 30 characters.
  - Why it fails: `TextSearchRequest` uses `@Size(min=3,max=30)`.
  - Violated prerequisite or constraint: search text length.
- Failing function: `byIdUsingGET_2`
  - Failure condition: id exists as an organization but not as a procuring entity.
  - Why it fails: role-specific repository lookup filters by organization type.
  - Violated prerequisite or constraint: requested id must have the requested domain role.
- Failing function: `contrMethodsUsingGET` or later filters using `contrMethod`
  - Failure condition: a non-ObjectId-like `contrMethod` value is supplied later.
  - Why it fails: the filter code converts values to Mongo `ObjectId`.
  - Violated prerequisite or constraint: contract method filter values must be valid ObjectId strings in practice, although API documentation only says alphanumeric.

Implementation notes:
OpenAPI descriptions for buyer and supplier mention `organization.types`, while source filters `roles`. The implementation is role-based.

### Behavior 3: Establish Dashboard Year and Value Filter Bounds

Business goal:
Let a client discover available years and numeric value intervals before applying filters to dashboard analytics.

Domain context:
Dashboard filters are user-facing controls. Years, tender value ranges, and award value ranges need discoverable bounds.

Starting point:
Pre-existing imported releases with tender dates, award dates, plan approval dates, tender values, or award values.

Required execution workflow:
1. Use function `tendersAwardsYearsUsingGET` (`GET /api/tendersAwardsYears`) with no required parameters to get distinct years from `awards.date`, `tender.tenderPeriod.startDate`, and `planning.bidPlanProjectDateApprove`.
2. Use function `tenderValueIntervalUsingGET` (`GET /api/tenderValueInterval`) with optional `year=2020`, `procuringEntityId=PE1`, `supplierId=SUP1`, `bidTypeId=CLASS1`, `pageNumber=0`, `pageSize=300` to get `minTenderValue` and `maxTenderValue`.
3. Use function `awardValueIntervalUsingGET` (`GET /api/awardValueInterval`) with the same relevant filters to get `minAwardValue` and `maxAwardValue`.

Optional verification workflow:
1. Use function `ocdsReleasesUsingGET` (`GET /api/ocds/release/all`) with `year=2020`, `minTenderValue={minTenderValue}`, and `maxTenderValue={maxTenderValue}` to inspect releases inside the computed range.

Existing-state shortcuts:
- If the UI or database already has the available years and value limits, these discovery steps can be skipped.
- The reused values must still match the same filter scope; a global min/max is not equivalent to a procuring-entity-specific min/max.

Parameter and value bindings:
- Years returned by `tendersAwardsYearsUsingGET` are reused as `year` query values in all year-filterable functions.
- `minTenderValue` and `maxTenderValue` returned by `tenderValueIntervalUsingGET` are reused as `minTenderValue` and `maxTenderValue`.
- `minAwardValue` and `maxAwardValue` returned by `awardValueIntervalUsingGET` are reused as `minAwardValue` and `maxAwardValue`.

Business result:
The client can render bounded year and value filters without mutating service state.

Constraints and invariants:
- `tenderValueIntervalUsingGET` filters by tender start date.
- `awardValueIntervalUsingGET` unwinds awards and filters by award date.
- Empty backing data returns empty result sets rather than creating default bounds.

Failure and exceptional cases:
- Failing function: `tenderValueIntervalUsingGET`
  - Failure condition: invalid `year`, `month`, `pageNumber`, or `pageSize`.
  - Why it fails: request validation applies shared paging/year constraints.
  - Violated prerequisite or constraint: valid dashboard filter bounds.
- Failing function: `awardValueIntervalUsingGET`
  - Failure condition: a filter such as `contrMethod` is syntactically accepted by OpenAPI but cannot be converted to `ObjectId`.
  - Why it fails: implementation converts `contrMethod` strings to `ObjectId`.
  - Violated prerequisite or constraint: valid contract method identifier representation.

Implementation notes:
`pageNumber` and `pageSize` are accepted by the request model for interval functions but do not materially affect the single aggregate min/max output.

### Behavior 4: Measure Procurement Activity Over Time

Business goal:
Show how many procurement plans, tenders, and awards exist by year or month.

Domain context:
Procurement activity volume is a core overview dashboard metric and is also used by exported activity charts.

Starting point:
Pre-existing imported releases with plan approval dates, tender start dates, or award dates.

Required execution workflow:
1. Use function `countBidPlansByYearUsingGET` (`GET /api/countBidPlansByYear`) with `year=2020`, `monthly=false`, `pageNumber=0`, `pageSize=300`, and optional common filters to count releases with `planning.bidPlanProjectDateApprove`.
2. Use function `countTendersByYearUsingGET` (`GET /api/countTendersByYear`) with the same `year`, `monthly`, and filter values to count tenders by `tender.tenderPeriod.startDate`.
3. Use function `countAwardsByYearUsingGET` (`GET /api/countAwardsByYear`) with the same `year`, `monthly`, and filter values to count awards by `awards.date`.

Optional verification workflow:
1. Use function `tendersAwardsYearsUsingGET` (`GET /api/tendersAwardsYears`) to verify that the requested `year` exists in at least one date source.
2. Use function `procurementActivityExcelChartUsingGET` (`GET /api/ocds/procurementActivityExcelChart`) with `language=en_US`, the same `year`, and the same filters to verify the counts through an exported chart.

Existing-state shortcuts:
- If equivalent counts have already been computed in a cache or database fixture, the count functions can be skipped for display, but exporting still invokes its export function.
- Direct database setup must include the correct date fields; a tender date does not satisfy bid-plan count, and an award date does not satisfy tender count.

Parameter and value bindings:
- The same `year`, `month`, `monthly`, organization, supplier, classification, procurement method, location, and value filters should be reused across the three count functions to compare the same scope.
- Date binding differs intentionally: bid plan count uses plan approval date, tender count uses tender start date, and award count uses award date.

Business result:
The caller receives count series for bid plans, tenders, and awards. No state changes occur.

Constraints and invariants:
- `month` is applied only when a single `year` is supplied.
- Monthly grouping is controlled separately by `monthly=true`.
- Count-awards unwinds awards, so one release with multiple awards contributes multiple award counts.

Failure and exceptional cases:
- Failing function: `countBidPlansByYearUsingGET`
  - Failure condition: no `planning.bidPlanProjectDateApprove` exists in the selected scope.
  - Why it fails by domain expectation but not implementation: the function returns an empty list rather than an error.
  - Violated prerequisite or constraint: data must contain plan approval dates for non-empty output.
- Failing function: `countAwardsByYearUsingGET`
  - Failure condition: `year` outside 1900-2200.
  - Why it fails: validation rejects out-of-range year.
  - Violated prerequisite or constraint: valid supported year range.

Implementation notes:
The Excel export controller calls the three count functions internally and aligns categories across all returned series.

### Behavior 5: Analyze Tender, Award, and Plan-to-Tender Timing

Business goal:
Measure procurement cycle duration and data completeness for timing metrics.

Domain context:
Tender periods, award periods, and time from procurement plan approval to tender start are operational performance indicators.

Starting point:
Pre-existing imported releases with relevant date fields.

Required execution workflow:
1. Use function `averageTenderPeriodUsingGET` (`GET /api/averageTenderPeriod`) with `year=2020`, `monthly=false`, and optional filters to calculate average days between `tender.tenderPeriod.startDate` and `tender.tenderPeriod.endDate`.
2. Use function `qualityAverageTenderPeriodUsingGET` (`GET /api/qualityAverageTenderPeriod`) with the same non-year common filters to calculate the percentage of tenders with both tender period dates.
3. Use function `averageAwardPeriodUsingGET` (`GET /api/averageAwardPeriod`) with `year=2020` and the same filters to calculate average days between tender period end and active award date.
4. Use function `qualityAverageAwardPeriodUsingGET` (`GET /api/qualityAverageAwardPeriod`) with the same non-year common filters to calculate award-date/tender-end-date completeness.
5. Use function `avgTimeFromPlanToTenderPhaseUsingGET` (`GET /api/avgTimeFromPlanToTenderPhase`) with `year=2020` and the same filters to calculate average days from `planning.bidPlanProjectDateApprove` to tender start.

Optional verification workflow:
1. Use function `bidTimelineExcelChartUsingGET` (`GET /api/ocds/bidTimelineExcelChart`) with `language=en_US`, `year=2020`, and the same filters to verify the timeline data through an Excel chart.
2. Use function `ocdsReleasesUsingGET` (`GET /api/ocds/release/all`) with the same filters to inspect raw date fields.

Existing-state shortcuts:
- If date completeness is already known, quality functions can be skipped, but the timing metric function must still be called to compute the displayed behavior.
- Direct database setup must include the exact date pairs consumed by each metric.

Parameter and value bindings:
- Reuse the same `year`, `month`, `monthly`, and common filter values when comparing timing functions.
- `averageTenderPeriodUsingGET` and `avgTimeFromPlanToTenderPhaseUsingGET` bind `year` to tender start date.
- `averageAwardPeriodUsingGET` binds `year` to award date.
- Quality functions do not use `YearFilterPagingRequest`; they use default filters and assess all matching records in the selected non-year scope.

Business result:
The caller receives timing averages and completeness percentages. No state changes occur.

Constraints and invariants:
- Average award period only includes active awards.
- Average tender period requires both tender start and end dates.
- Plan-to-tender average requires tender start, planning budget amount, and plan approval date.
- Negative durations are not explicitly rejected; if dates are reversed, the arithmetic can produce negative day values.

Failure and exceptional cases:
- Failing function: `averageTenderPeriodUsingGET`
  - Failure condition: tender start or end date missing.
  - Why it fails by domain expectation but not implementation: records are excluded from the aggregate rather than rejected.
  - Violated prerequisite or constraint: complete tender period dates are required for inclusion.
- Failing function: `averageAwardPeriodUsingGET`
  - Failure condition: award is not active, award date is missing, or tender end date is missing.
  - Why it fails by domain expectation but not implementation: records are filtered out.
  - Violated prerequisite or constraint: active award with complete dates.
- Failing function: `avgTimeFromPlanToTenderPhaseUsingGET`
  - Failure condition: `planning.budget.amount` is missing even if plan approval date exists.
  - Why it fails by implementation: the match requires `planning.budget.amount`.
  - Violated prerequisite or constraint: the implementation treats budget amount as proof of a real planning entity.

Implementation notes:
The average award OpenAPI description says duration is counted between tender end and tender start, but source calculates `awards.date - tender.tenderPeriod.endDate`. Source should be treated as authoritative.

### Behavior 6: Analyze Competition and E-Procurement Adoption

Business goal:
Assess bidder participation, awarded competition, and electronic procurement adoption.

Domain context:
Competition and e-procurement indicators support market openness, efficiency, and risk assessment.

Starting point:
Pre-existing releases with tenderer counts, tender submission method, publication method, active awards, and linked planning data.

Required execution workflow:
1. Use function `averageNumberOfTenderersUsingGET` (`GET /api/averageNumberOfTenderers`) with `year=2020`, `monthly=false`, and optional filters to calculate average `tender.numberOfTenderers`.
2. Use function `percentTendersWithTwoOrMoreTenderersUsingGET` (`GET /api/percentTendersWithTwoOrMoreTenderers`) with the same filters to calculate the share of all tenders with more than one tenderer.
3. Use function `percentTendersAwardedUsingGET` (`GET /api/percentTendersAwardedWithTwoOrMoreTenderers`) with the same filters to calculate the share among tenders with at least one tenderer.
4. Use function `percentTendersUsingEBidUsingGET` (`GET /api/percentTendersUsingEBid`) with the same filters to calculate the share of active-award tenders whose `tender.submissionMethod` includes `electronicSubmission`.
5. Use function `percentTendersUsingEgpUsingGET` (`GET /api/percentTendersUsingEgp`) with the same filters to calculate the share of tenders with `tender.publicationMethod=eGP`.
6. Use function `percentTendersWithLinkedProcurementPlanUsingGET` (`GET /api/percentTendersWithLinkedProcurementPlan`) with the same filters to calculate the share with `planning.budget.amount`.

Optional verification workflow:
1. Use function `averageNumberBidsExcelChartUsingGET` (`GET /api/ocds/averageNumberBidsExcelChart`) with `language=en_US` and the same filters to verify average bid counts in Excel.
2. Use `percentTendersUsingEBidExcelChartUsingGET`, `numberTendersUsingEBidExcelChartUsingGET`, `percentTendersUsingEgpExcelChartUsingGET`, or `tendersWithLinkedProcurementPlanExcelChartUsingGET` with the same filters to verify exported dashboard views.

Existing-state shortcuts:
- If only one competition indicator is needed, the unrelated metric functions can be skipped.
- Direct database setup must preserve the specific consumed fields: `numberOfTenderers`, `submissionMethod`, `publicationMethod`, active award status, and `planning.budget.amount`.

Parameter and value bindings:
- Use the same filter set across all functions for comparable results.
- `year` binds to `tender.tenderPeriod.startDate` for these functions.
- `electronicSubmission=true` can be used as a filter in shared filter logic, but the `percentTendersUsingEBidUsingGET` metric itself computes electronic submission count and percentage.

Business result:
The caller receives competition and e-procurement percentage/count series. No state changes occur.

Constraints and invariants:
- Awarded-competition percentage excludes tenders with `numberOfTenderers <= 0` from the denominator.
- E-bid percentage requires active awards and at least one submission method.
- EGP percentage is a strict string equality on `publicationMethod=eGP`.
- Linked-plan percentage uses presence of `planning.budget.amount`, not a separate plan relationship table.

Failure and exceptional cases:
- Failing function: `percentTendersAwardedUsingGET`
  - Failure condition: all matching tenders have `numberOfTenderers <= 0`.
  - Why it fails by domain expectation but not implementation: no matching denominator records are returned.
  - Violated prerequisite or constraint: at least one tenderer is required for denominator inclusion.
- Failing function: `percentTendersUsingEBidUsingGET`
  - Failure condition: active award status is absent even when tender submission method exists.
  - Why it fails by implementation: the match requires `awards.status=active`.
  - Violated prerequisite or constraint: e-bid adoption is measured over active-award tenders only.

Implementation notes:
The function name `percentTendersAwardedUsingGET` is less descriptive than its endpoint; it refers to `/api/percentTendersAwardedWithTwoOrMoreTenderers`.

### Behavior 7: Analyze Procurement Value, Cost Effectiveness, and Price Distribution

Business goal:
Compare tender values, award values, procurement methods, and value differences between tenders and awards.

Domain context:
Value analytics show how funds are planned, tendered, awarded, and distributed by procurement method and supplier.

Starting point:
Pre-existing releases with tender values, award values, methods, active awards, and tender dates.

Required execution workflow:
1. Use function `tenderPriceByProcurementMethodUsingGET` (`GET /api/tenderPriceByProcurementMethod`) with `year=2020` and optional filters to total tender value by `tender.procurementMethod`.
2. Use function `tenderPriceByBidSelectionMethodUsingGET` (`GET /api/tenderPriceByBidSelectionMethod`) with the same filters to total tender value by `tender.procurementMethodDetails`.
3. Use function `tenderPriceByAllBidSelectionMethodsUsingGET` (`GET /api/tenderPriceByAllBidSelectionMethods`) with the same filters to include zero-valued rows for missing bid selection methods.
4. Use function `costEffectivenessTenderAmountUsingGET` (`GET /api/costEffectivenessTenderAmount`) with `year=2020`, `groupByCategory=bidSelectionMethod` or omitted, and optional filters to total active tender value with active awards.
5. Use function `costEffectivenessAwardAmountUsingGET` (`GET /api/costEffectivenessAwardAmount`) with the same filters to total active award value.
6. Use function `costEffectivenessTenderAwardAmountUsingGET` (`GET /api/costEffectivenessTenderAwardAmount`) with the same filters to receive combined tender amount, award amount, difference, award percentage, and difference percentage.

Optional verification workflow:
1. Use function `bidSelectionExcelChartUsingGET` (`GET /api/ocds/bidSelectionExcelChart`) with `language=en_US` and the same filters.
2. Use function `procurementMethodExcelChartUsingGET` (`GET /api/ocds/procurementMethodExcelChart`) with `language=en_US` and the same filters.
3. Use function `costEffectivenessExcelChartUsingGET` (`GET /api/ocds/costEffectivenessExcelChart`) with `language=en_US` and the same filters.

Existing-state shortcuts:
- If only method totals are needed, cost-effectiveness functions can be skipped.
- If the client only needs combined tender/award comparison, calling `costEffectivenessTenderAwardAmountUsingGET` is enough; it invokes tender and award amount logic internally.
- Direct database setup must include active award records and tender values for inclusion in cost-effectiveness calculations.

Parameter and value bindings:
- Reuse `year`, `month`, `monthly`, and filters across tender and award amount functions to compare the same scope.
- `groupByCategory` is only meaningful for cost-effectiveness tender amount and combined cost effectiveness; supported values are `bidSelectionMethod`, `bidTypeId`, and `procuringEntityId`.
- `tenderPriceByAllBidSelectionMethodsUsingGET` reuses bid selection method values from `bidSelectionMethodsUsingGET` and adds zero totals where absent.

Business result:
The caller receives procurement value totals by method and cost-effectiveness comparison metrics. No state changes occur.

Constraints and invariants:
- Tender price by procurement method requires active awards and tender value.
- Tender price by bid selection method applies default filters after year filtering.
- Combined cost effectiveness calculates missing tender or award totals as zero when one side lacks a matching year/month key.
- Percentage calculations use `0` when tender total is zero.

Failure and exceptional cases:
- Failing function: `costEffectivenessTenderAwardAmountUsingGET`
  - Failure condition: internal tender or award aggregate throws an exception.
  - Why it fails: implementation wraps `InterruptedException` or `ExecutionException` from async internal calls in `RuntimeException`.
  - Violated prerequisite or constraint: both internal aggregate computations must complete.
- Failing function: `tenderPriceByAllBidSelectionMethodsUsingGET`
  - Failure condition: a result row has a null method and other rows sort against it.
  - Why it is handled: implementation replaces null with `Unspecified` before adding to sorted output.
  - Violated prerequisite or constraint: none; null method is normalized.
- Failing function: `tenderPriceByProcurementMethodUsingGET`
  - Failure condition: no active award exists for a tender with value.
  - Why it fails by domain expectation but not implementation: tender is excluded.
  - Violated prerequisite or constraint: only tenders with active awards are included.

Implementation notes:
`costEffectivenessTenderAmountUsingGET` supports grouping, but `costEffectivenessAwardAmountUsingGET` accepts the grouped request type only because of Java inheritance; award aggregation itself is year/month oriented.

### Behavior 8: Analyze Cancelled Tenders

Business goal:
Measure cancellation rates and cancelled tender value, including cancellation rationale.

Domain context:
Cancelled tenders represent procurement failure, rework, or possible market/administrative issues.

Starting point:
Pre-existing releases with `tender.status=cancelled`, tender value, tender start date, and optional `tender.cancellationRationale`.

Required execution workflow:
1. Use function `percentTendersCancelledUsingGET` (`GET /api/percentTendersCancelled`) with `year=2020`, `monthly=false`, and optional filters to calculate total tenders, cancelled tenders, and cancellation percentage.
2. Use function `totalCancelledTendersByYearUsingGET` (`GET /api/totalCancelledTendersByYear`) with the same filters to calculate total cancelled tender value.
3. Use function `totalCancelledTendersByYearByRationaleUsingGET` (`GET /api/totalCancelledTendersByYearByRationale`) with the same filters to group cancelled value by cancellation rationale.

Optional verification workflow:
1. Use function `cancelledFundingPercentageExcelChartUsingGET` (`GET /api/ocds/cancelledFundingPercentageExcelChart`) with `language=en_US` and the same filters.
2. Use function `cancelledFundingExcelChartUsingGET` (`GET /api/ocds/cancelledFundingExcelChart`) with `language=en_US` and the same filters.
3. Use function `cancelledTendersByYearByRationaleExcelChartUsingGET` (`GET /api/ocds/cancelledTendersByYearByRationaleExcelChart`) with `language=en_US` and the same filters.

Existing-state shortcuts:
- If only cancellation rate is needed, cancelled value functions can be skipped.
- Direct database setup must set `tender.status` exactly to `cancelled` for cancelled-value functions.

Parameter and value bindings:
- Reuse the same `year`, `month`, `monthly`, and filters across percentage and value functions.
- `year` binds to `tender.tenderPeriod.startDate`.
- Cancellation rationale grouping consumes `tender.cancellationRationale`; null rationales can become their own group.

Business result:
The caller receives cancellation percentage, cancellation count, cancelled value, and cancelled value by rationale. No state changes occur.

Constraints and invariants:
- Cancellation value functions do not apply pagination in source even though request model supports paging.
- Cancellation rate denominator includes all tenders with tender start date in scope.
- Cancelled value requires tender value amount to contribute meaningful sums.

Failure and exceptional cases:
- Failing function: `totalCancelledTendersByYearUsingGET`
  - Failure condition: matching cancelled tenders have missing `tender.value.amount`.
  - Why it fails by domain expectation but not implementation: records may aggregate with missing values rather than explicit validation.
  - Violated prerequisite or constraint: cancelled funding analysis requires tender values.
- Failing function: `percentTendersCancelledUsingGET`
  - Failure condition: no tenders have `tender.tenderPeriod.startDate` in scope.
  - Why it fails by domain expectation but not implementation: returns empty list.
  - Violated prerequisite or constraint: started tenders are required for denominator.

Implementation notes:
`percentTendersCancelledUsingGET` includes `PERCENT_TENDERS` in final projection, but the computed cancellation field is `percentCancelled`.

### Behavior 9: Analyze Procurement by Item and Location

Business goal:
Show how procurement value and counts are distributed across item classifications and geographic locations.

Domain context:
Item and location views support sector analysis, regional spending analysis, and map visualizations.

Starting point:
Pre-existing releases with tender item classifications, tender delivery locations, planning project locations, values, and geocoding coordinates.

Required execution workflow:
1. Use function `tendersByItemClassificationUsingGET` (`GET /api/tendersByItemClassification`) with `year=2020` and optional filters to count tenders and sum tender values by item classification.
2. Use function `fundingByTenderDeliveryLocationUsingGET` (`GET /api/fundingByTenderDeliveryLocation`) with `year=2020`, `monthly=false`, and optional filters to sum tender funding by delivery location.
3. Use function `qualityFundingByTenderDeliveryLocationUsingGET` (`GET /api/qualityFundingByTenderDeliveryLocation`) with the same filters to assess whether tenders have delivery locations.
4. Use function `plannedFundingByLocationUsingGET` (`GET /api/plannedFundingByLocation`) with `year=2020` and optionally `procuringEntityId=PE1` to sum planning budget by project location.
5. Use function `qualityPlannedFundingByLocationUsingGET` (`GET /api/qualityPlannedFundingByLocation`) with `procuringEntityId=PE1` to assess whether budgeted plans have locations.

Optional verification workflow:
1. Use function `numberOfTendersByItemExcelChartUsingGET` (`GET /api/ocds/tendersByItemExcelChart`) with `language=en_US` and the same filters.
2. Use function `locationsAllUsingGET` (`GET /api/ocds/location/all`) or `locationsSearchUsingGET` (`GET /api/ocds/location/search`) to inspect location reference data.

Existing-state shortcuts:
- If item classifications or locations are known, lookup functions can be skipped.
- Direct database setup must include `geometry.coordinates` for map funding functions; location records without coordinates are filtered out.
- Planned funding can skip non-procuring-entity filters because the implementation only honors `procuringEntityId` and year for that function.

Parameter and value bindings:
- `bidTypeId` binds to `tender.items.classification._id` for item filters.
- `tenderLoc` binds to `tender.items.deliveryLocation._id`.
- `planningLoc` binds to `planning.budget.projectLocation._id`.
- `plannedFundingByLocationUsingGET` splits `planning.budget.amount.amount` across the number of project locations; the same budget amount is intentionally divided across child locations.

Business result:
The caller receives item classification counts/value, tender funding by delivery location, planned funding by project location, and quality percentages. No state changes occur.

Constraints and invariants:
- Tender delivery funding requires tender start date and geocoded delivery location.
- Planned funding requires planning data, project locations, geocoded coordinates, and plan approval date.
- `plannedFundingByLocationUsingGET` responds only to `procuringEntityId` among the common domain filters, per implementation and OpenAPI description.
- Quality planned funding requires `planning.budget.amount`.

Failure and exceptional cases:
- Failing function: `plannedFundingByLocationUsingGET`
  - Failure condition: `planning.budget.projectLocation` is empty or locations have no coordinates.
  - Why it fails by domain expectation but not implementation: records are filtered out.
  - Violated prerequisite or constraint: mapped planned funding requires geocoded project locations.
- Failing function: `qualityFundingByTenderDeliveryLocationUsingGET`
  - Failure condition: tender items are missing.
  - Why it can fail in execution: implementation unwinds `tender.items`, so records without items do not contribute as expected.
  - Violated prerequisite or constraint: tender items must exist for delivery-location quality assessment.

Implementation notes:
Funding-by-location functions contain commented-out pagination, so large result sets may not be paginated even though the shared request model advertises pagination.

### Behavior 10: Identify Top Suppliers, Largest Awards, Largest Tenders, and Repeated Relationships

Business goal:
Find concentration and repeated relationship patterns in procurement activity.

Domain context:
Top value rankings and repeated pairings can support market concentration analysis and risk screening.

Starting point:
Pre-existing releases with tender values, active award values, suppliers, procuring entities, tenderers, and award dates.

Required execution workflow:
1. Use function `topTenLargestTendersUsingGET` (`GET /api/topTenLargestTenders`) with optional `year=2020` and filters to retrieve the ten largest tenders by `tender.value.amount`.
2. Use function `topTenLargestAwardsUsingGET` (`GET /api/topTenLargestAwards`) with optional `year=2020` and filters to retrieve the ten largest active awards by `awards.value.amount`.
3. Use function `topTenLargestSuppliersUsingGET` (`GET /api/topTenSuppliers`) with optional `year=2020` and filters to retrieve suppliers ranked by total active award value.
4. Use function `frequentSuppliersTimeIntervalUsingGET` (`GET /api/frequentSuppliersTimeInterval`) with `intervalDays=365`, `maxAwards=3`, and optional `now=2026-05-27` to find supplier/procuring-entity pairs with award counts greater than `maxAwards` in a computed time interval.
5. Use function `frequentTenderersUsingGET` (`GET /api/frequentTenderers`) with `year=2020`, `pageNumber=0`, `pageSize=300`, and optional filters to find repeated supplier/tenderer pairings.

Optional verification workflow:
1. Use function `byIdCollectionUsingGET` (`GET /api/ocds/organization/ids`) with repeated supplier and procuring entity ids returned by ranking functions to inspect names and organization records.
2. Use function `ocdsReleasesUsingGET` (`GET /api/ocds/release/all`) with `supplierId={supplierId}` and `procuringEntityId={procuringEntityId}` to inspect source releases.

Existing-state shortcuts:
- If supplier and procuring entity ids are already known, organization lookup can be skipped.
- Direct database setup must include active awards for award/supplier rankings.
- Frequent supplier analysis can use a fixed `now` value for reproducibility; otherwise the current server date changes interval assignment over time.

Parameter and value bindings:
- Supplier ids returned by `topTenLargestSuppliersUsingGET` bind to `supplierId` filters and organization lookup `id`.
- Procuring entity ids in `procuringEntityIds` bind to `procuringEntityId` filters.
- `frequentSuppliersTimeIntervalUsingGET` groups by `tender.procuringEntity._id`, `awards.suppliers._id`, and computed `timeInterval`.
- `intervalDays` controls bucket size; `maxAwards` is a strict greater-than threshold, not greater-than-or-equal.

Business result:
The caller receives top ten value rankings and repeated relationship signals. No state changes occur.

Constraints and invariants:
- Largest awards and top suppliers include only active awards.
- Largest tenders source code does not explicitly require `tender.status=active`, despite the OpenAPI summary saying active tenders.
- Frequent tenderers requires at least two tenderers, at least one supplier, and active awards.
- Frequent supplier intervals are relative to `now`.

Failure and exceptional cases:
- Failing function: `frequentSuppliersTimeIntervalUsingGET`
  - Failure condition: `intervalDays=0`.
  - Why it fails: implementation divides by `intervalDays` in Mongo aggregation and does not validate positive values.
  - Violated prerequisite or constraint: interval length must be positive.
- Failing function: `frequentTenderersUsingGET`
  - Failure condition: tender has co-tenderers but no active award supplier.
  - Why it fails by domain expectation but not implementation: records are excluded because implementation compares tenderers to active award suppliers.
  - Violated prerequisite or constraint: source requires active award supplier data.
- Failing function: `topTenLargestTendersUsingGET`
  - Failure condition: relying on OpenAPI promise that only active tenders are returned.
  - Why it should fail by domain/API expectation but does not: implementation does not filter `tender.status=active`.
  - Violated prerequisite or constraint: documented active-tender invariant is not enforced.

Implementation notes:
`frequentTenderersUsingGET` does not actually compute all pairs of tenderers that applied together. It forms pairs between active award suppliers and tenderers, excluding identical ids.

### Behavior 11: Inspect Corruption-Risk Indicator Flags

Business goal:
View flagged releases and aggregate statistics for supported risk indicators.

Domain context:
The service stores precomputed `flags` on releases and exposes searches and stats for selected indicators.

Starting point:
Pre-existing releases with `flags.flaggedStats` and indicator boolean properties such as I003, I004, I007, I019, I038, I077, and I180.

Required execution workflow:
1. Use one flag release function, such as `releaseFlagSearchUsingGET` (`GET /api/flags/i003/releases`) with `year=2020`, `pageNumber=0`, `pageSize=300`, and optional common filters to list releases flagged by I003.
2. Use the matching stats function, such as `flagStatsUsingGET` (`GET /api/flags/i003/stats`) with the same `year`, `month`, `monthly`, and filters to obtain `total`, `totalTrue`, `totalFalse`, `totalPrecondMet`, and percentages.
3. Repeat with matching function pairs for I004, I007, I019, I038, I077, or I180: `releaseFlagSearchUsingGET_1` with `flagStatsUsingGET_1`, `releaseFlagSearchUsingGET_2` with `flagStatsUsingGET_2`, and so on.

Optional verification workflow:
1. Use function `ocdsReleasesUsingGET` (`GET /api/ocds/release/all`) with `flagged=true`, `year=2020`, and the same filters to inspect all releases flagged by at least one indicator.
2. Use function `percentageAwardsNarrowPublicationDatesUsingGET` (`GET /api/percentageAwardsNarrowPublicationDates`) with the same year filters to inspect one publication-timing risk-adjacent metric.

Existing-state shortcuts:
- If flag fields have already been computed and inserted directly into releases, no API setup is needed.
- There is no documented API function to calculate or recalculate flags; direct database or import-service setup is the only way to establish flag state through available files.

Parameter and value bindings:
- The same indicator code must be used for release search and stats; I003 release search should be compared with I003 stats, not I004 stats.
- `year` binds to `tender.tenderPeriod.startDate`.
- `flagged=true` in generic release listing means at least one `flags.flaggedStats` element exists, not a specific indicator.

Business result:
The caller receives flagged release slices and aggregate precondition/true/false percentages for each indicator. No state changes occur.

Constraints and invariants:
- Release search returns only records where `flags.flaggedStats.0` exists and the selected indicator property is true.
- Stats include true, false, and precondition-met counts based on whether the indicator field is true, false, or non-null.
- Stats guard division by zero for true/false precondition percentages, but not every percentage expression has an explicit zero guard.

Failure and exceptional cases:
- Failing function: `releaseFlagSearchUsingGET`
  - Failure condition: release has raw procurement data that would satisfy I003 but no persisted flag field.
  - Why it fails by domain expectation but not implementation: endpoint only reads precomputed flag state.
  - Violated prerequisite or constraint: flags must be precomputed and stored.
- Failing function: `flagStatsUsingGET`
  - Failure condition: no tender start date exists.
  - Why it fails by implementation: stats match requires the year property to exist.
  - Violated prerequisite or constraint: flag stats are grouped by tender start date.

Implementation notes:
Source contains additional flag endpoints for I002, I085, I171, crosstabs, and indicator type mapping that are not present in `ocvn.json`; they are source-backed but not part of the documented OpenAPI inventory used here.

### Behavior 12: Export Dashboard Data to Excel

Business goal:
Download dashboard views and release data as Excel files for offline analysis or reporting.

Domain context:
The API supports spreadsheet exports for key dashboards such as procurement activity, bid timeline, competition, procurement method, cancellation, cost effectiveness, and item classification.

Starting point:
Pre-existing releases and, for localized chart titles/series, translation values.

Required execution workflow:
1. Use function `excelExportUsingGET` (`GET /api/ocds/excelExport`) with optional `year=2020`, `pageNumber=0`, `pageSize=300`, and common filters to export release rows.
2. Use function `procurementActivityExcelChartUsingGET` (`GET /api/ocds/procurementActivityExcelChart`) with `language=en_US`, `year=2020`, `monthly=false`, and common filters to export activity counts.
3. Use any chart export needed for the domain view: `averageNumberBidsExcelChartUsingGET`, `bidTimelineExcelChartUsingGET`, `bidSelectionExcelChartUsingGET`, `procurementMethodExcelChartUsingGET`, `costEffectivenessExcelChartUsingGET`, `cancelledFundingExcelChartUsingGET`, `cancelledTendersByYearByRationaleExcelChartUsingGET`, `cancelledFundingPercentageExcelChartUsingGET`, `numberCancelledFundingExcelChartUsingGET`, `percentTendersUsingEBidExcelChartUsingGET`, `numberTendersUsingEBidExcelChartUsingGET`, `percentTendersUsingEgpExcelChartUsingGET`, `tendersWithLinkedProcurementPlanExcelChartUsingGET`, or `numberOfTendersByItemExcelChartUsingGET` with the same filter values.

Optional verification workflow:
1. Use the corresponding JSON data function before export, such as `countTendersByYearUsingGET` for procurement activity, `averageNumberOfTenderersUsingGET` for average bids, or `costEffectivenessTenderAwardAmountUsingGET` for cost effectiveness.
2. Inspect the HTTP response headers for spreadsheet content type and `Content-Disposition` filename.

Existing-state shortcuts:
- If the caller trusts the export endpoint, JSON preview functions can be skipped.
- Direct database setup must include the fields required by the underlying chart function.
- Existing cached chart data can satisfy repeated export calls, but the export function itself is still required to produce the file.

Parameter and value bindings:
- Reuse the same `year`, `month`, `monthly`, filter ids, and value ranges between JSON preview and Excel export.
- `language` binds to translation lookup for chart title and series names.
- Export functions write binary XLSX content to the HTTP response rather than returning JSON.

Business result:
The caller receives an Excel workbook. No persisted service state changes.

Constraints and invariants:
- Language is validated by `LangYearFilterPagingRequest`; accepted values depend on the custom language validator and available translation files.
- Exported chart data is assembled by invoking the corresponding controller functions internally.
- Empty data can still produce an Excel file with empty categories/series depending on export controller logic.

Failure and exceptional cases:
- Failing function: `procurementActivityExcelChartUsingGET`
  - Failure condition: invalid or unsupported `language`.
  - Why it fails: language validation and translation lookup are applied before writing the workbook.
  - Violated prerequisite or constraint: supported UI language.
- Failing function: `excelExportUsingGET`
  - Failure condition: invalid page or year filters.
  - Why it fails: shared request validation applies.
  - Violated prerequisite or constraint: valid filter request.

Implementation notes:
Export endpoints are side-effect-free but write directly to `HttpServletResponse`. They are not JSON APIs despite appearing in the same OpenAPI service.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Create, Import, Update, or Delete Procurement Releases Through the REST API

Priority:
Critical domain gap

Expected business goal:
A data manager would expect to create or import new OCDS releases, correct release data, or delete invalid records through service APIs.

Why it is unsupported:
The documented functions are read, aggregate, lookup, or export operations. No OpenAPI path or controller method provides release create, update, delete, validation, or import execution.

Existing functions considered:
- `ocdsReleasesUsingGET`: lists releases but cannot create or mutate them.
- `ocdsByOcidUsingGET`: retrieves one release but cannot update it.
- `excelExportUsingGET`: exports releases but cannot import spreadsheet data.
- Dashboard aggregate functions: read from MongoDB but do not persist changes.

Missing capability:
Missing POST/PUT/PATCH/DELETE release endpoints, import endpoint, schema validation endpoint, and persistence workflow.

Proof that function composition is insufficient:
Chaining lookup and export functions can only read existing data. It cannot create a new `release` document, add an award, change tender status, correct a supplier id, or remove invalid data. Direct database insertion is possible outside the API, but it is not equivalent to an API-supported domain workflow because it bypasses validation, auditability, and any intended import rules.

Evidence from existing functions/source:
All documented controllers use MongoDB `find` or `aggregate`, or write an Excel response. No documented endpoint calls repository save/delete for releases.

Business impact:
The dashboard API cannot be used as a complete procurement data management service. Data freshness and correction depend on out-of-band import or database processes.

### Missing Behavior 2: Recalculate or Repair Risk Flags Through the API

Priority:
Critical domain gap

Expected business goal:
A risk analyst would expect to recalculate indicators after new releases are imported or corrected.

Why it is unsupported:
Flag endpoints only read precomputed `flags` fields. There is no documented API to run flag processors or refresh stored flag state.

Existing functions considered:
- `releaseFlagSearchUsingGET`: finds releases where a specific flag is already true.
- `flagStatsUsingGET`: aggregates already stored true/false/null indicator values.
- `ocdsReleasesUsingGET` with `flagged=true`: only checks whether any flaggedStats element exists.

Missing capability:
Missing flag recalculation endpoint, batch processor trigger, status endpoint, and stale-flag detection.

Proof that function composition is insufficient:
Calling stats and search functions cannot create `flags.flaggedStats`, cannot recompute boolean indicator fields, and cannot distinguish stale flag values from current values after source data changes. Delete-and-reimport outside the API is not equivalent because the public REST API still cannot trigger or verify the recalculation process.

Evidence from existing functions/source:
`AbstractFlagReleaseSearchController` and `AbstractFlagStatsController` only query fields under `flags`. Flag processor classes exist in source, but documented REST functions do not expose processor execution.

Business impact:
Risk dashboards can become stale or empty if flag processing is not run externally. Users cannot repair risk state through the API.

### Missing Behavior 3: Retrieve or Manage Individual Tenders, Awards, Plans, Items, and Locations as First-Class Resources

Priority:
Important robustness gap

Expected business goal:
A user would reasonably expect to retrieve a tender, award, plan, tender item, or location directly by its own id.

Why it is unsupported:
The API exposes release-level retrieval and aggregate views, but not child-resource retrieval or lifecycle operations.

Existing functions considered:
- `ocdsByOcidUsingGET`: returns the whole release containing child data, if the release OCID is known.
- `tendersByItemClassificationUsingGET`: aggregates items but does not retrieve item records.
- `fundingByTenderDeliveryLocationUsingGET`: aggregates delivery locations but does not expose tender-item detail.
- `topTenLargestAwardsUsingGET`: returns projected award fields but not a stable award-detail endpoint.

Missing capability:
Missing `/tender/{id}`, `/award/{id}`, `/planning/{id}`, `/item/{id}`, and `/location/{id}` style endpoints and child-resource scoping rules.

Proof that function composition is insufficient:
If a caller only knows an award id or tender item classification, existing functions cannot deterministically retrieve the owning release and exact child object. Aggregate endpoints discard source identity, and release listing requires broad filters followed by client-side scanning.

Evidence from existing functions/source:
Release retrieval is keyed by OCID, project id, or planning bid number only. Aggregations use `$project`, `$group`, and `$unwind`, losing full child-resource context.

Business impact:
Debugging, audit review, drill-down, and correction workflows are cumbersome or impossible through the API alone.

### Missing Behavior 4: Validate Filter Values and Cross-Resource Relationships Before Running Analytics

Priority:
Important robustness gap

Expected business goal:
A dashboard client would expect to validate that selected supplier, procuring entity, method, location, and value filters are meaningful and compatible.

Why it is unsupported:
Lookup endpoints can list values, but no function validates a complete filter set or explains why a filter combination returns no results.

Existing functions considered:
- `searchTextUsingGET_2`: can find procuring entities but does not validate dashboard filters.
- `bidSelectionMethodsUsingGET`: lists method values but does not validate combinations.
- `tenderValueIntervalUsingGET`: returns bounds but does not validate all filters.
- `ocdsReleasesUsingGET`: returns matching records or an empty list, without diagnostics.

Missing capability:
Missing filter validation endpoint, relationship existence checks, and structured empty-result explanations.

Proof that function composition is insufficient:
A client can call many lookup endpoints and still cannot know whether `supplierId=SUP1` ever appears with `procuringEntityId=PE1`, `year=2020`, and `bidSelectionMethod=METHOD1` except by running the final query and interpreting emptiness manually. It also cannot distinguish invalid ids from valid ids with no matching releases.

Evidence from existing functions/source:
Shared filter criteria are direct MongoDB `in`, `not in`, range, and equality criteria. There is no validation layer that checks relationships before query execution.

Business impact:
Users can create confusing empty dashboards, and clients must implement their own diagnostics.

### Missing Behavior 5: Transactionally Consistent Dashboard Snapshot Across Multiple Metrics

Priority:
Important robustness gap

Expected business goal:
A dashboard page should show counts, percentages, values, and exports from the same data snapshot.

Why it is unsupported:
Composite dashboard views require multiple independent API calls, and there is no snapshot id, as-of timestamp, ETag, cursor, or transaction boundary.

Existing functions considered:
- `countTendersByYearUsingGET`, `countAwardsByYearUsingGET`, and `countBidPlansByYearUsingGET`: each computes separately.
- `costEffectivenessTenderAwardAmountUsingGET`: combines two internal async calls but does not expose a snapshot token.
- Excel export functions: internally call data functions but do not coordinate with separate JSON calls.

Missing capability:
Missing snapshot token, consistent read timestamp, ETag/version return, and multi-metric batch endpoint.

Proof that function composition is insufficient:
Sequentially calling metric functions cannot guarantee that the underlying MongoDB collection did not change between calls. Reusing identical filters ensures semantic scope, but not temporal consistency.

Evidence from existing functions/source:
Controllers call Mongo aggregations independently. No function returns or accepts a dataset version, import batch id, cursor, or ETag.

Business impact:
Dashboard panels can disagree during imports or data refreshes, weakening trust in reported totals and percentages.

### Missing Behavior 6: Strong API-Level Validation for Numeric and Domain Constraints

Priority:
Important robustness gap

Expected business goal:
The API should reject invalid thresholds, unsupported grouping values, malformed ObjectIds, and nonsensical date intervals with clear errors.

Why it is unsupported:
Some bean validation exists, but many domain constraints are unchecked.

Existing functions considered:
- `frequentSuppliersTimeIntervalUsingGET`: accepts `intervalDays`, `maxAwards`, and `now`.
- `costEffectivenessTenderAmountUsingGET`: accepts `groupByCategory`.
- Common filter functions accept `contrMethod`, value ranges, and inclusion/exclusion filters.

Missing capability:
Missing positive validation for `intervalDays`, non-negative validation for `maxAwards`, enum validation for `groupByCategory`, ObjectId validation for `contrMethod`, and range validation that `min <= max`.

Proof that function composition is insufficient:
Lookup functions can suggest values but cannot enforce complete request validity. Invalid values either produce empty results, runtime exceptions, or misleading output.

Evidence from existing functions/source:
`intervalDays` and `maxAwards` are plain request parameters without validation. `groupByCategory` returns null for unsupported values. `contrMethod` is converted to `ObjectId` after accepting alphanumeric strings.

Business impact:
Clients can trigger runtime errors or produce ambiguous dashboards with invalid filters.

### Missing Behavior 7: Authenticated Access, Ownership, and Audit Controls

Priority:
Important robustness gap

Expected business goal:
A production procurement system may need authenticated roles, scoped access, and auditability for sensitive records or administrative exports.

Why it is unsupported:
The documented dashboard endpoints are public read/export endpoints with no ownership or session binding in the documented functions.

Existing functions considered:
- All lookup and analytics functions: accept filters but no authorization context.
- `excelExportUsingGET`: exports filtered release data without documented auth parameters.
- Organization retrieval functions: expose organization records by id and role.

Missing capability:
Missing authentication, authorization, tenant/owner scoping, and audit log endpoints.

Proof that function composition is insufficient:
Passing filters is not an ownership check. A caller can request any documented filter value if the endpoint is reachable, and no combination of read functions can enforce caller-specific access policy.

Evidence from existing functions/source:
The relevant controllers do not consume authenticated principal, session, owner id, or authorization headers in the documented request models.

Business impact:
The API is suitable for public dashboard data but not for restricted procurement administration without external access controls.

### Missing Behavior 8: Explain Empty or Partial Analytics Results

Priority:
API ergonomics gap

Expected business goal:
A dashboard should explain whether a metric is empty because data is missing, filters are too narrow, dates are absent, awards are inactive, or locations lack coordinates.

Why it is unsupported:
Analytics endpoints return aggregate lists only; they do not return exclusion counts or reason codes.

Existing functions considered:
- `qualityAverageTenderPeriodUsingGET`: gives one completeness metric but only for tender-period dates.
- `qualityFundingByTenderDeliveryLocationUsingGET`: gives one location completeness metric.
- `averageAwardPeriodUsingGET`, `plannedFundingByLocationUsingGET`, and `frequentTenderersUsingGET`: silently filter out many records.

Missing capability:
Missing diagnostics endpoint and per-metric exclusion breakdown.

Proof that function composition is insufficient:
Quality endpoints cover only selected dimensions and cannot explain every aggregate. For example, no existing function can say how many planned funding records were excluded specifically because coordinates were missing versus plan approval date missing.

Evidence from existing functions/source:
Most controllers use Mongo `match` stages that filter missing fields without returning rejected counts.

Business impact:
Users may misinterpret missing or low metrics as business reality rather than data completeness limitations.

## Cross-Behavior Observations

- The documented API is read-only. Business state is established outside the REST API through import jobs, direct database state, or other modules.
- GET and POST are generally equivalent for documented endpoints; both bind request parameters to model attributes rather than using distinct command semantics.
- Shared filters bind directly to nested Mongo fields. There is little domain validation beyond basic page/year/month/text constraints.
- Many metrics silently exclude incomplete records. Quality endpoints exist for some, but not all, data completeness dimensions.
- Role-specific organization lookup is implemented using organization roles, while some OpenAPI text says `types`.
- Several source endpoints are not present in `ocvn.json`, including additional corruption-risk dashboard, crosstab, and indicator mapping endpoints.
- Some OpenAPI descriptions disagree with implementation: package lookup by planning bid number is described as OCID lookup, average award period wording is wrong, top-ten largest tenders are not filtered to active status in source, and frequent tenderers is implemented as supplier/tenderer pairing rather than all co-tenderer pairs.
- Caching is used for many controllers. The API does not expose cache invalidation or dataset versioning, so data freshness depends on external processes.
- Excel exports are response-streaming functions and can call multiple JSON metric functions internally.

## Coverage Summary

Supported domain areas:
Release/package retrieval, organization/reference lookup, procurement activity counts, timing metrics, competition/e-procurement percentages, cancellation analytics, value and cost-effectiveness analytics, item/location analytics, top rankings, repeated relationship signals, precomputed flag inspection, and Excel exports.

Partially supported domain areas:
Data quality, risk analysis, dashboard filter discovery, and geographic analysis are supported through selected read metrics but lack full diagnostics, recalculation, validation, and drill-down.

Unsupported domain areas:
REST-based data creation/import/update/delete, first-class tender/award/plan lifecycle management, flag recalculation, transactional dashboard snapshots, authenticated ownership controls, comprehensive filter validation, and explanatory analytics diagnostics.