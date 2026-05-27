# Domain-Level Behavior Analysis

## Domain Summary

This service exposes a read-only country information catalog. The main domain resource is a country, represented in two API versions. V1 exposes countries with string-based currencies and languages. V2 exposes a richer country representation with currency objects, language objects, flag paths, regional blocs, demonyms, and optional response field projection.

Country state is not created or changed through the API. The implementation loads static in-memory lists from `countriesV1.json` and `countriesV2.json` at application startup. The API therefore supports discovery, lookup, and filtering of preloaded country facts, but not country data maintenance. The OpenAPI server base is `/rest`; endpoint paths below are shown without that base.

## Available Function Inventory

### V1 country catalog

- `list v1 countries` - `GET /v1/all` - lists every preloaded v1 country record.
- `retrieve v1 country by alpha code` - `GET /v1/alpha/{alphacode}` - retrieves one v1 country by alpha-2 or alpha-3 code.
- `retrieve v1 countries by alpha-code list` - `GET /v1/alpha` - retrieves multiple v1 countries by a semicolon-separated `codes` query.
- `search v1 countries by currency` - `GET /v1/currency/{currency}` - finds v1 countries that use a string currency code.
- `search v1 countries by partial name` - `GET /v1/name/{name}` - finds v1 countries whose name or alternative spelling contains a normalized name fragment.
- `search v1 countries by exact name` - `GET /v1/name/{name}` - finds v1 countries whose name or alternative spelling exactly equals a normalized name value when `fullText=true`.
- `search v1 countries by calling code` - `GET /v1/callingcode/{callingcode}` - finds v1 countries with a matching telephone calling code.
- `search v1 countries by capital` - `GET /v1/capital/{capital}` - finds v1 countries whose capital contains a normalized capital fragment.
- `search v1 countries by region` - `GET /v1/region/{region}` - finds v1 countries with an exact case-insensitive region.
- `search v1 countries by subregion` - `GET /v1/subregion/{subregion}` - finds v1 countries with an exact case-insensitive subregion.
- `search v1 countries by language` - `GET /v1/lang/{lang}` - finds v1 countries with a matching stored language string.

### V2 country catalog

- `list v2 countries` - `GET /v2/all` - lists every preloaded v2 country record, optionally projected with `fields`.
- `retrieve v2 country by alpha code` - `GET /v2/alpha/{alphacode}` - retrieves one v2 country by alpha-2 or alpha-3 code, optionally projected with `fields`.
- `retrieve v2 countries by alpha-code list` - `GET /v2/alpha` - retrieves multiple v2 countries by a semicolon-separated `codes` query, optionally projected with `fields`.
- `search v2 countries by currency` - `GET /v2/currency/{currency}` - finds v2 countries whose currency object has a matching `code`.
- `search v2 countries by partial name` - `GET /v2/name/{name}` - finds v2 countries whose name or alternative spelling contains a normalized name fragment.
- `search v2 countries by exact name` - `GET /v2/name/{name}` - finds v2 countries whose name or alternative spelling exactly equals a normalized name value when `fullText=true`.
- `search v2 countries by calling code` - `GET /v2/callingcode/{callingcode}` - finds v2 countries with a matching telephone calling code.
- `search v2 countries by capital` - `GET /v2/capital/{capital}` - finds v2 countries whose capital contains a normalized capital fragment.
- `search v2 countries by region` - `GET /v2/region/{region}` - finds v2 countries with an exact case-insensitive region.
- `search v2 countries by subregion` - `GET /v2/subregion/{subregion}` - finds v2 countries with an exact case-insensitive subregion.
- `search v2 countries by language` - `GET /v2/lang/{lang}` - finds v2 countries by ISO 639-1 when `lang` has length 2, or ISO 639-2 when `lang` has length 3.
- `search v2 countries by demonym` - `GET /v2/demonym/{demonym}` - finds v2 countries by demonym.
- `search v2 countries by regional bloc` - `GET /v2/regionalbloc/{regionalbloc}` - finds v2 countries by regional bloc acronym or alternate acronym.

## Supported Business Behaviors

### Behavior 1: Browse the complete v1 country catalog

Business goal:
Expose the full legacy country catalog to clients that need the v1 schema.

Domain context:
This is the broadest v1 discovery behavior. It gives clients all preloaded country facts without filtering.

Starting point:
No prior API-created state. The v1 static catalog must have been loaded from `countriesV1.json` during service startup.

Required execution workflow:
1. Use function `list v1 countries` (`GET /v1/all`) with no path, query, body, form, or header values to retrieve all v1 country records.

Optional verification workflow:
None.

Existing-state shortcuts:
- No API setup step can be skipped because no API setup step exists.
- Equivalent state can only be changed by editing `countriesV1.json` before startup or by direct in-memory test manipulation. Direct database setup is not applicable because country state is not database-backed.

Parameter and value bindings:
- There are no request values to reuse. Returned country fields such as `alpha2Code`, `alpha3Code`, `currencies`, `languages`, `region`, and `subregion` can be reused by later lookup or search behaviors.

Business result:
The client receives the complete current v1 country collection. No country state is created, changed, or deleted.

Constraints and invariants:
- The implementation applies no request-controlled filtering.
- If JSON loading fails, the list can be empty even though the request still succeeds.
- The source also implements `GET /v1` as an undocumented alias for the same list operation.

Failure and exceptional cases:
- Failing function: `list v1 countries`
- Failure condition: No request-controlled failure branch is visible for the documented endpoint.
- Why it fails: The endpoint simply returns the in-memory list loaded at startup.
- Violated prerequisite or constraint: None from request input.

Implementation notes:
- OpenAPI documents only `GET /v1/all`; the source also exposes `GET /v1`.

### Behavior 2: Retrieve one v1 country by alpha code

Business goal:
Resolve a known country identifier to the full v1 country record.

Domain context:
Alpha-2 and alpha-3 codes are stable identifiers for countries. This behavior lets clients move from a known code to country details.

Starting point:
No prior API-created state. The target country must already exist in the preloaded v1 catalog.

Required execution workflow:
1. Use function `retrieve v1 country by alpha code` (`GET /v1/alpha/{alphacode}`) with `alphacode=US` for an alpha-2 lookup or `alphacode=USA` for an alpha-3 lookup to retrieve the matching v1 country.

Optional verification workflow:
None.

Existing-state shortcuts:
- If the caller already has the country record, this read can be skipped for downstream client logic.
- If the desired country is not in the startup catalog, the only setup alternative is to edit `countriesV1.json` before startup. The `alphacode` value must still equal the country `alpha2Code` or `alpha3Code` case-insensitively.

Parameter and value bindings:
- The same `alphacode` path value is matched against `alpha2Code` when its length is 2 and against `alpha3Code` when its length is 3.
- A code returned by `list v1 countries`, `retrieve v1 countries by alpha-code list`, or any v1 search result can be reused as this `alphacode`.

Business result:
The client receives exactly one v1 country when the code exists. No service state changes.

Constraints and invariants:
- `alphacode` must be exactly 2 or 3 characters.
- Lookup is case-insensitive.
- There is no ownership or tenant scoping.

Failure and exceptional cases:
- Failing function: `retrieve v1 country by alpha code`
- Failure condition: `alphacode` is missing, shorter than 2 characters, or longer than 3 characters.
- Why it fails: The resource method validates length before lookup and returns `400 Bad Request`.
- Violated prerequisite or constraint: `alphacode` must be an alpha-2 or alpha-3 style code.

- Failing function: `retrieve v1 country by alpha code`
- Failure condition: `alphacode` has valid length but matches no preloaded v1 country.
- Why it fails: The service lookup returns `null`, so the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The code must exist in the v1 catalog.

Implementation notes:
- The implementation prioritizes code length over content; it does not validate alphabetic characters.

### Behavior 3: Retrieve selected v1 countries by alpha-code list

Business goal:
Fetch a specific set of legacy country records in one call.

Domain context:
Clients often hold multiple country codes, such as border lists or selected countries, and need their country records together.

Starting point:
No prior API-created state. The requested countries must already exist in the preloaded v1 catalog.

Required execution workflow:
1. Use function `retrieve v1 countries by alpha-code list` (`GET /v1/alpha`) with query `codes=US;CAN;MEX` to retrieve all matching v1 countries.

Optional verification workflow:
None.

Existing-state shortcuts:
- If the client already has all required country records, this read can be skipped.
- To add missing countries, edit `countriesV1.json` before startup. Each `codes` token must equal an `alpha2Code` or `alpha3Code` in that static catalog.

Parameter and value bindings:
- The semicolon-separated `codes` query supplies each alpha identifier.
- Returned countries can be reused by their `alpha2Code` or `alpha3Code` in later single-country retrievals.
- Duplicate resolved country objects are not repeated because the result list checks object containment.

Business result:
The client receives a list of v1 country records for the requested codes. No service state changes.

Constraints and invariants:
- `codes` is required.
- The raw `codes` string must be at least 2 characters.
- If the raw value is longer than 3 characters, it must contain `;`.
- Unmatched tokens are not cleanly rejected by the shared lookup logic.

Failure and exceptional cases:
- Failing function: `retrieve v1 countries by alpha-code list`
- Failure condition: `codes` is missing, empty, shorter than 2 characters, or longer than 3 characters without `;`.
- Why it fails: The resource method validates the raw query before splitting and returns `400 Bad Request`.
- Violated prerequisite or constraint: `codes` must be a single valid-length code or a semicolon-separated list.

- Failing function: `retrieve v1 countries by alpha-code list`
- Failure condition: `codes` contains only separators and no usable tokens.
- Why it fails: The resolved list is empty, so the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: At least one real alpha-code token must be supplied.

- Failing function: `retrieve v1 countries by alpha-code list`
- Failure condition: One syntactically accepted token does not match any country.
- Why it fails: The implementation may add `null` to the result list and return a successful response containing `null` rather than a domain error.
- Violated prerequisite or constraint: Every requested code should identify a country, but this is not fully enforced.

Implementation notes:
- This is an implementation/API semantic gap: partial unresolved lists are not reported consistently.

### Behavior 4: Search v1 countries by currency

Business goal:
Find countries that use a specified currency code in the legacy schema.

Domain context:
Currency search supports economic, travel, and localization use cases.

Starting point:
No prior API-created state. At least one preloaded v1 country must contain the currency string.

Required execution workflow:
1. Use function `search v1 countries by currency` (`GET /v1/currency/{currency}`) with `currency=USD` to retrieve v1 countries whose stored currency string matches.

Optional verification workflow:
None.

Existing-state shortcuts:
- If matching country records are already available to the client, the search can be skipped.
- Static setup requires editing `countriesV1.json` so a country's `currencies` list contains the same three-character code.

Parameter and value bindings:
- The `currency` path value is compared case-insensitively with each string in each v1 country's `currencies` list.
- The same currency code can be captured from a prior v1 country response and reused in this search.

Business result:
The client receives all v1 countries using the requested currency code. No service state changes.

Constraints and invariants:
- `currency` must be exactly 3 characters.
- The implementation does not verify that the value is a real ISO 4217 code.

Failure and exceptional cases:
- Failing function: `search v1 countries by currency`
- Failure condition: `currency` is missing or not exactly 3 characters.
- Why it fails: The endpoint validates length and returns `400 Bad Request`.
- Violated prerequisite or constraint: Currency search expects a three-character code.

- Failing function: `search v1 countries by currency`
- Failure condition: `currency` has valid length but is absent from all v1 countries.
- Why it fails: The result list is empty, so the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: At least one preloaded country must use the currency.

Implementation notes:
- V1 currency data is modeled as strings; v2 uses currency objects.

### Behavior 5: Search v1 countries by name

Business goal:
Find legacy country records by country name or alternative spelling.

Domain context:
Name lookup is the most human-facing discovery behavior. It supports both broad partial matching and exact normalized matching.

Starting point:
No prior API-created state. At least one preloaded v1 country must match the supplied name according to the selected mode.

Required execution workflow:
1. Use function `search v1 countries by partial name` (`GET /v1/name/{name}`) with `name=united` and `fullText=false` omitted or set to `false` to search by normalized substring.
2. Alternatively, use function `search v1 countries by exact name` (`GET /v1/name/{name}`) with `name=United States of America` and query `fullText=true` to search by exact normalized name or alternative spelling.

Optional verification workflow:
1. Use function `retrieve v1 country by alpha code` (`GET /v1/alpha/{alphacode}`) with `alphacode` captured from one returned country's `alpha2Code` or `alpha3Code` to inspect a single selected result.

Existing-state shortcuts:
- If a client already has the target country code, it can skip name search and use `retrieve v1 country by alpha code`.
- To make a name searchable, edit `countriesV1.json` before startup so `name` or an `altSpellings` value contains or equals the requested value under normalization.

Parameter and value bindings:
- The `name` path value is lowercased and accent-normalized.
- `fullText=false` or omitted means substring matching; `fullText=true` means exact matching.
- A returned `alpha2Code` or `alpha3Code` can be bound into a later `alphacode` path value.

Business result:
The client receives matching v1 countries ordered with primary `name` matches before alternative spelling matches. No service state changes.

Constraints and invariants:
- Matching is case-insensitive and accent-insensitive.
- Alternative spellings are considered only after primary names and are de-duplicated against already matched countries.
- No minimum name length is enforced by the implementation.

Failure and exceptional cases:
- Failing function: `search v1 countries by partial name`
- Failure condition: No country name or alternative spelling contains the normalized `name`.
- Why it fails: The substring search returns an empty list and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The search fragment must appear in a searchable v1 country name.

- Failing function: `search v1 countries by exact name`
- Failure condition: No country name or alternative spelling exactly equals the normalized `name`.
- Why it fails: The exact search returns an empty list and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The exact normalized name must exist.

- Failing function: `retrieve v1 country by alpha code`
- Failure condition: A client binds an invalid or missing code from outside the search result.
- Why it fails: The alpha retrieval validates length and existence.
- Violated prerequisite or constraint: Verification must reuse a real returned alpha code.

Implementation notes:
- The OpenAPI exposes one endpoint with `fullText`; `full-behavior.md` correctly separates the partial and exact name functions because they have different domain semantics.

### Behavior 6: Search v1 countries by telephone calling code

Business goal:
Identify countries associated with a telephone calling code.

Domain context:
Calling-code lookup supports telephony, contact normalization, and country inference from phone-number metadata.

Starting point:
No prior API-created state. At least one v1 country must contain the calling code.

Required execution workflow:
1. Use function `search v1 countries by calling code` (`GET /v1/callingcode/{callingcode}`) with `callingcode=1` to retrieve v1 countries whose calling-code list contains that exact string.

Optional verification workflow:
None.

Existing-state shortcuts:
- If the country list has already been retrieved, clients can filter locally and skip the API search.
- Static setup requires editing `countriesV1.json` so a country `callingCodes` list contains the exact `callingcode` string.

Parameter and value bindings:
- The `callingcode` path value is compared exactly with stored calling-code strings.
- Values captured from a previous country response's `callingCodes` list can be reused directly.

Business result:
The client receives all v1 countries sharing the requested calling code. No service state changes.

Constraints and invariants:
- No length, numeric-only, or plus-sign validation is enforced.
- Matching is exact and case-sensitive, though calling codes are normally numeric strings.

Failure and exceptional cases:
- Failing function: `search v1 countries by calling code`
- Failure condition: No preloaded v1 country contains the exact `callingcode`.
- Why it fails: The result list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The calling code must exist in the catalog.

Implementation notes:
- Shared calling codes can return multiple countries.

### Behavior 7: Search v1 countries by capital

Business goal:
Find countries by capital city name in the legacy schema.

Domain context:
Capital lookup supports geographic discovery when users know a capital but not the country.

Starting point:
No prior API-created state. At least one v1 country must have a matching capital.

Required execution workflow:
1. Use function `search v1 countries by capital` (`GET /v1/capital/{capital}`) with `capital=washington` to retrieve v1 countries whose normalized capital contains the value.

Optional verification workflow:
None.

Existing-state shortcuts:
- If full country records are already loaded client-side, local filtering by capital can replace the API search.
- Static setup requires editing `countriesV1.json` so a country `capital` contains the normalized search value.

Parameter and value bindings:
- The `capital` path value is lowercased and accent-normalized before substring matching.
- A returned country's `capital` value can be reused as a later `capital` path value.

Business result:
The client receives all v1 countries whose capital contains the search fragment. No service state changes.

Constraints and invariants:
- The search is substring-based, case-insensitive, and accent-insensitive.
- No exact-capital mode is exposed.

Failure and exceptional cases:
- Failing function: `search v1 countries by capital`
- Failure condition: No normalized v1 capital contains `capital`.
- Why it fails: The result list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The capital fragment must appear in a stored capital.

Implementation notes:
- If a country record had a null `capital`, the implementation would be vulnerable to a null dereference, but the bundled data is expected to supply capital strings.

### Behavior 8: Search v1 countries by region

Business goal:
Group legacy countries by broad geographic region.

Domain context:
Region search supports continent-scale browsing and reporting.

Starting point:
No prior API-created state. At least one v1 country must have the region value.

Required execution workflow:
1. Use function `search v1 countries by region` (`GET /v1/region/{region}`) with `region=Americas` to retrieve v1 countries whose `region` matches case-insensitively.

Optional verification workflow:
None.

Existing-state shortcuts:
- If all countries are already available from `list v1 countries`, clients can filter locally by `region`.
- Static setup requires editing `countriesV1.json` so a country `region` equals the requested value case-insensitively.

Parameter and value bindings:
- The `region` path value is compared case-insensitively with each country `region`.
- A `region` value returned from any v1 country response can be reused as this path value.

Business result:
The client receives all v1 countries in the requested region. No service state changes.

Constraints and invariants:
- Matching is exact after case normalization, not substring-based.
- Region names are not validated against a controlled enum.

Failure and exceptional cases:
- Failing function: `search v1 countries by region`
- Failure condition: No country has the requested region.
- Why it fails: The result list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The region must exist in the preloaded catalog.

Implementation notes:
- Region values are data-driven rather than schema-constrained.

### Behavior 9: Search v1 countries by subregion

Business goal:
Group legacy countries by finer geographic subregion.

Domain context:
Subregion search supports more specific geographic segmentation than broad region search.

Starting point:
No prior API-created state. At least one v1 country must have the subregion value.

Required execution workflow:
1. Use function `search v1 countries by subregion` (`GET /v1/subregion/{subregion}`) with `subregion=Northern America` to retrieve v1 countries whose `subregion` matches case-insensitively.

Optional verification workflow:
None.

Existing-state shortcuts:
- If all countries are already available from `list v1 countries`, clients can filter locally by `subregion`.
- Static setup requires editing `countriesV1.json` so a country `subregion` equals the requested value case-insensitively.

Parameter and value bindings:
- The `subregion` path value is compared case-insensitively with each country `subregion`.
- A returned country's `subregion` can be reused as this path value.

Business result:
The client receives all v1 countries in the requested subregion. No service state changes.

Constraints and invariants:
- Matching is exact after case normalization.
- Subregion values are not validated against a controlled enum.

Failure and exceptional cases:
- Failing function: `search v1 countries by subregion`
- Failure condition: No country has the requested subregion.
- Why it fails: The result list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The subregion must exist in the v1 catalog.

Implementation notes:
- The v1 resource log message says "Getting by region" for subregion requests, but behavior is still subregion filtering.

### Behavior 10: Search v1 countries by language

Business goal:
Find legacy countries that use a language value.

Domain context:
Language search supports localization, content targeting, and demographic discovery.

Starting point:
No prior API-created state. At least one v1 country must contain the language string.

Required execution workflow:
1. Use function `search v1 countries by language` (`GET /v1/lang/{lang}`) with `lang=en` to retrieve v1 countries whose stored language string equals the value case-insensitively.

Optional verification workflow:
None.

Existing-state shortcuts:
- If full country records are already available client-side, local filtering by `languages` can replace the API search.
- Static setup requires editing `countriesV1.json` so a country `languages` list contains the requested string.

Parameter and value bindings:
- The `lang` path value is compared case-insensitively with each string in each v1 country `languages` list.
- A language value captured from a prior v1 response can be reused directly.

Business result:
The client receives all v1 countries matching the language string. No service state changes.

Constraints and invariants:
- V1 does not distinguish ISO 639-1 from ISO 639-2 structurally; both are just strings.
- No language-code length validation is enforced.

Failure and exceptional cases:
- Failing function: `search v1 countries by language`
- Failure condition: No country language string equals `lang`.
- Why it fails: The result list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The language value must exist in the v1 catalog.

Implementation notes:
- V2 language search is more structured than v1 because v2 language entries have `iso639_1` and `iso639_2` fields.

### Behavior 11: Browse the complete or projected v2 country catalog

Business goal:
Expose the modern country catalog, optionally limited to fields a client needs.

Domain context:
V2 is the richer catalog version and supports lightweight directory-style responses through field projection.

Starting point:
No prior API-created state. The v2 static catalog must have been loaded from `countriesV2.json` during service startup.

Required execution workflow:
1. Use function `list v2 countries` (`GET /v2/all`) with query `fields=name;alpha2Code;alpha3Code;region` to retrieve a projected v2 catalog, or omit `fields` to retrieve full country records.

Optional verification workflow:
None.

Existing-state shortcuts:
- If the client already has a fresh local copy of the v2 catalog, this read can be skipped.
- Static setup requires editing `countriesV2.json` before startup. Direct database setup is not applicable.

Parameter and value bindings:
- The optional `fields` query is a semicolon-separated inclusion list of exact v2 field names.
- Returned `alpha2Code` or `alpha3Code` values can be reused by v2 alpha retrieval and code-list behaviors.

Business result:
The client receives all v2 countries, either fully populated or with only requested fields retained. No service state changes.

Constraints and invariants:
- Unknown field names are not rejected.
- Field names are exact and not trimmed.
- If `fields` contains only unknown names, each country can project to an empty JSON object.
- The source also implements `GET /v2` as an undocumented alias for the same list operation.

Failure and exceptional cases:
- Failing function: `list v2 countries`
- Failure condition: No request-controlled failure branch is visible for the documented endpoint.
- Why it fails: The endpoint returns the loaded in-memory list and projection removes unrequested fields.
- Violated prerequisite or constraint: None from request input.

Implementation notes:
- OpenAPI documents `fields` as a string but does not enumerate valid field names. The valid implementation names include `name`, `topLevelDomain`, `alpha2Code`, `alpha3Code`, `callingCodes`, `capital`, `altSpellings`, `region`, `subregion`, `translations`, `population`, `latlng`, `demonym`, `area`, `gini`, `timezones`, `borders`, `nativeName`, `numericCode`, `currencies`, `languages`, `flag`, `regionalBlocs`, and `cioc`.

### Behavior 12: Retrieve one v2 country by alpha code with optional projection

Business goal:
Resolve a known country identifier to the modern v2 country record, optionally with only selected fields.

Domain context:
This is the main precise lookup behavior for v2 clients.

Starting point:
No prior API-created state. The target country must already exist in the preloaded v2 catalog.

Required execution workflow:
1. Use function `retrieve v2 country by alpha code` (`GET /v2/alpha/{alphacode}`) with `alphacode=US` or `alphacode=USA` and optional query `fields=name;capital;currencies;languages` to retrieve the matching v2 country.

Optional verification workflow:
None.

Existing-state shortcuts:
- If a prior v2 list or search response already contains all needed fields for the country, this lookup can be skipped.
- Static setup requires editing `countriesV2.json` before startup so the target `alpha2Code` or `alpha3Code` exists.

Parameter and value bindings:
- The `alphacode` path value is matched against `alpha2Code` for length 2 and `alpha3Code` for length 3.
- The optional `fields` query controls which response fields remain.
- A code returned by any v2 list or search can be reused as `alphacode`.

Business result:
The client receives one v2 country record, full or projected. No service state changes.

Constraints and invariants:
- `alphacode` must be exactly 2 or 3 characters.
- Lookup is case-insensitive.
- Projection does not validate unknown fields.

Failure and exceptional cases:
- Failing function: `retrieve v2 country by alpha code`
- Failure condition: `alphacode` is missing, shorter than 2 characters, or longer than 3 characters.
- Why it fails: The endpoint validates length before lookup and returns `400 Bad Request`.
- Violated prerequisite or constraint: The code must be alpha-2 or alpha-3 length.

- Failing function: `retrieve v2 country by alpha code`
- Failure condition: `alphacode` has valid length but matches no v2 country.
- Why it fails: The service lookup returns `null`, so the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The country code must exist in the v2 catalog.

Implementation notes:
- Field projection serializes the country to JSON, removes unrequested fields, and returns the JSON string.

### Behavior 13: Retrieve selected v2 countries by alpha-code list with optional projection

Business goal:
Fetch a selected set of modern country records in one call.

Domain context:
This supports client workflows that already know multiple country codes, such as rendering border countries or selected regions.

Starting point:
No prior API-created state. The requested countries must already exist in the preloaded v2 catalog.

Required execution workflow:
1. Use function `retrieve v2 countries by alpha-code list` (`GET /v2/alpha`) with query `codes=USA;CAN;MEX` and optional query `fields=name;alpha3Code;borders` to retrieve the selected v2 countries.

Optional verification workflow:
None.

Existing-state shortcuts:
- If the selected records are already available from an earlier v2 list/search response, this read can be skipped.
- Static setup requires editing `countriesV2.json` before startup so each requested code maps to a country.

Parameter and value bindings:
- The `codes` query supplies semicolon-separated alpha identifiers.
- The optional `fields` query applies the same projection to every returned country.
- Codes captured from any v2 country response can be reused in `codes`.

Business result:
The client receives the requested v2 countries, full or projected. No service state changes.

Constraints and invariants:
- `codes` is required, must be at least 2 characters, and must contain `;` when longer than 3 characters.
- Unknown requested codes are not handled consistently.

Failure and exceptional cases:
- Failing function: `retrieve v2 countries by alpha-code list`
- Failure condition: `codes` is missing, empty, shorter than 2 characters, or longer than 3 characters without `;`.
- Why it fails: The endpoint validates the raw query and returns `400 Bad Request`.
- Violated prerequisite or constraint: `codes` must be a single accepted code or a semicolon-separated list.

- Failing function: `retrieve v2 countries by alpha-code list`
- Failure condition: `codes` contains only separators and no usable tokens.
- Why it fails: The resolved list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: At least one real alpha-code token must be supplied.

- Failing function: `retrieve v2 countries by alpha-code list`
- Failure condition: A token does not match any country and `fields` is omitted.
- Why it fails: The shared lookup may include `null` in a non-empty result and return success rather than `404`.
- Violated prerequisite or constraint: Every requested code should map to a country, but this is not fully enforced.

- Failing function: `retrieve v2 countries by alpha-code list`
- Failure condition: A token does not match any country and `fields` is present.
- Why it fails: Projection casts every list item to a JSON object; a `null` item causes an exception and the endpoint returns `500 Internal Server Error`.
- Violated prerequisite or constraint: Projected list lookup requires all tokens to resolve to country objects.

Implementation notes:
- This behavior has the sharpest lookup/projection inconsistency in the service.

### Behavior 14: Search v2 countries by currency

Business goal:
Find modern country records that use a specified currency code.

Domain context:
V2 currency entries are structured objects, so this behavior searches the currency `code` field rather than a raw string list.

Starting point:
No prior API-created state. At least one preloaded v2 country must have a currency object with the requested code.

Required execution workflow:
1. Use function `search v2 countries by currency` (`GET /v2/currency/{currency}`) with `currency=EUR` and optional query `fields=name;currencies` to retrieve matching v2 countries.

Optional verification workflow:
None.

Existing-state shortcuts:
- If a client already has v2 country records and their `currencies` fields, it can filter locally.
- Static setup requires editing `countriesV2.json` so a country's `currencies[].code` equals the requested code case-insensitively.

Parameter and value bindings:
- The `currency` path value is compared case-insensitively with `currencies[].code`.
- The same currency code can be captured from a v2 country response and reused.
- Optional `fields` limits returned fields.

Business result:
The client receives all v2 countries using the requested currency. No service state changes.

Constraints and invariants:
- `currency` must be exactly 3 characters.
- Null currency codes are skipped.
- The value is not validated against an ISO 4217 registry.

Failure and exceptional cases:
- Failing function: `search v2 countries by currency`
- Failure condition: `currency` is missing or not exactly 3 characters.
- Why it fails: The endpoint validates length and returns `400 Bad Request`.
- Violated prerequisite or constraint: Currency search expects a three-character code.

- Failing function: `search v2 countries by currency`
- Failure condition: No v2 currency object has the requested code.
- Why it fails: The result list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The currency code must exist in the v2 catalog.

Implementation notes:
- V2 currency matching is better modeled than v1 but still lacks code-set validation.

### Behavior 15: Search v2 countries by name

Business goal:
Find modern country records by country name or alternative spelling.

Domain context:
This is the primary human-facing search path for v2 clients.

Starting point:
No prior API-created state. At least one preloaded v2 country must match the supplied name according to the selected mode.

Required execution workflow:
1. Use function `search v2 countries by partial name` (`GET /v2/name/{name}`) with `name=united`, `fullText=false` omitted or set to `false`, and optional query `fields=name;alpha3Code` to search by normalized substring.
2. Alternatively, use function `search v2 countries by exact name` (`GET /v2/name/{name}`) with `name=United States of America`, query `fullText=true`, and optional query `fields=name;alpha3Code` to search by exact normalized name or alternative spelling.

Optional verification workflow:
1. Use function `retrieve v2 country by alpha code` (`GET /v2/alpha/{alphacode}`) with `alphacode` captured from a returned `alpha2Code` or `alpha3Code` and any needed `fields` query to inspect a single selected result.

Existing-state shortcuts:
- If a client already has the target alpha code, it can skip name search and call `retrieve v2 country by alpha code`.
- Static setup requires editing `countriesV2.json` before startup so `name` or `altSpellings` contains or equals the requested value after normalization.

Parameter and value bindings:
- The `name` path value is lowercased and accent-normalized.
- `fullText=false` or omitted means substring matching; `fullText=true` means exact matching.
- Optional `fields` applies to the search result, and a returned alpha code can be reused as `alphacode`.

Business result:
The client receives matching v2 countries, optionally projected. No service state changes.

Constraints and invariants:
- Matching is case-insensitive and accent-insensitive.
- Primary name matches are returned before alternative spelling matches.
- No minimum search length is enforced.

Failure and exceptional cases:
- Failing function: `search v2 countries by partial name`
- Failure condition: No country name or alternative spelling contains the normalized `name`.
- Why it fails: The substring search result is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The search fragment must appear in a searchable v2 country name.

- Failing function: `search v2 countries by exact name`
- Failure condition: No country name or alternative spelling exactly equals the normalized `name`.
- Why it fails: The exact search result is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The exact normalized name must exist.

- Failing function: `retrieve v2 country by alpha code`
- Failure condition: The verification `alphacode` is invalid or does not come from a returned result.
- Why it fails: Alpha retrieval validates length and existence.
- Violated prerequisite or constraint: Verification must reuse a real returned alpha code.

Implementation notes:
- Projection works after search by removing unrequested fields from the serialized countries.

### Behavior 16: Search v2 countries by telephone calling code

Business goal:
Identify modern country records associated with a telephone calling code.

Domain context:
Calling-code lookup supports phone-number-driven country inference.

Starting point:
No prior API-created state. At least one v2 country must contain the calling code.

Required execution workflow:
1. Use function `search v2 countries by calling code` (`GET /v2/callingcode/{callingcode}`) with `callingcode=1` and optional query `fields=name;callingCodes` to retrieve matching v2 countries.

Optional verification workflow:
None.

Existing-state shortcuts:
- If v2 country records are already present client-side, local filtering by `callingCodes` can replace the API call.
- Static setup requires editing `countriesV2.json` so a country's `callingCodes` list contains the exact requested value.

Parameter and value bindings:
- The `callingcode` path value is compared exactly with stored calling-code strings.
- Optional `fields` limits returned fields.
- Values captured from a prior country `callingCodes` list can be reused directly.

Business result:
The client receives all v2 countries sharing the calling code. No service state changes.

Constraints and invariants:
- No numeric-only or plus-sign validation is enforced.
- Matching is exact.

Failure and exceptional cases:
- Failing function: `search v2 countries by calling code`
- Failure condition: No preloaded v2 country contains the exact `callingcode`.
- Why it fails: The result list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The calling code must exist in the v2 catalog.

Implementation notes:
- Shared calling codes can return multiple countries.

### Behavior 17: Search v2 countries by capital

Business goal:
Find modern country records by capital city name.

Domain context:
This supports geographic discovery when a user knows a capital but not the country.

Starting point:
No prior API-created state. At least one v2 country must have a matching capital.

Required execution workflow:
1. Use function `search v2 countries by capital` (`GET /v2/capital/{capital}`) with `capital=paris` and optional query `fields=name;capital` to retrieve v2 countries whose normalized capital contains the value.

Optional verification workflow:
None.

Existing-state shortcuts:
- If all v2 countries are already available, the client can filter locally by `capital`.
- Static setup requires editing `countriesV2.json` so a country `capital` contains the normalized search value.

Parameter and value bindings:
- The `capital` path value is lowercased and accent-normalized before substring matching.
- Optional `fields` limits returned fields.

Business result:
The client receives all v2 countries whose capital contains the requested fragment. No service state changes.

Constraints and invariants:
- Matching is substring-based, case-insensitive, and accent-insensitive.
- No exact-capital search mode is exposed.

Failure and exceptional cases:
- Failing function: `search v2 countries by capital`
- Failure condition: No normalized v2 capital contains `capital`.
- Why it fails: The result list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The capital fragment must appear in a stored capital.

Implementation notes:
- Null capital values would risk null dereference in the shared search code.

### Behavior 18: Search v2 countries by region

Business goal:
Group modern country records by broad geographic region.

Domain context:
This supports continent-scale browsing and reporting using the v2 schema.

Starting point:
No prior API-created state. At least one v2 country must have the region value.

Required execution workflow:
1. Use function `search v2 countries by region` (`GET /v2/region/{region}`) with `region=Europe` and optional query `fields=name;region` to retrieve v2 countries in that region.

Optional verification workflow:
None.

Existing-state shortcuts:
- If all v2 countries are already available, the client can filter locally by `region`.
- Static setup requires editing `countriesV2.json` so a country `region` equals the requested value case-insensitively.

Parameter and value bindings:
- The `region` path value is compared case-insensitively with `country.region`.
- Optional `fields` limits returned fields.

Business result:
The client receives all v2 countries in the requested region. No service state changes.

Constraints and invariants:
- Matching is exact after case normalization.
- Region values are not schema-enumerated.

Failure and exceptional cases:
- Failing function: `search v2 countries by region`
- Failure condition: No v2 country has the requested region.
- Why it fails: The result list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The region must exist in the v2 catalog.

Implementation notes:
- This behavior is structurally identical to v1 region search but can use field projection.

### Behavior 19: Search v2 countries by subregion

Business goal:
Group modern country records by finer geographic subregion.

Domain context:
This supports more precise geographic segmentation in the v2 schema.

Starting point:
No prior API-created state. At least one v2 country must have the subregion value.

Required execution workflow:
1. Use function `search v2 countries by subregion` (`GET /v2/subregion/{subregion}`) with `subregion=Western Europe` and optional query `fields=name;subregion` to retrieve v2 countries in that subregion.

Optional verification workflow:
None.

Existing-state shortcuts:
- If all v2 countries are already available, the client can filter locally by `subregion`.
- Static setup requires editing `countriesV2.json` so a country `subregion` equals the requested value case-insensitively.

Parameter and value bindings:
- The `subregion` path value is compared case-insensitively with `country.subregion`.
- Optional `fields` limits returned fields.

Business result:
The client receives all v2 countries in the requested subregion. No service state changes.

Constraints and invariants:
- Matching is exact after case normalization.
- Subregion values are not schema-enumerated.

Failure and exceptional cases:
- Failing function: `search v2 countries by subregion`
- Failure condition: No v2 country has the requested subregion.
- Why it fails: The result list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The subregion must exist in the v2 catalog.

Implementation notes:
- The v2 resource method is named `getBySubRegion`, while the shared service method uses subregion matching.

### Behavior 20: Search v2 countries by language ISO code

Business goal:
Find countries that use a language identified by ISO code.

Domain context:
V2 language data is structured, allowing different matching fields for two-character and three-character codes.

Starting point:
No prior API-created state. At least one v2 country must have a matching language object.

Required execution workflow:
1. Use function `search v2 countries by language` (`GET /v2/lang/{lang}`) with `lang=en` for ISO 639-1 or `lang=eng` for ISO 639-2 and optional query `fields=name;languages` to retrieve matching v2 countries.

Optional verification workflow:
None.

Existing-state shortcuts:
- If v2 country language objects are already present client-side, local filtering can replace the API search.
- Static setup requires editing `countriesV2.json` so a language object has `iso639_1` equal to a two-character `lang` or `iso639_2` equal to a three-character `lang`.

Parameter and value bindings:
- A two-character `lang` is compared with `languages[].iso639_1`.
- A three-character `lang` is compared with `languages[].iso639_2`.
- Optional `fields` limits returned fields.

Business result:
The client receives all v2 countries that use the requested language code. No service state changes.

Constraints and invariants:
- Lengths other than 2 or 3 are not rejected as bad requests; they simply produce no matches.
- Comparisons use lowercased input, so codes are effectively case-insensitive when data is lowercase.

Failure and exceptional cases:
- Failing function: `search v2 countries by language`
- Failure condition: No language object matches `lang`, including when `lang` has unsupported length.
- Why it fails: The service returns an empty result and the endpoint returns `404 Not Found`; invalid length is not distinguished from a valid but absent language.
- Violated prerequisite or constraint: `lang` must match a stored ISO 639-1 or ISO 639-2 value.

Implementation notes:
- This differs from v1 language search, which compares raw strings without ISO field semantics.

### Behavior 21: Search v2 countries by demonym

Business goal:
Find countries associated with a demonym such as a nationality descriptor.

Domain context:
Demonym lookup supports people-to-country association and nationality-related discovery.

Starting point:
No prior API-created state. At least one v2 country must have the requested demonym.

Required execution workflow:
1. Use function `search v2 countries by demonym` (`GET /v2/demonym/{demonym}`) with `demonym=French` and optional query `fields=name;demonym` to retrieve matching v2 countries.

Optional verification workflow:
None.

Existing-state shortcuts:
- If all v2 countries and demonym fields are already available, the client can filter locally.
- Static setup requires editing `countriesV2.json` so a country `demonym` equals the normalized requested value.

Parameter and value bindings:
- The `demonym` path value is lowercased and accent-normalized.
- The comparison is against `country.demonym.toLowerCase()`.
- Optional `fields` limits returned fields.

Business result:
The client receives all v2 countries matching the demonym. No service state changes.

Constraints and invariants:
- Matching is exact after lowercasing and input normalization.
- There is no v1 demonym search endpoint.

Failure and exceptional cases:
- Failing function: `search v2 countries by demonym`
- Failure condition: No v2 country has a matching demonym.
- Why it fails: The result list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The demonym must exist in the v2 catalog.

Implementation notes:
- Only the input is explicitly accent-normalized in the comparison; bundled demonym data is expected to be compatible.

### Behavior 22: Search v2 countries by regional bloc acronym

Business goal:
Find countries that participate in a regional bloc identified by acronym.

Domain context:
Regional bloc lookup supports political and economic grouping workflows such as EU, EFTA, or similar bloc membership discovery.

Starting point:
No prior API-created state. At least one v2 country must have a matching regional bloc.

Required execution workflow:
1. Use function `search v2 countries by regional bloc` (`GET /v2/regionalbloc/{regionalbloc}`) with `regionalbloc=EU` and optional query `fields=name;regionalBlocs` to retrieve matching v2 countries.

Optional verification workflow:
None.

Existing-state shortcuts:
- If v2 records with `regionalBlocs` are already available, the client can filter locally.
- Static setup requires editing `countriesV2.json` so a country's `regionalBlocs[].acronym` equals the requested value case-insensitively or `regionalBlocs[].otherAcronyms` contains the uppercased requested value.

Parameter and value bindings:
- The `regionalbloc` path value is uppercased for comparison.
- The value must match a bloc `acronym` or an item in `otherAcronyms`, not the bloc name.
- Optional `fields` limits returned fields.

Business result:
The client receives all v2 countries whose regional bloc acronym or alternate acronym matches. No service state changes.

Constraints and invariants:
- Search is acronym-based, not full-name-based.
- No v1 regional bloc search endpoint exists.

Failure and exceptional cases:
- Failing function: `search v2 countries by regional bloc`
- Failure condition: No bloc acronym or alternate acronym matches `regionalbloc`.
- Why it fails: The result list is empty and the endpoint returns `404 Not Found`.
- Violated prerequisite or constraint: The acronym must exist in at least one country's regional bloc list.

Implementation notes:
- Null `regionalBlocs`, null `acronym`, or null `otherAcronyms` values would risk null dereference, but bundled data is expected to supply compatible structures.

### Behavior 23: Build a compact v2 country directory

Business goal:
Produce a lightweight directory of countries for selectors, maps, or search-result cards.

Domain context:
Clients often need only display fields rather than full country payloads. V2 field projection supports this directly.

Starting point:
No prior API-created state. The v2 catalog must be loaded.

Required execution workflow:
1. Use function `list v2 countries` (`GET /v2/all`) with query `fields=name;alpha2Code;alpha3Code;flag;region;capital` to retrieve a compact country directory.

Optional verification workflow:
1. Use function `retrieve v2 country by alpha code` (`GET /v2/alpha/{alphacode}`) with `alphacode` captured from a directory entry and query `fields=name;alpha2Code;alpha3Code;flag;region;capital` to inspect one directory entry.

Existing-state shortcuts:
- If an equivalent projected directory has already been cached by the client, the list call can be skipped.
- Static catalog changes still require editing `countriesV2.json` before startup. The verification step requires reusing a real returned `alpha2Code` or `alpha3Code`.

Parameter and value bindings:
- The same `fields` inclusion list should be reused between the directory list and single-country verification if the client wants the same shape.
- The `alpha2Code` or `alpha3Code` returned from step 1 is bound to the later `alphacode` path value.

Business result:
The client has a compact read-only directory of v2 countries and can inspect one entry with the same projection. No service state changes.

Constraints and invariants:
- Projection does not validate field names, so a misspelled field silently disappears.
- The behavior depends on v2; v1 has no field projection support.

Failure and exceptional cases:
- Failing function: `retrieve v2 country by alpha code`
- Failure condition: The verification `alphacode` is not copied from a real directory result or has invalid length.
- Why it fails: The alpha retrieval returns `400` for invalid length or `404` for absent valid-length codes.
- Violated prerequisite or constraint: Verification must bind a real returned country code.

- Failing function: `list v2 countries`
- Failure condition: The `fields` query contains only unknown field names.
- Why it fails: This does not fail at HTTP level; the implementation returns empty JSON objects, which violates the business expectation of a useful directory.
- Violated prerequisite or constraint: Requested fields must be exact v2 implementation field names.

Implementation notes:
- This is a composite client workflow, but its state remains read-only.

### Behavior 24: Resolve a country profile from a user-supplied name

Business goal:
Turn a human-entered country name into a canonical country profile.

Domain context:
User interfaces commonly start with a name search and then need a stable code-based profile lookup after the user selects a result.

Starting point:
No prior API-created state. The target country must exist in the selected API version's static catalog.

Required execution workflow:
1. Use function `search v2 countries by partial name` (`GET /v2/name/{name}`) with `name=france`, `fullText=false` omitted or set to `false`, and query `fields=name;alpha2Code;alpha3Code` to find candidate countries.
2. Use function `retrieve v2 country by alpha code` (`GET /v2/alpha/{alphacode}`) with `alphacode` set to the selected candidate's returned `alpha2Code` or `alpha3Code` and optional query `fields=name;capital;currencies;languages;region;subregion;borders` to retrieve the canonical profile.
3. Alternative v1 path: use function `search v1 countries by partial name` (`GET /v1/name/{name}`) with `name=france`, then use function `retrieve v1 country by alpha code` (`GET /v1/alpha/{alphacode}`) with the selected returned `alpha2Code` or `alpha3Code`.
4. Alternative exact-name path: replace step 1 with function `search v2 countries by exact name` (`GET /v2/name/{name}`) using `fullText=true`, or replace step 3's v1 search with function `search v1 countries by exact name` (`GET /v1/name/{name}`) using `fullText=true`.

Optional verification workflow:
None.

Existing-state shortcuts:
- If the caller already has the selected country alpha code, the name-search step can be skipped and the alpha retrieval can be called directly.
- If the caller already has the full profile fields from the search response, the alpha retrieval can be skipped only for client display; it cannot be skipped if the behavior requires a fresh code-based canonical profile.
- Static setup requires editing the relevant JSON file before startup so the name and alpha code are present on the same country record.

Parameter and value bindings:
- The selected search result's `alpha2Code` or `alpha3Code` is reused as the `alphacode` path value.
- In v2, the `fields` list in the search may be intentionally narrower than the profile retrieval; this mismatch means "find candidates cheaply, then fetch richer selected details."
- The API does not preserve a server-side selection token; all binding is client-managed through returned code fields.

Business result:
The client obtains a canonical country profile selected from a human name search. No service state changes.

Constraints and invariants:
- Search result selection happens client-side.
- If the search returns multiple candidates, the API does not rank beyond primary-name matches before alternative-spelling matches.
- The alpha code used for the profile must come from the same API version's result to avoid v1/v2 schema mismatches.

Failure and exceptional cases:
- Failing function: `search v2 countries by partial name`
- Failure condition: No v2 candidate contains the supplied name.
- Why it fails: The search returns `404 Not Found`.
- Violated prerequisite or constraint: The user-entered name must match a v2 country name or alternative spelling.

- Failing function: `retrieve v2 country by alpha code`
- Failure condition: The client passes an alpha code not returned by the search or with invalid length.
- Why it fails: The retrieval returns `400` for invalid length or `404` for a non-existent valid-length code.
- Violated prerequisite or constraint: The profile step must reuse a real returned country code.

- Failing function: `search v1 countries by partial name`
- Failure condition: The v1 alternative path uses a name that exists only in v2 data.
- Why it fails: V1 and v2 datasets are separately loaded and searched; v1 returns `404`.
- Violated prerequisite or constraint: The selected API version's catalog must contain the searchable name.

Implementation notes:
- This workflow is API-realizable without mutation because alpha codes act as the stable link between search and profile retrieval.

### Behavior 25: Compare legacy and modern records for the same country

Business goal:
Inspect how the same country is represented in v1 and v2.

Domain context:
Clients migrating from v1 to v2 may need to compare schemas and values for a shared country code.

Starting point:
No prior API-created state. The country must exist in both the v1 and v2 static catalogs under the same alpha-2 or alpha-3 code.

Required execution workflow:
1. Use function `retrieve v1 country by alpha code` (`GET /v1/alpha/{alphacode}`) with `alphacode=FRA` to retrieve the v1 country record.
2. Use function `retrieve v2 country by alpha code` (`GET /v2/alpha/{alphacode}`) with the same `alphacode=FRA` and optional query `fields=name;alpha3Code;currencies;languages;regionalBlocs;flag` to retrieve the v2 representation.

Optional verification workflow:
1. Use function `search v2 countries by partial name` (`GET /v2/name/{name}`) with `name` copied from the v1 country `name` and query `fields=name;alpha3Code` to inspect whether v2 name search also resolves the country.

Existing-state shortcuts:
- If either version's country record is already available, the corresponding retrieval can be skipped for comparison input.
- Static setup requires editing both `countriesV1.json` and `countriesV2.json` before startup. The same `alphacode` must identify the intended country in both versions.

Parameter and value bindings:
- The same `alphacode` value is intentionally reused across v1 and v2 retrieval. This represents "same country identifier, different API version schema."
- V2 `fields` may intentionally differ from v1's full response because v1 does not support projection.
- The optional verification binds the v1 `name` response field into the v2 name-search `name` path value.

Business result:
The client has both v1 and v2 representations of the same country and can compare schema differences such as string currencies versus currency objects and string languages versus language objects. No service state changes.

Constraints and invariants:
- There is no server-side version-diff endpoint; comparison is client-side.
- The workflow assumes code consistency between the static v1 and v2 datasets.

Failure and exceptional cases:
- Failing function: `retrieve v1 country by alpha code`
- Failure condition: The shared code is absent from v1.
- Why it fails: V1 lookup returns `404 Not Found`.
- Violated prerequisite or constraint: The code must exist in the v1 catalog.

- Failing function: `retrieve v2 country by alpha code`
- Failure condition: The shared code is absent from v2.
- Why it fails: V2 lookup returns `404 Not Found`.
- Violated prerequisite or constraint: The code must exist in the v2 catalog.

- Failing function: `search v2 countries by partial name`
- Failure condition: The v1 name is not searchable in v2.
- Why it fails: V2 name search returns `404`; this may reflect data differences rather than country absence.
- Violated prerequisite or constraint: The v1 name must also appear in v2 name or alternative spelling data for this verification to succeed.

Implementation notes:
- This composite behavior is useful for migration analysis, but the API itself provides no schema mapping or diff semantics.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Maintain country records through the API

Priority:
Critical domain gap

Expected business goal:
Create, update, rename, correct, or delete country records through controlled API operations.

Why it is unsupported:
All documented country functions are reads. The implementation loads country data from static JSON into memory and exposes no country mutation endpoint.

Existing functions considered:
- `list v1 countries`: can reveal current v1 records but cannot create or change them.
- `retrieve v1 country by alpha code`: can inspect one v1 record but cannot update it.
- `retrieve v1 countries by alpha-code list`: can inspect selected v1 records but cannot maintain them.
- `list v2 countries`: can reveal current v2 records but cannot create or change them.
- `retrieve v2 country by alpha code`: can inspect one v2 record but cannot update it.
- `retrieve v2 countries by alpha-code list`: can inspect selected v2 records but cannot maintain them.
- All v1 and v2 search functions: can locate records but cannot mutate the underlying catalog.

Missing capability:
Country create, update, patch, delete, and validation endpoints, plus persistence support beyond static startup JSON.

Proof that function composition is insufficient:
Chaining reads can discover records but cannot create a missing country, correct a stale field, preserve a change after restart, delete a record, or validate a proposed update. Editing JSON before startup is not equivalent to an API behavior because it is out-of-band, not transactional, not authenticated, and not available at runtime.

Evidence from existing functions/source:
- The v1 and v2 services initialize from `countriesV1.json` and `countriesV2.json`.
- The only source-visible `POST /v1` and `POST /v2` methods return `405 Method Not Allowed` and are omitted from OpenAPI.

Business impact:
The service can be used as a static catalog but not as a country data management system.

### Missing Behavior 2: Make contribution payments through the documented API contract

Priority:
Critical domain gap

Expected business goal:
Allow users to submit a financial contribution and receive a reliable accepted or rejected payment outcome.

Why it is unsupported:
The source implements `POST /contribute`, but `full-behavior.md` does not define it as a documented country-data function and OpenAPI omits it. The implementation also sets `Stripe.apiKey` to an empty string, so real Stripe charging is not operational without out-of-band configuration.

Existing functions considered:
- `list v2 countries`: unrelated read-only catalog access.
- `retrieve v2 country by alpha code`: unrelated read-only catalog access.
- All documented country search functions: none accept payment token or amount values.

Missing capability:
A documented contribution function, OpenAPI path, configured Stripe credentials, payment validation rules, amount constraints, idempotency, and audit/persistence of contribution results.

Proof that function composition is insufficient:
Country read functions cannot create a Stripe charge, validate a payment token, store a contribution, or expose payment state. The source-only endpoint cannot be referenced as a supported documented function from `full-behavior.md`, and with an empty API key it is not a reliable business payment behavior.

Evidence from existing functions/source:
- `StripeRest` exposes `POST /contribute` with body fields `token` and `amount`, returns `400` for missing token or Stripe exceptions, and `202 Accepted` after `Charge.create`.
- `restcountries.yaml` contains no `/contribute` path.

Business impact:
Contribution functionality is invisible to contract-driven clients and likely fails in production-like execution unless configured outside the code.

### Missing Behavior 3: Server-side combined filtering

Priority:
Important robustness gap

Expected business goal:
Find countries that satisfy multiple domain filters at once, such as "European countries that use EUR and speak French" or "EU countries in Western Europe."

Why it is unsupported:
Each search endpoint applies exactly one criterion. No endpoint accepts multiple filters, and no server-side intersection operation exists.

Existing functions considered:
- `search v2 countries by region`: filters by region only.
- `search v2 countries by subregion`: filters by subregion only.
- `search v2 countries by currency`: filters by currency only.
- `search v2 countries by language`: filters by language only.
- `search v2 countries by regional bloc`: filters by bloc acronym only.
- V1 equivalents for region, subregion, currency, and language have the same single-filter limitation.

Missing capability:
A multi-criteria search endpoint or query model that combines filters with explicit AND/OR semantics.

Proof that function composition is insufficient:
Clients can call multiple single-filter functions and intersect returned alpha codes locally, but that is not equivalent to a server-side behavior. The server cannot validate combined criteria, provide consistent pagination, rank combined results, or return an atomic result calculated against one catalog snapshot.

Evidence from existing functions/source:
- Every resource method delegates to one service search method and returns that single result list.
- OpenAPI defines no query parameters for combining region, currency, language, bloc, capital, demonym, or name filters.

Business impact:
Clients duplicate filtering logic and can receive inconsistent or inefficient results for common domain queries.

### Missing Behavior 4: Safe bulk alpha lookup with per-code error reporting

Priority:
Important robustness gap

Expected business goal:
Retrieve several countries by code and know exactly which requested codes were valid, missing, duplicated, or malformed.

Why it is unsupported:
Bulk alpha lookup returns a list of countries and has inconsistent behavior for unresolved tokens.

Existing functions considered:
- `retrieve v1 countries by alpha-code list`: performs bulk lookup but may return `null` entries for unknown codes.
- `retrieve v2 countries by alpha-code list`: performs bulk lookup but may return `null` without projection or `500 Internal Server Error` with projection when an unknown code is present.
- `retrieve v1 country by alpha code` and `retrieve v2 country by alpha code`: can check one code at a time but cannot provide one atomic bulk report.

Missing capability:
A bulk lookup response model with per-code status, rejected malformed tokens, explicit not-found entries, and stable handling under field projection.

Proof that function composition is insufficient:
Calling single-code retrieval repeatedly can identify missing codes, but it is not one API operation and cannot preserve requested order, duplicate semantics, or atomic response shape. Current bulk endpoints cannot distinguish a successful country from an unresolved token represented as `null`.

Evidence from existing functions/source:
- `CountryServiceBase.getByCodeList` adds the result of `getByAlpha` even when it is `null`.
- V2 projection casts list elements to JSON objects, which fails on `null`.

Business impact:
Bulk consumers can receive ambiguous success responses or server errors for ordinary missing-code cases.

### Missing Behavior 5: Validate field projection requests

Priority:
Important robustness gap

Expected business goal:
Ask for selected v2 fields and receive either those fields or a clear validation error for unsupported field names.

Why it is unsupported:
The v2 `fields` query silently ignores unknown field names by removing all fields not exactly listed.

Existing functions considered:
- `list v2 countries`: supports projection but does not validate fields.
- `retrieve v2 country by alpha code`: supports projection but does not validate fields.
- All v2 search functions: support projection but share the same non-validating projection logic.

Missing capability:
Field-name validation, error reporting for unknown fields, trimming/normalization of field tokens, and an OpenAPI enum or schema for legal field names.

Proof that function composition is insufficient:
No existing function can ask the server which field names are valid or validate a projection before use. A client can infer validity by inspecting empty or missing fields, but cannot distinguish a valid null/absent field from a misspelled projection request.

Evidence from existing functions/source:
- `getExcludedFields` starts with a fixed implementation field list and removes requested names; unknown names simply remain ineffective.
- OpenAPI declares `fields` only as `type: string`.

Business impact:
Clients can ship broken projections that look like successful API calls but contain incomplete or empty data.

### Missing Behavior 6: Traverse country relationships such as borders

Priority:
Important robustness gap

Expected business goal:
Given one country, retrieve its neighboring countries or other related countries using the `borders` relationship.

Why it is unsupported:
Country records contain `borders`, but no endpoint expands those codes into country records or validates relationship consistency.

Existing functions considered:
- `retrieve v2 country by alpha code`: can return a country's `borders` list.
- `retrieve v2 countries by alpha-code list`: can retrieve countries for a client-constructed `codes` query from border codes, but is unsafe when any border code is missing.
- `retrieve v1 country by alpha code` and `retrieve v1 countries by alpha-code list`: provide the same pattern for v1.

Missing capability:
A relationship traversal endpoint such as `GET /v2/alpha/{alphacode}/borders`, plus relationship validation and per-border error handling.

Proof that function composition is insufficient:
Client-side composition can read `borders` and call the bulk code endpoint, but it cannot guarantee referential integrity, distinguish missing border codes safely in bulk, or express the domain relationship as a first-class server behavior.

Evidence from existing functions/source:
- `BaseCountry` includes `borders`.
- No OpenAPI path or resource method exposes border expansion.

Business impact:
Map and geography clients must implement relationship traversal and error handling themselves.

### Missing Behavior 7: Full-name regional bloc search

Priority:
API ergonomics gap

Expected business goal:
Find countries by regional bloc name, not only acronym, for example "European Union."

Why it is unsupported:
The v2 regional bloc search only compares `acronym` and `otherAcronyms`.

Existing functions considered:
- `search v2 countries by regional bloc`: searches bloc acronym and alternate acronyms only.
- `list v2 countries`: can return `regionalBlocs`, allowing client-side full-name filtering.

Missing capability:
Server-side regional bloc name matching against the bloc `name` field.

Proof that function composition is insufficient:
The client can list countries and filter bloc names locally, but no existing function performs server-side full-name bloc lookup or returns `404`/success according to full-name semantics.

Evidence from existing functions/source:
- `CountryService.getByRegionalBloc` checks `getAcronym()` and `getOtherAcronyms()` but not bloc name.

Business impact:
Users must know acronyms rather than human-readable bloc names, weakening discoverability.

### Missing Behavior 8: Contract coverage for source-visible endpoints

Priority:
API ergonomics gap

Expected business goal:
Expose all supported routes consistently through OpenAPI so generated clients and tests understand the service surface.

Why it is unsupported:
Several source-visible endpoints are absent from OpenAPI.

Existing functions considered:
- `list v1 countries`: documents `GET /v1/all`, but source also exposes undocumented `GET /v1`.
- `list v2 countries`: documents `GET /v2/all`, but source also exposes undocumented `GET /v2`.
- No documented function exists for source-visible `POST /v1`, `POST /v2`, or `POST /contribute`.

Missing capability:
OpenAPI paths for root list aliases, explicit 405 responses for rejected POSTs, and a documented contribution path if it is intended to be public.

Proof that function composition is insufficient:
Documented country functions cannot tell a contract-driven client that alias routes or source-only POST routes exist. Conversely, source-only routes cannot be safely treated as supported contract behavior.

Evidence from existing functions/source:
- `restcountries.yaml` lists only `/v1/all`, documented v1 searches, `/v2/all`, and documented v2 searches.
- `CountryRestV1`, `CountryRestV2`, and `StripeRest` contain additional resource methods.

Business impact:
Generated clients and API consumers have an incomplete view of the runtime service.

## Cross-Behavior Observations

- The country catalog is read-only through documented APIs. Runtime country maintenance is impossible without editing bundled JSON and restarting.
- V1 and v2 are separate static datasets. Shared alpha codes usually link the same country across versions, but the API does not enforce cross-version consistency.
- V2 field projection is powerful but weakly validated. Unknown fields do not fail and can produce empty objects.
- Bulk alpha lookup has inconsistent handling of unknown codes, including successful responses containing `null` and v2 projection failures.
- Name and capital searches normalize case and accents and use substring matching unless exact name search is selected with `fullText=true`.
- Region and subregion searches are exact after case normalization and are data-driven rather than enum-driven.
- V2 language search treats two-character and three-character values differently, but invalid lengths return `404` instead of `400`.
- There is no authentication, ownership, tenant scoping, generated id, ETag, cursor, or pagination behavior in the documented country functions.
- OpenAPI and implementation disagree on route coverage: root `GET /v1`, root `GET /v2`, rejected root POSTs, and `POST /contribute` are source-visible but omitted from the contract.
- No documented endpoint mutates country state, so there are no cascading deletes, active/inactive states, or automatic reevaluation workflows.

## Coverage Summary

Supported domain areas:
- Listing v1 and v2 country catalogs.
- Retrieving one or multiple countries by alpha code.
- Searching by name, currency, calling code, capital, region, subregion, and language.
- V2-only searching by demonym and regional bloc acronym.
- V2-only response field projection for lightweight client views.
- Client-side composite workflows that reuse returned alpha codes to move from search results to canonical profile retrieval.

Partially supported domain areas:
- Bulk lookup is present but has weak missing-code semantics.
- Regional bloc discovery exists only by acronym or alternate acronym.
- Relationship traversal is possible only by client-side reuse of `borders` codes.
- V2 projection exists but has no validation or discoverable field schema.
- Contribution payment exists in source but is not contract-backed and is not reliable with the visible empty Stripe key.

Unsupported domain areas:
- Country creation, update, patch, deletion, validation, and persistence through the API.
- Server-side combined filtering and result pagination.
- Per-code bulk lookup diagnostics.
- First-class border/neighbor traversal.
- Contract-complete documentation of all runtime routes.
