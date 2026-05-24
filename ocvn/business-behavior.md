# Domain-Level Behavior Analysis

## Domain Summary

The `ocvn` service is described by its OpenAPI contract as: These endpoints are used to feed the open contracting dashboard

The core business concepts are:

- average-tender-and-award-periods-controller: endpoint group for average tender and award periods controller behavior.
- average-number-of-tenderers-controller: endpoint group for average number of tenderers controller behavior.
- tender-percentages-controller: endpoint group for tender percentages controller behavior.
- tenders-awards-value-intervals: endpoint group for tenders awards value intervals behavior.
- cost-effectiveness-visuals-controller: endpoint group for cost effectiveness visuals controller behavior.
- count-plans-tenders-awards-controller: endpoint group for count plans tenders awards controller behavior.
- flag-i-003-release-search-controller: endpoint group for flag i 003 release search controller behavior.
- flag-i-003-stats-controller: endpoint group for flag i 003 stats controller behavior.
- flag-i-004-release-search-controller: endpoint group for flag i 004 release search controller behavior.
- flag-i-004-stats-controller: endpoint group for flag i 004 stats controller behavior.
- flag-i-007-release-search-controller: endpoint group for flag i 007 release search controller behavior.
- flag-i-007-stats-controller: endpoint group for flag i 007 stats controller behavior.
- flag-i-019-release-search-controller: endpoint group for flag i 019 release search controller behavior.
- flag-i-019-stats-controller: endpoint group for flag i 019 stats controller behavior.
- flag-i-038-release-search-controller: endpoint group for flag i 038 release search controller behavior.
- flag-i-038-stats-controller: endpoint group for flag i 038 stats controller behavior.
- flag-i-077-release-search-controller: endpoint group for flag i 077 release search controller behavior.
- flag-i-077-stats-controller: endpoint group for flag i 077 stats controller behavior.
- flag-i-180-release-search-controller: endpoint group for flag i 180 release search controller behavior.
- flag-i-180-stats-controller: endpoint group for flag i 180 stats controller behavior.
- frequent-suppliers-time-interval-controller: endpoint group for frequent suppliers time interval controller behavior.
- frequent-tenderers-controller: endpoint group for frequent tenderers controller behavior.
- funding-by-location-controller: endpoint group for funding by location controller behavior.
- average-number-of-tenderers-excel-controller: endpoint group for average number of tenderers excel controller behavior.
- Additional endpoint groups: 27 more groups declared in the API contract.

Contract-level limits: this document captures externally visible business behavior from the API contract. Implementation-only validation, persistence side effects, authorization rules, and asynchronous processing details may be stricter than what is visible here.

## Available Function Inventory

### average-number-of-tenderers-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `calculate average number of tenderers by year the endpoint can be filteredby year read from tender tender period start date the number of tenderers are read from tender number of tenderers` | `GET /api/averageNumberOfTenderers` | Calculate average number of tenderers, by year. . |
| `calculate average number of tenderers by year the endpoint can be filteredby year read from tender tender period start date the number of tenderers are read from tender number of tenderers` | `POST /api/averageNumberOfTenderers` | Calculate average number of tenderers, by year. . |

### average-number-of-tenderers-excel-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `exports average number of bids dashboard in excel format` | `GET /api/ocds/averageNumberBidsExcelChart` | Exports *Average number of bids* dashboard in Excel format. |
| `exports average number of bids dashboard in excel format` | `POST /api/ocds/averageNumberBidsExcelChart` | Exports *Average number of bids* dashboard in Excel format. |

### average-tender-and-award-periods-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `calculates the average award period per each year the year is taken from awards date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date the award has to be active` | `GET /api/averageAwardPeriod` | Calculates the average award period, per each year. . |
| `calculates the average award period per each year the year is taken from awards date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date the award has to be active` | `POST /api/averageAwardPeriod` | Calculates the average award period, per each year. . |
| `calculates the average tender period per each year the year is taken from tender tender period start date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date` | `GET /api/averageTenderPeriod` | Calculates the average tender period, per each year. . |
| `calculates the average tender period per each year the year is taken from tender tender period start date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date` | `POST /api/averageTenderPeriod` | Calculates the average tender period, per each year. . |
| `quality indicator for average award period endpoint showing the percentage of awards that have start and end dates vs the total tenders in the system` | `GET /api/qualityAverageAwardPeriod` | Quality indicator for averageAwardPeriod endpoint, showing the percentage of awards that have start and end dates vs the total tenders in the system. |
| `quality indicator for average award period endpoint showing the percentage of awards that have start and end dates vs the total tenders in the system` | `POST /api/qualityAverageAwardPeriod` | Quality indicator for averageAwardPeriod endpoint, showing the percentage of awards that have start and end dates vs the total tenders in the system. |
| `quality indicator for average tender period endpoint showing the percentage of tenders that have start and end dates vs the total tenders in the system` | `GET /api/qualityAverageTenderPeriod` | Quality indicator for averageTenderPeriod endpoint, showing the percentage of tenders that have start and end dates vs the total tenders in the system. |
| `quality indicator for average tender period endpoint showing the percentage of tenders that have start and end dates vs the total tenders in the system` | `POST /api/qualityAverageTenderPeriod` | Quality indicator for averageTenderPeriod endpoint, showing the percentage of tenders that have start and end dates vs the total tenders in the system. |

### average-tender-and-awards-excel-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `exports bid timeline dashboard in excel format` | `GET /api/ocds/bidTimelineExcelChart` | Exports *Bid Timeline* dashboard in Excel format. |
| `exports bid timeline dashboard in excel format` | `POST /api/ocds/bidTimelineExcelChart` | Exports *Bid Timeline* dashboard in Excel format. |

### bid-selection-method-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `display the available bid selection methods these are taken from tender procurement method details` | `GET /api/ocds/bidSelectionMethod/all` | Display the available bid selection methods. . |
| `display the available bid selection methods these are taken from tender procurement method details` | `POST /api/ocds/bidSelectionMethod/all` | Display the available bid selection methods. . |

### bid-types-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `display the available bid types these are the classification entities in ocds` | `GET /api/ocds/bidType/all` | Display the available bid types. . |
| `display the available bid types these are the classification entities in ocds` | `POST /api/ocds/bidType/all` | Display the available bid types. . |

### buyer-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `lists all buyers in the database suppliers are organizations that have the label buyer assigned to organization types array allows full text search using the text parameter` | `GET /api/ocds/organization/buyer/all` | Lists all buyers in the database. . |
| `lists all buyers in the database suppliers are organizations that have the label buyer assigned to organization types array allows full text search using the text parameter` | `POST /api/ocds/organization/buyer/all` | Lists all buyers in the database. . |
| `finds buyer entity by the given id` | `GET /api/ocds/organization/buyer/id/{id}` | Finds buyer entity by the given id. |
| `finds buyer entity by the given id` | `POST /api/ocds/organization/buyer/id/{id}` | Finds buyer entity by the given id. |

### city-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `org cities` | `GET /api/ocds/city/all` | orgCities. |
| `org cities` | `POST /api/ocds/city/all` | orgCities. |
| `cities search` | `GET /api/ocds/city/search` | citiesSearch. |
| `cities search` | `POST /api/ocds/city/search` | citiesSearch. |

### contr-method-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `contr methods` | `GET /api/ocds/contrMethod/all` | contrMethods. |
| `contr methods` | `POST /api/ocds/contrMethod/all` | contrMethods. |

### cost-effectiveness-excel-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `exports cost effectiveness dashboard in excel format` | `GET /api/ocds/costEffectivenessExcelChart` | Exports *Cost effectiveness* dashboard in Excel format. |
| `exports cost effectiveness dashboard in excel format` | `POST /api/ocds/costEffectivenessExcelChart` | Exports *Cost effectiveness* dashboard in Excel format. |

### cost-effectiveness-visuals-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `cost effectiveness of awards displays the total amount of active awards grouped by year the tender entity for each award has to have amount value the year is calculated from tender tender period start date` | `GET /api/costEffectivenessAwardAmount` | Cost effectiveness of Awards: Displays the total amount of active awards grouped by year.The tender entity, for each award, has to have amount value. . |
| `cost effectiveness of awards displays the total amount of active awards grouped by year the tender entity for each award has to have amount value the year is calculated from tender tender period start date` | `POST /api/costEffectivenessAwardAmount` | Cost effectiveness of Awards: Displays the total amount of active awards grouped by year.The tender entity, for each award, has to have amount value. . |
| `cost effectiveness of tenders displays the total amount of the active tenders that have active awards grouped by year only tenders status activeare taken into account the year is calculated from tender period start date` | `GET /api/costEffectivenessTenderAmount` | Cost effectiveness of Tenders: Displays the total amount of the active tenders that have active awards, grouped by year. . |
| `cost effectiveness of tenders displays the total amount of the active tenders that have active awards grouped by year only tenders status activeare taken into account the year is calculated from tender period start date` | `POST /api/costEffectivenessTenderAmount` | Cost effectiveness of Tenders: Displays the total amount of the active tenders that have active awards, grouped by year. . |
| `aggregated version of api cost effectiveness tender amount and api cost effectiveness award amount this endpoint aggregates the responses from the specified endpoints per year responds to the same filters` | `GET /api/costEffectivenessTenderAwardAmount` | Aggregated version of /api/costEffectivenessTenderAmount and /api/costEffectivenessAwardAmount.This endpoint aggregates the responses from the specified endpoints, per year. . |
| `aggregated version of api cost effectiveness tender amount and api cost effectiveness award amount this endpoint aggregates the responses from the specified endpoints per year responds to the same filters` | `POST /api/costEffectivenessTenderAwardAmount` | Aggregated version of /api/costEffectivenessTenderAmount and /api/costEffectivenessAwardAmount.This endpoint aggregates the responses from the specified endpoints, per year. . |

### count-plans-tenders-awards-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `count the awards and group the results by year the year is calculated from the awards date field` | `GET /api/countAwardsByYear` | Count the awards and group the results by year. . |
| `count the awards and group the results by year the year is calculated from the awards date field` | `POST /api/countAwardsByYear` | Count the awards and group the results by year. . |
| `count of bid plans by year this will count the releases that have the fieldplanning bid plan project date approve populated the year grouping is taken from planning bid plan project date approve` | `GET /api/countBidPlansByYear` | Count of bid plans, by year. . |
| `count of bid plans by year this will count the releases that have the fieldplanning bid plan project date approve populated the year grouping is taken from planning bid plan project date approve` | `POST /api/countBidPlansByYear` | Count of bid plans, by year. . |
| `count the tenders and group the results by year the year is calculated from tender tender period start date` | `GET /api/countTendersByYear` | Count the tenders and group the results by year. . |
| `count the tenders and group the results by year the year is calculated from tender tender period start date` | `POST /api/countTendersByYear` | Count the tenders and group the results by year. . |

### excel-export-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `export releases in excel format` | `GET /api/ocds/excelExport` | Export releases in Excel format. |
| `export releases in excel format` | `POST /api/ocds/excelExport` | Export releases in Excel format. |

### flag-i-003-release-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search releases by flag i003` | `GET /api/flags/i003/releases` | Search releases by flag i003. |
| `search releases by flag i003` | `POST /api/flags/i003/releases` | Search releases by flag i003. |

### flag-i-003-stats-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `stats for flag i003` | `GET /api/flags/i003/stats` | Stats for flag i003. |
| `stats for flag i003` | `POST /api/flags/i003/stats` | Stats for flag i003. |

### flag-i-004-release-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search releases by flag i004` | `GET /api/flags/i004/releases` | Search releases by flag i004. |
| `search releases by flag i004` | `POST /api/flags/i004/releases` | Search releases by flag i004. |

### flag-i-004-stats-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `stats for flag i004` | `GET /api/flags/i004/stats` | Stats for flag i004. |
| `stats for flag i004` | `POST /api/flags/i004/stats` | Stats for flag i004. |

### flag-i-007-release-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search releases by flag i007` | `GET /api/flags/i007/releases` | Search releases by flag i007. |
| `search releases by flag i007` | `POST /api/flags/i007/releases` | Search releases by flag i007. |

### flag-i-007-stats-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `stats for flag i007` | `GET /api/flags/i007/stats` | Stats for flag i007. |
| `stats for flag i007` | `POST /api/flags/i007/stats` | Stats for flag i007. |

### flag-i-019-release-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search releases by flag i019` | `GET /api/flags/i019/releases` | Search releases by flag i019. |
| `search releases by flag i019` | `POST /api/flags/i019/releases` | Search releases by flag i019. |

### flag-i-019-stats-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `stats for flag i019` | `GET /api/flags/i019/stats` | Stats for flag i019. |
| `stats for flag i019` | `POST /api/flags/i019/stats` | Stats for flag i019. |

### flag-i-038-release-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search releases by flag i038` | `GET /api/flags/i038/releases` | Search releases by flag i038. |
| `search releases by flag i038` | `POST /api/flags/i038/releases` | Search releases by flag i038. |

### flag-i-038-stats-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `stats for flag i038` | `GET /api/flags/i038/stats` | Stats for flag i038. |
| `stats for flag i038` | `POST /api/flags/i038/stats` | Stats for flag i038. |

### flag-i-077-release-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search releases by flag i077` | `GET /api/flags/i077/releases` | Search releases by flag i077. |
| `search releases by flag i077` | `POST /api/flags/i077/releases` | Search releases by flag i077. |

### flag-i-077-stats-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `stats for flag i077` | `GET /api/flags/i077/stats` | Stats for flag i077. |
| `stats for flag i077` | `POST /api/flags/i077/stats` | Stats for flag i077. |

### flag-i-180-release-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `search releases by flag i180` | `GET /api/flags/i180/releases` | Search releases by flag i180. |
| `search releases by flag i180` | `POST /api/flags/i180/releases` | Search releases by flag i180. |

### flag-i-180-stats-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `stats for flag i180` | `GET /api/flags/i180/stats` | Stats for flag i180. |
| `stats for flag i180` | `POST /api/flags/i180/stats` | Stats for flag i180. |

### frequent-suppliers-time-interval-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `returns the frequent suppliers of a procuring entity split by a time interval the time interval is a parameter and represents the number of days to take as interval starting with today and going back till the last award date the awards are grouped by procuring entity supplier and the time interval max awards parameter is used to designate what is the maximum number of awards granted to one supplier by the same procuring entity inside one time interval the default value for max awards is 3 days and the default value for interval days is 365` | `GET /api/frequentSuppliersTimeInterval` | Returns the frequent suppliers of a procuringEntity split by a time interval. . |
| `returns the frequent suppliers of a procuring entity split by a time interval the time interval is a parameter and represents the number of days to take as interval starting with today and going back till the last award date the awards are grouped by procuring entity supplier and the time interval max awards parameter is used to designate what is the maximum number of awards granted to one supplier by the same procuring entity inside one time interval the default value for max awards is 3 days and the default value for interval days is 365` | `POST /api/frequentSuppliersTimeInterval` | Returns the frequent suppliers of a procuringEntity split by a time interval. . |

### frequent-tenderers-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `detect frequent pairs of tenderers that apply together to bids we are only showing pairs if they applied to more than one bid together we are sorting the results after the number of occurences descending you can use all the filters that are available along with pagination options` | `GET /api/frequentTenderers` | Detect frequent pairs of tenderers that apply together to bids.We are only showing pairs if they applied to more than one bid together.We are sorting the results after the number of occurences, descending.You can use all the filters that ar. |
| `detect frequent pairs of tenderers that apply together to bids we are only showing pairs if they applied to more than one bid together we are sorting the results after the number of occurences descending you can use all the filters that are available along with pagination options` | `POST /api/frequentTenderers` | Detect frequent pairs of tenderers that apply together to bids.We are only showing pairs if they applied to more than one bid together.We are sorting the results after the number of occurences, descending.You can use all the filters that ar. |

### funding-by-location-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `total estimated funding tender value grouped by tender items delivery location and also grouped by year the endpoint also returns the count of tenders for each location it responds to all filters the year is calculated based on tender tender period start date` | `GET /api/fundingByTenderDeliveryLocation` | Total estimated funding (tender.value) grouped by tender.items.deliveryLocation and also grouped by year. . |
| `total estimated funding tender value grouped by tender items delivery location and also grouped by year the endpoint also returns the count of tenders for each location it responds to all filters the year is calculated based on tender tender period start date` | `POST /api/fundingByTenderDeliveryLocation` | Total estimated funding (tender.value) grouped by tender.items.deliveryLocation and also grouped by year. . |
| `planned funding by location by year returns the total amount of planning budget available per planning budget project location grouped by year this will return full location information including geocoding data responds only to the procuring entity id filter tender procuring entity id` | `GET /api/plannedFundingByLocation` | Planned funding by location by year. . |
| `planned funding by location by year returns the total amount of planning budget available per planning budget project location grouped by year this will return full location information including geocoding data responds only to the procuring entity id filter tender procuring entity id` | `POST /api/plannedFundingByLocation` | Planned funding by location by year. . |
| `calculates percentage of releases with tender with at least one specified delivery location that is the array tender items delivery location has to have items filters out stub tenders therefore tender tender period start date has to exist` | `GET /api/qualityFundingByTenderDeliveryLocation` | Calculates percentage of releases with tender with at least one specified delivery location, that is the array tender.items.deliveryLocation has to have items.Filters out stub tenders, therefore tender.tenderPeriod.startDate has to exist. |
| `calculates percentage of releases with tender with at least one specified delivery location that is the array tender items delivery location has to have items filters out stub tenders therefore tender tender period start date has to exist` | `POST /api/qualityFundingByTenderDeliveryLocation` | Calculates percentage of releases with tender with at least one specified delivery location, that is the array tender.items.deliveryLocation has to have items.Filters out stub tenders, therefore tender.tenderPeriod.startDate has to exist. |
| `calculates percentage of releases with planning with at least one specified location that is the array planning budget project location has to be initialzied filters out stub planning therefore planning budget amount has to exist responds only to the procuring entity id filter tender procuring entity id` | `GET /api/qualityPlannedFundingByLocation` | Calculates percentage of releases with planning with at least one specified location, that is the array planning.budget.projectLocation has to be initialzied.Filters out stub planning, therefore planning.budget.amount has to exist.Responds . |
| `calculates percentage of releases with planning with at least one specified location that is the array planning budget project location has to be initialzied filters out stub planning therefore planning budget amount has to exist responds only to the procuring entity id filter tender procuring entity id` | `POST /api/qualityPlannedFundingByLocation` | Calculates percentage of releases with planning with at least one specified location, that is the array planning.budget.projectLocation has to be initialzied.Filters out stub planning, therefore planning.budget.amount has to exist.Responds . |

### location-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `locations all` | `GET /api/ocds/location/all` | locationsAll. |
| `locations all` | `POST /api/ocds/location/all` | locationsAll. |
| `locations search` | `GET /api/ocds/location/search` | locationsSearch. |
| `locations search` | `POST /api/ocds/location/search` | locationsSearch. |

### ocds-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `returns all available packages filtered by the given criteria this will contain the ocds package information metadata about publisher plus the release itself` | `GET /api/ocds/package/all` | Returns all available packages, filtered by the given criteria.This will contain the OCDS package information (metadata about publisher) plus the release itself. |
| `returns all available packages filtered by the given criteria this will contain the ocds package information metadata about publisher plus the release itself` | `POST /api/ocds/package/all` | Returns all available packages, filtered by the given criteria.This will contain the OCDS package information (metadata about publisher) plus the release itself. |
| `returns a release package for the given project id the project id is read from planning budget project id this will contain the ocds package information metadata about publisher plus the release itself` | `GET /api/ocds/package/budgetProjectId/{projectId}` | Returns a release package for the given project id. . |
| `returns a release package for the given project id the project id is read from planning budget project id this will contain the ocds package information metadata about publisher plus the release itself` | `POST /api/ocds/package/budgetProjectId/{projectId}` | Returns a release package for the given project id. . |
| `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself` | `GET /api/ocds/package/ocid/{ocid}` | Returns a release package for the given open contracting id (OCID).This will contain the OCDS package information (metadata about publisher) plus the release itself. |
| `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself` | `POST /api/ocds/package/ocid/{ocid}` | Returns a release package for the given open contracting id (OCID).This will contain the OCDS package information (metadata about publisher) plus the release itself. |
| `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself` | `GET /api/ocds/package/planningBidNo/{bidNo}` | Returns a release package for the given open contracting id (OCID).This will contain the OCDS package information (metadata about publisher) plus the release itself. |
| `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself` | `POST /api/ocds/package/planningBidNo/{bidNo}` | Returns a release package for the given open contracting id (OCID).This will contain the OCDS package information (metadata about publisher) plus the release itself. |
| `resturns all available releases filtered by the given criteria` | `GET /api/ocds/release/all` | Resturns all available releases, filtered by the given criteria. |
| `resturns all available releases filtered by the given criteria` | `POST /api/ocds/release/all` | Resturns all available releases, filtered by the given criteria. |
| `returns a release entity for the given project id the project id is read from planning budget project id` | `GET /api/ocds/release/budgetProjectId/{projectId}` | Returns a release entity for the given project id. . |
| `returns a release entity for the given project id the project id is read from planning budget project id` | `POST /api/ocds/release/budgetProjectId/{projectId}` | Returns a release entity for the given project id. . |
| `returns a release entity for the given open contracting id ocid` | `GET /api/ocds/release/ocid/{ocid}` | Returns a release entity for the given open contracting id (OCID). |
| `returns a release entity for the given open contracting id ocid` | `POST /api/ocds/release/ocid/{ocid}` | Returns a release entity for the given open contracting id (OCID). |
| `returns a release entity for the given planning bid number the planning bid number is taken from planning bid no` | `GET /api/ocds/release/planningBidNo/{bidNo}` | Returns a release entity for the given Planning Bid Number.The planning bid number is taken from planning.bidNo. |
| `returns a release entity for the given planning bid number the planning bid number is taken from planning bid no` | `POST /api/ocds/release/planningBidNo/{bidNo}` | Returns a release entity for the given Planning Bid Number.The planning bid number is taken from planning.bidNo. |

### org-department-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `org departments` | `GET /api/ocds/orgDepartment/all` | orgDepartments. |
| `org departments` | `POST /api/ocds/orgDepartment/all` | orgDepartments. |
| `department search` | `GET /api/ocds/orgDepartment/search` | departmentSearch. |
| `department search` | `POST /api/ocds/orgDepartment/search` | departmentSearch. |

### org-group-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `org groups` | `GET /api/ocds/orgGroup/all` | orgGroups. |
| `org groups` | `POST /api/ocds/orgGroup/all` | orgGroups. |
| `groups search` | `GET /api/ocds/orgGroup/search` | groupsSearch. |
| `groups search` | `POST /api/ocds/orgGroup/search` | groupsSearch. |

### organization-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `lists all organizations in the database allows full text search using the text parameter` | `GET /api/ocds/organization/all` | Lists all organizations in the database. . |
| `lists all organizations in the database allows full text search using the text parameter` | `POST /api/ocds/organization/all` | Lists all organizations in the database. . |
| `finds organization entity by the given id` | `GET /api/ocds/organization/id/{id}` | Finds organization entity by the given id. |
| `finds organization entity by the given id` | `POST /api/ocds/organization/id/{id}` | Finds organization entity by the given id. |
| `finds organization entities by the given list of ids comma separated` | `GET /api/ocds/organization/ids` | Finds organization entities by the given list of ids, comma separated. |
| `finds organization entities by the given list of ids comma separated` | `POST /api/ocds/organization/ids` | Finds organization entities by the given list of ids, comma separated. |

### percentage-awards-narrow-publication-dates

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `percentage of awards where award publication date award date is less than 7 days percentage should be by year the denominator for the percentage is the number of awards that have both awards date and awards published date` | `GET /api/percentageAwardsNarrowPublicationDates` | Percentage of awards where award publication date - award.date is less than 7 days. . |
| `percentage of awards where award publication date award date is less than 7 days percentage should be by year the denominator for the percentage is the number of awards that have both awards date and awards published date` | `POST /api/percentageAwardsNarrowPublicationDates` | Percentage of awards where award publication date - award.date is less than 7 days. . |

### procurement-activity-by-year-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `exports procurement activity by year dashboard in excel format` | `GET /api/ocds/procurementActivityExcelChart` | Exports *Procurement activity by year* dashboard in Excel format. |
| `exports procurement activity by year dashboard in excel format` | `POST /api/ocds/procurementActivityExcelChart` | Exports *Procurement activity by year* dashboard in Excel format. |

### procurement-method-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `display the available procurement methods these are taken from tender procurement method` | `GET /api/ocds/procurementMethod/all` | Display the available procurement methods. . |
| `display the available procurement methods these are taken from tender procurement method` | `POST /api/ocds/procurementMethod/all` | Display the available procurement methods. . |

### procuring-entity-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `lists all procuring entities in the database procuring entities are organizations that have the label procuring entity assigned to organization types array allows full text search using the text parameter` | `GET /api/ocds/organization/procuringEntity/all` | Lists all procuring entities in the database. . |
| `lists all procuring entities in the database procuring entities are organizations that have the label procuring entity assigned to organization types array allows full text search using the text parameter` | `POST /api/ocds/organization/procuringEntity/all` | Lists all procuring entities in the database. . |
| `finds procuring entities by the given id` | `GET /api/ocds/organization/procuringEntity/id/{id}` | Finds procuringEntities by the given id. |
| `finds procuring entities by the given id` | `POST /api/ocds/organization/procuringEntity/id/{id}` | Finds procuringEntities by the given id. |

### supplier-search-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `lists all suppliers in the database suppliers are organizations that have the label supplier assigned to organization types array allows full text search using the text parameter` | `GET /api/ocds/organization/supplier/all` | Lists all suppliers in the database. . |
| `lists all suppliers in the database suppliers are organizations that have the label supplier assigned to organization types array allows full text search using the text parameter` | `POST /api/ocds/organization/supplier/all` | Lists all suppliers in the database. . |
| `finds supplier by the given id` | `GET /api/ocds/organization/supplier/id/{id}` | Finds supplier by the given id. |
| `finds supplier by the given id` | `POST /api/ocds/organization/supplier/id/{id}` | Finds supplier by the given id. |

### tender-percentages-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `for all tenders that have both tender tender period start date and planning bid plan project date approvecalculates the number o days from planning bid plan project date approve to tender tender period start dateand creates the average groups results by tender year calculatedfrom tender tender period start date` | `GET /api/avgTimeFromPlanToTenderPhase` | For all tenders that have both tender.tenderPeriod.startDate and planning.bidPlanProjectDateApprovecalculates the number o days from planning.bidPlanProjectDateApprove to tender.tenderPeriod.startDateand creates the average. . |
| `for all tenders that have both tender tender period start date and planning bid plan project date approvecalculates the number o days from planning bid plan project date approve to tender tender period start dateand creates the average groups results by tender year calculatedfrom tender tender period start date` | `POST /api/avgTimeFromPlanToTenderPhase` | For all tenders that have both tender.tenderPeriod.startDate and planning.bidPlanProjectDateApprovecalculates the number o days from planning.bidPlanProjectDateApprove to tender.tenderPeriod.startDateand creates the average. . |
| `percent of awarded tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders with number of tenderers 0 this endpoint uses tender tender period start date to calculate the tender year` | `GET /api/percentTendersAwardedWithTwoOrMoreTenderers` | Percent of awarded tenders with >1 tenderer/bidderCount of tenders with numberOfTenderers >1 divided by total count of tenders with numberOfTenderers >0This endpoint uses tender.tenderPeriod.startDate to calculate the tender year. |
| `percent of awarded tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders with number of tenderers 0 this endpoint uses tender tender period start date to calculate the tender year` | `POST /api/percentTendersAwardedWithTwoOrMoreTenderers` | Percent of awarded tenders with >1 tenderer/bidderCount of tenders with numberOfTenderers >1 divided by total count of tenders with numberOfTenderers >0This endpoint uses tender.tenderPeriod.startDate to calculate the tender year. |
| `returns the percent of tenders that were cancelled grouped by year the year is taken from tender tender period start date the response also contains the total number of tenders and total number of cancelled tenders for each year` | `GET /api/percentTendersCancelled` | Returns the percent of tenders that were cancelled, grouped by year. . |
| `returns the percent of tenders that were cancelled grouped by year the year is taken from tender tender period start date the response also contains the total number of tenders and total number of cancelled tenders for each year` | `POST /api/percentTendersCancelled` | Returns the percent of tenders that were cancelled, grouped by year. . |
| `returns the percent of tenders with active awards with tender submission method electronic submission the endpoint also returns the total tenderds with active awards and the count of tenders with tender submission method electronic submission` | `GET /api/percentTendersUsingEBid` | Returns the percent of tenders with active awards, with tender.submissionMethod='electronicSubmission'.The endpoint also returns the total tenderds with active awards and the count of tenders with tender.submissionMethod='electronicSubmissi. |
| `returns the percent of tenders with active awards with tender submission method electronic submission the endpoint also returns the total tenderds with active awards and the count of tenders with tender submission method electronic submission` | `POST /api/percentTendersUsingEBid` | Returns the percent of tenders with active awards, with tender.submissionMethod='electronicSubmission'.The endpoint also returns the total tenderds with active awards and the count of tenders with tender.submissionMethod='electronicSubmissi. |
| `returns the percent of tenders that are using e procurement this is read from tender publication method e gp` | `GET /api/percentTendersUsingEgp` | Returns the percent of tenders that are using eProcurement. . |
| `returns the percent of tenders that are using e procurement this is read from tender publication method e gp` | `POST /api/percentTendersUsingEgp` | Returns the percent of tenders that are using eProcurement. . |
| `percentage of tenders that are associated in releases that have the planning budget amount non empty meaning there really is a planning entity correlated with the tender entity this endpoint uses tender tender period start date to calculate the tender year` | `GET /api/percentTendersWithLinkedProcurementPlan` | Percentage of tenders that are associated in releases that have the planning.budget.amount non empty,meaning there really is a planning entity correlated with the tender entity.This endpoint uses tender.tenderPeriod.startDate to calculate t. |
| `percentage of tenders that are associated in releases that have the planning budget amount non empty meaning there really is a planning entity correlated with the tender entity this endpoint uses tender tender period start date to calculate the tender year` | `POST /api/percentTendersWithLinkedProcurementPlan` | Percentage of tenders that are associated in releases that have the planning.budget.amount non empty,meaning there really is a planning entity correlated with the tender entity.This endpoint uses tender.tenderPeriod.startDate to calculate t. |
| `percentage of tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders this endpoint uses tender tender period start date to calculate the tender year` | `GET /api/percentTendersWithTwoOrMoreTenderers` | Percentage of tenders with >1 tenderer/bidder): Count of tenders with numberOfTenderers >1 divided by total count of tenders.This endpoint uses tender.tenderPeriod.startDate to calculate the tender year. |
| `percentage of tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders this endpoint uses tender tender period start date to calculate the tender year` | `POST /api/percentTendersWithTwoOrMoreTenderers` | Percentage of tenders with >1 tenderer/bidder): Count of tenders with numberOfTenderers >1 divided by total count of tenders.This endpoint uses tender.tenderPeriod.startDate to calculate the tender year. |

### tender-percentages-excel-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `exports cancelled funding percentage dashboard in excel format` | `GET /api/ocds/cancelledFundingPercentageExcelChart` | Exports *Cancelled funding (percentage)* dashboard in Excel format. |
| `exports cancelled funding percentage dashboard in excel format` | `POST /api/ocds/cancelledFundingPercentageExcelChart` | Exports *Cancelled funding (percentage)* dashboard in Excel format. |
| `exports number of cancelled bids dashboard in excel format` | `GET /api/ocds/numberCancelledFundingExcelChart` | Exports *Number of cancelled bids* dashboard in Excel format. |
| `exports number of cancelled bids dashboard in excel format` | `POST /api/ocds/numberCancelledFundingExcelChart` | Exports *Number of cancelled bids* dashboard in Excel format. |
| `exports number of e bid awards dashboard in excel format` | `GET /api/ocds/numberTendersUsingEBidExcelChart` | Exports *Number of eBid Awards* dashboard in Excel format. |
| `exports number of e bid awards dashboard in excel format` | `POST /api/ocds/numberTendersUsingEBidExcelChart` | Exports *Number of eBid Awards* dashboard in Excel format. |
| `exports percent of tenders using e bid dashboard in excel format` | `GET /api/ocds/percentTendersUsingEBidExcelChart` | Exports *Percent of Tenders Using e-Bid* dashboard in Excel format. |
| `exports percent of tenders using e bid dashboard in excel format` | `POST /api/ocds/percentTendersUsingEBidExcelChart` | Exports *Percent of Tenders Using e-Bid* dashboard in Excel format. |
| `exports percent of tenders using e procurement dashboard in excel format` | `GET /api/ocds/percentTendersUsingEgpExcelChart` | Exports *Percent of Tenders Using e-Procurement* dashboard in Excel format. |
| `exports percent of tenders using e procurement dashboard in excel format` | `POST /api/ocds/percentTendersUsingEgpExcelChart` | Exports *Percent of Tenders Using e-Procurement* dashboard in Excel format. |
| `exports percentage of plans with tender dashboard in excel format` | `GET /api/ocds/tendersWithLinkedProcurementPlanExcelChart` | Exports *Percentage of plans with tender* dashboard in Excel format. |
| `exports percentage of plans with tender dashboard in excel format` | `POST /api/ocds/tendersWithLinkedProcurementPlanExcelChart` | Exports *Percentage of plans with tender* dashboard in Excel format. |

### tender-price-by-type-year-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `same as api tender price by bid selection method but it always returns all bid selection methods it adds the missing bid selection methods with zero totals` | `GET /api/tenderPriceByAllBidSelectionMethods` | Same as /api/tenderPriceByBidSelectionMethod, but it always returns all bidSelectionMethods (it adds the missing bid selection methods with zero totals. |
| `same as api tender price by bid selection method but it always returns all bid selection methods it adds the missing bid selection methods with zero totals` | `POST /api/tenderPriceByAllBidSelectionMethods` | Same as /api/tenderPriceByBidSelectionMethod, but it always returns all bidSelectionMethods (it adds the missing bid selection methods with zero totals. |
| `returns the tender price by vietnam type procurement method details by year the ocds type is read from tender procurement method details the tender price is read from tender value amount` | `GET /api/tenderPriceByBidSelectionMethod` | Returns the tender price by Vietnam type (procurementMethodDetails), by year. . |
| `returns the tender price by vietnam type procurement method details by year the ocds type is read from tender procurement method details the tender price is read from tender value amount` | `POST /api/tenderPriceByBidSelectionMethod` | Returns the tender price by Vietnam type (procurementMethodDetails), by year. . |
| `returns the tender price by ocds type procurement method by year the ocds type is read from tender procurement method the tender price is read from tender value amount` | `GET /api/tenderPriceByProcurementMethod` | Returns the tender price by OCDS type (procurementMethod), by year. . |
| `returns the tender price by ocds type procurement method by year the ocds type is read from tender procurement method the tender price is read from tender value amount` | `POST /api/tenderPriceByProcurementMethod` | Returns the tender price by OCDS type (procurementMethod), by year. . |

### tender-price-excel-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `exports bid selection dashboard in excel format` | `GET /api/ocds/bidSelectionExcelChart` | Exports *Bid selection* dashboard in Excel format. |
| `exports bid selection dashboard in excel format` | `POST /api/ocds/bidSelectionExcelChart` | Exports *Bid selection* dashboard in Excel format. |
| `exports procurement method dashboard in excel format` | `GET /api/ocds/procurementMethodExcelChart` | Exports *Procurement method* dashboard in Excel format. |
| `exports procurement method dashboard in excel format` | `POST /api/ocds/procurementMethodExcelChart` | Exports *Procurement method* dashboard in Excel format. |

### tenders-awards-value-intervals

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `returns the min and max of awards value amount` | `GET /api/awardValueInterval` | Returns the min and max of awards.value.amount. |
| `returns the min and max of awards value amount` | `POST /api/awardValueInterval` | Returns the min and max of awards.value.amount. |
| `returns the min and max of tender value amount` | `GET /api/tenderValueInterval` | Returns the min and max of tender.value.amount. |
| `returns the min and max of tender value amount` | `POST /api/tenderValueInterval` | Returns the min and max of tender.value.amount. |

### tenders-awards-years

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `computes all available years from awards date tender tender period start dateand planning bid plan project date approve` | `GET /api/tendersAwardsYears` | Computes all available years from awards.date, tender.tenderPeriod.startDateand planning.bidPlanProjectDateApprove. |
| `computes all available years from awards date tender tender period start dateand planning bid plan project date approve` | `POST /api/tendersAwardsYears` | Computes all available years from awards.date, tender.tenderPeriod.startDateand planning.bidPlanProjectDateApprove. |

### tenders-by-item-classification

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `this should show the number of tenders per tender items classification the tender date is taken from tender tender period start date` | `GET /api/tendersByItemClassification` | This should show the number of tenders per tender.items.classification.The tender date is taken from tender.tenderPeriod.startDate. |
| `this should show the number of tenders per tender items classification the tender date is taken from tender tender period start date` | `POST /api/tendersByItemClassification` | This should show the number of tenders per tender.items.classification.The tender date is taken from tender.tenderPeriod.startDate. |

### tenders-by-item-excel-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `exports number of bids by item dashboard in excel format` | `GET /api/ocds/tendersByItemExcelChart` | Exports *Number of bids by item* dashboard in Excel format. |
| `exports number of bids by item dashboard in excel format` | `POST /api/ocds/tendersByItemExcelChart` | Exports *Number of bids by item* dashboard in Excel format. |

### top-ten-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `returns the top ten largest active awards the amount is taken from the award value field the returned data will containthe following fields planning bid no awards date awards suppliers name awards value amount awards suppliers name planning budget if any` | `GET /api/topTenLargestAwards` | Returns the top ten largest active awards. . |
| `returns the top ten largest active awards the amount is taken from the award value field the returned data will containthe following fields planning bid no awards date awards suppliers name awards value amount awards suppliers name planning budget if any` | `POST /api/topTenLargestAwards` | Returns the top ten largest active awards. . |
| `returns the top ten largest active tenders the amount is taken from the tender value amount field the returned data will containthe following fields planning bid no tender date tender value amount tender tender period tender procuring entity name` | `GET /api/topTenLargestTenders` | Returns the top ten largest active tenders. . |
| `returns the top ten largest active tenders the amount is taken from the tender value amount field the returned data will containthe following fields planning bid no tender date tender value amount tender tender period tender procuring entity name` | `POST /api/topTenLargestTenders` | Returns the top ten largest active tenders. . |
| `this endpoint should return the following data for the top 10 suppliers by award value returns supplier id total awarded amount of all awarded contracts count of awarded contracts ids of the procuring entities from which they have received an award and their count all filters ally here the year filter uses the awards date field` | `GET /api/topTenSuppliers` | This endpoint should return the following data for the Top 10 suppliers (by award value).Returns supplier id, total awarded amount of all awarded contracts, count of awarded contracts,Ids of the procuring entities from which they have received an award, and their count. . |
| `this endpoint should return the following data for the top 10 suppliers by award value returns supplier id total awarded amount of all awarded contracts count of awarded contracts ids of the procuring entities from which they have received an award and their count all filters ally here the year filter uses the awards date field` | `POST /api/topTenSuppliers` | This endpoint should return the following data for the Top 10 suppliers (by award value).Returns supplier id, total awarded amount of all awarded contracts, count of awarded contracts,Ids of the procuring entities from which they have received an award, and their count. . |

### total-cancelled-tenders-by-year-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `total cancelled tenders by year the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date` | `GET /api/totalCancelledTendersByYear` | Total Cancelled tenders by year. . |
| `total cancelled tenders by year the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date` | `POST /api/totalCancelledTendersByYear` | Total Cancelled tenders by year. . |
| `total cancelled tenders by year by cancel reason the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date the cancellation reason is read from tender cancellation rationale` | `GET /api/totalCancelledTendersByYearByRationale` | Total Cancelled tenders by year by cancel reason. . |
| `total cancelled tenders by year by cancel reason the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date the cancellation reason is read from tender cancellation rationale` | `POST /api/totalCancelledTendersByYearByRationale` | Total Cancelled tenders by year by cancel reason. . |

### total-cancelled-tenders-excel-controller

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `exports cancelled funding dashboard in excel format` | `GET /api/ocds/cancelledFundingExcelChart` | Exports *Cancelled funding* dashboard in Excel format. |
| `exports cancelled funding dashboard in excel format` | `POST /api/ocds/cancelledFundingExcelChart` | Exports *Cancelled funding* dashboard in Excel format. |
| `exports cancelled funding by reason dashboard in excel format` | `GET /api/ocds/cancelledTendersByYearByRationaleExcelChart` | Exports *Cancelled funding by reason* dashboard in Excel format. |
| `exports cancelled funding by reason dashboard in excel format` | `POST /api/ocds/cancelledTendersByYearByRationaleExcelChart` | Exports *Cancelled funding by reason* dashboard in Excel format. |

## Supported Business Behaviors

### Behavior 1: Calculates The Average Award Period Per Each Year The Year Is Taken From Awards Date And The Duration Is Taken By Counting The Daysbetween Tender Tender Period End Date And Tender Tender Period Start Date The Award Has To Be Active

Business goal:
Calculates the average award period, per each year. .

Domain context:
This behavior belongs to the `average-tender-and-award-periods-controller` capability area and operates through `GET /api/averageAwardPeriod`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `calculates the average award period per each year the year is taken from awards date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date the award has to be active` (`GET /api/averageAwardPeriod`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `calculates the average award period per each year the year is taken from awards date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date the award has to be active`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates the average award period per each year the year is taken from awards date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date the award has to be active`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates the average award period per each year the year is taken from awards date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date the award has to be active`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 2: Calculates The Average Award Period Per Each Year The Year Is Taken From Awards Date And The Duration Is Taken By Counting The Daysbetween Tender Tender Period End Date And Tender Tender Period Start Date The Award Has To Be Active

Business goal:
Calculates the average award period, per each year. .

Domain context:
This behavior belongs to the `average-tender-and-award-periods-controller` capability area and operates through `POST /api/averageAwardPeriod`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `calculates the average award period per each year the year is taken from awards date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date the award has to be active` (`POST /api/averageAwardPeriod`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `calculates the average award period per each year the year is taken from awards date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date the award has to be active`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates the average award period per each year the year is taken from awards date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date the award has to be active`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates the average award period per each year the year is taken from awards date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date the award has to be active`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 3: Calculate Average Number Of Tenderers By Year The Endpoint Can Be Filteredby Year Read From Tender Tender Period Start Date The Number Of Tenderers Are Read From Tender Number Of Tenderers

Business goal:
Calculate average number of tenderers, by year. .

Domain context:
This behavior belongs to the `average-number-of-tenderers-controller` capability area and operates through `GET /api/averageNumberOfTenderers`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `calculate average number of tenderers by year the endpoint can be filteredby year read from tender tender period start date the number of tenderers are read from tender number of tenderers` (`GET /api/averageNumberOfTenderers`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `calculate average number of tenderers by year the endpoint can be filteredby year read from tender tender period start date the number of tenderers are read from tender number of tenderers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculate average number of tenderers by year the endpoint can be filteredby year read from tender tender period start date the number of tenderers are read from tender number of tenderers`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculate average number of tenderers by year the endpoint can be filteredby year read from tender tender period start date the number of tenderers are read from tender number of tenderers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 4: Calculate Average Number Of Tenderers By Year The Endpoint Can Be Filteredby Year Read From Tender Tender Period Start Date The Number Of Tenderers Are Read From Tender Number Of Tenderers

Business goal:
Calculate average number of tenderers, by year. .

Domain context:
This behavior belongs to the `average-number-of-tenderers-controller` capability area and operates through `POST /api/averageNumberOfTenderers`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `calculate average number of tenderers by year the endpoint can be filteredby year read from tender tender period start date the number of tenderers are read from tender number of tenderers` (`POST /api/averageNumberOfTenderers`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `calculate average number of tenderers by year the endpoint can be filteredby year read from tender tender period start date the number of tenderers are read from tender number of tenderers`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculate average number of tenderers by year the endpoint can be filteredby year read from tender tender period start date the number of tenderers are read from tender number of tenderers`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculate average number of tenderers by year the endpoint can be filteredby year read from tender tender period start date the number of tenderers are read from tender number of tenderers`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 5: Calculates The Average Tender Period Per Each Year The Year Is Taken From Tender Tender Period Start Date And The Duration Is Taken By Counting The Daysbetween Tender Tender Period End Date And Tender Tender Period Start Date

Business goal:
Calculates the average tender period, per each year. .

Domain context:
This behavior belongs to the `average-tender-and-award-periods-controller` capability area and operates through `GET /api/averageTenderPeriod`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `calculates the average tender period per each year the year is taken from tender tender period start date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date` (`GET /api/averageTenderPeriod`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `calculates the average tender period per each year the year is taken from tender tender period start date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates the average tender period per each year the year is taken from tender tender period start date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates the average tender period per each year the year is taken from tender tender period start date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 6: Calculates The Average Tender Period Per Each Year The Year Is Taken From Tender Tender Period Start Date And The Duration Is Taken By Counting The Daysbetween Tender Tender Period End Date And Tender Tender Period Start Date

Business goal:
Calculates the average tender period, per each year. .

Domain context:
This behavior belongs to the `average-tender-and-award-periods-controller` capability area and operates through `POST /api/averageTenderPeriod`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `calculates the average tender period per each year the year is taken from tender tender period start date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date` (`POST /api/averageTenderPeriod`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `calculates the average tender period per each year the year is taken from tender tender period start date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates the average tender period per each year the year is taken from tender tender period start date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates the average tender period per each year the year is taken from tender tender period start date and the duration is taken by counting the daysbetween tender tender period end date and tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 7: For All Tenders That Have Both Tender Tender Period Start Date And Planning Bid Plan Project Date Approvecalculates The Number O Days From Planning Bid Plan Project Date Approve To Tender Tender Period Start Dateand Creates The Average Groups Results By Tender Year Calculatedfrom Tender Tender Period Start Date

Business goal:
For all tenders that have both tender.tenderPeriod.startDate and planning.bidPlanProjectDateApprovecalculates the number o days from planning.bidPlanProjectDateApprove to tender.tenderPeriod.startDateand creates the average. .

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `GET /api/avgTimeFromPlanToTenderPhase`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `for all tenders that have both tender tender period start date and planning bid plan project date approvecalculates the number o days from planning bid plan project date approve to tender tender period start dateand creates the average groups results by tender year calculatedfrom tender tender period start date` (`GET /api/avgTimeFromPlanToTenderPhase`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `for all tenders that have both tender tender period start date and planning bid plan project date approvecalculates the number o days from planning bid plan project date approve to tender tender period start dateand creates the average groups results by tender year calculatedfrom tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `for all tenders that have both tender tender period start date and planning bid plan project date approvecalculates the number o days from planning bid plan project date approve to tender tender period start dateand creates the average groups results by tender year calculatedfrom tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `for all tenders that have both tender tender period start date and planning bid plan project date approvecalculates the number o days from planning bid plan project date approve to tender tender period start dateand creates the average groups results by tender year calculatedfrom tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 8: For All Tenders That Have Both Tender Tender Period Start Date And Planning Bid Plan Project Date Approvecalculates The Number O Days From Planning Bid Plan Project Date Approve To Tender Tender Period Start Dateand Creates The Average Groups Results By Tender Year Calculatedfrom Tender Tender Period Start Date

Business goal:
For all tenders that have both tender.tenderPeriod.startDate and planning.bidPlanProjectDateApprovecalculates the number o days from planning.bidPlanProjectDateApprove to tender.tenderPeriod.startDateand creates the average. .

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `POST /api/avgTimeFromPlanToTenderPhase`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `for all tenders that have both tender tender period start date and planning bid plan project date approvecalculates the number o days from planning bid plan project date approve to tender tender period start dateand creates the average groups results by tender year calculatedfrom tender tender period start date` (`POST /api/avgTimeFromPlanToTenderPhase`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `for all tenders that have both tender tender period start date and planning bid plan project date approvecalculates the number o days from planning bid plan project date approve to tender tender period start dateand creates the average groups results by tender year calculatedfrom tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `for all tenders that have both tender tender period start date and planning bid plan project date approvecalculates the number o days from planning bid plan project date approve to tender tender period start dateand creates the average groups results by tender year calculatedfrom tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `for all tenders that have both tender tender period start date and planning bid plan project date approvecalculates the number o days from planning bid plan project date approve to tender tender period start dateand creates the average groups results by tender year calculatedfrom tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 9: Returns The Min And Max Of Awards Value Amount

Business goal:
Returns the min and max of awards.value.amount.

Domain context:
This behavior belongs to the `tenders-awards-value-intervals` capability area and operates through `GET /api/awardValueInterval`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns the min and max of awards value amount` (`GET /api/awardValueInterval`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the min and max of awards value amount`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the min and max of awards value amount`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the min and max of awards value amount`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 10: Returns The Min And Max Of Awards Value Amount

Business goal:
Returns the min and max of awards.value.amount.

Domain context:
This behavior belongs to the `tenders-awards-value-intervals` capability area and operates through `POST /api/awardValueInterval`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns the min and max of awards value amount` (`POST /api/awardValueInterval`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the min and max of awards value amount`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the min and max of awards value amount`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the min and max of awards value amount`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 11: Cost Effectiveness Of Awards Displays The Total Amount Of Active Awards Grouped By Year The Tender Entity For Each Award Has To Have Amount Value The Year Is Calculated From Tender Tender Period Start Date

Business goal:
Cost effectiveness of Awards: Displays the total amount of active awards grouped by year.The tender entity, for each award, has to have amount value. .

Domain context:
This behavior belongs to the `cost-effectiveness-visuals-controller` capability area and operates through `GET /api/costEffectivenessAwardAmount`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `cost effectiveness of awards displays the total amount of active awards grouped by year the tender entity for each award has to have amount value the year is calculated from tender tender period start date` (`GET /api/costEffectivenessAwardAmount`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `cost effectiveness of awards displays the total amount of active awards grouped by year the tender entity for each award has to have amount value the year is calculated from tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cost effectiveness of awards displays the total amount of active awards grouped by year the tender entity for each award has to have amount value the year is calculated from tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cost effectiveness of awards displays the total amount of active awards grouped by year the tender entity for each award has to have amount value the year is calculated from tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 12: Cost Effectiveness Of Awards Displays The Total Amount Of Active Awards Grouped By Year The Tender Entity For Each Award Has To Have Amount Value The Year Is Calculated From Tender Tender Period Start Date

Business goal:
Cost effectiveness of Awards: Displays the total amount of active awards grouped by year.The tender entity, for each award, has to have amount value. .

Domain context:
This behavior belongs to the `cost-effectiveness-visuals-controller` capability area and operates through `POST /api/costEffectivenessAwardAmount`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `cost effectiveness of awards displays the total amount of active awards grouped by year the tender entity for each award has to have amount value the year is calculated from tender tender period start date` (`POST /api/costEffectivenessAwardAmount`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `cost effectiveness of awards displays the total amount of active awards grouped by year the tender entity for each award has to have amount value the year is calculated from tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cost effectiveness of awards displays the total amount of active awards grouped by year the tender entity for each award has to have amount value the year is calculated from tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cost effectiveness of awards displays the total amount of active awards grouped by year the tender entity for each award has to have amount value the year is calculated from tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 13: Cost Effectiveness Of Tenders Displays The Total Amount Of The Active Tenders That Have Active Awards Grouped By Year Only Tenders Status Activeare Taken Into Account The Year Is Calculated From Tender Period Start Date

Business goal:
Cost effectiveness of Tenders: Displays the total amount of the active tenders that have active awards, grouped by year. .

Domain context:
This behavior belongs to the `cost-effectiveness-visuals-controller` capability area and operates through `GET /api/costEffectivenessTenderAmount`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `cost effectiveness of tenders displays the total amount of the active tenders that have active awards grouped by year only tenders status activeare taken into account the year is calculated from tender period start date` (`GET /api/costEffectivenessTenderAmount`) with query: groupByCategory optional, year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `groupByCategory`, `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `cost effectiveness of tenders displays the total amount of the active tenders that have active awards grouped by year only tenders status activeare taken into account the year is calculated from tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cost effectiveness of tenders displays the total amount of the active tenders that have active awards grouped by year only tenders status activeare taken into account the year is calculated from tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cost effectiveness of tenders displays the total amount of the active tenders that have active awards grouped by year only tenders status activeare taken into account the year is calculated from tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 14: Cost Effectiveness Of Tenders Displays The Total Amount Of The Active Tenders That Have Active Awards Grouped By Year Only Tenders Status Activeare Taken Into Account The Year Is Calculated From Tender Period Start Date

Business goal:
Cost effectiveness of Tenders: Displays the total amount of the active tenders that have active awards, grouped by year. .

Domain context:
This behavior belongs to the `cost-effectiveness-visuals-controller` capability area and operates through `POST /api/costEffectivenessTenderAmount`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `cost effectiveness of tenders displays the total amount of the active tenders that have active awards grouped by year only tenders status activeare taken into account the year is calculated from tender period start date` (`POST /api/costEffectivenessTenderAmount`) with query: groupByCategory optional, year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `groupByCategory`, `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `cost effectiveness of tenders displays the total amount of the active tenders that have active awards grouped by year only tenders status activeare taken into account the year is calculated from tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cost effectiveness of tenders displays the total amount of the active tenders that have active awards grouped by year only tenders status activeare taken into account the year is calculated from tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cost effectiveness of tenders displays the total amount of the active tenders that have active awards grouped by year only tenders status activeare taken into account the year is calculated from tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 15: Aggregated Version Of Api Cost Effectiveness Tender Amount And Api Cost Effectiveness Award Amount This Endpoint Aggregates The Responses From The Specified Endpoints Per Year Responds To The Same Filters

Business goal:
Aggregated version of /api/costEffectivenessTenderAmount and /api/costEffectivenessAwardAmount.This endpoint aggregates the responses from the specified endpoints, per year. .

Domain context:
This behavior belongs to the `cost-effectiveness-visuals-controller` capability area and operates through `GET /api/costEffectivenessTenderAwardAmount`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `aggregated version of api cost effectiveness tender amount and api cost effectiveness award amount this endpoint aggregates the responses from the specified endpoints per year responds to the same filters` (`GET /api/costEffectivenessTenderAwardAmount`) with query: groupByCategory optional, year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `groupByCategory`, `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `aggregated version of api cost effectiveness tender amount and api cost effectiveness award amount this endpoint aggregates the responses from the specified endpoints per year responds to the same filters`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `aggregated version of api cost effectiveness tender amount and api cost effectiveness award amount this endpoint aggregates the responses from the specified endpoints per year responds to the same filters`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `aggregated version of api cost effectiveness tender amount and api cost effectiveness award amount this endpoint aggregates the responses from the specified endpoints per year responds to the same filters`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 16: Aggregated Version Of Api Cost Effectiveness Tender Amount And Api Cost Effectiveness Award Amount This Endpoint Aggregates The Responses From The Specified Endpoints Per Year Responds To The Same Filters

Business goal:
Aggregated version of /api/costEffectivenessTenderAmount and /api/costEffectivenessAwardAmount.This endpoint aggregates the responses from the specified endpoints, per year. .

Domain context:
This behavior belongs to the `cost-effectiveness-visuals-controller` capability area and operates through `POST /api/costEffectivenessTenderAwardAmount`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `aggregated version of api cost effectiveness tender amount and api cost effectiveness award amount this endpoint aggregates the responses from the specified endpoints per year responds to the same filters` (`POST /api/costEffectivenessTenderAwardAmount`) with query: groupByCategory optional, year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `groupByCategory`, `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `aggregated version of api cost effectiveness tender amount and api cost effectiveness award amount this endpoint aggregates the responses from the specified endpoints per year responds to the same filters`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `aggregated version of api cost effectiveness tender amount and api cost effectiveness award amount this endpoint aggregates the responses from the specified endpoints per year responds to the same filters`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `aggregated version of api cost effectiveness tender amount and api cost effectiveness award amount this endpoint aggregates the responses from the specified endpoints per year responds to the same filters`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 17: Count The Awards And Group The Results By Year The Year Is Calculated From The Awards Date Field

Business goal:
Count the awards and group the results by year. .

Domain context:
This behavior belongs to the `count-plans-tenders-awards-controller` capability area and operates through `GET /api/countAwardsByYear`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count the awards and group the results by year the year is calculated from the awards date field` (`GET /api/countAwardsByYear`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count the awards and group the results by year the year is calculated from the awards date field`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count the awards and group the results by year the year is calculated from the awards date field`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count the awards and group the results by year the year is calculated from the awards date field`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 18: Count The Awards And Group The Results By Year The Year Is Calculated From The Awards Date Field

Business goal:
Count the awards and group the results by year. .

Domain context:
This behavior belongs to the `count-plans-tenders-awards-controller` capability area and operates through `POST /api/countAwardsByYear`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count the awards and group the results by year the year is calculated from the awards date field` (`POST /api/countAwardsByYear`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count the awards and group the results by year the year is calculated from the awards date field`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count the awards and group the results by year the year is calculated from the awards date field`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count the awards and group the results by year the year is calculated from the awards date field`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 19: Count Of Bid Plans By Year This Will Count The Releases That Have The Fieldplanning Bid Plan Project Date Approve Populated The Year Grouping Is Taken From Planning Bid Plan Project Date Approve

Business goal:
Count of bid plans, by year. .

Domain context:
This behavior belongs to the `count-plans-tenders-awards-controller` capability area and operates through `GET /api/countBidPlansByYear`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count of bid plans by year this will count the releases that have the fieldplanning bid plan project date approve populated the year grouping is taken from planning bid plan project date approve` (`GET /api/countBidPlansByYear`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of bid plans by year this will count the releases that have the fieldplanning bid plan project date approve populated the year grouping is taken from planning bid plan project date approve`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of bid plans by year this will count the releases that have the fieldplanning bid plan project date approve populated the year grouping is taken from planning bid plan project date approve`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of bid plans by year this will count the releases that have the fieldplanning bid plan project date approve populated the year grouping is taken from planning bid plan project date approve`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 20: Count Of Bid Plans By Year This Will Count The Releases That Have The Fieldplanning Bid Plan Project Date Approve Populated The Year Grouping Is Taken From Planning Bid Plan Project Date Approve

Business goal:
Count of bid plans, by year. .

Domain context:
This behavior belongs to the `count-plans-tenders-awards-controller` capability area and operates through `POST /api/countBidPlansByYear`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count of bid plans by year this will count the releases that have the fieldplanning bid plan project date approve populated the year grouping is taken from planning bid plan project date approve` (`POST /api/countBidPlansByYear`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count of bid plans by year this will count the releases that have the fieldplanning bid plan project date approve populated the year grouping is taken from planning bid plan project date approve`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of bid plans by year this will count the releases that have the fieldplanning bid plan project date approve populated the year grouping is taken from planning bid plan project date approve`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count of bid plans by year this will count the releases that have the fieldplanning bid plan project date approve populated the year grouping is taken from planning bid plan project date approve`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 21: Count The Tenders And Group The Results By Year The Year Is Calculated From Tender Tender Period Start Date

Business goal:
Count the tenders and group the results by year. .

Domain context:
This behavior belongs to the `count-plans-tenders-awards-controller` capability area and operates through `GET /api/countTendersByYear`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `count the tenders and group the results by year the year is calculated from tender tender period start date` (`GET /api/countTendersByYear`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count the tenders and group the results by year the year is calculated from tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count the tenders and group the results by year the year is calculated from tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count the tenders and group the results by year the year is calculated from tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 22: Count The Tenders And Group The Results By Year The Year Is Calculated From Tender Tender Period Start Date

Business goal:
Count the tenders and group the results by year. .

Domain context:
This behavior belongs to the `count-plans-tenders-awards-controller` capability area and operates through `POST /api/countTendersByYear`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `count the tenders and group the results by year the year is calculated from tender tender period start date` (`POST /api/countTendersByYear`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `count the tenders and group the results by year the year is calculated from tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count the tenders and group the results by year the year is calculated from tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `count the tenders and group the results by year the year is calculated from tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 23: Search Releases By Flag I003

Business goal:
Search releases by flag i003.

Domain context:
This behavior belongs to the `flag-i-003-release-search-controller` capability area and operates through `GET /api/flags/i003/releases`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `search releases by flag i003` (`GET /api/flags/i003/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i003`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i003`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i003`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 24: Search Releases By Flag I003

Business goal:
Search releases by flag i003.

Domain context:
This behavior belongs to the `flag-i-003-release-search-controller` capability area and operates through `POST /api/flags/i003/releases`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `search releases by flag i003` (`POST /api/flags/i003/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i003`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i003`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i003`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 25: Stats For Flag I003

Business goal:
Stats for flag i003.

Domain context:
This behavior belongs to the `flag-i-003-stats-controller` capability area and operates through `GET /api/flags/i003/stats`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `stats for flag i003` (`GET /api/flags/i003/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i003`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i003`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i003`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 26: Stats For Flag I003

Business goal:
Stats for flag i003.

Domain context:
This behavior belongs to the `flag-i-003-stats-controller` capability area and operates through `POST /api/flags/i003/stats`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `stats for flag i003` (`POST /api/flags/i003/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i003`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i003`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i003`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 27: Search Releases By Flag I004

Business goal:
Search releases by flag i004.

Domain context:
This behavior belongs to the `flag-i-004-release-search-controller` capability area and operates through `GET /api/flags/i004/releases`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `search releases by flag i004` (`GET /api/flags/i004/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i004`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i004`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i004`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 28: Search Releases By Flag I004

Business goal:
Search releases by flag i004.

Domain context:
This behavior belongs to the `flag-i-004-release-search-controller` capability area and operates through `POST /api/flags/i004/releases`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `search releases by flag i004` (`POST /api/flags/i004/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i004`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i004`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i004`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 29: Stats For Flag I004

Business goal:
Stats for flag i004.

Domain context:
This behavior belongs to the `flag-i-004-stats-controller` capability area and operates through `GET /api/flags/i004/stats`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `stats for flag i004` (`GET /api/flags/i004/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i004`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i004`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i004`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 30: Stats For Flag I004

Business goal:
Stats for flag i004.

Domain context:
This behavior belongs to the `flag-i-004-stats-controller` capability area and operates through `POST /api/flags/i004/stats`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `stats for flag i004` (`POST /api/flags/i004/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i004`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i004`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i004`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 31: Search Releases By Flag I007

Business goal:
Search releases by flag i007.

Domain context:
This behavior belongs to the `flag-i-007-release-search-controller` capability area and operates through `GET /api/flags/i007/releases`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `search releases by flag i007` (`GET /api/flags/i007/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i007`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i007`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i007`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 32: Search Releases By Flag I007

Business goal:
Search releases by flag i007.

Domain context:
This behavior belongs to the `flag-i-007-release-search-controller` capability area and operates through `POST /api/flags/i007/releases`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `search releases by flag i007` (`POST /api/flags/i007/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i007`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i007`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i007`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 33: Stats For Flag I007

Business goal:
Stats for flag i007.

Domain context:
This behavior belongs to the `flag-i-007-stats-controller` capability area and operates through `GET /api/flags/i007/stats`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `stats for flag i007` (`GET /api/flags/i007/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i007`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i007`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i007`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 34: Stats For Flag I007

Business goal:
Stats for flag i007.

Domain context:
This behavior belongs to the `flag-i-007-stats-controller` capability area and operates through `POST /api/flags/i007/stats`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `stats for flag i007` (`POST /api/flags/i007/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i007`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i007`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i007`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 35: Search Releases By Flag I019

Business goal:
Search releases by flag i019.

Domain context:
This behavior belongs to the `flag-i-019-release-search-controller` capability area and operates through `GET /api/flags/i019/releases`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `search releases by flag i019` (`GET /api/flags/i019/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i019`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i019`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i019`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 36: Search Releases By Flag I019

Business goal:
Search releases by flag i019.

Domain context:
This behavior belongs to the `flag-i-019-release-search-controller` capability area and operates through `POST /api/flags/i019/releases`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `search releases by flag i019` (`POST /api/flags/i019/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i019`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i019`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i019`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 37: Stats For Flag I019

Business goal:
Stats for flag i019.

Domain context:
This behavior belongs to the `flag-i-019-stats-controller` capability area and operates through `GET /api/flags/i019/stats`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `stats for flag i019` (`GET /api/flags/i019/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i019`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i019`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i019`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 38: Stats For Flag I019

Business goal:
Stats for flag i019.

Domain context:
This behavior belongs to the `flag-i-019-stats-controller` capability area and operates through `POST /api/flags/i019/stats`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `stats for flag i019` (`POST /api/flags/i019/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i019`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i019`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i019`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 39: Search Releases By Flag I038

Business goal:
Search releases by flag i038.

Domain context:
This behavior belongs to the `flag-i-038-release-search-controller` capability area and operates through `GET /api/flags/i038/releases`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `search releases by flag i038` (`GET /api/flags/i038/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i038`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i038`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i038`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 40: Search Releases By Flag I038

Business goal:
Search releases by flag i038.

Domain context:
This behavior belongs to the `flag-i-038-release-search-controller` capability area and operates through `POST /api/flags/i038/releases`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `search releases by flag i038` (`POST /api/flags/i038/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i038`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i038`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i038`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 41: Stats For Flag I038

Business goal:
Stats for flag i038.

Domain context:
This behavior belongs to the `flag-i-038-stats-controller` capability area and operates through `GET /api/flags/i038/stats`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `stats for flag i038` (`GET /api/flags/i038/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i038`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i038`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i038`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 42: Stats For Flag I038

Business goal:
Stats for flag i038.

Domain context:
This behavior belongs to the `flag-i-038-stats-controller` capability area and operates through `POST /api/flags/i038/stats`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `stats for flag i038` (`POST /api/flags/i038/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i038`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i038`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i038`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 43: Search Releases By Flag I077

Business goal:
Search releases by flag i077.

Domain context:
This behavior belongs to the `flag-i-077-release-search-controller` capability area and operates through `GET /api/flags/i077/releases`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `search releases by flag i077` (`GET /api/flags/i077/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i077`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i077`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i077`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 44: Search Releases By Flag I077

Business goal:
Search releases by flag i077.

Domain context:
This behavior belongs to the `flag-i-077-release-search-controller` capability area and operates through `POST /api/flags/i077/releases`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `search releases by flag i077` (`POST /api/flags/i077/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i077`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i077`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i077`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 45: Stats For Flag I077

Business goal:
Stats for flag i077.

Domain context:
This behavior belongs to the `flag-i-077-stats-controller` capability area and operates through `GET /api/flags/i077/stats`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `stats for flag i077` (`GET /api/flags/i077/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i077`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i077`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i077`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 46: Stats For Flag I077

Business goal:
Stats for flag i077.

Domain context:
This behavior belongs to the `flag-i-077-stats-controller` capability area and operates through `POST /api/flags/i077/stats`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `stats for flag i077` (`POST /api/flags/i077/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i077`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i077`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i077`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 47: Search Releases By Flag I180

Business goal:
Search releases by flag i180.

Domain context:
This behavior belongs to the `flag-i-180-release-search-controller` capability area and operates through `GET /api/flags/i180/releases`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `search releases by flag i180` (`GET /api/flags/i180/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i180`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i180`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i180`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 48: Search Releases By Flag I180

Business goal:
Search releases by flag i180.

Domain context:
This behavior belongs to the `flag-i-180-release-search-controller` capability area and operates through `POST /api/flags/i180/releases`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `search releases by flag i180` (`POST /api/flags/i180/releases`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `search releases by flag i180`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i180`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `search releases by flag i180`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 49: Stats For Flag I180

Business goal:
Stats for flag i180.

Domain context:
This behavior belongs to the `flag-i-180-stats-controller` capability area and operates through `GET /api/flags/i180/stats`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `stats for flag i180` (`GET /api/flags/i180/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i180`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i180`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i180`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 50: Stats For Flag I180

Business goal:
Stats for flag i180.

Domain context:
This behavior belongs to the `flag-i-180-stats-controller` capability area and operates through `POST /api/flags/i180/stats`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `stats for flag i180` (`POST /api/flags/i180/stats`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `stats for flag i180`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i180`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `stats for flag i180`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 51: Returns The Frequent Suppliers Of A Procuring Entity Split By A Time Interval The Time Interval Is A Parameter And Represents The Number Of Days To Take As Interval Starting With Today And Going Back Till The Last Award Date The Awards Are Grouped By Procuring Entity Supplier And The Time Interval Max Awards Parameter Is Used To Designate What Is The Maximum Number Of Awards Granted To One Supplier By The Same Procuring Entity Inside One Time Interval The Default Value For Max Awards Is 3 Days And The Default Value For Interval Days Is 365

Business goal:
Returns the frequent suppliers of a procuringEntity split by a time interval. .

Domain context:
This behavior belongs to the `frequent-suppliers-time-interval-controller` capability area and operates through `GET /api/frequentSuppliersTimeInterval`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns the frequent suppliers of a procuring entity split by a time interval the time interval is a parameter and represents the number of days to take as interval starting with today and going back till the last award date the awards are grouped by procuring entity supplier and the time interval max awards parameter is used to designate what is the maximum number of awards granted to one supplier by the same procuring entity inside one time interval the default value for max awards is 3 days and the default value for interval days is 365` (`GET /api/frequentSuppliersTimeInterval`) with query: intervalDays optional, maxAwards optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `intervalDays`, `maxAwards` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the frequent suppliers of a procuring entity split by a time interval the time interval is a parameter and represents the number of days to take as interval starting with today and going back till the last award date the awards are grouped by procuring entity supplier and the time interval max awards parameter is used to designate what is the maximum number of awards granted to one supplier by the same procuring entity inside one time interval the default value for max awards is 3 days and the default value for interval days is 365`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the frequent suppliers of a procuring entity split by a time interval the time interval is a parameter and represents the number of days to take as interval starting with today and going back till the last award date the awards are grouped by procuring entity supplier and the time interval max awards parameter is used to designate what is the maximum number of awards granted to one supplier by the same procuring entity inside one time interval the default value for max awards is 3 days and the default value for interval days is 365`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the frequent suppliers of a procuring entity split by a time interval the time interval is a parameter and represents the number of days to take as interval starting with today and going back till the last award date the awards are grouped by procuring entity supplier and the time interval max awards parameter is used to designate what is the maximum number of awards granted to one supplier by the same procuring entity inside one time interval the default value for max awards is 3 days and the default value for interval days is 365`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 52: Returns The Frequent Suppliers Of A Procuring Entity Split By A Time Interval The Time Interval Is A Parameter And Represents The Number Of Days To Take As Interval Starting With Today And Going Back Till The Last Award Date The Awards Are Grouped By Procuring Entity Supplier And The Time Interval Max Awards Parameter Is Used To Designate What Is The Maximum Number Of Awards Granted To One Supplier By The Same Procuring Entity Inside One Time Interval The Default Value For Max Awards Is 3 Days And The Default Value For Interval Days Is 365

Business goal:
Returns the frequent suppliers of a procuringEntity split by a time interval. .

Domain context:
This behavior belongs to the `frequent-suppliers-time-interval-controller` capability area and operates through `POST /api/frequentSuppliersTimeInterval`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns the frequent suppliers of a procuring entity split by a time interval the time interval is a parameter and represents the number of days to take as interval starting with today and going back till the last award date the awards are grouped by procuring entity supplier and the time interval max awards parameter is used to designate what is the maximum number of awards granted to one supplier by the same procuring entity inside one time interval the default value for max awards is 3 days and the default value for interval days is 365` (`POST /api/frequentSuppliersTimeInterval`) with query: intervalDays optional, maxAwards optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `intervalDays`, `maxAwards` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the frequent suppliers of a procuring entity split by a time interval the time interval is a parameter and represents the number of days to take as interval starting with today and going back till the last award date the awards are grouped by procuring entity supplier and the time interval max awards parameter is used to designate what is the maximum number of awards granted to one supplier by the same procuring entity inside one time interval the default value for max awards is 3 days and the default value for interval days is 365`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the frequent suppliers of a procuring entity split by a time interval the time interval is a parameter and represents the number of days to take as interval starting with today and going back till the last award date the awards are grouped by procuring entity supplier and the time interval max awards parameter is used to designate what is the maximum number of awards granted to one supplier by the same procuring entity inside one time interval the default value for max awards is 3 days and the default value for interval days is 365`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the frequent suppliers of a procuring entity split by a time interval the time interval is a parameter and represents the number of days to take as interval starting with today and going back till the last award date the awards are grouped by procuring entity supplier and the time interval max awards parameter is used to designate what is the maximum number of awards granted to one supplier by the same procuring entity inside one time interval the default value for max awards is 3 days and the default value for interval days is 365`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 53: Detect Frequent Pairs Of Tenderers That Apply Together To Bids We Are Only Showing Pairs If They Applied To More Than One Bid Together We Are Sorting The Results After The Number Of Occurences Descending You Can Use All The Filters That Are Available Along With Pagination Options

Business goal:
Detect frequent pairs of tenderers that apply together to bids.We are only showing pairs if they applied to more than one bid together.We are sorting the results after the number of occurences, descending.You can use all the filters that ar.

Domain context:
This behavior belongs to the `frequent-tenderers-controller` capability area and operates through `GET /api/frequentTenderers`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `detect frequent pairs of tenderers that apply together to bids we are only showing pairs if they applied to more than one bid together we are sorting the results after the number of occurences descending you can use all the filters that are available along with pagination options` (`GET /api/frequentTenderers`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `detect frequent pairs of tenderers that apply together to bids we are only showing pairs if they applied to more than one bid together we are sorting the results after the number of occurences descending you can use all the filters that are available along with pagination options`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `detect frequent pairs of tenderers that apply together to bids we are only showing pairs if they applied to more than one bid together we are sorting the results after the number of occurences descending you can use all the filters that are available along with pagination options`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `detect frequent pairs of tenderers that apply together to bids we are only showing pairs if they applied to more than one bid together we are sorting the results after the number of occurences descending you can use all the filters that are available along with pagination options`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 54: Detect Frequent Pairs Of Tenderers That Apply Together To Bids We Are Only Showing Pairs If They Applied To More Than One Bid Together We Are Sorting The Results After The Number Of Occurences Descending You Can Use All The Filters That Are Available Along With Pagination Options

Business goal:
Detect frequent pairs of tenderers that apply together to bids.We are only showing pairs if they applied to more than one bid together.We are sorting the results after the number of occurences, descending.You can use all the filters that ar.

Domain context:
This behavior belongs to the `frequent-tenderers-controller` capability area and operates through `POST /api/frequentTenderers`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `detect frequent pairs of tenderers that apply together to bids we are only showing pairs if they applied to more than one bid together we are sorting the results after the number of occurences descending you can use all the filters that are available along with pagination options` (`POST /api/frequentTenderers`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `detect frequent pairs of tenderers that apply together to bids we are only showing pairs if they applied to more than one bid together we are sorting the results after the number of occurences descending you can use all the filters that are available along with pagination options`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `detect frequent pairs of tenderers that apply together to bids we are only showing pairs if they applied to more than one bid together we are sorting the results after the number of occurences descending you can use all the filters that are available along with pagination options`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `detect frequent pairs of tenderers that apply together to bids we are only showing pairs if they applied to more than one bid together we are sorting the results after the number of occurences descending you can use all the filters that are available along with pagination options`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 55: Total Estimated Funding Tender Value Grouped By Tender Items Delivery Location And Also Grouped By Year The Endpoint Also Returns The Count Of Tenders For Each Location It Responds To All Filters The Year Is Calculated Based On Tender Tender Period Start Date

Business goal:
Total estimated funding (tender.value) grouped by tender.items.deliveryLocation and also grouped by year. .

Domain context:
This behavior belongs to the `funding-by-location-controller` capability area and operates through `GET /api/fundingByTenderDeliveryLocation`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `total estimated funding tender value grouped by tender items delivery location and also grouped by year the endpoint also returns the count of tenders for each location it responds to all filters the year is calculated based on tender tender period start date` (`GET /api/fundingByTenderDeliveryLocation`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `total estimated funding tender value grouped by tender items delivery location and also grouped by year the endpoint also returns the count of tenders for each location it responds to all filters the year is calculated based on tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `total estimated funding tender value grouped by tender items delivery location and also grouped by year the endpoint also returns the count of tenders for each location it responds to all filters the year is calculated based on tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `total estimated funding tender value grouped by tender items delivery location and also grouped by year the endpoint also returns the count of tenders for each location it responds to all filters the year is calculated based on tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 56: Total Estimated Funding Tender Value Grouped By Tender Items Delivery Location And Also Grouped By Year The Endpoint Also Returns The Count Of Tenders For Each Location It Responds To All Filters The Year Is Calculated Based On Tender Tender Period Start Date

Business goal:
Total estimated funding (tender.value) grouped by tender.items.deliveryLocation and also grouped by year. .

Domain context:
This behavior belongs to the `funding-by-location-controller` capability area and operates through `POST /api/fundingByTenderDeliveryLocation`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `total estimated funding tender value grouped by tender items delivery location and also grouped by year the endpoint also returns the count of tenders for each location it responds to all filters the year is calculated based on tender tender period start date` (`POST /api/fundingByTenderDeliveryLocation`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `total estimated funding tender value grouped by tender items delivery location and also grouped by year the endpoint also returns the count of tenders for each location it responds to all filters the year is calculated based on tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `total estimated funding tender value grouped by tender items delivery location and also grouped by year the endpoint also returns the count of tenders for each location it responds to all filters the year is calculated based on tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `total estimated funding tender value grouped by tender items delivery location and also grouped by year the endpoint also returns the count of tenders for each location it responds to all filters the year is calculated based on tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 57: Exports Average Number Of Bids Dashboard In Excel Format

Business goal:
Exports *Average number of bids* dashboard in Excel format.

Domain context:
This behavior belongs to the `average-number-of-tenderers-excel-controller` capability area and operates through `GET /api/ocds/averageNumberBidsExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports average number of bids dashboard in excel format` (`GET /api/ocds/averageNumberBidsExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports average number of bids dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports average number of bids dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports average number of bids dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 58: Exports Average Number Of Bids Dashboard In Excel Format

Business goal:
Exports *Average number of bids* dashboard in Excel format.

Domain context:
This behavior belongs to the `average-number-of-tenderers-excel-controller` capability area and operates through `POST /api/ocds/averageNumberBidsExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports average number of bids dashboard in excel format` (`POST /api/ocds/averageNumberBidsExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports average number of bids dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports average number of bids dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports average number of bids dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 59: Exports Bid Selection Dashboard In Excel Format

Business goal:
Exports *Bid selection* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-price-excel-controller` capability area and operates through `GET /api/ocds/bidSelectionExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports bid selection dashboard in excel format` (`GET /api/ocds/bidSelectionExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports bid selection dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports bid selection dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports bid selection dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 60: Exports Bid Selection Dashboard In Excel Format

Business goal:
Exports *Bid selection* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-price-excel-controller` capability area and operates through `POST /api/ocds/bidSelectionExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports bid selection dashboard in excel format` (`POST /api/ocds/bidSelectionExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports bid selection dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports bid selection dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports bid selection dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 61: Display The Available Bid Selection Methods These Are Taken From Tender Procurement Method Details

Business goal:
Display the available bid selection methods. .

Domain context:
This behavior belongs to the `bid-selection-method-search-controller` capability area and operates through `GET /api/ocds/bidSelectionMethod/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `display the available bid selection methods these are taken from tender procurement method details` (`GET /api/ocds/bidSelectionMethod/all`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `display the available bid selection methods these are taken from tender procurement method details`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `display the available bid selection methods these are taken from tender procurement method details`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `display the available bid selection methods these are taken from tender procurement method details`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 62: Display The Available Bid Selection Methods These Are Taken From Tender Procurement Method Details

Business goal:
Display the available bid selection methods. .

Domain context:
This behavior belongs to the `bid-selection-method-search-controller` capability area and operates through `POST /api/ocds/bidSelectionMethod/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `display the available bid selection methods these are taken from tender procurement method details` (`POST /api/ocds/bidSelectionMethod/all`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `display the available bid selection methods these are taken from tender procurement method details`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `display the available bid selection methods these are taken from tender procurement method details`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `display the available bid selection methods these are taken from tender procurement method details`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 63: Exports Bid Timeline Dashboard In Excel Format

Business goal:
Exports *Bid Timeline* dashboard in Excel format.

Domain context:
This behavior belongs to the `average-tender-and-awards-excel-controller` capability area and operates through `GET /api/ocds/bidTimelineExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports bid timeline dashboard in excel format` (`GET /api/ocds/bidTimelineExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports bid timeline dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports bid timeline dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports bid timeline dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 64: Exports Bid Timeline Dashboard In Excel Format

Business goal:
Exports *Bid Timeline* dashboard in Excel format.

Domain context:
This behavior belongs to the `average-tender-and-awards-excel-controller` capability area and operates through `POST /api/ocds/bidTimelineExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports bid timeline dashboard in excel format` (`POST /api/ocds/bidTimelineExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports bid timeline dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports bid timeline dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports bid timeline dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 65: Display The Available Bid Types These Are The Classification Entities In Ocds

Business goal:
Display the available bid types. .

Domain context:
This behavior belongs to the `bid-types-search-controller` capability area and operates through `GET /api/ocds/bidType/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `display the available bid types these are the classification entities in ocds` (`GET /api/ocds/bidType/all`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `display the available bid types these are the classification entities in ocds`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `display the available bid types these are the classification entities in ocds`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `display the available bid types these are the classification entities in ocds`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 66: Display The Available Bid Types These Are The Classification Entities In Ocds

Business goal:
Display the available bid types. .

Domain context:
This behavior belongs to the `bid-types-search-controller` capability area and operates through `POST /api/ocds/bidType/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `display the available bid types these are the classification entities in ocds` (`POST /api/ocds/bidType/all`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `display the available bid types these are the classification entities in ocds`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `display the available bid types these are the classification entities in ocds`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `display the available bid types these are the classification entities in ocds`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 67: Exports Cancelled Funding Dashboard In Excel Format

Business goal:
Exports *Cancelled funding* dashboard in Excel format.

Domain context:
This behavior belongs to the `total-cancelled-tenders-excel-controller` capability area and operates through `GET /api/ocds/cancelledFundingExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports cancelled funding dashboard in excel format` (`GET /api/ocds/cancelledFundingExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports cancelled funding dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cancelled funding dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cancelled funding dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 68: Exports Cancelled Funding Dashboard In Excel Format

Business goal:
Exports *Cancelled funding* dashboard in Excel format.

Domain context:
This behavior belongs to the `total-cancelled-tenders-excel-controller` capability area and operates through `POST /api/ocds/cancelledFundingExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports cancelled funding dashboard in excel format` (`POST /api/ocds/cancelledFundingExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports cancelled funding dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cancelled funding dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cancelled funding dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 69: Exports Cancelled Funding Percentage Dashboard In Excel Format

Business goal:
Exports *Cancelled funding (percentage)* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-percentages-excel-controller` capability area and operates through `GET /api/ocds/cancelledFundingPercentageExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports cancelled funding percentage dashboard in excel format` (`GET /api/ocds/cancelledFundingPercentageExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports cancelled funding percentage dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cancelled funding percentage dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cancelled funding percentage dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 70: Exports Cancelled Funding Percentage Dashboard In Excel Format

Business goal:
Exports *Cancelled funding (percentage)* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-percentages-excel-controller` capability area and operates through `POST /api/ocds/cancelledFundingPercentageExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports cancelled funding percentage dashboard in excel format` (`POST /api/ocds/cancelledFundingPercentageExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports cancelled funding percentage dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cancelled funding percentage dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cancelled funding percentage dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 71: Exports Cancelled Funding By Reason Dashboard In Excel Format

Business goal:
Exports *Cancelled funding by reason* dashboard in Excel format.

Domain context:
This behavior belongs to the `total-cancelled-tenders-excel-controller` capability area and operates through `GET /api/ocds/cancelledTendersByYearByRationaleExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports cancelled funding by reason dashboard in excel format` (`GET /api/ocds/cancelledTendersByYearByRationaleExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports cancelled funding by reason dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cancelled funding by reason dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cancelled funding by reason dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 72: Exports Cancelled Funding By Reason Dashboard In Excel Format

Business goal:
Exports *Cancelled funding by reason* dashboard in Excel format.

Domain context:
This behavior belongs to the `total-cancelled-tenders-excel-controller` capability area and operates through `POST /api/ocds/cancelledTendersByYearByRationaleExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports cancelled funding by reason dashboard in excel format` (`POST /api/ocds/cancelledTendersByYearByRationaleExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports cancelled funding by reason dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cancelled funding by reason dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cancelled funding by reason dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 73: Org Cities

Business goal:
orgCities.

Domain context:
This behavior belongs to the `city-search-controller` capability area and operates through `GET /api/ocds/city/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `org cities` (`GET /api/ocds/city/all`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `org cities`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `org cities`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `org cities`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 74: Org Cities

Business goal:
orgCities.

Domain context:
This behavior belongs to the `city-search-controller` capability area and operates through `POST /api/ocds/city/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `org cities` (`POST /api/ocds/city/all`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `org cities`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `org cities`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `org cities`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 75: Cities Search

Business goal:
citiesSearch.

Domain context:
This behavior belongs to the `city-search-controller` capability area and operates through `GET /api/ocds/city/search`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `cities search` (`GET /api/ocds/city/search`) with query: text optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `text`, `pageNumber`, `pageSize` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `cities search`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cities search`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cities search`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 76: Cities Search

Business goal:
citiesSearch.

Domain context:
This behavior belongs to the `city-search-controller` capability area and operates through `POST /api/ocds/city/search`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `cities search` (`POST /api/ocds/city/search`) with query: text optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `text`, `pageNumber`, `pageSize` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `cities search`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cities search`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `cities search`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 77: Contr Methods

Business goal:
contrMethods.

Domain context:
This behavior belongs to the `contr-method-search-controller` capability area and operates through `GET /api/ocds/contrMethod/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `contr methods` (`GET /api/ocds/contrMethod/all`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `contr methods`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `contr methods`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `contr methods`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 78: Contr Methods

Business goal:
contrMethods.

Domain context:
This behavior belongs to the `contr-method-search-controller` capability area and operates through `POST /api/ocds/contrMethod/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `contr methods` (`POST /api/ocds/contrMethod/all`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `contr methods`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `contr methods`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `contr methods`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 79: Exports Cost Effectiveness Dashboard In Excel Format

Business goal:
Exports *Cost effectiveness* dashboard in Excel format.

Domain context:
This behavior belongs to the `cost-effectiveness-excel-controller` capability area and operates through `GET /api/ocds/costEffectivenessExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports cost effectiveness dashboard in excel format` (`GET /api/ocds/costEffectivenessExcelChart`) with query: groupByCategory optional, year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `groupByCategory`, `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports cost effectiveness dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cost effectiveness dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cost effectiveness dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 80: Exports Cost Effectiveness Dashboard In Excel Format

Business goal:
Exports *Cost effectiveness* dashboard in Excel format.

Domain context:
This behavior belongs to the `cost-effectiveness-excel-controller` capability area and operates through `POST /api/ocds/costEffectivenessExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports cost effectiveness dashboard in excel format` (`POST /api/ocds/costEffectivenessExcelChart`) with query: groupByCategory optional, year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `groupByCategory`, `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports cost effectiveness dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cost effectiveness dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports cost effectiveness dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 81: Export Releases In Excel Format

Business goal:
Export releases in Excel format.

Domain context:
This behavior belongs to the `excel-export-controller` capability area and operates through `GET /api/ocds/excelExport`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `export releases in excel format` (`GET /api/ocds/excelExport`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `export releases in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `export releases in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `export releases in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 82: Export Releases In Excel Format

Business goal:
Export releases in Excel format.

Domain context:
This behavior belongs to the `excel-export-controller` capability area and operates through `POST /api/ocds/excelExport`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `export releases in excel format` (`POST /api/ocds/excelExport`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `export releases in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `export releases in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `export releases in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 83: Locations All

Business goal:
locationsAll.

Domain context:
This behavior belongs to the `location-search-controller` capability area and operates through `GET /api/ocds/location/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `locations all` (`GET /api/ocds/location/all`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `locations all`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `locations all`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `locations all`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 84: Locations All

Business goal:
locationsAll.

Domain context:
This behavior belongs to the `location-search-controller` capability area and operates through `POST /api/ocds/location/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `locations all` (`POST /api/ocds/location/all`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `locations all`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `locations all`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `locations all`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 85: Locations Search

Business goal:
locationsSearch.

Domain context:
This behavior belongs to the `location-search-controller` capability area and operates through `GET /api/ocds/location/search`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `locations search` (`GET /api/ocds/location/search`) with query: text optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `text`, `pageNumber`, `pageSize` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `locations search`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `locations search`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `locations search`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 86: Locations Search

Business goal:
locationsSearch.

Domain context:
This behavior belongs to the `location-search-controller` capability area and operates through `POST /api/ocds/location/search`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `locations search` (`POST /api/ocds/location/search`) with query: text optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `text`, `pageNumber`, `pageSize` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `locations search`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `locations search`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `locations search`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 87: Exports Number Of Cancelled Bids Dashboard In Excel Format

Business goal:
Exports *Number of cancelled bids* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-percentages-excel-controller` capability area and operates through `GET /api/ocds/numberCancelledFundingExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports number of cancelled bids dashboard in excel format` (`GET /api/ocds/numberCancelledFundingExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports number of cancelled bids dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports number of cancelled bids dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports number of cancelled bids dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 88: Exports Number Of Cancelled Bids Dashboard In Excel Format

Business goal:
Exports *Number of cancelled bids* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-percentages-excel-controller` capability area and operates through `POST /api/ocds/numberCancelledFundingExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports number of cancelled bids dashboard in excel format` (`POST /api/ocds/numberCancelledFundingExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports number of cancelled bids dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports number of cancelled bids dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports number of cancelled bids dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 89: Exports Number Of E Bid Awards Dashboard In Excel Format

Business goal:
Exports *Number of eBid Awards* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-percentages-excel-controller` capability area and operates through `GET /api/ocds/numberTendersUsingEBidExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports number of e bid awards dashboard in excel format` (`GET /api/ocds/numberTendersUsingEBidExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports number of e bid awards dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports number of e bid awards dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports number of e bid awards dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 90: Exports Number Of E Bid Awards Dashboard In Excel Format

Business goal:
Exports *Number of eBid Awards* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-percentages-excel-controller` capability area and operates through `POST /api/ocds/numberTendersUsingEBidExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports number of e bid awards dashboard in excel format` (`POST /api/ocds/numberTendersUsingEBidExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports number of e bid awards dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports number of e bid awards dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports number of e bid awards dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 91: Org Departments

Business goal:
orgDepartments.

Domain context:
This behavior belongs to the `org-department-search-controller` capability area and operates through `GET /api/ocds/orgDepartment/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `org departments` (`GET /api/ocds/orgDepartment/all`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `org departments`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `org departments`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `org departments`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 92: Org Departments

Business goal:
orgDepartments.

Domain context:
This behavior belongs to the `org-department-search-controller` capability area and operates through `POST /api/ocds/orgDepartment/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `org departments` (`POST /api/ocds/orgDepartment/all`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `org departments`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `org departments`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `org departments`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 93: Department Search

Business goal:
departmentSearch.

Domain context:
This behavior belongs to the `org-department-search-controller` capability area and operates through `GET /api/ocds/orgDepartment/search`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `department search` (`GET /api/ocds/orgDepartment/search`) with query: text optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `text`, `pageNumber`, `pageSize` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `department search`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `department search`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `department search`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 94: Department Search

Business goal:
departmentSearch.

Domain context:
This behavior belongs to the `org-department-search-controller` capability area and operates through `POST /api/ocds/orgDepartment/search`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `department search` (`POST /api/ocds/orgDepartment/search`) with query: text optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `text`, `pageNumber`, `pageSize` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `department search`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `department search`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `department search`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 95: Org Groups

Business goal:
orgGroups.

Domain context:
This behavior belongs to the `org-group-search-controller` capability area and operates through `GET /api/ocds/orgGroup/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `org groups` (`GET /api/ocds/orgGroup/all`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `org groups`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `org groups`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `org groups`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 96: Org Groups

Business goal:
orgGroups.

Domain context:
This behavior belongs to the `org-group-search-controller` capability area and operates through `POST /api/ocds/orgGroup/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `org groups` (`POST /api/ocds/orgGroup/all`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `org groups`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `org groups`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `org groups`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 97: Groups Search

Business goal:
groupsSearch.

Domain context:
This behavior belongs to the `org-group-search-controller` capability area and operates through `GET /api/ocds/orgGroup/search`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `groups search` (`GET /api/ocds/orgGroup/search`) with query: text optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `text`, `pageNumber`, `pageSize` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `groups search`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `groups search`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `groups search`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 98: Groups Search

Business goal:
groupsSearch.

Domain context:
This behavior belongs to the `org-group-search-controller` capability area and operates through `POST /api/ocds/orgGroup/search`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `groups search` (`POST /api/ocds/orgGroup/search`) with query: text optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `text`, `pageNumber`, `pageSize` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `groups search`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `groups search`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `groups search`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 99: Lists All Organizations In The Database Allows Full Text Search Using The Text Parameter

Business goal:
Lists all organizations in the database. .

Domain context:
This behavior belongs to the `organization-search-controller` capability area and operates through `GET /api/ocds/organization/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `lists all organizations in the database allows full text search using the text parameter` (`GET /api/ocds/organization/all`) with body: request optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `lists all organizations in the database allows full text search using the text parameter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all organizations in the database allows full text search using the text parameter`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all organizations in the database allows full text search using the text parameter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 100: Lists All Organizations In The Database Allows Full Text Search Using The Text Parameter

Business goal:
Lists all organizations in the database. .

Domain context:
This behavior belongs to the `organization-search-controller` capability area and operates through `POST /api/ocds/organization/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `lists all organizations in the database allows full text search using the text parameter` (`POST /api/ocds/organization/all`) with body: request optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `lists all organizations in the database allows full text search using the text parameter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all organizations in the database allows full text search using the text parameter`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all organizations in the database allows full text search using the text parameter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 101: Lists All Buyers In The Database Suppliers Are Organizations That Have The Label Buyer Assigned To Organization Types Array Allows Full Text Search Using The Text Parameter

Business goal:
Lists all buyers in the database. .

Domain context:
This behavior belongs to the `buyer-search-controller` capability area and operates through `GET /api/ocds/organization/buyer/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `lists all buyers in the database suppliers are organizations that have the label buyer assigned to organization types array allows full text search using the text parameter` (`GET /api/ocds/organization/buyer/all`) with body: request optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `lists all buyers in the database suppliers are organizations that have the label buyer assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all buyers in the database suppliers are organizations that have the label buyer assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all buyers in the database suppliers are organizations that have the label buyer assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 102: Lists All Buyers In The Database Suppliers Are Organizations That Have The Label Buyer Assigned To Organization Types Array Allows Full Text Search Using The Text Parameter

Business goal:
Lists all buyers in the database. .

Domain context:
This behavior belongs to the `buyer-search-controller` capability area and operates through `POST /api/ocds/organization/buyer/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `lists all buyers in the database suppliers are organizations that have the label buyer assigned to organization types array allows full text search using the text parameter` (`POST /api/ocds/organization/buyer/all`) with body: request optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `lists all buyers in the database suppliers are organizations that have the label buyer assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all buyers in the database suppliers are organizations that have the label buyer assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all buyers in the database suppliers are organizations that have the label buyer assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 103: Finds Buyer Entity By The Given Id

Business goal:
Finds buyer entity by the given id.

Domain context:
This behavior belongs to the `buyer-search-controller` capability area and operates through `GET /api/ocds/organization/buyer/id/{id}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `finds buyer entity by the given id` (`GET /api/ocds/organization/buyer/id/{id}`) with path: id required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `id` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `id`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `finds buyer entity by the given id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds buyer entity by the given id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds buyer entity by the given id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 104: Finds Buyer Entity By The Given Id

Business goal:
Finds buyer entity by the given id.

Domain context:
This behavior belongs to the `buyer-search-controller` capability area and operates through `POST /api/ocds/organization/buyer/id/{id}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `finds buyer entity by the given id` (`POST /api/ocds/organization/buyer/id/{id}`) with path: id required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `id` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `id`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `finds buyer entity by the given id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds buyer entity by the given id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds buyer entity by the given id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 105: Finds Organization Entity By The Given Id

Business goal:
Finds organization entity by the given id.

Domain context:
This behavior belongs to the `organization-search-controller` capability area and operates through `GET /api/ocds/organization/id/{id}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `finds organization entity by the given id` (`GET /api/ocds/organization/id/{id}`) with path: id required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `id` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `id`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `finds organization entity by the given id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds organization entity by the given id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds organization entity by the given id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 106: Finds Organization Entity By The Given Id

Business goal:
Finds organization entity by the given id.

Domain context:
This behavior belongs to the `organization-search-controller` capability area and operates through `POST /api/ocds/organization/id/{id}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `finds organization entity by the given id` (`POST /api/ocds/organization/id/{id}`) with path: id required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `id` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `id`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `finds organization entity by the given id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds organization entity by the given id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds organization entity by the given id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 107: Finds Organization Entities By The Given List Of Ids Comma Separated

Business goal:
Finds organization entities by the given list of ids, comma separated.

Domain context:
This behavior belongs to the `organization-search-controller` capability area and operates through `GET /api/ocds/organization/ids`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `finds organization entities by the given list of ids comma separated` (`GET /api/ocds/organization/ids`) with query: id optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `id` filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `finds organization entities by the given list of ids comma separated`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds organization entities by the given list of ids comma separated`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds organization entities by the given list of ids comma separated`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 108: Finds Organization Entities By The Given List Of Ids Comma Separated

Business goal:
Finds organization entities by the given list of ids, comma separated.

Domain context:
This behavior belongs to the `organization-search-controller` capability area and operates through `POST /api/ocds/organization/ids`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `finds organization entities by the given list of ids comma separated` (`POST /api/ocds/organization/ids`) with query: id optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `id` filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `finds organization entities by the given list of ids comma separated`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds organization entities by the given list of ids comma separated`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds organization entities by the given list of ids comma separated`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 109: Lists All Procuring Entities In The Database Procuring Entities Are Organizations That Have The Label Procuring Entity Assigned To Organization Types Array Allows Full Text Search Using The Text Parameter

Business goal:
Lists all procuring entities in the database. .

Domain context:
This behavior belongs to the `procuring-entity-search-controller` capability area and operates through `GET /api/ocds/organization/procuringEntity/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `lists all procuring entities in the database procuring entities are organizations that have the label procuring entity assigned to organization types array allows full text search using the text parameter` (`GET /api/ocds/organization/procuringEntity/all`) with body: request optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `lists all procuring entities in the database procuring entities are organizations that have the label procuring entity assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all procuring entities in the database procuring entities are organizations that have the label procuring entity assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all procuring entities in the database procuring entities are organizations that have the label procuring entity assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 110: Lists All Procuring Entities In The Database Procuring Entities Are Organizations That Have The Label Procuring Entity Assigned To Organization Types Array Allows Full Text Search Using The Text Parameter

Business goal:
Lists all procuring entities in the database. .

Domain context:
This behavior belongs to the `procuring-entity-search-controller` capability area and operates through `POST /api/ocds/organization/procuringEntity/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `lists all procuring entities in the database procuring entities are organizations that have the label procuring entity assigned to organization types array allows full text search using the text parameter` (`POST /api/ocds/organization/procuringEntity/all`) with body: request optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `lists all procuring entities in the database procuring entities are organizations that have the label procuring entity assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all procuring entities in the database procuring entities are organizations that have the label procuring entity assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all procuring entities in the database procuring entities are organizations that have the label procuring entity assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 111: Finds Procuring Entities By The Given Id

Business goal:
Finds procuringEntities by the given id.

Domain context:
This behavior belongs to the `procuring-entity-search-controller` capability area and operates through `GET /api/ocds/organization/procuringEntity/id/{id}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `finds procuring entities by the given id` (`GET /api/ocds/organization/procuringEntity/id/{id}`) with path: id required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `id` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `id`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `finds procuring entities by the given id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds procuring entities by the given id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds procuring entities by the given id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 112: Finds Procuring Entities By The Given Id

Business goal:
Finds procuringEntities by the given id.

Domain context:
This behavior belongs to the `procuring-entity-search-controller` capability area and operates through `POST /api/ocds/organization/procuringEntity/id/{id}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `finds procuring entities by the given id` (`POST /api/ocds/organization/procuringEntity/id/{id}`) with path: id required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `id` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `id`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `finds procuring entities by the given id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds procuring entities by the given id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds procuring entities by the given id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 113: Lists All Suppliers In The Database Suppliers Are Organizations That Have The Label Supplier Assigned To Organization Types Array Allows Full Text Search Using The Text Parameter

Business goal:
Lists all suppliers in the database. .

Domain context:
This behavior belongs to the `supplier-search-controller` capability area and operates through `GET /api/ocds/organization/supplier/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `lists all suppliers in the database suppliers are organizations that have the label supplier assigned to organization types array allows full text search using the text parameter` (`GET /api/ocds/organization/supplier/all`) with body: request optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `lists all suppliers in the database suppliers are organizations that have the label supplier assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all suppliers in the database suppliers are organizations that have the label supplier assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all suppliers in the database suppliers are organizations that have the label supplier assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 114: Lists All Suppliers In The Database Suppliers Are Organizations That Have The Label Supplier Assigned To Organization Types Array Allows Full Text Search Using The Text Parameter

Business goal:
Lists all suppliers in the database. .

Domain context:
This behavior belongs to the `supplier-search-controller` capability area and operates through `POST /api/ocds/organization/supplier/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `lists all suppliers in the database suppliers are organizations that have the label supplier assigned to organization types array allows full text search using the text parameter` (`POST /api/ocds/organization/supplier/all`) with body: request optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `lists all suppliers in the database suppliers are organizations that have the label supplier assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all suppliers in the database suppliers are organizations that have the label supplier assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `lists all suppliers in the database suppliers are organizations that have the label supplier assigned to organization types array allows full text search using the text parameter`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 115: Finds Supplier By The Given Id

Business goal:
Finds supplier by the given id.

Domain context:
This behavior belongs to the `supplier-search-controller` capability area and operates through `GET /api/ocds/organization/supplier/id/{id}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `finds supplier by the given id` (`GET /api/ocds/organization/supplier/id/{id}`) with path: id required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `id` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `id`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `finds supplier by the given id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds supplier by the given id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds supplier by the given id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 116: Finds Supplier By The Given Id

Business goal:
Finds supplier by the given id.

Domain context:
This behavior belongs to the `supplier-search-controller` capability area and operates through `POST /api/ocds/organization/supplier/id/{id}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `finds supplier by the given id` (`POST /api/ocds/organization/supplier/id/{id}`) with path: id required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `id` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `id`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `finds supplier by the given id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds supplier by the given id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `finds supplier by the given id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 117: Returns All Available Packages Filtered By The Given Criteria This Will Contain The Ocds Package Information Metadata About Publisher Plus The Release Itself

Business goal:
Returns all available packages, filtered by the given criteria.This will contain the OCDS package information (metadata about publisher) plus the release itself.

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `GET /api/ocds/package/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns all available packages filtered by the given criteria this will contain the ocds package information metadata about publisher plus the release itself` (`GET /api/ocds/package/all`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns all available packages filtered by the given criteria this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns all available packages filtered by the given criteria this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns all available packages filtered by the given criteria this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 118: Returns All Available Packages Filtered By The Given Criteria This Will Contain The Ocds Package Information Metadata About Publisher Plus The Release Itself

Business goal:
Returns all available packages, filtered by the given criteria.This will contain the OCDS package information (metadata about publisher) plus the release itself.

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `POST /api/ocds/package/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns all available packages filtered by the given criteria this will contain the ocds package information metadata about publisher plus the release itself` (`POST /api/ocds/package/all`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns all available packages filtered by the given criteria this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns all available packages filtered by the given criteria this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns all available packages filtered by the given criteria this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 119: Returns A Release Package For The Given Project Id The Project Id Is Read From Planning Budget Project Id This Will Contain The Ocds Package Information Metadata About Publisher Plus The Release Itself

Business goal:
Returns a release package for the given project id. .

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `GET /api/ocds/package/budgetProjectId/{projectId}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns a release package for the given project id the project id is read from planning budget project id this will contain the ocds package information metadata about publisher plus the release itself` (`GET /api/ocds/package/budgetProjectId/{projectId}`) with path: projectId required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `projectId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `projectId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns a release package for the given project id the project id is read from planning budget project id this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release package for the given project id the project id is read from planning budget project id this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release package for the given project id the project id is read from planning budget project id this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 120: Returns A Release Package For The Given Project Id The Project Id Is Read From Planning Budget Project Id This Will Contain The Ocds Package Information Metadata About Publisher Plus The Release Itself

Business goal:
Returns a release package for the given project id. .

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `POST /api/ocds/package/budgetProjectId/{projectId}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns a release package for the given project id the project id is read from planning budget project id this will contain the ocds package information metadata about publisher plus the release itself` (`POST /api/ocds/package/budgetProjectId/{projectId}`) with path: projectId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `projectId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `projectId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns a release package for the given project id the project id is read from planning budget project id this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release package for the given project id the project id is read from planning budget project id this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release package for the given project id the project id is read from planning budget project id this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 121: Returns A Release Package For The Given Open Contracting Id Ocid This Will Contain The Ocds Package Information Metadata About Publisher Plus The Release Itself

Business goal:
Returns a release package for the given open contracting id (OCID).This will contain the OCDS package information (metadata about publisher) plus the release itself.

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `GET /api/ocds/package/ocid/{ocid}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself` (`GET /api/ocds/package/ocid/{ocid}`) with path: ocid required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `ocid` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `ocid`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 122: Returns A Release Package For The Given Open Contracting Id Ocid This Will Contain The Ocds Package Information Metadata About Publisher Plus The Release Itself

Business goal:
Returns a release package for the given open contracting id (OCID).This will contain the OCDS package information (metadata about publisher) plus the release itself.

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `POST /api/ocds/package/ocid/{ocid}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself` (`POST /api/ocds/package/ocid/{ocid}`) with path: ocid required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `ocid` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `ocid`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 123: Returns A Release Package For The Given Open Contracting Id Ocid This Will Contain The Ocds Package Information Metadata About Publisher Plus The Release Itself

Business goal:
Returns a release package for the given open contracting id (OCID).This will contain the OCDS package information (metadata about publisher) plus the release itself.

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `GET /api/ocds/package/planningBidNo/{bidNo}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself` (`GET /api/ocds/package/planningBidNo/{bidNo}`) with path: bidNo required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `bidNo` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `bidNo`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 124: Returns A Release Package For The Given Open Contracting Id Ocid This Will Contain The Ocds Package Information Metadata About Publisher Plus The Release Itself

Business goal:
Returns a release package for the given open contracting id (OCID).This will contain the OCDS package information (metadata about publisher) plus the release itself.

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `POST /api/ocds/package/planningBidNo/{bidNo}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself` (`POST /api/ocds/package/planningBidNo/{bidNo}`) with path: bidNo required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `bidNo` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `bidNo`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release package for the given open contracting id ocid this will contain the ocds package information metadata about publisher plus the release itself`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 125: Exports Percent Of Tenders Using E Bid Dashboard In Excel Format

Business goal:
Exports *Percent of Tenders Using e-Bid* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-percentages-excel-controller` capability area and operates through `GET /api/ocds/percentTendersUsingEBidExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports percent of tenders using e bid dashboard in excel format` (`GET /api/ocds/percentTendersUsingEBidExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports percent of tenders using e bid dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports percent of tenders using e bid dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports percent of tenders using e bid dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 126: Exports Percent Of Tenders Using E Bid Dashboard In Excel Format

Business goal:
Exports *Percent of Tenders Using e-Bid* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-percentages-excel-controller` capability area and operates through `POST /api/ocds/percentTendersUsingEBidExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports percent of tenders using e bid dashboard in excel format` (`POST /api/ocds/percentTendersUsingEBidExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports percent of tenders using e bid dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports percent of tenders using e bid dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports percent of tenders using e bid dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 127: Exports Percent Of Tenders Using E Procurement Dashboard In Excel Format

Business goal:
Exports *Percent of Tenders Using e-Procurement* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-percentages-excel-controller` capability area and operates through `GET /api/ocds/percentTendersUsingEgpExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports percent of tenders using e procurement dashboard in excel format` (`GET /api/ocds/percentTendersUsingEgpExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports percent of tenders using e procurement dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports percent of tenders using e procurement dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports percent of tenders using e procurement dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 128: Exports Percent Of Tenders Using E Procurement Dashboard In Excel Format

Business goal:
Exports *Percent of Tenders Using e-Procurement* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-percentages-excel-controller` capability area and operates through `POST /api/ocds/percentTendersUsingEgpExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports percent of tenders using e procurement dashboard in excel format` (`POST /api/ocds/percentTendersUsingEgpExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports percent of tenders using e procurement dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports percent of tenders using e procurement dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports percent of tenders using e procurement dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 129: Exports Procurement Activity By Year Dashboard In Excel Format

Business goal:
Exports *Procurement activity by year* dashboard in Excel format.

Domain context:
This behavior belongs to the `procurement-activity-by-year-controller` capability area and operates through `GET /api/ocds/procurementActivityExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports procurement activity by year dashboard in excel format` (`GET /api/ocds/procurementActivityExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports procurement activity by year dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports procurement activity by year dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports procurement activity by year dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 130: Exports Procurement Activity By Year Dashboard In Excel Format

Business goal:
Exports *Procurement activity by year* dashboard in Excel format.

Domain context:
This behavior belongs to the `procurement-activity-by-year-controller` capability area and operates through `POST /api/ocds/procurementActivityExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports procurement activity by year dashboard in excel format` (`POST /api/ocds/procurementActivityExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports procurement activity by year dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports procurement activity by year dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports procurement activity by year dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 131: Display The Available Procurement Methods These Are Taken From Tender Procurement Method

Business goal:
Display the available procurement methods. .

Domain context:
This behavior belongs to the `procurement-method-search-controller` capability area and operates through `GET /api/ocds/procurementMethod/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `display the available procurement methods these are taken from tender procurement method` (`GET /api/ocds/procurementMethod/all`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `display the available procurement methods these are taken from tender procurement method`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `display the available procurement methods these are taken from tender procurement method`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `display the available procurement methods these are taken from tender procurement method`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 132: Display The Available Procurement Methods These Are Taken From Tender Procurement Method

Business goal:
Display the available procurement methods. .

Domain context:
This behavior belongs to the `procurement-method-search-controller` capability area and operates through `POST /api/ocds/procurementMethod/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `display the available procurement methods these are taken from tender procurement method` (`POST /api/ocds/procurementMethod/all`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `display the available procurement methods these are taken from tender procurement method`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `display the available procurement methods these are taken from tender procurement method`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `display the available procurement methods these are taken from tender procurement method`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 133: Exports Procurement Method Dashboard In Excel Format

Business goal:
Exports *Procurement method* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-price-excel-controller` capability area and operates through `GET /api/ocds/procurementMethodExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports procurement method dashboard in excel format` (`GET /api/ocds/procurementMethodExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports procurement method dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports procurement method dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports procurement method dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 134: Exports Procurement Method Dashboard In Excel Format

Business goal:
Exports *Procurement method* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-price-excel-controller` capability area and operates through `POST /api/ocds/procurementMethodExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports procurement method dashboard in excel format` (`POST /api/ocds/procurementMethodExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports procurement method dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports procurement method dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports procurement method dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 135: Resturns All Available Releases Filtered By The Given Criteria

Business goal:
Resturns all available releases, filtered by the given criteria.

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `GET /api/ocds/release/all`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `resturns all available releases filtered by the given criteria` (`GET /api/ocds/release/all`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `resturns all available releases filtered by the given criteria`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `resturns all available releases filtered by the given criteria`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `resturns all available releases filtered by the given criteria`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 136: Resturns All Available Releases Filtered By The Given Criteria

Business goal:
Resturns all available releases, filtered by the given criteria.

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `POST /api/ocds/release/all`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `resturns all available releases filtered by the given criteria` (`POST /api/ocds/release/all`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `resturns all available releases filtered by the given criteria`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `resturns all available releases filtered by the given criteria`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `resturns all available releases filtered by the given criteria`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 137: Returns A Release Entity For The Given Project Id The Project Id Is Read From Planning Budget Project Id

Business goal:
Returns a release entity for the given project id. .

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `GET /api/ocds/release/budgetProjectId/{projectId}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns a release entity for the given project id the project id is read from planning budget project id` (`GET /api/ocds/release/budgetProjectId/{projectId}`) with path: projectId required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `projectId` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `projectId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns a release entity for the given project id the project id is read from planning budget project id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release entity for the given project id the project id is read from planning budget project id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release entity for the given project id the project id is read from planning budget project id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 138: Returns A Release Entity For The Given Project Id The Project Id Is Read From Planning Budget Project Id

Business goal:
Returns a release entity for the given project id. .

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `POST /api/ocds/release/budgetProjectId/{projectId}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns a release entity for the given project id the project id is read from planning budget project id` (`POST /api/ocds/release/budgetProjectId/{projectId}`) with path: projectId required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `projectId` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `projectId`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns a release entity for the given project id the project id is read from planning budget project id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release entity for the given project id the project id is read from planning budget project id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release entity for the given project id the project id is read from planning budget project id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 139: Returns A Release Entity For The Given Open Contracting Id Ocid

Business goal:
Returns a release entity for the given open contracting id (OCID).

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `GET /api/ocds/release/ocid/{ocid}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns a release entity for the given open contracting id ocid` (`GET /api/ocds/release/ocid/{ocid}`) with path: ocid required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `ocid` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `ocid`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns a release entity for the given open contracting id ocid`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release entity for the given open contracting id ocid`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release entity for the given open contracting id ocid`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 140: Returns A Release Entity For The Given Open Contracting Id Ocid

Business goal:
Returns a release entity for the given open contracting id (OCID).

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `POST /api/ocds/release/ocid/{ocid}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns a release entity for the given open contracting id ocid` (`POST /api/ocds/release/ocid/{ocid}`) with path: ocid required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `ocid` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `ocid`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns a release entity for the given open contracting id ocid`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release entity for the given open contracting id ocid`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release entity for the given open contracting id ocid`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 141: Returns A Release Entity For The Given Planning Bid Number The Planning Bid Number Is Taken From Planning Bid No

Business goal:
Returns a release entity for the given Planning Bid Number.The planning bid number is taken from planning.bidNo.

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `GET /api/ocds/release/planningBidNo/{bidNo}`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns a release entity for the given planning bid number the planning bid number is taken from planning bid no` (`GET /api/ocds/release/planningBidNo/{bidNo}`) with path: bidNo required.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Path values `bidNo` identify the business resource scope for the operation.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- Required request values: `bidNo`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns a release entity for the given planning bid number the planning bid number is taken from planning bid no`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release entity for the given planning bid number the planning bid number is taken from planning bid no`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release entity for the given planning bid number the planning bid number is taken from planning bid no`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 142: Returns A Release Entity For The Given Planning Bid Number The Planning Bid Number Is Taken From Planning Bid No

Business goal:
Returns a release entity for the given Planning Bid Number.The planning bid number is taken from planning.bidNo.

Domain context:
This behavior belongs to the `ocds-controller` capability area and operates through `POST /api/ocds/release/planningBidNo/{bidNo}`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns a release entity for the given planning bid number the planning bid number is taken from planning bid no` (`POST /api/ocds/release/planningBidNo/{bidNo}`) with path: bidNo required.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Path values `bidNo` identify the business resource scope for the operation.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- Required request values: `bidNo`.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns a release entity for the given planning bid number the planning bid number is taken from planning bid no`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release entity for the given planning bid number the planning bid number is taken from planning bid no`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns a release entity for the given planning bid number the planning bid number is taken from planning bid no`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 143: Exports Number Of Bids By Item Dashboard In Excel Format

Business goal:
Exports *Number of bids by item* dashboard in Excel format.

Domain context:
This behavior belongs to the `tenders-by-item-excel-controller` capability area and operates through `GET /api/ocds/tendersByItemExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports number of bids by item dashboard in excel format` (`GET /api/ocds/tendersByItemExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports number of bids by item dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports number of bids by item dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports number of bids by item dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 144: Exports Number Of Bids By Item Dashboard In Excel Format

Business goal:
Exports *Number of bids by item* dashboard in Excel format.

Domain context:
This behavior belongs to the `tenders-by-item-excel-controller` capability area and operates through `POST /api/ocds/tendersByItemExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports number of bids by item dashboard in excel format` (`POST /api/ocds/tendersByItemExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports number of bids by item dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports number of bids by item dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports number of bids by item dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 145: Exports Percentage Of Plans With Tender Dashboard In Excel Format

Business goal:
Exports *Percentage of plans with tender* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-percentages-excel-controller` capability area and operates through `GET /api/ocds/tendersWithLinkedProcurementPlanExcelChart`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `exports percentage of plans with tender dashboard in excel format` (`GET /api/ocds/tendersWithLinkedProcurementPlanExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports percentage of plans with tender dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports percentage of plans with tender dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports percentage of plans with tender dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 146: Exports Percentage Of Plans With Tender Dashboard In Excel Format

Business goal:
Exports *Percentage of plans with tender* dashboard in Excel format.

Domain context:
This behavior belongs to the `tender-percentages-excel-controller` capability area and operates through `POST /api/ocds/tendersWithLinkedProcurementPlanExcelChart`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `exports percentage of plans with tender dashboard in excel format` (`POST /api/ocds/tendersWithLinkedProcurementPlanExcelChart`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `exports percentage of plans with tender dashboard in excel format`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports percentage of plans with tender dashboard in excel format`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `exports percentage of plans with tender dashboard in excel format`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 147: Percent Of Awarded Tenders With 1 Tenderer Bidder Count Of Tenders With Number Of Tenderers 1 Divided By Total Count Of Tenders With Number Of Tenderers 0 This Endpoint Uses Tender Tender Period Start Date To Calculate The Tender Year

Business goal:
Percent of awarded tenders with >1 tenderer/bidderCount of tenders with numberOfTenderers >1 divided by total count of tenders with numberOfTenderers >0This endpoint uses tender.tenderPeriod.startDate to calculate the tender year.

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `GET /api/percentTendersAwardedWithTwoOrMoreTenderers`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `percent of awarded tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders with number of tenderers 0 this endpoint uses tender tender period start date to calculate the tender year` (`GET /api/percentTendersAwardedWithTwoOrMoreTenderers`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `percent of awarded tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders with number of tenderers 0 this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percent of awarded tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders with number of tenderers 0 this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percent of awarded tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders with number of tenderers 0 this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 148: Percent Of Awarded Tenders With 1 Tenderer Bidder Count Of Tenders With Number Of Tenderers 1 Divided By Total Count Of Tenders With Number Of Tenderers 0 This Endpoint Uses Tender Tender Period Start Date To Calculate The Tender Year

Business goal:
Percent of awarded tenders with >1 tenderer/bidderCount of tenders with numberOfTenderers >1 divided by total count of tenders with numberOfTenderers >0This endpoint uses tender.tenderPeriod.startDate to calculate the tender year.

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `POST /api/percentTendersAwardedWithTwoOrMoreTenderers`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `percent of awarded tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders with number of tenderers 0 this endpoint uses tender tender period start date to calculate the tender year` (`POST /api/percentTendersAwardedWithTwoOrMoreTenderers`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `percent of awarded tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders with number of tenderers 0 this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percent of awarded tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders with number of tenderers 0 this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percent of awarded tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders with number of tenderers 0 this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 149: Returns The Percent Of Tenders That Were Cancelled Grouped By Year The Year Is Taken From Tender Tender Period Start Date The Response Also Contains The Total Number Of Tenders And Total Number Of Cancelled Tenders For Each Year

Business goal:
Returns the percent of tenders that were cancelled, grouped by year. .

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `GET /api/percentTendersCancelled`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns the percent of tenders that were cancelled grouped by year the year is taken from tender tender period start date the response also contains the total number of tenders and total number of cancelled tenders for each year` (`GET /api/percentTendersCancelled`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the percent of tenders that were cancelled grouped by year the year is taken from tender tender period start date the response also contains the total number of tenders and total number of cancelled tenders for each year`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the percent of tenders that were cancelled grouped by year the year is taken from tender tender period start date the response also contains the total number of tenders and total number of cancelled tenders for each year`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the percent of tenders that were cancelled grouped by year the year is taken from tender tender period start date the response also contains the total number of tenders and total number of cancelled tenders for each year`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 150: Returns The Percent Of Tenders That Were Cancelled Grouped By Year The Year Is Taken From Tender Tender Period Start Date The Response Also Contains The Total Number Of Tenders And Total Number Of Cancelled Tenders For Each Year

Business goal:
Returns the percent of tenders that were cancelled, grouped by year. .

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `POST /api/percentTendersCancelled`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns the percent of tenders that were cancelled grouped by year the year is taken from tender tender period start date the response also contains the total number of tenders and total number of cancelled tenders for each year` (`POST /api/percentTendersCancelled`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the percent of tenders that were cancelled grouped by year the year is taken from tender tender period start date the response also contains the total number of tenders and total number of cancelled tenders for each year`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the percent of tenders that were cancelled grouped by year the year is taken from tender tender period start date the response also contains the total number of tenders and total number of cancelled tenders for each year`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the percent of tenders that were cancelled grouped by year the year is taken from tender tender period start date the response also contains the total number of tenders and total number of cancelled tenders for each year`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 151: Returns The Percent Of Tenders With Active Awards With Tender Submission Method Electronic Submission The Endpoint Also Returns The Total Tenderds With Active Awards And The Count Of Tenders With Tender Submission Method Electronic Submission

Business goal:
Returns the percent of tenders with active awards, with tender.submissionMethod='electronicSubmission'.The endpoint also returns the total tenderds with active awards and the count of tenders with tender.submissionMethod='electronicSubmissi.

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `GET /api/percentTendersUsingEBid`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns the percent of tenders with active awards with tender submission method electronic submission the endpoint also returns the total tenderds with active awards and the count of tenders with tender submission method electronic submission` (`GET /api/percentTendersUsingEBid`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the percent of tenders with active awards with tender submission method electronic submission the endpoint also returns the total tenderds with active awards and the count of tenders with tender submission method electronic submission`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the percent of tenders with active awards with tender submission method electronic submission the endpoint also returns the total tenderds with active awards and the count of tenders with tender submission method electronic submission`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the percent of tenders with active awards with tender submission method electronic submission the endpoint also returns the total tenderds with active awards and the count of tenders with tender submission method electronic submission`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 152: Returns The Percent Of Tenders With Active Awards With Tender Submission Method Electronic Submission The Endpoint Also Returns The Total Tenderds With Active Awards And The Count Of Tenders With Tender Submission Method Electronic Submission

Business goal:
Returns the percent of tenders with active awards, with tender.submissionMethod='electronicSubmission'.The endpoint also returns the total tenderds with active awards and the count of tenders with tender.submissionMethod='electronicSubmissi.

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `POST /api/percentTendersUsingEBid`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns the percent of tenders with active awards with tender submission method electronic submission the endpoint also returns the total tenderds with active awards and the count of tenders with tender submission method electronic submission` (`POST /api/percentTendersUsingEBid`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the percent of tenders with active awards with tender submission method electronic submission the endpoint also returns the total tenderds with active awards and the count of tenders with tender submission method electronic submission`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the percent of tenders with active awards with tender submission method electronic submission the endpoint also returns the total tenderds with active awards and the count of tenders with tender submission method electronic submission`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the percent of tenders with active awards with tender submission method electronic submission the endpoint also returns the total tenderds with active awards and the count of tenders with tender submission method electronic submission`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 153: Returns The Percent Of Tenders That Are Using E Procurement This Is Read From Tender Publication Method E Gp

Business goal:
Returns the percent of tenders that are using eProcurement. .

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `GET /api/percentTendersUsingEgp`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns the percent of tenders that are using e procurement this is read from tender publication method e gp` (`GET /api/percentTendersUsingEgp`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the percent of tenders that are using e procurement this is read from tender publication method e gp`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the percent of tenders that are using e procurement this is read from tender publication method e gp`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the percent of tenders that are using e procurement this is read from tender publication method e gp`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 154: Returns The Percent Of Tenders That Are Using E Procurement This Is Read From Tender Publication Method E Gp

Business goal:
Returns the percent of tenders that are using eProcurement. .

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `POST /api/percentTendersUsingEgp`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns the percent of tenders that are using e procurement this is read from tender publication method e gp` (`POST /api/percentTendersUsingEgp`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the percent of tenders that are using e procurement this is read from tender publication method e gp`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the percent of tenders that are using e procurement this is read from tender publication method e gp`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the percent of tenders that are using e procurement this is read from tender publication method e gp`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 155: Percentage Of Tenders That Are Associated In Releases That Have The Planning Budget Amount Non Empty Meaning There Really Is A Planning Entity Correlated With The Tender Entity This Endpoint Uses Tender Tender Period Start Date To Calculate The Tender Year

Business goal:
Percentage of tenders that are associated in releases that have the planning.budget.amount non empty,meaning there really is a planning entity correlated with the tender entity.This endpoint uses tender.tenderPeriod.startDate to calculate t.

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `GET /api/percentTendersWithLinkedProcurementPlan`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `percentage of tenders that are associated in releases that have the planning budget amount non empty meaning there really is a planning entity correlated with the tender entity this endpoint uses tender tender period start date to calculate the tender year` (`GET /api/percentTendersWithLinkedProcurementPlan`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `percentage of tenders that are associated in releases that have the planning budget amount non empty meaning there really is a planning entity correlated with the tender entity this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percentage of tenders that are associated in releases that have the planning budget amount non empty meaning there really is a planning entity correlated with the tender entity this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percentage of tenders that are associated in releases that have the planning budget amount non empty meaning there really is a planning entity correlated with the tender entity this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 156: Percentage Of Tenders That Are Associated In Releases That Have The Planning Budget Amount Non Empty Meaning There Really Is A Planning Entity Correlated With The Tender Entity This Endpoint Uses Tender Tender Period Start Date To Calculate The Tender Year

Business goal:
Percentage of tenders that are associated in releases that have the planning.budget.amount non empty,meaning there really is a planning entity correlated with the tender entity.This endpoint uses tender.tenderPeriod.startDate to calculate t.

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `POST /api/percentTendersWithLinkedProcurementPlan`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `percentage of tenders that are associated in releases that have the planning budget amount non empty meaning there really is a planning entity correlated with the tender entity this endpoint uses tender tender period start date to calculate the tender year` (`POST /api/percentTendersWithLinkedProcurementPlan`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `percentage of tenders that are associated in releases that have the planning budget amount non empty meaning there really is a planning entity correlated with the tender entity this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percentage of tenders that are associated in releases that have the planning budget amount non empty meaning there really is a planning entity correlated with the tender entity this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percentage of tenders that are associated in releases that have the planning budget amount non empty meaning there really is a planning entity correlated with the tender entity this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 157: Percentage Of Tenders With 1 Tenderer Bidder Count Of Tenders With Number Of Tenderers 1 Divided By Total Count Of Tenders This Endpoint Uses Tender Tender Period Start Date To Calculate The Tender Year

Business goal:
Percentage of tenders with >1 tenderer/bidder): Count of tenders with numberOfTenderers >1 divided by total count of tenders.This endpoint uses tender.tenderPeriod.startDate to calculate the tender year.

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `GET /api/percentTendersWithTwoOrMoreTenderers`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `percentage of tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders this endpoint uses tender tender period start date to calculate the tender year` (`GET /api/percentTendersWithTwoOrMoreTenderers`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `percentage of tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percentage of tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percentage of tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 158: Percentage Of Tenders With 1 Tenderer Bidder Count Of Tenders With Number Of Tenderers 1 Divided By Total Count Of Tenders This Endpoint Uses Tender Tender Period Start Date To Calculate The Tender Year

Business goal:
Percentage of tenders with >1 tenderer/bidder): Count of tenders with numberOfTenderers >1 divided by total count of tenders.This endpoint uses tender.tenderPeriod.startDate to calculate the tender year.

Domain context:
This behavior belongs to the `tender-percentages-controller` capability area and operates through `POST /api/percentTendersWithTwoOrMoreTenderers`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `percentage of tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders this endpoint uses tender tender period start date to calculate the tender year` (`POST /api/percentTendersWithTwoOrMoreTenderers`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `percentage of tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percentage of tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percentage of tenders with 1 tenderer bidder count of tenders with number of tenderers 1 divided by total count of tenders this endpoint uses tender tender period start date to calculate the tender year`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 159: Percentage Of Awards Where Award Publication Date Award Date Is Less Than 7 Days Percentage Should Be By Year The Denominator For The Percentage Is The Number Of Awards That Have Both Awards Date And Awards Published Date

Business goal:
Percentage of awards where award publication date - award.date is less than 7 days. .

Domain context:
This behavior belongs to the `percentage-awards-narrow-publication-dates` capability area and operates through `GET /api/percentageAwardsNarrowPublicationDates`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `percentage of awards where award publication date award date is less than 7 days percentage should be by year the denominator for the percentage is the number of awards that have both awards date and awards published date` (`GET /api/percentageAwardsNarrowPublicationDates`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `percentage of awards where award publication date award date is less than 7 days percentage should be by year the denominator for the percentage is the number of awards that have both awards date and awards published date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percentage of awards where award publication date award date is less than 7 days percentage should be by year the denominator for the percentage is the number of awards that have both awards date and awards published date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percentage of awards where award publication date award date is less than 7 days percentage should be by year the denominator for the percentage is the number of awards that have both awards date and awards published date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 160: Percentage Of Awards Where Award Publication Date Award Date Is Less Than 7 Days Percentage Should Be By Year The Denominator For The Percentage Is The Number Of Awards That Have Both Awards Date And Awards Published Date

Business goal:
Percentage of awards where award publication date - award.date is less than 7 days. .

Domain context:
This behavior belongs to the `percentage-awards-narrow-publication-dates` capability area and operates through `POST /api/percentageAwardsNarrowPublicationDates`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `percentage of awards where award publication date award date is less than 7 days percentage should be by year the denominator for the percentage is the number of awards that have both awards date and awards published date` (`POST /api/percentageAwardsNarrowPublicationDates`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `percentage of awards where award publication date award date is less than 7 days percentage should be by year the denominator for the percentage is the number of awards that have both awards date and awards published date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percentage of awards where award publication date award date is less than 7 days percentage should be by year the denominator for the percentage is the number of awards that have both awards date and awards published date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `percentage of awards where award publication date award date is less than 7 days percentage should be by year the denominator for the percentage is the number of awards that have both awards date and awards published date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 161: Planned Funding By Location By Year Returns The Total Amount Of Planning Budget Available Per Planning Budget Project Location Grouped By Year This Will Return Full Location Information Including Geocoding Data Responds Only To The Procuring Entity Id Filter Tender Procuring Entity Id

Business goal:
Planned funding by location by year. .

Domain context:
This behavior belongs to the `funding-by-location-controller` capability area and operates through `GET /api/plannedFundingByLocation`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `planned funding by location by year returns the total amount of planning budget available per planning budget project location grouped by year this will return full location information including geocoding data responds only to the procuring entity id filter tender procuring entity id` (`GET /api/plannedFundingByLocation`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `planned funding by location by year returns the total amount of planning budget available per planning budget project location grouped by year this will return full location information including geocoding data responds only to the procuring entity id filter tender procuring entity id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `planned funding by location by year returns the total amount of planning budget available per planning budget project location grouped by year this will return full location information including geocoding data responds only to the procuring entity id filter tender procuring entity id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `planned funding by location by year returns the total amount of planning budget available per planning budget project location grouped by year this will return full location information including geocoding data responds only to the procuring entity id filter tender procuring entity id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 162: Planned Funding By Location By Year Returns The Total Amount Of Planning Budget Available Per Planning Budget Project Location Grouped By Year This Will Return Full Location Information Including Geocoding Data Responds Only To The Procuring Entity Id Filter Tender Procuring Entity Id

Business goal:
Planned funding by location by year. .

Domain context:
This behavior belongs to the `funding-by-location-controller` capability area and operates through `POST /api/plannedFundingByLocation`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `planned funding by location by year returns the total amount of planning budget available per planning budget project location grouped by year this will return full location information including geocoding data responds only to the procuring entity id filter tender procuring entity id` (`POST /api/plannedFundingByLocation`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `planned funding by location by year returns the total amount of planning budget available per planning budget project location grouped by year this will return full location information including geocoding data responds only to the procuring entity id filter tender procuring entity id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `planned funding by location by year returns the total amount of planning budget available per planning budget project location grouped by year this will return full location information including geocoding data responds only to the procuring entity id filter tender procuring entity id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `planned funding by location by year returns the total amount of planning budget available per planning budget project location grouped by year this will return full location information including geocoding data responds only to the procuring entity id filter tender procuring entity id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 163: Quality Indicator For Average Award Period Endpoint Showing The Percentage Of Awards That Have Start And End Dates Vs The Total Tenders In The System

Business goal:
Quality indicator for averageAwardPeriod endpoint, showing the percentage of awards that have start and end dates vs the total tenders in the system.

Domain context:
This behavior belongs to the `average-tender-and-award-periods-controller` capability area and operates through `GET /api/qualityAverageAwardPeriod`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `quality indicator for average award period endpoint showing the percentage of awards that have start and end dates vs the total tenders in the system` (`GET /api/qualityAverageAwardPeriod`) with query: bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc`, `minTenderValue`, `maxTenderValue`, `minAwardValue` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `quality indicator for average award period endpoint showing the percentage of awards that have start and end dates vs the total tenders in the system`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `quality indicator for average award period endpoint showing the percentage of awards that have start and end dates vs the total tenders in the system`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `quality indicator for average award period endpoint showing the percentage of awards that have start and end dates vs the total tenders in the system`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 164: Quality Indicator For Average Award Period Endpoint Showing The Percentage Of Awards That Have Start And End Dates Vs The Total Tenders In The System

Business goal:
Quality indicator for averageAwardPeriod endpoint, showing the percentage of awards that have start and end dates vs the total tenders in the system.

Domain context:
This behavior belongs to the `average-tender-and-award-periods-controller` capability area and operates through `POST /api/qualityAverageAwardPeriod`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `quality indicator for average award period endpoint showing the percentage of awards that have start and end dates vs the total tenders in the system` (`POST /api/qualityAverageAwardPeriod`) with query: bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc`, `minTenderValue`, `maxTenderValue`, `minAwardValue` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `quality indicator for average award period endpoint showing the percentage of awards that have start and end dates vs the total tenders in the system`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `quality indicator for average award period endpoint showing the percentage of awards that have start and end dates vs the total tenders in the system`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `quality indicator for average award period endpoint showing the percentage of awards that have start and end dates vs the total tenders in the system`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 165: Quality Indicator For Average Tender Period Endpoint Showing The Percentage Of Tenders That Have Start And End Dates Vs The Total Tenders In The System

Business goal:
Quality indicator for averageTenderPeriod endpoint, showing the percentage of tenders that have start and end dates vs the total tenders in the system.

Domain context:
This behavior belongs to the `average-tender-and-award-periods-controller` capability area and operates through `GET /api/qualityAverageTenderPeriod`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `quality indicator for average tender period endpoint showing the percentage of tenders that have start and end dates vs the total tenders in the system` (`GET /api/qualityAverageTenderPeriod`) with query: bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc`, `minTenderValue`, `maxTenderValue`, `minAwardValue` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `quality indicator for average tender period endpoint showing the percentage of tenders that have start and end dates vs the total tenders in the system`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `quality indicator for average tender period endpoint showing the percentage of tenders that have start and end dates vs the total tenders in the system`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `quality indicator for average tender period endpoint showing the percentage of tenders that have start and end dates vs the total tenders in the system`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 166: Quality Indicator For Average Tender Period Endpoint Showing The Percentage Of Tenders That Have Start And End Dates Vs The Total Tenders In The System

Business goal:
Quality indicator for averageTenderPeriod endpoint, showing the percentage of tenders that have start and end dates vs the total tenders in the system.

Domain context:
This behavior belongs to the `average-tender-and-award-periods-controller` capability area and operates through `POST /api/qualityAverageTenderPeriod`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `quality indicator for average tender period endpoint showing the percentage of tenders that have start and end dates vs the total tenders in the system` (`POST /api/qualityAverageTenderPeriod`) with query: bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc`, `minTenderValue`, `maxTenderValue`, `minAwardValue` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `quality indicator for average tender period endpoint showing the percentage of tenders that have start and end dates vs the total tenders in the system`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `quality indicator for average tender period endpoint showing the percentage of tenders that have start and end dates vs the total tenders in the system`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `quality indicator for average tender period endpoint showing the percentage of tenders that have start and end dates vs the total tenders in the system`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 167: Calculates Percentage Of Releases With Tender With At Least One Specified Delivery Location That Is The Array Tender Items Delivery Location Has To Have Items Filters Out Stub Tenders Therefore Tender Tender Period Start Date Has To Exist

Business goal:
Calculates percentage of releases with tender with at least one specified delivery location, that is the array tender.items.deliveryLocation has to have items.Filters out stub tenders, therefore tender.tenderPeriod.startDate has to exist.

Domain context:
This behavior belongs to the `funding-by-location-controller` capability area and operates through `GET /api/qualityFundingByTenderDeliveryLocation`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `calculates percentage of releases with tender with at least one specified delivery location that is the array tender items delivery location has to have items filters out stub tenders therefore tender tender period start date has to exist` (`GET /api/qualityFundingByTenderDeliveryLocation`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `calculates percentage of releases with tender with at least one specified delivery location that is the array tender items delivery location has to have items filters out stub tenders therefore tender tender period start date has to exist`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates percentage of releases with tender with at least one specified delivery location that is the array tender items delivery location has to have items filters out stub tenders therefore tender tender period start date has to exist`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates percentage of releases with tender with at least one specified delivery location that is the array tender items delivery location has to have items filters out stub tenders therefore tender tender period start date has to exist`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 168: Calculates Percentage Of Releases With Tender With At Least One Specified Delivery Location That Is The Array Tender Items Delivery Location Has To Have Items Filters Out Stub Tenders Therefore Tender Tender Period Start Date Has To Exist

Business goal:
Calculates percentage of releases with tender with at least one specified delivery location, that is the array tender.items.deliveryLocation has to have items.Filters out stub tenders, therefore tender.tenderPeriod.startDate has to exist.

Domain context:
This behavior belongs to the `funding-by-location-controller` capability area and operates through `POST /api/qualityFundingByTenderDeliveryLocation`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `calculates percentage of releases with tender with at least one specified delivery location that is the array tender items delivery location has to have items filters out stub tenders therefore tender tender period start date has to exist` (`POST /api/qualityFundingByTenderDeliveryLocation`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `calculates percentage of releases with tender with at least one specified delivery location that is the array tender items delivery location has to have items filters out stub tenders therefore tender tender period start date has to exist`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates percentage of releases with tender with at least one specified delivery location that is the array tender items delivery location has to have items filters out stub tenders therefore tender tender period start date has to exist`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates percentage of releases with tender with at least one specified delivery location that is the array tender items delivery location has to have items filters out stub tenders therefore tender tender period start date has to exist`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 169: Calculates Percentage Of Releases With Planning With At Least One Specified Location That Is The Array Planning Budget Project Location Has To Be Initialzied Filters Out Stub Planning Therefore Planning Budget Amount Has To Exist Responds Only To The Procuring Entity Id Filter Tender Procuring Entity Id

Business goal:
Calculates percentage of releases with planning with at least one specified location, that is the array planning.budget.projectLocation has to be initialzied.Filters out stub planning, therefore planning.budget.amount has to exist.Responds .

Domain context:
This behavior belongs to the `funding-by-location-controller` capability area and operates through `GET /api/qualityPlannedFundingByLocation`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `calculates percentage of releases with planning with at least one specified location that is the array planning budget project location has to be initialzied filters out stub planning therefore planning budget amount has to exist responds only to the procuring entity id filter tender procuring entity id` (`GET /api/qualityPlannedFundingByLocation`) with query: bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc`, `minTenderValue`, `maxTenderValue`, `minAwardValue` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `calculates percentage of releases with planning with at least one specified location that is the array planning budget project location has to be initialzied filters out stub planning therefore planning budget amount has to exist responds only to the procuring entity id filter tender procuring entity id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates percentage of releases with planning with at least one specified location that is the array planning budget project location has to be initialzied filters out stub planning therefore planning budget amount has to exist responds only to the procuring entity id filter tender procuring entity id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates percentage of releases with planning with at least one specified location that is the array planning budget project location has to be initialzied filters out stub planning therefore planning budget amount has to exist responds only to the procuring entity id filter tender procuring entity id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 170: Calculates Percentage Of Releases With Planning With At Least One Specified Location That Is The Array Planning Budget Project Location Has To Be Initialzied Filters Out Stub Planning Therefore Planning Budget Amount Has To Exist Responds Only To The Procuring Entity Id Filter Tender Procuring Entity Id

Business goal:
Calculates percentage of releases with planning with at least one specified location, that is the array planning.budget.projectLocation has to be initialzied.Filters out stub planning, therefore planning.budget.amount has to exist.Responds .

Domain context:
This behavior belongs to the `funding-by-location-controller` capability area and operates through `POST /api/qualityPlannedFundingByLocation`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `calculates percentage of releases with planning with at least one specified location that is the array planning budget project location has to be initialzied filters out stub planning therefore planning budget amount has to exist responds only to the procuring entity id filter tender procuring entity id` (`POST /api/qualityPlannedFundingByLocation`) with query: bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc`, `minTenderValue`, `maxTenderValue`, `minAwardValue` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `calculates percentage of releases with planning with at least one specified location that is the array planning budget project location has to be initialzied filters out stub planning therefore planning budget amount has to exist responds only to the procuring entity id filter tender procuring entity id`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates percentage of releases with planning with at least one specified location that is the array planning budget project location has to be initialzied filters out stub planning therefore planning budget amount has to exist responds only to the procuring entity id filter tender procuring entity id`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `calculates percentage of releases with planning with at least one specified location that is the array planning budget project location has to be initialzied filters out stub planning therefore planning budget amount has to exist responds only to the procuring entity id filter tender procuring entity id`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 171: Same As Api Tender Price By Bid Selection Method But It Always Returns All Bid Selection Methods It Adds The Missing Bid Selection Methods With Zero Totals

Business goal:
Same as /api/tenderPriceByBidSelectionMethod, but it always returns all bidSelectionMethods (it adds the missing bid selection methods with zero totals.

Domain context:
This behavior belongs to the `tender-price-by-type-year-controller` capability area and operates through `GET /api/tenderPriceByAllBidSelectionMethods`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `same as api tender price by bid selection method but it always returns all bid selection methods it adds the missing bid selection methods with zero totals` (`GET /api/tenderPriceByAllBidSelectionMethods`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `same as api tender price by bid selection method but it always returns all bid selection methods it adds the missing bid selection methods with zero totals`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `same as api tender price by bid selection method but it always returns all bid selection methods it adds the missing bid selection methods with zero totals`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `same as api tender price by bid selection method but it always returns all bid selection methods it adds the missing bid selection methods with zero totals`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 172: Same As Api Tender Price By Bid Selection Method But It Always Returns All Bid Selection Methods It Adds The Missing Bid Selection Methods With Zero Totals

Business goal:
Same as /api/tenderPriceByBidSelectionMethod, but it always returns all bidSelectionMethods (it adds the missing bid selection methods with zero totals.

Domain context:
This behavior belongs to the `tender-price-by-type-year-controller` capability area and operates through `POST /api/tenderPriceByAllBidSelectionMethods`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `same as api tender price by bid selection method but it always returns all bid selection methods it adds the missing bid selection methods with zero totals` (`POST /api/tenderPriceByAllBidSelectionMethods`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `same as api tender price by bid selection method but it always returns all bid selection methods it adds the missing bid selection methods with zero totals`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `same as api tender price by bid selection method but it always returns all bid selection methods it adds the missing bid selection methods with zero totals`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `same as api tender price by bid selection method but it always returns all bid selection methods it adds the missing bid selection methods with zero totals`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 173: Returns The Tender Price By Vietnam Type Procurement Method Details By Year The Ocds Type Is Read From Tender Procurement Method Details The Tender Price Is Read From Tender Value Amount

Business goal:
Returns the tender price by Vietnam type (procurementMethodDetails), by year. .

Domain context:
This behavior belongs to the `tender-price-by-type-year-controller` capability area and operates through `GET /api/tenderPriceByBidSelectionMethod`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns the tender price by vietnam type procurement method details by year the ocds type is read from tender procurement method details the tender price is read from tender value amount` (`GET /api/tenderPriceByBidSelectionMethod`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the tender price by vietnam type procurement method details by year the ocds type is read from tender procurement method details the tender price is read from tender value amount`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the tender price by vietnam type procurement method details by year the ocds type is read from tender procurement method details the tender price is read from tender value amount`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the tender price by vietnam type procurement method details by year the ocds type is read from tender procurement method details the tender price is read from tender value amount`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 174: Returns The Tender Price By Vietnam Type Procurement Method Details By Year The Ocds Type Is Read From Tender Procurement Method Details The Tender Price Is Read From Tender Value Amount

Business goal:
Returns the tender price by Vietnam type (procurementMethodDetails), by year. .

Domain context:
This behavior belongs to the `tender-price-by-type-year-controller` capability area and operates through `POST /api/tenderPriceByBidSelectionMethod`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns the tender price by vietnam type procurement method details by year the ocds type is read from tender procurement method details the tender price is read from tender value amount` (`POST /api/tenderPriceByBidSelectionMethod`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the tender price by vietnam type procurement method details by year the ocds type is read from tender procurement method details the tender price is read from tender value amount`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the tender price by vietnam type procurement method details by year the ocds type is read from tender procurement method details the tender price is read from tender value amount`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the tender price by vietnam type procurement method details by year the ocds type is read from tender procurement method details the tender price is read from tender value amount`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 175: Returns The Tender Price By Ocds Type Procurement Method By Year The Ocds Type Is Read From Tender Procurement Method The Tender Price Is Read From Tender Value Amount

Business goal:
Returns the tender price by OCDS type (procurementMethod), by year. .

Domain context:
This behavior belongs to the `tender-price-by-type-year-controller` capability area and operates through `GET /api/tenderPriceByProcurementMethod`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns the tender price by ocds type procurement method by year the ocds type is read from tender procurement method the tender price is read from tender value amount` (`GET /api/tenderPriceByProcurementMethod`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the tender price by ocds type procurement method by year the ocds type is read from tender procurement method the tender price is read from tender value amount`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the tender price by ocds type procurement method by year the ocds type is read from tender procurement method the tender price is read from tender value amount`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the tender price by ocds type procurement method by year the ocds type is read from tender procurement method the tender price is read from tender value amount`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 176: Returns The Tender Price By Ocds Type Procurement Method By Year The Ocds Type Is Read From Tender Procurement Method The Tender Price Is Read From Tender Value Amount

Business goal:
Returns the tender price by OCDS type (procurementMethod), by year. .

Domain context:
This behavior belongs to the `tender-price-by-type-year-controller` capability area and operates through `POST /api/tenderPriceByProcurementMethod`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns the tender price by ocds type procurement method by year the ocds type is read from tender procurement method the tender price is read from tender value amount` (`POST /api/tenderPriceByProcurementMethod`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the tender price by ocds type procurement method by year the ocds type is read from tender procurement method the tender price is read from tender value amount`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the tender price by ocds type procurement method by year the ocds type is read from tender procurement method the tender price is read from tender value amount`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the tender price by ocds type procurement method by year the ocds type is read from tender procurement method the tender price is read from tender value amount`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 177: Returns The Min And Max Of Tender Value Amount

Business goal:
Returns the min and max of tender.value.amount.

Domain context:
This behavior belongs to the `tenders-awards-value-intervals` capability area and operates through `GET /api/tenderValueInterval`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns the min and max of tender value amount` (`GET /api/tenderValueInterval`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the min and max of tender value amount`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the min and max of tender value amount`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the min and max of tender value amount`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 178: Returns The Min And Max Of Tender Value Amount

Business goal:
Returns the min and max of tender.value.amount.

Domain context:
This behavior belongs to the `tenders-awards-value-intervals` capability area and operates through `POST /api/tenderValueInterval`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns the min and max of tender value amount` (`POST /api/tenderValueInterval`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the min and max of tender value amount`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the min and max of tender value amount`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the min and max of tender value amount`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 179: Computes All Available Years From Awards Date Tender Tender Period Start Dateand Planning Bid Plan Project Date Approve

Business goal:
Computes all available years from awards.date, tender.tenderPeriod.startDateand planning.bidPlanProjectDateApprove.

Domain context:
This behavior belongs to the `tenders-awards-years` capability area and operates through `GET /api/tendersAwardsYears`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `computes all available years from awards date tender tender period start dateand planning bid plan project date approve` (`GET /api/tendersAwardsYears`) with no request parameters or body declared.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `computes all available years from awards date tender tender period start dateand planning bid plan project date approve`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `computes all available years from awards date tender tender period start dateand planning bid plan project date approve`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `computes all available years from awards date tender tender period start dateand planning bid plan project date approve`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 180: Computes All Available Years From Awards Date Tender Tender Period Start Dateand Planning Bid Plan Project Date Approve

Business goal:
Computes all available years from awards.date, tender.tenderPeriod.startDateand planning.bidPlanProjectDateApprove.

Domain context:
This behavior belongs to the `tenders-awards-years` capability area and operates through `POST /api/tendersAwardsYears`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `computes all available years from awards date tender tender period start dateand planning bid plan project date approve` (`POST /api/tendersAwardsYears`) with no request parameters or body declared.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- No request values are reused by later steps according to the OpenAPI contract.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `computes all available years from awards date tender tender period start dateand planning bid plan project date approve`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `computes all available years from awards date tender tender period start dateand planning bid plan project date approve`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `computes all available years from awards date tender tender period start dateand planning bid plan project date approve`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 181: This Should Show The Number Of Tenders Per Tender Items Classification The Tender Date Is Taken From Tender Tender Period Start Date

Business goal:
This should show the number of tenders per tender.items.classification.The tender date is taken from tender.tenderPeriod.startDate.

Domain context:
This behavior belongs to the `tenders-by-item-classification` capability area and operates through `GET /api/tendersByItemClassification`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `this should show the number of tenders per tender items classification the tender date is taken from tender tender period start date` (`GET /api/tendersByItemClassification`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `this should show the number of tenders per tender items classification the tender date is taken from tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `this should show the number of tenders per tender items classification the tender date is taken from tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `this should show the number of tenders per tender items classification the tender date is taken from tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 182: This Should Show The Number Of Tenders Per Tender Items Classification The Tender Date Is Taken From Tender Tender Period Start Date

Business goal:
This should show the number of tenders per tender.items.classification.The tender date is taken from tender.tenderPeriod.startDate.

Domain context:
This behavior belongs to the `tenders-by-item-classification` capability area and operates through `POST /api/tendersByItemClassification`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `this should show the number of tenders per tender items classification the tender date is taken from tender tender period start date` (`POST /api/tendersByItemClassification`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `this should show the number of tenders per tender items classification the tender date is taken from tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `this should show the number of tenders per tender items classification the tender date is taken from tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `this should show the number of tenders per tender items classification the tender date is taken from tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 183: Returns The Top Ten Largest Active Awards The Amount Is Taken From The Award Value Field The Returned Data Will Containthe Following Fields Planning Bid No Awards Date Awards Suppliers Name Awards Value Amount Awards Suppliers Name Planning Budget If Any

Business goal:
Returns the top ten largest active awards. .

Domain context:
This behavior belongs to the `top-ten-controller` capability area and operates through `GET /api/topTenLargestAwards`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns the top ten largest active awards the amount is taken from the award value field the returned data will containthe following fields planning bid no awards date awards suppliers name awards value amount awards suppliers name planning budget if any` (`GET /api/topTenLargestAwards`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the top ten largest active awards the amount is taken from the award value field the returned data will containthe following fields planning bid no awards date awards suppliers name awards value amount awards suppliers name planning budget if any`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the top ten largest active awards the amount is taken from the award value field the returned data will containthe following fields planning bid no awards date awards suppliers name awards value amount awards suppliers name planning budget if any`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the top ten largest active awards the amount is taken from the award value field the returned data will containthe following fields planning bid no awards date awards suppliers name awards value amount awards suppliers name planning budget if any`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 184: Returns The Top Ten Largest Active Awards The Amount Is Taken From The Award Value Field The Returned Data Will Containthe Following Fields Planning Bid No Awards Date Awards Suppliers Name Awards Value Amount Awards Suppliers Name Planning Budget If Any

Business goal:
Returns the top ten largest active awards. .

Domain context:
This behavior belongs to the `top-ten-controller` capability area and operates through `POST /api/topTenLargestAwards`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns the top ten largest active awards the amount is taken from the award value field the returned data will containthe following fields planning bid no awards date awards suppliers name awards value amount awards suppliers name planning budget if any` (`POST /api/topTenLargestAwards`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the top ten largest active awards the amount is taken from the award value field the returned data will containthe following fields planning bid no awards date awards suppliers name awards value amount awards suppliers name planning budget if any`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the top ten largest active awards the amount is taken from the award value field the returned data will containthe following fields planning bid no awards date awards suppliers name awards value amount awards suppliers name planning budget if any`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the top ten largest active awards the amount is taken from the award value field the returned data will containthe following fields planning bid no awards date awards suppliers name awards value amount awards suppliers name planning budget if any`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 185: Returns The Top Ten Largest Active Tenders The Amount Is Taken From The Tender Value Amount Field The Returned Data Will Containthe Following Fields Planning Bid No Tender Date Tender Value Amount Tender Tender Period Tender Procuring Entity Name

Business goal:
Returns the top ten largest active tenders. .

Domain context:
This behavior belongs to the `top-ten-controller` capability area and operates through `GET /api/topTenLargestTenders`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `returns the top ten largest active tenders the amount is taken from the tender value amount field the returned data will containthe following fields planning bid no tender date tender value amount tender tender period tender procuring entity name` (`GET /api/topTenLargestTenders`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the top ten largest active tenders the amount is taken from the tender value amount field the returned data will containthe following fields planning bid no tender date tender value amount tender tender period tender procuring entity name`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the top ten largest active tenders the amount is taken from the tender value amount field the returned data will containthe following fields planning bid no tender date tender value amount tender tender period tender procuring entity name`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the top ten largest active tenders the amount is taken from the tender value amount field the returned data will containthe following fields planning bid no tender date tender value amount tender tender period tender procuring entity name`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 186: Returns The Top Ten Largest Active Tenders The Amount Is Taken From The Tender Value Amount Field The Returned Data Will Containthe Following Fields Planning Bid No Tender Date Tender Value Amount Tender Tender Period Tender Procuring Entity Name

Business goal:
Returns the top ten largest active tenders. .

Domain context:
This behavior belongs to the `top-ten-controller` capability area and operates through `POST /api/topTenLargestTenders`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `returns the top ten largest active tenders the amount is taken from the tender value amount field the returned data will containthe following fields planning bid no tender date tender value amount tender tender period tender procuring entity name` (`POST /api/topTenLargestTenders`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `returns the top ten largest active tenders the amount is taken from the tender value amount field the returned data will containthe following fields planning bid no tender date tender value amount tender tender period tender procuring entity name`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the top ten largest active tenders the amount is taken from the tender value amount field the returned data will containthe following fields planning bid no tender date tender value amount tender tender period tender procuring entity name`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `returns the top ten largest active tenders the amount is taken from the tender value amount field the returned data will containthe following fields planning bid no tender date tender value amount tender tender period tender procuring entity name`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 187: This Endpoint Should Return The Following Data For The Top 10 Suppliers By Award Value Returns Supplier Id Total Awarded Amount Of All Awarded Contracts Count Of Awarded Contracts Ids Of The Procuring Entities From Which They Have Received An Award And Their Count All Filters Ally Here The Year Filter Uses The Awards Date Field

Business goal:
This endpoint should return the following data for the Top 10 suppliers (by award value).Returns supplier id, total awarded amount of all awarded contracts, count of awarded contracts,Ids of the procuring entities from which they have received an award, and their count. .

Domain context:
This behavior belongs to the `top-ten-controller` capability area and operates through `GET /api/topTenSuppliers`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `this endpoint should return the following data for the top 10 suppliers by award value returns supplier id total awarded amount of all awarded contracts count of awarded contracts ids of the procuring entities from which they have received an award and their count all filters ally here the year filter uses the awards date field` (`GET /api/topTenSuppliers`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `this endpoint should return the following data for the top 10 suppliers by award value returns supplier id total awarded amount of all awarded contracts count of awarded contracts ids of the procuring entities from which they have received an award and their count all filters ally here the year filter uses the awards date field`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `this endpoint should return the following data for the top 10 suppliers by award value returns supplier id total awarded amount of all awarded contracts count of awarded contracts ids of the procuring entities from which they have received an award and their count all filters ally here the year filter uses the awards date field`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `this endpoint should return the following data for the top 10 suppliers by award value returns supplier id total awarded amount of all awarded contracts count of awarded contracts ids of the procuring entities from which they have received an award and their count all filters ally here the year filter uses the awards date field`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 188: This Endpoint Should Return The Following Data For The Top 10 Suppliers By Award Value Returns Supplier Id Total Awarded Amount Of All Awarded Contracts Count Of Awarded Contracts Ids Of The Procuring Entities From Which They Have Received An Award And Their Count All Filters Ally Here The Year Filter Uses The Awards Date Field

Business goal:
This endpoint should return the following data for the Top 10 suppliers (by award value).Returns supplier id, total awarded amount of all awarded contracts, count of awarded contracts,Ids of the procuring entities from which they have received an award, and their count. .

Domain context:
This behavior belongs to the `top-ten-controller` capability area and operates through `POST /api/topTenSuppliers`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `this endpoint should return the following data for the top 10 suppliers by award value returns supplier id total awarded amount of all awarded contracts count of awarded contracts ids of the procuring entities from which they have received an award and their count all filters ally here the year filter uses the awards date field` (`POST /api/topTenSuppliers`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `this endpoint should return the following data for the top 10 suppliers by award value returns supplier id total awarded amount of all awarded contracts count of awarded contracts ids of the procuring entities from which they have received an award and their count all filters ally here the year filter uses the awards date field`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `this endpoint should return the following data for the top 10 suppliers by award value returns supplier id total awarded amount of all awarded contracts count of awarded contracts ids of the procuring entities from which they have received an award and their count all filters ally here the year filter uses the awards date field`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `this endpoint should return the following data for the top 10 suppliers by award value returns supplier id total awarded amount of all awarded contracts count of awarded contracts ids of the procuring entities from which they have received an award and their count all filters ally here the year filter uses the awards date field`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 189: Total Cancelled Tenders By Year The Tender Amount Is Read From Tender Value The Tender Status Has To Be Cancelled The Year Is Retrieved From Tender Tender Period Start Date

Business goal:
Total Cancelled tenders by year. .

Domain context:
This behavior belongs to the `total-cancelled-tenders-by-year-controller` capability area and operates through `GET /api/totalCancelledTendersByYear`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `total cancelled tenders by year the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date` (`GET /api/totalCancelledTendersByYear`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `total cancelled tenders by year the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `total cancelled tenders by year the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `total cancelled tenders by year the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 190: Total Cancelled Tenders By Year The Tender Amount Is Read From Tender Value The Tender Status Has To Be Cancelled The Year Is Retrieved From Tender Tender Period Start Date

Business goal:
Total Cancelled tenders by year. .

Domain context:
This behavior belongs to the `total-cancelled-tenders-by-year-controller` capability area and operates through `POST /api/totalCancelledTendersByYear`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `total cancelled tenders by year the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date` (`POST /api/totalCancelledTendersByYear`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `total cancelled tenders by year the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `total cancelled tenders by year the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `total cancelled tenders by year the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 191: Total Cancelled Tenders By Year By Cancel Reason The Tender Amount Is Read From Tender Value The Tender Status Has To Be Cancelled The Year Is Retrieved From Tender Tender Period Start Date The Cancellation Reason Is Read From Tender Cancellation Rationale

Business goal:
Total Cancelled tenders by year by cancel reason. .

Domain context:
This behavior belongs to the `total-cancelled-tenders-by-year-controller` capability area and operates through `GET /api/totalCancelledTendersByYearByRationale`.

Starting point:
Any service state that satisfies the declared path parameters. For collection reads, the backing store may be empty.

Required execution workflow:
1. Use function `total cancelled tenders by year by cancel reason the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date the cancellation reason is read from tender cancellation rationale` (`GET /api/totalCancelledTendersByYearByRationale`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
None. The behavior itself is a read or inspection operation.

Existing-state shortcuts:
- Direct database or fixture setup can create records before invoking the read if a non-empty response is needed.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
No state changes are expected. The response returns the requested resource, collection, calculation, or status view.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `total cancelled tenders by year by cancel reason the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date the cancellation reason is read from tender cancellation rationale`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `total cancelled tenders by year by cancel reason the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date the cancellation reason is read from tender cancellation rationale`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `total cancelled tenders by year by cancel reason the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date the cancellation reason is read from tender cancellation rationale`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.

### Behavior 192: Total Cancelled Tenders By Year By Cancel Reason The Tender Amount Is Read From Tender Value The Tender Status Has To Be Cancelled The Year Is Retrieved From Tender Tender Period Start Date The Cancellation Reason Is Read From Tender Cancellation Rationale

Business goal:
Total Cancelled tenders by year by cancel reason. .

Domain context:
This behavior belongs to the `total-cancelled-tenders-by-year-controller` capability area and operates through `POST /api/totalCancelledTendersByYearByRationale`.

Starting point:
The caller has prepared all required path parameters and any required request body or form fields. Parent resources referenced by the path should already exist when the domain requires them.

Required execution workflow:
1. Use function `total cancelled tenders by year by cancel reason the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date the cancellation reason is read from tender cancellation rationale` (`POST /api/totalCancelledTendersByYearByRationale`) with query: year optional, month optional, monthly optional, bidTypeId optional, notBidTypeId optional, procuringEntityId optional, notProcuringEntityId optional, supplierId optional, bidSelectionMethod optional, notBidSelectionMethod optional, contrMethod optional, tenderLoc optional, minTenderValue optional, maxTenderValue optional, minAwardValue optional, maxAwardValue optional, procuringEntityCityId optional, procuringEntityDepartmentId optional, procuringEntityGroupId optional, pageNumber optional, pageSize optional.

Optional verification workflow:
1. Use the corresponding read/list function for the same resource, when available, to verify the persisted or computed state.

Existing-state shortcuts:
- Direct setup can create parent resources referenced by path values before invoking this function.
- Reusing an already-existing target may be accepted, rejected, or treated idempotently depending on implementation validation.

Parameter and value bindings:
- Query values `year`, `month`, `monthly`, `bidTypeId`, `notBidTypeId`, `procuringEntityId`, `notProcuringEntityId`, `supplierId`, `bidSelectionMethod`, `notBidSelectionMethod`, `contrMethod`, `tenderLoc` and others filter, page, or modify the operation result.

Business result:
The service creates, imports, starts, or executes the requested business action. Returned identifiers or links can be reused in later resource-specific calls when present.

Constraints and invariants:
- No required primitive parameters are declared in the OpenAPI contract.
- Authentication, authorization, and cross-resource consistency are implementation concerns unless explicitly documented by the endpoint responses.

Failure and exceptional cases:
- Failing function: `total cancelled tenders by year by cancel reason the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date the cancellation reason is read from tender cancellation rationale`
  - Failure condition: HTTP `401` response.
  - Why it fails: Unauthorized
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `total cancelled tenders by year by cancel reason the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date the cancellation reason is read from tender cancellation rationale`
  - Failure condition: HTTP `403` response.
  - Why it fails: Forbidden
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.
- Failing function: `total cancelled tenders by year by cancel reason the tender amount is read from tender value the tender status has to be cancelled the year is retrieved from tender tender period start date the cancellation reason is read from tender cancellation rationale`
  - Failure condition: HTTP `404` response.
  - Why it fails: Not Found
  - Violated prerequisite or constraint: The request does not satisfy service validation, authorization, existence, or state-transition rules for this endpoint.

Implementation notes:
Success responses: 200 OK; 201 Created. Failure responses: 401 Unauthorized; 403 Forbidden; 404 Not Found.
