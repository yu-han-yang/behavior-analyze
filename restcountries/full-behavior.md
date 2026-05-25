Source notes:
- `restcountries.yaml` and `src/main/resources/static/openapi.yaml` are identical.
- Paths below use the OpenAPI path portion; the configured server base is `/rest`.
- No documented endpoint creates or mutates countries. Country state is loaded into in-memory lists from `countriesV1.json` and `countriesV2.json`; there is no API setup endpoint and no database-backed setup path.
- The implementation also registers source-visible routes not present in OpenAPI: root `GET /v1`, root `GET /v2`, `POST /v1`, `POST /v2`, and `POST /contribute`. They are listed separately at the end as source/OpenAPI discrepancies; runtime details for documented endpoints follow the source code when it differs from OpenAPI.

### Function 1: list v1 countries

Function name:
list v1 countries

Core endpoint(s):
- `GET /v1/all`

Preconditions:
- The v1 country dataset is loaded from `countriesV1.json` into the singleton country service. This is satisfied by application startup loading the JSON resource; no documented endpoint establishes this state, and direct database setup is not applicable because the implementation does not use a database for country state.

Successful execution:
- Result:
  Returns every country record from the v1 dataset.
- Invocation:
  Step 1: `GET /v1/all` with no required path, query, body, form, or header values
- Constraints:
  No request-controlled filter is applied. The source also exposes undocumented root `GET /v1` with the same list operation.

Failure or exceptional branches:
- None visible from request-controlled inputs for this documented endpoint.

Endpoint coverage:
- Covers:
  `GET /v1/all`
- Distinct meaning:
  Lists the complete v1 country collection.

### Function 2: retrieve v1 country by alpha code

Function name:
retrieve v1 country by alpha code

Core endpoint(s):
- `GET /v1/alpha/{alphacode}`

Preconditions:
- The v1 country dataset is loaded from `countriesV1.json`. For a successful lookup, it contains a country whose `alpha2Code` or `alpha3Code` matches `{alphacode}` case-insensitively. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns the single v1 country whose `alpha2Code` or `alpha3Code` matches `{alphacode}` case-insensitively.
- Invocation:
  Step 1: `GET /v1/alpha/{alphacode}` with `{alphacode}` as the path value
- Constraints:
  `{alphacode}` must be exactly 2 or 3 characters and must match an existing preloaded country code.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{alphacode}` is absent, shorter than 2 characters, or longer than 3 characters.
  - Failure endpoint:
    `GET /v1/alpha/{alphacode}`
  - Why this fails:
    The resource method rejects empty or invalid alpha-code lengths before lookup and returns `400 Bad Request`.
  - Intentionally violated constraints:
    `{alphacode}` is not a 2-character alpha-2 code or 3-character alpha-3 code.
- Branch 2:
  - Preconditions:
    - The v1 dataset is loaded, but no country has an `alpha2Code` or `alpha3Code` matching `{alphacode}`. This can be produced by using a valid-length unknown code and not adding a matching JSON resource entry before startup.
  - Failure endpoint:
    `GET /v1/alpha/{alphacode}`
  - Why this fails:
    The service lookup returns `null`, so the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{alphacode}` has valid length but does not exist in the v1 dataset.

Endpoint coverage:
- Covers:
  `GET /v1/alpha/{alphacode}`
- Distinct meaning:
  Reads one v1 country by alpha-2 or alpha-3 code.

### Function 3: retrieve v1 countries by alpha-code list

Function name:
retrieve v1 countries by alpha-code list

Core endpoint(s):
- `GET /v1/alpha`

Preconditions:
- The v1 country dataset is loaded from `countriesV1.json`. For a meaningful successful lookup, one or more semicolon-separated `codes` tokens match preloaded `alpha2Code` or `alpha3Code` values. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v1 countries matching the semicolon-separated `codes` query.
- Invocation:
  Step 1: `GET /v1/alpha` with required query parameter `codes={code1};{code2}`
- Constraints:
  `codes` is required. The raw query must be at least 2 characters, and values longer than 3 characters must contain `;`. Tokens are split on `;`; duplicate resolved country objects are not repeated.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `codes` is missing, empty, shorter than 2 characters, or longer than 3 characters without containing `;`.
  - Failure endpoint:
    `GET /v1/alpha`
  - Why this fails:
    The resource method validates the raw `codes` query before splitting and returns `400 Bad Request`.
  - Intentionally violated constraints:
    The required `codes` query is absent or not in accepted single-code or semicolon-list form.
- Branch 2:
  - Preconditions:
    - `codes` contains only semicolon separators and no usable token, so no country lookup can be performed.
  - Failure endpoint:
    `GET /v1/alpha`
  - Why this fails:
    The split list resolves to no countries and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `codes` does not provide any real alpha-code token.
- Branch 3:
  - Preconditions:
    - At least one syntactically accepted `codes` token does not match any v1 country in the preloaded dataset.
  - Failure endpoint:
    `GET /v1/alpha`
  - Why this fails:
    This is an implementation exception from expected API semantics rather than an HTTP failure: the shared list lookup can add `null` for an unmatched token, so the endpoint may return a successful list containing `null` instead of `404 Not Found`.
  - Intentionally violated constraints:
    At least one `codes` token is not an existing v1 alpha code.

Endpoint coverage:
- Covers:
  `GET /v1/alpha`
- Distinct meaning:
  Reads multiple v1 countries by semicolon-separated alpha codes.

### Function 4: search v1 countries by currency

Function name:
search v1 countries by currency

Core endpoint(s):
- `GET /v1/currency/{currency}`

Preconditions:
- The v1 country dataset is loaded from `countriesV1.json`. For a successful search, at least one country has a stored currency string equal to `{currency}` case-insensitively. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v1 countries whose stored currency string matches `{currency}` case-insensitively.
- Invocation:
  Step 1: `GET /v1/currency/{currency}` with `{currency}` as the path value
- Constraints:
  `{currency}` must be exactly 3 characters and match a currency code in the v1 dataset.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{currency}` is missing or is not exactly 3 characters.
  - Failure endpoint:
    `GET /v1/currency/{currency}`
  - Why this fails:
    The endpoint rejects empty or non-3-character currency values and returns `400 Bad Request`.
  - Intentionally violated constraints:
    `{currency}` is not a 3-character currency code.
- Branch 2:
  - Preconditions:
    - The v1 dataset is loaded, but no country uses `{currency}`. This can be produced by using a valid-length unknown code and not adding a matching JSON resource entry before startup.
  - Failure endpoint:
    `GET /v1/currency/{currency}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{currency}` has valid length but is absent from the dataset.

Endpoint coverage:
- Covers:
  `GET /v1/currency/{currency}`
- Distinct meaning:
  Searches v1 countries by currency code.

### Function 5: search v1 countries by partial name

Function name:
search v1 countries by partial name

Core endpoint(s):
- `GET /v1/name/{name}`

Preconditions:
- The v1 country dataset is loaded from `countriesV1.json`. For a successful partial search, at least one country `name` or `altSpellings` value contains `{name}` after case and accent normalization. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v1 countries whose name or alternative spelling contains `{name}`, case-insensitively and accent-insensitively.
- Invocation:
  Step 1: `GET /v1/name/{name}` with `{name}` as the path value and `fullText` omitted or set to `false`
- Constraints:
  `fullText` must be omitted or set to `false`. `{name}` must be contained in at least one country name or alternative spelling after normalization.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v1 dataset is loaded, but no country name or alternative spelling contains `{name}` after normalization.
  - Failure endpoint:
    `GET /v1/name/{name}`
  - Why this fails:
    The substring search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{name}` is not a substring of any searchable v1 country name or alternative spelling.

Endpoint coverage:
- Covers:
  `GET /v1/name/{name}`
- Distinct meaning:
  Partial-name search when `fullText` is omitted or `false`.

### Function 6: search v1 countries by exact name

Function name:
search v1 countries by exact name

Core endpoint(s):
- `GET /v1/name/{name}`

Preconditions:
- The v1 country dataset is loaded from `countriesV1.json`. For a successful exact search, at least one country `name` or `altSpellings` value exactly equals `{name}` after case and accent normalization. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v1 countries whose name or alternative spelling exactly equals `{name}`, case-insensitively and accent-insensitively.
- Invocation:
  Step 1: `GET /v1/name/{name}` with `{name}` as the path value and query parameter `fullText=true`
- Constraints:
  `fullText=true` is required. `{name}` must exactly match a country name or alternative spelling after normalization.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v1 dataset is loaded, but no country name or alternative spelling exactly equals `{name}` after normalization.
  - Failure endpoint:
    `GET /v1/name/{name}`
  - Why this fails:
    The exact-name search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `fullText=true` is used, but `{name}` is not an exact normalized match.

Endpoint coverage:
- Covers:
  `GET /v1/name/{name}`
- Distinct meaning:
  Exact-name search when `fullText=true`.

### Function 7: search v1 countries by calling code

Function name:
search v1 countries by calling code

Core endpoint(s):
- `GET /v1/callingcode/{callingcode}`

Preconditions:
- The v1 country dataset is loaded from `countriesV1.json`. For a successful search, at least one country has `{callingcode}` in its calling-code list. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v1 countries whose calling-code list contains `{callingcode}` exactly.
- Invocation:
  Step 1: `GET /v1/callingcode/{callingcode}` with `{callingcode}` as the path value
- Constraints:
  `{callingcode}` must exactly equal a stored calling-code string.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v1 dataset is loaded, but no country contains `{callingcode}` in its calling-code list.
  - Failure endpoint:
    `GET /v1/callingcode/{callingcode}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{callingcode}` does not exist in the v1 dataset.

Endpoint coverage:
- Covers:
  `GET /v1/callingcode/{callingcode}`
- Distinct meaning:
  Searches v1 countries by telephone calling code.

### Function 8: search v1 countries by capital

Function name:
search v1 countries by capital

Core endpoint(s):
- `GET /v1/capital/{capital}`

Preconditions:
- The v1 country dataset is loaded from `countriesV1.json`. For a successful search, at least one country has a capital containing `{capital}` after case and accent normalization. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v1 countries whose capital contains `{capital}`, case-insensitively and accent-insensitively.
- Invocation:
  Step 1: `GET /v1/capital/{capital}` with `{capital}` as the path value
- Constraints:
  `{capital}` must be contained in at least one stored capital name after normalization.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v1 dataset is loaded, but no country capital contains `{capital}` after normalization.
  - Failure endpoint:
    `GET /v1/capital/{capital}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{capital}` is not a normalized substring of any v1 capital.

Endpoint coverage:
- Covers:
  `GET /v1/capital/{capital}`
- Distinct meaning:
  Searches v1 countries by capital-name substring.

### Function 9: search v1 countries by region

Function name:
search v1 countries by region

Core endpoint(s):
- `GET /v1/region/{region}`

Preconditions:
- The v1 country dataset is loaded from `countriesV1.json`. For a successful search, at least one country has a `region` equal to `{region}` case-insensitively. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v1 countries whose region exactly matches `{region}` case-insensitively.
- Invocation:
  Step 1: `GET /v1/region/{region}` with `{region}` as the path value
- Constraints:
  `{region}` must equal a stored region value.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v1 dataset is loaded, but no country has a `region` equal to `{region}` case-insensitively.
  - Failure endpoint:
    `GET /v1/region/{region}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{region}` is not an existing v1 region value.

Endpoint coverage:
- Covers:
  `GET /v1/region/{region}`
- Distinct meaning:
  Searches v1 countries by exact region.

### Function 10: search v1 countries by subregion

Function name:
search v1 countries by subregion

Core endpoint(s):
- `GET /v1/subregion/{subregion}`

Preconditions:
- The v1 country dataset is loaded from `countriesV1.json`. For a successful search, at least one country has a `subregion` equal to `{subregion}` case-insensitively. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v1 countries whose subregion exactly matches `{subregion}` case-insensitively.
- Invocation:
  Step 1: `GET /v1/subregion/{subregion}` with `{subregion}` as the path value
- Constraints:
  `{subregion}` must equal a stored subregion value.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v1 dataset is loaded, but no country has a `subregion` equal to `{subregion}` case-insensitively.
  - Failure endpoint:
    `GET /v1/subregion/{subregion}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{subregion}` is not an existing v1 subregion value.

Endpoint coverage:
- Covers:
  `GET /v1/subregion/{subregion}`
- Distinct meaning:
  Searches v1 countries by exact subregion.

### Function 11: search v1 countries by language

Function name:
search v1 countries by language

Core endpoint(s):
- `GET /v1/lang/{lang}`

Preconditions:
- The v1 country dataset is loaded from `countriesV1.json`. For a successful search, at least one country has a stored language string equal to `{lang}` case-insensitively. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v1 countries whose stored language string equals `{lang}` case-insensitively.
- Invocation:
  Step 1: `GET /v1/lang/{lang}` with `{lang}` as the path value
- Constraints:
  `{lang}` must equal a language value stored in the v1 country data.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v1 dataset is loaded, but no country has a stored language string equal to `{lang}` case-insensitively.
  - Failure endpoint:
    `GET /v1/lang/{lang}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{lang}` is not present in any v1 country language list.

Endpoint coverage:
- Covers:
  `GET /v1/lang/{lang}`
- Distinct meaning:
  Searches v1 countries by language code or string.

### Function 12: list v2 countries

Function name:
list v2 countries

Core endpoint(s):
- `GET /v2/all`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json` into the singleton country service. This is satisfied by application startup loading the JSON resource; no documented endpoint establishes this state, and direct database setup is not applicable because the implementation does not use a database for country state.

Successful execution:
- Result:
  Returns every country record from the v2 dataset, optionally projected to selected fields.
- Invocation:
  Step 1: `GET /v2/all` with optional query parameter `fields={field1};{field2}`
- Constraints:
  `fields` is optional. When present, it is a semicolon-separated inclusion list of exact v2 field names; omitted or empty `fields` returns full v2 country objects. Unknown field names are not validated and simply do not prevent other fields from being removed. The source also exposes undocumented root `GET /v2` with the same list operation.

Failure or exceptional branches:
- None visible from request-controlled inputs for this documented endpoint.

Endpoint coverage:
- Covers:
  `GET /v2/all`
- Distinct meaning:
  Lists the complete v2 country collection with optional field projection.

### Function 13: retrieve v2 country by alpha code

Function name:
retrieve v2 country by alpha code

Core endpoint(s):
- `GET /v2/alpha/{alphacode}`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json`. For a successful lookup, it contains a country whose `alpha2Code` or `alpha3Code` matches `{alphacode}` case-insensitively. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns the single v2 country whose `alpha2Code` or `alpha3Code` matches `{alphacode}` case-insensitively, optionally projected to selected fields.
- Invocation:
  Step 1: `GET /v2/alpha/{alphacode}` with `{alphacode}` as the path value and optional query parameter `fields={field1};{field2}`
- Constraints:
  `{alphacode}` must be exactly 2 or 3 characters and match an existing country. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{alphacode}` is absent, shorter than 2 characters, or longer than 3 characters.
  - Failure endpoint:
    `GET /v2/alpha/{alphacode}`
  - Why this fails:
    The endpoint rejects invalid alpha-code length before lookup and returns `400 Bad Request`.
  - Intentionally violated constraints:
    `{alphacode}` is not a 2-character alpha-2 code or 3-character alpha-3 code.
- Branch 2:
  - Preconditions:
    - The v2 dataset is loaded, but no country has an `alpha2Code` or `alpha3Code` matching `{alphacode}`.
  - Failure endpoint:
    `GET /v2/alpha/{alphacode}`
  - Why this fails:
    The service lookup returns `null`, so the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{alphacode}` has valid length but does not exist in the v2 dataset.

Endpoint coverage:
- Covers:
  `GET /v2/alpha/{alphacode}`
- Distinct meaning:
  Reads one v2 country by alpha-2 or alpha-3 code with optional field projection.

### Function 14: retrieve v2 countries by alpha-code list

Function name:
retrieve v2 countries by alpha-code list

Core endpoint(s):
- `GET /v2/alpha`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json`. For a meaningful successful lookup, one or more semicolon-separated `codes` tokens match preloaded `alpha2Code` or `alpha3Code` values. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v2 countries matching the semicolon-separated `codes` query, optionally projected to selected fields.
- Invocation:
  Step 1: `GET /v2/alpha` with required query parameter `codes={code1};{code2}` and optional query parameter `fields={field1};{field2}`
- Constraints:
  `codes` is required. The raw query must be at least 2 characters, and values longer than 3 characters must contain `;`. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `codes` is missing, empty, shorter than 2 characters, or longer than 3 characters without containing `;`.
  - Failure endpoint:
    `GET /v2/alpha`
  - Why this fails:
    The endpoint validates the raw `codes` query before splitting and returns `400 Bad Request`.
  - Intentionally violated constraints:
    The required `codes` query is absent or malformed.
- Branch 2:
  - Preconditions:
    - `codes` contains only semicolon separators and no usable token, so no country lookup can be performed.
  - Failure endpoint:
    `GET /v2/alpha`
  - Why this fails:
    The split list resolves to no countries and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `codes` does not provide any real alpha-code token.
- Branch 3:
  - Preconditions:
    - A syntactically accepted `codes` token does not match any country in the v2 dataset, and `fields` is omitted.
  - Failure endpoint:
    `GET /v2/alpha`
  - Why this fails:
    This is an implementation exception from expected API semantics rather than an HTTP failure: the shared lookup can add `null` for an unmatched token, so the endpoint may return a successful list containing `null` instead of `404 Not Found`.
  - Intentionally violated constraints:
    At least one `codes` token is not an existing v2 alpha code.
- Branch 4:
  - Preconditions:
    - A syntactically accepted `codes` token does not match any country in the v2 dataset, and `fields` is present.
  - Failure endpoint:
    `GET /v2/alpha`
  - Why this fails:
    The field-projection code parses the list as JSON and casts each array element to a JSON object. A `null` item from the unresolved code causes an exception, which the endpoint catches and reports as `500 Internal Server Error`.
  - Intentionally violated constraints:
    `codes` includes an unresolved alpha token while requesting projected fields.

Endpoint coverage:
- Covers:
  `GET /v2/alpha`
- Distinct meaning:
  Reads multiple v2 countries by semicolon-separated alpha codes with optional field projection.

### Function 15: search v2 countries by currency

Function name:
search v2 countries by currency

Core endpoint(s):
- `GET /v2/currency/{currency}`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json`. For a successful search, at least one country has a currency object whose `code` equals `{currency}` case-insensitively. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v2 countries whose currency object has a `code` matching `{currency}` case-insensitively.
- Invocation:
  Step 1: `GET /v2/currency/{currency}` with `{currency}` as the path value and optional query parameter `fields={field1};{field2}`
- Constraints:
  `{currency}` must be exactly 3 characters and match a stored currency `code`. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - `{currency}` is missing or is not exactly 3 characters.
  - Failure endpoint:
    `GET /v2/currency/{currency}`
  - Why this fails:
    The endpoint rejects empty or non-3-character currency values and returns `400 Bad Request`.
  - Intentionally violated constraints:
    `{currency}` is not a 3-character currency code.
- Branch 2:
  - Preconditions:
    - The v2 dataset is loaded, but no country uses `{currency}` as a currency object `code`.
  - Failure endpoint:
    `GET /v2/currency/{currency}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{currency}` has valid length but is absent from the dataset.

Endpoint coverage:
- Covers:
  `GET /v2/currency/{currency}`
- Distinct meaning:
  Searches v2 countries by currency code with optional field projection.

### Function 16: search v2 countries by partial name

Function name:
search v2 countries by partial name

Core endpoint(s):
- `GET /v2/name/{name}`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json`. For a successful partial search, at least one country `name` or `altSpellings` value contains `{name}` after case and accent normalization. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v2 countries whose name or alternative spelling contains `{name}`, case-insensitively and accent-insensitively.
- Invocation:
  Step 1: `GET /v2/name/{name}` with `{name}` as the path value, `fullText` omitted or set to `false`, and optional query parameter `fields={field1};{field2}`
- Constraints:
  `fullText` must be omitted or set to `false`. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v2 dataset is loaded, but no country name or alternative spelling contains `{name}` after normalization.
  - Failure endpoint:
    `GET /v2/name/{name}`
  - Why this fails:
    The substring search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{name}` is not a substring of any searchable v2 country name or alternative spelling.

Endpoint coverage:
- Covers:
  `GET /v2/name/{name}`
- Distinct meaning:
  Partial-name search when `fullText` is omitted or `false`, with optional field projection.

### Function 17: search v2 countries by exact name

Function name:
search v2 countries by exact name

Core endpoint(s):
- `GET /v2/name/{name}`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json`. For a successful exact search, at least one country `name` or `altSpellings` value exactly equals `{name}` after case and accent normalization. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v2 countries whose name or alternative spelling exactly equals `{name}`, case-insensitively and accent-insensitively.
- Invocation:
  Step 1: `GET /v2/name/{name}` with `{name}` as the path value, query parameter `fullText=true`, and optional query parameter `fields={field1};{field2}`
- Constraints:
  `fullText=true` is required. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v2 dataset is loaded, but no country name or alternative spelling exactly equals `{name}` after normalization.
  - Failure endpoint:
    `GET /v2/name/{name}`
  - Why this fails:
    The exact-name search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `fullText=true` is used, but `{name}` is not an exact normalized match.

Endpoint coverage:
- Covers:
  `GET /v2/name/{name}`
- Distinct meaning:
  Exact-name search when `fullText=true`, with optional field projection.

### Function 18: search v2 countries by calling code

Function name:
search v2 countries by calling code

Core endpoint(s):
- `GET /v2/callingcode/{callingcode}`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json`. For a successful search, at least one country has `{callingcode}` in its calling-code list. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v2 countries whose calling-code list contains `{callingcode}` exactly.
- Invocation:
  Step 1: `GET /v2/callingcode/{callingcode}` with `{callingcode}` as the path value and optional query parameter `fields={field1};{field2}`
- Constraints:
  `{callingcode}` must exactly equal a stored calling-code string. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v2 dataset is loaded, but no country contains `{callingcode}` in its calling-code list.
  - Failure endpoint:
    `GET /v2/callingcode/{callingcode}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{callingcode}` does not exist in the v2 dataset.

Endpoint coverage:
- Covers:
  `GET /v2/callingcode/{callingcode}`
- Distinct meaning:
  Searches v2 countries by telephone calling code with optional field projection.

### Function 19: search v2 countries by capital

Function name:
search v2 countries by capital

Core endpoint(s):
- `GET /v2/capital/{capital}`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json`. For a successful search, at least one country has a capital containing `{capital}` after case and accent normalization. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v2 countries whose capital contains `{capital}`, case-insensitively and accent-insensitively.
- Invocation:
  Step 1: `GET /v2/capital/{capital}` with `{capital}` as the path value and optional query parameter `fields={field1};{field2}`
- Constraints:
  `{capital}` must be contained in at least one stored capital name after normalization. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v2 dataset is loaded, but no country capital contains `{capital}` after normalization.
  - Failure endpoint:
    `GET /v2/capital/{capital}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{capital}` is not a normalized substring of any v2 capital.

Endpoint coverage:
- Covers:
  `GET /v2/capital/{capital}`
- Distinct meaning:
  Searches v2 countries by capital-name substring with optional field projection.

### Function 20: search v2 countries by region

Function name:
search v2 countries by region

Core endpoint(s):
- `GET /v2/region/{region}`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json`. For a successful search, at least one country has a `region` equal to `{region}` case-insensitively. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v2 countries whose region exactly matches `{region}` case-insensitively.
- Invocation:
  Step 1: `GET /v2/region/{region}` with `{region}` as the path value and optional query parameter `fields={field1};{field2}`
- Constraints:
  `{region}` must equal a stored region value. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v2 dataset is loaded, but no country has a `region` equal to `{region}` case-insensitively.
  - Failure endpoint:
    `GET /v2/region/{region}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{region}` is not an existing v2 region value.

Endpoint coverage:
- Covers:
  `GET /v2/region/{region}`
- Distinct meaning:
  Searches v2 countries by exact region with optional field projection.

### Function 21: search v2 countries by subregion

Function name:
search v2 countries by subregion

Core endpoint(s):
- `GET /v2/subregion/{subregion}`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json`. For a successful search, at least one country has a `subregion` equal to `{subregion}` case-insensitively. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v2 countries whose subregion exactly matches `{subregion}` case-insensitively.
- Invocation:
  Step 1: `GET /v2/subregion/{subregion}` with `{subregion}` as the path value and optional query parameter `fields={field1};{field2}`
- Constraints:
  `{subregion}` must equal a stored subregion value. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v2 dataset is loaded, but no country has a `subregion` equal to `{subregion}` case-insensitively.
  - Failure endpoint:
    `GET /v2/subregion/{subregion}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{subregion}` is not an existing v2 subregion value.

Endpoint coverage:
- Covers:
  `GET /v2/subregion/{subregion}`
- Distinct meaning:
  Searches v2 countries by exact subregion with optional field projection.

### Function 22: search v2 countries by language

Function name:
search v2 countries by language

Core endpoint(s):
- `GET /v2/lang/{lang}`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json`. For a successful search, at least one country has a language object whose `iso639_1` equals `{lang}` when `{lang}` has length 2, or whose `iso639_2` equals `{lang}` when `{lang}` has length 3. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v2 countries whose language object matches `{lang}` by ISO 639-1 when `{lang}` has length 2, or by ISO 639-2 when `{lang}` has length 3.
- Invocation:
  Step 1: `GET /v2/lang/{lang}` with `{lang}` as the path value and optional query parameter `fields={field1};{field2}`
- Constraints:
  `{lang}` should be a 2-character `iso639_1` value or a 3-character `iso639_2` value. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v2 dataset is loaded, but no language object matches `{lang}`. This includes unsupported lengths other than 2 or 3, which are not rejected before lookup.
  - Failure endpoint:
    `GET /v2/lang/{lang}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`; invalid language-code length is not reported as `400 Bad Request`.
  - Intentionally violated constraints:
    `{lang}` is not a matching ISO 639-1 or ISO 639-2 value.

Endpoint coverage:
- Covers:
  `GET /v2/lang/{lang}`
- Distinct meaning:
  Searches v2 countries by language ISO code with optional field projection.

### Function 23: search v2 countries by demonym

Function name:
search v2 countries by demonym

Core endpoint(s):
- `GET /v2/demonym/{demonym}`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json`. For a successful search, at least one country has a `demonym` equal to `{demonym}` after lowercasing and accent normalization of the input. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v2 countries whose demonym exactly matches `{demonym}` case-insensitively after normalizing the input.
- Invocation:
  Step 1: `GET /v2/demonym/{demonym}` with `{demonym}` as the path value and optional query parameter `fields={field1};{field2}`
- Constraints:
  `{demonym}` must equal a stored country demonym after input normalization. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v2 dataset is loaded, but no country has a demonym equal to `{demonym}` under the implementation's comparison.
  - Failure endpoint:
    `GET /v2/demonym/{demonym}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{demonym}` is not an existing v2 demonym.

Endpoint coverage:
- Covers:
  `GET /v2/demonym/{demonym}`
- Distinct meaning:
  Searches v2 countries by demonym with optional field projection.

### Function 24: search v2 countries by regional bloc

Function name:
search v2 countries by regional bloc

Core endpoint(s):
- `GET /v2/regionalbloc/{regionalbloc}`

Preconditions:
- The v2 country dataset is loaded from `countriesV2.json`. For a successful search, at least one country has a regional bloc whose `acronym` equals `{regionalbloc}` case-insensitively or whose `otherAcronyms` contains the uppercased `{regionalbloc}` value. This can be satisfied by editing the JSON resource before startup; no documented endpoint or direct database setup establishes this state.

Successful execution:
- Result:
  Returns v2 countries whose regional bloc acronym or `otherAcronyms` contains `{regionalbloc}`, case-insensitively.
- Invocation:
  Step 1: `GET /v2/regionalbloc/{regionalbloc}` with `{regionalbloc}` as the path value and optional query parameter `fields={field1};{field2}`
- Constraints:
  `{regionalbloc}` must match a regional bloc acronym or other acronym, not a bloc name. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - The v2 dataset is loaded, but no country has a regional bloc acronym or alternate acronym matching `{regionalbloc}`.
  - Failure endpoint:
    `GET /v2/regionalbloc/{regionalbloc}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{regionalbloc}` is not present as a regional bloc acronym or alternate acronym.

Endpoint coverage:
- Covers:
  `GET /v2/regionalbloc/{regionalbloc}`
- Distinct meaning:
  Searches v2 countries by regional bloc acronym with optional field projection.

Additional source-visible endpoints:
- `GET /v1` and `GET /v2` are implemented root list aliases but are omitted from OpenAPI. The corresponding documented functions are `GET /v1/all` and `GET /v2/all`.
- `POST /v1` and `POST /v2` are implemented to return `405 Method Not Allowed` and are omitted from OpenAPI.
- `POST /contribute` is implemented as a Stripe charge submission endpoint and is omitted from OpenAPI, so it is a source/OpenAPI discrepancy rather than a documented country-data function.
