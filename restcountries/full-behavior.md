Source notes:
- `restcountries.yaml` and `src/main/resources/static/openapi.yaml` are identical.
- Paths below use the OpenAPI path portion; the configured server base is `/rest`.
- No documented endpoint creates or mutates countries. Country state is preloaded from `countriesV1.json` and `countriesV2.json`, so resource-specific reads have no API setup sequence.

### Behavior 1: list v1 countries

Behavior name:
list v1 countries

Successful execution:
- Result:
  Returns every country record from the v1 dataset.
- Endpoint sequence:
  Step 1: `GET /v1/all`
- Constraints:
  No prerequisite endpoint exists. The response is the full v1 country list.

Failure or exceptional branches:
- None visible from request-controlled inputs for this documented endpoint.

Endpoint coverage:
- Covers:
  `GET /v1/all`
- Distinct meaning:
  Lists the complete v1 country collection.

### Behavior 2: retrieve v1 country by alpha code

Behavior name:
retrieve v1 country by alpha code

Successful execution:
- Result:
  Returns the single v1 country whose `alpha2Code` or `alpha3Code` matches `{alphacode}` case-insensitively.
- Endpoint sequence:
  Step 1: `GET /v1/alpha/{alphacode}`
- Constraints:
  `{alphacode}` must be exactly 2 or 3 characters and must match an existing preloaded country code.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{alphacode}` is shorter than 2 characters or longer than 3 characters.
  - Endpoint group:
    Step 1: `GET /v1/alpha/{alphacode}`
  - Failure endpoint:
    `GET /v1/alpha/{alphacode}`
  - Why this fails:
    The implementation rejects invalid alpha-code length and returns `400 Bad Request`.
  - Intentionally violated constraints:
    `{alphacode}` is not a 2-character alpha-2 code or 3-character alpha-3 code.
- Branch 2:
  - Unsatisfied condition:
    No v1 country has a matching alpha-2 or alpha-3 code.
  - Endpoint group:
    Step 1: `GET /v1/alpha/{alphacode}`
  - Failure endpoint:
    `GET /v1/alpha/{alphacode}`
  - Why this fails:
    Lookup returns no country and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{alphacode}` has valid length but does not exist in the v1 dataset.

Endpoint coverage:
- Covers:
  `GET /v1/alpha/{alphacode}`
- Distinct meaning:
  Reads one v1 country by alpha-2 or alpha-3 code.

### Behavior 3: retrieve v1 countries by alpha-code list

Behavior name:
retrieve v1 countries by alpha-code list

Successful execution:
- Result:
  Returns v1 countries matching the semicolon-separated `codes` query.
- Endpoint sequence:
  Step 1: `GET /v1/alpha`
- Constraints:
  `codes` is required. For meaningful country results, each token should be an existing 2- or 3-character alpha code. Tokens are split by `;`; duplicate resolved countries are not repeated.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `codes` is missing, empty, shorter than 2 characters, or longer than 3 characters without containing `;`.
  - Endpoint group:
    Step 1: `GET /v1/alpha`
  - Failure endpoint:
    `GET /v1/alpha`
  - Why this fails:
    The endpoint validates the raw `codes` query and returns `400 Bad Request`.
  - Intentionally violated constraints:
    The required `codes` query is absent or not in accepted single-code/semicolon-list form.
- Branch 2:
  - Unsatisfied condition:
    `codes` contains only semicolon separators and no usable token.
  - Endpoint group:
    Step 1: `GET /v1/alpha`
  - Failure endpoint:
    `GET /v1/alpha`
  - Why this fails:
    The split list resolves to no countries and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `codes` does not provide any real alpha-code token.
- Branch 3:
  - Unsatisfied condition:
    A syntactically accepted code token does not match any country.
  - Endpoint group:
    Step 1: `GET /v1/alpha`
  - Failure endpoint:
    `GET /v1/alpha`
  - Why this fails:
    Implementation detail: the shared list lookup adds `null` for an unmatched token, so this may return a successful list containing `null` instead of `404`.
  - Intentionally violated constraints:
    At least one `codes` token is not an existing v1 alpha code.

Endpoint coverage:
- Covers:
  `GET /v1/alpha`
- Distinct meaning:
  Reads multiple v1 countries by semicolon-separated alpha codes.

### Behavior 4: search v1 countries by currency

Behavior name:
search v1 countries by currency

Successful execution:
- Result:
  Returns v1 countries whose stored currency string matches `{currency}` case-insensitively.
- Endpoint sequence:
  Step 1: `GET /v1/currency/{currency}`
- Constraints:
  `{currency}` must be exactly 3 characters and match a currency code in the v1 dataset.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{currency}` is not exactly 3 characters.
  - Endpoint group:
    Step 1: `GET /v1/currency/{currency}`
  - Failure endpoint:
    `GET /v1/currency/{currency}`
  - Why this fails:
    The endpoint returns `400 Bad Request`.
  - Intentionally violated constraints:
    `{currency}` is not a 3-character currency code.
- Branch 2:
  - Unsatisfied condition:
    No v1 country uses the currency.
  - Endpoint group:
    Step 1: `GET /v1/currency/{currency}`
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

### Behavior 5: search v1 countries by partial name

Behavior name:
search v1 countries by partial name

Successful execution:
- Result:
  Returns v1 countries whose name or alternative spelling contains `{name}`, case-insensitively and accent-insensitively.
- Endpoint sequence:
  Step 1: `GET /v1/name/{name}`
- Constraints:
  `fullText` must be omitted or set to `false`. `{name}` must be contained in at least one country name or alternative spelling.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v1 country name or alternative spelling contains `{name}`.
  - Endpoint group:
    Step 1: `GET /v1/name/{name}`
  - Failure endpoint:
    `GET /v1/name/{name}`
  - Why this fails:
    The substring search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{name}` is not a substring of any searchable v1 country name.

Endpoint coverage:
- Covers:
  `GET /v1/name/{name}`
- Distinct meaning:
  Partial-name search when `fullText` is omitted or `false`.

### Behavior 6: search v1 countries by exact name

Behavior name:
search v1 countries by exact name

Successful execution:
- Result:
  Returns v1 countries whose name or alternative spelling exactly equals `{name}`, case-insensitively and accent-insensitively.
- Endpoint sequence:
  Step 1: `GET /v1/name/{name}`
- Constraints:
  `fullText=true` is required. `{name}` must exactly match a country name or alternative spelling after normalization.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v1 country name or alternative spelling exactly matches `{name}`.
  - Endpoint group:
    Step 1: `GET /v1/name/{name}`
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

### Behavior 7: search v1 countries by calling code

Behavior name:
search v1 countries by calling code

Successful execution:
- Result:
  Returns v1 countries whose calling-code list contains `{callingcode}` exactly.
- Endpoint sequence:
  Step 1: `GET /v1/callingcode/{callingcode}`
- Constraints:
  `{callingcode}` must exactly equal a stored calling-code string.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v1 country has the calling code.
  - Endpoint group:
    Step 1: `GET /v1/callingcode/{callingcode}`
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

### Behavior 8: search v1 countries by capital

Behavior name:
search v1 countries by capital

Successful execution:
- Result:
  Returns v1 countries whose capital contains `{capital}`, case-insensitively and accent-insensitively.
- Endpoint sequence:
  Step 1: `GET /v1/capital/{capital}`
- Constraints:
  `{capital}` must be contained in at least one stored capital name after normalization.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v1 country capital contains `{capital}`.
  - Endpoint group:
    Step 1: `GET /v1/capital/{capital}`
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

### Behavior 9: search v1 countries by region

Behavior name:
search v1 countries by region

Successful execution:
- Result:
  Returns v1 countries whose region exactly matches `{region}` case-insensitively.
- Endpoint sequence:
  Step 1: `GET /v1/region/{region}`
- Constraints:
  `{region}` must equal a stored region value.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v1 country has the region.
  - Endpoint group:
    Step 1: `GET /v1/region/{region}`
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

### Behavior 10: search v1 countries by subregion

Behavior name:
search v1 countries by subregion

Successful execution:
- Result:
  Returns v1 countries whose subregion exactly matches `{subregion}` case-insensitively.
- Endpoint sequence:
  Step 1: `GET /v1/subregion/{subregion}`
- Constraints:
  `{subregion}` must equal a stored subregion value.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v1 country has the subregion.
  - Endpoint group:
    Step 1: `GET /v1/subregion/{subregion}`
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

### Behavior 11: search v1 countries by language

Behavior name:
search v1 countries by language

Successful execution:
- Result:
  Returns v1 countries whose stored language string equals `{lang}` case-insensitively.
- Endpoint sequence:
  Step 1: `GET /v1/lang/{lang}`
- Constraints:
  `{lang}` must equal a language value stored in the v1 country data.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v1 country has the language.
  - Endpoint group:
    Step 1: `GET /v1/lang/{lang}`
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
  Searches v1 countries by language code/string.

### Behavior 12: list v2 countries

Behavior name:
list v2 countries

Successful execution:
- Result:
  Returns every country record from the v2 dataset, optionally projected to selected fields.
- Endpoint sequence:
  Step 1: `GET /v2/all`
- Constraints:
  `fields` is optional. When present, it is a semicolon-separated inclusion list of exact v2 field names; omitted or empty `fields` returns full v2 country objects.

Failure or exceptional branches:
- None visible from request-controlled inputs for this documented endpoint.

Endpoint coverage:
- Covers:
  `GET /v2/all`
- Distinct meaning:
  Lists the complete v2 country collection with optional field projection.

### Behavior 13: retrieve v2 country by alpha code

Behavior name:
retrieve v2 country by alpha code

Successful execution:
- Result:
  Returns the single v2 country whose `alpha2Code` or `alpha3Code` matches `{alphacode}` case-insensitively, optionally projected to selected fields.
- Endpoint sequence:
  Step 1: `GET /v2/alpha/{alphacode}`
- Constraints:
  `{alphacode}` must be exactly 2 or 3 characters and match an existing country. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{alphacode}` is shorter than 2 characters or longer than 3 characters.
  - Endpoint group:
    Step 1: `GET /v2/alpha/{alphacode}`
  - Failure endpoint:
    `GET /v2/alpha/{alphacode}`
  - Why this fails:
    The endpoint returns `400 Bad Request`.
  - Intentionally violated constraints:
    `{alphacode}` is not a 2-character alpha-2 code or 3-character alpha-3 code.
- Branch 2:
  - Unsatisfied condition:
    No v2 country has a matching alpha-2 or alpha-3 code.
  - Endpoint group:
    Step 1: `GET /v2/alpha/{alphacode}`
  - Failure endpoint:
    `GET /v2/alpha/{alphacode}`
  - Why this fails:
    Lookup returns no country and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{alphacode}` has valid length but does not exist in the v2 dataset.

Endpoint coverage:
- Covers:
  `GET /v2/alpha/{alphacode}`
- Distinct meaning:
  Reads one v2 country by alpha-2 or alpha-3 code with optional field projection.

### Behavior 14: retrieve v2 countries by alpha-code list

Behavior name:
retrieve v2 countries by alpha-code list

Successful execution:
- Result:
  Returns v2 countries matching the semicolon-separated `codes` query, optionally projected to selected fields.
- Endpoint sequence:
  Step 1: `GET /v2/alpha`
- Constraints:
  `codes` is required. For meaningful country results, each token should be an existing 2- or 3-character alpha code. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `codes` is missing, empty, shorter than 2 characters, or longer than 3 characters without containing `;`.
  - Endpoint group:
    Step 1: `GET /v2/alpha`
  - Failure endpoint:
    `GET /v2/alpha`
  - Why this fails:
    The endpoint returns `400 Bad Request`.
  - Intentionally violated constraints:
    The required `codes` query is absent or malformed.
- Branch 2:
  - Unsatisfied condition:
    `codes` contains only semicolon separators and no usable token.
  - Endpoint group:
    Step 1: `GET /v2/alpha`
  - Failure endpoint:
    `GET /v2/alpha`
  - Why this fails:
    The split list resolves to no countries and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `codes` does not provide any real alpha-code token.
- Branch 3:
  - Unsatisfied condition:
    A syntactically accepted code token does not match any country and `fields` is omitted.
  - Endpoint group:
    Step 1: `GET /v2/alpha`
  - Failure endpoint:
    `GET /v2/alpha`
  - Why this fails:
    Implementation detail: the shared lookup adds `null` for the unmatched token, so this may return a successful list containing `null` instead of `404`.
  - Intentionally violated constraints:
    At least one `codes` token is not an existing v2 alpha code.
- Branch 4:
  - Unsatisfied condition:
    A syntactically accepted code token does not match any country and `fields` is present.
  - Endpoint group:
    Step 1: `GET /v2/alpha`
  - Failure endpoint:
    `GET /v2/alpha`
  - Why this fails:
    The field-projection code tries to treat the `null` list item as a JSON object and returns `500 Internal Server Error`.
  - Intentionally violated constraints:
    `codes` includes an unresolved alpha token while requesting projected fields.

Endpoint coverage:
- Covers:
  `GET /v2/alpha`
- Distinct meaning:
  Reads multiple v2 countries by semicolon-separated alpha codes with optional field projection.

### Behavior 15: search v2 countries by currency

Behavior name:
search v2 countries by currency

Successful execution:
- Result:
  Returns v2 countries whose currency object has a `code` matching `{currency}` case-insensitively.
- Endpoint sequence:
  Step 1: `GET /v2/currency/{currency}`
- Constraints:
  `{currency}` must be exactly 3 characters and match a stored currency `code`. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{currency}` is not exactly 3 characters.
  - Endpoint group:
    Step 1: `GET /v2/currency/{currency}`
  - Failure endpoint:
    `GET /v2/currency/{currency}`
  - Why this fails:
    The endpoint returns `400 Bad Request`.
  - Intentionally violated constraints:
    `{currency}` is not a 3-character currency code.
- Branch 2:
  - Unsatisfied condition:
    No v2 country uses the currency code.
  - Endpoint group:
    Step 1: `GET /v2/currency/{currency}`
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

### Behavior 16: search v2 countries by partial name

Behavior name:
search v2 countries by partial name

Successful execution:
- Result:
  Returns v2 countries whose name or alternative spelling contains `{name}`, case-insensitively and accent-insensitively.
- Endpoint sequence:
  Step 1: `GET /v2/name/{name}`
- Constraints:
  `fullText` must be omitted or set to `false`. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v2 country name or alternative spelling contains `{name}`.
  - Endpoint group:
    Step 1: `GET /v2/name/{name}`
  - Failure endpoint:
    `GET /v2/name/{name}`
  - Why this fails:
    The substring search result is empty and the endpoint returns `404 Not Found`.
  - Intentionally violated constraints:
    `{name}` is not a substring of any searchable v2 country name.

Endpoint coverage:
- Covers:
  `GET /v2/name/{name}`
- Distinct meaning:
  Partial-name search when `fullText` is omitted or `false`, with optional field projection.

### Behavior 17: search v2 countries by exact name

Behavior name:
search v2 countries by exact name

Successful execution:
- Result:
  Returns v2 countries whose name or alternative spelling exactly equals `{name}`, case-insensitively and accent-insensitively.
- Endpoint sequence:
  Step 1: `GET /v2/name/{name}`
- Constraints:
  `fullText=true` is required. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v2 country name or alternative spelling exactly matches `{name}`.
  - Endpoint group:
    Step 1: `GET /v2/name/{name}`
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

### Behavior 18: search v2 countries by calling code

Behavior name:
search v2 countries by calling code

Successful execution:
- Result:
  Returns v2 countries whose calling-code list contains `{callingcode}` exactly.
- Endpoint sequence:
  Step 1: `GET /v2/callingcode/{callingcode}`
- Constraints:
  `{callingcode}` must exactly equal a stored calling-code string. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v2 country has the calling code.
  - Endpoint group:
    Step 1: `GET /v2/callingcode/{callingcode}`
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

### Behavior 19: search v2 countries by capital

Behavior name:
search v2 countries by capital

Successful execution:
- Result:
  Returns v2 countries whose capital contains `{capital}`, case-insensitively and accent-insensitively.
- Endpoint sequence:
  Step 1: `GET /v2/capital/{capital}`
- Constraints:
  `{capital}` must be contained in at least one stored capital name after normalization. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v2 country capital contains `{capital}`.
  - Endpoint group:
    Step 1: `GET /v2/capital/{capital}`
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

### Behavior 20: search v2 countries by region

Behavior name:
search v2 countries by region

Successful execution:
- Result:
  Returns v2 countries whose region exactly matches `{region}` case-insensitively.
- Endpoint sequence:
  Step 1: `GET /v2/region/{region}`
- Constraints:
  `{region}` must equal a stored region value. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v2 country has the region.
  - Endpoint group:
    Step 1: `GET /v2/region/{region}`
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

### Behavior 21: search v2 countries by subregion

Behavior name:
search v2 countries by subregion

Successful execution:
- Result:
  Returns v2 countries whose subregion exactly matches `{subregion}` case-insensitively.
- Endpoint sequence:
  Step 1: `GET /v2/subregion/{subregion}`
- Constraints:
  `{subregion}` must equal a stored subregion value. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v2 country has the subregion.
  - Endpoint group:
    Step 1: `GET /v2/subregion/{subregion}`
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

### Behavior 22: search v2 countries by language

Behavior name:
search v2 countries by language

Successful execution:
- Result:
  Returns v2 countries whose language object matches `{lang}` by ISO 639-1 when `{lang}` has length 2, or by ISO 639-2 when `{lang}` has length 3.
- Endpoint sequence:
  Step 1: `GET /v2/lang/{lang}`
- Constraints:
  `{lang}` should be a 2-character `iso639_1` value or a 3-character `iso639_2` value. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v2 language matches `{lang}`, including unsupported lengths other than 2 or 3.
  - Endpoint group:
    Step 1: `GET /v2/lang/{lang}`
  - Failure endpoint:
    `GET /v2/lang/{lang}`
  - Why this fails:
    The search result is empty and the endpoint returns `404 Not Found`; invalid language-code length is not reported as `400`.
  - Intentionally violated constraints:
    `{lang}` is not a matching ISO 639-1 or ISO 639-2 value.

Endpoint coverage:
- Covers:
  `GET /v2/lang/{lang}`
- Distinct meaning:
  Searches v2 countries by language ISO code with optional field projection.

### Behavior 23: search v2 countries by demonym

Behavior name:
search v2 countries by demonym

Successful execution:
- Result:
  Returns v2 countries whose demonym exactly matches `{demonym}` case-insensitively after normalizing the input.
- Endpoint sequence:
  Step 1: `GET /v2/demonym/{demonym}`
- Constraints:
  `{demonym}` must equal a stored country demonym after input normalization. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v2 country has the demonym.
  - Endpoint group:
    Step 1: `GET /v2/demonym/{demonym}`
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

### Behavior 24: search v2 countries by regional bloc

Behavior name:
search v2 countries by regional bloc

Successful execution:
- Result:
  Returns v2 countries whose regional bloc acronym or `otherAcronyms` contains `{regionalbloc}`, case-insensitively.
- Endpoint sequence:
  Step 1: `GET /v2/regionalbloc/{regionalbloc}`
- Constraints:
  `{regionalbloc}` must match a regional bloc acronym or other acronym, not a bloc name. Optional `fields` keeps only requested exact v2 field names.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    No v2 country has a regional bloc acronym matching `{regionalbloc}`.
  - Endpoint group:
    Step 1: `GET /v2/regionalbloc/{regionalbloc}`
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

Unclear or auxiliary endpoints:
No endpoint in the OpenAPI file is unclear or uncovered. The implementation also registers undocumented routes such as root `GET /v1`, root `GET /v2`, `POST /v1`, `POST /v2`, and `POST /contribute`; they are source-visible but not listed as documented behaviors because they are absent from the OpenAPI file.