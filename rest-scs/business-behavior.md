# Domain-Level Behavior Analysis

## Domain Summary

`rest-scs` is a stateless REST wrapper around a set of small string, numeric, and classification algorithms from the String Case Study. The service does not manage persistent business resources, users, sessions, ownership links, generated identifiers, transactions, or database state. Its domain concepts are algorithmic decisions over request path values: calculator operations, cookie-shape classification, benchmark branch classification, date abbreviation scoring, file suffix classification, ordering classification, pattern search, regex classification, texting abbreviation translation, and title compatibility validation.

Every controller method is a `GET` endpoint under `/api` and delegates directly to a static implementation method. The OpenAPI file lists generic `401`, `403`, and `404` responses for each route, but the implementation has no authentication, authorization, or explicit not-found logic. After Spring path-variable binding succeeds, the controller returns `200 OK` with a string result.

## Available Function Inventory

### Calculator

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `return calculator constant` | `GET /api/calc/{op}/{arg1}/{arg2}` | Returns a mathematical constant for `pi` or `e`. |
| `compute unary calculator function` | `GET /api/calc/{op}/{arg1}/{arg2}` | Applies one unary math operation to `arg1`. |
| `compute binary calculator operation` | `GET /api/calc/{op}/{arg1}/{arg2}` | Applies one binary arithmetic operation to `arg1` and `arg2`. |
| `return default calculator result for unsupported operation` | `GET /api/calc/{op}/{arg1}/{arg2}` | Returns the calculator default result for an unsupported operation name. |

### Cookie Classification

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `recognize matching userid cookie` | `GET /api/cookie/{name}/{val}/{site}` | Recognizes a `userid` cookie whose value has the expected user prefix shape. |
| `recognize accepted session cookie` | `GET /api/cookie/{name}/{val}/{site}` | Recognizes the one accepted session-cookie and site combination. |
| `classify non-accepted session cookie` | `GET /api/cookie/{name}/{val}/{site}` | Distinguishes a session cookie that is present but not accepted for the configured site/value pair. |
| `classify unrecognized or nonmatching cookie` | `GET /api/cookie/{name}/{val}/{site}` | Returns the default cookie classification for unsupported names or malformed userid values. |

### Benchmark and Generic Input Classification

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `evaluate cost-functions benchmark` | `GET /api/costfuns/{i}/{s}` | Evaluates the cost-functions benchmark branch result. |
| `classify no-type-var input as high integer` | `GET /api/notypevar/{i}/{s}` | Classifies integer input greater than `5`. |
| `classify no-type-var input by string comparison` | `GET /api/notypevar/{i}/{s}` | Classifies low integer input whose string value compares greater than `hello`. |
| `return default no-type-var classification` | `GET /api/notypevar/{i}/{s}` | Returns the default no-type-var classification. |

### Date, File, and Ordering Classification

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `score day and month abbreviations` | `GET /api/dateparse/{dayname}/{monthname}` | Scores recognized day and month abbreviations. |
| `classify file suffix for a directory category` | `GET /api/filesuffix/{directory}/{file}` | Attempts to match a file suffix to an expected directory category. |
| `classify four strings as increasing` | `GET /api/ordered4/{w}/{x}/{z}/{y}` | Reports a four-string lexicographic increasing relationship. |
| `classify four strings as decreasing` | `GET /api/ordered4/{w}/{x}/{z}/{y}` | Reports a four-string lexicographic decreasing relationship. |
| `classify four strings as unordered` | `GET /api/ordered4/{w}/{x}/{z}/{y}` | Reports the default unordered result. |

### Pattern and Regex Classification

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `find direct pattern only` | `GET /api/pat/{txt}/{pat}` | Finds a direct pattern without a later reverse-pattern pair. |
| `find reverse pattern only` | `GET /api/pat/{txt}/{pat}` | Finds a reverse pattern without a later direct-pattern pair. |
| `find pattern and reverse pair` | `GET /api/pat/{txt}/{pat}` | Finds both a pattern and its reverse and returns the first match index. |
| `report no usable pattern match` | `GET /api/pat/{txt}/{pat}` | Reports no usable direct or reverse pattern match. |
| `classify text as URL-like string` | `GET /api/pat/{txt}` | Classifies a full text token as the implementation's URL-like format. |
| `classify text as date-like string` | `GET /api/pat/{txt}` | Classifies a full text token as the implementation's date-like format. |
| `classify text as floating-point exponent string` | `GET /api/pat/{txt}` | Classifies a full text token as a floating-point exponent string. |
| `classify text as no regex match` | `GET /api/pat/{txt}` | Reports that a text token matches none of the regex categories. |

### Text and Title Classification

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `translate common single word to texting abbreviation` | `GET /api/text2txt/{word1}/{word2}/{word3}` | Translates a supported first word to a texting abbreviation. |
| `translate see you phrase` | `GET /api/text2txt/{word1}/{word2}/{word3}` | Translates the phrase `see you` to `cu`. |
| `translate by the way phrase` | `GET /api/text2txt/{word1}/{word2}/{word3}` | Translates the phrase `by the way` to `btw`. |
| `return no texting abbreviation` | `GET /api/text2txt/{word1}/{word2}/{word3}` | Returns no abbreviation for unsupported words or phrases. |
| `validate male-compatible title` | `GET /api/title/{sex}/{title}` | Validates a male-compatible title pairing. |
| `validate female-compatible title` | `GET /api/title/{sex}/{title}` | Validates a female-compatible title pairing. |
| `validate neutral-compatible title` | `GET /api/title/{sex}/{title}` | Validates a title compatible with no specified sex. |
| `reject incompatible or unsupported title pairing` | `GET /api/title/{sex}/{title}` | Rejects unsupported sex/title combinations. |

## Supported Business Behaviors

### Behavior 1: Return A Mathematical Constant

Business goal:
Provide a named mathematical constant through the calculator API.

Domain context:
The calculator exposes constants as operations on the same endpoint used for numeric calculations. The two numeric arguments are part of the route contract even though the implementation ignores them for constants.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `return calculator constant` (`GET /api/calc/{op}/{arg1}/{arg2}`) with `op=pi` or `op=e`, `arg1=0.0`, and `arg2=0.0` to request the selected constant.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. There is no database, generated id, stored calculator profile, or prior setup function. Direct database setup is not relevant.

Parameter and value bindings:
- `op` selects the constant and is lowercased by the implementation.
- `arg1` and `arg2` must be numeric path values so Spring can bind them to `double`, but their values are not consumed by the constant branch.

Business result:
The response body is the string form of `Math.PI` for `op=pi` or `Math.E` for `op=e`. No service state changes.

Constraints and invariants:
- `op` is case-insensitive for supported constants.
- Both numeric path variables are required by the route and OpenAPI schema despite being ignored.
- No authentication or ownership rule is enforced.

Failure and exceptional cases:
- Failing function: `return calculator constant`
  - Failure condition: `arg1` or `arg2` is missing or not convertible to Java `double`.
  - Why it fails: Spring path-variable binding fails before `Calc.subject` runs.
  - Violated prerequisite or constraint: The route requires numeric `arg1` and `arg2`.
- Failing function: `return calculator constant`
  - Failure condition: `op` is neither `pi` nor `e`.
  - Why it fails: It does not fail as an HTTP error; the request is handled by `return default calculator result for unsupported operation`.
  - Violated prerequisite or constraint: The requested operation is outside the constant operation set.

Implementation notes:
OpenAPI lists generic `401`, `403`, and `404`, but source code exposes no auth or explicit not-found behavior for this route.

### Behavior 2: Execute A Unary Calculator Operation

Business goal:
Apply a supported one-argument mathematical function to a supplied numeric operand.

Domain context:
This is the calculator's scientific-function capability for square root, natural log, sine, cosine, and tangent.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `compute unary calculator function` (`GET /api/calc/{op}/{arg1}/{arg2}`) with `op=sqrt`, `op=log`, `op=sine`, `op=cosine`, or `op=tangent`, `arg1={operand}`, and `arg2=0.0` to calculate the unary result.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. There is no saved operand, generated operation id, or direct database setup alternative.

Parameter and value bindings:
- `arg1` is the operand consumed by the unary math function.
- `arg2` must still be a numeric path value but is ignored for unary operations.
- `op` is lowercased before dispatch.

Business result:
The response body is the string form of the Java `Math` result. No state is created or modified.

Constraints and invariants:
- Java math domain behavior is preserved: invalid mathematical domains can produce `NaN` or `Infinity` as a successful string result rather than an HTTP error.
- Only the listed operation names select this behavior.

Failure and exceptional cases:
- Failing function: `compute unary calculator function`
  - Failure condition: `arg1` or `arg2` is missing or not convertible to Java `double`.
  - Why it fails: Spring binding rejects non-numeric path values before the implementation runs.
  - Violated prerequisite or constraint: Numeric path-variable binding for both arguments.
- Failing function: `compute unary calculator function`
  - Failure condition: `op` is unsupported.
  - Why it fails: It does not fail as an HTTP error; the implementation returns the default calculator result through `return default calculator result for unsupported operation`.
  - Violated prerequisite or constraint: Supported unary operation name.

Implementation notes:
The endpoint has no separate error model for mathematical invalidity; the Java double result is serialized directly.

### Behavior 3: Execute A Binary Arithmetic Operation

Business goal:
Apply a supported two-argument arithmetic operation to supplied numeric operands.

Domain context:
This is the calculator's arithmetic capability for addition, subtraction, multiplication, and division.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `compute binary calculator operation` (`GET /api/calc/{op}/{arg1}/{arg2}`) with `op=plus`, `op=subtract`, `op=multiply`, or `op=divide`, `arg1={leftOperand}`, and `arg2={rightOperand}` to calculate the arithmetic result.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. There are no stored operands, result resources, or database records to pre-create.

Parameter and value bindings:
- `arg1` and `arg2` are both consumed by the selected binary operation.
- `op` binds the request to the arithmetic branch after lowercasing.

Business result:
The response body is the string form of the computed Java double result. No persistent result is stored.

Constraints and invariants:
- Division by zero is not rejected by domain validation; Java double arithmetic returns values such as `Infinity` or `NaN`.
- The operation set is fixed in source code and cannot be extended through the API.

Failure and exceptional cases:
- Failing function: `compute binary calculator operation`
  - Failure condition: `arg1` or `arg2` is missing or not convertible to Java `double`.
  - Why it fails: Spring binding fails before `Calc.subject` is invoked.
  - Violated prerequisite or constraint: Both operands must be numeric path variables.
- Failing function: `compute binary calculator operation`
  - Failure condition: `op` is unsupported.
  - Why it fails: It does not fail; it is handled by `return default calculator result for unsupported operation`.
  - Violated prerequisite or constraint: Supported binary operation name.

Implementation notes:
The calculator never returns a structured error for invalid operations; unsupported operation names are intentionally collapsed to `0.0`.

### Behavior 4: Handle An Unsupported Calculator Operation

Business goal:
Return a deterministic default result when a caller supplies an unknown calculator operation.

Domain context:
The calculator exposes a fallback branch rather than rejecting unsupported operation names.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `return default calculator result for unsupported operation` (`GET /api/calc/{op}/{arg1}/{arg2}`) with `op={unsupportedOperation}`, `arg1=1.0`, and `arg2=2.0` where `op` is not `pi`, `e`, `sqrt`, `log`, `sine`, `cosine`, `tangent`, `plus`, `subtract`, `multiply`, or `divide`.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. This behavior is fully determined by the request path values.

Parameter and value bindings:
- `op` must intentionally differ from every supported operation after lowercasing.
- `arg1` and `arg2` are required for binding but do not change the fallback result.

Business result:
The response body is `0.0`. No operation definition is created, and no error state is recorded.

Constraints and invariants:
- Unsupported operations are treated as successful requests.
- The endpoint does not tell the caller whether `0.0` is a real arithmetic result or a fallback result except by the caller knowing the operation name.

Failure and exceptional cases:
- Failing function: `return default calculator result for unsupported operation`
  - Failure condition: `arg1` or `arg2` is non-numeric or missing.
  - Why it fails: Spring cannot bind the required double path variables.
  - Violated prerequisite or constraint: Numeric path-variable binding for the route.

Implementation notes:
This is an implementation-backed discrepancy from a stricter calculator domain: unsupported operations should normally fail validation, but here they return `200 OK` with `0.0`.

### Behavior 5: Classify Cookie-Like Identity Or Session Values

Business goal:
Classify path-supplied cookie attributes as a recognized userid cookie, accepted session cookie, non-accepted session cookie, or unrecognized cookie.

Domain context:
The service does not read HTTP `Cookie` headers or create sessions. It classifies three path strings that represent cookie name, value, and site.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `recognize matching userid cookie` (`GET /api/cookie/{name}/{val}/{site}`) with `name=userid`, `val=user123` or another value longer than six characters beginning with `user`, and `site={anySite}` to classify a matching userid cookie.
2. Alternatively, use function `recognize accepted session cookie` (`GET /api/cookie/{name}/{val}/{site}`) with `name=session`, `val=am`, and `site=abc.com` to classify the accepted session combination.
3. Alternatively, use function `classify non-accepted session cookie` (`GET /api/cookie/{name}/{val}/{site}`) with `name=session` and either `val` not equal to `am` or `site` not equal to `abc.com` to classify a rejected session combination.
4. Alternatively, use function `classify unrecognized or nonmatching cookie` (`GET /api/cookie/{name}/{val}/{site}`) with `name={unsupportedName}` or with `name=userid` and a value that is too short or does not begin with `user` to obtain the default cookie classification.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. There is no session store, user table, ownership record, generated token, or direct database setup path. The apparent identity/session state exists only as path values supplied in the request.

Parameter and value bindings:
- `name`, `val`, and `site` are lowercased and then consumed together by the classifier.
- For the userid branch, `site` is intentionally ignored; the domain meaning is only the shape of `name` and `val`.
- For the accepted session branch, `name`, `val`, and `site` must all bind to the accepted tuple.

Business result:
The response body is `1` for recognized userid and accepted session values, `2` for non-accepted session values, or `0` for unrecognized/nonmatching cookie values. No session, login state, or user relationship is created.

Constraints and invariants:
- Comparisons are case-insensitive because all values are lowercased.
- `userid` values require length greater than six and prefix `user`.
- Only the exact lowercased session tuple `session/am/abc.com` is accepted.
- No real HTTP cookie header, authentication token, expiration, or site ownership rule is enforced.

Failure and exceptional cases:
- Failing function: `recognize matching userid cookie`
  - Failure condition: `name=userid` but `val` has length six or less, or does not start with `user`.
  - Why it fails: The implementation leaves the result at the default `0`.
  - Violated prerequisite or constraint: Userid cookie shape requirement.
- Failing function: `recognize accepted session cookie`
  - Failure condition: `name=session` with any `val/site` pair other than `am/abc.com`.
  - Why it fails: The implementation routes the request to the non-accepted session branch and returns `2`.
  - Violated prerequisite or constraint: Accepted session tuple requirement.
- Failing function: `classify non-accepted session cookie`
  - Failure condition: `name` is not `session`.
  - Why it fails: The session-specific branch is skipped and the default cookie classifier applies.
  - Violated prerequisite or constraint: Session cookie name requirement.

Implementation notes:
The OpenAPI path parameters model cookie attributes as path strings, not headers. This is a classifier, not an authentication or session-management API.

### Behavior 6: Evaluate The Cost-Functions Benchmark

Business goal:
Expose the benchmark branch result for an integer and string input pair.

Domain context:
The cost-functions endpoint is a test benchmark rather than a business resource workflow. It demonstrates branch-cost comparisons over numeric and string values.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `evaluate cost-functions benchmark` (`GET /api/costfuns/{i}/{s}`) with `i={integer}` and `s={pathString}` to compute the benchmark result.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. No benchmark run is stored, and no direct database fixture can replace the request values.

Parameter and value bindings:
- `i` must bind to Java `Integer`.
- `s` is consumed by string equality and lexicographic comparisons, but normal request strings ultimately reach the final reference-inequality assignment.

Business result:
For normal non-null path strings, the response body is `10`. No benchmark history is persisted.

Constraints and invariants:
- `i` is path-bound as an integer.
- The final comparison uses Java reference inequality, `s != s2 + s2`, so it overwrites earlier branch results for ordinary requests.

Failure and exceptional cases:
- Failing function: `evaluate cost-functions benchmark`
  - Failure condition: `i` is missing or not convertible to Java `Integer`.
  - Why it fails: Spring path-variable binding fails before `Costfuns.subject` is called.
  - Violated prerequisite or constraint: Integer binding for `i`.

Implementation notes:
Several earlier source branches can assign values from `1` through `9`, but the final reference comparison normally overwrites them with `10`. This source behavior is more precise than the OpenAPI summary.

### Behavior 7: Score Day And Month Abbreviations

Business goal:
Convert a weekday abbreviation and month abbreviation into a numeric recognition score.

Domain context:
This endpoint is a lightweight date-token scorer. It does not validate full dates, day/month consistency, or calendar ranges.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `score day and month abbreviations` (`GET /api/dateparse/{dayname}/{monthname}`) with `dayname={weekdayAbbreviation}` and `monthname={monthAbbreviation}` to compute the score.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. The result is computed only from the two path values.

Parameter and value bindings:
- `dayname` contributes `1` if it lowercases to `mon`, `tue`, `wed`, `thur`, `fri`, `sat`, or `sun`.
- `monthname` contributes the month number if it lowercases to `jan` through `dec`.

Business result:
The response body is the numeric score as a string: recognized weekday contribution plus recognized month contribution. Unrecognized values contribute `0`; no state changes.

Constraints and invariants:
- Values are lowercased before comparison.
- The score is additive, so `mon/jan` and an unrecognized day with `feb` can both produce `2`, making the result non-unique as a semantic date classification.

Failure and exceptional cases:
- Failing function: `score day and month abbreviations`
  - Failure condition: `dayname` or `monthname` is unrecognized.
  - Why it fails: It does not fail as an HTTP error; the unrecognized part contributes `0`.
  - Violated prerequisite or constraint: Recognized abbreviation list.

Implementation notes:
The service treats invalid abbreviation inputs as successful classifications with lower scores.

### Behavior 8: Classify File Suffix Against Directory Category

Business goal:
Determine whether a file name has the suffix expected for a directory category.

Domain context:
The intended domain mapping is `text/txt`, `acrobat/pdf`, `word/doc`, `bin/exe`, and `lib/dll`.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `classify file suffix for a directory category` (`GET /api/filesuffix/{directory}/{file}`) with `directory=text` and `file={fileName}` to attempt text-file classification, or with `directory=acrobat`, `word`, `bin`, or `lib` and corresponding file names to attempt those classifications.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. No file metadata, directory registry, or database fixture is available; the apparent directory and file state exists only in the path values.

Parameter and value bindings:
- `directory` selects the expected suffix family.
- `file` is split by the implementation to derive a suffix, and that derived suffix is compared to the selected directory's expected suffix.

Business result:
The intended result codes are `1` for `text/txt`, `2` for `acrobat/pdf`, `3` for `word/doc`, `4` for `bin/exe`, `5` for `lib/dll`, and `0` for no match. With ordinary dotted file names, the implementation usually returns `0` because it calls `file.split(".")`, where `.` is a regex wildcard.

Constraints and invariants:
- The implementation does not lowercase `directory` or `file`, so matching is case-sensitive.
- The implementation's suffix extraction is unreliable for ordinary file names.
- No actual filesystem directory or file existence is checked.

Failure and exceptional cases:
- Failing function: `classify file suffix for a directory category`
  - Failure condition: A normal filename such as `report.txt` is supplied for `directory=text`.
  - Why it fails: Java `String.split(".")` treats `.` as a regex wildcard, not a literal dot, so suffix extraction does not behave like normal file-extension parsing.
  - Violated prerequisite or constraint: Intended literal suffix extraction.
- Failing function: `classify file suffix for a directory category`
  - Failure condition: `directory` casing differs from the hard-coded lowercase category.
  - Why it fails: The implementation compares directory strings without lowercasing.
  - Violated prerequisite or constraint: Exact category string match.

Implementation notes:
This is an implementation/OpenAPI meaning gap: the route appears to classify file suffixes, but source behavior undermines ordinary dotted filename classification.

### Behavior 9: Classify Generic Integer/String Benchmark Inputs

Business goal:
Classify a pair of generic inputs according to the no-type-var benchmark rules.

Domain context:
This endpoint demonstrates interactions between integer assignment, string comparison, and later branch overwrites.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `classify no-type-var input as high integer` (`GET /api/notypevar/{i}/{s}`) with `i=6` or any integer greater than `5` and `s={anyString}` to classify high integer input.
2. Alternatively, use function `classify no-type-var input by string comparison` (`GET /api/notypevar/{i}/{s}`) with `i=5` or any integer less than or equal to `5`, and `s` lexicographically greater than `hello`, to classify by string comparison.
3. Alternatively, use function `return default no-type-var classification` (`GET /api/notypevar/{i}/{s}`) with `i=5` or lower and `s` lexicographically less than or equal to `hello` to obtain the default result.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. The classification has no stored input profile, run id, or database setup alternative.

Parameter and value bindings:
- `i` binds to Java `Integer` and is assigned through local variables `x` and `y`.
- `s` is compared against the literal `hello`.
- High integer classification intentionally overrides earlier possible assignments when `i > 5`.

Business result:
The response body is `3` for high integer input, `2` for low integer input with `s > hello`, or `0` for the default branch. No state changes.

Constraints and invariants:
- `i` must be an integer.
- Branches for internal values such as `i == 7` or `i == 28` are not visible in final output because the later `y > 5` branch overwrites them with `3`.

Failure and exceptional cases:
- Failing function: `classify no-type-var input as high integer`
  - Failure condition: `i` is missing or not convertible to Java `Integer`.
  - Why it fails: Spring binding fails before `NotyPevar.subject` runs.
  - Violated prerequisite or constraint: Integer path-variable binding.
- Failing function: `classify no-type-var input by string comparison`
  - Failure condition: `i > 5`.
  - Why it fails: The later high-integer branch overwrites the string-comparison result with `3`.
  - Violated prerequisite or constraint: String-comparison classification requires `i <= 5`.
- Failing function: `return default no-type-var classification`
  - Failure condition: `s` is lexicographically greater than `hello` while `i <= 5`, or `i > 5`.
  - Why it fails: The implementation selects the string-comparison branch or high-integer branch instead.
  - Violated prerequisite or constraint: Default classification requires neither visible non-default branch to apply.

Implementation notes:
The endpoint name suggests untyped variables, but the REST controller enforces integer binding for `i`.

### Behavior 10: Classify Four Strings By Lexicographic Ordering

Business goal:
Determine whether four supplied strings form one of the supported increasing or decreasing order patterns, or neither.

Domain context:
This endpoint is an ordering classifier with a length gate that keeps all four values within five or six characters before order comparisons are considered.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `classify four strings as increasing` (`GET /api/ordered4/{w}/{x}/{z}/{y}`) with all four values length five or six and values satisfying `z > y > x > w` lexicographically to obtain `increasing`.
2. Alternatively, use function `classify four strings as decreasing` (`GET /api/ordered4/{w}/{x}/{z}/{y}`) with all four values length five or six and values satisfying `w > x > y > z` lexicographically to obtain `decreasing`.
3. Alternatively, use function `classify four strings as unordered` (`GET /api/ordered4/{w}/{x}/{z}/{y}`) with any value outside the length gate or values that satisfy neither ordering predicate to obtain `unordered`.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. There is no persisted sequence or reusable ordering id.

Parameter and value bindings:
- The route order is `w`, `x`, `z`, `y`.
- For increasing classification, the comparison order is `z`, then `y`, then `x`, then `w`, which differs from route order.
- For decreasing classification, the comparison order is `w`, then `x`, then `y`, then `z`.

Business result:
The response body is `increasing`, `decreasing`, or `unordered`. No ordering record is stored.

Constraints and invariants:
- Every string must have length between five and six inclusive before increasing or decreasing can be returned.
- The implementation is case-sensitive and uses Java `String.compareTo`.
- `unordered` is the initialized default result.

Failure and exceptional cases:
- Failing function: `classify four strings as increasing`
  - Failure condition: Any value length is outside five through six, or the values do not satisfy `z > y > x > w`.
  - Why it fails: The length gate or comparison predicate prevents the increasing assignment.
  - Violated prerequisite or constraint: Increasing order and length constraints.
- Failing function: `classify four strings as decreasing`
  - Failure condition: Any value length is outside five through six, or the values do not satisfy `w > x > y > z`.
  - Why it fails: The length gate or comparison predicate prevents the decreasing assignment.
  - Violated prerequisite or constraint: Decreasing order and length constraints.

Implementation notes:
The OpenAPI parameter listing orders `y` before `z`, while the path and controller route are `/api/ordered4/{w}/{x}/{z}/{y}`. The source route order is authoritative.

### Behavior 11: Search Text For A Pattern And Its Reverse

Business goal:
Classify whether a text contains a direct pattern, the reverse of that pattern, both in sequence, or no usable pattern.

Domain context:
The pattern-search endpoint is a string inspection capability. It computes the reverse of the requested pattern and searches for direct/reverse occurrences within the supplied text.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `find direct pattern only` (`GET /api/pat/{txt}/{pat}`) with `pat={patternLongerThanTwo}` and `txt` containing `pat` without a later matching reverse occurrence to return `1`.
2. Alternatively, use function `find reverse pattern only` (`GET /api/pat/{txt}/{pat}`) with `pat={patternLongerThanTwo}` and `txt` containing the reverse of `pat` without a later direct occurrence to return `2`.
3. Alternatively, use function `find pattern and reverse pair` (`GET /api/pat/{txt}/{pat}`) with `pat={patternLongerThanTwo}` and `txt` containing `pat` followed by its reverse, or the reverse followed by `pat`, to return the starting index of the first matched segment.
4. Alternatively, use function `report no usable pattern match` (`GET /api/pat/{txt}/{pat}`) with `pat` length two or less, or with no direct or reverse occurrence in `txt`, to return `0`.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. There is no stored text corpus, saved pattern, cursor, or generated match id. The text and pattern must be supplied as path values for each request.

Parameter and value bindings:
- `pat` is reused internally to compute `patrev`, the reverse string.
- `txt` is scanned for `pat` and `patrev`.
- The same `pat` value determines the minimum length gate and both search targets.

Business result:
The response body is `1`, `2`, `0`, or a numeric starting index for paired direct/reverse matches. No match list, cursor, or search history is stored.

Constraints and invariants:
- `pat` must have length greater than two for any search to occur.
- Matching is case-sensitive.
- The implementation returns an index for pair cases, not the comment-described codes `3`, `4`, or `5`.
- Because `txt` and `pat` are path segments, text containing unescaped slashes is not naturally supported by this route.

Failure and exceptional cases:
- Failing function: `find direct pattern only`
  - Failure condition: `pat` length is two or less, `txt` does not contain `pat`, or a later reverse match causes the pair branch to return an index.
  - Why it fails: The implementation either does not search, leaves result at default, or returns early for a pair.
  - Violated prerequisite or constraint: Direct-only match requirement.
- Failing function: `find reverse pattern only`
  - Failure condition: `pat` length is two or less, `txt` does not contain the reverse of `pat`, or a later direct match causes the pair branch to return an index.
  - Why it fails: The reverse-only result remains visible only without a later direct pair.
  - Violated prerequisite or constraint: Reverse-only match requirement.
- Failing function: `find pattern and reverse pair`
  - Failure condition: `txt` does not contain both a direct and reverse occurrence in the required order.
  - Why it fails: The nested scan never reaches the early return that emits the starting index.
  - Violated prerequisite or constraint: Paired direct/reverse occurrence requirement.

Implementation notes:
Source comments describe separate codes for pair and palindrome cases, but the actual Java implementation returns the starting index string for both adjacent and non-adjacent pair cases.

### Behavior 12: Classify A Text Token By Regex Category

Business goal:
Classify a single text token as URL-like, date-like, floating-point-exponent-like, or no regex match.

Domain context:
This endpoint is a full-token classifier. It uses regular expressions, not substring search.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `classify text as URL-like string` (`GET /api/pat/{txt}`) with `txt={protocol}//:{identifier}/{identifier}` where the protocol is `http`, `ftp`, `afs`, or `gopher`, to classify a URL-like token.
2. Alternatively, use function `classify text as date-like string` (`GET /api/pat/{txt}`) with `txt={day}{digit}{digit}{month}`, such as `mon12jan`, to classify a date-like token.
3. Alternatively, use function `classify text as floating-point exponent string` (`GET /api/pat/{txt}`) with `txt={digits}.{digits}e+{twoDigits}` or `txt={digits}.{digits}e-{twoDigits}` to classify an exponent-form number.
4. Alternatively, use function `classify text as no regex match` (`GET /api/pat/{txt}`) with a token that matches none of the URL, date, or exponent regexes to return `none`.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. No regex profile, classifier rule, or prior token resource exists.

Parameter and value bindings:
- `txt` is the sole classification input and is consumed by the URL regex first, then date regex, then floating-point exponent regex.
- The same `txt` value cannot be normalized or lowercased by a setup function; it must already match the expected case and shape.

Business result:
The response body is `url`, `date`, `fpe`, or `none`. No classification record is persisted.

Constraints and invariants:
- Matching uses `Pattern.matches`, so the whole text must match.
- Date and protocol matching are lowercase-only because the implementation does not lowercase `txt`.
- The URL-like format expects the unusual literal sequence `//:` before the host-like identifier.
- `GET /api/pat/{txt}` shares the `/api/pat` route prefix with the pattern-search endpoint, but it is a separate one-segment route.

Failure and exceptional cases:
- Failing function: `classify text as URL-like string`
  - Failure condition: The token uses a normal URL form such as `http://host/path`.
  - Why it fails: The regex expects `http//:host/path`, not the conventional `://` sequence.
  - Violated prerequisite or constraint: Implementation-specific URL-like regex.
- Failing function: `classify text as date-like string`
  - Failure condition: The token uses uppercase abbreviations, unsupported day/month abbreviations, or a separator.
  - Why it fails: The regex is lowercase and exact.
  - Violated prerequisite or constraint: Exact date-like token regex.
- Failing function: `classify text as floating-point exponent string`
  - Failure condition: The token omits the sign, uses uppercase `E`, or has other than two exponent digits.
  - Why it fails: The regex requires lowercase `e`, a `+` or `-`, and exactly two trailing digits.
  - Violated prerequisite or constraint: Exact floating-point exponent regex.

Implementation notes:
The controller method for `/api/pat/{txt}` is named `regex`, but the path prefix overlaps with pattern search. Source behavior takes priority over route naming ambiguity.

### Behavior 13: Translate Words Or Phrases To Texting Abbreviations

Business goal:
Convert selected English words or short phrases into mobile texting abbreviations.

Domain context:
The translator consumes three path words and either returns a recognized abbreviation or an empty string.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `translate common single word to texting abbreviation` (`GET /api/text2txt/{word1}/{word2}/{word3}`) with `word1=two`, `for`, `four`, `you`, `and`, or `are`, and any `word2` and `word3`, to translate a supported single word.
2. Alternatively, use function `translate see you phrase` (`GET /api/text2txt/{word1}/{word2}/{word3}`) with `word1=see`, `word2=you`, and `word3={anyWord}` to translate `see you`.
3. Alternatively, use function `translate by the way phrase` (`GET /api/text2txt/{word1}/{word2}/{word3}`) with `word1=by`, `word2=the`, and `word3=way` to translate `by the way`.
4. Alternatively, use function `return no texting abbreviation` (`GET /api/text2txt/{word1}/{word2}/{word3}`) with words that match none of the supported single-word or phrase rules to return no abbreviation.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. The abbreviation dictionary is hard-coded, and there is no API or database setup path for adding entries.

Parameter and value bindings:
- `word1`, `word2`, and `word3` are all lowercased.
- Single-word translations consume only `word1`; `word2` and `word3` are required path values but are ignored by those branches.
- The phrase translations bind `word1` and `word2`, or all three words, to the fixed phrase.

Business result:
The response body is one of `2`, `4`, `u`, `n`, `r`, `cu`, `btw`, or the empty string. No translation memory or user vocabulary is stored.

Constraints and invariants:
- The endpoint requires exactly three path words even for one-word and two-word translations.
- The `are` branch is followed by `else if` phrase checks, so `word1=are` returns `r` and prevents phrase matching.
- No spaces, punctuation, sentence-level translation, or multi-token output are supported.

Failure and exceptional cases:
- Failing function: `translate common single word to texting abbreviation`
  - Failure condition: The intended word appears in `word2` or `word3` rather than `word1`.
  - Why it fails: Single-word translation only checks `word1`.
  - Violated prerequisite or constraint: Supported single word must be the first path word.
- Failing function: `translate see you phrase`
  - Failure condition: `word1` is not `see` or `word2` is not `you`.
  - Why it fails: The phrase predicate is exact after lowercasing.
  - Violated prerequisite or constraint: Fixed phrase order.
- Failing function: `translate by the way phrase`
  - Failure condition: Any of the three words differs from `by/the/way`.
  - Why it fails: All three phrase positions must match.
  - Violated prerequisite or constraint: Fixed three-word phrase.

Implementation notes:
The translator returns an empty successful response for no match, which can be ambiguous to clients that do not distinguish empty translations from transport issues.

### Behavior 14: Validate Title Compatibility With Stated Sex

Business goal:
Determine whether a personal title is compatible with a stated sex category.

Domain context:
The title endpoint is a compatibility validator over a small fixed catalog of titles for `male`, `female`, and `none`.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `validate male-compatible title` (`GET /api/title/{sex}/{title}`) with `sex=male` and `title=mr`, `dr`, `sir`, `rev`, `rthon`, or `prof` to validate a male-compatible title.
2. Alternatively, use function `validate female-compatible title` (`GET /api/title/{sex}/{title}`) with `sex=female` and `title=mrs`, `miss`, `ms`, `dr`, `lady`, `rev`, `rthon`, or `prof` to validate a female-compatible title.
3. Alternatively, use function `validate neutral-compatible title` (`GET /api/title/{sex}/{title}`) with `sex=none` and `title=dr`, `rev`, `rthon`, or `prof` to validate a neutral-compatible title.
4. Alternatively, use function `reject incompatible or unsupported title pairing` (`GET /api/title/{sex}/{title}`) with an unsupported `sex` or incompatible `title` to reject the pairing.

Optional verification workflow:
None.

Existing-state shortcuts:
- None. There is no person profile, account record, title catalog endpoint, or direct database setup path.

Parameter and value bindings:
- `sex` selects the title compatibility list after lowercasing.
- `title` is lowercased and matched within the selected list.
- Shared titles such as `dr`, `rev`, `rthon`, and `prof` intentionally bind to more than one sex category but produce different result codes depending on `sex`.

Business result:
The response body is `1` for male-compatible, `0` for female-compatible, `2` for neutral-compatible, or `-1` for incompatible or unsupported pairings. No person record is updated.

Constraints and invariants:
- Only `male`, `female`, and `none` are recognized sex categories.
- Title matching is against fixed hard-coded lists.
- There is no independent endpoint to retrieve the supported title catalog.

Failure and exceptional cases:
- Failing function: `validate male-compatible title`
  - Failure condition: `sex` is not `male` or `title` is not in the male list.
  - Why it fails: The male branch is skipped or leaves the default rejection result.
  - Violated prerequisite or constraint: Male-compatible title pairing.
- Failing function: `validate female-compatible title`
  - Failure condition: `sex` is not `female` or `title` is not in the female list.
  - Why it fails: The female branch is skipped or leaves the default rejection result.
  - Violated prerequisite or constraint: Female-compatible title pairing.
- Failing function: `validate neutral-compatible title`
  - Failure condition: `sex` is not `none` or `title` is not in the neutral list.
  - Why it fails: The neutral branch is skipped or leaves the default rejection result.
  - Violated prerequisite or constraint: Neutral-compatible title pairing.

Implementation notes:
The numeric return codes are not intuitive as validity flags because the valid branches return three different values and female-compatible validation returns `0`, while invalid returns `-1`.

### Behavior 15: Cross-Check A Date Token Across Scoring And Regex Classification

Business goal:
Confirm a day/month abbreviation pair both contributes to a date score and can be embedded into the regex endpoint's date-like token shape.

Domain context:
The service exposes two independent date-related capabilities: `dateparse` scores separate day/month abbreviations, while `regex` classifies a single compact text token. Used together, they provide a client-driven consistency check for supported lowercase date abbreviations.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `score day and month abbreviations` (`GET /api/dateparse/{dayname}/{monthname}`) with `dayname=mon` and `monthname=jan` to score recognized abbreviations.
2. Use function `classify text as date-like string` (`GET /api/pat/{txt}`) with `txt=mon12jan`, reusing the same `dayname=mon` and `monthname=jan` values around two digits, to classify the compact token as date-like.

Optional verification workflow:
None.

Existing-state shortcuts:
- If the caller already knows the chosen `dayname` and `monthname` are supported by both functions, step 1 can be skipped and `classify text as date-like string` can be invoked directly.
- Direct database setup is not relevant; the value-reuse requirement must still hold: the `txt` token must contain the same supported day and month abbreviations in lowercase.

Parameter and value bindings:
- `dayname` from step 1 is reused as the leading day abbreviation inside `txt` in step 2.
- `monthname` from step 1 is reused as the trailing month abbreviation inside `txt` in step 2.
- The two inserted middle characters in `txt` must be digits; they have no equivalent value in `score day and month abbreviations`.

Business result:
The caller obtains a date score from the separated abbreviation endpoint and a `date` classification for the compact token. No server-side relationship is stored between the two results.

Constraints and invariants:
- `score day and month abbreviations` lowercases inputs; `classify text as date-like string` does not. The reused values must therefore be lowercase for the regex step.
- The score does not prove the compact token is a valid calendar date; it only reflects recognized abbreviations.
- The regex step requires exactly two digits between day and month.

Failure and exceptional cases:
- Failing function: `score day and month abbreviations`
  - Failure condition: `dayname` or `monthname` is not in the recognized abbreviation list.
  - Why it fails: It does not return an HTTP error, but the unrecognized component contributes `0`, weakening the cross-check.
  - Violated prerequisite or constraint: Supported abbreviation reuse.
- Failing function: `classify text as date-like string`
  - Failure condition: `txt` does not reuse lowercase supported abbreviations, does not contain exactly two digits between them, or contains separators.
  - Why it fails: The full-string date regex does not match and the endpoint may return `none`.
  - Violated prerequisite or constraint: Compact date-token shape.

Implementation notes:
This is a client-composed workflow. The service does not expose a single endpoint that binds the two date interpretations or guarantees their consistency.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Real Cookie-Based Authentication Or Session Management

Priority:
Critical domain gap

Expected business goal:
Use cookie values to authenticate a user, establish a session, bind the session to a site, and protect subsequent API calls.

Why it is unsupported:
The cookie endpoint only classifies path strings. No endpoint reads HTTP `Cookie` headers, creates sessions, issues tokens, stores users, expires sessions, or authorizes later requests.

Existing functions considered:
- `recognize matching userid cookie`: recognizes a userid-shaped path value, but does not verify a real user or create identity state.
- `recognize accepted session cookie`: recognizes one hard-coded `session/am/abc.com` tuple, but does not establish an authenticated session.
- `classify non-accepted session cookie`: returns a visible classification for rejected session-like values, but does not revoke or audit anything.
- `classify unrecognized or nonmatching cookie`: gives a default result, but not an authentication failure model.

Missing capability:
Missing HTTP cookie/header handling, login/session creation, token generation, session persistence, expiry, user ownership checks, and authorization enforcement on other endpoints.

Proof that function composition is insufficient:
Chaining cookie classification before another endpoint cannot protect that endpoint because the later endpoints do not consume any token, session id, user id, header, or prior classification result. No state can be created or preserved between calls, and no ownership or authentication condition can be checked.

Evidence from existing functions/source:
- `ScsRest.cookie` accepts `name`, `val`, and `site` as path variables and returns `Cookie.subject(...)`.
- All controller methods return `ResponseEntity.ok(...)`; no method checks authentication.
- OpenAPI lists `401` and `403`, but the source contains no corresponding enforcement.

Business impact:
Any client treating this service as a real cookie/session service would get a false sense of security. Identity and authorization workflows are completely unsupported.

### Missing Behavior 2: Persistent Resource Lifecycle Or Stored Analysis Results

Priority:
Critical domain gap

Expected business goal:
Create, retrieve, update, list, or delete named analysis jobs, inputs, outputs, user profiles, files, dictionaries, title catalogs, or calculation results.

Why it is unsupported:
Every available function is stateless and returns a computed string from path variables. There are no `POST`, `PUT`, `PATCH`, `DELETE`, collection `GET`, database model, repository, generated id, or stored output.

Existing functions considered:
- `compute binary calculator operation`: computes a result but does not store it.
- `classify text as URL-like string`: classifies one token but does not create a classification record.
- `translate common single word to texting abbreviation`: translates a path value but does not update a dictionary or translation memory.
- `validate male-compatible title`: validates a pair but does not update a person profile.

Missing capability:
Missing persistence model, generated identifiers, create/read/update/delete endpoints, list/search endpoints, and ownership/scoping fields.

Proof that function composition is insufficient:
Repeated calls can recompute outputs but cannot create a server-side resource that a later call can retrieve, update, delete, list, or own. There is no response field such as id, location, cursor, ETag, or token that can be captured and reused.

Evidence from existing functions/source:
- The implementation package contains only static algorithm classes.
- `ScsRest` exposes only `GET` endpoints and no storage dependencies.

Business impact:
The service can be used only for immediate stateless classification/calculation, not for business workflows that require auditability, repeatable job management, saved results, or user-scoped data.

### Missing Behavior 3: Reliable File Extension Classification For Ordinary Filenames

Priority:
Important robustness gap

Expected business goal:
Classify common filenames such as `report.txt`, `manual.pdf`, `letter.doc`, `tool.exe`, and `plugin.dll` against directory categories.

Why it is unsupported:
The implementation uses `file.split(".")`, where `.` is a regex wildcard. This does not split on a literal dot and therefore usually prevents ordinary suffix extraction.

Existing functions considered:
- `classify file suffix for a directory category`: appears intended to classify suffixes, but its suffix extraction logic is defective for normal dotted filenames.

Missing capability:
Missing literal-dot extension parsing, case normalization, multi-dot handling, and clear invalid/no-extension semantics.

Proof that function composition is insufficient:
No other function can repair or normalize the file name before `classify file suffix for a directory category`, and the file-suffix endpoint does not consume output from another function. Delete-and-recreate is irrelevant because there is no stored file record.

Evidence from existing functions/source:
- `FileSuffix.subject` derives `fileparts = file.split(".")`.
- `full-behavior.md` explicitly notes that ordinary dotted filenames normally return `0`.

Business impact:
The file classification capability is unreliable for the exact file naming pattern users would reasonably expect.

### Missing Behavior 4: Clear Validation Errors For Unsupported Or Invalid Inputs

Priority:
Important robustness gap

Expected business goal:
Distinguish valid negative classifications from malformed requests, unsupported operations, and invalid domain values.

Why it is unsupported:
Most functions encode invalid or unsupported values as successful default strings such as `0`, `0.0`, `none`, empty string, or `-1`. Only numeric path binding failures produce framework-level request errors.

Existing functions considered:
- `return default calculator result for unsupported operation`: returns `0.0` for an unknown operation instead of an error.
- `report no usable pattern match`: returns `0` for both too-short patterns and genuine no-match cases.
- `classify text as no regex match`: returns `none` for all unmatched regex categories.
- `return no texting abbreviation`: returns an empty string for unsupported translations.
- `reject incompatible or unsupported title pairing`: collapses unsupported sex and incompatible title into `-1`.

Missing capability:
Missing structured validation errors, error codes, reason fields, and separate status codes for malformed, unsupported, and valid-negative outcomes.

Proof that function composition is insufficient:
Client-side chaining cannot recover reasons that the server never returns. Once an endpoint collapses different failure modes into the same string, no later function can distinguish the hidden state.

Evidence from existing functions/source:
- Calculator default initializes `result = 0.0`.
- Pattern search initializes `result = 0`.
- Regex returns `none` after all regex checks fail.
- Text translator initializes `result = ""`.
- Title validator initializes `result = -1`.

Business impact:
Clients cannot reliably explain failures, guide user correction, or differentiate an expected negative result from malformed input.

### Missing Behavior 5: Conventional URL And Full Date Validation

Priority:
Important robustness gap

Expected business goal:
Validate normal URL strings and realistic calendar dates, including conventional `://` URLs, separators, case-insensitive abbreviations, day/month/year components, and calendar validity.

Why it is unsupported:
The regex classifier implements narrow custom token patterns. URL matching expects `//:` rather than conventional `://`, and date matching only accepts `{day}{twoDigits}{month}` with lowercase abbreviations.

Existing functions considered:
- `classify text as URL-like string`: matches only the implementation-specific URL-like regex.
- `classify text as date-like string`: matches only compact lowercase day/two-digit/month tokens.
- `score day and month abbreviations`: scores abbreviations but does not validate a full date or calendar semantics.

Missing capability:
Missing conventional URL parsing, case-insensitive regex handling, separator support, date range checks, year support, and calendar validation.

Proof that function composition is insufficient:
Scoring day/month abbreviations and regex-classifying a compact token can show that abbreviations are recognized, but cannot validate a real calendar date or a conventional URL. No function parses or preserves the missing fields.

Evidence from existing functions/source:
- `Regex.subject` defines `url = protocol + "//:" + iden + "/" + iden`.
- `Regex.subject` defines `date = day + digit + digit + month`.
- `DateParse.subject` only adds a weekday flag and month number.

Business impact:
The service cannot support common validation workflows for URLs or dates without client-side validation outside the API.

### Missing Behavior 6: Server-Side Multi-Step Text Processing Pipeline

Priority:
API ergonomics gap

Expected business goal:
Submit text once and receive combined outputs such as regex classification, pattern-search result, and texting abbreviation in one coherent response.

Why it is unsupported:
Each algorithm is exposed as an independent stateless route. No endpoint accepts a text object, runs multiple algorithms, or returns a structured aggregate result.

Existing functions considered:
- `classify text as no regex match`: classifies one token by regex category.
- `find pattern and reverse pair`: searches a supplied text and pattern.
- `translate see you phrase`: translates fixed path words.
- `translate by the way phrase`: translates fixed path words.

Missing capability:
Missing composite analysis endpoint, request body model, reusable input id, structured response schema, and server-side orchestration.

Proof that function composition is insufficient:
Clients can call multiple endpoints, but the server never binds those calls to the same input record, transaction, or analysis job. Results cannot be recomputed or audited as a single server-side workflow.

Evidence from existing functions/source:
- `ScsRest` exposes separate route methods with unrelated path variable signatures.
- No response contains a shared id or location to bind later calls.

Business impact:
Consumers must implement orchestration and consistency handling themselves, which weakens repeatability and makes analysis results harder to audit.

### Missing Behavior 7: Discover Supported Rule Catalogs

Priority:
API ergonomics gap

Expected business goal:
List supported calculator operations, cookie classifications, date abbreviations, directory/suffix mappings, regex categories, texting abbreviations, and title compatibility rules.

Why it is unsupported:
The supported values are hard-coded in implementation classes and exposed only implicitly through behavior.

Existing functions considered:
- `compute unary calculator function`: accepts supported operation names but cannot list them.
- `classify file suffix for a directory category`: embeds directory/suffix mappings but cannot enumerate them.
- `translate common single word to texting abbreviation`: embeds word mappings but cannot list them.
- `validate neutral-compatible title`: embeds title lists but cannot list them.

Missing capability:
Missing metadata endpoints such as supported operations, title catalog, abbreviation dictionary, regex category documentation, and file category mappings.

Proof that function composition is insufficient:
Calling existing functions can sample known values but cannot prove completeness of the hard-coded catalogs or discover hidden supported values. No function returns a catalog or schema-derived enum.

Evidence from existing functions/source:
- OpenAPI parameters are plain strings, not enums.
- Supported values exist only as string literals in implementation classes.

Business impact:
Clients must reverse engineer supported inputs or duplicate server rules, increasing drift and integration errors.

### Missing Behavior 8: Bulk Or Arbitrary Text Input Handling

Priority:
API ergonomics gap

Expected business goal:
Analyze arbitrary text, filenames, URLs, or phrases containing spaces, slashes, punctuation, or multiple tokens, and process multiple items in one request.

Why it is unsupported:
All inputs are path variables. There are no request bodies, query parameters for encoded text, multipart uploads, or bulk endpoints.

Existing functions considered:
- `find direct pattern only`: accepts `txt` and `pat` as path segments, which is awkward for arbitrary text.
- `classify text as URL-like string`: accepts a single path segment, which conflicts with slash-containing URL-like data.
- `translate by the way phrase`: accepts exactly three path words, not a sentence.
- `classify file suffix for a directory category`: accepts a path segment, not real filesystem paths.

Missing capability:
Missing body-based text input, list/bulk input schemas, encoding guidance, file upload support, and sentence-level parsing.

Proof that function composition is insufficient:
No function can transform arbitrary raw text into a safely reusable server-side value. Path encoding can work for some characters, but slash-delimited routing still constrains natural text and does not provide bulk processing.

Evidence from existing functions/source:
- Every `ScsRest` method uses `@PathVariable`.
- OpenAPI defines only path parameters and no body parameters.

Business impact:
The API is hard to use for realistic text analysis and batch workflows, even though the service domain is string algorithms.

## Cross-Behavior Observations

- The service is stateless. There are no generated ids, ownership links, sessions, database records, cursors, ETags, or persisted relationships to bind across calls.
- Implementation logic takes priority over OpenAPI. The generic `401`, `403`, and `404` responses in OpenAPI are not backed by authentication, authorization, or explicit not-found code.
- Numeric binding is the main framework-level failure mode. String-domain invalidity is usually returned as a successful default classification.
- Several endpoints use default return values that collapse different business meanings: unsupported operation versus legitimate zero result, no pattern versus too-short pattern, no abbreviation versus empty output, unsupported title category versus incompatible title.
- Rule catalogs are hard-coded and not discoverable from the API as enums or metadata resources.
- The file suffix classifier has a concrete implementation defect because `split(".")` uses a regex wildcard.
- Date behavior is split across two independent functions with different case handling: `score day and month abbreviations` lowercases inputs, while `classify text as date-like string` does not.
- Pattern pair detection returns the starting index for pair cases despite comments implying separate result codes.
- All routes use path variables, which limits natural handling of arbitrary text, URLs, filenames, slashes, spaces, and bulk inputs.

## Coverage Summary

Supported domain areas:
Calculator constants and operations; cookie-like path-value classification; cost-functions and no-type-var benchmark classification; day/month abbreviation scoring; limited file suffix classification; lexicographic ordering classification; direct/reverse pattern search; regex token classification; small texting abbreviation translation; title compatibility validation.

Partially supported domain areas:
Date recognition is split and incomplete; file suffix classification is intended but unreliable; cookie/session terminology is only classification, not session management; text processing exists only as independent path-value algorithms.

Unsupported domain areas:
Authentication and authorization; persistent resource lifecycle; stored analysis jobs or audit trails; rule catalog discovery; conventional URL/date validation; robust validation error reporting; server-side multi-step analysis pipelines; body-based, arbitrary-text, or bulk processing.
