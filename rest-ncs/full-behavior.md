Analyzed only `rest-ncs.json` and `src`. Discrepancies: Swagger lists `401`, `403`, and `404` for every endpoint, but `src` contains no auth/forbidden/not-found branch inside these endpoints. Several implementation `400` validation/error branches are not documented in Swagger. Swagger also marks triangle path parameters as `required: false`, but the Spring path mapping requires them.

### Behavior 1: calculate Bessel J value

Behavior name:
calculate Bessel J value

Successful execution:
- Result:
  This behavior calculates a Bessel function result for order `{n}` and input `{x}`, returned as `resultAsDouble`.
- Endpoint sequence:
  Step 1: `GET /api/bessj/{n}/{x}`
- Constraints:
  `{n}` must be an integer with `3 <= n <= 1000`; `{x}` must be a double path value. No prior resource state or response reuse is required.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{n}` is outside the controller-enforced range.
  - Endpoint group:
    Step 1: `GET /api/bessj/{n}/{x}`
  - Failure endpoint:
    `GET /api/bessj/{n}/{x}`
  - Why this fails:
    The controller returns `400` when `{n} <= 2` or `{n} > 1000`.
  - Intentionally violated constraints:
    Use `{n} <= 2` or `{n} > 1000`.

Endpoint coverage:
- Covers:
  `GET /api/bessj/{n}/{x}`
- Distinct meaning:
  Computes the Bessel result and returns it in `resultAsDouble`.

### Behavior 2: calculate exponential integral

Behavior name:
calculate exponential integral

Successful execution:
- Result:
  This behavior calculates the exponential integral value for `{n}` and `{x}`, returned as `resultAsDouble`.
- Endpoint sequence:
  Step 1: `GET /api/expint/{n}/{x}`
- Constraints:
  `{n}` must be an integer and `{x}` must be a double. Implementation success requires `{n} >= 0`, `{x} >= 0`, and not `({x} == 0.0 && ({n} == 0 || {n} == 1))`. The numerical iteration must converge within 100 iterations.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The exponential integral input domain is invalid.
  - Endpoint group:
    Step 1: `GET /api/expint/{n}/{x}`
  - Failure endpoint:
    `GET /api/expint/{n}/{x}`
  - Why this fails:
    `Expint.exe` throws and the controller converts the exception to `400`.
  - Intentionally violated constraints:
    Use `{n} < 0`, or `{x} < 0`, or `{x} == 0.0` with `{n}` equal to `0` or `1`.
- Branch 2:
  - Unsatisfied condition:
    The continued-fraction calculation does not converge within 100 iterations.
  - Endpoint group:
    Step 1: `GET /api/expint/{n}/{x}`
  - Failure endpoint:
    `GET /api/expint/{n}/{x}`
  - Why this fails:
    For the `x > 1.0` algorithm branch, the implementation throws `continued fraction failed in expint`, which becomes `400`.
  - Intentionally violated constraints:
    Use otherwise domain-valid values with `{x} > 1.0` that fail the implementation convergence check.
- Branch 3:
  - Unsatisfied condition:
    The series calculation does not converge within 100 iterations.
  - Endpoint group:
    Step 1: `GET /api/expint/{n}/{x}`
  - Failure endpoint:
    `GET /api/expint/{n}/{x}`
  - Why this fails:
    For the series algorithm branch, the implementation throws `series failed in expint`, which becomes `400`.
  - Intentionally violated constraints:
    Use otherwise domain-valid values in the series branch that fail the implementation convergence check.

Endpoint coverage:
- Covers:
  `GET /api/expint/{n}/{x}`
- Distinct meaning:
  Computes the exponential integral and returns it in `resultAsDouble`.

### Behavior 3: calculate Fisher value

Behavior name:
calculate Fisher value

Successful execution:
- Result:
  This behavior calculates the Fisher numerical result for `{m}`, `{n}`, and `{x}`, returned as `resultAsDouble`.
- Endpoint sequence:
  Step 1: `GET /api/fisher/{m}/{n}/{x}`
- Constraints:
  `{m}` and `{n}` must be integer path values; `{x}` must be a double path value. Implementation rejects only `{m} > 1000` or `{n} > 1000`; it does not enforce positive degrees of freedom or nonnegative `{x}`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    `{m}` or `{n}` exceeds the controller-enforced maximum.
  - Endpoint group:
    Step 1: `GET /api/fisher/{m}/{n}/{x}`
  - Failure endpoint:
    `GET /api/fisher/{m}/{n}/{x}`
  - Why this fails:
    The controller returns `400` before calling the Fisher implementation.
  - Intentionally violated constraints:
    Use `{m} > 1000` or `{n} > 1000`.

Endpoint coverage:
- Covers:
  `GET /api/fisher/{m}/{n}/{x}`
- Distinct meaning:
  Computes the Fisher numerical result and returns it in `resultAsDouble`.

### Behavior 4: calculate upper incomplete gamma Q

Behavior name:
calculate upper incomplete gamma Q

Successful execution:
- Result:
  This behavior calculates the upper incomplete gamma Q value for `{a}` and `{x}`, returned as `resultAsDouble`.
- Endpoint sequence:
  Step 1: `GET /api/gammq/{a}/{x}`
- Constraints:
  `{a}` and `{x}` must be double path values. Implementation success requires `{a} > 0.0`, `{x} >= 0.0`, and convergence within 100 iterations.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The gamma input domain is invalid.
  - Endpoint group:
    Step 1: `GET /api/gammq/{a}/{x}`
  - Failure endpoint:
    `GET /api/gammq/{a}/{x}`
  - Why this fails:
    `Gammq.exe` throws when `{x} < 0.0` or `{a} <= 0.0`, and the controller converts the exception to `400`.
  - Intentionally violated constraints:
    Use `{a} <= 0.0` or `{x} < 0.0`.
- Branch 2:
  - Unsatisfied condition:
    The series calculation does not converge within 100 iterations.
  - Endpoint group:
    Step 1: `GET /api/gammq/{a}/{x}`
  - Failure endpoint:
    `GET /api/gammq/{a}/{x}`
  - Why this fails:
    For `{x} < {a} + 1.0`, the implementation uses the series branch and throws if it exceeds `ITMAX`.
  - Intentionally violated constraints:
    Use otherwise domain-valid values with `{x} < {a} + 1.0` that fail convergence.
- Branch 3:
  - Unsatisfied condition:
    The continued-fraction calculation does not converge within 100 iterations.
  - Endpoint group:
    Step 1: `GET /api/gammq/{a}/{x}`
  - Failure endpoint:
    `GET /api/gammq/{a}/{x}`
  - Why this fails:
    For `{x} >= {a} + 1.0`, the implementation uses the continued-fraction branch and throws if it exceeds `ITMAX`.
  - Intentionally violated constraints:
    Use otherwise domain-valid values with `{x} >= {a} + 1.0` that fail convergence.

Endpoint coverage:
- Covers:
  `GET /api/gammq/{a}/{x}`
- Distinct meaning:
  Computes the gamma Q result and returns it in `resultAsDouble`.

### Behavior 5: calculate bounded integer remainder

Behavior name:
calculate bounded integer remainder

Successful execution:
- Result:
  This behavior calculates the implementation’s integer remainder result for nonzero bounded operands `{a}` and `{b}`, returned as `resultAsInt`.
- Endpoint sequence:
  Step 1: `GET /api/remainder/{a}/{b}`
- Constraints:
  `{a}` and `{b}` must be integers, both must be within `[-10000, 10000]`, and both must be nonzero for this normal remainder path.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    An operand is outside the allowed range.
  - Endpoint group:
    Step 1: `GET /api/remainder/{a}/{b}`
  - Failure endpoint:
    `GET /api/remainder/{a}/{b}`
  - Why this fails:
    The controller returns `400` when either operand is less than `-10000` or greater than `10000`.
  - Intentionally violated constraints:
    Use `{a} < -10000`, `{a} > 10000`, `{b} < -10000`, or `{b} > 10000`.

Endpoint coverage:
- Covers:
  `GET /api/remainder/{a}/{b}`
- Distinct meaning:
  Computes the nonzero-operand remainder path and returns it in `resultAsInt`.

### Behavior 6: return zero-operand remainder sentinel

Behavior name:
return zero-operand remainder sentinel

Successful execution:
- Result:
  This behavior returns the implementation sentinel value `-1` as `resultAsInt` when `{a}` is zero or `{b}` is zero.
- Endpoint sequence:
  Step 1: `GET /api/remainder/{a}/{b}`
- Constraints:
  `{a}` and `{b}` must be integers within `[-10000, 10000]`; at least one of `{a}` or `{b}` must be `0`.

Failure or exceptional branches:
- Branch 1:
  - Unsatisfied condition:
    The zero-operand request also includes an out-of-range operand.
  - Endpoint group:
    Step 1: `GET /api/remainder/{a}/{b}`
  - Failure endpoint:
    `GET /api/remainder/{a}/{b}`
  - Why this fails:
    The controller range check runs before the remainder implementation can return the `-1` sentinel.
  - Intentionally violated constraints:
    Use one operand as `0` and the other outside `[-10000, 10000]`.

Endpoint coverage:
- Covers:
  `GET /api/remainder/{a}/{b}`
- Distinct meaning:
  Handles the implementation’s special zero-operand case instead of the normal nonzero remainder loop.

### Behavior 7: classify edges as not a triangle

Behavior name:
classify edges as not a triangle

Successful execution:
- Result:
  This behavior classifies the provided edge values as not forming a valid triangle and returns `resultAsInt = 0`.
- Endpoint sequence:
  Step 1: `GET /api/triangle/{a}/{b}/{c}`
- Constraints:
  `{a}`, `{b}`, and `{c}` must be integer path values. The result is `0` when any edge is `<= 0`, or when the largest edge is greater than or equal to the sum of the other two edges.

Failure or exceptional branches:
- None identified in `src` for parseable integer path values; invalid or non-triangle inputs are represented as a successful `200` response with `resultAsInt = 0`.

Endpoint coverage:
- Covers:
  `GET /api/triangle/{a}/{b}/{c}`
- Distinct meaning:
  Returns the not-a-triangle classification code `0`.

### Behavior 8: classify scalene triangle

Behavior name:
classify scalene triangle

Successful execution:
- Result:
  This behavior classifies the provided edge values as a scalene triangle and returns `resultAsInt = 1`.
- Endpoint sequence:
  Step 1: `GET /api/triangle/{a}/{b}/{c}`
- Constraints:
  `{a}`, `{b}`, and `{c}` must be positive integer path values, must satisfy the triangle inequality, and all three edge values must be different.

Failure or exceptional branches:
- None identified in `src` for parseable integer path values; inputs that do not satisfy these constraints return another classification code rather than an HTTP failure.

Endpoint coverage:
- Covers:
  `GET /api/triangle/{a}/{b}/{c}`
- Distinct meaning:
  Returns the scalene classification code `1`.

### Behavior 9: classify isosceles triangle

Behavior name:
classify isosceles triangle

Successful execution:
- Result:
  This behavior classifies the provided edge values as an isosceles triangle and returns `resultAsInt = 2`.
- Endpoint sequence:
  Step 1: `GET /api/triangle/{a}/{b}/{c}`
- Constraints:
  `{a}`, `{b}`, and `{c}` must be positive integer path values, must satisfy the triangle inequality, and exactly two edge values must be equal.

Failure or exceptional branches:
- None identified in `src` for parseable integer path values; inputs that do not satisfy these constraints return another classification code rather than an HTTP failure.

Endpoint coverage:
- Covers:
  `GET /api/triangle/{a}/{b}/{c}`
- Distinct meaning:
  Returns the isosceles classification code `2`.

### Behavior 10: classify equilateral triangle

Behavior name:
classify equilateral triangle

Successful execution:
- Result:
  This behavior classifies the provided edge values as an equilateral triangle and returns `resultAsInt = 3`.
- Endpoint sequence:
  Step 1: `GET /api/triangle/{a}/{b}/{c}`
- Constraints:
  `{a}`, `{b}`, and `{c}` must be positive integer path values, and all three values must be equal.

Failure or exceptional branches:
- None identified in `src` for parseable integer path values; nonpositive equal values return `resultAsInt = 0` instead of the equilateral code.

Endpoint coverage:
- Covers:
  `GET /api/triangle/{a}/{b}/{c}`
- Distinct meaning:
  Returns the equilateral classification code `3`.

Unclear or auxiliary endpoints:
None. All six Swagger paths are covered by the behaviors above.