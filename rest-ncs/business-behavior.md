# Domain-Level Behavior Analysis

## Domain Summary

The `rest-ncs` service is a stateless REST facade over a set of numerical case-study algorithms. It does not model persistent business resources, users, ownership, sessions, generated identifiers, or stored calculation history. Every supported capability is driven entirely by path parameters and returns a `Dto` containing either `resultAsDouble` or `resultAsInt`.

The main domain concepts are numerical algorithm evaluation, bounded integer arithmetic, and triangle edge classification. The OpenAPI description presents these as "Examples of different numerical algorithms accessible via REST." The implementation is the authoritative behavior source where it disagrees with the OpenAPI contract.

## Available Function Inventory

### Special Function Calculations

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `calculate Bessel J value` | `GET /api/bessj/{n}/{x}` | Computes a Bessel J value for an integer order and numeric input. |
| `calculate exponential integral` | `GET /api/expint/{n}/{x}` | Computes an exponential integral value for an integer order and numeric input. |
| `calculate upper incomplete gamma Q` | `GET /api/gammq/{a}/{x}` | Computes the upper incomplete gamma Q value for numeric shape/input values. |

### Statistical Calculation

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `calculate Fisher value` | `GET /api/fisher/{m}/{n}/{x}` | Computes the implementation's Fisher distribution-style numeric value for two degree parameters and an input value. |

### Integer Arithmetic

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `calculate bounded integer remainder` | `GET /api/remainder/{a}/{b}` | Computes the implementation's bounded integer remainder path for nonzero operands. |
| `return zero-operand remainder sentinel` | `GET /api/remainder/{a}/{b}` | Returns the implementation sentinel value `-1` when either operand is zero. |

### Triangle Classification

| Function | Core endpoint(s) | Domain meaning |
|---|---|---|
| `classify edges as not a triangle` | `GET /api/triangle/{a}/{b}/{c}` | Classifies edge values as not forming a valid triangle and returns code `0`. |
| `classify scalene triangle` | `GET /api/triangle/{a}/{b}/{c}` | Classifies valid triangle edge values with all sides different and returns code `1`. |
| `classify isosceles triangle` | `GET /api/triangle/{a}/{b}/{c}` | Classifies valid triangle edge values with exactly two equal sides and returns code `2`. |
| `classify equilateral triangle` | `GET /api/triangle/{a}/{b}/{c}` | Classifies valid triangle edge values with all three sides equal and returns code `3`. |

## Supported Business Behaviors

### Behavior 1: Calculate a Bessel J Value

Business goal:
Compute a Bessel J numerical result for a requested order and input value.

Domain context:
This is a stateless numerical-analysis capability. The client supplies the complete calculation request as path values, and the service returns the computed double result.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `calculate Bessel J value` (`GET /api/bessj/{n}/{x}`) with path `n=3` and path `x=1.5` to calculate the Bessel J value and receive `resultAsDouble`.

Optional verification workflow:
None. The behavior itself returns the computed value.

Existing-state shortcuts:
- No setup function can be skipped because there is no setup state.
- Direct database setup is not relevant; the service has no persistent calculation store.
- If the client already has an equivalent Bessel result for the same `n` and `x`, the API call can be avoided client-side, but that is outside service behavior.

Parameter and value bindings:
- The path value `n` is the Bessel order consumed directly by `Bessj.bessj(n, x)`.
- The path value `x` is the numeric input consumed by the same calculation.
- No generated id, ownership scope, token, cursor, or response-field binding exists.

Business result:
The response is `200 OK` with `Dto.resultAsDouble` populated. No service state is created, updated, or deleted.

Constraints and invariants:
- `n` must be parseable as an integer and must satisfy `3 <= n <= 1000`.
- `x` must be parseable as a double.
- Negative `x` is allowed; the Bessel implementation applies sign handling for negative values and odd `n`.
- The OpenAPI document lists `401`, `403`, and `404`, but the implementation has no authentication, authorization, or resource lookup branch.

Failure and exceptional cases:
- Failing function: `calculate Bessel J value`
  - Failure condition: `n <= 2` or `n > 1000`.
  - Why it fails: the controller returns `400` before invoking the Bessel implementation.
  - Violated prerequisite or constraint: Bessel order must be inside the controller-enforced range.
- Failing function: `calculate Bessel J value`
  - Failure condition: `n` is not an integer path value or `x` is not a double path value.
  - Why it fails: Spring path conversion cannot bind the controller parameters.
  - Violated prerequisite or constraint: path values must match their declared Java types.

Implementation notes:
The implementation checks `n` in the controller, even though `Bessj.bessj` itself only rejects `n < 2`. The controller therefore makes `n=2` invalid even though the lower-level algorithm would accept it.

### Behavior 2: Calculate an Exponential Integral

Business goal:
Compute an exponential integral for a supplied integer order and numeric input.

Domain context:
This behavior exposes a mathematical special-function calculation where the domain of valid inputs is enforced by the implementation rather than by explicit OpenAPI constraints.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `calculate exponential integral` (`GET /api/expint/{n}/{x}`) with path `n=2` and path `x=1.5` to calculate the exponential integral and receive `resultAsDouble`.

Optional verification workflow:
None. The behavior itself returns the computed value.

Existing-state shortcuts:
- No workflow step can be skipped for service-side execution.
- Direct database setup is not relevant.
- A cached client-side result is reusable only if it was computed with the same `n` and `x`.

Parameter and value bindings:
- The path value `n` is passed unchanged to `Expint.exe(n, x)`.
- The path value `x` is passed unchanged to `Expint.exe(n, x)`.
- No persistent state or response identifier is reused by later service functions.

Business result:
The response is `200 OK` with `Dto.resultAsDouble` populated. The service persists no calculation record.

Constraints and invariants:
- `n` must be parseable as an integer.
- `x` must be parseable as a double.
- Implementation success requires `n >= 0`, `x >= 0.0`, and not `x == 0.0` with `n == 0` or `n == 1`.
- The selected numerical branch must converge within 100 iterations.
- OpenAPI does not document these domain constraints.

Failure and exceptional cases:
- Failing function: `calculate exponential integral`
  - Failure condition: `n < 0`, `x < 0.0`, or `x == 0.0` with `n` equal to `0` or `1`.
  - Why it fails: `Expint.exe(n, x)` throws a runtime exception and the controller converts it to `400`.
  - Violated prerequisite or constraint: the exponential integral input domain must be satisfied.
- Failing function: `calculate exponential integral`
  - Failure condition: a domain-valid input selects a numerical branch that does not converge within 100 iterations.
  - Why it fails: `Expint.exe(n, x)` throws either `continued fraction failed in expint` or `series failed in expint`; the controller returns `400`.
  - Violated prerequisite or constraint: the calculation must converge under the implementation's iteration limit.
- Failing function: `calculate exponential integral`
  - Failure condition: path conversion fails for `n` or `x`.
  - Why it fails: Spring cannot bind the controller method arguments.
  - Violated prerequisite or constraint: path values must match the declared integer and double types.

Implementation notes:
The controller catches all runtime exceptions from the algorithm and returns an empty `400` response, so clients cannot distinguish invalid input from non-convergence through the response body.

### Behavior 3: Calculate a Fisher Numeric Value

Business goal:
Compute the implementation's Fisher distribution-style numeric value for supplied degree parameters and an input value.

Domain context:
This behavior is a statistical numerical calculation. It resembles a Fisher/F-distribution computation, but the implementation only enforces maximum degree values in the controller.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `calculate Fisher value` (`GET /api/fisher/{m}/{n}/{x}`) with path `m=4`, path `n=6`, and path `x=1.2` to calculate the Fisher value and receive `resultAsDouble`.

Optional verification workflow:
None. The behavior itself returns the computed value.

Existing-state shortcuts:
- No setup function can be skipped because the service is stateless.
- Direct database setup is not relevant.
- A prior client-side result is equivalent only when `m`, `n`, and `x` all match.

Parameter and value bindings:
- The path values `m` and `n` are consumed as the two integer degree-like parameters.
- The path value `x` is consumed as the numeric calculation input.
- No ownership, generated id, cursor, or stored result binding exists.

Business result:
The response is `200 OK` with `Dto.resultAsDouble` populated when the calculation completes. No service state changes.

Constraints and invariants:
- `m` and `n` must be parseable as integers and must satisfy `m <= 1000` and `n <= 1000`.
- `x` must be parseable as a double.
- The controller does not enforce `m > 0`, `n > 0`, or `x >= 0.0`, even though those would normally be expected for a Fisher distribution domain.
- If the algorithm produces a value below `0.0`, it returns `0.0`; if it produces a value above `1.0`, it returns `1.0`.

Failure and exceptional cases:
- Failing function: `calculate Fisher value`
  - Failure condition: `m > 1000` or `n > 1000`.
  - Why it fails: the controller returns `400` before calling `Fisher.exe(m, n, x)`.
  - Violated prerequisite or constraint: the controller-enforced maximum degree value is exceeded.
- Failing function: `calculate Fisher value`
  - Failure condition: `Fisher.exe(m, n, x)` throws a runtime exception for the supplied values.
  - Why it fails: the controller catches runtime exceptions and returns `400`.
  - Violated prerequisite or constraint: the lower-level calculation must complete without a runtime exception.
- Failing function: `calculate Fisher value`
  - Failure condition: values such as nonpositive degree parameters should fail by statistical-domain expectation but may not fail in the implementation.
  - Why it fails: it generally does not fail at the controller because only upper bounds are checked.
  - Violated prerequisite or constraint: expected Fisher-domain positivity is not enforced by the API implementation.

Implementation notes:
The implementation permits `m <= 0`, `n <= 0`, and negative `x` to reach the algorithm. Depending on Java floating-point behavior, such inputs can produce exceptional numeric values rather than a domain-specific error.

### Behavior 4: Calculate an Upper Incomplete Gamma Q Value

Business goal:
Compute the upper incomplete gamma Q value for supplied numeric parameters.

Domain context:
This is a special-function calculation used by clients that need the gamma Q result without storing any calculation state.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `calculate upper incomplete gamma Q` (`GET /api/gammq/{a}/{x}`) with path `a=2.5` and path `x=3.0` to calculate the upper incomplete gamma Q value and receive `resultAsDouble`.

Optional verification workflow:
None. The behavior itself returns the computed value.

Existing-state shortcuts:
- There are no setup steps to skip.
- Direct database setup is not relevant.
- Client-side cached results are equivalent only for the same `a` and `x`.

Parameter and value bindings:
- The path value `a` is the gamma parameter consumed by `Gammq.exe(a, x)`.
- The path value `x` is the nonnegative input consumed by the same algorithm.
- No generated identifiers or resource scope values exist.

Business result:
The response is `200 OK` with `Dto.resultAsDouble` populated. No persistent state is mutated.

Constraints and invariants:
- `a` and `x` must be parseable as double path values.
- Implementation success requires `a > 0.0` and `x >= 0.0`.
- The selected series or continued-fraction branch must converge within 100 iterations.
- OpenAPI does not document the implementation's numeric domain.

Failure and exceptional cases:
- Failing function: `calculate upper incomplete gamma Q`
  - Failure condition: `a <= 0.0` or `x < 0.0`.
  - Why it fails: `Gammq.exe(a, x)` throws and the controller returns `400`.
  - Violated prerequisite or constraint: gamma Q requires a positive `a` and nonnegative `x`.
- Failing function: `calculate upper incomplete gamma Q`
  - Failure condition: the series or continued-fraction branch does not converge within 100 iterations.
  - Why it fails: the gamma implementation throws a runtime exception; the controller maps it to `400`.
  - Violated prerequisite or constraint: the calculation must converge within the implementation's fixed iteration bound.
- Failing function: `calculate upper incomplete gamma Q`
  - Failure condition: path conversion fails for `a` or `x`.
  - Why it fails: Spring cannot bind non-double path values to `Double` parameters.
  - Violated prerequisite or constraint: path values must be parseable as doubles.

Implementation notes:
The algorithm chooses `gser` when `x < a + 1.0` and `gcf` otherwise. Both branches are hidden behind the same REST function and produce the same response shape.

### Behavior 5: Calculate a Bounded Integer Remainder for Nonzero Operands

Business goal:
Compute the service's bounded integer remainder result for two nonzero operands.

Domain context:
This behavior exposes integer arithmetic with explicit operand bounds. It is not a general-purpose Java `%` wrapper; it follows the custom `Remainder.exe` loop implementation.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `calculate bounded integer remainder` (`GET /api/remainder/{a}/{b}`) with path `a=17` and path `b=5` to calculate the bounded remainder and receive `resultAsInt`.

Optional verification workflow:
None. The behavior itself returns the integer result.

Existing-state shortcuts:
- No setup function can be skipped.
- Direct database setup is not relevant.
- A cached result is reusable only for the same ordered pair `a` and `b`; swapping operands changes the domain meaning.

Parameter and value bindings:
- The path value `a` is the dividend-like operand passed to `Remainder.exe(a, b)`.
- The path value `b` is the divisor-like operand passed to `Remainder.exe(a, b)`.
- Both operands must be reused exactly if the client wants to compare or cache the same arithmetic request.

Business result:
The response is `200 OK` with `Dto.resultAsInt` populated. No state is persisted. For many nonzero inputs the result is the implementation's remainder-like value, not necessarily the mathematically expected remainder.

Constraints and invariants:
- `a` and `b` must be parseable as integers.
- Both values must be inside `[-10000, 10000]`.
- For this normal path, both values should be nonzero.
- The implementation may return `-1` for nonzero operands when its loop never runs, which can collide with the zero-operand sentinel behavior.

Failure and exceptional cases:
- Failing function: `calculate bounded integer remainder`
  - Failure condition: `a < -10000`, `a > 10000`, `b < -10000`, or `b > 10000`.
  - Why it fails: the controller returns `400` before calling `Remainder.exe(a, b)`.
  - Violated prerequisite or constraint: both operands must be inside the controller-enforced range.
- Failing function: `calculate bounded integer remainder`
  - Failure condition: `a` or `b` is not an integer path value.
  - Why it fails: Spring cannot bind the path value to an `Integer`.
  - Violated prerequisite or constraint: path operands must be integer values.
- Failing function: `calculate bounded integer remainder`
  - Failure condition: `a == 0` or `b == 0`.
  - Why it fails: it does not fail at HTTP level; the request is handled by `return zero-operand remainder sentinel` semantics instead.
  - Violated prerequisite or constraint: the nonzero-operand remainder behavior requires both operands to be nonzero.

Implementation notes:
The function-level document separates the same endpoint into two domain branches: normal nonzero remainder and zero-operand sentinel. The controller does not expose a separate route or result flag to distinguish them.

### Behavior 6: Return the Zero-Operand Remainder Sentinel

Business goal:
Represent a remainder request involving zero by returning the implementation's sentinel result.

Domain context:
Instead of rejecting division by zero or zero dividend cases, the custom arithmetic implementation returns a sentinel integer. This is a meaningful domain behavior because it is explicitly observable through the same remainder endpoint.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `return zero-operand remainder sentinel` (`GET /api/remainder/{a}/{b}`) with path `a=0` and path `b=5` to receive sentinel `resultAsInt=-1`. Alternative: use path `a=5` and path `b=0`, or `a=0` and `b=0`, while staying within the operand range.

Optional verification workflow:
None. The behavior itself returns the sentinel.

Existing-state shortcuts:
- No setup state exists and no workflow step can be skipped.
- Direct database setup is not relevant.

Parameter and value bindings:
- At least one of the same path operands consumed by `Remainder.exe(a, b)` must be exactly `0`.
- Both `a` and `b` must still satisfy the controller range check before the sentinel path can be reached.
- No response-field binding exists beyond interpreting `resultAsInt=-1`.

Business result:
The response is `200 OK` with `Dto.resultAsInt=-1`. No state is persisted.

Constraints and invariants:
- Both operands must be parseable integers inside `[-10000, 10000]`.
- At least one operand must be `0` for this sentinel behavior.
- The sentinel value is not uniquely reserved; the normal nonzero implementation can also return `-1` for some operand pairs.

Failure and exceptional cases:
- Failing function: `return zero-operand remainder sentinel`
  - Failure condition: at least one operand is zero but any operand is outside `[-10000, 10000]`.
  - Why it fails: the controller range check runs before `Remainder.exe(a, b)` and returns `400`.
  - Violated prerequisite or constraint: the sentinel path still requires bounded operands.
- Failing function: `return zero-operand remainder sentinel`
  - Failure condition: neither operand is zero.
  - Why it fails: it does not fail at HTTP level; the request is handled by `calculate bounded integer remainder` semantics.
  - Violated prerequisite or constraint: the sentinel behavior requires a zero operand.

Implementation notes:
The implementation uses `int r = 0 - 1` and leaves it unchanged when `a == 0` or `b == 0`. This is a side effect of the algorithm structure rather than a separately modeled error response.

### Behavior 7: Classify Edges as Not Forming a Triangle

Business goal:
Determine that supplied edge values do not describe a valid triangle.

Domain context:
Triangle validation is a domain-facing classification task. The service represents invalid or non-triangle inputs as a successful classification result rather than as an HTTP validation failure.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `classify edges as not a triangle` (`GET /api/triangle/{a}/{b}/{c}`) with path `a=1`, path `b=2`, and path `c=3` to classify the edge set and receive `resultAsInt=0`. Alternative non-triangle setup: use any nonpositive edge such as `a=0`, `b=2`, `c=2`.

Optional verification workflow:
None. The classification response is the verification result.

Existing-state shortcuts:
- No setup function can be skipped.
- Direct database setup is not relevant.
- A client-side cached classification is equivalent only for the same ordered edge values, although the domain meaning is symmetric across permutations.

Parameter and value bindings:
- The path values `a`, `b`, and `c` are the three edges consumed by `TriangleClassification.classify(a, b, c)`.
- The values are not persisted and are not reused by later API calls.
- The order of the values does not change the mathematical triangle type, but the endpoint still consumes them as three separate path variables.

Business result:
The response is `200 OK` with `Dto.resultAsInt=0`. The result means at least one edge is nonpositive or the largest edge is greater than or equal to the sum of the other two edges.

Constraints and invariants:
- All three path values must be parseable integers.
- Invalid triangle geometry is not an HTTP error; it is a normal domain result.
- The OpenAPI contract marks the three path parameters as `required: false`, but the Spring route requires all three path segments.

Failure and exceptional cases:
- Failing function: `classify edges as not a triangle`
  - Failure condition: one or more path segments are missing.
  - Why it fails: the Spring route `GET /api/triangle/{a}/{b}/{c}` cannot match without all three segments.
  - Violated prerequisite or constraint: the implementation requires all three edge path values.
- Failing function: `classify edges as not a triangle`
  - Failure condition: any path value is not parseable as an integer.
  - Why it fails: Spring cannot bind the controller parameters.
  - Violated prerequisite or constraint: each edge must be an integer path value.
- Failing function: `classify edges as not a triangle`
  - Failure condition: the edge set is a valid scalene, isosceles, or equilateral triangle.
  - Why it fails: it does not fail at HTTP level; a different classification code is returned.
  - Violated prerequisite or constraint: this specific behavior requires a non-triangle edge set.

Implementation notes:
The code tests nonpositive values first, then equilateral equality, then triangle inequality, then isosceles/scalene distinction. Nonpositive equal values such as `0,0,0` therefore return `0`, not `3`.

### Behavior 8: Classify a Scalene Triangle

Business goal:
Identify a valid triangle whose three edge lengths are all different.

Domain context:
This is the triangle-classification path for valid asymmetric triangles. The result code is part of the service's integer classification vocabulary.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `classify scalene triangle` (`GET /api/triangle/{a}/{b}/{c}`) with path `a=3`, path `b=4`, and path `c=5` to classify the edge set and receive `resultAsInt=1`.

Optional verification workflow:
None. The classification response is the verification result.

Existing-state shortcuts:
- No setup function can be skipped.
- Direct database setup is not relevant.
- Cached classification is reusable only for equivalent edge values.

Parameter and value bindings:
- The same edge values `a`, `b`, and `c` are passed together to the classifier.
- The values must be positive, satisfy triangle inequality, and be pairwise different.
- No generated or stored value is consumed by another function.

Business result:
The response is `200 OK` with `Dto.resultAsInt=1`. No persistent resource is changed.

Constraints and invariants:
- `a`, `b`, and `c` must be parseable integers.
- All three values must be positive.
- The largest edge must be less than the sum of the other two edges.
- All three edge values must differ.
- The route requires all three path segments despite OpenAPI marking them optional.

Failure and exceptional cases:
- Failing function: `classify scalene triangle`
  - Failure condition: any edge is nonpositive, the triangle inequality fails, or any two edges are equal.
  - Why it fails: it does not fail at HTTP level; the classifier returns `0`, `2`, or `3` depending on the supplied values.
  - Violated prerequisite or constraint: scalene classification requires positive valid triangle edges with no equal pair.
- Failing function: `classify scalene triangle`
  - Failure condition: any path value is missing or non-integer.
  - Why it fails: routing or path conversion fails before classification.
  - Violated prerequisite or constraint: all three edge path values must be present and parseable as integers.

Implementation notes:
The classifier has no upper bound on edge values beyond Java integer binding. Integer overflow in `max - other1 - other2` is not explicitly guarded.

### Behavior 9: Classify an Isosceles Triangle

Business goal:
Identify a valid triangle with exactly two equal sides.

Domain context:
This behavior supports the common triangle taxonomy where isosceles means one matching pair and not all three equal.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `classify isosceles triangle` (`GET /api/triangle/{a}/{b}/{c}`) with path `a=5`, path `b=5`, and path `c=8` to classify the edge set and receive `resultAsInt=2`.

Optional verification workflow:
None. The classification response is the verification result.

Existing-state shortcuts:
- No setup function can be skipped.
- Direct database setup is not relevant.
- Cached results are equivalent only for the same triangle edge multiset.

Parameter and value bindings:
- The three path values `a`, `b`, and `c` are consumed by the classifier in one call.
- Exactly one equality relationship must hold among the three edge pairs.
- No child resource, parent scope, generated id, or response token exists.

Business result:
The response is `200 OK` with `Dto.resultAsInt=2`. No service-side state changes.

Constraints and invariants:
- All edges must be parseable positive integers.
- The edge set must satisfy triangle inequality.
- Exactly two edge values must be equal. All three equal values are classified as equilateral, not isosceles.

Failure and exceptional cases:
- Failing function: `classify isosceles triangle`
  - Failure condition: the edge set has no equal pair, all three values are equal, any value is nonpositive, or triangle inequality fails.
  - Why it fails: it does not fail as HTTP; the classifier returns another classification code.
  - Violated prerequisite or constraint: isosceles classification requires exactly one equal pair in a valid triangle.
- Failing function: `classify isosceles triangle`
  - Failure condition: missing or non-integer path values.
  - Why it fails: Spring routing or type conversion cannot invoke the controller method.
  - Violated prerequisite or constraint: all three path values must be present integers.

Implementation notes:
The implementation classifies equilateral before checking isosceles, so equilateral triangles are not included in the isosceles result category.

### Behavior 10: Classify an Equilateral Triangle

Business goal:
Identify a valid triangle with three equal positive sides.

Domain context:
This behavior exposes the highest-specificity triangle classification branch.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `classify equilateral triangle` (`GET /api/triangle/{a}/{b}/{c}`) with path `a=6`, path `b=6`, and path `c=6` to classify the edge set and receive `resultAsInt=3`.

Optional verification workflow:
None. The classification response is the verification result.

Existing-state shortcuts:
- No setup function can be skipped.
- Direct database setup is not relevant.
- Cached results require the same positive equal edge values.

Parameter and value bindings:
- The path values `a`, `b`, and `c` must all be reused as the same positive integer value within the single request.
- No service-side state or generated value is reused later.

Business result:
The response is `200 OK` with `Dto.resultAsInt=3`. No persistent state is changed.

Constraints and invariants:
- All three values must be parseable integers.
- All three values must be equal and greater than zero.
- Nonpositive equal values are classified as not a triangle because the nonpositive check runs first.

Failure and exceptional cases:
- Failing function: `classify equilateral triangle`
  - Failure condition: the three edges are not all equal or are equal but nonpositive.
  - Why it fails: it does not fail at HTTP level; the classifier returns a different classification code.
  - Violated prerequisite or constraint: equilateral classification requires three equal positive edges.
- Failing function: `classify equilateral triangle`
  - Failure condition: missing or non-integer path values.
  - Why it fails: Spring cannot route or bind the request.
  - Violated prerequisite or constraint: all three edge values must be present integer path values.

Implementation notes:
Because positive equality is checked before triangle inequality, any positive equal edge set is immediately classified as equilateral, which is consistent with triangle geometry.

### Behavior 11: Run a Client-Orchestrated Numerical Case-Study Calculation Set

Business goal:
Obtain a multi-algorithm result set from the service for independent numerical case-study inputs.

Domain context:
The service domain is a collection of independent numerical algorithms. Although there is no server-side batch endpoint, a client can compose available functions into a case-study workflow by invoking each calculation with its own valid inputs.

Starting point:
No prior service state.

Required execution workflow:
1. Use function `calculate Bessel J value` (`GET /api/bessj/{n}/{x}`) with path `n=3` and path `x=1.5` to obtain the Bessel result.
2. Use function `calculate exponential integral` (`GET /api/expint/{n}/{x}`) with path `n=2` and path `x=1.5` to obtain the exponential integral result.
3. Use function `calculate Fisher value` (`GET /api/fisher/{m}/{n}/{x}`) with path `m=4`, path `n=6`, and path `x=1.2` to obtain the Fisher result.
4. Use function `calculate upper incomplete gamma Q` (`GET /api/gammq/{a}/{x}`) with path `a=2.5` and path `x=3.0` to obtain the gamma Q result.
5. Use function `calculate bounded integer remainder` (`GET /api/remainder/{a}/{b}`) with path `a=17` and path `b=5` to obtain the integer remainder result.
6. Use one triangle classification function based on the desired edge scenario, for example `classify scalene triangle` (`GET /api/triangle/{a}/{b}/{c}`) with path `a=3`, path `b=4`, and path `c=5` to obtain the triangle classification result.

Optional verification workflow:
None. Each step returns its own result; there is no aggregate server state to inspect.

Existing-state shortcuts:
- Any individual calculation step can be skipped if the client already has an equivalent result for the exact same input values.
- No direct database setup alternative exists.
- Skipping a step means the service will not produce that result in this workflow; the client must preserve the cached value and its input binding.

Parameter and value bindings:
- There is no service-side binding across functions. Inputs are independent unless the client intentionally reuses values.
- In the example workflow, `x=1.5` is intentionally reused for Bessel and exponential integral calculations, but this is a client-side comparison choice rather than a service invariant.
- The `n` in `calculate Bessel J value` and the `n` in `calculate exponential integral` are separate path parameters with different domain meanings. The `n` in `calculate Fisher value` is also separate and acts as a second degree-like parameter.
- Each response field must be associated by the client with the function and input tuple that produced it.

Business result:
The client obtains a set of independent numerical outputs: double results for Bessel, exponential integral, Fisher, and gamma Q; an integer result for remainder; and an integer triangle classification code. The service stores no aggregate job, report, or history.

Constraints and invariants:
- Every step must satisfy the constraints of its individual function.
- Failure of one step does not roll back or affect any prior step because the service has no transaction or shared state.
- The API provides no server-side correlation id for the composed workflow.

Failure and exceptional cases:
- Failing function: `calculate Bessel J value`
  - Failure condition: the Bessel step uses `n <= 2` or `n > 1000`.
  - Why it fails: the Bessel controller rejects the request with `400`.
  - Violated prerequisite or constraint: Bessel order range.
- Failing function: `calculate exponential integral`
  - Failure condition: the exponential integral step uses invalid domain values or does not converge.
  - Why it fails: the algorithm throws and the controller returns `400`.
  - Violated prerequisite or constraint: exponential integral input domain and convergence.
- Failing function: `calculate Fisher value`
  - Failure condition: the Fisher step uses `m > 1000` or `n > 1000`, or the algorithm throws.
  - Why it fails: the controller returns `400`.
  - Violated prerequisite or constraint: Fisher controller maximum and algorithm completion.
- Failing function: `calculate upper incomplete gamma Q`
  - Failure condition: the gamma step uses `a <= 0.0`, `x < 0.0`, or non-converging inputs.
  - Why it fails: the algorithm throws and the controller returns `400`.
  - Violated prerequisite or constraint: gamma Q domain and convergence.
- Failing function: `calculate bounded integer remainder`
  - Failure condition: a remainder operand is outside `[-10000, 10000]`.
  - Why it fails: the controller rejects the request with `400`.
  - Violated prerequisite or constraint: bounded integer operand range.
- Failing function: `classify scalene triangle`
  - Failure condition: the supplied edge set is not a scalene triangle.
  - Why it fails: it does not fail at HTTP level; a different classification code is returned.
  - Violated prerequisite or constraint: the selected triangle branch requires its matching edge relationship.

Implementation notes:
This composite behavior is API-realizable only as client orchestration. The server does not provide a batch endpoint, shared request body, transaction boundary, or combined response model.

## Unsupported or Missing Business Behaviors

### Missing Behavior 1: Mathematically Standard Remainder Semantics

Priority:
Critical domain gap

Expected business goal:
Compute a conventional integer remainder, including clear handling of zero divisor and cases where the dividend magnitude is smaller than the divisor magnitude.

Why it is unsupported:
The only remainder function exposes `Remainder.exe`, which initializes `r` to `-1` and can return `-1` for zero operands and for some nonzero operands where the loop does not execute.

Existing functions considered:
- `calculate bounded integer remainder`: can compute the custom loop result for bounded nonzero operands, but it is not guaranteed to match mathematical or Java `%` semantics.
- `return zero-operand remainder sentinel`: returns `-1` for zero operands, but does not distinguish division by zero from zero dividend or from nonzero cases that also produce `-1`.

Missing capability:
A mathematically defined remainder endpoint, explicit division-by-zero error behavior, and a result model that distinguishes normal results from sentinel/error states.

Proof that function composition is insufficient:
No combination of existing functions can force `Remainder.exe` to compute a different algorithm. Delete-and-recreate or manual repair is irrelevant because there is no resource state. Client-side correction would be outside the service and not equivalent to supported API behavior.

Evidence from existing functions/source:
- `Remainder.exe` starts with `int r = 0 - 1`.
- If `a == 0` or `b == 0`, the method leaves `r` as `-1`.
- For some nonzero inputs where no loop iteration occurs, the same initial `-1` can be returned.

Business impact:
Consumers cannot rely on the endpoint for standard arithmetic. The sentinel collision makes it impossible to know from `resultAsInt=-1` alone whether the request represented an error-like zero case or a normal custom calculation path.

### Missing Behavior 2: Server-Side Batch Calculation Request

Priority:
Important robustness gap

Expected business goal:
Submit multiple numerical calculations in one request and receive a correlated, ordered result set with per-item success or failure.

Why it is unsupported:
All available functions are single-calculation `GET` endpoints with path parameters. There is no endpoint accepting a body with multiple calculations, no aggregate response schema, and no job or batch identifier.

Existing functions considered:
- `calculate Bessel J value`: computes one Bessel result and cannot carry other calculations.
- `calculate exponential integral`: computes one exponential integral result and cannot be combined server-side with other functions.
- `calculate Fisher value`: computes one Fisher result and has no correlation field.
- `calculate upper incomplete gamma Q`: computes one gamma Q result and has no batch context.
- `calculate bounded integer remainder`: computes one integer result.
- `return zero-operand remainder sentinel`: exposes a special remainder branch only.
- `classify edges as not a triangle`: classifies one edge set.
- `classify scalene triangle`: classifies one edge set.
- `classify isosceles triangle`: classifies one edge set.
- `classify equilateral triangle`: classifies one edge set.

Missing capability:
A `POST` or equivalent batch endpoint with a request body, item identifiers, typed calculation descriptors, and an aggregate response model.

Proof that function composition is insufficient:
Chaining existing functions can produce multiple client-side results, but it cannot create a single atomic server-side batch, cannot preserve server-side item ordering, cannot return a unified partial-failure structure, and cannot provide a server-generated correlation id for the calculation set.

Evidence from existing functions/source:
- `NcsRest` defines only individual `@GetMapping` methods.
- `Dto` contains only `resultAsInt` and `resultAsDouble`, not an array or calculation metadata.

Business impact:
Clients must manage orchestration, retries, correlation, and partial failure handling themselves. This weakens reliability for workflows that require a coherent multi-calculation result.

### Missing Behavior 3: Persistent Calculation History and Result Retrieval

Priority:
Important robustness gap

Expected business goal:
Store a calculation request and retrieve the result later by an identifier, for auditing, reproducibility, or user-facing history.

Why it is unsupported:
No function creates a calculation resource, returns a generated id, lists previous calculations, or retrieves a stored result. The service has no repository or database-backed model.

Existing functions considered:
- `calculate Bessel J value`: returns only the immediate Bessel result.
- `calculate exponential integral`: returns only the immediate exponential integral result.
- `calculate Fisher value`: returns only the immediate Fisher result.
- `calculate upper incomplete gamma Q`: returns only the immediate gamma Q result.
- `calculate bounded integer remainder`: returns only the immediate integer result.
- Triangle classification functions: return immediate classification codes only.

Missing capability:
Persistent calculation entities, generated calculation ids, create/list/retrieve endpoints, and a response schema that includes request parameters, result, status, and timestamp.

Proof that function composition is insufficient:
No existing response includes an id or storage location. Repeating the same function call recomputes the result rather than retrieving a persisted record. Direct database setup is not possible because the implementation has no database model for calculations.

Evidence from existing functions/source:
- `NcsRest` contains no `POST`, `PUT`, `DELETE`, list, or retrieve-by-id endpoints.
- `Dto` does not include ids, timestamps, status, or original input values.

Business impact:
Users cannot audit past calculations, prove which inputs produced a result, or share a stable result reference through the API.

### Missing Behavior 4: Explicit Input Validation Without Running the Calculation

Priority:
Important robustness gap

Expected business goal:
Validate whether inputs are accepted for a numerical function and receive a structured explanation without executing the full calculation.

Why it is unsupported:
Validation is embedded inside controller checks or algorithm execution. The only way to discover invalid input is to call the calculation endpoint and observe a result or an empty `400`.

Existing functions considered:
- `calculate Bessel J value`: validates only the Bessel order range before execution.
- `calculate exponential integral`: validates through exceptions thrown by the algorithm.
- `calculate Fisher value`: validates only upper bounds for `m` and `n`, not the full statistical domain.
- `calculate upper incomplete gamma Q`: validates through exceptions thrown by the algorithm.
- `calculate bounded integer remainder` and `return zero-operand remainder sentinel`: validate only operand range at the controller level.
- Triangle classification functions: treat invalid geometry as a successful classification rather than a validation report.

Missing capability:
Dedicated validation endpoints or structured error bodies that identify invalid fields, accepted ranges, algorithm branch, and convergence risk.

Proof that function composition is insufficient:
Calling a calculation endpoint cannot separate validation from execution. Empty `400` responses do not distinguish parse errors, domain errors, and non-convergence. Triangle invalidity is not represented as a validation failure at all.

Evidence from existing functions/source:
- `expint` and `gammq` controller methods catch `RuntimeException` and return `ResponseEntity.status(400).build()` without a body.
- OpenAPI does not document implementation-specific input ranges for most functions.

Business impact:
Clients cannot provide precise preflight validation or user guidance. They must duplicate implementation rules or handle opaque failures after attempting the calculation.

### Missing Behavior 5: Strong Fisher Domain Enforcement

Priority:
Important robustness gap

Expected business goal:
Reject Fisher calculation inputs that violate normal statistical domain expectations, such as nonpositive degree parameters or negative input values.

Why it is unsupported:
The controller only rejects `m > 1000` or `n > 1000`. It does not reject `m <= 0`, `n <= 0`, or `x < 0.0` before calling the algorithm.

Existing functions considered:
- `calculate Fisher value`: is the only Fisher function and cannot be configured to apply stricter validation.

Missing capability:
Controller-level or algorithm-level validation for `m > 0`, `n > 0`, finite numeric values, and domain-appropriate `x`.

Proof that function composition is insufficient:
No other function validates Fisher inputs. Calling other numerical endpoints cannot establish or enforce Fisher-specific domain correctness. The invalid values reach `Fisher.exe` directly unless they exceed the upper bound or fail path parsing.

Evidence from existing functions/source:
- `NcsRest.fisher` checks only `if(m > 1000 || n > 1000)`.
- `Fisher.exe` performs floating-point operations without explicit domain checks.

Business impact:
Clients may receive apparently successful results for statistically invalid inputs, weakening trust in the calculation and forcing clients to implement their own validation rules.

### Missing Behavior 6: Authentication and Authorization Enforcement

Priority:
API ergonomics gap

Expected business goal:
Restrict numerical calculation access or return meaningful `401` and `403` responses when credentials or permissions are missing.

Why it is unsupported:
The OpenAPI responses list `401 Unauthorized` and `403 Forbidden`, but the implementation contains no authentication or authorization checks.

Existing functions considered:
- All ten available functions: each is publicly callable through a controller method and contains no security branch.

Missing capability:
Security middleware, authentication configuration, authorization checks, and documented credential requirements.

Proof that function composition is insufficient:
Security cannot be created by chaining calculation endpoints. No function issues tokens, creates users, checks permissions, or scopes access.

Evidence from existing functions/source:
- `NcsRest` imports no security types and performs no credential checks.
- Each endpoint directly processes path values and returns a calculation result or `400`.

Business impact:
The contract suggests security outcomes that clients cannot intentionally exercise or rely on. This can mislead integrators and test suites.

### Missing Behavior 7: Accurate OpenAPI Contract for Triangle Required Path Values

Priority:
API ergonomics gap

Expected business goal:
Expose a contract that accurately tells clients all three triangle edge path values are required.

Why it is unsupported:
The OpenAPI document marks `a`, `b`, and `c` for `/api/triangle/{a}/{b}/{c}` as `required: false`, while the Spring route cannot match without all three path segments.

Existing functions considered:
- `classify edges as not a triangle`: requires all three values at runtime.
- `classify scalene triangle`: requires all three values at runtime.
- `classify isosceles triangle`: requires all three values at runtime.
- `classify equilateral triangle`: requires all three values at runtime.

Missing capability:
Correct OpenAPI path parameter metadata with `required: true` for all three triangle edges.

Proof that function composition is insufficient:
No function can classify a triangle with omitted path values. Supplying fewer than three values changes the URL and prevents the route from invoking the classifier.

Evidence from existing functions/source:
- `rest-ncs.json` marks triangle path parameters as not required.
- `NcsRest.checkTriangle` maps `GET /api/triangle/{a}/{b}/{c}` and declares all three `@PathVariable` parameters.

Business impact:
Generated clients or validators may treat required path values as optional, producing requests that cannot be served.

### Missing Behavior 8: Discoverable Function Metadata and Classification Vocabulary

Priority:
API ergonomics gap

Expected business goal:
Discover supported algorithms, input domains, and triangle classification code meanings through the API.

Why it is unsupported:
The service has no metadata endpoint and the `Dto` response only returns numeric result fields. Classification code meanings are not included in responses.

Existing functions considered:
- All calculation functions: each returns only a numeric result.
- Triangle classification functions: each returns only `resultAsInt`, not a label such as `SCALENE` or `EQUILATERAL`.

Missing capability:
A metadata endpoint, richer response models, enum labels for triangle classifications, and documented numeric domain constraints.

Proof that function composition is insufficient:
Calling the existing endpoints can reveal examples of outputs, but cannot enumerate all supported algorithms, expose domain limits, or explain result codes in-band. Clients must rely on external documentation or source knowledge.

Evidence from existing functions/source:
- `Dto` has only `Integer resultAsInt` and `Double resultAsDouble`.
- OpenAPI summaries are sparse and do not document classification code meanings or most numeric constraints.

Business impact:
Integrations are more brittle because clients must hard-code function names, parameter domains, and result-code interpretations.

## Cross-Behavior Observations

- The service is entirely stateless. There are no create, update, delete, list, ownership, session, generated-id, cursor, or persistence behaviors.
- All supported business capabilities are exposed as `GET` requests, including computations that can fail due to input domain or convergence.
- `Dto` is a generic result container. Double-valued calculations populate `resultAsDouble`; integer-valued calculations populate `resultAsInt`.
- OpenAPI lists `401`, `403`, and `404` for endpoints, but the implementation does not contain authentication, authorization, or resource lookup logic for these responses.
- OpenAPI omits most implementation constraints, including Bessel order range, exponential integral domain, gamma Q domain, remainder operand range, and convergence failures.
- Triangle invalidity is modeled as a successful domain classification (`resultAsInt=0`), not an HTTP validation error.
- The triangle OpenAPI contract incorrectly marks path parameters as optional even though the route requires them.
- Remainder behavior has a sentinel collision: `-1` can represent zero-operand handling and may also arise from nonzero custom-loop behavior.
- Fisher validation is weak relative to normal statistical expectations because nonpositive degrees and negative `x` are not rejected by the controller.
- Runtime algorithm exceptions are collapsed into empty `400` responses, making failure causes opaque to API clients.

## Coverage Summary

Supported domain areas:
Stateless evaluation of Bessel J, exponential integral, Fisher-style numeric values, upper incomplete gamma Q, bounded custom remainder behavior, zero-operand remainder sentinel behavior, and triangle classification into not-a-triangle, scalene, isosceles, and equilateral result codes.

Partially supported domain areas:
Client-side composition of multiple calculations is possible, but only as independent calls without server-side batching, correlation, transactionality, stored results, or aggregate status. Input validation exists unevenly and is often exposed only through opaque `400` responses or successful classification codes.

Unsupported domain areas:
Persistent calculation history, generated result identifiers, batch calculation jobs, structured validation-only workflows, mathematically standard remainder semantics, strong Fisher-domain enforcement, authentication/authorization despite documented response codes, accurate triangle path-parameter metadata, and API-discoverable algorithm/result-code metadata.
