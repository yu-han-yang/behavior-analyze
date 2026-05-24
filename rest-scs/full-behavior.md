I analyzed only `src` and `rest-scs.json`. The Swagger file lists generic `401`, `403`, and `404` responses on every route, but `src` has no authentication, authorization, or explicit not-found logic; the controller returns `200 OK` after path binding succeeds. I therefore list application-visible branches from the implementation and note binding failures only where numeric path variables are required.

### Behavior 1: return calculator constant

Behavior name:
return calculator constant

Successful execution:
- Result:
  This behavior returns a mathematical constant using the calculator endpoint. `{op}` is case-insensitive and may be `pi` or `e`.
- Endpoint sequence:
  Step 1: `GET /api/calc/{op}/{arg1}/{arg2}` with `{op}` set to `pi` or `e`, and `{arg1}` and `{arg2}` set to parseable doubles.
- Constraints:
  `{arg1}` and `{arg2}` are required by the route and Swagger schema even though the implementation ignores them for `pi` and `e`. No prerequisite endpoint exists or is required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{arg1}` or `{arg2}` cannot be converted to a Java `double`.
  - Endpoint group:
    Step 1: `GET /api/calc/{op}/{arg1}/{arg2}` with `{op}` set to `pi` or `e` and a non-numeric `{arg1}` or `{arg2}`.
  - Failure endpoint:
    `GET /api/calc/{op}/{arg1}/{arg2}`
  - Why this fails:
    Spring path-variable binding fails before `Calc.subject` is called.
  - Intentionally violated constraints:
    The Swagger and controller require `{arg1}` and `{arg2}` to be numeric doubles.

Endpoint coverage:
- Covers:
  `GET /api/calc/{op}/{arg1}/{arg2}`
- Distinct meaning:
  The same endpoint returns constants when `{op}` is `pi` or `e`.

### Behavior 2: compute unary calculator function

Behavior name:
compute unary calculator function

Successful execution:
- Result:
  This behavior applies a unary math function to `{arg1}`. Supported case-insensitive `{op}` values are `sqrt`, `log`, `sine`, `cosine`, and `tangent`.
- Endpoint sequence:
  Step 1: `GET /api/calc/{op}/{arg1}/{arg2}` with `{op}` set to a supported unary operator, `{arg1}` set to the operand, and `{arg2}` set to any parseable double.
- Constraints:
  `{arg1}` supplies the operand. `{arg2}` is required by the route but ignored by the unary implementation. Java math domain results such as `NaN` or `Infinity` are returned as strings rather than treated as HTTP failures.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{arg1}` or `{arg2}` cannot be converted to a Java `double`.
  - Endpoint group:
    Step 1: `GET /api/calc/{op}/{arg1}/{arg2}` with `{op}` set to a unary operator and a non-numeric `{arg1}` or `{arg2}`.
  - Failure endpoint:
    `GET /api/calc/{op}/{arg1}/{arg2}`
  - Why this fails:
    Spring path-variable binding fails before the calculator logic runs.
  - Intentionally violated constraints:
    Numeric path-variable binding for `{arg1}` or `{arg2}`.

Endpoint coverage:
- Covers:
  `GET /api/calc/{op}/{arg1}/{arg2}`
- Distinct meaning:
  The same endpoint performs unary math when `{op}` selects a unary function.

### Behavior 3: compute binary calculator operation

Behavior name:
compute binary calculator operation

Successful execution:
- Result:
  This behavior applies a binary arithmetic operation to `{arg1}` and `{arg2}`. Supported case-insensitive `{op}` values are `plus`, `subtract`, `multiply`, and `divide`.
- Endpoint sequence:
  Step 1: `GET /api/calc/{op}/{arg1}/{arg2}` with `{op}` set to a supported binary operator and both arguments set to parseable doubles.
- Constraints:
  `{arg1}` and `{arg2}` are both used. Division by zero follows Java double arithmetic and returns a string such as `Infinity` or `NaN`, not an application-level error.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{arg1}` or `{arg2}` cannot be converted to a Java `double`.
  - Endpoint group:
    Step 1: `GET /api/calc/{op}/{arg1}/{arg2}` with `{op}` set to a binary operator and a non-numeric `{arg1}` or `{arg2}`.
  - Failure endpoint:
    `GET /api/calc/{op}/{arg1}/{arg2}`
  - Why this fails:
    Spring path-variable binding fails before `Calc.subject` is called.
  - Intentionally violated constraints:
    Numeric path-variable binding for `{arg1}` or `{arg2}`.

Endpoint coverage:
- Covers:
  `GET /api/calc/{op}/{arg1}/{arg2}`
- Distinct meaning:
  The same endpoint performs binary arithmetic when `{op}` selects a binary operator.

### Behavior 4: return default calculator result for unsupported operation

Behavior name:
return default calculator result for unsupported operation

Successful execution:
- Result:
  This behavior returns `0.0` when `{op}` does not match any supported calculator operation after lowercasing.
- Endpoint sequence:
  Step 1: `GET /api/calc/{op}/{arg1}/{arg2}` with `{op}` set to any unsupported string and both arguments set to parseable doubles.
- Constraints:
  `{op}` must not equal `pi`, `e`, `sqrt`, `log`, `sine`, `cosine`, `tangent`, `plus`, `subtract`, `multiply`, or `divide` after lowercasing.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{arg1}` or `{arg2}` cannot be converted to a Java `double`.
  - Endpoint group:
    Step 1: `GET /api/calc/{op}/{arg1}/{arg2}` with unsupported `{op}` and a non-numeric `{arg1}` or `{arg2}`.
  - Failure endpoint:
    `GET /api/calc/{op}/{arg1}/{arg2}`
  - Why this fails:
    Spring path-variable binding fails before the default calculator result can be returned.
  - Intentionally violated constraints:
    Numeric path-variable binding for `{arg1}` or `{arg2}`.

Endpoint coverage:
- Covers:
  `GET /api/calc/{op}/{arg1}/{arg2}`
- Distinct meaning:
  The same endpoint exposes a default result for unsupported calculator operations.

### Behavior 5: recognize matching userid cookie

Behavior name:
recognize matching userid cookie

Successful execution:
- Result:
  This behavior returns `1` when the cookie name is `userid` and the value, after lowercasing, is longer than six characters and starts with `user`.
- Endpoint sequence:
  Step 1: `GET /api/cookie/{name}/{val}/{site}` with `{name}` set to `userid`, `{val}` set to a value longer than six characters beginning with `user`, and `{site}` set to any path string.
- Constraints:
  `{name}`, `{val}`, and `{site}` are lowercased by the implementation. `{site}` is ignored for the `userid` branch.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/cookie/{name}/{val}/{site}`
- Distinct meaning:
  The endpoint validates the special `userid` cookie shape.

### Behavior 6: recognize accepted session cookie

Behavior name:
recognize accepted session cookie

Successful execution:
- Result:
  This behavior returns `1` when the cookie name is `session`, the value is `am`, and the site is `abc.com`, all compared case-insensitively.
- Endpoint sequence:
  Step 1: `GET /api/cookie/{name}/{val}/{site}` with `{name}` set to `session`, `{val}` set to `am`, and `{site}` set to `abc.com`.
- Constraints:
  All three path values are lowercased before comparison.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/cookie/{name}/{val}/{site}`
- Distinct meaning:
  The endpoint recognizes the one accepted session-cookie/site combination.

### Behavior 7: classify non-accepted session cookie

Behavior name:
classify non-accepted session cookie

Successful execution:
- Result:
  This behavior returns `2` for a `session` cookie when `{val}` and `{site}` are not exactly the accepted `am` and `abc.com` combination after lowercasing.
- Endpoint sequence:
  Step 1: `GET /api/cookie/{name}/{val}/{site}` with `{name}` set to `session` and either `{val}` not equal to `am` or `{site}` not equal to `abc.com`.
- Constraints:
  `{name}` must be `session` after lowercasing. At least one of `{val}` or `{site}` must differ from the accepted pair.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/cookie/{name}/{val}/{site}`
- Distinct meaning:
  The endpoint gives a separate visible result for session cookies that are recognized as sessions but not accepted.

### Behavior 8: classify unrecognized or nonmatching cookie

Behavior name:
classify unrecognized or nonmatching cookie

Successful execution:
- Result:
  This behavior returns `0` when the cookie is not a matching `userid` cookie and is not a `session` cookie.
- Endpoint sequence:
  Step 1: `GET /api/cookie/{name}/{val}/{site}` with `{name}` not equal to `session`, and either `{name}` not equal to `userid` or `{val}` not longer than six characters starting with `user`.
- Constraints:
  Values are compared after lowercasing. A `userid` name with a short value or a value not starting with `user` also returns `0`.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/cookie/{name}/{val}/{site}`
- Distinct meaning:
  The endpoint exposes a default cookie classification result.

### Behavior 9: evaluate cost-functions benchmark

Behavior name:
evaluate cost-functions benchmark

Successful execution:
- Result:
  This behavior runs the cost-functions benchmark endpoint and, for normal non-null path strings, returns `10`.
- Endpoint sequence:
  Step 1: `GET /api/costfuns/{i}/{s}` with `{i}` set to a parseable integer and `{s}` set to a path string.
- Constraints:
  The source contains earlier conditions for `{i}` and `{s}`, but the final comparison uses Java reference inequality, `s != s2 + s2`, so it overwrites earlier results with `10` for normal request strings. This implementation detail is more specific than the Swagger summary.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{i}` cannot be converted to a Java `Integer`.
  - Endpoint group:
    Step 1: `GET /api/costfuns/{i}/{s}` with non-integer `{i}`.
  - Failure endpoint:
    `GET /api/costfuns/{i}/{s}`
  - Why this fails:
    Spring path-variable binding fails before `Costfuns.subject` is called.
  - Intentionally violated constraints:
    The Swagger and controller require `{i}` to be an integer.

Endpoint coverage:
- Covers:
  `GET /api/costfuns/{i}/{s}`
- Distinct meaning:
  The endpoint exposes the cost-functions benchmark result.

### Behavior 10: score day and month abbreviations

Behavior name:
score day and month abbreviations

Successful execution:
- Result:
  This behavior returns a numeric string score for a day/month abbreviation pair. A recognized weekday adds `1`; a recognized month adds its month number from `jan = 1` through `dec = 12`.
- Endpoint sequence:
  Step 1: `GET /api/dateparse/{dayname}/{monthname}` with `{dayname}` and `{monthname}` set to path strings.
- Constraints:
  Both path values are lowercased before comparison. Recognized days are `mon`, `tue`, `wed`, `thur`, `fri`, `sat`, and `sun`. Recognized months are `jan` through `dec`. Unrecognized values contribute `0`; they do not cause an HTTP error.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/dateparse/{dayname}/{monthname}`
- Distinct meaning:
  The endpoint parses visible day/month abbreviation inputs into a score.

### Behavior 11: classify file suffix for a directory category

Behavior name:
classify file suffix for a directory category

Successful execution:
- Result:
  This behavior invokes the file-suffix classifier for a `{directory}` and `{file}` path value. The intended category codes in the source are `text/txt = 1`, `acrobat/pdf = 2`, `word/doc = 3`, `bin/exe = 4`, and `lib/dll = 5`, but ordinary dotted filenames normally return `0` because the implementation calls `file.split(".")`, where `.` is a regex wildcard rather than a literal dot.
- Endpoint sequence:
  Step 1: `GET /api/filesuffix/{directory}/{file}` with `{directory}` and `{file}` set to path strings.
- Constraints:
  No prerequisite endpoint exists. Source logic, not the Swagger summary alone, determines that suffix extraction is unreliable for ordinary filenames.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/filesuffix/{directory}/{file}`
- Distinct meaning:
  The endpoint exposes file suffix classification, with the implementation caveat above.

### Behavior 12: classify no-type-var input as high integer

Behavior name:
classify no-type-var input as high integer

Successful execution:
- Result:
  This behavior returns `3` when `{i}` is greater than `5`.
- Endpoint sequence:
  Step 1: `GET /api/notypevar/{i}/{s}` with `{i}` set to an integer greater than `5` and `{s}` set to any path string.
- Constraints:
  The last visible condition in the implementation overwrites earlier results when `{i} > 5`, so values such as `7` or `28` return `3`, not the earlier intermediate results.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{i}` cannot be converted to a Java `Integer`.
  - Endpoint group:
    Step 1: `GET /api/notypevar/{i}/{s}` with non-integer `{i}`.
  - Failure endpoint:
    `GET /api/notypevar/{i}/{s}`
  - Why this fails:
    Spring path-variable binding fails before `NotyPevar.subject` is called.
  - Intentionally violated constraints:
    `{i}` must be an integer.

Endpoint coverage:
- Covers:
  `GET /api/notypevar/{i}/{s}`
- Distinct meaning:
  The endpoint exposes the final high-integer classification branch.

### Behavior 13: classify no-type-var input by string comparison

Behavior name:
classify no-type-var input by string comparison

Successful execution:
- Result:
  This behavior returns `2` when `{i}` is not greater than `5` and the literal string `hello` is lexicographically less than `{s}`.
- Endpoint sequence:
  Step 1: `GET /api/notypevar/{i}/{s}` with `{i}` set to an integer less than or equal to `5` and `{s}` set to a string lexicographically greater than `hello`.
- Constraints:
  `{i}` must be at most `5`; otherwise the later `{i} > 5` condition overwrites the result with `3`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{i}` cannot be converted to a Java `Integer`.
  - Endpoint group:
    Step 1: `GET /api/notypevar/{i}/{s}` with non-integer `{i}`.
  - Failure endpoint:
    `GET /api/notypevar/{i}/{s}`
  - Why this fails:
    Spring path-variable binding fails before the string comparison runs.
  - Intentionally violated constraints:
    `{i}` must be an integer.

Endpoint coverage:
- Covers:
  `GET /api/notypevar/{i}/{s}`
- Distinct meaning:
  The endpoint exposes the string-comparison classification branch.

### Behavior 14: return default no-type-var classification

Behavior name:
return default no-type-var classification

Successful execution:
- Result:
  This behavior returns `0` when `{i}` is at most `5` and `{s}` is not lexicographically greater than `hello`.
- Endpoint sequence:
  Step 1: `GET /api/notypevar/{i}/{s}` with `{i}` set to an integer less than or equal to `5` and `{s}` set to a string less than or equal to `hello`.
- Constraints:
  Earlier source branches for `{i} == 7` and `{i} == 28` are not visible in the final result because `{i} > 5` overwrites them.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{i}` cannot be converted to a Java `Integer`.
  - Endpoint group:
    Step 1: `GET /api/notypevar/{i}/{s}` with non-integer `{i}`.
  - Failure endpoint:
    `GET /api/notypevar/{i}/{s}`
  - Why this fails:
    Spring path-variable binding fails before the benchmark logic runs.
  - Intentionally violated constraints:
    `{i}` must be an integer.

Endpoint coverage:
- Covers:
  `GET /api/notypevar/{i}/{s}`
- Distinct meaning:
  The endpoint exposes the default no-type-var result.

### Behavior 15: classify four strings as increasing

Behavior name:
classify four strings as increasing

Successful execution:
- Result:
  This behavior returns `increasing` when all four inputs have length 5 or 6 and satisfy `z > y > x > w` lexicographically.
- Endpoint sequence:
  Step 1: `GET /api/ordered4/{w}/{x}/{z}/{y}` with `{w}`, `{x}`, `{z}`, and `{y}` set so that each length is 5 or 6 and `z.compareTo(y) > 0`, `y.compareTo(x) > 0`, and `x.compareTo(w) > 0`.
- Constraints:
  The path order is `{w}/{x}/{z}/{y}`, but the comparison order for this branch is `{z}`, then `{y}`, then `{x}`, then `{w}`.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/ordered4/{w}/{x}/{z}/{y}`
- Distinct meaning:
  The endpoint reports an increasing lexicographic ordering.

### Behavior 16: classify four strings as decreasing

Behavior name:
classify four strings as decreasing

Successful execution:
- Result:
  This behavior returns `decreasing` when all four inputs have length 5 or 6 and satisfy `w > x > y > z` lexicographically.
- Endpoint sequence:
  Step 1: `GET /api/ordered4/{w}/{x}/{z}/{y}` with all values length 5 or 6 and `w.compareTo(x) > 0`, `x.compareTo(y) > 0`, and `y.compareTo(z) > 0`.
- Constraints:
  The path order places `{z}` before `{y}`, but the decreasing comparison uses `{y}` before `{z}`.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/ordered4/{w}/{x}/{z}/{y}`
- Distinct meaning:
  The endpoint reports a decreasing lexicographic ordering.

### Behavior 17: classify four strings as unordered

Behavior name:
classify four strings as unordered

Successful execution:
- Result:
  This behavior returns `unordered` when the inputs do not satisfy the increasing or decreasing branch, or when any input length is outside 5 through 6.
- Endpoint sequence:
  Step 1: `GET /api/ordered4/{w}/{x}/{z}/{y}` with values that violate the required length gate or both ordering predicates.
- Constraints:
  This is the default result initialized by the implementation.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/ordered4/{w}/{x}/{z}/{y}`
- Distinct meaning:
  The endpoint exposes the unordered/default classification.

### Behavior 18: find direct pattern only

Behavior name:
find direct pattern only

Successful execution:
- Result:
  This behavior returns `1` when `{pat}` has length greater than 2 and the text contains `{pat}` but the implementation does not find the reverse of `{pat}` later in the text.
- Endpoint sequence:
  Step 1: `GET /api/pat/{txt}/{pat}` with `{pat}` length greater than 2 and `{txt}` containing `{pat}` without a later matching reverse occurrence.
- Constraints:
  `{pat}` is reversed internally. The direct-pattern-only branch is visible only when no later reverse match triggers the index-return branch.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/pat/{txt}/{pat}`
- Distinct meaning:
  The endpoint searches for a direct pattern occurrence.

### Behavior 19: find reverse pattern only

Behavior name:
find reverse pattern only

Successful execution:
- Result:
  This behavior returns `2` when `{pat}` has length greater than 2 and the text contains the reverse of `{pat}` but the implementation does not find `{pat}` later in the text.
- Endpoint sequence:
  Step 1: `GET /api/pat/{txt}/{pat}` with `{pat}` length greater than 2 and `{txt}` containing the reverse of `{pat}` without a later direct `{pat}` occurrence.
- Constraints:
  The reverse is computed by the implementation. The reverse-only result remains visible only if the later direct-pattern scan does not return an index.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/pat/{txt}/{pat}`
- Distinct meaning:
  The endpoint searches for a reversed pattern occurrence.

### Behavior 20: find pattern and reverse pair

Behavior name:
find pattern and reverse pair

Successful execution:
- Result:
  This behavior returns the starting index of the first matched pattern or reverse-pattern occurrence when the implementation finds both `{pat}` and its reverse in the required order.
- Endpoint sequence:
  Step 1: `GET /api/pat/{txt}/{pat}` with `{pat}` length greater than 2 and `{txt}` containing `{pat}` followed later by its reverse, or the reverse followed later by `{pat}`.
- Constraints:
  Source comments describe results `3`, `4`, and `5`, but the actual implementation returns the index `i` as a string for both adjacent and non-adjacent pair cases. The implementation result takes priority.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/pat/{txt}/{pat}`
- Distinct meaning:
  The endpoint reports a paired direct/reverse pattern match by returning the starting index.

### Behavior 21: report no usable pattern match

Behavior name:
report no usable pattern match

Successful execution:
- Result:
  This behavior returns `0` when `{pat}` length is 2 or less, or when neither `{pat}` nor its reverse is found in `{txt}`.
- Endpoint sequence:
  Step 1: `GET /api/pat/{txt}/{pat}` with `{pat}` length at most 2, or with `{pat}` length greater than 2 but no direct or reverse match in `{txt}`.
- Constraints:
  The implementation does not search at all when `{pat}` length is less than or equal to 2.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/pat/{txt}/{pat}`
- Distinct meaning:
  The endpoint exposes the no-match/default result for pattern search.

### Behavior 22: classify text as URL-like string

Behavior name:
classify text as URL-like string

Successful execution:
- Result:
  This behavior returns `url` when `{txt}` fully matches the implementation’s URL regex.
- Endpoint sequence:
  Step 1: `GET /api/pat/{txt}` with `{txt}` set to a string matching the supported URL regex.
- Constraints:
  Supported protocols are `http`, `ftp`, `afs`, and `gopher`. The implementation expects the unusual literal sequence `//:` before the host-like identifier. The route path is `/api/pat/{txt}`, but the Swagger summary and controller method identify this behavior as regex classification.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/pat/{txt}`
- Distinct meaning:
  The endpoint classifies a single text input as a URL-like string.

### Behavior 23: classify text as date-like string

Behavior name:
classify text as date-like string

Successful execution:
- Result:
  This behavior returns `date` when `{txt}` fully matches the implementation’s date regex.
- Endpoint sequence:
  Step 1: `GET /api/pat/{txt}` with `{txt}` set to `{day}{digit}{digit}{month}`, using supported lowercase day and month abbreviations.
- Constraints:
  Recognized days are `mon`, `tue`, `wed`, `thur`, `fri`, `sat`, and `sun`. Recognized months are `jan` through `dec`. Unlike `dateparse`, this implementation does not lowercase `{txt}` before matching.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/pat/{txt}`
- Distinct meaning:
  The endpoint classifies a single text input as a date-like string.

### Behavior 24: classify text as floating-point exponent string

Behavior name:
classify text as floating-point exponent string

Successful execution:
- Result:
  This behavior returns `fpe` when `{txt}` fully matches the floating-point-with-exponent regex.
- Endpoint sequence:
  Step 1: `GET /api/pat/{txt}` with `{txt}` set to digits, a dot, digits, `e`, a `+` or `-` sign, and exactly two trailing digits.
- Constraints:
  The regex requires lowercase `e` and a sign. URL matching is checked first, then date, then floating-point exponent.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/pat/{txt}`
- Distinct meaning:
  The endpoint classifies a single text input as an exponent-form number.

### Behavior 25: classify text as no regex match

Behavior name:
classify text as no regex match

Successful execution:
- Result:
  This behavior returns `none` when `{txt}` does not fully match the URL, date, or floating-point exponent regex.
- Endpoint sequence:
  Step 1: `GET /api/pat/{txt}` with `{txt}` set to a string that matches none of the three regex categories.
- Constraints:
  The implementation uses full-string `Pattern.matches`, not substring search.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/pat/{txt}`
- Distinct meaning:
  The endpoint exposes the regex classifier’s default result.

### Behavior 26: translate common single word to texting abbreviation

Behavior name:
translate common single word to texting abbreviation

Successful execution:
- Result:
  This behavior returns a texting abbreviation for a supported first word: `two -> 2`, `for` or `four -> 4`, `you -> u`, `and -> n`, and `are -> r`.
- Endpoint sequence:
  Step 1: `GET /api/text2txt/{word1}/{word2}/{word3}` with `{word1}` set to one of the supported single words and `{word2}` and `{word3}` set to any path strings.
- Constraints:
  All three words are lowercased, but this branch only uses `{word1}`. For `{word1} = are`, the later phrase branches are skipped by the `else if` structure.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/text2txt/{word1}/{word2}/{word3}`
- Distinct meaning:
  The endpoint translates a recognized single word into a texting abbreviation.

### Behavior 27: translate see you phrase

Behavior name:
translate see you phrase

Successful execution:
- Result:
  This behavior returns `cu` when `{word1}` is `see` and `{word2}` is `you`.
- Endpoint sequence:
  Step 1: `GET /api/text2txt/{word1}/{word2}/{word3}` with `{word1}` set to `see`, `{word2}` set to `you`, and `{word3}` set to any path string.
- Constraints:
  Values are lowercased before comparison. `{word3}` is ignored for this phrase.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/text2txt/{word1}/{word2}/{word3}`
- Distinct meaning:
  The endpoint translates the two-word phrase `see you`.

### Behavior 28: translate by the way phrase

Behavior name:
translate by the way phrase

Successful execution:
- Result:
  This behavior returns `btw` when the three path words are `by`, `the`, and `way`.
- Endpoint sequence:
  Step 1: `GET /api/text2txt/{word1}/{word2}/{word3}` with `{word1}` set to `by`, `{word2}` set to `the`, and `{word3}` set to `way`.
- Constraints:
  Values are lowercased before comparison. All three words must match the phrase.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/text2txt/{word1}/{word2}/{word3}`
- Distinct meaning:
  The endpoint translates the three-word phrase `by the way`.

### Behavior 29: return no texting abbreviation

Behavior name:
return no texting abbreviation

Successful execution:
- Result:
  This behavior returns an empty string when the words do not match any supported single-word or phrase abbreviation.
- Endpoint sequence:
  Step 1: `GET /api/text2txt/{word1}/{word2}/{word3}` with values that do not satisfy the supported abbreviation conditions.
- Constraints:
  All three words are lowercased. The empty string is the initialized default result.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/text2txt/{word1}/{word2}/{word3}`
- Distinct meaning:
  The endpoint exposes the default no-translation result.

### Behavior 30: validate male-compatible title

Behavior name:
validate male-compatible title

Successful execution:
- Result:
  This behavior returns `1` when `{sex}` is `male` and `{title}` is one of the male-compatible titles.
- Endpoint sequence:
  Step 1: `GET /api/title/{sex}/{title}` with `{sex}` set to `male` and `{title}` set to `mr`, `dr`, `sir`, `rev`, `rthon`, or `prof`.
- Constraints:
  Both path values are lowercased before comparison.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/title/{sex}/{title}`
- Distinct meaning:
  The endpoint validates male title compatibility.

### Behavior 31: validate female-compatible title

Behavior name:
validate female-compatible title

Successful execution:
- Result:
  This behavior returns `0` when `{sex}` is `female` and `{title}` is one of the female-compatible titles.
- Endpoint sequence:
  Step 1: `GET /api/title/{sex}/{title}` with `{sex}` set to `female` and `{title}` set to `mrs`, `miss`, `ms`, `dr`, `lady`, `rev`, `rthon`, or `prof`.
- Constraints:
  Both path values are lowercased before comparison.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/title/{sex}/{title}`
- Distinct meaning:
  The endpoint validates female title compatibility.

### Behavior 32: validate neutral-compatible title

Behavior name:
validate neutral-compatible title

Successful execution:
- Result:
  This behavior returns `2` when `{sex}` is `none` and `{title}` is one of the neutral-compatible titles.
- Endpoint sequence:
  Step 1: `GET /api/title/{sex}/{title}` with `{sex}` set to `none` and `{title}` set to `dr`, `rev`, `rthon`, or `prof`.
- Constraints:
  Both path values are lowercased before comparison.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/title/{sex}/{title}`
- Distinct meaning:
  The endpoint validates a title that is compatible with no specified sex.

### Behavior 33: reject incompatible or unsupported title pairing

Behavior name:
reject incompatible or unsupported title pairing

Successful execution:
- Result:
  This behavior returns `-1` when `{sex}` is unsupported or when `{title}` is not compatible with the supported `{sex}` value.
- Endpoint sequence:
  Step 1: `GET /api/title/{sex}/{title}` with `{sex}` and `{title}` set to a pair that does not satisfy the male, female, or none-compatible title lists.
- Constraints:
  Both values are lowercased. `-1` is the initialized default result and remains unchanged for unsupported or incompatible pairs.

Failure or exceptional branches:
- None identified in the allowed implementation.

Endpoint coverage:
- Covers:
  `GET /api/title/{sex}/{title}`
- Distinct meaning:
  The endpoint exposes the title validator’s incompatible/default result.

### Unclear or auxiliary endpoints

None. Every endpoint in `rest-scs.json` is covered above.