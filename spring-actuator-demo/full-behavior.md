### Function 1: get default greeting

Function name:
get default greeting

Core endpoint(s):
- `GET /`

Preconditions:
- No prerequisite database or resource state is required. The endpoint is stateless and does not depend on any setup endpoint or direct database setup.

Successful execution:
- Result:
  The endpoint returns a plain-text greeting for the default user name `Guest`.
- Invocation:
  Step 1: `GET /` with the `name` query parameter omitted.
- Constraints:
  The `name` query parameter must be omitted to use the default value. The OpenAPI schema documents `Guest` as the default, and the controller implementation sets `@RequestParam(value = "name", defaultValue = "Guest")`.

Failure or exceptional branches:
- None identified from the OpenAPI definition or implementation logic for the default-name invocation.

Endpoint coverage:
- Covers:
  `GET /`
- Distinct meaning:
  This function uses the endpoint's documented and implemented default `name` value rather than a caller-supplied query value.

### Function 2: get personalized greeting

Function name:
get personalized greeting

Core endpoint(s):
- `GET /`

Preconditions:
- No prerequisite database or resource state is required. The endpoint is stateless and does not depend on any setup endpoint or direct database setup.

Successful execution:
- Result:
  The endpoint returns a plain-text greeting addressed to the caller-supplied `name` value.
- Invocation:
  Step 1: `GET /` with query parameter `name={name}`.
- Constraints:
  `{name}` is accepted as a string query value and is inserted directly into the returned text as `Hello {name}!!`. No additional validation, persistence requirement, authentication requirement, or resource lookup is implemented for this endpoint.

Failure or exceptional branches:
- None identified from the OpenAPI definition or implementation logic for a supplied string `name` query value.

Endpoint coverage:
- Covers:
  `GET /`
- Distinct meaning:
  This function uses the same greeting endpoint with a caller-provided `name` query parameter instead of the default `Guest` value.

### Function 3: run slow API with automatic delay

Function name:
run slow API with automatic delay

Core endpoint(s):
- `GET /slowApi`

Preconditions:
- No prerequisite database or resource state is required. The endpoint is stateless and does not depend on any setup endpoint or direct database setup.

Successful execution:
- Result:
  The endpoint waits for an automatically selected delay and then returns the plain-text response `Result`.
- Invocation:
  Step 1: `GET /slowApi` with the `delay` query parameter omitted, or with query parameter `delay=0`.
- Constraints:
  The OpenAPI schema documents `delay` as an optional integer query parameter with default `0`. The controller implementation also uses default value `0`; when the bound integer is `0`, it selects a random integer delay from `0` through `9` seconds before sleeping and returning `Result`.

Failure or exceptional branches:
- None identified from the OpenAPI definition or implementation logic for the omitted `delay` value or integer `delay=0`.

Endpoint coverage:
- Covers:
  `GET /slowApi`
- Distinct meaning:
  This function uses the endpoint's documented default `delay=0`, which the implementation treats as a request for automatic random delay selection.

### Function 4: run slow API with caller-specified delay

Function name:
run slow API with caller-specified delay

Core endpoint(s):
- `GET /slowApi`

Preconditions:
- No prerequisite database or resource state is required. The endpoint is stateless and does not depend on any setup endpoint or direct database setup.

Successful execution:
- Result:
  The endpoint applies the caller-supplied integer `delay` value and then returns the plain-text response `Result`.
- Invocation:
  Step 1: `GET /slowApi` with query parameter `delay={delay}`.
- Constraints:
  `{delay}` must be parseable as an integer because the controller parameter type is `Integer` and the OpenAPI schema declares the parameter as `integer` with `int32` format. If `{delay}` is a positive integer, the implementation sleeps for that many seconds. If `{delay}` is a negative integer, `TimeUnit.SECONDS.sleep(delay)` returns without sleeping and the endpoint still returns `Result`. The value `0` is reserved for the automatic random-delay function.

Failure or exceptional branches:
- Branch 1:
  - Preconditions:
    - No prerequisite database or resource state is required. The failing request depends only on the malformed `delay` query value.
    - The `delay` query parameter is supplied with a value such as `{nonIntegerDelay}` that cannot be parsed as an integer.
  - Failure endpoint:
    `GET /slowApi`
  - Why this fails:
    Spring must bind the `delay` query parameter to the controller's `Integer delay` argument before entering the method. A non-integer query value violates that binding requirement, so request handling fails before the endpoint can execute its delay logic.
  - Intentionally violated constraints:
    The `delay` query parameter violates the integer type required by both the OpenAPI schema and the controller method signature.

Endpoint coverage:
- Covers:
  `GET /slowApi`
- Distinct meaning:
  This function uses a caller-provided integer delay value instead of the automatic random-delay path.
