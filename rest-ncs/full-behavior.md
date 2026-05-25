### Function 1: calculate Bessel J value

Function name:
calculate Bessel J value

Core endpoint(s):
- `GET /api/bessj/{n}/{x}`

Preconditions:
- No persistent database or resource state is required. The endpoint is stateless and all inputs are supplied as path values.

Successful execution:
- Result:
  The endpoint calculates a Bessel function result for order `{n}` and input `{x}` and returns it as `resultAsDouble`.
- Invocation:
  Step 1: `GET /api/bessj/{n}/{x}` with integer path value `{n}` and double path value `{x}`
- Constraints:
  `{n}` must satisfy `3 <= {n} <= 1000` before the controller invokes `Bessj.bessj(n, x)`. `{x}` must be parseable as a double path value. Swagger documents `401`, `403`, and `404` responses, but the implementation contains no authentication, authorization, or resource lookup branch for this endpoint.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No persistent database or resource state is required.
    - The path value `{n}` is outside the controller-enforced range.
  - Failure endpoint:
    `GET /api/bessj/{n}/{x}`
  - Why this fails:
    The controller returns `400` before invoking the Bessel implementation when `{n} <= 2` or `{n} > 1000`.
  - Intentionally violated constraints:
    The order range requirement is violated by using `{n} <= 2` or `{n} > 1000`.

Endpoint coverage:
- Covers:
  `GET /api/bessj/{n}/{x}`
- Distinct meaning:
  This endpoint directly computes the Bessel result and returns it in `resultAsDouble`.

### Function 2: calculate exponential integral

Function name:
calculate exponential integral

Core endpoint(s):
- `GET /api/expint/{n}/{x}`

Preconditions:
- No persistent database or resource state is required. The endpoint is stateless and all inputs are supplied as path values.

Successful execution:
- Result:
  The endpoint calculates the exponential integral value for `{n}` and `{x}` and returns it as `resultAsDouble`.
- Invocation:
  Step 1: `GET /api/expint/{n}/{x}` with integer path value `{n}` and double path value `{x}`
- Constraints:
  `{n}` must be parseable as an integer and `{x}` must be parseable as a double. Implementation success requires `{n} >= 0`, `{x} >= 0`, and not `({x} == 0.0 && ({n} == 0 || {n} == 1))`. The selected numerical algorithm branch must converge within 100 iterations. Swagger documents `401`, `403`, and `404` responses, but the implementation contains no authentication, authorization, or resource lookup branch for this endpoint.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No persistent database or resource state is required.
    - The supplied path values violate the exponential integral input domain.
  - Failure endpoint:
    `GET /api/expint/{n}/{x}`
  - Why this fails:
    `Expint.exe(n, x)` throws when `{n} < 0`, `{x} < 0.0`, or `{x} == 0.0` with `{n}` equal to `0` or `1`; the controller catches the runtime exception and returns `400`.
  - Intentionally violated constraints:
    The input domain requirement is violated by using `{n} < 0`, `{x} < 0.0`, or `{x} == 0.0` with `{n}` equal to `0` or `1`.
- Branch 2:
  - Preconditions:
    - No persistent database or resource state is required.
    - The supplied path values are otherwise domain-valid and select the continued-fraction branch with `{x} > 1.0`.
    - The continued-fraction calculation does not converge within 100 iterations.
  - Failure endpoint:
    `GET /api/expint/{n}/{x}`
  - Why this fails:
    `Expint.exe(n, x)` throws `continued fraction failed in expint`; the controller catches the runtime exception and returns `400`.
  - Intentionally violated constraints:
    The convergence requirement for the continued-fraction calculation is violated.
- Branch 3:
  - Preconditions:
    - No persistent database or resource state is required.
    - The supplied path values are otherwise domain-valid and select the series branch with `{x} <= 1.0`.
    - The series calculation does not converge within 100 iterations.
  - Failure endpoint:
    `GET /api/expint/{n}/{x}`
  - Why this fails:
    `Expint.exe(n, x)` throws `series failed in expint`; the controller catches the runtime exception and returns `400`.
  - Intentionally violated constraints:
    The convergence requirement for the series calculation is violated.

Endpoint coverage:
- Covers:
  `GET /api/expint/{n}/{x}`
- Distinct meaning:
  This endpoint directly computes the exponential integral and returns it in `resultAsDouble`.

### Function 3: calculate Fisher value

Function name:
calculate Fisher value

Core endpoint(s):
- `GET /api/fisher/{m}/{n}/{x}`

Preconditions:
- No persistent database or resource state is required. The endpoint is stateless and all inputs are supplied as path values.

Successful execution:
- Result:
  The endpoint calculates the Fisher numerical result for `{m}`, `{n}`, and `{x}` and returns it as `resultAsDouble`.
- Invocation:
  Step 1: `GET /api/fisher/{m}/{n}/{x}` with integer path values `{m}` and `{n}` and double path value `{x}`
- Constraints:
  `{m}` and `{n}` must be parseable as integers and must satisfy `{m} <= 1000` and `{n} <= 1000`. `{x}` must be parseable as a double. The controller does not enforce positive degrees of freedom or nonnegative `{x}` before invoking `Fisher.exe(m, n, x)`. Swagger documents `401`, `403`, and `404` responses, but the implementation contains no authentication, authorization, or resource lookup branch for this endpoint.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No persistent database or resource state is required.
    - At least one degree-of-freedom path value exceeds the controller-enforced maximum.
  - Failure endpoint:
    `GET /api/fisher/{m}/{n}/{x}`
  - Why this fails:
    The controller returns `400` before invoking the Fisher implementation when `{m} > 1000` or `{n} > 1000`.
  - Intentionally violated constraints:
    The maximum-value requirement is violated by using `{m} > 1000` or `{n} > 1000`.
- Branch 2:
  - Preconditions:
    - No persistent database or resource state is required.
    - `{m}` and `{n}` do not exceed `1000`, but the supplied numeric values cause `Fisher.exe(m, n, x)` to throw a runtime exception.
  - Failure endpoint:
    `GET /api/fisher/{m}/{n}/{x}`
  - Why this fails:
    The controller catches any runtime exception thrown by `Fisher.exe(m, n, x)` and returns `400`.
  - Intentionally violated constraints:
    The implicit implementation requirement that the Fisher calculation complete without a runtime exception is violated.

Endpoint coverage:
- Covers:
  `GET /api/fisher/{m}/{n}/{x}`
- Distinct meaning:
  This endpoint directly computes the Fisher numerical result and returns it in `resultAsDouble`.

### Function 4: calculate upper incomplete gamma Q

Function name:
calculate upper incomplete gamma Q

Core endpoint(s):
- `GET /api/gammq/{a}/{x}`

Preconditions:
- No persistent database or resource state is required. The endpoint is stateless and all inputs are supplied as path values.

Successful execution:
- Result:
  The endpoint calculates the upper incomplete gamma Q value for `{a}` and `{x}` and returns it as `resultAsDouble`.
- Invocation:
  Step 1: `GET /api/gammq/{a}/{x}` with double path values `{a}` and `{x}`
- Constraints:
  `{a}` and `{x}` must be parseable as double path values. Implementation success requires `{a} > 0.0`, `{x} >= 0.0`, and convergence within 100 iterations. Swagger documents `401`, `403`, and `404` responses, but the implementation contains no authentication, authorization, or resource lookup branch for this endpoint.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No persistent database or resource state is required.
    - The supplied path values violate the gamma input domain.
  - Failure endpoint:
    `GET /api/gammq/{a}/{x}`
  - Why this fails:
    `Gammq.exe(a, x)` throws when `{x} < 0.0` or `{a} <= 0.0`; the controller catches the runtime exception and returns `400`.
  - Intentionally violated constraints:
    The input domain requirement is violated by using `{a} <= 0.0` or `{x} < 0.0`.
- Branch 2:
  - Preconditions:
    - No persistent database or resource state is required.
    - The supplied path values are otherwise domain-valid and select the series branch with `{x} < {a} + 1.0`.
    - The series calculation does not converge within 100 iterations.
  - Failure endpoint:
    `GET /api/gammq/{a}/{x}`
  - Why this fails:
    `Gammq.gser(a, x)` throws `a too large, ITMAX too small in routine gser`; the controller catches the runtime exception and returns `400`.
  - Intentionally violated constraints:
    The convergence requirement for the gamma series calculation is violated.
- Branch 3:
  - Preconditions:
    - No persistent database or resource state is required.
    - The supplied path values are otherwise domain-valid and select the continued-fraction branch with `{x} >= {a} + 1.0`.
    - The continued-fraction calculation does not converge within 100 iterations.
  - Failure endpoint:
    `GET /api/gammq/{a}/{x}`
  - Why this fails:
    `Gammq.gcf(a, x)` throws `a too large, ITMAX too small in gcf`; the controller catches the runtime exception and returns `400`.
  - Intentionally violated constraints:
    The convergence requirement for the gamma continued-fraction calculation is violated.

Endpoint coverage:
- Covers:
  `GET /api/gammq/{a}/{x}`
- Distinct meaning:
  This endpoint directly computes the gamma Q result and returns it in `resultAsDouble`.

### Function 5: calculate bounded integer remainder

Function name:
calculate bounded integer remainder

Core endpoint(s):
- `GET /api/remainder/{a}/{b}`

Preconditions:
- No persistent database or resource state is required. The endpoint is stateless and all inputs are supplied as path values.

Successful execution:
- Result:
  The endpoint calculates the implementation's integer remainder result for nonzero bounded operands `{a}` and `{b}` and returns it as `resultAsInt`.
- Invocation:
  Step 1: `GET /api/remainder/{a}/{b}` with integer path values `{a}` and `{b}`
- Constraints:
  `{a}` and `{b}` must be parseable as integers, both must be within `[-10000, 10000]`, and both must be nonzero for this normal remainder path. Swagger documents `401`, `403`, and `404` responses, but the implementation contains no authentication, authorization, or resource lookup branch for this endpoint.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No persistent database or resource state is required.
    - At least one operand path value is outside the controller-enforced range.
  - Failure endpoint:
    `GET /api/remainder/{a}/{b}`
  - Why this fails:
    The controller returns `400` before invoking the remainder implementation when `{a} < -10000`, `{a} > 10000`, `{b} < -10000`, or `{b} > 10000`.
  - Intentionally violated constraints:
    The operand range requirement is violated by using `{a} < -10000`, `{a} > 10000`, `{b} < -10000`, or `{b} > 10000`.

Endpoint coverage:
- Covers:
  `GET /api/remainder/{a}/{b}`
- Distinct meaning:
  This endpoint directly computes the normal nonzero-operand remainder path and returns it in `resultAsInt`.

### Function 6: return zero-operand remainder sentinel

Function name:
return zero-operand remainder sentinel

Core endpoint(s):
- `GET /api/remainder/{a}/{b}`

Preconditions:
- No persistent database or resource state is required. The endpoint is stateless and all inputs are supplied as path values.

Successful execution:
- Result:
  The endpoint returns the implementation sentinel value `-1` as `resultAsInt` when `{a}` is zero or `{b}` is zero.
- Invocation:
  Step 1: `GET /api/remainder/{a}/{b}` with integer path values `{a}` and `{b}`
- Constraints:
  `{a}` and `{b}` must be parseable as integers and both must be within `[-10000, 10000]`. At least one of `{a}` or `{b}` must be `0`. Swagger documents `401`, `403`, and `404` responses, but the implementation contains no authentication, authorization, or resource lookup branch for this endpoint.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No persistent database or resource state is required.
    - At least one operand is `0`, and the other operand or the zero operand itself is outside `[-10000, 10000]`.
  - Failure endpoint:
    `GET /api/remainder/{a}/{b}`
  - Why this fails:
    The controller range check runs before `Remainder.exe(a, b)` can return the `-1` sentinel, so it returns `400`.
  - Intentionally violated constraints:
    The operand range requirement is violated while attempting to exercise the zero-operand sentinel path.

Endpoint coverage:
- Covers:
  `GET /api/remainder/{a}/{b}`
- Distinct meaning:
  This endpoint directly exposes the implementation's special zero-operand case instead of the normal nonzero remainder loop.

### Function 7: classify edges as not a triangle

Function name:
classify edges as not a triangle

Core endpoint(s):
- `GET /api/triangle/{a}/{b}/{c}`

Preconditions:
- No persistent database or resource state is required. The endpoint is stateless and all inputs are supplied as path values.

Successful execution:
- Result:
  The endpoint classifies the provided edge values as not forming a valid triangle and returns `resultAsInt = 0`.
- Invocation:
  Step 1: `GET /api/triangle/{a}/{b}/{c}` with integer path values `{a}`, `{b}`, and `{c}`
- Constraints:
  `{a}`, `{b}`, and `{c}` must be parseable as integer path values. The result is `0` when any edge is `<= 0`, or when the largest edge is greater than or equal to the sum of the other two edges. Swagger marks the triangle path parameters as `required: false`, but the Spring path mapping requires all three path segments to route to this endpoint. Swagger also documents `401`, `403`, and `404` responses, but the implementation contains no authentication, authorization, or resource lookup branch for this endpoint.

Failure or exceptional branches:
- None identified for parseable integer path values. Inputs that do not form a triangle are represented as a successful `200` response with `resultAsInt = 0`.

Endpoint coverage:
- Covers:
  `GET /api/triangle/{a}/{b}/{c}`
- Distinct meaning:
  This endpoint directly returns the not-a-triangle classification code `0`.

### Function 8: classify scalene triangle

Function name:
classify scalene triangle

Core endpoint(s):
- `GET /api/triangle/{a}/{b}/{c}`

Preconditions:
- No persistent database or resource state is required. The endpoint is stateless and all inputs are supplied as path values.

Successful execution:
- Result:
  The endpoint classifies the provided edge values as a scalene triangle and returns `resultAsInt = 1`.
- Invocation:
  Step 1: `GET /api/triangle/{a}/{b}/{c}` with integer path values `{a}`, `{b}`, and `{c}`
- Constraints:
  `{a}`, `{b}`, and `{c}` must be parseable as positive integer path values, must satisfy the triangle inequality, and all three edge values must be different. Swagger marks the triangle path parameters as `required: false`, but the Spring path mapping requires all three path segments to route to this endpoint. Swagger also documents `401`, `403`, and `404` responses, but the implementation contains no authentication, authorization, or resource lookup branch for this endpoint.

Failure or exceptional branches:
- None identified for parseable integer path values. Inputs that do not satisfy the scalene constraints return another classification code rather than an HTTP failure.

Endpoint coverage:
- Covers:
  `GET /api/triangle/{a}/{b}/{c}`
- Distinct meaning:
  This endpoint directly returns the scalene classification code `1`.

### Function 9: classify isosceles triangle

Function name:
classify isosceles triangle

Core endpoint(s):
- `GET /api/triangle/{a}/{b}/{c}`

Preconditions:
- No persistent database or resource state is required. The endpoint is stateless and all inputs are supplied as path values.

Successful execution:
- Result:
  The endpoint classifies the provided edge values as an isosceles triangle and returns `resultAsInt = 2`.
- Invocation:
  Step 1: `GET /api/triangle/{a}/{b}/{c}` with integer path values `{a}`, `{b}`, and `{c}`
- Constraints:
  `{a}`, `{b}`, and `{c}` must be parseable as positive integer path values, must satisfy the triangle inequality, and exactly two edge values must be equal. Swagger marks the triangle path parameters as `required: false`, but the Spring path mapping requires all three path segments to route to this endpoint. Swagger also documents `401`, `403`, and `404` responses, but the implementation contains no authentication, authorization, or resource lookup branch for this endpoint.

Failure or exceptional branches:
- None identified for parseable integer path values. Inputs that do not satisfy the isosceles constraints return another classification code rather than an HTTP failure.

Endpoint coverage:
- Covers:
  `GET /api/triangle/{a}/{b}/{c}`
- Distinct meaning:
  This endpoint directly returns the isosceles classification code `2`.

### Function 10: classify equilateral triangle

Function name:
classify equilateral triangle

Core endpoint(s):
- `GET /api/triangle/{a}/{b}/{c}`

Preconditions:
- No persistent database or resource state is required. The endpoint is stateless and all inputs are supplied as path values.

Successful execution:
- Result:
  The endpoint classifies the provided edge values as an equilateral triangle and returns `resultAsInt = 3`.
- Invocation:
  Step 1: `GET /api/triangle/{a}/{b}/{c}` with integer path values `{a}`, `{b}`, and `{c}`
- Constraints:
  `{a}`, `{b}`, and `{c}` must be parseable as positive integer path values, and all three values must be equal. Nonpositive equal values return `resultAsInt = 0` instead. Swagger marks the triangle path parameters as `required: false`, but the Spring path mapping requires all three path segments to route to this endpoint. Swagger also documents `401`, `403`, and `404` responses, but the implementation contains no authentication, authorization, or resource lookup branch for this endpoint.

Failure or exceptional branches:
- None identified for parseable integer path values. Inputs that do not satisfy the equilateral constraints return another classification code rather than an HTTP failure.

Endpoint coverage:
- Covers:
  `GET /api/triangle/{a}/{b}/{c}`
- Distinct meaning:
  This endpoint directly returns the equilateral classification code `3`.
